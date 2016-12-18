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

addPromise(2,2).then(function(response){
    console.log('promise success: ', response)
}, function(response){
    console.log('promise error', response)
})

addPromise("ray",2).then(function(response){
    console.log('promise success: ', response)
}, function(response){
    console.log('promise error', response)
})
```