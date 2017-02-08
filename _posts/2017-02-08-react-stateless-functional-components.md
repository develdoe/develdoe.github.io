---
date: '2017-02-08 14:35 +0100'
published: true
title: React - Stateless Functional Components
category:
  - React
---

**Old syntax**

```js
var Text = React.createClass({
  render: function () {
    return <p>{this.props.children}</p>;
  }
});
React.render(<Text>Hello World</Text>, document.body);
```

**ES2015**

```js
class Text extends React.Component {
  render() {
    return <p>{this.props.children}</p>;
  }
}
React.render(<Text>Hello World</Text>, document.body);
```

**Statless**

```js
const Text = (props) =>
  <p>{props.children}</p>;
// ReactDOM is part of the introduction of React 0.14
ReactDOM.render(
  <Text>Hello World</Text>, 
  document.querySelector('#root')
);
```
