---
title: "How to SSR with `apollostack` and `meteor-react-router-ssr`"
seoDescription: "Learn how to set up SSR with Apollo and Meteor-React-Router-SSR, handling initial state hydration, preRender hooks, and routing tweaks."
datePublished: Fri Nov 18 2016 04:00:00 GMT+0000 (Coordinated Universal Time)
cuid: cm844z4em000c08jsbbiafckz
slug: how-to-ssr-with-apollostack-and-meteor-react-router-ssr
canonical: https://jasonnathan.wordpress.com/2016/11/18/how-to-ssr-with-apollostack-and-meteor-react-router-ssr/
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1741675391608/a552d87e-1cc0-4972-a004-9da84651aaf8.webp
tags: react-router, javascript, web-development, meteor, full-stack, graphql, apollo, ssr

---

<div data-node-type="callout">
<div data-node-type="callout-emoji">💡</div>
<div data-node-type="callout-text">Originally written in 2016, when Meteor was still a solid choice for full-stack JavaScript. Preserved as is, with updated links where needed. A 2025 retrospective is in the works!</div>
</div>

Wiring up meteor with Apollo and adding a router was easy enough, but getting server-side rendering and populating the client with an initial state was quite challenging.

The documentation at [dev.apollodata.com](http://dev.apollodata.com) is *fairly* extensive but ridiculous to navigate. The sidebar is filled with menu items that only appear as you scroll - making it impossible to locate *that* piece of information that you were sure you read somewhere. It would really help if they had an option to search too.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1741674902270/ca3b452e-4a8d-426f-b887-f85f978c6683.jpeg align="center")

After piecing in bits and bytes from all over the place,  I am writing this in the hope that it might make your life a little easier if you were after the same thing. First, I had to hack `meteor-react-router-ssr` and move `preRender` down so it runs after the `wrapperHook`. I also added `app` as an argument. (See below)

Here's how my isomorphic `index.jsx` looks after that:

```javascript
import React from 'react';
import { Meteor } from 'meteor/meteor';
/*
* Had to hack server.jsx in meteor-react-router-ssr to move the  
* `preRender` hook so it runs after `wrapperHook` and I had to 
* give it {app} as an extra  argument.
*/
import { ReactRouterSSR } from 'meteor/jasonnathan:react-router-ssr';
import ReactHelmet from 'react-helmet';
import AppRoutes from '/imports/routes.jsx';
import ApolloClient, { createNetworkInterface } from 'apollo-client';
import { getDataFromTree } from "react-apollo/server";

let client, initialState,
  url = "localhost",
  opts = {
    ssrMode: Meteor.isServer
  };

// relevant for apollo when a client-side query is made
if (Meteor.isClient && process.env.NODE_ENV === 'production') {
  url = "public-url.com";
}

// setup apollo's networkInterface
opts.networkInterface = createNetworkInterface({
  credentials: 'same-origin', 
  uri: `http://${url}:3000/graphql`
});

const rehydrateHook = state => initialState = state;

const wrapperHook = app => {
  opts.initialState = initialState;
  client = new ApolloClient(opts);
  return { app };
};

// the preRender is simpler. All it needed was the `app` argument
const preRender = (req, res, app) => 
  Promise.await(getDataFromTree(app));

// dehydrating in a way that is apollo friendly. 
// Queries & mutations need to be removed
const dehydrateHook = () => ({
  apollo: { 
    data: client.store.getState().apollo.data 
  }
});

const htmlHook = html => {
  const h = ReactHelmet.rewind();
  return html.replace(
    '', '' + h.title + h.base + h.meta + h.link + h.script
  );
};

// the weirdest thing - wrapperHook in clientOptions - 
// inferring it only runs on the client
const clientOptions = { wrapperHook, rehydrateHook };
const serverOptions = { htmlHook, preRender, dehydrateHook };

ReactRouterSSR.Run(AppRoutes(), clientOptions, serverOptions);
```

And here's the modified portion of react-router-ssr:

```javascript
// from line 190      
// if (serverOptions.preRender) {
//   serverOptions.preRender(req, res);
// }
//...

global.__STYLE_COLLECTOR_MODULES__ = [];
global.__STYLE_COLLECTOR__ = '';

renderProps = {
  ...renderProps,
  ...serverOptions.props
};

fetchComponentData(serverOptions, renderProps);
  let app;

  if (typeof clientOptions.wrapperHook === 'function') {
     app = clientOptions.wrapperHook(app);
  }
    // preRender moved after wrapperHook
  if (serverOptions.preRender) {
    serverOptions.preRender(req, res, app);
  }
```

That's it! You can grab a copy of the modified `meteor-react-router-ssr` from [here](https://github.com/jasonnathan/meteor-react-router-ssr)

<div data-node-type="callout">
<div data-node-type="callout-emoji">💡</div>
<div data-node-type="callout-text">Originally written in 2016, when Meteor was still a solid choice for full-stack JavaScript. Preserved as is, with updated links where needed. A 2025 retrospective is in the works!</div>
</div>