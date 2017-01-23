---
title: jQuery源码domReady
date: 2017-01-22 10:30:44
tags: [jQuery,ready]
---


# jQuery3.0 domReady的实现原理
--------------------------------
### 实现思路：
使用了jQuery内部的异步机制Defferred ，回调执行的时机根据dom是否加载完成判断,
$(function(){})和$(document).ready(function(){}) 这两种写法是等价的都会走一样的执行过程

1.  $.fn.ready()  -> readList.then();
2.  $.ready() —> readList.resolveWith();


### 代码如下：
```
// The deferred used on DOM ready
var readyList = jQuery.Deferred();  //声明一个异步对象

jQuery.fn.ready = function( fn ) {

	readyList
		.then( fn )

		// Wrap jQuery.readyException in a function so that the lookup
		// happens at the time of error handling instead of callback
		// registration.
		.catch( function( error ) {
			jQuery.readyException( error );
		} );

	return this;
};

//扩展$.ready() 方法 在completed里调用
jQuery.extend( {

	// Is the DOM ready to be used? Set to true once it occurs.
	isReady: false,

	// A counter to track how many items to wait for before
	// the ready event fires. See #6781
	readyWait: 1,
	
	// Handle when the DOM is ready
	ready: function( wait ) {

		// Abort if there are pending holds or we're already ready
		if ( wait === true ? --jQuery.readyWait : jQuery.isReady ) {
			return;
		}

		// Remember that the DOM is ready
		jQuery.isReady = true;

		// If a normal DOM Ready event fired, decrement, and wait if need be
		if ( wait !== true && --jQuery.readyWait > 0 ) {
			return;
		}

		// If there are functions bound, to execute
		readyList.resolveWith( document, [ jQuery ] );   // 触发回调firewith() ，执行fn函数;
	}
} );

jQuery.ready.then = readyList.then;    //  ？？？？注释掉并无影响

// The ready event handler and self cleanup method
function completed() {
	document.removeEventListener( "DOMContentLoaded", completed );
	window.removeEventListener( "load", completed );
	jQuery.ready();
}

// Catch cases where $(document).ready() is called
// after the browser event has already occurred.
// Support: IE <=9 - 10 only
// Older IE sometimes signals "interactive" too soon
// 判断的部分  根据document的readyState状态   或者  doScroll方法是否可以调用 来判断 dom是否加载完，
//我的理解第一个if为了防止脚本加载的时候dom已经渲染完成的情况
if ( document.readyState === "complete" ||
	( document.readyState !== "loading" && !document.documentElement.doScroll ) ) {

	// Handle it asynchronously to allow scripts the opportunity to delay ready
	window.setTimeout( jQuery.ready );

} else {

	// Use the handy event callback  通过DOM加载完成事件执行回调函数
	document.addEventListener( "DOMContentLoaded", completed );

	// A fallback to window.onload, that will always work
	window.addEventListener( "load", completed );
}
```

基本上理解了关于$(document).Ready()执行的原理，其中关于$.Deferred对象还需深入的了解。