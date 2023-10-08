---
title: Git - Git规范整理
tags:
  - Git
categories: []
date: 2023-10-07 15:15:00
updated: 2023-10-07 15:15:00
top_img: https://res.cloudinary.com/dt3vcmqdt/image/upload/v1611214191/Git/68747470733a2f2f6769742d73636d2e636f6d2f696d616765732f6c6f676f732f646f776e6c6f6164732f4769742d4c6f676f2d32436f6c6f722e706e67_lb4xtu.png
cover: https://res.cloudinary.com/dt3vcmqdt/image/upload/v1611214191/Git/68747470733a2f2f6769742d73636d2e636f6d2f696d616765732f6c6f676f732f646f776e6c6f6164732f4769742d4c6f676f2d32436f6c6f722e706e67_lb4xtu.png
---

{% note blue 'fas fa-bullhorn' modern %}
本篇教程是自己学习 Git 记录的
本篇教程不会介绍 Git 背景以及一些专业知识，毕竟本人时间有限哈哈哈！
如果喜欢教程就评论下或者把站点添加到你宝贵的收藏夹里面吧
{% endnote %}


# Git Commit 规范指南

本文旨在整理并介绍 Git 提交信息的规范和标准，以及各种提交描述的含义和用法。遵循一定的提交规范，能够保持项目的整洁和易读，也方便团队成员间的协作。

## 目录

- [Git Commit 规范指南](#git-commit-规范指南)
  - [目录](#目录)
  - [提交类型(Type)](#提交类型type)
  - [作用域(Scope)](#作用域scope)
  - [描述(Description)](#描述description)
  - [正文(Body)](#正文body)
  - [脚注(Footer)](#脚注footer)
  - [BREAKING CHANGE](#breaking-change)
    - [用法](#用法)
    - [案例](#案例)

## 提交类型(Type)

提交类型用于表明每个提交的主要目的。常见的提交类型包括：

- `feat`: 新功能(feature)
- `fix`: 修补 bug
- `docs`: 文档(documentation)
- `style`: 代码格式(不影响代码运行的变动)
- `refactor`: 重构(即不是新增功能，也不是修改 bug 的代码变动)
- `perf`: 提高性能的代码
- `test`: 增加测试
- `chore`: 构建过程或辅助工具的变动

## 作用域(Scope)

作用域用于指明本次提交影响的范围。例如数据层、控制层、视图层等，或者是具体的模块名称。

```plaintext
feat(user): add email validations
```

## 描述(Description)

简明扼要地描述本次提交的效果和目的。

```plaintext
fix: correct minor typos in code
```

## 正文(Body)

正文部分是对本次提交的详细描述，解释为什么要做这个提交，做了什么改动，以及开发的过程。

```plaintext
More detailed explanatory text, if necessary. 
Wrap it to about 72 characters or so. 
In some contexts, the first line is treated as the subject of an commit and the rest of the text as the body.
```

## 脚注(Footer)

脚注用于记录本次提交的一些重要信息，如与某个 issue 相关或者是破坏性的变更。


Issue #12


## BREAKING CHANGE

`BREAKING CHANGE` 用于标识不兼容的变更，通常包括变更的描述，影响和可能的解决方案。

### 用法

1. **明确标识**: 在提交信息中明确标识 `BREAKING CHANGE`，以通知团队成员此次提交包含重大变更。
2. **描述变更**: 在 `BREAKING CHANGE` 之后详细描述变更的内容，包括变更的原因、主要的不兼容点和可能的解决方案。
3. **引用相关文档或 Issue**: 如果有相关的文档或 Issue，可以在 `BREAKING CHANGE` 描述中提供链接，方便团队成员查找更多信息。

### 案例

假设你在一个 JavaScript 项目中修改了一个验证电子邮件的函数的 API，以前是这样的：

```javascript
function validateEmail(email) {
    // ...
}
```

现在改为了：

```javascript
function validateEmail(email, options) {
    // ...
}
```

你的提交信息可能是这样的：

```plaintext
feat(validate): update validateEmail API

BREAKING CHANGE: alters validateEmail API to accept an options object, which is a new second argument. This change is introduced to handle multiple validation scenarios more flexibly. 

See the updated documentation for the new API: [link to documentation]
```

在这个案例中：
- `BREAKING CHANGE` 标识了这是一个重大变更。
- 描述了变更的内容和原因。
- 提供了指向更新后文档的链接，以便其他人了解新的 API 用法。

此规范指南旨在创建清晰、一致且有意义的 Git 提交信息，从而提高代码的可读性和项目的可维护性。