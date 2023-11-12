---
title: Sprite Unpacker——裁剪图集的开源工具
author: Mingxian Yang
date: 2023-07-19 18:10:00 +0800
description: 我正在开发自己的独立游戏，在做资源迁移的时候遇到了Sprite图集恢复成单独Sprite图片的问题，所以我就顺手做了一个根据plist文件裁剪Sprite图集的小工具。我抽空写了这篇文章并且将工具开源分享出来，希望能对像我一样的独立开发者有所帮助。Have fun~
categories: [游戏设计]
tags: [Unity3D]
render_with_liquid: false
---


<!-- ![识别结果截图](007.png) -->

<img src="007.png" alt="识别结果截图" style="width:802px;height:633px;">

## 前言  

开发自己独立游戏时，用到了很多来源于 Cocos 的 2D 资源，但是 Unity3D 的 Sprite 分割有些简陋，不支持根据 .plist 文件分割打包好的 Sprite 图集，因此我搜索到了一些工具，并使用开源的算法，再用 Unity3D 进行了重新包装，然后再次把这个项目开源分享出来。项目支持根据 .plist 或者是 .png 图片进行 Sprite 图集的分割，适用于 **Cocos** 或者是**TexturePacker** 打包的图集拆分。

## 其他事项

- 为了使用Windows的拖拽（支持将文件直接拖入工具的窗口中），Unity3D引擎内运行时不支持拖拽，因此需要测试的话，需要打包出exe程序才可以。  
- 为了实现任意拖入**plist数据文件** 或者**png文件**，这两个文件需要**同名**并且处于**同一文件夹目录**内，而分割的结果会在此新建一个同名文件夹。
- **由于是开源项目，严禁使用本工具进行商用目的**。


## 运行截图  

![导入后的预览界面](008.png)
![输出结果](009.png)

## 有关链接

[**项目地址：** https://github.com/yangmingxian/SpriteUnpacker](https://github.com/yangmingxian/SpriteUnpacker)


 [**作者博客：YMX's Site**](https://yangmingxian.com)  
 [**作者B站：CyberStreamer**](https://space.bilibili.com/22212765)

希望项目能给其他开发者节省开发时间
Have fun~

--- 
**Reference**：   
[https://blog.csdn.net/NRatel/article/details/85009462](https://blog.csdn.net/NRatel/article/details/85009462)
[https://blog.csdn.net/NRatel/article/details/85017491](https://blog.csdn.net/NRatel/article/details/85017491)
[https://github.com/NRatel/TextureUnpacker](https://github.com/NRatel/TextureUnpacker)






