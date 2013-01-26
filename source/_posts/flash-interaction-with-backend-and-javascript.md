---
layout: post
title: 总结下Flash和外部的交互
date: 2011-08-13 09:22:19
comments: true
tags: 
---

好像是大三下的时候写过一篇利用amfphp和后端程序交互的[文章](http://tangtun.blogbus.com/logs/50442825.html "a")，那不过是篇经验总结而已，个中原理依然不明白，而现在我也越来越不喜欢这种需要依靠第三方工具的笨重方式，只有简洁的东西在能永恒啊。于是这周的空闲时间里不务正业的拿起这块东西又研究了一下。

不同媒介间的通信，最基本的做法，说白了就是互相通过字符串来传递信息。先来看flash和php之间的消息传递，其实和后端通信，说白了就是发http请求过去，拿到运算结果，再做分析处理罢了，ajax是如此，flash也一样， 只是用来发请求的对象略有不同而已，做法如下。

**ajax:**
``` js
var req;
req = new XMLHttpRequest();
req.open('POST','remote.php');
req.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
req.onreadystatechange = handler;
req.send('say=hey man');

function handler() {
	if(this.readyState == 4 &amp;&amp; this.status == 200) {
		console.log(this.responseText);
	}
}
```

**flash:**
``` actionscript
var loader,req,data；
var loader,req,data;
loader = new URLLoader(); // flash.net.URLLoader
req = new URLRequest(); // flash.net.URLRequest
data = new URLVariables(); // flash.net.URLVariables;

data.say = "hey man";			
req.url = 'remote.php';
req.method = URLRequestMethod.POST; // flash.net.URLRequestMethod			
req.data = data;
loader.addEventListener(Event.COMPLETE,handler);
loader.load(req);

function handler(e:Event):void{
	trace(loader.data);//use a certain object to parse it as you like
}
```

得到的response差不多都是这样的
``` text
POST http://spud.in/demo/flash/remote/remote.php HTTP/1.1
Host: spud.in
Connection: keep-alive
Content-Length: 13
Origin: http://spud.in
User-Agent: Mozilla/5.0 (Windows NT 6.1) AppleWebKit/535.1 (KHTML, like Gecko) Chrome/14.0.835.8 Safari/535.1

content-type: application/x-www-form-urlencoded
Accept: */*
Referer: http://spud.in/demo/flash/remote/flash.swf
Accept-Encoding: gzip,deflate,sdch
Accept-Language: zh-CN,zh;q=0.8
Accept-Charset: UTF-8,*;q=0.5

say=hey%20man
```

这部分就这点花头了，这些对象还有更多变的用法，但最基本的就这些了。下面来说下flash和页面上的js之间是如何交互的。

自打Flash 8开始，as中引入了ExternalInterface 这个对象（flash.external.ExternalInterface）。

**flash -&gt; js:**
``` actionscript
ExternalInterface.call(funcionName:String, ... arguments):*
```

可以调用到window对象下的functionName方法。

**js -&gt; flash:**

``` actionscript
ExternalInterface.addCallback(functionName:String, closure:Function):void
```

这个方法会在flash被载入后，将特定的方法丢到承载他的&lt;object&gt;节点上。

有的聪明的同学就好奇了，js里不同类型的参数是怎么和as中不同类型的参数对上号的嘞？

原来flash被载入后会丢一堆方法到window上。

他们会把js方法名，和参数按特定的方式解析成很长一串xml字符串。再用flashobj.CallFunction(theXmlStr)将其传递给flash。更多细节可以参考tencent flash team的[这篇文章](http://flashteam.tencent.com/post/18/externalinterface-and-javascript/ "ExternalInterface 与 JavaScript")

这次调用后，flash中的方法的返回值也按照正确的类型会反应到js中，若flash方法的返回类型为js中没有的复杂类型，则会抛出 "Error calling method on NPObject" 错误。

以上讲的是方法间的互相调用，此外，传递参数这件事情还可以通过flashvars来做，具体[见此](http://kb2.adobe.com/cps/164/tn_16417.html "Use FlashVars to pass variables to SWF files")，不高兴写了嘿嘿。

**Demos:**

with backend ([ajax](http://spud.in/demo/flash/remote/ajax.html)|[flash](http://spud.in/demo/flash/remote/flash.swf)|[source](http://spud.in/demo/flash/remote/remote.zip))

with js ([demo](http://spud.in/demo/flash/external/)|[source](http://spud.in/demo/flash/external/flashExternal.zip))