---
layout: post
title: 短网址生成和还原
date: 2011-02-26 08:09:11
comments: true
tags: 
---

在v2ex询问了一下还原的原理，意外收获生成原理。
自己之前理解的有偏差，其实都是巨简单的事情。

生成：shorturl即以62进制 (0-9a-zA-Z) 转换下id。(思维定势，想复杂了)

还原：拿一下 http header 里的 location。(协议基础没学好，想复杂了)