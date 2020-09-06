---
title: windows terminal(preview)简单美化设置
tags:
- windows
- 配置
categories:
- 计算机
---





# windows terminal(preview)简单美化配置

毫无疑问，windows 本身自带的terminal非常难看，甚至已经被当做事是Windows的一个特点了。但是微软在之前就已经放出了新版的terminal，并且可以让用户自定义设置外观。

这一篇基本是参考网上的一些资料而做的简单整合，只是我配置terminal的一个过程，只包括字体、透明度、兼容git、首选git这些方面，可能不满足许多人的需求。但这也作为我自己的一个记录。

<!--more-->

## windows terminal(preview)下载

只需要在电脑自带的微软商店里，搜索Windows terminal(preview)，即可免费获取并下载安装。

## 加入右键菜单

这个是网上博客里分享的使用bat批处理文件运行的，具体是创建一个.bat文件，然后将以下语句输入进去，之后管理员运行：

```powershell
@echo off

reg.exe add "HKEY_CLASSES_ROOT\Directory\Background\shell\wt" /f /ve /d "Windows Terminal here"
reg.exe add "HKEY_CLASSES_ROOT\Directory\Background\shell\wt" /f /v "Icon" /t REG_EXPAND_SZ /d "X:\assets\terminal.ico"
reg.exe add "HKEY_CLASSES_ROOT\Directory\Background\shell\wt\command" /f /ve /t REG_EXPAND_SZ /d "\"%%LOCALAPPDATA%%\Microsoft\WindowsApps\wt.exe\""

pause
```

其中第二条语句中最后的文件是terminal的图标，这个图标可以从https://raw.githubusercontent.com/microsoft/terminal/master/res/terminal.ico 此链接获取。保存的图标可以在任意位置，只需要在第二条语句的最后修改为图片的绝对路径就行。

## 外观修改

打开terminal，在标题栏的下拉菜单中，点击设置（或者通过ctrl+`），即可开始编辑一个profiles.json文件，terminal就是通过该json文件来配置的。同时，按住alt并点击设置，就可以跳出一个默认的defaults.json文件。这个json文件是系统自动生成的，即使你修改默认的defaults.json文件，也不会有效。它可以作为一个参考，用于帮助我们修改profiles.json。

首先说明，可以在官方文档中找到各种键值对的意义，而我参考了下面这篇博客：https://akarin.dev/2019/05/26/windows-terminal/ 。

在defaults.json文件中，可以看到，从第一个键值对一直到profiles键之前，都是一些全局变量，用于控制全局的一些设置。例如，"defaultProfile"的值就是之后的profiles键中某个shell或者bash的id，当设定为该id，terminal启动时就会默认启动该shell或bash；"initialCols"与"initialRows"分别代表窗口的宽度和高度。

profiles键是一个[]列表，里面是若干个字典，每一个字典就是一个shell或者bash的设定键值对。"guid"键的值是标识该shell或bash的id，id可以任取，只要不重复就行；"name"键是该shell的名称，用于标题栏显示；"commandline"标识的是该shell的程序位置；"colorScheme"键标识颜色方案；"fontSize"标识字体大小；"icon"标识该shell或bash的图标；"useAcrylic"值为true时，表示使用半透明效果；"acrylicOpacity"当半透明开启时，指定半透明程度，范围在0-1之间，一般0.5即可；"startingDiectory"代表启动的位置，当为"."时表示在当前目录下启动。

之后的scheme键也是一个列表，它可以存储多个字典，每一个字典就是一种颜色方案的设置，由里面的"name"键标识。即用户可以在这里存储多个颜色方案，然后在profiles里的一个shell或者bash字典里，给"colorscheme"键赋予一个颜色方案的名称，即可在使用shell或者bash的时候，使用该种颜色方案。颜色方案可以在网上搜索喜爱的。

最后的keybindings键就是快捷键的设置。这里可以更改自己适合的快捷键。

我们在修改profiles.json的时候，只需要修改自己想要自定义的部分，其它的部分依旧会按默认设置的进行。

## 加入git bash

这需要你的电脑上首先安装了Git后才能设置。

在"profile"键中新增一个字典，为了简便，可以直接使用以下设置：

```json
{
            "acrylicOpacity": 0.5, // 透明度
            "closeOnExit": true, // 关闭的时候退出命令终端
            "colorScheme": "Atom", 
            "commandline": "X:\\Git\\bin\\bash.exe", // gitbash的命令行所在位置
            "cursorColor": "#FFFFFF", // 光标颜色
            "cursorShape": "bar", 
            "fontFace": "Consolas", 
            "fontSize": 12, // 终端字体大小
            "guid": "{1c4de342-38b7-51cf-b940-2309a097f589}", 
            "historySize": 9001, 
            "icon": "<图标位置>", // git的图标，打开终端时候会看到
            "name": "Bash", // tab栏的标题显示
            "padding": "8, 8, 8, 8", // 边距
            "snapOnInput": true,
            "startingDirectory": ".", // gitbash 启动的位置
            "useAcrylic": true // 是否开启透明度
 },
```

之后可以根据需要自行修改。如果你需要将terminal设置为开启默认git bash，就可以将之前的"defaultProfile"键的值修改为你的git bash的"guid"值。

保存退出即可。

## 参考链接

https://printempw.github.io/windows-terminal-setup-guide/

https://blog.csdn.net/Jioho_chen/article/details/100625599

https://akarin.dev/2019/05/26/windows-terminal/