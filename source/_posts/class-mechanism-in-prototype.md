---
layout: post
title: Prototype中的Class机制
date: 2011-02-19 22:56:58
comments: true
tags: 
---

好吧其实我没有看过Prototype的源码。下午看了一篇有关call跟apply的[文章](http://www.cnblogs.com/sunwangji/archive/2006/08/21/482341.html)。里面有这样一段示例代码：
<pre lang="javascript">
var Class = {
    create: function() {
        return function() {
            this.initialize.apply(this, arguments);
        }
    }
}

var vehicle=Class.create();
vehicle.prototype={
    initialize:function(type){
        this.type=type;
    },
    showSelf:function(){
        alert("this vehicle is "+ this.type);
    }
}

var moto=new vehicle("Moto");
moto.showSelf();
</pre>
文章下面的讨论主要集中在
<pre lang="javascript">
this.initialize.apply(this, arguments);
</pre>
这句中的两个this是不是指同一个东西，如果是，那么他是指什么？

下面众说纷纭，我来谈谈我试验后的看法：这里的this指的是new vehicle('moto') 这个对象。
首先，vehicle=Class.create(); 这句话中，Class.create()返回的是一个函数体。也就相当于
<pre lang="javascript">
vehicle=function() {
    this.initialize.apply(this, arguments);
}</pre>
[这篇文章](http://www.ijavascript.cn/shouce/javascript-operator-new-454.html)里提到：
> new 运算符执行下面的任务：> 
> 创建一个没有成员的对象。> 
> 为那个对象调用构造函数，传递一个指针给新创建的对象作为 this 指针。> 
> 然后构造函数根据传递给它的参数初始化该对象。
所以说在 **new vehicle("Moto")** 这句的时候其中的this自然就指向了新创建的对象。

最后，**this.initialize.apply(this, arguments);**
这句就是把构造函数的实参(arguments)带到initialize里，再把initialize放到构造函数里去执行一下。算是一个代理的步骤吧。

当然，javascript跟传统的OO还是有蛮大区别的，他本是没有Class这个东西。一些类库弄那么麻烦，绕来绕去构造出一个类来，也就是为了避免大家各执一套写法，代码乱掉(太灵活的弊端orz)，并且让写传统OO比较多的程序员写起来更顺畅一些吧。以上功能按我习惯做法其实这样就可以了：
<pre lang="javascript">var vehicle=function(type){
    this.type=type;
    this.showSelf=function(){
	alert("this vehicle is "+ this.type);
    }
};

moto=new vehicle("Moto");
moto.showSelf();</pre>