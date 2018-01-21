# How to Properly Integrate D3 into React

[Performance Models](http://www.react.express/performance_model)
>Modifying the DOM is slow. React is built to modify that DOM as little as possible. React builds an in-memory representation of the components you render, and then syncs this representation to the DOM. As you update components, React will update the in-memory representation, and then make the least amount of changes to the DOM possible.

This is an important point if you want to add `d3` to React. You do not want to make the `d3` "component" part of the DOM; rather, you want to allow React high level control over what `d3` takes as input to render itself and sync `d3`'s data model (i.e., `csv` or `json`) with a `state` variable within the React master component.

>In cases where component `render` is happening too frequently and causing performance issues, React gives you more control over the component lifecycle with `shouldComponentUpdate`, allowing you to choose exactly when a component does and doesn't re-render. When this method isn't defined, components will always re-render whenever their state or props change. Defining `shouldComponentUpdate` allows you to manually compare whatever data you need to determine whether or not the component should re-render. Return true to trigger a render call and false to prevent it.
>you can safely skip the `shouldComponentUpdate` until you notice sluggish app behavior.