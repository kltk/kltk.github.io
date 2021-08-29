---
layout: post
title: node-sass binary
tags:
    - nodejs
    - node-sass
---
又碰到 node-sass 安装失败，总会有各种各样的情况，在这整理记录下。

### 访问 npm 异常

node-sass 安装失败的一个原因是到 npm 仓库的网络请求异常，速度慢、请求超时等，不止 node-sass，其它包的安装也会受到影响。这个问题可以使用代理或者使用第三方的npm镜像来解决。

```bash
# 使用梯子，如下命令，需自行替换地址为可用的实际地址
npm config set proxy http://127.0.0.1:1080

# 不使用时取消梯子
npm config delete proxy

# npm registry
# https://docs.npmjs.com/cli/v7/using-npm/registry
# 替换 npm 源为 taobao 镜像
npm config set registry https://registry.npm.taobao.org
```

### 不能下载预编译包

node-sass 安装时还会下载一个预编译的文件，这个文件托管在 github 网站上，同样可能由于网络问题，导致不能正常下载。除了使用梯子外，node-sass 还提供了从其它地方获取预编译文件的方案。

```bash
# 更多 node-sass 预编译文件的使用
# https://github.com/sass/node-sass#binary-configuration-parameters

# 使用 taobao 提供的预编译镜像安装 node-sass
# http://npm.taobao.org/mirrors 还提供了其它 npm 相关的镜像
SASS_BINARY_SITE=http://npm.taobao.org/mirrors/node-sass npm install node-sass

# 使用自行下载的预编译文件安装
SASS_BINARY_PATH=/path/to/sass/binary npm install node-sass
```

### 没有预编译包

node-sass 提供了多个运行环境下的预编译文件，运行环境包括操作系统、cpu架构、node版本等几个因素。

```bash
# 查看当前运行环境
node -p "[process.platform, process.arch, process.versions.modules].join('-')"
```

主流的操作系统、cpu架构都有预编译文件。有一种情况下会找不到适合的预编译文件，在新版本 node 环境上安装旧版本的 node-sass，因为发布时只会提供当前版本及旧版本的文件。
