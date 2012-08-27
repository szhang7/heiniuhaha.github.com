---
layout: post
title : seajs模块依赖的加载处理
description : 比如执行init.js时，init.js、jquery.plugA.js、jquery.plugB.js都会依赖到jquery，那么这种情况下seajs对jquery如何处理的呢？只执行一次？执行多次？还是其他方式？
category : seajs
tags : [seajs, 依赖, 模块, 原理, 异步加载, cache, 接口]
---
{% include JB/setup %}

最近在做项目的时候发现一些关于模块依赖问题，特记录下:

比如现有3个文件：

	/*init.js*/
	define(function(require, exports, module){
		require('jquery');
		require('jquery.plugA');
	})
	
	/*jquery.plugA.js*/
	define(function(require, exports, module){
		require('jquery');
		require('jquery.plugB');
		//code...
	})
	
	/*jquery.plugB.js*/
	define(functioin(require, exports, module){
		require('jquery');
		//code...
	})


比如执行init.js时，init.js、jquery.plugA.js、jquery.plugB.js都会依赖到jquery，那么这种情况下seajs对jquery如何处理的呢？只执行一次？执行多次？还是其他方式？

此处参考玉伯的回答：
>我对模块调用的理解是，调用是指获取某个模块的接口。在 SeaJS 里，只有 seajs.use, require.async, 和 require 会产生
模块调用，比如：
<code>var a = require('./a')</code>
在执行 require('./a') 时，会获取模块的接口，如果是第一次调用，会初始化模块 a，以后再调用时，直接返回模块 a 的接口
define 只是注册模块信息，比如打包之后：
<code>define(id, deps, factory)</code>
是注册了一个模块到 seajs.cache 中，define 类似：
<code>seajs.cache[id] = { id: id, dependencies: deps, factory: factory }</code>

>是纯注册信息。

>而 <code>require('./a')</code> 时，才会执行 <code>seajs.cache['a'].factory</code>, 执行后得到 <code>seajs.cache['a'].exports</code>