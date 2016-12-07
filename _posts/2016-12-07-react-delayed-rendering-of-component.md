---
date: '2016-12-07 16:13 +0100'
published: true
title: React- Delayed rendering of component
category:
  - js
---
By giving the children a "wait" prop, the component if hidden for the duration that was passed down from the parent. 

By setting the default state to hidden, React will render the component, but it won't be visible.

Then, you can set up componentWillMount to call a function to show it after the duration that was passed via props.

**The component:**

```js
var Hidden = React.createClass({
    getInitialState : function () {
        return({hidden : "hidden"});
    },
    componentWillMount : function () {
        var that = this;
        setTimeout(function() {
            that.show();
        }, that.props.wait);
    },
    show : function () {
        this.setState({hidden : ""});
    },
    render : function () {
        return (
            <div className={this.state.hidden}>
                <p>Hidden</p>
            </div>
        )
    }
});
```

**The Parent:**

```js
var Parent = React.createClass({
    render : function () {
        return (
            <div className="parent">
                <p>Parent</p>
                <div className="child-list">
                    <Child wait={1000} />
                    <Child wait={3000} />
                    <Child wait={5000} />
                </div>
            </div>
        )
    }
});
```
