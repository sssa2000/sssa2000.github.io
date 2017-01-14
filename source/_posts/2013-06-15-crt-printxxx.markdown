---
layout: post
title: "从变长参数到格式化字符串2 CRT的printXXX"
date: 2013-06-15 01:30
comments: true
categories: 
---

<!-- more -->


##CRT中的xxprintf家族
VS2010 的CRT中提供了一系列和格式化字符串有关的函数，例如printf等等，这里列出来一些（摘自http://msdn.microsoft.com/en-us/library/0zf95wk0(v=vs.100).aspx）：

<table cellpadding="0" border="1" style=
"BORDER-BOTTOM: medium none; BORDER-LEFT: medium none; TEXT-TRANSFORM: none; TEXT-INDENT: 0px; BORDER-COLLAPSE: collapse; FONT-FAMILY: Simsun; LETTER-SPACING: normal; BORDER-TOP: medium none; BORDER-RIGHT: medium none; WORD-SPACING: 0px; -webkit-text-size-adjust: auto; -webkit-text-stroke-width: 0px"
cellspacing="0">
   <tbody>
      <tr>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; BORDER-LEFT: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; BACKGROUND-COLOR: rgb(237,237,237); PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP: rgb(187,187,187) 1pt solid; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt">
         <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <strong>Function</strong>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; BACKGROUND-COLOR: rgb(237,237,237); PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-LEFT-STYLE: none; BORDER-TOP: rgb(187,187,187) 1pt solid; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt">
         <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <strong>Counterpart function</strong>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; BACKGROUND-COLOR: rgb(237,237,237); PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-LEFT-STYLE: none; BORDER-TOP: rgb(187,187,187) 1pt solid; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt">
         <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <strong>Output destination</strong>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; BACKGROUND-COLOR: rgb(237,237,237); PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-LEFT-STYLE: none; BORDER-TOP: rgb(187,187,187) 1pt solid; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt">
         <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <strong>Parameter Validation</strong>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; BACKGROUND-COLOR: rgb(237,237,237); PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-LEFT-STYLE: none; BORDER-TOP: rgb(187,187,187) 1pt solid; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt">
         <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <strong>Positional Parameter Support</strong>
            </p>
         </td>
      </tr>
      <tr>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; BORDER-LEFT: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <strong>_vcprintf</strong>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <a href="http://msdn.microsoft.com/en-us/library/7ax4dbdt(v=vs.100).aspx">_cprintf</a>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               console
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               Check for null.
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               no
            </p>
         </td>
      </tr>
      <tr>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; BORDER-LEFT: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <strong>_vcwprintf</strong>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <a href="http://msdn.microsoft.com/en-us/library/7ax4dbdt(v=vs.100).aspx">_cwprintf</a>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               console
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               Check for null.
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               no
            </p>
         </td>
      </tr>
      <tr>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; BORDER-LEFT: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <strong>vfprintf</strong>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <a href="http://msdn.microsoft.com/en-us/library/xkh07fe2(v=vs.100).aspx">fprintf</a>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <em>Stream</em>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               Check for null.
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               no
            </p>
         </td>
      </tr>
      <tr>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; BORDER-LEFT: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <strong>vfprintf_p</strong>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <a href="http://msdn.microsoft.com/en-us/library/35tfd78h(v=vs.100).aspx">fprintf_p</a>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <em>Stream</em>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               Check for null and valid format.
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               yes
            </p>
         </td>
      </tr>
      <tr>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; BORDER-LEFT: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <strong>vfprintf_s</strong>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <a href="http://msdn.microsoft.com/en-us/library/ksf1fzyy(v=vs.100).aspx">fprintf_s</a>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <em>Stream</em>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               Check for null and valid format.
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               no
            </p>
         </td>
      </tr>
      <tr>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; BORDER-LEFT: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <strong>vfwprintf</strong>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <a href="http://msdn.microsoft.com/en-us/library/xkh07fe2(v=vs.100).aspx">fwprintf</a>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <em>Stream</em>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               Check for null.
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               no
            </p>
         </td>
      </tr>
      <tr>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; BORDER-LEFT: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <strong>vfwprintf_p</strong>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <a href="http://msdn.microsoft.com/en-us/library/35tfd78h(v=vs.100).aspx">fwprintf_p</a>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <em>Stream</em>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               Check for null and valid format.
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               yes
            </p>
         </td>
      </tr>
      <tr>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; BORDER-LEFT: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <strong>vfwprintf_s</strong>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <a href="http://msdn.microsoft.com/en-us/library/ksf1fzyy(v=vs.100).aspx">fwprintf_s</a>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <em>Stream</em>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               Check for null and valid format.
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               no
            </p>
         </td>
      </tr>
      <tr>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; BORDER-LEFT: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <strong>vprintf</strong>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <a href="http://msdn.microsoft.com/en-us/library/wc7014hz(v=vs.100).aspx">printf</a>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <strong>Stdout</strong>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               Check for null.
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               no
            </p>
         </td>
      </tr>
      <tr>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; BORDER-LEFT: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <strong>vprintf_p</strong>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <a href="http://msdn.microsoft.com/en-us/library/s3a5ky4x(v=vs.100).aspx">printf_p</a>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <strong>Stdout</strong>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               Check for null and valid format.
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               yes
            </p>
         </td>
      </tr>
      <tr>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; BORDER-LEFT: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <strong>vprintf_s</strong>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <a href="http://msdn.microsoft.com/en-us/library/239ffwa0(v=vs.100).aspx">printf_s</a>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <strong>Stdout</strong>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               Check for null and valid format.
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               no
            </p>
         </td>
      </tr>
      <tr>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; BORDER-LEFT: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <strong>vwprintf</strong>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <a href="http://msdn.microsoft.com/en-us/library/wc7014hz(v=vs.100).aspx">wprintf</a>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <strong>Stdout</strong>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               Check for null.
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               no
            </p>
         </td>
      </tr>
      <tr>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; BORDER-LEFT: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <strong>vwprintf_p</strong>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <a href="http://msdn.microsoft.com/en-us/library/s3a5ky4x(v=vs.100).aspx">wprintf_p</a>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <strong>Stdout</strong>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               Check for null and valid format.
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               yes
            </p>
         </td>
      </tr>
      <tr>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; BORDER-LEFT: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <strong>vwprintf_s</strong>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <a href="http://msdn.microsoft.com/en-us/library/239ffwa0(v=vs.100).aspx">wprintf_s</a>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <strong>Stdout</strong>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               Check for null and valid format.
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               no
            </p>
         </td>
      </tr>
      <tr>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; BORDER-LEFT: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <strong>vsprintf</strong>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <a href="http://msdn.microsoft.com/en-us/library/ybk95axf(v=vs.100).aspx">sprintf</a>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               memory pointed to by <em>buffer</em>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               Check for null.
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               no
            </p>
         </td>
      </tr>
      <tr>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; BORDER-LEFT: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <strong>vsprintf_p</strong>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <a href="http://msdn.microsoft.com/en-us/library/02y7cdtk(v=vs.100).aspx">sprintf_p</a>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               memory pointed to by <em>buffer</em>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               Check for null and valid format.
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               yes
            </p>
         </td>
      </tr>
      <tr>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; BORDER-LEFT: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <strong>vsprintf_s</strong>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <a href="http://msdn.microsoft.com/en-us/library/ce3zzk1k(v=vs.100).aspx">sprintf_s</a>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               memory pointed to by <em>buffer</em>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               Check for null and valid format.
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               no
            </p>
         </td>
      </tr>
      <tr>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; BORDER-LEFT: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <strong>vswprintf</strong>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <a href="http://msdn.microsoft.com/en-us/library/ybk95axf(v=vs.100).aspx">swprintf</a>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               memory pointed to by <em>buffer</em>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               Check for null.
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               no
            </p>
         </td>
      </tr>
      <tr>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; BORDER-LEFT: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <strong>vswprintf_p</strong>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <a href="http://msdn.microsoft.com/en-us/library/02y7cdtk(v=vs.100).aspx">swprintf_p</a>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               memory pointed to by <em>buffer</em>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               Check for null and valid format.
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               yes
            </p>
         </td>
      </tr>
      <tr>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; BORDER-LEFT: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <strong>vswprintf_s</strong>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <a href="http://msdn.microsoft.com/en-us/library/ce3zzk1k(v=vs.100).aspx">swprintf_s</a>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               memory pointed to by <em>buffer</em>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               Check for null and valid format.
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               no
            </p>
         </td>
      </tr>
      <tr>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; BORDER-LEFT: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <strong>_vscprintf</strong>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <a href="http://msdn.microsoft.com/en-us/library/w05tbk72(v=vs.100).aspx">_vscprintf</a>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               memory pointed to by <em>buffer</em>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               Check for null.
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               no
            </p>
         </td>
      </tr>
      <tr>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; BORDER-LEFT: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <strong>_vscwprintf</strong>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <a href="http://msdn.microsoft.com/en-us/library/w05tbk72(v=vs.100).aspx">_vscwprintf</a>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               memory pointed to by <em>buffer</em>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               Check for null.
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               no
            </p>
         </td>
      </tr>
      <tr>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; BORDER-LEFT: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <strong>_vsnprintf</strong>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <a href="http://msdn.microsoft.com/en-us/library/2ts7cx93(v=vs.100).aspx">_snprintf</a>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               memory pointed to by <em>buffer</em>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               Check for null.
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               no
            </p>
         </td>
      </tr>
      <tr>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; BORDER-LEFT: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <strong>_vsnwprintf</strong>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               <a href="http://msdn.microsoft.com/en-us/library/2ts7cx93(v=vs.100).aspx">_snwprintf</a>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               memory pointed to by <em>buffer</em>
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               Check for null.
            </p>
         </td>
         <td style=
         "BORDER-BOTTOM: rgb(187,187,187) 1pt solid; PADDING-BOTTOM: 7.5pt; PADDING-LEFT: 6pt; PADDING-RIGHT: 6pt; BORDER-TOP-STYLE: none; BORDER-LEFT-STYLE: none; BORDER-RIGHT: rgb(187,187,187) 1pt solid; PADDING-TOP: 7.5pt"
         valign="top">
            <p style="TEXT-ALIGN: left; LINE-HEIGHT: 13.5pt; MARGIN: 0cm 7.5pt 0pt; FONT-FAMILY: Calibri, sans-serif; FONT-SIZE: 10.5pt">
               no
            </p>
         </td>
      </tr>
   </tbody>
</table>


这些函数看似杂乱，也有规律可循。其中以_v开头的函数表示该函数接受一个va_list的参数，例如：
```cpp
int _vprintf(
   const char *format,
   va_list argptr 
);
```
和_vprintf有一个对应的printf函数，和vrpintf的区别是，printf接受的是…的变长参数，而不是va_list。
```cpp
int printf(
   const char *format [,
   argument]... 
);
```

带有_s后缀的表示需要传一个buff的长度进去。
带有_p后缀的表示支持在格式化字符串的时候使用指定位置的参数。例如
```cpp
_printf_p("%1$d %2$d %1$d\n", i, j);
```

带有w的函数，例如wprintf、vwprintf表示针对wide-character处理的；反之是针对多字节字符串处理的。

##sprintf_s源码一瞥
挑一个常用的，看看源码长什么样子。sprint_s的代码清清楚楚落落大方：
```cpp
int __cdecl sprintf_s (
        char *string,
        size_t sizeInBytes,
        const char *format,
        ...
        )
{
        va_list arglist;
        va_start(arglist, format);
        return _vsprintf_s_l(string, sizeInBytes, format, NULL, arglist);

}
```
_vsprintf_s的代码也比较简洁，只是调用了一个_vsnprintf_helper函数而已。可以看到这里将_output_s_l的函数地址传了进去。
```cpp
int __cdecl _vsprintf_s_l (
        char *string,
        size_t sizeInBytes,
        const char *format,
        _locale_t plocinfo,
        va_list ap
        )
{
    int retvalue = -1;

    /* validation section */
    _VALIDATE_RETURN(format != NULL, EINVAL, -1);
    _VALIDATE_RETURN(string != NULL && sizeInBytes > 0, EINVAL, -1);

    retvalue = _vsnprintf_helper(_output_s_l, string, sizeInBytes, format, plocinfo, ap);
    if (retvalue < 0)
    {
        string[0] = 0;
        _SECURECRT__FILL_STRING(string, sizeInBytes, 1);
    }
    if (retvalue == -2)
    {
        _VALIDATE_RETURN(("Buffer too small", 0), ERANGE, -1);
    }
    if (retvalue >= 0)
    {
        _SECURECRT__FILL_STRING(string, sizeInBytes, retvalue + 1);
    }

    return retvalue;
}
```
接下来看_vsnprintf_helper的实现，开始出现各种各样的宏，好吧这种程度这还能忍受。
```cpp
int __cdecl _vsnprintf_helper (
        OUTPUTFN outfn,
        char *string,
        size_t count,
        const char *format,
        _locale_t plocinfo,
        va_list ap
        )
#endif  /* _SWPRINTFS_ERROR_RETURN_FIX */
#endif  /* _COUNT_ */

{
        FILE str = { 0 };
        REG1 FILE *outfile = &str;
        REG2 int retval;

        _VALIDATE_RETURN( (format != NULL), EINVAL, -1);

#ifdef _COUNT_
        _VALIDATE_RETURN( (count == 0) || (string != NULL), EINVAL, -1 );
#else  /* _COUNT_ */
        _VALIDATE_RETURN( (string != NULL), EINVAL, -1 );
#endif  /* _COUNT_ */

#ifndef _COUNT_
        outfile->_cnt = MAXSTR;
#else  /* _COUNT_ */
        if(count>INT_MAX)
        {
            /* old-style functions allow any large value to mean unbounded */
            outfile->_cnt = INT_MAX;
        }
        else
        {
            outfile->_cnt = (int)count;
        }
#endif  /* _COUNT_ */

        outfile->_flag = _IOWRT|_IOSTRG;
        outfile->_ptr = outfile->_base = string;

#ifndef _SWPRINTFS_ERROR_RETURN_FIX
        retval = _output_l(outfile, format, plocinfo, ap );
#else  /* _SWPRINTFS_ERROR_RETURN_FIX */
        retval = outfn(outfile, format, plocinfo, ap );
#endif  /* _SWPRINTFS_ERROR_RETURN_FIX */

        if ( string==NULL)
            return(retval);

#ifndef _SWPRINTFS_ERROR_RETURN_FIX

        _putc_nolock('\0',outfile);

        return(retval);
#else  /* _SWPRINTFS_ERROR_RETURN_FIX */
        if((retval >= 0) && (_putc_nolock('\0',outfile) != EOF))
            return(retval);

        string[count - 1] = 0;

        if (outfile->_cnt < 0)
        {
            /* the buffer was too small; we return -2 to indicate truncation */
            return -2;
        }
        return -1;
#endif  /* _SWPRINTFS_ERROR_RETURN_FIX */

}
```
这里看起来那么多其实都不用关注，只需要知道这个函数里面调用了传进来的函数指针即可，实际上所有格式化字符串的逻辑都在那个函数指针指向的函数里面：```_output_s_l```。

终于要把镜头对准这次的主角了：
```cpp
_output_s_l(_iobuf * stream, const char * format, localeinfo_struct * plocinfo, char * argptr)
```
只是我实在不想把CRT中这个函数的代码贴在这里。函数有一千多行，其中有数不尽的ifdef、goto。想看的朋友可以自行去这个目录查看：```\Microsoft Visual Studio 10.0\VC\crt\src\output.c```。

该函数的基本意思其实很简单：挨个遍历format中每一个字符，如果是百分号开头，则判断下一个字符，如果是控制字符，那么就使用va_arg从变长参数中读取一个参数出来，添加到out的字符串末尾。

在前文表格中列举的所有的printXXX的函数最终都会走到这个函数中。

##小结
* 1、_output_s_l的代码很糟糕。

* 2、printfXXX系列中使用的百分号来格式化字符串的方式实际上也稀松平常，暴力法来遍历而已。

* 3、CRT中提供了大量的格式化字符串的函数，选择的时候尽量选择_s、_p的。

好吧，我知道这篇文章很水,我自己都看不下去了。
