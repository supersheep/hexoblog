---
layout: post
title: Sinonjs，你的js测试好伙伴
date: 2013-05-03 22:11:16
comments: true
tags:
---

最近似乎一直在到处找测试相关的东西看，某个周末出游回家的路上打开手机搜了一下依赖注入是怎么回事。简单讲，就是我们在做测试的时候要遵循一个原则：明确自己要测试的功能，明确输入和输出，而不依赖于外部的设施。

比如讲，有一个功能，需要从一个服务取到一些数据，处理一下，再输出出来。那这个服务的运行状态其实是不用关心的，我们只关心输入给这个服务什么，可以得到什么。所以这里我们需要放这么一个东西，到原来调用服务的位置上去，就可以让其他部分运转起来看看灵不灵了。依赖注入也就是这个意思，好像说出来就一点都不高级了。

在js的世界里也有做这样事情的元件，[Sinonjs](http://sinonjs.org/) 是也。你可以通过它来生成test spy 和 test stub。

普及下这两个东西的概念：

- spy可以用来作为匿名函数或者包装已有函数，可以记录调用的次数，参数和this的值。
- stub和spy有类似的作用，不过你可以指定它的行为，比如抛出异常或者返回你预设的值。比如A方法用到了B方法，不过B方法还没搞定，但是你知道它要有什么行为，这个时候就可以用stub来模拟。

以前为了测ajax，或者测试文件系统，我们会放一个假文件。但是神说，要高级。这些属于测试本身的逻辑，应该跟着测试用例走，我们来看一个栗子：

``` 
// 这里我们根据id，使用jQuery的ajax方法获取一些数据，
// 再交给callback方法处理，看我们怎么来测试他。
function getTodos(id,callback){
	$.ajax({
		url:"/todo/"+id+"/items",
		success:function(data){
			callback(null,data)
		}
	});
}
```

``` 
// 我们看到jQuery.ajax这个方法属于外部依赖对哇，我们要模拟一个假的来用。
after(function(){
	// 测完之后把真正的jQuery.ajax物归原主
	jQuery.ajax.restore();
});
it("makes get request for todo items",function(){
	sinon.stub($,"ajax"); // 模拟一个假的
	getTodos(42,sinon.spy()); // 再模拟一个匿名函数传到getTodos里去
	assert(jQuery.ajax.calledWithMatch({url:"/todo/42/items"}));
	// 确认jQuery.ajax被以期待中的参数调用了
})
```

在nodejs中的使用也一样

```
var fs = require("fs");
var mocha = require("mocha"); // 测试框架
var assert = require("chai").assert; // 断言类库
var sinon = require("sinon");


function wrap(path){
    var content = fs.readFileSync(path)
    return "<" + content + ">"
}


describe("test wrap",function(){
   	// 先把fs.readFileSync方法狸猫换太子
    before(function(){
        sinon.stub(fs, 'readFileSync').withArgs('a.txt').returns('(=v=)');
    });
   	// 开测
    it("should return <(=v=)>",function(){
        assert.equal(wrap("a.txt"),"<(=v=)>");
    });
    // 测完还回去
    after(function(){
        fs.readFileSync.restore();
    });
});
```

sinon提供了许多方法，可以用来模拟各种参数回调返回值之类，可以去官方文档上翻翻。文章里说到的示例丢了个项目到github上[戳我](git@github.com:supersheep/grunt-sinon-sample.git)，有兴趣的同学可以拿下来看看。

PS:这个标题看起来很ppg有没有。