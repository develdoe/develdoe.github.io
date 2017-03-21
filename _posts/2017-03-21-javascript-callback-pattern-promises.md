---
date: '2017-03-21 01:08 +0100'
published: true
title: JavaScript - Callback Pattern & Promises
category:
  - JavaScript
---

## Classic Callback pattern

Say we have a async function that takes an location and returns the temp.

```js
function getTempCallback (location, callback) {
	callback(undefined ,78) // success
    callback('City not found') // error
}

getTempCallback('Stockholm', (err, temp) => {
	if(err) {
    	console.log('error', err)
    } else {
    	console.log('success', temp)
    }
})
```


## Promises

With Promises we cant mess up our code and get both success and error to get called, only one of them are going to get fired. 

```js
function getTempPromise(location) {
	return new Promise(function (resolve,reject) {
    	resolve(79)
        reject('City not found')
    })
}

getTempPromise('Stockholm').then(function (temp) {
	console.log('promise success',temp)
}, function (err) {
	console.log('promise error',err)
})