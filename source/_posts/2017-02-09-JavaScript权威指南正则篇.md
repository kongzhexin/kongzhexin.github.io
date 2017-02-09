---
title: JavaScript权威指南正则篇
date: 2017-02-09 08:43:31
tags: 正则
---

### 两种方式
```
var pattern =new RegExp("s$")
var pattern =/^\s$/g 
```

### 常用匹配

[...] 方括号内的任意字符
[^...] 不在方括号内的任意字符    ^不在方括号是开始
. 除换行符和其他Unicode行终止符之外的任意字符
\w   ASCII字符 [a-zA-Z0-9]
\W   非ASCII字符 [^a-zA-Z0-9]
\s  任何空白字符
\S  任何非Unicode空白字符(制表符等一系列)  与 \w不同
\d  任何ASCII数字，等价于[0-9]
\D  [^0-9]
\b  边界   注意：[\b]匹配退格符
\B  非边界
{n,m}   n-m次，不填为无穷
 +1次或多次
？ 0或1次
*0次或多次

### 非贪婪重复

可在匹配后加一个问号
aaa   /a+？/     只会匹配 a
但是aaab  /a+?b/    会匹配整个和预想不一样 ， 不会考虑子串

### 选择、分组和引用

匹配从左到右，左边匹配便会忽视右边的匹配
ab  /a|ab/     只会匹配到a
\1  引用第一个带圆括号的子表达式，参与计数的是左括号
/(['"])[^'"]*\1/
(?:) 不记忆与该组相匹配的字符，不参与索引编码

### 断言

(?= )  括号内必须匹配
(?!  )  接下来的字符都不必匹配
/Java(?!Script)([A-Z]\w*)/

### String方法

str.search(/cript/i)  不支持全局检索
str.replace()  第二个参数可以是函数动态计算替换字符串
str.match()  返回一个匹配数组  如果无g 则只检索第一个
数组的第一个元素就是匹配的字符串，余下元素则是正则表达式中用括号括起来的子表达式

### 解析一个URL
```
function parseUrl(url) {    
    //url   http://www.example.com/david   
     var result =url.match(/(\w+):\/\/([\w.]+)\/(\S*)/);    
     if(result != null){        
        var fullUrl = result[0]; //  http://www.example.com/david       
        var protocol = result[1]; // http        
        var host = result[2]; //www.example.com        
        var path = result[3]  //david    
      }
}
```

### RegExp对象

* new RegExp("\\d{5}","g")

* **属性**
    + source   正则表达式文本
    + lastIndex   下一次检索开始的位置

* **exec()与test()**
    + pattern.exec(str)   忽略g返回与match()一样结果，没有返回null ，有则返回数组 
   返回结果属性index为匹配开始位置，第二次调用从lastIndex开始匹配
    + pattern.test(str)  返回true or false