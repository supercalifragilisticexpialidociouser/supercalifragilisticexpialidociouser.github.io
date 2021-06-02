---
title: Flowable
date: 2021-05-27 09:52:06
tags: [6.6.0]
---

# 入门

有两种方式来使用Flowable工作流引擎：

- 内嵌式：直接将Flowable的JAR添加为应用的依赖。这种方式适合需要高度可定制的场景。
- 独立应用式：将Flowable的WAR部署成一个独立应用，然后自已的应用通过HTTP使用Flowable REST API来与Flowable工作流引擎通信。这种方式几乎没有什么耦合，工作量少。另外，Flowable还提供了一些开箱即用的应用（Flowable Modeler、Flowable Admin、Flowable IDM 和 Flowable Task）。（推荐）

## 创建项目

Flowable既可以在普通的Java SE项目中使用，也可以在Spring Boot项目中使用，这里以Spring Boot项目为例。

采用内嵌式的，需要在项目中导入相应依赖，并在配置文件中做相应配置。而采用独立应用式的，则不需要导入任何Flowable依赖，直接通过HTTP访问Flowable REST API。

## 流程定义和部署

# 设计模式

## 门面模式

ProcessEngine
