---
title: Cheating (And Express with React Environment draft)
date: '2018-12-18'
---

### Why this is cheating

A month ago I made a deal with a friend to publish at least one article every two weeks or pay a penalty. In typical David fashion I waited till the night of the deadline to write something and now I'm half asleep with no real ideas. So I'm gonna cheat. I've found a pretty solid draft of a post I wrote two months ago and I'm gonna publish it. No I'm not gonna refine the draft. I'm not gonna add the code snippets or the github links. I'm literally gonna publish the draft. Enjoy.. And if your name is Chiamaka well.. I found a loophole :P

### Sidenote

If you found this while desparately searching the interwebs for a solution to your real life problem, and you want me to finish the post hit me on twitter.

### Dev Environment For Express X React Application [DRAFT]

This writeup is to give the reader some insight as to how we’re developing our _appX_ web app.

#### THE STRUCTURE

Before I talk about the structure of the codebase it’s necessary to shed some light on what we’re building. Not the workings of the product necessarily, but the _sort_ of product it is.
_appX_ is a multi-faced web application. By that I mean that it’s one application that show’s different “portals” to different users. The simplest example you can liken it to is an application with a main app that user’s interact with, and a backoffice that admins interact with.
A popular approach to building such an app would be to have a single backend API, and two separate frontend applications accessing it. But that’s not how we intend to go about it here. Our application basically has 6 faces, and we don’t want to manage 6 different codebases. We’re building a monolith. Well, we’re building a monolith that looks like micro services. Let me explain; We’re using react for the application frontend and express for the backend. Because react (well actually web pack) allows us to create multiple bundles from multiple entry points, we’ve basically divided the frontend applications such that each has its own entry point. This way, web pack creates a separate react app for each of them. That’s a lot of words already, let me show you what I mean:

#### The directory (10,000 foot view)

So from the image above (or this GitHub repo) you can see that the root directory is quite straightforward. Client (react) code here, server (Express) code there. I’m gonna go deeper into each of these, but first let’s talk about the dev environment. This is a very crucial aspect of any project. It plays a huge role in whether you wake up in the morning filled with excitement to build, or filled with anxiety and self loathing. So let’s get to it.

Our application is two main parts, the react part and the express part. These two parts are developed separate but together. Let’s talk about the separate, then we’ll get to the together.

#### The separate development environments of react and express.

The express application is the simpler of the two in terms of development environment. Since all frontend things are gonna be handled by react, the express is essentially an api. So we don’t really need a build step. All we really need here is a way to reload the app when we make a change to the code (so that we don’t have to stop and start the server every time we save). For that, we’ll use nodemon. We did 2 things to get nodemon to work the way we wanted.

1. We added a script to our package.json so that we can run nodemon with the command npm run dev-server.
2. We added a nodemon.json file. At this point we did something slightly unconventional. We added nodemon.js to our gitignore. The reason being that all our env variables are in that file. Don’t want them getting into the wrong hands do we.

And that’s it for the express side, let’s talk about the react side now.
React was a little tricky, but we stuck with convention for the most part. We have a web pack.config.js file in the root. We specified multiple entry points for each portal, and we made them output to a respective folder in the dist. So for example: /src/client/main/index.js produces /dist/main/bundle.js.
[code]
We also setup something called web pack dev server, which basically does for the client side what nodemon is doing for the server side. (Recompiling every time we change code so we don’t have to). Setting up web pack dev server is actually not so straightforward when you’re building a multi-faced monolith. Remember when I said that the two sides are developed separate but together? Well you can’t really setup webpack-dev-server without understanding the together part, so let’s do that:

#### The coupled development environments of react and express

Let me say something first to keep your mind on it: web pack-dev-server runs on it’s own port. Remember that.
Now if you’re reading this I’m gonna assume you have _some_ knowledge about how react works. At least you know this part: Your whole entire application (html, css, js, fonts, icons etc) is bundled up into one single javascript file. Just one file (web pack is a powerful magic). Let’s call this file bundle.js. Now you can’t just send bundle.js to the browser all by itself, you have to put it in an html file. Your html file will probably look like the following:
[code]
Lemme reiterate: when a user first makes a request to your application (yourdomain.com), they should receive this html file (with bundle.js sitting in it). Here’s where it gets tricky. Who’s responsible for sending this file? Where does this html file sit in our codebase?
Now if this was a single face application, we could put the index.html file at the root so that filesystem would serve it for us. But this being a multifaced application, we need separate html files for each face (because they each have a separate bundle.js). Not only that, but we need a way to route a request to yourdomain/backoffce to the backoffice html file, and yourdomain/main to the main html file. (Stay with me now)
To answer these questions, remember that the user making a request to yourdomain.com, would be making requests to your server… your express server. So who’s gonna do the routing? Express. We’re basically setting up our express to server-side render the html file for the respective request. We do this by telling express to use the “dist” folder as it’s “views” folder, and then setting up the routes:
[code]

#### Almost there...

Great.. so now that we’ve got that setup nice and clean, everything should work right? err.. not quite. Remember that first statement I made at the beginning of this section? Web pack-dev-server runs on it’s own port! This means that when we’re in development webpack-dev-server could be running on port 9000, while express runs on port 4000. So , if we make a request to the express server, we’ll get the html file alright, but we won’t get the benefit of the bundle updating when we save. We’ll have to recompile everytime. On the other hand if we make a request to the web pack-dev-server (at port 9000) we won’t hit the express server at all, so who does the routing and whatnot for us?? (This is the point where you bang your head on the table)

There is a solution to this problem though. And it’s so simple you might shed a tear. There’s a property on the web pack-dev-server configuration called historyApiFallback. You can read more about the html5 history api here. But what this basically allows you to do is to “rewrite” a url to another url. I can do some talking but let me show you the code first:
[code]
You’ll see that we’ve done a bit of routing here, telling web pack-dev-server which html file to serve depending on the request entered.
There’s one more important thing in this config. The proxy key. Because web pack-dev-server is running on a separate port, you’ll have to proxy api requests to the express server(running on another port).

Whew. Are you still with me? Nice. If you’ve gotten to this point you can now run npm run web-server, and npm run client-server and your two sides should be working hand in hand. You change some code in the server folder, nodemon restarts your server. You change some code in the client side, web pack recompiles your bundles. It’s… beautiful. The code is quite simple too. It’s the explanation that’s long. I needed to write this because it took me quite a while and a few iterations to set it up and I thought I’d save someone (my future self) some time.

Thank you for reading :)
