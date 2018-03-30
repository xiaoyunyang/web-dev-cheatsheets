# Building Isomorphic Webapp

You will need: 

* React - view provider
* Redux - manages business logic for the view provider
* Express - server framework
* Node - server engine
* Babel - ES6 transpiler
* Webpack - packager

The book: [Isomorphic Web Applications Chapter 1](https://livebook.manning.com/#!/book/isomorphic-web-applications/chapter-1/v-10/106)

## What is isomophic Webapp

### Background

[https://blog.tableflip.io/server-side-rendering-with-react-and-redux/](https://blog.tableflip.io/server-side-rendering-with-react-and-redux/)
> Traditionally if you’re serving a website, a request will come into your server, and your app will gather all the data it needs to render the page and then use Handlebars or Jade or whatever to smoosh the two together and send the resultant HTML down to the browser. If you’re using a client side app like react, you get the templates sent to you first, and then you request the data for that page (from your API). Maybe you do an initial render in between, show a nice spinner or something, but when that data finally comes back, you do a re-render to show the page you wanted.
> 
> We all want it - the Holy Grail of having your app render server side to give your users a quicker initial rendering and to help SEO. The problem is that React doesn’t fit with the traditional model for server side rendering, in fact React turns it on it’s head.

When you create a webpage, there will be static content and there will be dynamic content. 

![Manning Isomorphic Webapp](https://dpzbhybb2pdcj.cloudfront.net/gordon/v-10/Figures/01_09.png)

Static content is stuff on the webpage that will not change. For example, the topnav and the footer are things that are static.

Dynamic content is stuff that changes after the DOM has loaded. For example, push notifications.

### Isomorphic Webapp

> isomorphic web apps are the result of combining server-rendered HTML pages with single-page application architecture.

Isomorphic webapp means have server side rendering for the static content and single page applications rendering dynamic content.

![](https://dpzbhybb2pdcj.cloudfront.net/gordon/v-10/Figures/01_10.png)

Isomorphic webapps are like SPAs, but has the capability to choose what to render dynamically in the browser. This is achieved through server-side rendering.

Three main steps in an isomorphic app: 

1. server render
	- The server gets an initial request via a HTTP GET request. The server grabs what it needs to render the HTML, such as data from a database or static assets like images, then sends HTML to the browser.
	- Initial request is generated when the the user clicks a link to your website or types the URL (address) to your website directly into the web address bar.
2. initial browser render
	- 
3. single-page application behavior
	- Once the page enters into Single Page Application (SPA) mode, updates are initiated by events, usually from the user.

[Isomorphic vs. Universal app](https://medium.com/@ghengeveld/isomorphism-vs-universal-javascript-4b47fb481beb)
>Isomorphism is the functional aspect of seamlessly switching between client- and server-side rendering without losing state. Universal is a term used to emphasize the fact that a particular piece of JavaScript code is able to run in multiple environments.

### Why is SPA useful?
> SPA lets you have interesting webpages that handle complex user interactions.

Ajax (Asynchronous JavaScript and XML) makes communication with the server asynchronous so that data is transferred and processed in the background, allowing the user to interact with other parts of a page without interruption. This improves usability and responsiveness.

When a user navigates to a new view, additional content required for the view is requested using an XHR (XMLHttpRequest), typically communicating with a server-side REST API or endpoint.

What really defines an SPA as such, is the fact that client-side JS handles the routing instead of the browser. Daniel Puplus explains the advantages of SPAs in [Building Single Page Applications](https://writing.pupius.co.uk/beyond-pushstate-building-single-page-applications-4353246f4480):

1. **Good**:  History and fast back
	> When a user presses the browser’s back button they expect the change to happen quickly and for the page to be in a similar state to how it was last time they saw it.
	>
	> once the app is initially loaded, it can support quick navigation between pages without refreshing the page, and if done right, can even work offline. ~ [airbnb engineering article](https://medium.com/airbnb-engineering/isomorphic-javascript-the-future-of-web-apps-10882b7a2ebc)

2. **Good**:  Save scroll position
	> When the user navigates using the browser’s forward or back button the scroll position should be the same as it was last time they were on the page. 

3. **Good**:  XMLHttpRequest (XHR)

4. **Good**: Fast response to user input

	> Apps like Gmail, the classic example of the single-page app, could respond immediately to user interactions, no longer needing to make a round-trip to the server just to render a new page.

SPA was popular in 2013. But over time, the disadvantages of SPA became hard to ignore.

[The disadvantages of a single page application](https://adamsilver.io/articles/the-disadvantages-of-single-page-applications/) include:

1. **Bad**:  SEO

	> For many SPAs, SEO is an afterthought, which is problematic because retro fitting server side rendering is not trivial. The alternative is to create a special website just for bots which isn’t ideal either.
	
	> Web crawlers function by making a request to a web server and interpreting the result; but if the server returns a blank page, it’s not of much value. There are workarounds, but not without jumping through some hoops. ~ [airbnb engineering article](https://medium.com/airbnb-engineering/isomorphic-javascript-the-future-of-web-apps-10882b7a2ebc)
	
2. **Bad**:  Memory leaks and long load time.
	> If an SPA grows to a significant size, loading the entire application on load will be slow.

	Also, loading a SPA can be a slow experience for a user (especially on mobile phones). Faster and improved accessibility because the user can view the app without JavaScript.
	
	> The bulk of the application logic (views, templates, controllers, models, internationalization, etc.) lives in the client, and it talks to an API for data. The server could be written in any language, such as Ruby, Python, or Java, and it mostly handles serving up an initial barebones page of HTML. Once the JavaScript files are downloaded by the browser, they are evaluated and the client-side app is initialized, fetching data from the API and rendering the rest of the HTML page. ~ [airbnb engineering article](https://medium.com/airbnb-engineering/isomorphic-javascript-the-future-of-web-apps-10882b7a2ebc)

3. **Bad**: It [breaks the web](https://ponyfoo.com/articles/stop-breaking-the-web)
	
	> Hash routing sucks. It does nothing to help modern browsers (except slowing down the experience, it does do that!) and everything to complicate development and confuse humans who are using older browsers.

### Why is server side rendering useful?
> server side rendering is ideal for SEO.

Bots and crawlers can read all of the data on page load. We need good SEO to maximize the number of people that come to the app from search engines. Single-page applications are difficult for search engine bots to crawl because they don’t load the data for the app until after the JavaScript has run in the browser.

Google tools for assessing your app for SEO

* Use Google Speed Insight Tool to assess speed of your web page:
[https://developers.google.com/speed/pagespeed/insights](https://developers.google.com/speed/pagespeed/insights)
* Use Google lighthouse for an in depth anaysis: [https://developers.google.com/web/tools/lighthouse/](https://developers.google.com/web/tools/lighthouse/)* Build a progressive webapp to improve usability for the mobile version of your webpage. [Addy Osamani](https://addyosmani.com/blog/getting-started-with-progressive-web-apps/) wrote extensively about progressive webapps.
* Use Audit in Chrome's devtools. Build a progressive Web Apps take advantage of new technologies to bring the best of mobile sites & native apps to users. Read [Progressive Webapps with React](https://medium.com/@addyosmani/progressive-web-apps-with-react-js-part-i-introduction-50679aef2b12).

Client side rendering is slow 

> because the human now has to download all of your markup, your CSS, and your JavaScript before the JavaScript is able to render the view the user expected you to deliver in the first place.

> you should be delivering the content in human-viewable form first, and not after every single render blocking request out there finishes loading. This means that a human-ready HTML view should be rendered in the server-side and served to the human, then you can add your fancy JavaScript magic on top of that, while the user is busy making sense of the information you’ve presented them with. ~[stop breaking the web](https://ponyfoo.com/articles/stop-breaking-the-web)


## React
> React’s virtual DOM lets us render HTML on the server.

React is a declarative, efficient, and flexible JavaScript library for building user interfaces.

In React, components are the basic building blocks, they encapsulate markup and logic together in one place. Components receive immutable data (called props) from parent components. There's a top-level parent component, which we will refer to as the `Provider`.

React takes advantage of functional concepts by adhering to single direction data flows from the top level component down to its children. What makes it appealing for isomorphic apps is how it uses a virtual DOM to manage changes and updates to the application

React uses a template language called JSX. JSX is compiled by Babel into pure JavaScript. You could write your components with the base React functions, but this is slower and less readable.

We want to take advantage of React to implement a declarative view that can be used to render both on the server and the browser.


Any time the app state is updated, the view receives an update and displays it to the user. When the view receives user input, it notifies the app state (Redux) to make an update. The view doesn’t worry about the implementation of the business logic and app state doesn’t worry about how it will be displayed.

## React Router V4 and Universal

For an isomorphic webapp, you need two things: (1) server delivers fully rendered HTML to the browser on initial load. (2) client SPA hooks itself to the HTML rendered by the server and responds to user interaction like cliking on the nav link.

**TL;DR** here's what you need to do:

1. Use `StaticRoute` for server side rendering
2. Use `BrowserRoute` for client side rendering
3. Set up webpack config to make `main.js` your main entry point for your SPA

I will be focusing on React Router V4 and use `react-router-config` to set up the app with routes to render both on the server side and client side.

Here are some repos and docs that helped me figure out how to set up the project:

* [technology-ebay-de/universal-react-router4](https://github.com/technology-ebay-de/universal-react-router4/tree/master/src/shared)
* [zacfukuda/universal-app-react-router](https://github.com/zacfukuda/universal-app-react-router)
* [React Router training on server rendering](https://reacttraining.com/react-router/web/guides/server-rendering)
* [isomorphic-dev-js](https://github.com/isomorphic-dev-js/complete-isomorphic-example)
* [EmileCantin's blog](https://blog.emilecantin.com/web/react/javascript/2017/05/16/ssr-react-router-4-webpack-code-split.html)

## Redux

> [`redux`](https://redux.js.org/) is a manager of global variables for React components.

### Why we need Redux?

Sometimes, we want multiple React components to render different views of the same JSON and modify that JSON. We need to have a way to properly manage global variables. [Understanding Redux](http://www.youhavetolearncomputers.com/blog/2015/9/15/a-conceptual-overview-of-redux-or-how-i-fell-in-love-with-a-javascript-state-container) explains the motivation of Redux well. Redux manages the global variables using states.


Andre Staltz created a wonderful illustration of redux in action as part of his [comparison between different patterns](https://staltz.com/unidirectional-user-interface-architectures.html) for doing client side web UI using unidirectional dataflow.

![](https://staltz.com/img/redux-unidir-ui-arch.jpg)

From the book:

![](https://dpzbhybb2pdcj.cloudfront.net/gordon/v-10/Figures/01_12.png)

### What is Redux

Redux is based on the idea of a single root state object for the entire application, commonly referred to as the store. 

* **Store** -  Redux holds the state of your app in its store, providing a single source of truth for your application. Redux’s store is where the state of the application lives. Redux's store is the top level (global) state of your client application and should not be confused with React component's state, which is local to the component. As the book says it:
	
	> By relying on a single root object to hold our application state, we can easily serialize our state on the server and send it down to the browser to be deserialized.
* **Reducer** - Only reducers can update the store (i.e., the state of the application). Reducer makes the update by taking the current state as input and returns the next state.
* **Action** - Actions trigger reducers. Actions are dispatched from the views. Actions are triggered by user actions such as a mouse click. Actions are essentially setter functions passed to React components to make changes to global states managed by redux. 
* **Provider** - The `Provider` provides the `store` to the React app, which allows us to connect our React components to the redux store. This is done by wrapping Provider around a top level react component called the `AppContainer`.


**Store and Provider Setup**

The boilerplate code for setting up redux store, provider, and reducer for an existing react app is as follows:

```javascript
//AppContainer.js
	
import React from 'react'
import { applyMiddleware, compose, createStore } from 'redux'
import thunk from 'redux-thunk'
import promise from 'redux-promise'
import createLogger from 'redux-logger'
import { Provider } from 'react-redux'
import AppContainer from './app/containers/AppContainer'
	
	
const configureStore = (initialState) => {
   const logger = createLogger();
	const store = createStore(
	rootReducer,
	initialState,
	applyMiddleware(thunk, promise, logger)
	);
}
	
const store = configureStore(window.INITIAL_STATE);
	
const App = () => (
	<Provider store={store}>
		<AppContainer />
	</Provider>	
);

export default AppContainer
```

The Provider component acts as the stateful top level component. It knows when the store updates and passes that change down to its children. Individual components are also able to subscribe to the store as needed.

**Reducer Setup**

```javascript
/*
 * https://medium.com/@jonlebensold/getting-started-with-react-native-redux-2b01408c0053
 * Creating a reducer without using switch statements
 */
export default function createReducer(initialState, handlers) {
  return function reducer(state = initialState, action) {
    if (handlers.hasOwnProperty(action.type)) {
      return handlers[action.type](state, action)
    } else {
      return state
    }
  }
}
```

```javascript
// reducers/todos.js

import createReducer from '../lib/createReducer'
import * as types from '../actions/types'

export const todoCount = createReducer(0, {
  [types.ADD_TODO](state, action) {
    return state + 1;
  }
});
export const activeUsers = createReducer(init.USERS, {
  [types.SET_ACTIVE_USERS](state, action) {
    return action.state;
  },
  [types.SET_USERS](state, action) {
    return action.state;
  }
});
```

```javascript
// reducers/index.js
import { combineReducers } from 'redux'
import * as todosReducer from './todos';

export default combineReducers(Object.assign(
  todosReducer,
));

```

**AppContainer Setup**

The AppContainer passes all the possible actions to the View components

```javascript
//AppContainer.js

import { connect } from 'react-redux'
import { bindActionCreators } from 'redux'
import { ActionCreators } from '../actions'
import Router from '../components/Router'

//Dispatching functions (boilerplate)

const mapDispatchToProps = (dispatch) => {
  return bindActionCreators(ActionCreators, dispatch);
}
export default connect((state) => {
  //the state in the argument is the global state of the application
  return {
    todoCount: state.todoCount,
    username: state.username,
  }
}, mapDispatchToProps)(Router);

```

```
// Router.js

const Router = () => {

	// ...
}

```

**Note**

* `actions` are dispached to trigger `reducers` to change the `store`.
* The components can't directly interact with the store; everything has to be done through redux:
* We can retrieve data by obtaining its current state
* we can change its state by dispatching an action
 wrapper for the top level react component referrred to as a the container. 



Image from [React-redux-connect explained](https://www.sohamkamani.com/blog/2017/03/31/react-redux-connect-explained/)

![](https://www.sohamkamani.com/assets/images/posts/react-redux-explanation/final-connect-flow.svg)


**Middleware for Redux**

* [`redux-thunk`](https://github.com/gaearon/redux-thunk) - allows you to write action creators that return a function instead of an action. `redux-thunk` allows you to delay the dispatch of an action or to dispatch only if a certain condition is met. A thunk is a function that wraps an expression to delay its evaluation.
* [`redux-promise`](https://github.com/acdlite/redux-promise) - receives a promise, dispatch the resolved value of the promise, but will not dismatch anything if the promise rejects.
* [`redux-logger`](https://github.com/evgenyrodionov/redux-logger) - logging tool that lets you replay problems as if they happened in your own browser.
* [`react-redux`](https://github.com/reactjs/react-redux) - We need to use `connect` from `react-redux` to connect a React component to a Redux store.