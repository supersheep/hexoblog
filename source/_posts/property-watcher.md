---
layout: post
title: 闲得蛋疼之属性观察者
date: 2011-07-19 23:48:03
comments: true
tags: 
---

javascript以前是没有getter和setter的，就是说我没办法在一个变量值被改变的时候，去做一些事情。

一开始觉得，非对象类的值没办法作为引用传给函数，就是没有 function(&str){...} 这样的写法，死穴啊。后来一下开窍，js有js的路数嘛，只能传对象引用就传变量所处对象的引用好了，变量名当成字符串传，放一块儿就能找到对应的变量引用了。

比如：var s; watch(s); 这样行不通（s被当做值传递了），那我可以这样：var s;watch(window,'s')，window是引用，通过window['s']就能拿到s的引用了。

这样一来以上问题就有办法咯，上菜：

<pre lang="javascript">
Object.prototype.watch = function(k,func){
	var env = this,t = env[k];

	setInterval(function(){
		if(env[k] !== t){
			func.call(this,t,env[k]);
			t=env[k]);
		}
	},100);
}	
</pre>

说白了其实就是设一个timer不断去检测一个变量看他有没有变，没什么技术含量。用起来就像这样：

<pre lang="javascript">
var s;
window.watch('s',function(oldv,newv){
	console.log('varible s has changed from '+ oldv + ' to ' + newv);
});
s=3;//varible s has changed from undefined to 3

//还可以这样用：
var input = document.getElementById('username');
input.watch('value',function(oldv,newv){
	console.log('input value has changed from '+ oldv + ' to ' + newv);
});		
</pre>

这样就很happy啦。其实新的javascript 标准里已经包含类似的接口了，我们可以这样写：
<pre lang="javascript">
window.__defineGetter__('s',function(){console.log('getting s')});
window.__defineSetter__('s',function(v){console.log('varible s is set to '+v )});
window.s;//getting s
window.s=3;//varible s is set to 3
</pre>
更多介绍可以看John Resig的[文章](http://ejohn.org/blog/javascript-getters-and-setters/)。

还有些细节我也还没太明白，看起来defineGetter这样一句里面就已经做了声明s的事情，额外写一句var s反而会适得其反，没有效果= =

最后留个困扰我很久的问题，类似事件啊，消息啊，getter/setter这种东西，其内在的原理是什么？我想了半天还是觉得最终其实都应该是靠不断循环判断吧。就像comet跟传统的ajax轮询的区别其实也就在于把一个不断向服务器发送请求的循环变成服务器自己在那里轮询文件修改日期或者数据库，再返回结果。也就是说把循环放到执行效率更高的地方去而已，不知道我的理解是否正确……