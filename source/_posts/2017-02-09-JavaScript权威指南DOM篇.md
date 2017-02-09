---
title: JavaScript权威指南DOM篇
date: 2017-02-09 07:52:03
tags: JavaScript
---

1. 获取元素的方法常用API
```
getElementById
getElementsByName
getElementsByTagName
getElementsByClassName
querySelectorAll    //通过css选择器
```
2. getElementById与querySelectorAll**区别**
getElementById等是实时的
querySelectorAll是静态的NodeList集合，IE8以上支持
```
x = document.querySelectorAll('img')
y = document.getElementsByTagName('img')
document.body.appendChild(new Image())
x.length // 0
y.length // 1
```
3. Node的重要属性，注意：**文本也是Node**
parentNode 、childNodes 、firstChild、lastChild、nextSibling、previoursSibling
上面的很容易理解，不多解释
nodeType
1 代表Element节点 3代表Text节点  8代表Comment节点 9代表Document  11 代表DocumentFragment节点
nodeValue
Text或Comment节点内容
nodeName
元素的大写标签名

4. 属性
getAttribute()不返回数值、布尔值或对象
setAttribute("class","test")设置属性
removeAttribute()
hasAttribute()
attributes 属性集合,实时的
5. 创建、插入和删除节点
### 创建
```
document.createElement("script")
document.createTextNode("")
document.createDocumentFragment()   //性能考虑容器
function reverse(n){
     var f=document.createDocumentFragment();
     while(n.lastChild){
          f.appendChild(n.lastChild)   
     }
     n.appendChild(f);
}
```
### 插入
父.appendChild(子)   //子已有则是剪切粘贴
父.insertBefore(新节点，已有节点 )   //根据语义，参数顺序新的在旧的之前
### 删除
父.removeChild()
父.replaceChild(新节点，已有节点)   //新的替代旧的

6. 文档坐标和视口坐标
### scrollLeft和scrollTop获取已经滚动的距离
```
function getScrollOffsets(w){
     w =w ||window;
     if(w.pageXOffset !=null){
        return {x:w.pageXOffset ,y:pageYOffset  }  
     }
     var d=w.document;
     if(document.compatMode =="CSS1Compat"){
     return {x:document.documentElement.scrollLeft ,y:document.documentElement.scrollTop }
     }
     else{
     return {x:document.body.scrollLeft ,y:document.body.scrollTop }
     }
}
```
### 获取视口的尺寸
```
function getViewportSize(w) {    
    w =w||window;   
    if(w.innerHeight !=null){         
        return {x:w.innerWidth,y:w.innerHeight};    
    }    
    if(document.compatMode == "CSS1Compat"){       
        return {
               x:document.documentElement.clientWidth,            
               y:document.documentElement.clientHeight
            }  
     }else{    
         return {            
                x:document.body.clientWidth,      
                y:document.body.clientHeight      
            }    
      }
}
```
### 获取元素的尺寸
getBoundingClientRect()返回元素的left top right bottom，**兼容性很棒**
除了IE之外，其他浏览器会有width、height值
**视口坐标+滚动条位置 =文档坐标**
7. 滚动
```
window.scrollTo() window.scrollTop()
window.scrollBy(0,10) 坐标是相对的
offsetHeight offsetWidth返回width+padding+border
offsetLeft offsetTop 相对于定位父元素的
offsetParent  父元素
clientWidth  clientHeight   //返回width+padding
clientLeft 、clientTop   // 左、上边框的宽度
```
8. 其他
```
window.getSelection().toString()  
document.selection.createRange().text
contenteditable
visibility  hidden/visible   文档中仍保留其位置
display   none   
```
**隐藏和显示定位元素时，首选visibility**
e.style.left = "300px"  定位属性**必须为字符串有单位**
document.styleSheets 表示与文档关联的样式表
数组元素为CSSStyleSheet对象，元素为 一个cssRules[]数组，元素为样式规则
var firstRule = document.styleSheets[0].CSSStyleSheet[0]
insertRule  deleteRule   IE下 addRule  removeRule
 document.createStyleSheet   //添加样式