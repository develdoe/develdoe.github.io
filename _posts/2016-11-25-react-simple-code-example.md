---
date: '2016-11-25 15:32 +0100'
published: true
title: React - Simple Code Example
category:
  - react
---

html:

```html
<!DOCTYPE html>
<html lang=en>
<head>

    <meta charset=utf-8>
    <link rel=stylesheet href=cli.css>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/babel-core/5.8.23/browser.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/react/0.14.7/react.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/react/0.14.7/react-dom.js"></script>

</head>
<body>

    <div id=app></div>

    <script type=text/babel src=app.jsx>

    </script>

</body>
</html>
```

app.jsx:

```jsx
var Output = React.createClass({
    render: function(){
        return (
            <section>
                <span>{this.props.output}</span>
            </section>
        )
    }
})
var Input = React.createClass({

    submit: function(e){
        e.preventDefault()
        var state = {}
        if (this.refs.input.value.length > 0) {
            state.output  = this.refs.input.value
            this.refs.input.value = ''
        }
        this.props.updateState(state)
    },
    render: function(){
        return (
            <section>
                <form onSubmit={this.submit}> <input type="text" ref="input" autoFocus /> </form>
            </section>
        )
    }
})
var CLI = React.createClass({

    getDefaultProps: function(){
        return {
            output: '',
        }
    },
    getInitialState: function(){
        return {
            output: this.props.output
        }
    },
    updateState : function(updates) {
        this.setState(updates)
    },
    render: function() {
        return (
            <div>
                <Output output={this.state.output}/>
                <Input updateState={this.updateState}/>
            </div>
        )
    }
})

ReactDOM.render(<CLI/>,document.getElementById('app'))
```

server.js:

```js
var express = require('express'),
    app     = express()


app.use(express.static('public'))

app.listen(4000, function(){
    console.log('Express server is up on port: 4000')
})
```