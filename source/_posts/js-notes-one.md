---
layout: post
title: js笔记
date: 2011-05-11 08:11:06
comments: true
tags: 
---

1.这个东西很有意思：
<pre lang="javascript">
function A(){
	console.log(typeof this,typeof true,this===true)
}
A.call(true) //object boolean false
</pre>
感觉就像在call的时候包了一层new Boolean()一样。一深究就头疼啊，不管它了。

2.判断鼠标事件是否是mouseover或者mouseout，看事件对象用没有relatedTarget这个属性就可以了。里面存的是与此次事件相关的Dom节点。
比如鼠标从div#a进入div#b，就会触发后者的mouseover事件，此时e.relatedTarget就是前者。

3.事件管理的东西，ghSky同学写过三篇文章，讲得很细致。[1](http://ghsky.com/2010/08/kissy-event-model-part-one.html) [2](http://ghsky.com/2010/08/kissy-event-model-part-two.html) [3](http://ghsky.com/2010/08/kissy-event-model-part-three.html) 

4.判断属性是否为元素的标准属性，比如id存在于div中，而data-time没有，现在很多html5可用性检测就用到了此类技巧。[这篇文章](http://andrew.hedges.name/experiments/in/)里说到了三种方法，并进行了性能比较。
那么如何检测浏览器是否支持某一css属性呢？方法是先设置后访问，不过不要去设置具体的属性，而是去设置cssText，如下:
<pre lang="javascript">
var a = document.createElement('div');
a.style.cssText += 'float:left';
console.log(a.style.float);//left
a.style.cssText += 'blah:yeah';
console.log(a.style.blah);//undefined
</pre>
实验的时候也遇到一些疑惑。chrome里的style下好像是有filter的，但是filter具体做什么我不是特别明白。
<pre lang="javascript">
var a = document.createElement('div');
console.log(a.style.filter); // undefined
console.log(a.style.blah); // undefined
a.style.filter='Alpha(Opacity=100)';
console.log(a.style.filter); //''
a.style.blah = 'yeah';
console.log(a.style.blah);//yeah
</pre>

5.[这篇文章](http://blog.csdn.net/niuyongjie/archive/2009/11/15/4810835.aspx)讲到了js中的constructor和prototype，解释了js的继承机制。感想是js很多地方都是你中有我我中有你，跟太极似的。另，文章看到最后按照前面的解释自己理一遍，觉得最后一张图好像有点问题啊有木有。

写的有点粗糙，反正自己看的，上班去了先=v=