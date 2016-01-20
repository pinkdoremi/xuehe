title: clip-path
date: 2015-04-25 20:31:45
tags:
- css3
- 动画
categories:
- css
---
趁着这两天比较闲研究了一下这个月风靡一时的属性clip-path。
起先关注到这个属性是看到了一个非常酷的[网站](http://species-in-pieces.com/#)
后来在微博上先后看到了很多人对它的解析，使用的是css3的clip-path属性。
<!-- more -->
# clip-path是个什么样子的属性
clip-path的意思就是裁剪路径，就是把一个对象，进行裁剪后成为你要的形状。
[一个可以切图片的网站](http://bennettfeely.com/clippy/)
使用这个网站可以获得基本的*clip-path*是做什么的概念，顺便可以用用，这个切图片比较方便。
# clip-path基本用法简介
利用上面的网站直接获得代码固然方便，但是基础用法还是需要去了解。
## inset()
裁剪一个矩形（可以带圆角），但是不是你想怎么矩就怎么矩的矩形（想要这种矩形用polygon），是一个相对于外框内缩自定义距离的矩形。

    clip-path:inset( <shape-arg>{1,4} [round <border-radius>]? )
前面四个值代表上右下左的内缩距离，后面的值代表了圆角大小
eg:

    clip-path: inset(2% 0% 0% 0% round 50% 20% 15px 20px);

## circle()
裁剪一个圆。

    clip-path:circle( [<shape-radius>]? [at <position>]? )
圆半径大小 at 圆心位置
eg:

    -webkit-clip-path: circle(40% at 50% 50%);

## ellipse()
椭圆

    clip-path:ellipse( [<shape-radius>{2}]? [at <position>]? )
宽半径 长半径 at 中心位置
eg:

    -webkit-clip-path: ellipse(52% 50% at 50% 50%);
## polygon()
多边形

    clip-path:polygon( [<fill-rule>,]? [<shape-arg> <shape-arg>]# )
第一个值可以不写，写的话是nonzero&evenodd中选一个。
nonzero是默认选项。这个值决定某一点是在内部还是外部。可以看[这个](http://www.w3cschool.cc/svg/svg-polygon.html)感受一下这个值干嘛用的.
后面的值就是裁剪的点坐标，按先后顺序绘制路径。
eg:

    clip-path: polygon(nonzero,50% 0%, 100% 100%, 0% 100%);
上面是个三角形
# 动画
使用`transition:clip-path .5s`，并且变化clip-path的路径可以制作文章最开头的那种炫酷的动画。
如果每个碎片都能有自己不同的dela时间件就更酷了。
此外，要相同的点坐标数量才能够做动画，不同的点坐标是连贯不起来的。
# 碎片风格和裁剪风景图片
无疑这两个是最近比较时尚的点，clip-path都能够驾驭。
clip-path真是个比较潮的属性啊。。。
# others
## 配合sass
如果确定要用clip-path画图片或者做动画的话请务必使用函数的写法写css。循环会有很大的帮助。并且，大部分的工作量是在获取很多很多坐标上。
## 兼容性
兼容性不是太好,需要带前缀。见[caniuse](http://caniuse.com/#search=clip-path)
## 拓展
[利用clip-path打造3D模型渲染器](http://www.html-js.com/article/2815)