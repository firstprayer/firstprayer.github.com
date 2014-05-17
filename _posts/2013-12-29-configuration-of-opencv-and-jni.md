---
layout: post
title: "configuration of opencv and jni"
description: ""
category: ""
tags: []
---


折腾了一个晚上，终于能够成功导出dll并且被java所调用了。流程大致如下：
背景介绍：VS2010，JDK7，Eclipse，OpenCV 2.6.3

1. 在Java里面写一个类，需要调用C++的方法加上native修饰。用javah package.class.name生成对应的头文件
2. VS2010新建WIN32项目，选择为dll类型。把1中得到的头文件加进去，然后加上对应的cpp。导出成一个dll
3. 把2中生成的dll放在JAVA_HOME的BIN目录下。由于我要使用OpenCV，所以我把对应的OPENCV DLL也一并放进去了。
4. Java里面要先System.loadLibrary('DLL NAME without .dll suffix')。一切顺利的话，就可以跑了。


后记：
我在配置的时候，碰到的最浪费时间的问题是32位和64位的问题。开始的时候尝试64位，但是似乎VS2010没办法导出64位的DLL，也可能是我配置的问题。总之，要保证所有程序都是32位：JDK, Eclipse, DLL。我由于之前拷贝到JAVA_HOME/bin的64位的OPENCV DLL忘记替换成32位的了，每次启动都报错：不是有效的WIN32应用程序。

