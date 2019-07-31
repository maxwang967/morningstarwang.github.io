---
layout: post
title:  "Flutter生产项目开发笔记01—引言"
date:   2019-07-31 22:36:28 +0800
categories: solution
---
# Flutter简介
> Flutter作为新一代由Google推出的的跨平台移动应用开发包，在开源社区成员的共同努力下已经迭代至1.5正式版本。

基于Dart语言提供的成熟的UI设计风格以及Skia 2D游戏级渲染引擎的加持。Flutter App可以在达到原生App运行性能的同时，支持无差别化的跨平台开发体验。

# 项目简介
在本次产品研发过程中，团队主要使用了Flutter完成了Android和iOS客户端的开发。由于Flutter尚属新兴阶段，各方面文档和资料十分匮乏。团队在实际开发过程中，遇到了较多阻碍，并进行了积极尝试并造了很多轮子，提出了多种有效的解决方案。

目前项目已经完成了三次重构和迭代工作，项目主要核心技术点包括：Android端全面AndroidX架构的迁移、Flutter Plugin的bugfix与重写、国内可用的Flutter移动端推送解决方案、Flutter移动端支付解决方案和Flutter开发中各类小trick的设计与实现等。

# 项目进展
项目2.0版本正在稳定运营，目前开放1.0版本的源代码工程。此工程架构为初期原型架构，不具有较多的参考意义。但是，在功能集成和项目配置方法具有重要的参考意义。

项目地址：[https://github.com/morningstarwang/flutter_campus_social_app](https://github.com/morningstarwang/flutter_campus_social_app "https://github.com/morningstarwang/flutter_campus_social_app")

# 撰写此笔记的原因
1. 推荐Flutter作为移动端开发T2梯队的开发利器。不论是UI设计与实现、状态管理还是开发效率均比直接使用原生的开发体验高很多。在具有比H5开发更直观和便于管理的前提下，还拥有和原生App相同或更高的运行性能。真正实现一种开发语言（Dart），一次开发，多平台运行。
目前已知可以运行的平台包括：Windows, Linux, MacOS, Android, iOS和Web浏览器。
2. 对已经入坑Flutter的小伙伴们提供一些可用的轮子和开发经验。希望我们能一起在这条探索道路上坚定地走下去。
3. 事情太多，学习和工作太忙......是时候擦一擦自己博客的蜘蛛网了...

# 送给读者的礼物
超级马里奥(这个不跨平台，PC Only) [Play](https://www.yejinmo.com/games/Mario/Mario.html "Play")