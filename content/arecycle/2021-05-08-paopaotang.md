---
title: "[做点有趣的]Java开发泡泡堂游戏（MVC架构）"
date: 2021-05-08T11:09:51+08:00

tags: [
    "Java"
]
tags: [
    "项目"
]
draft: true

---
### 介绍
本项目是一个很久以前的实训周项目，由我和我的组员 ljr 共同实现。整个项目思路清晰，整体难度不大，但是很多细节需要花功夫。本项目仍存在一些不足的地方，后续可能会进行优化，现在我将项目源代码和一些实现思路开源公布。

源代码：[https://gitee.com/lzxqaq/CrazyArcade](https://gitee.com/lzxqaq/CrazyArcade)

文章介绍：[https://lzxqaq.com/post/java/paopaotang/](https://lzxqaq.com/post/java/paopaotang/)

程序运行： 在终端下进入执行程序所在目录，执行 `java -jar CrazyArcade.jar` 或者双击 `CrazyArcade.jar`，或者在开发环境中打开源代码，运行 `GameStart.java`的 `main` 方法。

运行环境：Linux、Windows均可。开发环境：IDEA。

演示视频：

运行截图：
<div  align="center">    
 <img src="https://cdn.jsdelivr.net/gh/lzxqaq/CrazyArcade@master/images/CrazyArcade.png" width = "500" height = "200" alt="图片1" align=center />
 <br/>
  <img src="https://cdn.jsdelivr.net/gh/lzxqaq/CrazyArcade/images/2.png" width = "500" height = "200" alt="图片2" align=center />
 </div>

### 功能
本项目实现的功能如下：

* 绘制游戏启动界面、结束界面、地图、主角、道具
* 实现泡泡爆炸
* 实现双主角PK（积分制）
* 实现道具掉落和相应属性加成
* 实现游戏音效和背景音乐

其中我们对游戏玩法做了调整，大致如下:

我们把游戏设计为双人pk积分赛模式，在这个模式里面，玩家只要率先达到一定分数既可以赢得比赛。玩家可以通过炸箱子可以得到少量的分数，也可以通过炸掉对手然后戳破包围对手的水泡得到大量分数。而玩家如果被泡泡爆炸击中，会被泡泡包裹一段时间，在这段时间内不可以移动和放泡泡，需要等时间过去或者被对手戳破水泡才能获得自由。但如果玩家被自己放的泡泡炸中，会扣一定的分数。


### 思路和架构
整个项目采用 MVC 架构，将项目整体分为数据模型层（M）、视图层（V）、控制层（C）。M层负责元素的创建、存储、管理，V层负责所有元素的显示（24帧/秒），C层负责交互（监听用户的操作），同时负责控制游戏的进程。

选择MVC架构最主要的原因是让这个游戏项目具有良好的可扩展性和更新功能，当然了，一个好的游戏也需要良好的交互功能，漂亮的UI设计。

架构设计图：

<div  align="center">    
 <img src="https://cdn.jsdelivr.net/gh/lzxqaq/CrazyArcade@master/images/design.png" width = "500" height = "200" alt="图片名称" align=center /></div>


### 包结构
未完待续……

### 核心实现
未完待续……

### 后续

该项目仍有许多不足之处，如果你对该项目有任何意见或建议，欢迎[联系我](https://lzxqaq.com/about/)。如有任何问题，亦可与我一同探讨。

