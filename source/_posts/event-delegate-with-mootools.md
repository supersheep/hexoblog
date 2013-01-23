---
layout: post
title: Mootools事件代理
date: 2011-05-03 23:21:55
comments: true
tags: 
---

写了一个用于事件代理的小函数，更多功能有待完善。
<pre lang="javascript">
function Delegate(holder,select,type,fn){
	holder = $(holder);
	holder.addEvent(type,function(e){
		var c = holder.getElements(select);
		if( c.contains(e.target)){
			fn.call(e.target,e);
		};
	});
}
</pre>
用法：
<pre lang="javascript">
Delegate('wrapper','a.inner','click',function(e){
	console.log(e.target);
});
</pre>
由于javascript的事件有向里捕获与向外冒泡的过程，外层容器可以在冒泡阶段获得来自内层容器的事件，继而进行处理。

这种做法可以有效避免在内层元素被添加，改变时额外的绑定事件的开销，对于比较复杂的应用很有实际意义。

缺点是一些接口事件，如focus,blur等不存在冒泡的过程，需要用其他办法去hack之。

[参考](http://www.planabc.net/2010/01/30/how_to_use_focus_and_blur_event_in_event_delegation/ "如何在事件代理中正确使用 focus 和 blur 事件")