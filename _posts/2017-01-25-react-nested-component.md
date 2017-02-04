---
date: '2017-01-25 14:18 +0100'
published: true
title: React - nested component
socialImg: react
category:
  - React
---
```js

var Output = React.createClass({
    getDefaultProps: function () {
        return {
            input: 'defaultProp'
        }
    },
    render: function () {
        return (
            <div>
                <span>{this.props.state}</span>
            </div>
        )
    }
})

var Input = React.createClass({
    onSubmit: function (e) {
        e.preventDefault()
        var inputValue = this.refs.input.value
        if (typeof inputValue === 'string' && inputValue.length > 0) {
            this.refs.input.value = ""
            this.props.onNewInput(inputValue)
        }
    },
    render: function () {
        return (
            <form onSubmit={this.onSubmit}>
                <input type="text" ref="input" />
                <button>Submit</button>
            </form>
        )
    }
})

var MainComponent = React.createClass({
    getInitialState: function () {
        return {
            state: "initState"
        }
    },
    handleNewInput: function (input) {
        this.setState({
            state: input
        })
    },
    render: function() {
        return (
            <div>
                <Output state={this.state.state}/>
                <Input onNewInput={this.handleNewInput}/>
            </div>
        )
    }
})

ReactDOM.render(<MainComponent />, document.getElementById("app"))
```