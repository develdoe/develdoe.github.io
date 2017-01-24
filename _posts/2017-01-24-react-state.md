---
date: '2017-01-24 14:47 +0100'
published: true
title: React - State
socialImg: react
category:
  - react
---
There are two types of data that get passed into a component, there are props and there is state. Props get passed in as you initialize a component, while a state is internally maintained and updated. 

```js

var Greeter = React.createClass({

    getDefaultProps: function () {
        return {
            name: 'defaultNameProp'
        }
    },

    getInitialState: function () {
        return {
            name: this.props.name
        }
    },

    handleSubmit: function (e) {
        e.preventDefault()
        this.setState({
            name: this.refs.input.value
        })
    },

    render: function() {
        return (
            <div>
                <span>{this.state.name}</span>
                <form onSubmit={this.handleSubmit}>
                    <input type="text" ref="input" />
                    <button>Set Name</button>
                </form>
            </div>
        )
    }
})

var aVar = "aVar"

ReactDOM.render(<Greeter name={aVar}  />, document.getElementById("app"))
```
