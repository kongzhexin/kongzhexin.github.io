---
title: jQuery源码callbacks解析
date: 2017-02-08 08:25:00
tags: [jQuery,callback,源码]
---

## $.Callbacks() 

* 它就是回调函数队列，控制它的执时机，其是jQuery中很重要的一部分，是后面的deferred和ajax模块的基础，所以我们必须要理解它
* 它的主要思想是pub / sub（观察者模式）， 是促进应用程序的松散耦合。而比对其他对象的方法调用的单个对象，一个对象，而不是另一个对象的一个特定的任务或活动，并通知当它发生。观察家也被称为订阅者，它指向观察对象。观察者（Publisher）事件发生时通知用户 
* 关于它的具体用法请移步 [jQuery3.0中文文档API](http://www.css88.com/jqapi-1.9/jQuery.Callbacks/)


## 源码如下：

对重要部分做了注释，不对的地方请指出
```
jQuery.Callbacks = function( options ) {

	// Convert options from String-formatted to Object-formatted if needed
	// (we check in cache first)
	options = typeof options === "string" ?
		createOptions( options ) :
		jQuery.extend( {}, options );

	var // Flag to know if list is currently firing
		firing,

		// Last fire value for non-forgettable lists
		memory,

		// Flag to know if list was already fired 
		fired,

		// Flag to prevent firing 正在执行回调是，不触发执行
		locked,

		// Actual callback list   回调列表
		list = [],

		// Queue of execution data for repeatable lists  存储上下文和参数  ？具体可能还有其他用处待发现在补充
		queue = [],

		// Index of currently firing callback (modified by add/remove as needed)
		firingIndex = -1,

		// Fire callbacks
		fire = function() {

			// Enforce single-firing
			locked = options.once;

			// Execute callbacks for all pending executions,
			// respecting firingIndex overrides and runtime changes
			fired = firing = true;
			for ( ; queue.length; firingIndex = -1 ) {
				memory = queue.shift();  //上下文和参数
				while ( ++firingIndex < list.length ) {

					// Run callback and check for early termination 执行回调并且检查是否提前结束 stopOnFalse
					if ( list[ firingIndex ].apply( memory[ 0 ], memory[ 1 ] ) === false &&
						options.stopOnFalse ) {

						// Jump to end and forget the data so .add doesn't re-fire
						firingIndex = list.length;
						memory = false;
					}
				}
			}

			// Forget the data if we're done with it
			if ( !options.memory ) {
				memory = false;
			}

			firing = false;

			// Clean up if we're done firing for good
			if ( locked ) {

				// Keep an empty list if we have data for future add calls
				if ( memory ) {
					list = [];

				// Otherwise, this object is spent
				} else {
					list = "";
				}
			}
		},

		// Actual Callbacks object  实际的Callbacks对象包含各种方法add、fire、remove等
		self = {

			// Add a callback or a collection of callbacks to the list
			add: function() {
				if ( list ) {

					// If we have memory from a past run, we should fire after adding
					if ( memory && !firing ) {
						firingIndex = list.length - 1;
						queue.push( memory );
					}
                    //闭包自执行函数 参数为add的回调列表
					( function add( args ) {
						jQuery.each( args, function( _, arg ) {
							if ( jQuery.isFunction( arg ) ) {
								if ( !options.unique || !self.has( arg ) ) {   //不含有相同的就添加到列表中
									list.push( arg );
								}
							} else if ( arg && arg.length && jQuery.type( arg ) !== "string" ) {

								// Inspect recursively
								add( arg );
							}
						} );
					} )( arguments );  //add（）的参数

					if ( memory && !firing ) {    //如果 memory 并当前无正在执行的回调，添加后则执行回调
						fire();
					}
				}
				return this;
			},

			// Remove a callback from the list
			remove: function() {
				jQuery.each( arguments, function( _, arg ) {
					var index;
					while ( ( index = jQuery.inArray( arg, list, index ) ) > -1 ) {
						list.splice( index, 1 );

						// Handle firing indexes
						if ( index <= firingIndex ) {
							firingIndex--;
						}
					}
				} );
				return this;
			},

			// Check if a given callback is in the list.
			// If no argument is given, return whether or not list has callbacks attached.
			has: function( fn ) {
				return fn ?
					jQuery.inArray( fn, list ) > -1 :
					list.length > 0;
			},

			// Remove all callbacks from the list
			empty: function() {
				if ( list ) {
					list = [];
				}
				return this;
			},

			// Disable .fire and .add
			// Abort any current/pending executions
			// Clear all callbacks and values
			disable: function() {
				locked = queue = [];
				list = memory = "";
				return this;
			},
			disabled: function() {
				return !list;
			},

			// Disable .fire
			// Also disable .add unless we have memory (since it would have no effect)
			// Abort any pending executions
			lock: function() {
				locked = queue = [];
				if ( !memory && !firing ) {
					list = memory = "";
				}
				return this;
			},
			locked: function() {
				return !!locked;
			},

			// Call all callbacks with the given context and arguments 执行回调用给定的上下文和参数
			fireWith: function( context, args ) {
				if ( !locked ) {
					args = args || [];
					args = [ context, args.slice ? args.slice() : args ];
					queue.push( args );
					if ( !firing ) {
						fire();
					}
				}
				return this;
			},

			// Call all the callbacks with the given arguments  执行回调，调用fireWith函数 ，可以给回调列表传递参数
			fire: function() {
				self.fireWith( this, arguments );   
				return this;
			},

			// To know if the callbacks have already been called at least once
			fired: function() {
				return !!fired;
			}
		};

	return self;   //返回创建的对象
};
```