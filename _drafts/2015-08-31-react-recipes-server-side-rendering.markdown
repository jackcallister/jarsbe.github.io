---
layout: post
title: React Recipes: Server Side Rendering
---

So you want to build isomorphic applications.

There are a few things to know and do in order to achieve this.

- Instances over singletons
- Client side Bootstrapping
- Client/Server routing

Firstly you'll need to learn to render everything on the server. Most often this comes in as a request. This request will trigger middle wear which fetches data needed for the particular route. This data is bundled together and feed to the Flux application, the flux application fills all of it's stores with this data and React then renders out the entire view to a string which can then be interpolated into a server side template. This HTML is finally sent back to the client. Oh and one more thing. The flux stores running on the client will be empty, unlike the ones on the server. You'll want to send down a data payload which will be used to fill the client side flux instance stores. This means that once the JavaScript application takes over from the initial request response cycle it's stores will be loaded with data, rather than being empty which would cause unnecesary requests and loading which is kinda like the opposite of what you want in an isomorphic application.

Next thing you want to learn about are Flux singletons. This is a little bit conducive to using node but I'm sure it applies around the board. When that first request to the node application fills the server side flux stores with data it stays like that. The next request comes in and the data could bleed into that request. That's because the flux instance is a singleton. It is instantiated once and lives for the lifetime of the node runtime. One would have to remove all data from the stores at the end of every request. But if something went wrong and that code didn't run or we forgot to clear some specific piece of data... it just leaves the possibility of things going wrong a little open.

The solution to this is to use a new instance of flux for every request. That way the store data is guaranteed to be empty. This is just server side. The client will initialise one flux instance and use that for it's session. Which is safe since only one client is ever using that instance.

You also need back end routes for each JavaScript route. Since the page being loaded may be a link (not the root) that particular route will hit the server, load the data, and return a full rendered HTML view. That same route needs to work client side only, loading the data it needs. A problem occurs when you transition to a new client side route. You aren't hitting the server so how is data loaded?

This is a pretty application specific problem. One solution is to have a loading boolean on stores. A component which utilises that stores data will check if the data has loaded upon mounting. If it has loaded (the server has loaded it) then it will just render. If it isn't loaded (the client has just mounted this component and the store is empty) then trigger an action to load that data.

TODO:

- Node JS HTTP route examples
- Node JS String rendering
- Client Side Bootstraping

