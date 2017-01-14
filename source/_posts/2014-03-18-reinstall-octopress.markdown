---
layout: post
title: "重装Octopress备忘"
date: 2014-03-18 02:02
comments: true
categories: 
---
又趟了一遍坑，弯路就不说了，记录下最简单的步骤，供以后备忘。


安装篇
---

**背景** ：windows系统、已经在github上使用Octopress架设过blog



 - ruby安装：使用RubyInstaller是最省事的方法
http://railsinstaller.org/
安装程序会自动将devkit、git、ssh都设置好了。

 - 设置git的用户密码、github的ssh。
ssh需要到github网站设置；
git用户密码省事的画，下载github for windows吧
 
 - 设置windows环境变量

```
set LC_ALL=zh_CN.UTF-8
set LANG=zh_CN.UTF-8
```


 - 不管你的windows是多少位，安装**32bit的ActivePython 2.7**

 - 在你期盼的目录下打开**bash**（不是cmd），执行：
```
git clone https://github.com/sssa2000/sssa2000.github.io
bundle install 
rake setup_github_pages
rake generate
rake preview
rake deploy
```
- 给windows 右键菜单添加“bash here”
```
Windows Registry Editor Version 5.00
[HKEY_LOCAL_MACHINE\SOFTWARE\Classes\Directory\shell\git_shell]
@="Git Ba&sh Here"

[HKEY_LOCAL_MACHINE\SOFTWARE\Classes\Directory\shell\git_shell\command]
@="wscript \"C:\\RailsInstaller\\Git\\Git Bash.vbs\" \"%1\""
```

最后，记得及时提交source到git，以免悲剧
```
cd /d c:\octopress
git add .
git commit -m "blog"
git push origin source

```
问题篇
---

问题1：如果在执行rake setup_github_pages时出现这样的错误：

> You have already activated rake 10.1.1, but your Gemfile requires rake 0.9.2


- 方法1：
可以先试试这个命令：
```
bundle exec rake setup_github_pages
```
如果成功的话，那么可以在bash中给rake设置一个别名。在bash中执行下面的命令

```
$ echo "alias rake='bundle exec rake'" >> ~/.bash_profile
```

- 方法2：
修改文件：octopress/Gemfiles gem 
将
>   gem ‘rake’, ‘~> 0.9’ 

改为 :

> gem ‘rake’


----------

问题2：ake setup_github_pages ‘hellip’ 不是内部或外部命令
这个错误无关痛痒，无需解决。


----------
