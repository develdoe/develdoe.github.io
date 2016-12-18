---
date: '2016-12-18 12:41 +0100'
published: true
title: JavaScript - Promises
category:
  - js
---
## The old way

```js
function getTempCallback(location,callback){
    callback(undefined,78)
    callback('City not found')
}

getTempCallback('Gothenburg', function(err,temp){
    if(err){
        console.log('err', err)
    } else{
        console.log("success", temp)
    }
})
```

## With Promises

```js
function getTempPromise(location){
    return new Promise(function(resolve,reject){
        resolve(79)
        reject('City not found')
    })
}

getTempPromise('Gothenburg').then(function(temp){
    console.log('promise success', temp)
}, function(err){
    console.log('promise error', err)
})
```

**Notice how you can't write code that both succed and fails with promise! resolv and reject can only be execute once, and the code above will only show promise success!**