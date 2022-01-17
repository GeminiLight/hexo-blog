---
title: 【Web】《CQU软件综合实践平台》项目总结
author: Gemini向光性
date: 2020-09-03 16:56:00
categories: 
  - WD - 网站开发
tags: 
  - front-end
  - Web
---

{% meting "002C8n7x1RdgXe" "tencent" "song" %}

在大二小学期软件实训课程中，自己做了一个“CQU软件综实践平台”的项目，于是在此总结一些自己第一次做自己的全栈开发项目的经验。（其实这篇总结应该很早就写的，至于为什么拖到了现在？“不愧是我——那只Monkey又在作怪”）

<!-- more -->

## 前言

### 为什么

为什么会选择做“CQU软件综合实践平台”这个项目呢？主要是因为自己在入门Vue后便很少去实践，关于后端的知识也少有动力去精进，恰巧课程名称《软件综合实践》且主要假以钉钉、QQ作为课程平台。于是在此特定的时机场景，便萌生了做一个重庆大学软件综合实践平台的想法。

### 做什么

现有的网络课程平台或是专门针对实验课程的平台繁多且不乏高质量网站，个人深知能力有限，并不渴求可以做出功能十分完善、需求十分满足的工业级项目。更多的出于自我学习的目的，项目偏向于一些必要需求的实现和一些特定场景的优化，因此，在前期需求分析阶段所作出的分析也主要是针对所学课程当中的常见需求和场景。

### 怎么做

该平台是基于B/S的实验课程管理系统，故离不开前端与后端的技术支撑。在具体实现过程中，主要用到了以下技术栈：

- 前端：HTML、CSS、JS、Vue、Element UI
- 后端：SpringBoot、Redis、Mybatis plus、AliYun oss
- 数据库：MySQL、CURD、乐观锁、触发器、范式设计

## 展示

![ ](/resource/images/web/web-cquse-overview.png)

以下为项目中部分界面的展示

### 统一登录入口

![ ](/resource/images/web/web-cquse-login.webp)

### 学生功能页面

#### 学生主页

![ ](/resource/images/web/web-cquse-stu-home.webp)

#### 学生签到界面

![ ](/resource/images/web/web-cquse-stu-sign.webp)

#### 学生上传报告

![ ](/resource/images/web/web-cquse-stu-report.webp)

#### 学生成绩查询

![ ](/resource/images/web/web-cquse-stu-grade.webp)

#### 学生个人中心

![ ](/resource/images/web/web-cquse-stu-user.webp)

### 教师功能页面

#### 教师主页

![ ](/resource/images/web/web-cquse-tch-home.webp)

#### 教师登记成绩

![ ](/resource/images/web/web-cquse-tch-grade.webp)

#### 教师查看选题

![ ](/resource/images/web/web-cquse-tch-project.webp)

### 后台功能页面

#### 学生信息管理

![ ](/resource/images/web/web-cquse-admin-stu.webp)

#### 修改学生信息

![ ](/resource/images/web/web-cquse-admin-modify.webp)

## 总结

### 前端方面

Vue、React、Angular等等这些流行的前端框架一定程度上改变了前端开发的模式，工程化地提高了前端开发的效率，但也对前端开发提出了更高的要求。
项目中，通过进一步对Vue的学习，发现Vue很多新的特性和难的特性都可以在JavaScript、TypeScript中找到源痕迹。Vue在新beta版本中加入的新特性，也越来越偏重于TS的语言特性，更加注重模板与类的使用。但奈何个人对TypeScript了解甚少，因此对于一些语法糖的使用规范理解得并不是很好。所以编码时，秉承着真不会就不用的原则..还有要感谢Element UI让我写出了还能看的UI。

**感想：基础为底石，方能站得稳，看得深，做得好。**

### 后端方面

从Django到Springboot的过渡可能如学语言时从python到java的过渡，虽然可说是由“简”入“繁”，但整个项目体验下来，不得不承认java语言的工程性会更强些。
整个项目的完成离不开对各种插件的使用，如Mybatis-plus、lombok、easy-excel等等，这些插件对一些常见的方法进行了封装实现，明显提高了开发效率。但其实过渡封装反而会对自定义方法的实现带来不便。
另外，Mybatis-plus官方文档在自定义SQL方面，特别xml实现方面，写的就很…不容易理解，也可能需要Mybatis的基础吧，然而课程时间限制来不及去补充了解下。现在感觉要想真正了解一些高效的插件，离不开对其实现代码的阅读。

**感想：所谓的高效开发往往可能是对底层实现的不求甚解。**

### 数据库方面

原本以为数据库应该是整个项目最简单的部分，但不得承认数据库改动次数还是挺多的，而且数据库的一变前后端都得变，就很繁琐。虽说数据库设计上也算还可以，但是由于数据量、实践和个人能力的问题，对数据库中关于并发、缓存、锁粒度等等并没有得到很好地应用。
此外，《高性能MySQL》这本书让我知道数据库理论并不是简单的，要想达到最优的性能，就必须了解如join、in等等这些选择方式背后的原理实现。当然，自以为然地掌握了原理并不是软件工程的最终目的，考卷上三四张表的设计已经满足了考察水平和时间要求，但现实生活中应用场景要远远复杂，真正要达到较高的掌握程度就要真正去实践。

**感想：检验理论掌握程度的最佳标准是实践。**

## 后记

三周的小学期对于拖延症患者而言并不长，精确些来说只是三天。至于说Deadline前的三天是如何度过，我只能期望大脑中的Monkey尽早能更加有远见些。
