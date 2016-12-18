---
date: '2016-12-18 12:41 +0100'
published: true
title: JavaScript - Promises
category:
  - js
---
```js
function addPromise(a,b){
    return new Promise(function(resolve,reject){
        if(typeof a === 'number' && typeof b === 'number'){
            resolve(a+b)
        } else {
            reject("these are not two numbers")
        }
    })
}

addPromise(2,2).then(function(temp){
    console.log('promise success: ', temp)
}, function(err){
    console.log('promise error', err)
})
```