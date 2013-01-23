---
layout: post
title: 耍两个css3小trick
date: 2011-08-28 16:02:05
comments: true
tags: 
---

介绍两个css3做的小效果。都是在webkit下做着玩的，完全没考虑浏览器兼容什么的。

**效果一：摇晃**

用过iphone的同学应该知道，当你要调整图标顺序或者删除app的时候，长按之，然后所有图标都会很萌的在那里打颤，并且脑门上会多出来一个小叉让你点。

仔细看了一下，其实这种小颤的效果就是图标在左右小幅度摇摆而已，当中或许还加入了一些很细微的随机位移吧。

好，我现在想把这种效果用到web页面上要怎么弄嘞。

一开始我想到的是css3 transition这个属性。transition，顾名思义就是过渡的意思，语法如下：

transition:prop duration timingfunc delay [[reference](http://www.w3schools.com/css3/css3_transitions.asp)]

这样就会在某个property改变的时候先delay一段时间，然后从原状态通过timingfunc所代表的缓动方程，经过duration的时间，过渡到目标状态。

我们要用它来完成shake的效果，就需要用setInterval反复的把元素旋转的角度正负来回设，中间状态会由transition来完成。

目的是达到了，但是transition表示压力很大，人家只是个做过渡的，循环动画什么的有点儿hold不住啊。setInterval第一次执行的时候肯定会有停顿，而且元素一多，不断的设置一堆dom元素的属性，肯定会慢得要死的啊笨蛋！

有木有更好的办法嘞？答案是有的。不就是做循环动画咩，有专门干这活儿的家伙。就是我们的css3 animation属性集啦。

写起来就像这样：
<pre lang="css">.elem{
	-webkit-animation-name: shake;//自己定义的动画名字
	-webkit-animation-duration: .15s;
	-webkit-animation-timing-function: linear;
	-webkit-animation-direction: alternate;	//顺着放完后倒带放回来
	-webkit-animation-iteration-count: infinite; //就这样直到永远
}

@-webkit-keyframes shake {
	from {-webkit-transform:rotate(-5deg);}
	to {-webkit-transform:rotate(5deg);}
}</pre>
效果不错，丝般顺滑。[猛击这里](http://spud.in/demo/shake/)看下效果对比。

**效果二：翻转**

这个效果需要用到的几样东西说明一下。

_-webkit-transform: rotateY(180deg);_

这个属性之前也有用到。3d旋转有xyz三根轴，x轴从左到右，y轴从上到下，z轴从屏幕里戳出来。rotateX,rotateY,rotateZ可以分别使元素围绕其旋转。上一例中的普通rotate就可以想象成围绕z轴的旋转。此外还有translate,scale等属性可以用来作平移啊缩放啊等事情。[[reference](http://www.w3.org/TR/css3-3d-transforms/#transform-property)]

_-webkit-transform-style: preserve-3d_

这个属性有两个值，preserve-3d|flat。前者可以使其子元素能够在他的3d空间的基础上应用3d变换。

设为flat的话子元素就都跟他贴在一个平面上了。默认值似乎是前者，所以可以省略。[[reference](http://www.webkit.org/blog-files/3d-transforms/transform-style.html)]

_-webkit-perspective: 500_

定义了元素进行3d变换时的透视程度，可以想象成镜头距离物件的距离，默认是无限远，就是看起来完全没有透视感。火影忍者里的很多打斗就大量运用了夸张的透视效果。

![](http://spud.in/wp-content/uploads/2011/08/naruto1-300x181.png "naruto")

_-webkit-backface-visibility: hidden_

这个属性也是两个值initial和hidden。前者的话我们的元素就相当于一张塑料薄片，翻转之后上面的内容还是可见的，设置为hidden它就变成了一张卡纸，翻过去就不可见了

最后你可以在源码中发现翻转的hover写在了最外层的元素上，这是因为翻转的时候实际上改变了元素的大小，这样，动画一开始元素缩小了，hover状态就没有了，这显然不是我们希望的，放到一个透明的外层容器上去就没事情喽。

[猛击这里](http://spud.in/demo/flip/)看demo吧！