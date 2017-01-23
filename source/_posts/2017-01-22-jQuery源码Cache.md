---
title: jQuery源码缓存
date: 2017-01-22 14:39:08
tags: [jQuery,缓存]
---

    在jQuery中缓存几乎无处不在，在多个模块中都有应用，非常重要的一部分，单个拎出来却是相对简单的内容。

### data构造函数与原型
```
function Data() {
	this.expando = jQuery.expando + Data.uid++;   //expando ="jQuery" + ( version + Math.random() ).replace( /\D/g, "" )   其实就是一个随机数，每次浏览器加载都会生成一个
}

Data.uid = 1;
Data.prototype = {
        cache : function(){},
        get : function(){},
        set : function(){},
        access : function(){},
        remove : function(){},
        hasData : function(){},
}
var dataPriv = new Data();             // 供jQuery使用，比如事件，动画等

var dataUser = new Data();           //供用户使用，存储值、对象
```
### .data()

>   该方法允许我们在DOM元素上绑定任意类型的数据,避免了循环引用的内存泄漏风险 ,因为通过 jQuery 方法删除的或者当用户离开页面时，jQuery 同时也会移除添加在上面的数据。 

>   在之前1.x的jQuery版本里Data的存储并不是现在这样，而是通过id关联，在$.cache中存储,就是为了防止内存泄漏。

>   通过$("div").data('a',1) 和$("div").data({'a',1})， 根据参数的形式与个数执行不同的逻辑，详细过程可跟踪代码测试

>   通过$(".slime").click(function(){})添加，便会存在另一个属性里

<center>
{% asset_img dataUser.png 绑定数据 %}
</center>

<center>
{% asset_img dataPriv.png 绑定事件 %}
</center>

### 源码如下：
```
jQuery.fn.extend( {
	data: function( key, value ) {
		var i, name, data,
			elem = this[ 0 ],
			attrs = elem && elem.attributes;

		// Gets all values
		if ( key === undefined ) {
		
			return data;
		}

		// Sets multiple values
		if ( typeof key === "object" ) {
			return this.each( function() {
				dataUser.set( this, key );
			} );
		}

		return access( this, function( value ) {
			var data;
            ...
			// Set the data...
			this.each( function() {

				// We always store the camelCased key
				dataUser.set( this, key, value );
			} );
		}, null, value, arguments.length > 1, null, true );
	},

	removeData: function( key ) {
		return this.each( function() {
			dataUser.remove( this, key );
		} );
	}
} );

```
