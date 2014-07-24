title: Ext Js 5中的委托和手势
date: 2014-07-23 21:30:33
tags:
- 手势
- 触摸
- 委托
- 翻译
catgories:
- ExtJs
---
这两天因为给SenchTouch项目做手势功能，找了很多资料。sencha touch本身的文档没有介绍关于手势功能的，我这几天会总结一下sencha 的touch event并且整理一篇文章出来。
这篇是自己翻译的Ext Js 5带来的事件系统的改变。

#基本介绍
在Ext Js 5之前，Ext Js只为桌面设备提供了传统的鼠标输入。但是在5中，我们也增加了触摸输入。因此Ext Js可以适用于很多设备，比如说平板，带触摸屏的笔记本。这新增的功能将对框架的使用者透明了，但是对这些机制的深入了解会帮助你更好地使用它。在这篇文章中，我们会介绍框架如何来处理这些事件，以及事件在不同的设备中的适配。
<!-- more --> 
#Ext JS 中的手势
也许Ext js5所增加的最令人激动的部分就是它的手势事件。因为Sencha Touch事件系统已经为最新的Ext js 5事件系统做好了铺垫，Sencha Touch开发者也许会发现他们已经对这些手势很熟悉了。对于门外汉来说，手势也许是一个很复杂的事件机制，它从很多小的浏览器事件合成而来，比如说，**“drag”**, **“swipe”**, **“longpress”**, **“pinch”**,** “rotate”**,** “tap”**。Ext js 5更是让这些手势有了加强，让他们在多种输入的情况下，除了通过触摸输入触发之外还能被鼠标输入触发。
从浏览器的角度上来说，存在3中基本的事件**（start，move ，end）**，这些事件是使用鼠标或者触摸屏来触发的。框架监听事件发生的顺序和持续的事件，并且决定发生的是什么手势。

| Browser|Start|Move|End
|:--:|:--:|:---:|:--:
| Desktop Browsers|mousedown|mousemove|mouseup
| Mobile Webkit|touchstart|touchmove|touchend
| IE10|MSPointerDown|MSPointerMove|MSPointerUp
| IE11|pointerdown|pointermove|pointerup

当框架判断哪个手势发生时，框架对每个正在监听的Elements都发送一个手势事件。监听手势事件就和监听Dom事件一样的听法：

    myElement.on('drag', myFunction);

下面的单触摸事件在所有平台和设备下，无论使用什么输入源（鼠标或者触摸）都能正常运行：

|Gesture    |Events
|:--:|:--:
|Tap|   tap, tapcancel
|DoubleTap| singletap, doubletap
|LongPress| longpress
|Drag|  dragstart, drag, dragend, dragcancel
|Swipe| swipe, swipestart, swipecancel
|EdgeSwipe| edgeswipestart, edgeswipe, edgeswipecancel

下面的多触摸事件可以在任何使用触摸屏的平台设备下运行：

|Gesture|   Events
|:--:|:--:
|Pinch| pinchstart, pinch, pinchend, pinchcancel
|Rotate|    rotatestart, rotate, rotateend, rotatecancel

#委派事件模型
Ext Js 5做一个很细微但是非常重要的范式转变：将直接放置在DOM上的监听转移到委派事件模型上。这就意味着对于每一种类型的事件，单个监听被放置在DOM的最顶层（window），每当一个DOM触发一个事件，在这个事件被处理之前，会冒泡地向上层报告。在内部，这样的复杂的事件只是一小块，因为事件系统模拟事件的传播，是通过从目标元素DOM逐层遍历的方式。乍得一看，这种处理方法也许根本不需要那么多毫无意义的复杂处理，而且只有很少的益处。

1. 委托事件模型是识别手势发生的关键。事件系统不停地监听确定的关键事件的发生的时间和顺序，探测是否发生了的手势。紧接着它将手势，以及组成手势的触摸事件以一种正确地顺序排列好。
2. 它大大减少了对DOM事件监听所需要的配置，因此提高了内存使用。它还给转移DOM的监听提供了便利。此外，因为这种做法简化了卸载时的清理操作，所以降低了老版本浏览器可能出现内存泄露的可能性。
3. 它允许老版本浏览器对事件的“top-down”事件捕捉。因为IE 8没有支持addEventListener()和“useCapture”选项，DOM事件就直接被选择了自下而上冒泡的方式。委托事件机制对这个问题提供了一个解决方法：使用自定义的事件传播路线。这种事件传播路线可以使用冒泡和捕获两种方式。如果想使用捕获，只要在选项里填上“capture”就可以选择一种自上而下的方式。

        myElement.on({
          click: someFunction,
          capture: true
        });

#委派模型的潜在挑战
伴随着框架的底层变化，对于现存的代码可能会存在一些不和谐。当代码和新的事件系统联系起来的时候，大体会陷入这两个窘境：
1. 只使用Ext Js事件系统的API（*i.e.Ext.Element#addListener() , Ext.Element#on() *）监听事件的应用，会不知道这么一个变化：如果只使用Ext Js API去监听事件的话，新的事件系统是100%向下兼容Ext Js 4的。
2. 使用了其他的js库或者直接用DOM API去直接监听事件的应用会被这一个委派机制的增加带来负面影响。因为直接监听的定时和委托定时是不同的，当程序需要以一定的特定顺序处理事件时会产生一些问题。好在的是，如果事件处理时对一个事件使用[stop propagation](https://developer.mozilla.org/en-US/docs/Web/API/event.stopPropagation)，问题会变得比较显而易见。

   + 委派监听器可以调用`stopPropagation()`来暂停委派事件系统模拟事件传播。但是这不会阻止直接连接事件的发生。因为当委派监听器在定时器允许执行的时候已经来不及阻止直接连接在事件上动作，这些事件已经冒泡到了DOM的最顶层。
   + 当直接配置在DOM上的listener调用`stopPropagation`，它会阻止所有委派事件的发生，包括在对DOM中比他低层的。因为`stopPropagaton`上直接连接的事件会阻止原生事件冒泡到顶部，从而防止它被委派事件处理。这也有禁用手势识别的潜力，因为手势在DOM顶处理。

如果因为某些原因，委派的模型不在需要了，监听器可以用一个值来停止使用：
        
    myElement.on({
        click: myFunction,
        delegated: false
    });

选择退出委托事件时，要特别注意。作为直接配置在DOM上的监听，它可能产生同样地副作用。当stopPropagation参与时尤其如此。

#事件统一化
这个新的事件系统的主要目标是让现有的Ext Js应用在平板电脑和带触摸屏的电脑上运行，并且降低升级这个功能需要的精力。为了实现这一点，这个框架在幕后执行基本事件的统一化。当一个鼠标事件的listener被请求到时，这个框架实际上附加了一个类似的触摸事件或指针事件（如果设备支持的话），比如说当应用程序试图连接一个按下鼠标的listener：

    myElement.on('mousedown', someFunction);

在Safari移动端会被解释成：

    myElement.on('touchstart', someFunction);

IE 11：

    myElement.on('pointerdown', someFunction);

和鼠标交互相似的触摸屏交互是被允许的。
以下的鼠标事件是会直接被翻译成触摸事件的（在触摸设备上）：
+ mousedown -> touchstart or pointerdown
+ mousemove -> touchmove or pointermove
+ mouseup -> touchend or pointerup
+ click -> tap
+ dblclick -> doubletap

然而对于某些触摸事件是没有合适的模拟的。如果程序依赖于以下的触摸事件，它会由开发人员来决定是否和如何实现这种输入：
- mouseover
- mouseout
- mouseenter
- mouseleave

虽然事件统一化会有利于大多数应用，有时也有可能需要退出。Ext Js 5对这个目的提供了一个`translate`事件选项：

    myElement.on({
        mousedown: myFunction,
        translate: false
    });

将`translate:false`会阻止事件系统进行对于这个事件的统一化，所以这个方法只会当一个鼠标电击时候才会被调用。

#总结
Ext JS的一直是HTML5的桌面应用程序选择的主要框架，但现在桌面和移动之间的界限正变得越来越模糊。新的Ext JS 5事件系统，拥有手势和和事件统一化，将继续推进传统的桌面式和触摸设备领域。
