---
title: ESLint自动修复
categories: web前端
author: lz_allen
tags:
  - typescript
date: 2019-05-21 23:59:31
---

## 模块安装

```typescript
cnpm i eslint typescript @typescript-eslint/parser @typescript-eslint/eslint-plugin --save-dev
```

## eslintrc配置文件

- .eslintrc.js

```typescript
module.exports = {
    "parser":"@typescript-eslint/parser",
    "plugins":["@typescript-eslint"],
    "rules":{
        "no-var":"error",
        "no-extra-semi":"error",
        "@typescript-eslint/indent":["error",2]
    },
    "parserOptions": {
        "ecmaVersion": 6,
        "sourceType": "module",
        "ecmaFeatures": {
          "modules": true
        }
    }
}
```

## ESLint自动修复

安装vscode的eslint插件
配置参数 .vscode\settings.json

```typescript
{
  "eslint.autoFixOnSave": true,
  "eslint.validate": [
      "javascript",
      "javascriptreact",
      {
          "language": "typescript",
          "autoFix": true
      },
       {
          "language": "typescriptreact",
          "autoFix": true
      }
  ]
}
```

## Git Hooks 检查

- Git 基本已经成为项目开发中默认的版本管理软件，在使用 Git 的项目中，我们可以为项目设置 Git Hooks 来帮我们在提交代码的各个阶段做一些代码检查等工作
- 钩子（Hooks） 都被存储在 Git 目录下的 hooks 子目录中。 也就是绝大部分项目中的 .git/hooks 目录
- 钩子分为两大类，客户端的和服务器端的
  - 客户端钩子主要被提交和合并这样的操作所调用
  - 而服务器端钩子作用于接收被推送的提交这样的联网操作，这里我们主要介绍客户端钩子

### pre-commit

- pre-commit 就是在代码提交之前做些东西，比如代码打包，代码检测，称之为钩子（hook）
- 在commit之前执行一个函数（callback）。这个函数成功执行完之后，再继续commit，但是失败之后就阻止commit
- 在.git->hooks->下面有个pre-commit.sample*，这个里面就是默认的函数(脚本)样本

### 安装pre-commit、

```typescript
npm install pre-commit --save-dev
```

### 脚本配置

```typescript
 "scripts": {
    "build": "tsc",
    "eslint": "eslint src --ext .ts",
    "eslint:fix": "eslint src --ext .ts --fix"
  },
  "pre-commit": [
    "eslint"
  ]
```

