---
title: CSS vs JS动画，哪个更快
date: 2018-4-17 12:19:22
tags: [CSS, JavaScript, 前端动画]
---
英文原文：https://davidwalsh.name/css-js-animation

原作者Julian Shapiro是Velocity.js的作者，Velocity.js是一个高效易用的js动画库。在《Javascript网页动画设计》一书中对这个库有很多更具体的剖析.

# JQuery

 Javascript 和 jQuery 两者不能错误的混为一谈。Javascript 动画很快，而 jQuery 动画却慢下来。为什么呢？因为尽管 jQuery 非常强大，但是它的设计目标并不是一个高效的动画引擎：

1. JQuery不能避免layout thrashing，由于它不仅仅要服务于动画，也需要用于其他场景。

2. JQuery的内存消耗会频繁的触发垃圾回收机制，而垃圾回收会让动画暂时卡住。

3. JQuery使用了setInterval而不是requestAnimationFrame(RAF)，为了避免RAF在失去焦点的时候停止动画(译者注：JQuery3.0集成了RAF，不支持IE8及以下版本了)。

注意 layout thrashing 是导致动画在开始的时候卡顿的原因，垃圾回收是导致动画运行过程中的卡顿的原因，不使用 RAF 通常会导致动画帧率低。

```
var currentTop, currentLeft;

/* With layout thrashing. */
currentTop = element.style.top; /* QUERY */
element.style.top = currentTop + 1; /* UPDATE */

currentLeft = element.style.left; /* QUERY */
element.style.left = currentLeft + 1; /* UPDATE */

/* Without layout thrashing. */
currentTop = element.style.top; /* QUERY */
currentLeft = element.style.left; /* QUERY */

element.style.top = currentTop + 1; /* UPDATE */
element.style.left = currentLeft + 1; /* UPDATE */
```

在更新操作之后的访问操作会强制浏览器重新计算页面元素的样式（因为要将更新的样式应用上去才能获取正确的值）。这个在一般操作下没太大的性能损失，但是放在间隔仅仅16ms的动画中则会导致显著的性能开销。只需要稍微改动下操作的顺序就可以大大提高动画的性能。

类似地，使用 RAF 也不会迫使你大量重构现在的代码。让我们来比较下使用 RAF 和使用 setInterval 的区别：

```
var startingTop = 0;

/* setInterval: Runs every 16ms to achieve 60fps (1000ms/60 ~= 16ms). */
setInterval(function() {
    /* Since this ticks 60 times a second, we divide the top property's increment of 1 unit per 1 second by 60. */
    element.style.top = (startingTop += 1/60);
}, 16);

/* requestAnimationFrame: Attempts to run at 60fps based on whether the browser is in an optimal state. */
function tick () {
    element.style.top = (startingTop += 1/60);
}

window.requestAnimationFrame(tick);
```

RAF可以提供对动画性能最大可能的提升，而为此你只需要对你的代码进行一个简单的修改。

# css Transitions

CSS transition的性能能够比 jQuery 动画好，transition将动画逻辑抛给浏览器自身来执行。它的优势体现在：

1.通过优化 DOM 操作和内存消耗来避免卡顿

2.利用RAF底层的原理

3.强制硬件加速（使用GPU加速来提高动画效果）。

然而实际上Javascript可以直接使用这些优化。GSAP 已经做这些优化有些年头了。Velocity.js ，一个新的动画引擎，不仅仅实现了相同的技术，而且走的更远些。

面对现实，让 Javascript 动画可以与 CSS 动画性能竞争只是我们复兴计划的第一步。第二步是意识到事实上 Javascript 动画比 CSS 动画快！

让我们以测试CSS动画的缺陷开始吧：

1.Transition的强制使用GPU加速，致使动画卡顿不流畅和高负荷下的束缚。这种情况在移动设备上会加剧发生。（特别需要说明的是，卡顿是由于当数据在浏览器的主线程和其排序线程之间传输的时候发生的过载引起的。一些CSS属性，例如transforms和opacity，不会受到这种限制。）Adobe兄弟的博客谈论过这个话题。

2. Transition在IE10不会起作用，这使得很多桌面场景残留的IE8、IE9中会产生兼容问题。

3. 由于Transition不能原生地被javascript控制（仅仅是被javascript触发），浏览器不知道如何去优化操作Transition的javascript代码。

相反的，基于javascript的动画库可以自己决定什么时候启用硬件加速，他们很自然的在IE各版本下正常工作，而且他们也非常适合批量动画优化。

我的建议是可以在专门为移动端开发的页面上使用原生的css Transitions,并且你的动画只由简单的状态变化组成。在这样的情况下，transitions是一个允许你把动画的逻辑全部保留在css样式表里面，不会因为使用太多javascript库导致页面膨胀的 高性能内嵌的方法。但是，你如果正在设计复杂的UI动画或者正开发一款多状态UI的APP，记得用动画库，以便你的动画处于高性能状态，你的工作流程处于易处理状态。

JavaScript动画

javascript在使用的时候性能是可以占优势的。但javascript究竟可以快多少？开始--快到足以建立一个对比强烈的3D演示动画，就像你想的那样，通常用webGl才能完成的。快到足以建立一个复杂的多媒体动画，通常使用Flash或After Effects才能完成的。

快到可以构建一个虚拟世界，通常使用canvas才能完成的。

为了直接比较主流的动画库的表现，包括Transit（它使用了CSS的Transition），可以在这里先看看Velocity的官方文档：VelocityJS.org

之前的问题还在：javascript如何达到高性能的？

下面列出了一些优化，javascript的动画库可以胜任的表现：

1. 同步DOM->调整堆栈之间的动画链来最小化布局抖动。

2. 在链式调用中缓存属性值来最小化DOM查询的发生（这就是高性能动画的阿喀琉斯之踵）。

3. 当更新基本上看不出来时，跳过样式更新。

可以重温下我们之前讨论过的布局抖动，velocity.js利用这些最佳实践，来缓存动画结束值，使其被重用为随后的动画的起始值。以此来避免再次查询DOM元素的起始值。

 ```
$element
/* Slide the element down into view. */
.velocity({ opacity: 1, top: "50%" })
/* After a delay of 1000ms, slide the element out of view. */
.velocity({ opacity: 0, top: "-50%" }, { delay: 1000 });
 ```

 在上面的例子中，第二次velocity调用时已经知道，应该在opacity为1，top为50%时自动开始。

浏览器可以自身执行类似这样的优化，但是这样做会很大程度上限制开发者手工写动画代码的方法。

因此，基于同样的原因，Jquery不用RAF（见上文），浏览器从不实行 可能会打破规范或者偏离预期行为的优化。

最后，让我们来比较一下两个javascript动画库（velocity和GSAP）。

1. GSAP是一个快速，功能丰富的动画库平台。velocity是一个大大提高UI动画性能和工作刘的轻量级的工具。

2. GSAP对于种类繁多的商业应用需要付费，velocity是完全开源免费的，它使用了十分自由的MITlicence。

3. 在实际应用中，两者的性能表现相当。

我的建议是你需要精确控制时间的时候使用GSAP（例如：remapping,暂停\继续\跳过），移动（例如贝塞尔曲线），或者复杂的动画组合\队列。这些特性对于游戏开发及一些特殊应用十分重要，但在web应用中不太常见。

GSAP有着富特性功能，但不意味着Velocity自身功能不丰富。相反，在压缩之后7KB的包里面，Velocity不仅仅实现了Jqueryanimate的所有功能，也打包了色彩动画，变换，循环，移动，类动画，滚动。
