---
date: '2017-01-24 14:20 +0100'
published: true
title: React - User Event Callbacks
socialImg: react
category:
  - react
---
```js
var Greeter = React.createClass({

    handleSubmit: function (e) {
        e.preventDefault()
        console.log(this.refs.input.value)
    },

    render: function() {
        return (
            <div>
                <form onSubmit={this.handleSubmit}>
                    <input type="text" ref="input" />
                    <button>Set Name</button>
                </form>
            </div>
        )
    }
})

ReactDOM.render(<Greeter />, document.getElementById("app"))
```
