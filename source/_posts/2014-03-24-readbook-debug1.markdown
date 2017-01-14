---
layout: post
title: "《格蠹汇编》读书笔记 1"
date: 2014-03-24 01:37
comments: true
categories: 读书
---

日常的工作中，调试和设计是我偏爱的两个领域。无论是调试还是设计，背后都深藏功与名。没有坚实的基础知识，没有洞察入微的眼光，调试起来一定是痛苦不堪。

《格蠹汇编》中的每一章都是一个独立的故事，作者将排查bug的过程娓娓道来，在我们眼前重现，在这过程中体会调试中每个决策的用意实在是一件美事。

《格蠹汇编》这本书在案头摆放已久，一直没有认真阅读。最近Todo list中的事情完成了一些，终于可以开始了。


----------


<!--more-->

##主要内容
估计是为了照顾我们这样战力比较弱的读者，第一章的debug过程显得很直来直往。基本过程就是：挂上调试器到IE，使用s命令搜索内存，然后使用.writemem把内存中的内容存出来。

不过，作者说windbg不能显示中文，稍微花了点笔墨谈如何确定某个字节是不是中文，但是我发现，其实可以直接使用du命令。前提是这个内存是使用unicode16格式编码的。

----------
##相关知识
###s命令
本章中当之无愧的主角：windbg中的s命令（搜索内存）。命令用法很长，可以匹配内容，还可以匹配对象的类型…最一般的用法是：

```
s -[flag] [type] [起始地址] [长度] [要搜索的模式]
例如：
0:011> s -u 1000 L800000 "当年" 
```

其中关于type的解释如下：


<div align=center>

<table class=MsoTableGrid border=1 cellspacing=0 cellpadding=0
 style='border-collapse:collapse;border:none;mso-border-alt:solid windowtext .5pt;
 mso-yfti-tbllook:1184;mso-padding-alt:0cm 5.4pt 0cm 5.4pt'>
 <tr style='mso-yfti-irow:0;mso-yfti-firstrow:yes'>
  <td width=184 valign=top style='width:138.25pt;border:solid windowtext 1.0pt;
  mso-border-alt:solid windowtext .5pt;padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal align=center style='text-align:center;mso-pagination:widow-orphan'><b><span
  lang=EN-US style='mso-bidi-font-size:10.5pt;font-family:宋体;mso-bidi-font-family:
  宋体;mso-font-kerning:0pt'>Type</span></b><span lang=EN-US style='mso-bidi-font-size:
  10.5pt;font-family:宋体;mso-bidi-font-family:宋体;color:black;mso-font-kerning:
  0pt'><o:p></o:p></span></p>
  </td>
  <td width=184 valign=top style='width:138.25pt;border:solid windowtext 1.0pt;
  border-left:none;mso-border-left-alt:solid windowtext .5pt;mso-border-alt:
  solid windowtext .5pt;padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal align=center style='text-align:center;mso-pagination:widow-orphan'><b><span
  lang=EN-US style='mso-bidi-font-size:10.5pt;font-family:宋体;mso-bidi-font-family:
  宋体;mso-font-kerning:0pt'>Description</span></b><span lang=EN-US
  style='mso-bidi-font-size:10.5pt;font-family:宋体;mso-bidi-font-family:宋体;
  color:black;mso-font-kerning:0pt'><o:p></o:p></span></p>
  </td>
 </tr>
 <tr style='mso-yfti-irow:1'>
  <td width=184 valign=top style='width:138.25pt;border:solid windowtext 1.0pt;
  border-top:none;mso-border-top-alt:solid windowtext .5pt;mso-border-alt:solid windowtext .5pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal align=center style='text-align:center;mso-pagination:widow-orphan'><b><span
  lang=EN-US style='mso-bidi-font-size:10.5pt;font-family:宋体;mso-bidi-font-family:
  宋体;mso-font-kerning:0pt'>b</span></b><span lang=EN-US style='mso-bidi-font-size:
  10.5pt;font-family:宋体;mso-bidi-font-family:宋体;mso-font-kerning:0pt'><o:p></o:p></span></p>
  </td>
  <td width=184 valign=top style='width:138.25pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  mso-border-top-alt:solid windowtext .5pt;mso-border-left-alt:solid windowtext .5pt;
  mso-border-alt:solid windowtext .5pt;padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal align=center style='text-align:center;mso-pagination:widow-orphan'><span
  lang=EN-US style='mso-bidi-font-size:10.5pt;font-family:宋体;mso-bidi-font-family:
  宋体;mso-font-kerning:0pt'>Byte (8 bits)<o:p></o:p></span></p>
  </td>
 </tr>
 <tr style='mso-yfti-irow:2'>
  <td width=184 valign=top style='width:138.25pt;border:solid windowtext 1.0pt;
  border-top:none;mso-border-top-alt:solid windowtext .5pt;mso-border-alt:solid windowtext .5pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal align=center style='text-align:center;mso-pagination:widow-orphan'><b><span
  lang=EN-US style='mso-bidi-font-size:10.5pt;font-family:宋体;mso-bidi-font-family:
  宋体;mso-font-kerning:0pt'>w</span></b><span lang=EN-US style='mso-bidi-font-size:
  10.5pt;font-family:宋体;mso-bidi-font-family:宋体;mso-font-kerning:0pt'><o:p></o:p></span></p>
  </td>
  <td width=184 valign=top style='width:138.25pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  mso-border-top-alt:solid windowtext .5pt;mso-border-left-alt:solid windowtext .5pt;
  mso-border-alt:solid windowtext .5pt;padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal align=center style='text-align:center;mso-pagination:widow-orphan'><span
  lang=EN-US style='mso-bidi-font-size:10.5pt;font-family:宋体;mso-bidi-font-family:
  宋体;mso-font-kerning:0pt'>WORD (16 bits)<o:p></o:p></span></p>
  </td>
 </tr>
 <tr style='mso-yfti-irow:3'>
  <td width=184 valign=top style='width:138.25pt;border:solid windowtext 1.0pt;
  border-top:none;mso-border-top-alt:solid windowtext .5pt;mso-border-alt:solid windowtext .5pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal align=center style='text-align:center;mso-pagination:widow-orphan'><b><span
  lang=EN-US style='mso-bidi-font-size:10.5pt;font-family:宋体;mso-bidi-font-family:
  宋体;mso-font-kerning:0pt'>d</span></b><span lang=EN-US style='mso-bidi-font-size:
  10.5pt;font-family:宋体;mso-bidi-font-family:宋体;mso-font-kerning:0pt'><o:p></o:p></span></p>
  </td>
  <td width=184 valign=top style='width:138.25pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  mso-border-top-alt:solid windowtext .5pt;mso-border-left-alt:solid windowtext .5pt;
  mso-border-alt:solid windowtext .5pt;padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal align=center style='text-align:center;mso-pagination:widow-orphan'><span
  lang=EN-US style='mso-bidi-font-size:10.5pt;font-family:宋体;mso-bidi-font-family:
  宋体;mso-font-kerning:0pt'>DWORD (32 bits)<o:p></o:p></span></p>
  </td>
 </tr>
 <tr style='mso-yfti-irow:4'>
  <td width=184 valign=top style='width:138.25pt;border:solid windowtext 1.0pt;
  border-top:none;mso-border-top-alt:solid windowtext .5pt;mso-border-alt:solid windowtext .5pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal align=center style='text-align:center;mso-pagination:widow-orphan'><b><span
  lang=EN-US style='mso-bidi-font-size:10.5pt;font-family:宋体;mso-bidi-font-family:
  宋体;mso-font-kerning:0pt'>q</span></b><span lang=EN-US style='mso-bidi-font-size:
  10.5pt;font-family:宋体;mso-bidi-font-family:宋体;mso-font-kerning:0pt'><o:p></o:p></span></p>
  </td>
  <td width=184 valign=top style='width:138.25pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  mso-border-top-alt:solid windowtext .5pt;mso-border-left-alt:solid windowtext .5pt;
  mso-border-alt:solid windowtext .5pt;padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal align=center style='text-align:center;mso-pagination:widow-orphan'><span
  lang=EN-US style='mso-bidi-font-size:10.5pt;font-family:宋体;mso-bidi-font-family:
  宋体;mso-font-kerning:0pt'>QWORD (64 bits)<o:p></o:p></span></p>
  </td>
 </tr>
 <tr style='mso-yfti-irow:5;mso-yfti-lastrow:yes'>
  <td width=184 valign=top style='width:138.25pt;border:solid windowtext 1.0pt;
  border-top:none;mso-border-top-alt:solid windowtext .5pt;mso-border-alt:solid windowtext .5pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal align=center style='text-align:center;mso-pagination:widow-orphan'><b><span
  lang=EN-US style='mso-bidi-font-size:10.5pt;font-family:宋体;mso-bidi-font-family:
  宋体;mso-font-kerning:0pt'>a</span></b><span lang=EN-US style='mso-bidi-font-size:
  10.5pt;font-family:宋体;mso-bidi-font-family:宋体;mso-font-kerning:0pt'><o:p></o:p></span></p>
  </td>
  <td width=184 valign=top style='width:138.25pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  mso-border-top-alt:solid windowtext .5pt;mso-border-left-alt:solid windowtext .5pt;
  mso-border-alt:solid windowtext .5pt;padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal align=center style='text-align:center;mso-pagination:widow-orphan'><span
  lang=EN-US style='mso-bidi-font-size:10.5pt;font-family:宋体;mso-bidi-font-family:
  宋体;mso-font-kerning:0pt'>ASCII string<o:p></o:p></span></p>
  <p class=MsoNormal align=center style='margin-left:27.0pt;text-align:center;
  mso-pagination:widow-orphan'><span lang=EN-US style='mso-bidi-font-size:10.5pt;
  font-family:宋体;mso-bidi-font-family:宋体;mso-font-kerning:0pt'>(not necessarily
  a null-terminated string)<o:p></o:p></span></p>
  </td>
 </tr>
</table>

</div>
关于Range，注意使用字符L开头。
关于Pattern，这里可以指定多个要搜索值

###d命令
d是display的缩写，意思是显示内存的内容。d有很多衍生的命令，在本章中用了这3种：

 - da：查看内存（显示的结果是ascii的方式）
 - db：查看内存（显示的结果是byte的方式或者ascii的方式）
 - du：查看内存（显示的结果是unicode格式）

###e命令
e是enter的缩写，意思是输入一个或者多个内容到内存（如果是多个内容，用空格分离），也就是修改内存的内容。本章中用了下面3种：

 - ea：后面接的是一个ASCII string (not NULL-terminated).
 - eb：后面接的是一个或多个Byte的值
 - ew：后面接的是一个或多个Word values

###windbg中计算简单的算术
debug中经常要做的事情是计算减法，进制间的转换。可以使用？命令。例如：
```
0:011> ? 001b6d70 -001b5942  
Evaluate expression: 5166 = 0000142e
```
也可以用？可以直接进行十进制和十六进制的转换，例如
```
0:011> ?142e
Evaluate expression: 5166 = 0000142e
```
如果需要进行更多的进制转换，使用.format命令，例如
```
0:000> .formats 1c407e62 Evaluate expression:   
Hex:     1c407e62  
Decimal: 473988706   
Octal:   03420077142   
Binary:  00011100 01000000 01111110 01100010   
Chars:   .@~b   
Time:    Mon Jan 07 15:31:46 1985   
Float:   low 6.36908e-022 high 0   
Double:  2.34182e-315
```
###Unicode的文件头
文中提及了，如果把内存dump到文件后，打开是乱码的原因是由于缺少文件头导致。作者使用2进制编辑工具给文件建立了文件头：0xfffe。这里把其他unicode编码方式的的文件头都列出来：

<table class=MsoTableGrid border=1 cellspacing=0 cellpadding=0
 style='border-collapse:collapse;border:none;mso-border-alt:solid windowtext .5pt;
 mso-yfti-tbllook:1184;mso-padding-alt:0cm 5.4pt 0cm 5.4pt'>
 <tr style='mso-yfti-irow:0;mso-yfti-firstrow:yes'>
  <td width=194 valign=top style='width:145.6pt;border:solid windowtext 1.0pt;
  mso-border-alt:solid windowtext .5pt;padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal align=center style='text-align:center'><b
  style='mso-bidi-font-weight:normal'><span lang=EN-US>Unicode</span></b><b
  style='mso-bidi-font-weight:normal'><span style='font-family:宋体;mso-ascii-font-family:
  Calibri;mso-ascii-theme-font:minor-latin;mso-fareast-font-family:宋体;
  mso-fareast-theme-font:minor-fareast;mso-hansi-font-family:Calibri;
  mso-hansi-theme-font:minor-latin'>类型</span><span lang=EN-US><o:p></o:p></span></b></p>
  </td>
  <td width=183 valign=top style='width:137.0pt;border:solid windowtext 1.0pt;
  border-left:none;mso-border-left-alt:solid windowtext .5pt;mso-border-alt:
  solid windowtext .5pt;padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal align=center style='text-align:center'><b
  style='mso-bidi-font-weight:normal'><span style='font-family:宋体;mso-ascii-font-family:
  Calibri;mso-ascii-theme-font:minor-latin;mso-fareast-font-family:宋体;
  mso-fareast-theme-font:minor-fareast;mso-hansi-font-family:Calibri;
  mso-hansi-theme-font:minor-latin'>文件头的标识</span><span lang=EN-US><o:p></o:p></span></b></p>
  </td>
  <td width=176 valign=top style='width:132.2pt;border:solid windowtext 1.0pt;
  border-left:none;mso-border-left-alt:solid windowtext .5pt;mso-border-alt:
  solid windowtext .5pt;padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal align=center style='text-align:center'><span class=GramE><b
  style='mso-bidi-font-weight:normal'><span style='font-family:宋体;mso-ascii-font-family:
  Calibri;mso-ascii-theme-font:minor-latin;mso-fareast-font-family:宋体;
  mso-fareast-theme-font:minor-fareast;mso-hansi-font-family:Calibri;
  mso-hansi-theme-font:minor-latin'>大端小端</span></b></span><b style='mso-bidi-font-weight:
  normal'><span lang=EN-US><o:p></o:p></span></b></p>
  </td>
 </tr>
 <tr style='mso-yfti-irow:1'>
  <td width=194 valign=top style='width:145.6pt;border:solid windowtext 1.0pt;
  border-top:none;mso-border-top-alt:solid windowtext .5pt;mso-border-alt:solid windowtext .5pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal align=center style='text-align:center'><span lang=EN-US>Utf8</span></p>
  </td>
  <td width=183 valign=top style='width:137.0pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  mso-border-top-alt:solid windowtext .5pt;mso-border-left-alt:solid windowtext .5pt;
  mso-border-alt:solid windowtext .5pt;padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal align=center style='text-align:center'><span lang=EN-US>EF
  BB BF</span></p>
  </td>
  <td width=176 valign=top style='width:132.2pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  mso-border-top-alt:solid windowtext .5pt;mso-border-left-alt:solid windowtext .5pt;
  mso-border-alt:solid windowtext .5pt;padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal align=center style='text-align:center'><span class=GramE><span
  style='font-family:宋体;mso-ascii-font-family:Calibri;mso-ascii-theme-font:
  minor-latin;mso-fareast-font-family:宋体;mso-fareast-theme-font:minor-fareast;
  mso-hansi-font-family:Calibri;mso-hansi-theme-font:minor-latin'>小端</span></span></p>
  </td>
 </tr>
 <tr style='mso-yfti-irow:2'>
  <td width=194 valign=top style='width:145.6pt;border:solid windowtext 1.0pt;
  border-top:none;mso-border-top-alt:solid windowtext .5pt;mso-border-alt:solid windowtext .5pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal align=center style='text-align:center'><span lang=EN-US>Uft16</span></p>
  </td>
  <td width=183 valign=top style='width:137.0pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  mso-border-top-alt:solid windowtext .5pt;mso-border-left-alt:solid windowtext .5pt;
  mso-border-alt:solid windowtext .5pt;padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal align=center style='text-align:center'><span lang=EN-US>FF
  FE</span></p>
  </td>
  <td width=176 valign=top style='width:132.2pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  mso-border-top-alt:solid windowtext .5pt;mso-border-left-alt:solid windowtext .5pt;
  mso-border-alt:solid windowtext .5pt;padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal align=center style='text-align:center'><span class=GramE><span
  style='font-family:宋体;mso-ascii-font-family:Calibri;mso-ascii-theme-font:
  minor-latin;mso-fareast-font-family:宋体;mso-fareast-theme-font:minor-fareast;
  mso-hansi-font-family:Calibri;mso-hansi-theme-font:minor-latin'>小端</span></span></p>
  </td>
 </tr>
 <tr style='mso-yfti-irow:3'>
  <td width=194 valign=top style='width:145.6pt;border:solid windowtext 1.0pt;
  border-top:none;mso-border-top-alt:solid windowtext .5pt;mso-border-alt:solid windowtext .5pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal align=center style='text-align:center'><span lang=EN-US>Uft16</span></p>
  </td>
  <td width=183 valign=top style='width:137.0pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  mso-border-top-alt:solid windowtext .5pt;mso-border-left-alt:solid windowtext .5pt;
  mso-border-alt:solid windowtext .5pt;padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal align=center style='text-align:center'><span lang=EN-US>FE
  FF</span></p>
  </td>
  <td width=176 valign=top style='width:132.2pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  mso-border-top-alt:solid windowtext .5pt;mso-border-left-alt:solid windowtext .5pt;
  mso-border-alt:solid windowtext .5pt;padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal align=center style='text-align:center'><span
  style='font-family:宋体;mso-ascii-font-family:Calibri;mso-ascii-theme-font:
  minor-latin;mso-fareast-font-family:宋体;mso-fareast-theme-font:minor-fareast;
  mso-hansi-font-family:Calibri;mso-hansi-theme-font:minor-latin'>大端</span></p>
  </td>
 </tr>
 <tr style='mso-yfti-irow:4'>
  <td width=194 valign=top style='width:145.6pt;border:solid windowtext 1.0pt;
  border-top:none;mso-border-top-alt:solid windowtext .5pt;mso-border-alt:solid windowtext .5pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal align=center style='text-align:center'><span lang=EN-US>Utf32</span></p>
  </td>
  <td width=183 valign=top style='width:137.0pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  mso-border-top-alt:solid windowtext .5pt;mso-border-left-alt:solid windowtext .5pt;
  mso-border-alt:solid windowtext .5pt;padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal align=center style='text-align:center'><span lang=EN-US>00
  00 FF FE</span></p>
  </td>
  <td width=176 valign=top style='width:132.2pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  mso-border-top-alt:solid windowtext .5pt;mso-border-left-alt:solid windowtext .5pt;
  mso-border-alt:solid windowtext .5pt;padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal align=center style='text-align:center'><span
  style='font-family:宋体;mso-ascii-font-family:Calibri;mso-ascii-theme-font:
  minor-latin;mso-fareast-font-family:宋体;mso-fareast-theme-font:minor-fareast;
  mso-hansi-font-family:Calibri;mso-hansi-theme-font:minor-latin'>大端</span></p>
  </td>
 </tr>
 <tr style='mso-yfti-irow:5;mso-yfti-lastrow:yes'>
  <td width=194 valign=top style='width:145.6pt;border:solid windowtext 1.0pt;
  border-top:none;mso-border-top-alt:solid windowtext .5pt;mso-border-alt:solid windowtext .5pt;
  padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal align=center style='text-align:center'><span lang=EN-US>Utf32</span></p>
  </td>
  <td width=183 valign=top style='width:137.0pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  mso-border-top-alt:solid windowtext .5pt;mso-border-left-alt:solid windowtext .5pt;
  mso-border-alt:solid windowtext .5pt;padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal align=center style='text-align:center'><span lang=EN-US>00
  00 FE FF</span></p>
  </td>
  <td width=176 valign=top style='width:132.2pt;border-top:none;border-left:
  none;border-bottom:solid windowtext 1.0pt;border-right:solid windowtext 1.0pt;
  mso-border-top-alt:solid windowtext .5pt;mso-border-left-alt:solid windowtext .5pt;
  mso-border-alt:solid windowtext .5pt;padding:0cm 5.4pt 0cm 5.4pt'>
  <p class=MsoNormal align=center style='text-align:center'><span class=GramE><span
  style='font-family:宋体;mso-ascii-font-family:Calibri;mso-ascii-theme-font:
  minor-latin;mso-fareast-font-family:宋体;mso-fareast-theme-font:minor-fareast;
  mso-hansi-font-family:Calibri;mso-hansi-theme-font:minor-latin'>小端</span></span></p>
  </td>
 </tr>
</table>


----------

