---
date: '2017-02-18 22:57 +0100'
published: true
title: 'React - Main, Input, output conponents'
category:
  - React
---
**Main.jsx**

```js
var React = require('react'),
    Input = require('Input'),
    Output = require('Output'),
    api = require('Api')

var Main  = React.createClass({
    getInitialState: function () {
        return{
            isLoading: false
        }
    },
    handleSearch: function (location) {
        var that = this

        that.setState({
            isLoading: true
        })

        api.getTemp(location).then(function (temp) {
            that.setState({
                location: location,
                temp: temp,
                isLoading: false
            })
        }, function (errMessage) {
            alert(errMessage)
            that.setState({
                isLoading: false
            })
        })
    },
    render: function () {
        var {isLoading, temp, location} = this.state

        function renderMessage () {
            if (isLoading) {
                return <h3>Fetching weather...</h3>
            } else if (temp && location) {
                return <Output temp={temp} location={location}/>
            }
        }

        return (
            <div className="component page" id="index" style=>
                <span>Home</span>
                <Input onSearch={this.handleSearch}/>
                {renderMessage()}
            </div>
        )
    }
})

module.exports = Main
```

**Input.jsx**

```js
var React = require('react')

var Input = (props)=>{
    onFormSubmit: function (e) {
        e.preventDefault()

        var location = this.refs.location.value

        if (location.length > 0) {
            this.refs.location.value = ""
            this.props.onSearch(location)
        }
    },
    render: function () {
        return(
            <div className="component" id="home-input">
                <form onSubmit={this.onFormSubmit}>
                    <input type="text" ref="location"/>
                    <button>Get Input</button>
                </form>
            </div>
        )
    }
}
module.exports = Input
```

**Output.jsx**

```js
var React = require('react')

var Output = ({temp, location}) => {
    return (
        <div className="component" id="home-output" >
            <span>It is {temp} in {location}</span>
        </div>
    )
}

module.exports = Output
```