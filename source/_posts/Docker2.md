---
title: Docker安装
date: 2017-12-21 16:35:45
tags:
  -- code
  -- Docker
---
###### [Docker — 从入门到实践](https://www.gitbook.com/book/yeasy/docker_practice)

### Ubuntu 安装 Docker CE

Docker CE 支持以下版本的 Ubuntu 操作系统：
- Artful 17.10 (Docker CE 17.11 Edge)
- Zesty 17.04
- Xenial 16.04 (LTS)
- Trusty 14.04 (LTS)

**卸载旧版本**
```bash
$ sudo apt-get remove docker \
               docker-engine \
               docker.io
```
**Ubuntu 14.04 可选内核模块**<br>
从 Ubuntu 14.04 开始，一部分内核模块移到了可选内核模块包 (linux-image-extra-*) ，以减少内核软件包的体积。正常安装的系统应该会包含可选内核模块包，而一些裁剪后的系统可能会将其精简掉。AUFS 内核驱动属于可选内核模块的一部分，作为推荐的 Docker 存储层驱动，一般建议安装可选内核模块包以使用 AUFS。

如果系统没有安装可选内核模块的话，可以执行下面的命令来安装可选内核模块包：
```bash
$ sudo apt-get update

$ sudo apt-get install \
    linux-image-extra-$(uname -r) \
    linux-image-extra-virtual
```

<!-- more -->

**使用 APT 镜像源 安装**<br>
由于官方源使用 HTTPS 以确保软件下载过程中不被篡改。因此，我们首先需要添加使用 HTTPS 传输的软件包以及 CA 证书。
```bash
$ sudo apt-get update

$ sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    software-properties-common
```
为了确认所下载软件包的合法性，需要添加软件源的 GPG 密钥。
```bash
$ curl -fsSL https://mirrors.ustc.edu.cn/docker-ce/linux/ubuntu/gpg | sudo apt-key add -


# 官方源
# $ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```
向 source.list 中添加 Docker 软件源
```bash
$ sudo add-apt-repository \
    "deb [arch=amd64] https://mirrors.ustc.edu.cn/docker-ce/linux/ubuntu \
    $(lsb_release -cs) \
    stable"


# 官方源
# $ sudo add-apt-repository \
#    "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
#    $(lsb_release -cs) \
#    stable"
```
**安装 Docker CE**
```
$ sudo apt-get update

$ sudo apt-get install docker-ce
```

**使用脚本自动安装**<br>
在测试或开发环境中 Docker 官方为了简化安装流程，提供了一套便捷的安装脚本，Ubuntu 系统上可以使用这套脚本安装：
```bash
$ curl -fsSL get.docker.com -o get-docker.sh
$ sudo sh get-docker.sh --mirror Aliyun
```

**启动 Docker CE**
```bash
$ sudo systemctl enable docker
$ sudo systemctl start docker

#Ubuntu 14.04 请使用以下命令启动：
$ sudo service docker start
```

**测试 Docker 是否安装正确**
```
$ docker run hello-world

Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
ca4f61b1923c: Pull complete
Digest: sha256:be0cd392e45be79ffeffa6b05338b98ebb16c87b255f48e297ec7f98e123905c
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://cloud.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/engine/userguide/
```
若能正常输出以上信息，则说明安装成功。
