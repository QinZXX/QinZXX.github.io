---
layout: post
title: markdown背景以颜色设置-速查
categories: Tools
description: markdown字体以及背景设置。
keywords: markdown语法, 字体设置
---

# Markdown字体和颜色设置-速查

## 概述

markdown的基本语法支持部分的html标签。对于 Markdown 涵盖范围之外的标签，都可以直接在文件里面用 HTML 本身。如需使用 HTML，不需要额外标注这是 HTML 或是 Markdown，只需 HTML 标签添加到 Markdown 文本中即可。



以此便可以通过html标签来对字体的颜色，背景颜色，字体大小等方面来做修改。

## HTML 用法最佳实践

出于安全原因，并非所有 Markdown 应用程序都支持在 Markdown 文档中添加 HTML。如有疑问，请查看相应 Markdown 应用程序的手册。某些应用程序只支持 HTML 标签的子集。

对于 HTML 的块级元素 ``、``、`` 和 ``，请在其前后使用空行（blank lines）与其它内容进行分隔。尽量不要使用制表符（tabs）或空格（spaces）对 HTML 标签做缩进，否则将影响格式。

在 HTML 块级标签内不能使用 Markdown 语法。例如 `italic and **bold**` 将不起作用。

## 1.字体颜色设置

在markdown中采用如下方式能够控制文字的颜色：

~~~html
浅红色文字：<font color="#dd0000">浅红色文字：</font>
深红色文字：<font color="#660000">深红色文字</font>
浅绿色文字：<font color="#00dd00">浅绿色文字</font> 
深绿色文字：<font color="#006600">深绿色文字</font> 
浅蓝色文字：<font color="#0000dd">浅蓝色文字</font> 
深蓝色文字：<font color="#000066">深蓝色文字</font> 
浅黄色文字：<font color="#dddd00">浅黄色文字</font> 
深黄色文字：<font color="#666600">深黄色文字</font> 
浅青色文字：<font color="#00dddd">浅青色文字</font> 
深青色文字：<font color="#006666">深青色文字</font> 
浅紫色文字：<font color="#dd00dd">浅紫色文字</font> 
深紫色文字：<font color="#660066">深紫色文字</font> 

~~~

效果：

浅红色文字：<font color="#dd0000">浅红色文字：</font>
深红色文字：<font color="#660000">深红色文字</font>
浅绿色文字：<font color="#00dd00">浅绿色文字</font> 
深绿色文字：<font color="#006600">深绿色文字</font> 
浅蓝色文字：<font color="#0000dd">浅蓝色文字</font> 
深蓝色文字：<font color="#000066">深蓝色文字</font> 
浅黄色文字：<font color="#dddd00">浅黄色文字</font> 
深黄色文字：<font color="#666600">深黄色文字</font> 
浅青色文字：<font color="#00dddd">浅青色文字</font> 
深青色文字：<font color="#006666">深青色文字</font> 
浅紫色文字：<font color="#dd00dd">浅紫色文字</font> 
深紫色文字：<font color="#660066">深紫色文字</font> 



## 2.大小

~~~html
size为1：<font size="1">size为1</font><br /> 
size为2：<font size="2">size为2</font><br /> 
size为3：<font size="3">size为3</font><br /> 
size为4：<font size="4">size为4</font><br /> 
size为10：<font size="10">size为10</font><br /> 
~~~

效果：

size为1：<font size="1">size为1</font> 
size为2：<font size="2">size为2</font> 
size为3：<font size="3">size为3</font> 
size为4：<font size="4">size为4</font> 
size为10：<font size="10">size为10</font> 

## 3.字体

~~~html
<font face="黑体">我是黑体字</font>
<font face="宋体">我是宋体字</font>
<font face="微软雅黑">我是微软雅黑字</font>
<font face="fantasy">我是fantasy字</font>
<font face="Helvetica">我是Helvetica字</font>
~~~

效果：

<font face="黑体">我是黑体字</font>
<font face="宋体">我是宋体字</font>
<font face="微软雅黑">我是微软雅黑字</font>
<font face="fantasy">我是fantasy字</font>
<font face="Helvetica">我是Helvetica字</font>



## 4.背景色

~~~html
<table>
    <tr>
        <td bgcolor=#FF00FF>背景色的设置是按照十六进制颜色值：#7FFFD4</td>		</tr>
</table>
<table>
    <tr>
        <td bgcolor=#FF83FA>背景色的设置是按照十六进制颜色值：#FF83FA
        </td>
    </tr>
</table>
<table>
    <tr>
        <td bgcolor=#D1EEEE>背景色的设置是按照十六进制颜色值：#D1EEEE
        </td>
    </tr>
</table>
<table>
    <tr>
        <td bgcolor=#C0FF3E>背景色的设置是按照十六进制颜色值：#C0FF3E
        </td>
    </tr>
</table>
<table>
    <tr>
        <td bgcolor=#54FF9F>背景色的设置是按照十六进制颜色值：#54FF9F
        </td>
    </tr>
</table>
<table>
    <tr>
        <td bgcolor=DarkSeaGreen>这里的背景色是：DarkSeaGreen，此处输入任意想输入的内容
        </td>
    </tr>
</table>
~~~

效果：

<table>
    <tr>
        <td bgcolor=#FF00FF>背景色的设置是按照十六进制颜色值：#7FFFD4</td>		</tr>
</table>
<table>
    <tr>
        <td bgcolor=#FF83FA>背景色的设置是按照十六进制颜色值：#FF83FA
        </td>
    </tr>
</table>
<table>
    <tr>
        <td bgcolor=#D1EEEE>背景色的设置是按照十六进制颜色值：#D1EEEE
        </td>
    </tr>
</table>
<table>
    <tr>
        <td bgcolor=#C0FF3E>背景色的设置是按照十六进制颜色值：#C0FF3E
        </td>
    </tr>
</table>
<table>
    <tr>
        <td bgcolor=#54FF9F>背景色的设置是按照十六进制颜色值：#54FF9F
        </td>
    </tr>
</table>
<table>
    <tr>
        <td bgcolor=DarkSeaGreen>这里的背景色是：DarkSeaGreen，此处输入任意想输入的内容
        </td>
    </tr>
</table>



## 参考资料

- [Markdown 内嵌 HTML 标签 | Markdown 官方教程](https://markdown.com.cn/basic-syntax/htmls.html)）。

