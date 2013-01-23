---
layout: post
title: 在虾米听豆瓣
date: 2010-06-04 15:57:18
comments: true
tags: 
---

结合豆瓣的数据和虾米的资源做了一个播放专辑用的小工具。反正自己也没有mp3，就没有那种要下了音乐好随身带的需要。[点此](http://spud.in/tools/listenwished.html)用用看。

[![xiamidou](http://farm5.static.flickr.com/4049/4667964283_49d5c7cf80.jpg)](http://spud.in/flickr/photo/4667964283/xiamidou.html "xiamidou")

最开心的还是做界面，因为可以看起来很酷嘛。顺带还画了两个图标，其实用箭头代替方角图标会更好一些。基本要用的功能都做得差不多了就这么po上来吧。

一些小说明：

1.如果弹出窗口说专辑未找到，就是虾米没有添加过这张专辑。

2.如果没有提示，而播放器也不播放，就是虾米添加了这张专辑，但还没上传歌曲。

========================    分享一些制作过程    ========================

1.豆瓣[api](http://www.douban.com/service/apidoc/clients)。也就会点php和js了，[jsapi](http://www.douban.com/js/api.js)的代码吃不透how it works没关系。至少可以知道有哪些函数可以用。

2.比如找到某用户想听的音乐的收藏列表。
<pre lang="javascript">
DOUBAN.getUserCollection({
    uid:'supersheep',
    cat:'music',
    status:'wish',
    callback:function(rs){doSomeThingWithRs}
});
</pre>

3.对json格式的调用。
<pre lang="javascript">
var spud={eyes:{@left:'closed',right:'open'}}
</pre>
简单的通过spud.eye.@left无法在火狐下取得closed这个值。必需以数组形势表示，比如spud.eye['@left']

4.jquery ajax函数使用同步模式。异步操作能够带来更好的体验(执行一个任务时程序继续往下走)，但一些操作必需等待上一部结束才能继续往下做。方法是把设置asyn:false

5.修改浏览器标题。
<pre lang="javascript">
$('title').html('some words')
</pre>

6.修改浏览器url。
<pre lang="javascript">
window.location="url"
</pre>
更多有关[window.location对象](http://www.w3schools.com/jsref/obj_location.asp)

7.php读取文件。
<pre lang="php">
file_get_contents('filepath')
</pre>

有了以上这些我就可以

1.通过豆瓣的api获取收藏中某张专辑的标题和作者。

2.将其交给自己写的一个php处理，找到以此为关键词搜索[结果页](http://www.xiami.com/search?key=64+Souvenir)中的第一个匹配
<pre lang="html">
href="/album/albumid"
</pre>
这种形式的字符串。其中albumid为专辑在虾米的id，是一串数字。

3.将这个数字返回给当前页面，在iframe中打开播放地址 [http://www.xiami.com/song/play?ids=/song/playlist/id/](http://www.xiami.com/song/play?ids=/song/playlist/id/albumid/type/1)**[albumid](http://www.xiami.com/song/play?ids=/song/playlist/id/albumid/type/1)**[/type/1](http://www.xiami.com/song/play?ids=/song/playlist/id/albumid/type/1) 就成了。

tips:
1.[虾米的第一张专辑](http://www.xiami.com/album/1)。

2.目前依然存在跨域安全性问题，暂时无解，不开调试窗口应该看不到这个错误。

3.人懒，目前完全不支持ie内核。

4.如果在豆瓣收藏了[不存在的专辑](http://www.xiami.com/album/194103)，会发生无法读取收藏列表的事情。豆瓣怂货。