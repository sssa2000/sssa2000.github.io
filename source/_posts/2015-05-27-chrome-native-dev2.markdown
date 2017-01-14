---
layout: post
title: "chrome native开发环境搭建"
date: 2015-05-27 23:29
comments: true
categories: chrome-dev

---

<!--more-->
本文记录一些在搭建chrome native开发环境中的一些过程。
我的OS环境是：win8 64。
##必要的准备工作
- 跟google玩没有好一点的梯子是不行的，而且还需要下载速度比较快的，否则寸步难行。
- ProcessExplorer。你可能需要它把chrome进程杀掉
- 如果你要用最新的native sdk，确保你的chrome版本不比sdk版本低
- 牢记在chrome中的进入开发者模式的快捷键：F12，以后会经常用到
- 在你的chrome浏览器书签加入这几个页面，以后你可能经常访问
```
chrome://flags/
chrome://plugins/
chrome://settings/
```

##搭建环境
1. 安装native sdk和python 2.7。natvie sdk的下载地址是：   https://developer.chrome.com/native-client/sdk/download
2. 把sdk的zip解开，这个zip其实还不是真正的sdk，只是下载器，它会根据你输入的版本号选择要安装的依赖项。对国内的网络环境而言这不是一个好消息。比如对我来说就挺糟糕，因为下载器是工作在console下，我只有浏览器能翻墙。所以这里我没有使用官方推荐的安装方式，而是直接打开zip包中的naclsdk_manifest2.json，在里面找到了sdk的下载地址：
```
{
  "bundles": [
    {
      "archives": [
        {
          "checksum": {
            "sha1": "3c63a6002dea894616292bb25d6b375dba0290f1"
          },
          "host_os": "all",
          "size": 30527,
          "url": "https://storage.googleapis.com/nativeclient-mirror/nacl/nacl_sdk/trunk.314836/sdk_tools.tgz"
        }
      ],
      "description": "Native Client SDK Tools, revision 314836",
      "name": "sdk_tools",
      "recommended": "yes",
      "revision": 314836,
      "stability": "stable",
      "version": 1
    }
  ],
  "manifest_version": 2
}
```

4. 把这个sdk_tools.tgz 下载以后直接解开就是真正的native sdk了，我这里得到的是pepper_37。
5. 添加环境变量。
 - 把python 2.7的bin目录页加到环境变量PATH中。
 - 新增环境变量：NACL_SDK_ROOT，设置为sdk的目录，比如 C:\nacl_sdk\pepper_37\
 - 新增环境变量：CHROME_PAT为chrome.exe的路径，比如比如: C:\Users\fred\AppData\Local\Google\Chrome SxS\Application\chrome.exe (这里还有一个选择，后面说)。
 
6. 我个人建议，在开发、调试的使用chromium来替代chrome会更方便，因为开发过程中会需要频繁的关闭浏览器，如果你还想开发过程上个网会很不方便；其次，chromium有代码，即使你不想编译，也可以下载编译好的exe和pdb，方便调试。由于我是win64，所以我在这个页面https://commondatastorage.googleapis.com/chromium-browser-continuous/index.html?prefix=Win_x64/ 找到最后一行，这里标明了，最近一次build的版本号是多少，然后找到这个版本对应的目录，下载。
{%img/images/chromedev2/1.jpg%}
如果进行了这一步，还需要把CHROME_PAT改为chromium中chrome.exe的路径

##编译运行example以及tutorial
在C:\nacl_sdk\pepper_37\example下，打开cmd，运行“make serve”，然后就等着吧，编译完以后，会自动开启一个简单的http服务器，然后打开chrome，输入“localhost：5103即可运行每一个example”
{%img/images/chromedev2/2.jpg%}

在运行之前，你需要确保chrome的flag设置正确
- 打开NaCl的flag。位于chrome://flags/ 页面。{%img/images/chromedev2/3.jpg%}
- 进入开发者模式，进入设置，关闭cache。{%img/images/chromedev2/4.jpg%}
- 另外运行之前最好彻底关闭chrome的进程，再启动。如果你用的是chromium就不用了。

**遇到的问题1**：编译不过:“/x86_64-nacl-ld: cannot find -lppapi_gles2”的错误
解决方法:如果安装了Cygwin，需要把Cygwin的bin目录从PATH中排除掉


**遇到的问题2**:UnicodeDecodeError: 'ascii' codec can't decode byte 0xb0 in position 1: ordinal
解决方法:可能与某些国产软件对注册表的改写的gbk格式导致python无法进行第三方库的安装操作。
在这行前面添加三行：
```python
if sys.getdefaultencoding() != 'gbk':  
    reload(sys)  
    sys.setdefaultencoding('gbk')  
```

##参考
- http://blog.csdn.net/gufeng99/article/details/45752307
- http://blog.csdn.net/xoyojank/article/details/8142630

