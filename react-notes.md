
[JSX](http://www.react.express/jsx)
>JSX is a shortcut for using the `React.createElement()` API, that is much more concise, easy to read, and visually looks a little like the generated UI (as both are tree-like).

>JSX is tag-based like XML. Each tag, like `<div />`, is transformed into a call to `React.createElement()`. Any attributes become props of the instantiated component.

```javascript
// JSX
const a = <div />

const b = (
  <div
    foo='hello'
    bar={baz}>
    <span>42</span>
  </div>
)

// Javascript equivalent
var a = React.createElement('div', null);

var b = React.createElement(
  'div',
  {
    foo: 'hello',
    bar: baz },
  React.createElement(
    'span',
    null,
    '42'
  )
);
```

There are two libraries we'll use:

* `react` contains the APIs for creating components
* `react-dom` contains the APIs for rendering to the browser DOM

[React Updating Cycles](http://www.react.express/lifecycle_api) and `class` vs `function`

* For simple React components, we can use a function. For example:
	
	```javascript
	import React from 'react'
	import { render } from 'react-dom'
	
	const node = document.querySelector('#app')
	const element = <div>Hello World!</div>
	
	render(element, node)
	```

* Often we want to render more complex components which encapsulate logic, styles, and multiple DOM components, and use lifecycle events (e.g. `componentWillMount`) and set any states. In this case, we want to use should use `class`.

	```javascript
	import React, { Component } from 'react'
	import { render } from 'react-dom'

	class Counter extends Component {
	
	  state = {count: 0}
	
	  componentDidMount() {
	    setInterval(() => {
	      this.setState({count: this.state.count + 1})
	    }, 1000)
	  }
	
	  render() {
	    const {count} = this.state
	    const {color, size} = this.props
	
	    return (
	      <div style={{color, fontSize: size}}>
	        {count}
	      </div>
	    )
	  }
	}
	
	class App extends Component {
	  render() {
	    const style = {
	      flex: 1,
	      justifyContent: 'center',
	      alignItems: 'center',
	    }
	
	    return (
	      <div style={style}>
	        <Counter color={'lightblue'} size={16} />
	        <Counter color={'skyblue'} size={32} />
	        <Counter color={'steelblue'} size={80} />
	        <Counter color={'darkblue'} size={140} />
	      </div>
	    )
	  }
	}
	
	render(<App />, document.querySelector('#app'))
	```

* If you use `class`, [make sure](http://cheng.logdown.com/posts/2016/03/26/683329) to call `super()` if you have a constructor.

> ES6 class constructors MUST call `super()` if they are subclasses. Thus, you have to call `super()` as long as you have a constructor. (But a subclass does not have to have a constructor)

```javascript

import React, { Component } from 'react'
import { render } from 'react-dom'

// OK
class MyClass extends Component {
	render(){
		return <div>Hello { this.props.world }</div>;
    }
}

// OK
class MyClass extends React.component{
	constructor(props){
		super(props);
		console.log(this.props); // this.props is undefined
    }
}

// OK
class MyClass extends React.component{
    render(){
    	// There is no need to call `super(props)` or even having a constructor 
    	// this.props is automatically set for you by React 
    	// not just in render but another where else other than the constructor
        console.log(this.props);  // it works!

    }
}
```

In summary:

* Calling `super()` is necessary if you need to have a `constructor`.
* Calling `super(props)` is necessary if you want to access `this.props` inside the `constructor`
* ES6 class constructors MUST call `super()` if they are subclasses; however a subclass does not have to have a constructor.


According to the [Official Documentation](https://reactjs.org/docs/react-component.html#constructor)
> The `constructor` for a React component is called before it is mounted. When implementing the `constructor` for a `React.Component` subclass, you should call `super(props)` before any other statement. Otherwise, this.props will be undefined in the constructor

>The `constructor` is the right place to initialize state. To do so, just assign an object to this.state; don’t try to call `setState()` from the constructor. The constructor is also often used to bind event handlers to the class instance.

>If you don’t initialize state and you don’t bind methods, you don’t need to implement a constructor for your React component.

Note, The difference between `constructor` and `getInitialState` is the difference between ES6 and ES5 itself. `getInitialState` is used with React.createClass and `constructor` is used with `React.Component`.

```javascript
import React, { Component } from 'react'
import { render } from 'react-dom'
class MyClass extends React.component{
	constructor(props) {
		super(props);
	  	this.handleChange = this.handleChange.bind(this);
	  	this.state = {
	  		color: props.initialColor
	  	}
	}
	handleChange(e) {
		this.setState({temperature: e.target.value});
	}
	render() {
		// ...
	}
	
}

```
Beware of this pattern, as state won’t be up-to-date with any props update. Instead of syncing props to state, you often want to [lift the state up](https://reactjs.org/docs/lifting-state-up.html) instead.
> Often, several components need to reflect the same changing data. We recommend lifting the shared state up to their closest common ancestor. Let’s see how this works in action.

**TL;DR** You want to make children render `this.props` instead of `this.state` and have the parent pass a function (say `changeParentProps` which modifies its states, to the chilren so the children can call `onClick={this.props.changeParentProps}` in their render functions.


[Styling React Component](http://www.react.express/styling) - Three ways:

1. Inline Styles
	* Pro:  No libraries/dependencies), dynamic (variables, themes, merging, etc) and works on Works on every React renderer (web, native, etc)
	* Con: No CSS features (psuedoclasses, media queries, keyframe animations) out of the box. 
2. CSS and Class Names
	* Simply write CSS using any pre-processor or post-processor you'd like (LESS, Sass, Stylus, PostCSS), and use the className attribute of your components to apply styles. With this option, you can have many of the CSS features for dynamic styles - pseudo classes (`:hover`), media queries, keyframe animations, etc.


[Event Handling](http://www.react.express/event_handling)

```javascript
// Bad practice

import React, { Component } from 'react'
import { render } from 'react-dom'

class CounterButton extends Component {

  state = {count: 0}

  render() {
    const {count} = this.state

    return (
      <button type='button' onClick={() => this.setState({count: count + 1})}>
        Click HERE to increment: {count}
      </button>
    )
  }
}

render(<CounterButton />, document.querySelector('#app'))
```
>It's generally bad practice to define functions within the props of your React elements like we did in the previous example. This is because a new function will be created each time `render` is called - it's common for components to compare props using `===`, which in this case will indicate that the `onClick` prop of the button has changed, and may cause unnecessary re-renders. Using .bind within component props has a similar effect.

```javascript
// Better

import React, { Component } from 'react'
import { render } from 'react-dom'

class CounterButton extends Component {

  state = {count: 0}

  handleClick = () => {
    const {count} = this.state

    this.setState({count: count + 1})
  }

  render() {
    const {count} = this.state

    return (
      <button type='button' onClick={this.handleClick}>
        Click HERE to increment: {count}
      </button>
    )
  }
}

render(<CounterButton />, document.querySelector('#app'))

```

[Conditional Rendering](http://www.react.express/conditional_rendering)

```javascript
// Rendering with &&
{subtitle && (
	<h2 style={styles.subtitle}>{subtitle}</h2>
 )}

// Rendering with ternary ?

{subtitle ? (
	<h2 style={styles.subtitle}>{subtitle}</h2>
	) : (
	<h3 style={styles.empty}>No subtitle</h3>
)}

// Rendering with if/else
class Card extends Component {
  renderContent() {
    const {title, subtitle} = this.props

    return (
      <div>
        <h1 style={styles.title}>{title}</h1>
        {subtitle ? (
          <h2 style={styles.subtitle}>{subtitle}</h2>
        ) : (
          <h3 style={styles.empty}>No subtitle</h3>
        )}
      </div>
    )
  } 
  render() {
    const {loading, error} = this.props

    let content

    if (error) {
      content = 'Error'
    } else if (loading) {
      content = (
        <h3 style={styles.empty}>Loading...</h3>
      )
    } else {
      content = this.renderContent()
    }

    return (
      <div style={styles.card}>
        {content}
      </div>
    )
  }
}

class App extends Component {
  render() {
    return (
      <div>
        <Card loading={true} />
        <Card error={true} />
        <Card title={'Title'} subtitle={'Subtitle'} />
      </div>
    )
  }
}

```

[List and Keys](http://www.react.express/lists_and_keys)

* Every React component can be passed a special prop called the `key`. React uses this `key` to determine the rendered element's identity. Understanding element identity is critical for performance and minimizing DOM manipulation: for example, if the first element in a list of thousands should no longer be rendered, React needs some way to detect this. Instead of re-rendering thousands of elements, React can simply remove a single DOM node for the first element, which is much more efficient.
* if you wanted to, you could assign a key to every element. You can even force a component to re-render by assigning a different key (this tells React that the element's identity has changed, thus triggering a re-render). But most of the time, you don't need to consider keys because React takes care of them automatically. It's likely the only time you'll need to use them is when rendering lists of elements.
* Ideally, we want to make the `key` the unique identifier for the items in the list (i.e., its actual value). If items in our data have no unique identifier of any kind, then we will generally resort to using the index of the item as its key. This silences the warning from React about forgetting to include keys, but remember that doing this will cause React to identify elements incorrectly if the data is modified

### Learning Resources
**React**

* Brad Westfall's [LearnReact Series](https://github.com/bradwestfall/Learn-React)
* [React.Express Tutorial](http://www.react.express) - Learn about React, Redux, ES6, Babel, Webpack

**React Router**

* [react-router-config](https://www.npmjs.com/package/react-router-config)
* [Starter project tutorial](https://www.mokuji.me/article/universal-app-react-router)
* [Egghead Tutorial](https://egghead.io/lessons/react-create-basic-routes-with-the-react-router-v4-browserrouter)
* [route-config example](https://reacttraining.com/react-router/web/example/route-config)
* [A Simple Tutorial](https://medium.com/@pshrmn/a-simple-react-router-v4-tutorial-7f23ff27adf)
* [Site Point Tutorial](https://www.sitepoint.com/react-router-v4-complete-guide/)
* [Detailed Tutorial](https://blog.digitalkwarts.com/server-side-rendering-with-reactjs-react-router-v4-react-helmet-and-css-modules/)
* [Lessons learned from migrating to v4 from an earlier version](https://webuild.envato.com/blog/a-real-word-story-of-upgrading-react-router-to-v4-in-an-isomorphic-app/)
* [unofficial migration guide](https://codeburst.io/react-router-v4-unofficial-migration-guide-5a370b8905a).
5. [Read this](https://github.com/ReactTraining/react-router/pull/4934) for more on how to fix the deprecation warnings.
	> Failed Context Types: Calling PropTypes validators directly is not supported by the `prop-types` package
* [Integrate React Router v4 with Server Router](https://crypt.codemancers.com/posts/2017-06-03-reactjs-server-side-rendering-with-router-v4-and-redux/)