---
title: 从vue-cnode开始学习Vue
date: 2017-11-02 22:08:24
tags:
  - vue
  - code
---

vue-cnode是一个单页应用，源码地址：```https://github.com/wszgxa/vue-cnode```，我fork了一份到：```https://github.com/Humilton/vue-cnode/```,让我们从运行开始。

### 运行
首先要有NodeJS：各个系统版本从http://nodejs.io/下载。我是下载过的，所以直接升级：
```
C:\Users\Hamilton>npm -v
3.10.10

C:\Users\Hamilton>npm install npm -g

C:\Users\Hamilton>npm -v
5.5.1
```
其次，要有Vue：工具地址：https://github.com/vuejs/vue-cli，配置全局工具：
```
npm install -g vue-cli
```

```npm install```安装依赖：
```
E:\Humilton\vue-cnode>git remote -v
origin  https://github.com/Humilton/vue-cnode/ (fetch)
origin  https://github.com/Humilton/vue-cnode/ (push)

E:\Humilton\vue-cnode>npm i
npm WARN deprecated babel-preset-es2015@6.24.1: 🙌  Thanks for using Babel: we recommend using babel-preset-env now: please read babeljs.io/env to update!
[ .................] | fetchMetadata: WARN deprecated babel-preset-es2015@6.24.1: 🙌  Thanks for using Babel: we recomnpm notice created a lockfile as package-lock.json. You should commit this file.
npm WARN optional SKIPPING OPTIONAL DEPENDENCY: fsevents@1.1.2 (node_modules\fsevents):
npm WARN notsup SKIPPING OPTIONAL DEPENDENCY: Unsupported platform for fsevents@1.1.2: wanted {"os":"darwin","arch":"any"} (current: {"os":"win32","arch":"x64"})

added 814 packages in 170.865s

```

```npm run dev```执行:
```
E:\Humilton\vue-cnode>npm run dev

> vue-cnode@1.0.0 dev E:\Humilton\vue-cnode
> node build/dev-server.js

[HPM] Proxy created: /api  ->  https://cnodejs.org/
Listening at http://localhost:8080

webpack built 5d0d1a042394b7ad412c in 41981ms
Hash: 5d0d1a042394b7ad412c
Version: webpack 1.15.0
Time: 41981ms
     Asset     Size  Chunks             Chunk Names
    0.0.js  39.2 kB       0  [emitted]
    app.js   1.7 MB       1  [emitted]  app
    2.2.js  80.3 kB       2  [emitted]
    3.3.js  74.3 kB       3  [emitted]
    4.4.js  40.4 kB       4  [emitted]
    5.5.js  20.5 kB       5  [emitted]
    6.6.js  22.3 kB       6  [emitted]
index.html   2.6 kB          [emitted]
Child html-webpack-plugin for "index.html":
         Asset     Size  Chunks       Chunk Names
    index.html  28.1 kB       0
webpack: Compiled successfully.
```

效果图：
![](/img/cnode.png)
![](/img/cnode2.png)

### ~~迁移~~
~~因为vue-cnode运行于vue1.0，现在对它升级。参考[从 Vue 1.x 迁移](https://cn.vuejs.org/v2/guide/migration.html#FAQ)~~

>> 我该从哪里开始项目迁移呢？

> 1. 首先，在当前项目下运行[迁移工具](https://github.com/vuejs/vue-migration-helper)。我们非常谨慎地把高级 Vue 升级过程简化为使用一个简单的命令行工具。当工具识别出旧有的特性后，就会告知你并给出建议，同时附上关于详细信息的链接。

> 2. 然后，浏览本页面的侧边栏列出的内容。如果发现有的标题对你的项目有影响，但是迁移工具没有给出提示，请检查自己的项目。

> 3. 如果你的项目有测试代码，运行并查看仍然失败的地方。如果没有测试代码，在浏览器中打开你的程序，通过导航环顾并留意那些报错或警告信息。

> 4. 现在，你的应用程序应该已彻底完成迁移。如果你渴望了解更多，可以阅读本页面剩余部分 - 或者从[介绍](https://cn.vuejs.org/v2/guide/index.html)部分，从头开始深入新的文档和改进过的指南。由于你已经熟悉一些核心概念，所以许多部分已经被删除掉。

依次执行
```shell
npm install --global vue-migration-helper  #安装工具
vue-migration-helper  #执行工具
#按照提示修改后执行：
npm i
```

![](img/vue-migration-helper.png)


### 修改
我们修改源代码，使它符合我们的情况：
