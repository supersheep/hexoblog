---
layout: post
title: hexo使用小记
date: 2013-03-09 23:32:10
comments: true
tags:
---

快点把这篇拖了很久的凑数文章写掉好睡觉。

[hexo](http://zespia.tw/hexo/docs/deploy.html)是一枚台湾同学制作的静态博客生成器，类似jekyll。这种东西的好处就是不用倒腾数据库，不用摸索博客后台，也不用考虑备份这种事情，所有内容都在自己的电脑上，使用markdown来书写，可以让大家更加关注文字内容本身，生成后的静态文件访问速度也快，评论可以使用第三方的社会化评论工具来做，如果使用github pages来进行托管，那连主机都省了。相对的，要插入图片或者多媒体就会比较绕，要找主题也会相对麻烦些，看个人需要了。

现在你看到的这个页面就出自hexo。我把日志的内容托管到了github上，而生成的页面以及会因环境而不同的配置文件则放到了gitignore里面。然后既然自己租了vps，就在上面也丢了一份版本库，写好git hook，每次代码提交的时候在服务器那里去生成文章内容。当时还发现这么做的时候，显示生成进度的代码会因为输入来自网络而不是标准控制台而报错，自己简单修复了一下。

很高级很有意识有没有，但其实hexo支持用rsync来发布的，可以不用在服务器端搞那么麻烦，果然对于生疏的领域容易产生错误的第一反应，大家不要学我。

插件的话用到了hexo-migrator-wordpress和hexo-generator-feed，用npm装好在config.yml里加上就好。最后讲下在用的主题[left](http://zachholman.com/posts/left/)，原本是写给jekyll的。觉得很合心意，于是转到hexo的时候就简单翻译了一下放在[这里](https://github.com/supersheep/hexoleft)，原项目是MIT协议的，可以随便使用，我就借花献佛一下了。