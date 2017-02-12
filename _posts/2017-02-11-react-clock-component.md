---
date: '2017-02-11 01:42 +0100'
published: true
title: React - Clock Component
category:
  - React
---
```js
var React = require('react')

var Clock = React.createClass({

    getInitialState: function () {
        var date = new Date()
        return {
            time: date.toLocaleTimeString('sv-SE')
        }
    },

    myTimer: function () {
      var date = new Date()

      this.setState({
               time: date.toLocaleTimeString('sv-SE')
      })
    },

    componentWillMount: function () {
        setInterval(()=>{ this.myTimer() }, 1000)
    },

    render: function () {
        return (
            <div className="component" id="clock" style={{%raw%}}{{border: '1px solid red',background:'rgba(255,0,0,0.5)'}}{{%endraw%}}>
                <span>{this.state.time}</span>
            </div>
        )
    }

})
```
