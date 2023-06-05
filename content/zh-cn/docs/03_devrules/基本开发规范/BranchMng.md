---
title: "分支管理"
linkTitle: "分支管理"
weight: 1
---

## 概述
在软件研发的过程中，代码分支管理模型至关重要，直接影响着软件研发交付的效率。行业里存在着多各不同的分支管理模型，如GitFlow、GitHupFlow、GitLabFlow、OneFlow以及AOneFlow(阿里)。
各模型存在不同的优缺点，需要根据研发团队的实际情况进行选择（甚至对分支模型进行适当的变种，以适应团队实际的需求）。以下针对几个常见的模型进行介绍。

## 分支模型介绍
### GitFlow
GitFlow是最早诞生并得到广泛应用的一种工作流程。
该模型中存在两种长期分支：master和develop。master中存放对外发布的版本，只有稳定的发布版本才会合并到master中。develop用于日常开发，存放最新的开发版本。
同时还存在三种临时分支：feature, hotfix, release。
+ feature分支是为了开发某个特定功能，从develop分支中切出，开发完成后合并到develop分支中。
+ hotfix分支是修复发布后发现的Bug的分支，从master分支中切出，修补完成后再合并到master和develop分支。
+ release分支指发布稳定版本前使用的预发布分支，从develop分支中切出，预发布完成后，合并到develop和master分支中。

整体分支模型，如下图所示：

优点：
+ feature 分支使开发代码隔离，可以独立的完成开发、构建、测试
+ feature 分支开发周期长于release时，可避免未完成的feature进入生产环境
缺点：
+ 无法支持持续发布
+ 过于复杂的分支管理，加重了开发者的负担，使开发者不能专注开发

### GitHupFlow

### GitLabFlow

### OneFlow

### AOneflow

