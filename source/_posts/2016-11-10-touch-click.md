---
title: 移动端touch与click
tags: [移动端,事件]
---
移动端touch比click事件快300ms,追其原因是手机端300ms用来等待是否用户进行双击缩放，所以说没有缩放就没有这个bug

为了解决这个问题:
1. 禁止缩放;
2. 移动端的库zepto、fastclick等都在touchend事件中模拟了click事件提高了响应速度;


测试时，请不要携带以下meta中的content（因为该标签会启动禁止缩放）
```
<meta name="viewport" content="width=device-width">
```
javascript代码内容如下:
```javascript  
var startTime;
var log = function (msg) {
    var div = document.createElement('div');
    div.append(document.createTextNode((new Date().getTime()) + ': ' + (new Date().getTime() - startTime) + ': ' + msg))
    document.getElementsByTagName('body')[0].append(div);
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
document.getElementById('d').addEventListener('mousedown', mouseDown);
document.getElementById('d').addEventListener('click', mouseClick);
document.getElementById('d').addEventListener('touchstart', touchStart);
document.getElementById('d').addEventListener('mouseup', mouseUp);
document.getElementById('d').addEventListener('touchend', touchEnd);
```

--EOF--