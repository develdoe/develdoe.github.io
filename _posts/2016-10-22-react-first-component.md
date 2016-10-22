---
date: '2016-10-22 15:30 +0200'
published: true
title: React - First Component
category:
  - react
---
The entry point to the ReactJS library is the React object and is made available to us in the global JavaScript namespace.

> Components let you split the UI into independent, reusable pieces, and think about each piece in isolation. React.Component is provided by React.(facebook.github.io, 2016)


```javascript
var App = React.createClass({
    render:function(){
        return(
            React.createElement('div',null,'First Component')
        )
    }
})

React.render(React.createElement(App), document.body)
```

React.createClass creates a component with the given specification. The component must implement the render method that returns a single child element.

React will take care of calling the render method of the component to generate the HTML.

**Even if the render method needs to return a single child, that single child can have an arbitrarily deep structure to contain full-fledged HTML page parts.**

Here, we are making use of React.createElement to create our content. 

It's a singleton method that allows us to create a div element with the content. 

React.createElement creates a ReactElement, which is an internal representation of the DOM element used by React. 

We are passing null as the second argument. This is used to pass and specify attributes for the element. Right now, we are leaving it as blank to create a simple div.

The type of ReactElement can be either a valid HTML tag name like `span`, `h1`, `p`and so on. Or, it can be component created by React.createClass itself.

Once done creating the component, it can be displayed using the React.render method. Here a new ReactElemtn is created for the App component that we have created and it is rendered into the HTML element `body`. 

This is called the mountNode, or mount point for our component and acts as the root node. Instead of passing document.body directly as a container for the component, any other DOM element can be passed.

While constructing the first conponent, we also got an overview of React'stop-level API, that is, making use of React.createClass, React.createElement and React.render.


