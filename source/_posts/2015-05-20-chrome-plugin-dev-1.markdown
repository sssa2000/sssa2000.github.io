---
layout: post
title: "chrome native开发中的一些概念"
date: 2015-05-20 00:28
comments: true
categories: chrome-dev

---
<!--more-->
##缘由

Chrome 42，禁用了 Netscape 插件 API。google声称：原因是 NPAPI“已经成为导致众多失去响应、甭溃、安全事故以及代码复杂化的罪魁祸首”。

QQ炫舞2 的web端是构建于firebreath上的，不幸的是firebreath中使用了npapi，所以想看看能不能有什么办法能在chrome上做点事情。

------
##插件 PlugIn
官方给出的解释是：
```
Plugins are external binaries that add new capabilities to a web browser and are loaded when content of the type they declare is embedded into a page. 

源自：
https://www.chromium.org/nativeclient/getting-started/getting-started-background-and-basics
```
在chrome中的常见插件有：flash的播放插件、pdf的插件等。你可以在chrome地址栏中输入:chrome://plugins/ 查看，例如下图

{%img/images/chromedev/plugin_1.jpg%}

一般来说插件都是运行在独立的进程里的。

------

##NPAPI和PPAPI
NPAPI（Netscape Plugin Application Programming Interface，网景插件应用程序接口）是被广泛使用的接口，几乎各大浏览器都支持这个接口，可以基于这个接口开发浏览器的插件。

PPAPI的提出是因为NPAPI的可移植性和性能存在比较大的问题，特别是针对跨进程的插件，特别是插件需要2D和3D绘图，声音等问题时候就更为棘手。

PPAPI插件有两种运行模式，受信(trusted)插件和非受信(untrusted)插件。
- 对于受信的PPAPI插件，它可以在Renderer进程中运行，也可以跨进程运行，它可以调用平台相关的API，没有限制。
- 对于非受信的PPAPI插件则运行在NaCl的沙盒中，只能调用沙盒提供的接口，收货了跨操作系统的红利。


但是google的文档也提到：
>
the only way to load trusted Pepper plugins is through the browser command-line options. In the future, Pepper plugins will only be supported within Native Client.


------
##扩展 extension

在chrome中扩展和插件是完全不同的东西。组成结构就不同，扩展由html+javascript+css+native client组成。其中，native client部分可能没有。

google给extension的定位是：增强chrome的功能的小程序。extension和app很像，没有特别明确的界限。

extension受到的安全方面的限制比app要少一点，extension能够从浏览器的地址栏直接启动。

------
##应用 app
相比扩展来说，chrome的应用的定位不是增强浏览器的功能，更像是一些和浏览器无关的程序。这些程序能具备更复杂的ui甚至独立的窗。app还分为两类，托管的和非托管的。
  - 托管在服务器的application。例如application只提供一个网址，所有软件的功能都在远端服务器完成。
  - 不依赖服务器可以在用户机器独自运行的application。

------
##NaCl 和 PNaCl
我们可以使用javascript+html+css来开发扩展开发App，但是当需要大量计算的时候这些都无法胜任。还是需要c/c++来进行开发。于是就有了NaCl。

Native Client（NaCl)，Portable Native Client (PNaCl)。这是两项技术，或者你可以认为是两个SDK+工具链。可以用这两个技术来使用c/c++来开发chrome的扩展、APP。

Native Client 是一个沙盒技术。是目前google主推的使用native code的开发扩展的方式。有了NaCl，可以在Chrome里安全运行Intel x86或ARM原生代码的沙盒技术，方便开发者使用C和C++代码在浏览器里实现高效的应用。

使用NaCl开发的模块是nexe文件，这样的文件是和操作系统无关的，但是还不能做到和硬件架构无关。NaCl开发的nexe只能通过google的web store进行发布，因为web store会帮你识别用户的硬件架构，把合适的硬件相关的nexe安装到用户机器上。

为了解决硬件架构兼容性的问题，又出现了PNacl。PNacl的工具链把代码编译为中间码解决了这个问题。chrome加载一个pexe的时候会把pexe翻译为NaCl的代码然后再执行。

PNaCl开发的模块得到的文件是pexe。pexe不需要通过Google Web Store来发布。

google建议，如果可能尽可能使用PNaCl。当然也有某些情况只能使用NaCl：
- 你的app使用了处理器相关的代码，例如内联汇编
- 你的app用了动态链接。PNacl只支持静态链接，只支持 c的newlib库。动态链接以及glibc在pnacl是不被支持的。
- 一些gnu的扩展是不被pnacl的llvm支持的，例如goto到某个address，或者嵌套的函数

 {%img/images/chromedev/nacl-pnacl-component-diagram.png%}


------

##PPAPI和NaCl的关系
- NaCl的内部是实现在PPAPI之上的。 
- PPAPI只是一个提供给c/c++的接口和实现；nacl还包括了工具链，运行在浏览器里的沙盒等等。
- PPAPI编写的插件能够通过命令行直接从chrome启动插件，NaCl编写的nexe不能。

------
##参考
>https://www.chromium.org/nativeclient/getting-started/getting-started-background-and-basics

>https://developer.chrome.com/native-client

 