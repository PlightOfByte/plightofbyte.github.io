---
layout: post
title: Our journey to Micro Frontend
date: 2020-06-28
categories: web-dev
subtitle: My team's journey explaining why, how, the problems we faced and solutions while implementing micro-frontend
share-img: /assets/images/microfrontend-journey/shareImg.jpg
cover-img: /assets/images/microfrontend-journey/heroImage.jpg
thumbnail-img: ""
css: /assets/css/style.css
---

Micro-frontend architecture is a way to develop frontend which is roughly equivalent to microservices in the backend.  
You break your frontend into smaller pieces which can be developed, tested & deployed independently.

Micro-frontend has some benefits like:
* You __can__ have a different tech for a particular part of the application (be careful with this one).
* Micro-frontend helps in managing a big codebase by slicing it in smaller pieces.
* Smaller pieces lead to more scalable development. More people can work on the same app.

Modular code in-turn has it's own benefits and of course, some drawbacks.

Don't worry, by the end of this blog you'll have a good understanding of what micro-frontend are.

> Now, let's talk about our journey.

*_All images are for understanding and are not a factual representation._

#### Background
We have an application which is built using React and Spring Boot. It follows microservice architecture in the backend.
However, frontend was a monolith react app.  
It had an architecture like this.
![Monolith Frontend](/assets/images/microfrontend-journey/monolith-frontback-microservices.png){: .center-image }

One fine day  
> *Product Owner walks in  
"I have these new features that we have to build...,  
one more thing,  micro-frontend" - Product Owner  
*mic drop

![Mic Drop](https://media.giphy.com/media/l0K42u9hCg0x6XbLa/giphy.gif){: .center-image }

That's a joke, frankly, I don't remember what he said because who listens to Product Owner anyways.  
That's another joke. ...Please don't fire me.

He had the vision to merge scattered projects into a single product.
Our project was picked to be transformed into this product.

The idea is, other projects would integrate into our project and the original team would maintain their part of the app.
This went hand in hand with the transformation towards the tribe model we were going through.

This is how our tribe looks like.

![Tribe](/assets/images/microfrontend-journey/tribe.png){: .center-image }

All these teams, now called squads are part of a tribe and tribe has one product.
Each part of the product will be handled by one squad.
Each part must cover end-to-end of that domain.

I wouldn't go in much detail of the tribe model as this article is about micro-frontend.
The important part to know is that in a tribe model a team is divided into small groups. And each group is the owner of a part of the application.
These groups should be self-sufficient to design, develop, test and deploy their part of the application.

Allowing squads to integrate into our project while having all the above-mentioned freedom was the challenge at hand.

We went to the drawing board to see if and how can this be done.  
In the backend, we were using microservices architecture which was perfect.  
New services can be created for projects which are to be integrated.

Problems were in frontend. Some prominent ones:
* Our app in itself was big and adding more code which will be written by other squads seems like a disaster in making.
* We want each squad to be able to deploy independently.
* We want minimal dependency between squads. The fewer co-ordination required between squads the better.

We knew we wanted separate code bases because multiple squads working on the same project in a single git repository would result in

![Spaghetti](/assets/images/microfrontend-journey/spaghetti.jpg){: .center-image .secret }

At that point, we knew we had to go micro-frontend.  
What we weren't sure about was if we want build-time integration or runtime integration.

_Glossary:_  
_Host - The one which everyone is integrating into._  
_Micro-frontend - The one being integrated._

Essentially, in build-time integration, you export your code as a library and the host app will add it as a dependency and render it.  
In runtime integration, you add the micro-frontend's bundled JS file in the index.html file directly, so host gets your code only in the browser, not before that.

We didn't pick build-time integration because it would create a dependency between squads.
Every time one squad would like to deploy to prod they would have to update their version in the host and run some tests.
We want to avoid this kind of dependency. We want squads to be able to deploy on their own.

Now coming to runtime integration, which is what we ended up doing.

With runtime integration, we can have it all. However, the cost of setting this up is high.

We looked at different approaches and settled for going with a custom approach because we wanted to integrate micro-frontend with minimal changes in the host.  
Also, we picked react to develop micro-frontend.

Our custom approach is to export the whole micro-frontend as a react component and render it in the host app.

Note that the exported micro-frontend will be available at runtime, in the browser. It is not a host's build time dependency.

This is how our application is divided now.
![Vertical Teams](/assets/images/microfrontend-journey/vertical-teams.png){: .center-image }

#### How did we do it?
I'm assuming you know react & how to bundle a frontend app under normal circumstances.  
We are using webpack to bundle our app. 

In a usual app, you'd set the entry point to `index.js`. This `index.js` has below code.

```javascript
ReactDOM.render(<App/>, document.getElementById("root"));
```

`ReactDOM.render` is responsible to render the app on the webpage.

Instead of exporting this `index.js`, we exported the `<App/>` component by setting `App.jsx` as `entrypoint` in webpack and bundling it as a library.

This creates a JS file which when added to an `index.html` sets `App` component in `window` object.

Below is the relevant part of webpack config which bundles micro-frontend into a library.

```javascript
webpackConfig = {
    entry: './src/App.jsx',
    output: {
        library: 'app',
        libraryTarget: 'umd'
    }
}
```
<br>
#### Problems
Some prominent challenges we faced:
* Local development setup
* Functional testing and integration testing with the host app
* CSS class name conflicts
* Communication between host and micro-frontend
* Production deployment
* Bundle size

Here's what we did with each of them.

#### Local development setup
Our custom approach has an issue that micro-frontend can't be rendered without a host.
This is because we are bundling our code as a library.  
We need something that can consume this library and show on UI.

An obvious solution to this problem would be to run host on local and connect micro-frontend to it.
This will allow us to view our application in the host.

This approach has issues.
First, we are now dependent on another team (one who maintains host), 
second, it is cumbersome to run two servers and we also lose hot-reloading.

In react's local setup everyone uses hot-reloading. This allows you to change code and see the changes instantly.
This seems trivial, but it has a meaningful impact on productivity.

To solve this we created separate webpack configs.
One for production where we bundle with `App.jsx` as entrypoint and one for local where entrypoint is `index.jsx`.

> Wait, what is this `index.jsx`?

Our `index.jsx` is just like a regular react app's `index.jsx`. Both have `ReactDOM.render` to render things on a page.

Relevant part of index.jsx:  
`ReactDOM.render(<App/>, document.getElementById("root"));`

We keep this `index.jsx` in a separate folder, away from `src`. We named it `appShell`.
This `appShell` is for local development and acts as a container to render `<App/>` component.
We have no intention of bundling anything inside `appShell` folder in production bundle.

This allows us to work on micro-frontend without the actual host and have our changes hot reload.

Our project file structure:
![Project file structure](/assets/images/microfrontend-journey/local-setup.png){: .center-image }

#### Functional testing and integration testing with the host
Just as local setup an obvious approach is to test micro-frontend inside host.
And just as local setup the downside is we add a dependency on another squad. If their code is broken our tests would fail.
 
To avoid dependency and keep micro-frontend independent we created a new bare-bones host for functional testing, just enough code to run micro-frontend.
We wrote all functional test using this testing instance.

We also wrote an integration test with the real host.
This integration test is a minimal test which just checks if the micro-frontend loads or not.

We added this integration test in micro-frontend's and host's functional test suite.
Both are now checking integration before deployment.
This allowed us to have zero dependencies between pipelines of micro-frontend and host.

#### CSS class name conflicts
Another issue with micro-frontend is that each micro-frontend creates it's own CSS classes and these classes can conflict with each other if they have the same name.
To avoid this issue we prefixed every CSS class name.

To do this automatically we customized `css-loader` in webpack.
```javascript
cssLoader = {
    loader: 'css-loader',
    options: {
        modules: {
            getLocalIdent: (context, localIdentName, localName) => {
                return `awesome_${localName}`;
            }
        }
    }
}
```

Adding the above to webpack will prefix every CSS class with `awesome_`.
Having app name as a prefix will make sure you'll never have a conflicting CSS class.

We did this via `css-loader` because we wanted this to be a background thing which we can forget even exists.  

#### Communication between host and micro-frontend
In our use-case, there is not much communication between host and micro-frontend.
What we've done for now is, when something needs to come from host to micro-frontend we pass it down as `props`.  
For things that need to go from micro-frontend to host we do it via events.

We've created an event bus and any micro-frontend can post events in it. It serves as a common channel for communication.

One other approach we were considering but didn't go ahead with was having a shared redux.
With a shared redux you can communicate bi-directional easily.
This approach has a risk that anyone can modify anything in the store. This is why we didn't take this approach.

I'll not go in details of eventing because that can be a blog in itself. 

#### Production deployment
This is an important challenge. How to deploy both apps independently.
If squads have to check with one another before deployment then this model will not be scalable. 

One important thing, we have integration tests that ensure micro-frontends are working well together and all squads run them.
These tests are end-to-end tests. These are required to prevent squads from breaking someone's else code.

![Deployment Architecture](/assets/images/microfrontend-journey/deployment-architecture.png)

The above diagram is how our architecture looks like.  
We have host's code deployed on an Nginx server and this Nginx is the entry point for every request.

_*Nginx is a software that can host static files like HTML, CSS & JS and it can also route traffic._

Search micro-frontend has it's code deployed on a different nginx.

These servers are managed by different teams. So their deployment is independent.

Let's look at what happens on a page load.

The first thing that happens when you open our website is you download `index.html` from host's server.
Next, this `index.html` has these lines.
```html
<script type="text/javascript" src="app.js"></script>
<script type="text/javascript" src="search/bundle.js"></script>
```  
Then the browser will send a request to download these files.  
First file `app.js` is straight forward, it is host's code and is available on the host's server.

Second file `search/bundle.js` is present on micro-frontend's server and not on the host.

Browser knows only about host's server so it sends this request to host and over there we have a routing rule to forward (pass-through) all requests starting with `search` to search micro-frontend's server.
And now this request is served by micro-frontend's server.

An important thing is that this is a forward (pass-through) and not a redirect.

Having a routing rule on the host gives control of `/search` to search squad and this way each squad can have control over their part.

#### Bundle size
When taking an approach of micro-frontend where apps combine at runtime this issue does come up.

Every app has to bundle all their dependencies (react and others).  
Every micro-frontend is bundling all their dependencies so there are a lot of duplicated dependencies.

This is an issue that we haven't solved yet.
We are caching our JS files on the browser so this issue is only when a user loads page for the first time, so we haven't prioritised it yet.

**If you enjoyed this article, please share to help others find it! Feel free to leave a comment below.**

You can read more about micro-frontends [here](https://martinfowler.com/articles/micro-frontends.html) & [here](https://micro-frontends.org/)

#### Photo Credits
Cover image - [Red photo created by jcomp - www.freepik.com](https://www.freepik.com/free-photos-vectors/red)  
Spaghetti image - [Food photo created by mrblmoreno - www.freepik.com](https://www.freepik.com/free-photos-vectors/food)  
Diagrams - [micro-frontends.org](https://micro-frontends.org/)
