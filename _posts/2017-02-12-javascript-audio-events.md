---
date: '2017-02-12 23:17 +0100'
published: true
title: JavaScript - Audio Events
category:
  - JavaScript
---
```js
audio.addEventListener('abort', function(e) { console.log('abort') }, false)
audio.addEventListener('canplay', function(e) { console.log('canplay') }, false)
audio.addEventListener('canplaythrough', function(e) { console.log('canplaythrough') }, false)
audio.addEventListener('durationchange', function(e) { console.log('durationchange') }, false)
audio.addEventListener('emptied', function(e) { console.log('emptied') }, false)
audio.addEventListener('ended', function(e) { console.log('ended') }, false)
audio.addEventListener('error', function(e) { console.log('error') }, false)
audio.addEventListener('loadeddata', function(e) { console.log('loadeddata') }, false)
audio.addEventListener('loadedmetadata', function(e) { console.log('loadedmetadata') }, false)
audio.addEventListener('loadstart', function(e) { console.log('loadstart') }, false)
audio.addEventListener('pause', function(e) { console.log('pause') }, false)
audio.addEventListener('play', function(e) { console.log('play') }, false)
audio.addEventListener('playing', function(e) { console.log('playing') }, false)
audio.addEventListener('progress', function(e) { console.log('progress') }, false)
audio.addEventListener('ratechange', function(e) { console.log('ratechange') }, false)
audio.addEventListener('seeked', function(e) { console.log('seeked') }, false)
audio.addEventListener('seeking', function(e) { console.log('seeking') }, false)
audio.addEventListener('stalled', function(e) { console.log('stalled') }, false)
audio.addEventListener('suspend', function(e) { console.log('suspend') }, false)
audio.addEventListener('timeupdate', function(e) { console.log('timeupdate') }, false)
audio.addEventListener('volumechange', function(e) { console.log('volumechange') }, false)
audio.addEventListener('waiting', function(e) { console.log('waiting') }, false)
```
