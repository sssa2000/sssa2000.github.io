---
layout: post
title: "谈Max Prerendered Frames"
date: 2014-03-23 02:14
comments: true
categories: 
---

##概念
在N卡驱动中有个选项是： ```Max Prerendered frames``` 。
在A卡驱动中对应的选项叫：```Flip Queue``` 。

这个选项默认是3，意思是：GPU落后CPU 3帧。其实说白了就是Command Buffer容纳命令的上限。


----------
<!-- more -->

这里通过Heaven Benchmark 4.0来举个例子：通过GPUView观察，可以明显的看出来，Max Prerendered frames等于不同的值时的差别。

当设置```Max Prerendered frames=1```时，可以明确的观察到，任意时刻CPU端的command buffer中最多只有一个Present类型的命令（红色交叉线的方块表示Present命令）。
 {% img /images/maxprerenderframe/1.jpg %}
 

当设置```Max Prerendered frames=3```时，可以明确的观察到，任意时刻CPU端的command buffer中最多可以有3个Present类型的命令（红色交叉线的方块表示Present命令）。
 {% img /images/maxprerenderframe/2.jpg %}
 

当然，如果GPU处理的快，那么CPU端的Command Buffer中也会出现不足3个的情况。
##调整该数值的原则
Command Bufferr容纳的命令越多，GPU就能够有足够的时间处理渲染任务，即使GPU处理的时间有一定的波动，也不会影响画面的流畅。但是带来的坏处是：延迟增加了。

所以，一般来说，如果瓶颈位于CPU侧，那么这个选项应该调小，例如设置为1；反之如果GPU是瓶颈，可以把这个值调大（不建议大于5）。

##那些隐晦的事情

 - 该设置只对Direct3D的程序有作用。这个不难理解。
 - 在win7、win8的窗口模式下，调整这个值是不起作用的。找到这个现象花费了有2个晚上的时间，至于原因，肯定又和烦人的dwm有关系

。

