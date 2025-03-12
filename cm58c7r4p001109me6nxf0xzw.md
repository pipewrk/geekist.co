---
title: "Building a Zoomable User Interface with Meteor.js & Greensock"
seoDescription: "An exploration of Zoomable User Interfaces (ZUIs) with Meteor.js and Greensock & a unique responsive UI design technique"
datePublished: Fri Jan 16 2015 05:06:37 GMT+0000 (Coordinated Universal Time)
cuid: cm58c7r4p001109me6nxf0xzw
slug: building-a-zoomable-user-interface-with-meteorjs-greensock
canonical: https://jasonnathan.wordpress.com/2015/01/04/building-a-zoomable-user-interface-with-meteorjs-gsap/
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1735399854191/4dce2e91-12dc-4f29-b346-93321214c8b3.png
tags: flexbox, web-design, meteor, animation, greensock, frontend-development, digging-into-the-archives, zui, zoomable-user-interface

---

*This article was originally published in January 2015 and reflects the tools, techniques, and trends of that time. It showcases the implementation of a Zoomable User Interface (ZUI) using Meteor.js and Greensock's GSAP library, along with LESS for layout styling. The screenshots and examples, along with the narrative, remain intentionally unaltered to preserve the authenticity of the original work so it reflects the web development landscape of 2015.*

---

In the journey through web user interfaces and experiences, I had always wondered if there was a way to present a UI with all of the content and information visible, just tucked away within different portions of the viewport until needed.

Sounds messy? I thought so too. So I did some research out of curiosity and stumbled upon a few techniques. One such technique was a **Zoomable User Interface** or **ZUI** as it's commonly known (think Google Maps) and one such website that implemented it was [beercamp](http://2011.beercamp.com/).

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1735399130858/a3553b44-38a1-4495-9a26-98456843668c.png align="center")

I decided to create one of my own, using Greensock's awesome [GSAP](https://greensock.com/gsap) library.  So, together with designer, Daniel, who designed the look and layout, I set out to rework jasonnathan.com to a cool new site featuring ZUI. I decided to give CSS frameworks a miss this time, the design was simple and I wanted to use [FlexBox](www.w3.org/TR/css3-flexbox/). I did however, [import a few LESS](https://github.com/jasonnathan/jasonnathan.meteor.com/tree/version-stitch/client/less) files to get quickly started for the project.

My first attempt was complete disaster! I wanted the site responsive so I tried to create a fluid layout where elements adjusted itself to spread out over the viewport. It worked, but it simply did not have the same feel as the original design. I then settled with a fixed ratio. Using a simple `LESS` `mixin` and `vh`, `vw` units for measure, I wrote:

```less
/* This version is adapted from http://stackoverflow.com/a/24894523/382536 to  
* constrain width to 100% height instead. See blog post for further reading:
* https://www.jasonnathan.com/article/gsap/building-a-zoomable-user-interface-with-meteorjs-gsap
*
* param {Number} @x - width
* param {Number} @y height
*/
.viewportRatio(@x, @y) {
  @height:100vh;
  @width: (@x * 100vh / @y);
  @max-height:( @y / @x * 100vw);
  @max-width: 100vw;
}

//usage

#constrainContainer{ 
/** or body, aspect ratio is 3:4 */
  .viewportRatio(3, 4);
  height: @height;
  width: @width;
  max-height: @max-height;
  max-width: @max-width;
  position:relative;
}

.Page{
  position:absolute;
  top:0;
  left:0;
  right:0;
  bottom:0;
}

#contact{
  /* 1/3 of total area*/
  transform:scale(.3333);
}

#works{
  /* 1/6 of total area  */
  transform:scale(.1111);
}
```

The second attempt, while it was super simple to implement, was just not cross-browser friendly enough. Another disaster. In the end, I opted for a mostly absolutely positioned layout - another first for me in web design. (Perhaps, I should have had a quick look at [Flexbugs](https://github.com/philipwalton/flexbugs) or even tried some [LESS](https://gist.github.com/jayj/4012969) [Mixins](https://github.com/ProLoser/Flexbox.less)!). Add some skeuomorphic juicy bits and I ended up with:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1735399178168/9beb2889-4a4a-4a6f-84f3-129ab2cddf33.png align="center")

With the exception of the top header, it's a standard 3-column layout with the middle column left empty to show the scaled-down pages. I wanted markup to be as simple as possible too. I created a single container with constrained proportions using the `LESS` `mixin` above and each subsequent page was absolutely positioned inside it.

```html
<!--The master layout for a zoomable user interface. 
See: 
www.scriptstreet.com/building-a-zoomable-user-interface-with-meteorjs-greensock
-->

<nav>
  <ul>
    <li><a href="#start" id="startMenu">Start</a></li>
    <li><a href="#contact" id="contactMenu">Links</a></li>
    <li><a href="#works" id="worksMenu">Works</a></li>
  </ul>
</nav>

<div id="constrainContainer">
  <div class="Page" id="start"></div>
  <div class="Page" id="contact"></div>
  <div class="Page" id="works"></div>
</div>
```

Now, I just had to add some GSAP goodness... :)

```js
/**
* This was written in Meteor JS, these globals are directly accessible thanks
* to Meteor's Smart Packages
* 
* @globals _, $, TimelineLite, TweenLite
* 
* See blog post for further reading:
* www.scriptstreet.com/building-a-zoomable-user-interface-with-meteorjs-gsap
*/
   
  _Animation = {
    tl: new TimelineLite,
    /**
     * A simple helper method that removes .active class from menu items and
     * adds it to given element.
     * 
     * @param {Object} elementToAdd - jQuery object
     */
    removeActive: function(elementToAdd) {
      $("#mainNav a").each(function() {
        return $(this).removeClass('active');
      });
      return elementToAdd && elementToAdd.addClass('active');
    },
    /**
     * Another helper method that builds an options object for Tweenlite
     * 
     * @params {Object} params - a hash of options
     * @return {Object} TweenLite
     */ 
    tw: function(element, scale) {
      // we add a 1.1 second total animation time
      return TweenLite.to(element, 1.1, {
        scale: scale,
        force3D: true, // force hardware accelerated animations if possible
        ease: Power4.easeIn
      });
    },
    /**
     * Animation for Start Page
     * 
     * returns {Object} _Animation - self
     */ 
    start: function(element) {
      var self = this;
      // remove active class from all other menus and add one for this one  
      self.removeActive(element);

      // add each tween to the timeline, scales to 1
      self.tl.add(self.tw("#start", 1))
        // scales to 1/3 of original scale
        .add(self.tw("#contact", .3333), "-=1.1") 
        // scales to 1/6 of original scale        
        .add(self.tw("#works", .1111), "-=1.1");

      return self;
    },
    /**
     * Animation for Contact Page
     * 
     * returns {Object} _Animation - self
     */     
    contact: function(element) {
      var self = this;
      // remove active class from all other menus and add one for this one  
      self.removeActive(element);

      // now the start page scales to 7.5
      self.tl.add(self.tw("#start", 7.5))
        // scales to original scale since it is in view now
        .add(self.tw("#contact", 1), "-=1.1") 
        // scales to 1/3 of original scale        
        .add(self.tw("#works", .3333), "-=1.1");

      return self;
    },
    /**
     * Animation for Works Page
     * 
     * returns {Object} _Animation - self
     */       
    works: function(element) {
       var self = this;
      // remove active class from all other menus and add one for this one  
      self.removeActive(element);

      // now the start page scales to 7.5
      self.tl.add(self.tw("#start", 7.5))
        // scales to 7.5
        .add(self.tw("#contact",  7.5), "-=1.1") 
        // scales to original scale since it is in view now   
        .add(self.tw("#works", 1), "-=1.1");

      return self;
    }
  }

// Meteor's startup function called when DOM is loaded  
Meteor.startup(function(){
  /**
   * The click handler for the menu items
   */ 
   _.each(['#start', '#contact', '#works'], function(item){
     
     // create a jQuery Object. Menu items are just appended with 'Menu'
     var element = $(item + "Menu");
     
     element.on('click', function(e){
       e.preventDefault();
       // check if a method is defined
       if(_Animation[item] && typeof _Animation[item] === 'function'){
         // call the method, giving the parent Object as context
         return _Animation[item].call(_Animation, element);
       }
     });
   });  
})
```

To mimic beercamp's easing, I chose the easing method above. It was close enough. I would still like to go back to it and get it right when I have the time.

***Edit***\*: I have changed the easing and you can see the changes in the repository\*

That's it! In my next post, I will talk about SEO techniques I adopted for the site and why it was deliberately made with very little content.

Do have a look at the completed source on [Github](https://github.com/jasonnathan/jasonnathan.meteor.com).