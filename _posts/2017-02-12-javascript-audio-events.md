---
date: '2017-02-12 23:17 +0100'
published: true
title: JavaScript - Audio Events
category:
  - JavaScript
---
```js
audio.addEventListener('progress', function(e) { console.log('progress') }, false)
audio.addEventListener('timeupdate', function(e) { console.log('Time Update') }, false)
audio.addEventListener('abort', function(e) { console.log('abort') }, false)
audio.addEventListener('blur', function(e) { console.log('blur') }, false)
audio.addEventListener('canplay', function(e) { console.log('canplay') }, false)
audio.addEventListener('canplaythrough', function(e) { console.log('canplaythrough') }, false)
audio.addEventListener('change', function(e) { console.log('change') }, false)
audio.addEventListener('drag', function(e) { console.log('drag') }, false)
audio.addEventListener('dragend', function(e) { console.log('draged') }, false)
audio.addEventListener('dragenter', function(e) { console.log('dragenter') }, false)
audio.addEventListener('dragleave', function(e) { console.log('dragleave') }, false)
audio.addEventListener('dragover', function(e) { console.log('dragover') }, false)
audio.addEventListener('dragstart', function(e) { console.log('dragstart') }, false)
audio.addEventListener('drop', function(e) { console.log('drop') }, false)
audio.addEventListener('durationchange', function(e) { console.log('durationchange') }, false)
audio.addEventListener('emptied', function(e) { console.log('emptied') }, false)
audio.addEventListener('ended', function(e) { console.log('ended') }, false)
audio.addEventListener('error', function(e) { console.log('error') }, false)
audio.addEventListener('focus', function(e) { console.log('focus') }, false)
audio.addEventListener('focusin', function(e) { console.log('focusin') }, false)
audio.addEventListener('focusout', function(e) { console.log('focusout') }, false)
audio.addEventListener('input', function(e) { console.log('input') }, false)
audio.addEventListener('keydown', function(e) { console.log('keydown') }, false)
audio.addEventListener('keypress', function(e) { console.log('keypress') }, false);
audio.addEventListener('load', function(e) { console.log('load') }, false);
audio.addEventListener('loadeddata', function(e) { console.log('loadeddata') }, false);
audio.addEventListener('loadedmetadata', function(e) { console.log('loadedmetadata') }, false);
audio.addEventListener('loadstart', function(e) { console.log('loadstart') }, false);
audio.addEventListener('mousedown', function(e) { console.log('mousedown') }, false);
audio.addEventListener('mousemove', function(e) { console.log('mousemove') }, false);
audio.addEventListener('mouseout', function(e) { console.log('mouseout') }, false);
audio.addEventListener('mouseover', function(e) { console.log('mouseover') }, false);
audio.addEventListener('mouseup', function(e) { console.log('mouseup') }, false);
audio.addEventListener('onkeyup', function(e) { console.log('onkeyup') }, false);
audio.addEventListener('onselect', function(e) { console.log('onselect') }, false);
audio.addEventListener('pause', function(e) { console.log('pause') }, false);
audio.addEventListener('ratechange', function(e) { console.log(ratechange) }, false);
audio.addEventListener('readystatechange', function(e) { console.log('readystatechange') }, false);
audio.addEventListener('reset', function(e) { console.log('reset') }, false);
audio.addEventListener('seeked', function(e) { console.log('seeked') }, false);
audio.addEventListener('seeking', function(e) { console.log('seeking') }, false);
audio.addEventListener('suspend', function(e) { console.log('suspend') }, false);
audio.addEventListener('volumechange', function(e) { console.log('volumechange') }, false);
audio.addEventListener('waiting', function(e) { console.log('waiting') }, false);
```
