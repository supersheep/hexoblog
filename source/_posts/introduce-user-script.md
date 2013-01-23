---
layout: post
title: UserScript初探
date: 2010-07-31 17:02:32
comments: true
tags: 
---

所谓UserScript就是存在用户自己本地的一段脚本。要使用的话不同浏览器需要安装不同的扩展，或插件，详情[见此](http://emulefans.com/userscript-on-various-browsers/)。Chrome下可以直接使用。以下就以Chrome为例做下介绍。

1.Hello World

UserScript的文件命名格式均为***.user.js，你可以试着新建名为helloword.user.js的文件，编辑文本 alert("hello"); 保存。然后把他拖入Chrome中，看看会发生什么。浏览器应该就会问你是否要安装了。这个脚本的作用就是在每个页面加载完毕之后都会弹出个警告框显示一句"hello"。真是个讨人厌的脚本啊，可以去<span title="chrome://extensions/">扩展页面</span>把它卸了。

2.源数据

所谓源数据就是一些遵照特定格式的注释。他们在运行的时候不会起任何作用，但是会包含一些特殊信息以说明自己。UserScript的源数据格式如下
<pre lang="javascript">
// ==UserScript==
// @name          Hello World
// @namespace  mynamespace
// @description  example script to alert "Hello world!" on everypage
// @include       *
// @exclude      http://spud.in/*
// ==/UserScript==
</pre>
他们可以出现在文档的任何位置，但一般放在头部。namespace用以和其他脚本区分，include说明了要包含此脚本的页面，exclude的页面则不包含。

这样每次访问符合要求的页面时，脚本便会被调用，以完成一些功能。事实上用户脚本机制在运作中把我们的脚本包装在了一个匿名函数包里，变成下面这样，以防止和页面中原有的脚本产生冲突。
<pre lang="javascript">
(function (unsafeWindow) {
alert("hello");
})(window);
</pre>
3.有了这些其实就可以根据自己的需要做一些事情了，比如给每个页面添加分享按钮之类的。需要一点html和js基础，不过都不会太难。而且一般而言userscript也会比extension跟轻一些。

自己也做了个小东西，用处是给豆瓣的导航上添加一个写日记的入口。[点此](http://spud.in/demo/douban_add_note.user.js)安装。没什么技术含量哈。

想深入学习的可以[点这儿](http://www.firefox.net.cn/dig/)虽然是针对火狐油猴扩展写的教程，但道理是一样的。