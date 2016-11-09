---
title: 移动端touch与click
---
请不要携带以下meta
```
<meta name="viewport" content="width=device-width">```
script代码内容如下:
```javascript  
 var startTime;
    var log = function (msg) {
        var div = $('<div></div>');
        div.html((new Date().getTime()) + ': ' + (new Date().getTime() - startTime) + ': ' + msg)
        $('body').append(div);
    };
    var touchStart = function () {
        startTime = new Date().getTime();
        log('touchStart');
    };
    var touchEnd = function () {
        log('touchEnd');
    };
    var mouseDown = function () {
        log('mouseDown');
    };
    var mouseClick = function () {
        log('mouseClick');
    };
    var mouseUp = function () {
        log('mouseUp');
    };
    var d = $('#d');
    document.getElementById('d').addEventListener('mousedown', mouseDown);
    document.getElementById('d').addEventListener('click', mouseClick);
    document.getElementById('d').addEventListener('touchstart', touchStart);
    document.getElementById('d').addEventListener('mouseup', mouseUp);
    document.getElementById('d').addEventListener('touchend', touchEnd)；```