---
date: '2017-01-24 14:05 +0100'
published: true
title: React - Props
socialImg: react
category:
  - React
---
There are two types of data that get passed into a component, there are props and there is state. Props get passed in as you initialize a component, while a state is internally maintained and updated. 

```js
var Greeter = React.createClass({

    getDefaultProps: function () {
        return {
            name:    'defaultNameProp',
            message: 'defaultMessageProp'
        }
    },

    render: function() {
        return (
            <div>
                <h1>{this.props.name}</h1>
                <p>{this.props.message}</p>
            </div>
        )
    }
})

var lname = "Ray"

ReactDOM.render(<Greeter name={lname}  />, document.getElementById("app"))
```

*Components let you split the UI into independent, reusable pieces, and think about each piece in isolation. Conceptually, components are like JavaScript functions. They accept arbitrary inputs (called "props") and return React elements describing what should appear on the screen.*

*A component, it must never modify its own props. Consider this sum function:*

```js
function sum(a, b) {
  return a + b;
}
```

Such functions are called "pure" because they do not attempt to change their inputs, and always return the same result for the same inputs.

In contrast, this function is impure because it changes its own input:

```js
function withdraw(account, amount) {
  account.total -= amount;
}
```

**React is pretty flexible but it has a single strict rule: All React components must act like pure functions with respect to their props.**
