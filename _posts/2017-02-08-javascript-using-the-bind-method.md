---
date: '2017-02-08 13:39 +0100'
published: true
title: 'JavaScript - Using the bind method '
category:
  - JavaScript
---
The JavaScript bind method has several uses.

Typically, it is used to preserve execution context for a function that executes in another context. **bind** creates a new function that has the same body as the original function. 

The first argument passed to bind specifies the value of the this keyword in the bound function. You can also pass additional, optional arguments to bind, however this is out of the scope of this article. 

### Preserving the execution context using bind

The bind function is often used when adding event listeners.

In the following code example, bind is used to preserve the context of the current object (DataObject). 

The data object is passed to bind by using the this keyword, which provides access to data object properties and functions when the event handler (dataReadyHandler) runs.

To illustrate how bind works, this code creates a custom event:

```js
var data;

var dataReadyEvent = document.createEvent("Event");
dataReadyEvent.initEvent("dataReady", true, false);

function DataObject() {
    this.name = "Data Object";
    this.data = function () {
        return data;
    }
    this.onDataCompleted = dataReadyHandler;
    document.addEventListener('dataReady', this.onDataCompleted.bind(this));
    // To see the result of not using bind, comment out the preceding line, 
    // and uncomment the following line of code.
    // document.addEventListener('dataReady', this.onDataCompleted);
}
function dataReadyHandler() {
    if (console && console.log) {
        console.log("Data object property value: " + this.name);
        console.log("Data object property value: " + this.data());
    }
}

setTimeout(function () {
    data = [0, 1, 2, 3];
    document.dispatchEvent(dataReadyEvent);
    }, 5000);
}

var dataObj = new DataObject();

// Output:
// Data Object
// 0,1,2,3
```

If you comment out the line of code that uses bind, uncomment the line of code that calls addEventListener without bind, and then rerun the code, the dataReadyHandler function will fail.

