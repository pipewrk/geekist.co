---
title: "You Can Blog from Your Local WordPress Site on iPhone. Here’s How."
seoTitle: "How to Blog from Your Local WordPress Site on iPhone (No Cloud Needed)"
seoDescription: "Run WordPress locally on your Mac and still blog from your iPhone? Here’s how ZeroTier or Tailscale lets you write & publish without going online."
datePublished: Tue May 20 2025 13:13:24 GMT+0000 (Coordinated Universal Time)
cuid: cmawjblcl000808lb35o27fyz
slug: you-can-blog-from-your-local-wordpress-site-on-iphone-heres-how
canonical: https://geekist.co/you-can-blog-from-your-local-wordpress-site-on-iphone-heres-how/
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1747746214193/d9069589-bda7-40e4-a3cf-a5e5f8612a41.avif
tags: cloudflare, blogging, wordpress, mobile-development, static-website, zerotier, tailscale, selfhosting

---

Blogging is supposed to be simple. Write words. Hit publish. (And then maybe admire your cleverness.)

But if you’re self-hosting WordPress locally, there’s nothing simple about it. You’re knee-deep in plugin madness & static site exports and then you’re left wondering why Lighthouse is screaming murder at your LCP.

My original plan was clean and logical. Get the stack running, wire up Cloudflare Pages, then post like a boss.

Of course, *nothing* went according to plan, but those will need 3 more posts, so let’s not bore you.

Instead, let me tell you how I’m writing this *right now* on my iPhone, all while WordPress is quietly sitting on my Mac behind [*localhost*](http://localhost)*.*

![Writing directly from my iPhone](https://img.geekist.co/wp-content/uploads/writing-directly-from-my-phone-433x480.avif align="center")

## Don’t quite get what I’m saying?

WordPress is installed locally on my Mac with no access to the outside world. But I still use it to write posts and build pages like normal. Then, using a plugin called Simply Static, I export the entire site and host it on Cloudflare Pages.

The only issue with this workflow is the fact that it makes it nearly impossible to use the WordPress app on other device, because there is no way of discovering where your WordPress installation is located.

So what’s the solution?

There are two services that work perfectly for this. The first is [ZeroTier](https://www.zerotier.com/download/) and the second is [Tailscale](https://tailscale.com/download). Both have a free tier that allows you to set up a private network mesh (think VPN) that you have complete control over. This means that your phone connects to a secure network that you can choose to add services to.

And for this use case? It’s actually perfect.

## Great. How do I get started?

The process is almost exactly the same for both services, so choose the service you want to use below.

### ZeroTier

### Setting It Up with ZeroTier

Here’s how you can access your local WordPress site from your phone, even if it’s just on `localhost`.

#### 1\. Install ZeroTier on both devices

* On your Mac, [download and install ZeroTier](https://www.zerotier.com/download/).
    
* Do the same on your **iPhone** (it’s on the App Store).
    
* Once installed, both devices will get unique **Node IDs**.
    

#### 2\. Create a ZeroTier Network

* Go to [my.zerotier.com](https://my.zerotier.com/) and sign in (GitHub login works).
    
* Click “Create a Network”.
    
* Copy the Network ID (you’ll need it in the next step).
    

#### 3\. Join the Network from each device

* On your Mac:  
    `zerotier-cli join <your-network-id>` (or use the GUI if you prefer)
    
* On your iPhone:  
    Paste the Network ID into the app and tap **Join Network**.
    
* Go back to the ZeroTier dashboard and **approve** both devices under **Members**.
    

#### 4\. Find your Mac’s ZeroTier IP

* Once connected, ZeroTier assigns your Mac a virtual IP like `10.x.x.x`.
    
* You’ll see this IP in the ZeroTier dashboard next to your Mac’s name.
    

#### 5\. Update WordPress to accept remote connections

* Edit `wp-config.php` and add the following. Doing it this way allows WordPress to respond to both `localhost` (or whatever you set your local domain to) and IP addresses. This works out perfectly because, well, it’s on your local machine anyway.
    

define**(**'WP\_HOME', "http://$host"**)**;

define**(**'WP\_SITEURL', "http://$host"**)**;

#### 6\. Open the WordPress App on iPhone

* Add the **full IP address** of your Mac and port like:  
    `10.x.x.x:8080`
    
* Login with your WordPress credentials.
    

💡 *Remember that you can’t use custom hosts here as your phone doesn’t know which address that host points to.*

That’s it! **You’re In** ®

---

### Tailscale

Setting It Up with Tailscale

Want the same magic but using Tailscale instead of ZeroTier? The steps are nearly identical — just a bit more elegant thanks to its identity-based access and built-in DNS.

#### 1\. Install Tailscale on both devices

* On your Mac, [download and install Tailscale](https://tailscale.com/download/).
    
* On your iPhone, install the [Tailscale app from the App Store](https://apps.apple.com/us/app/tailscale/id1475497147).
    
* Sign in on both devices with the same account (Google, GitHub, or Microsoft).
    

#### 2\. Authorise devices in your Tailscale admin panel

* Go to [login.tailscale.com/admin/machines](https://login.tailscale.com/admin/machines).
    
* You should see both your Mac and iPhone listed.
    
* Approve them if required (usually automatic).
    

#### 3\. Note your Mac’s Tailscale IP or MagicDNS name

* Tailscale assigns your Mac a stable IP like `100.x.x.x`, or a **MagicDNS name** like:  
    `geekist-mac.local.tailnet-yourname.ts.net`
    
* Either one works. MagicDNS is easier to remember (and you won’t need to hardcode IPs later).
    

#### 4\. Update WordPress to accept remote connections

Just like in the ZeroTier setup, edit `wp-config.php` with:

define**(**'WP\_HOME', "http://$host"**)**;

define**(**'WP\_SITEURL', "http://$host"**)**;

Where `$host` can be either:

* `100.x.x.x:8080` (Tailscale IP), or
    
* `geekist-mac.local.tailnet-yourname.ts.net:8080` (MagicDNS)
    

#### 5\. Add your site to the WordPress app

* Open the WordPress app on your iPhone.
    
* Choose **“**Add self-hosted site**”**.
    
* Enter the **full address** including port, e.g.:  
    `http://100.x.x.x:8080` or `http://geekist-mac.local.tailnet-yourname.ts.net:8080`
    
* Login with your normal credentials.
    

💡 *If you use MagicDNS, there’s no need to remember the IP address.*

It **Just Works.**™

## Wait. So Which one to pick?

Honestly, it comes down to what kind of person you are.

If you like seeing everything, ZeroTier will speak your language. It lets you name your networks, manually approve devices (and run a dashboard that feels like your own little ISP.)

It feels wonderfully self-contained. The trade-off is that setup takes a little more intent.

Tailscale, on the other hand, behaves more like something that’s already part of your system. You sign in & it just works. Devices show up automatically, MagicDNS remembers the names for you, and you don’t have to click “approve” anywhere. If you’re the type who wants to forget you ever set it up, this one’s probably the better fit.

Both are excellent, both are free for the use case we’ve talked about and both will let you blog from your phone like some sort of secret agent tunnelling into your own [localhost](http://localhost).

If you already use one of them for other projects, stick with it. If you’re starting fresh, try both. You’ll know within ten minutes which one feels right.

---

Blogging from your phone doesn’t have to mean surrendering control to third-party platforms or cloud editors. With a simple mesh network setup and a local WordPress install, you can draft, edit & publish your posts exactly how you want (*from wherever you are*).

And whether you go with ZeroTier or Tailscale, it doesn’t matter much because the results are the same:

Your blog, your rules, your environment. Private and under your control.