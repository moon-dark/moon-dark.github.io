---
title: docker常用命令
date: 2018-05-21 15:06:50
tags:
 - code
---

### Manage Docker as a non-root user
- Add the docker group if it doesn't already exist:
``sudo groupadd docker``
- Add the connected user "$USER" to the docker group. Change the user name to match your preferred user if you do not want to use your current user:
``sudo gpasswd -a $USER docker``
- Either do a ``newgrp docker`` or ``log out/in`` to activate the changes to groups.

You can use
```
docker run hello-world
```
to check if you can run docker without sudo.


### 安装docker和docker compose
1、安装docker
```
sudo curl -sSL https://get.daocloud.io/docker | sh
```
2、安装 docker compose，资料：install-compose
```
sudo curl -L https://github.com/docker/compose/releases/download/1.18.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
```

```
# 构建
docker-compose build
# 运行
docker-compose up  // 按下ctrl+c退出停止。
# 后台运行
docker-compose up -d
# 查看compose启动的各个容器的状态：
docker-compose ps
# 进入某个容器,譬如php：
docker-compose exec php bash
# 退出某个容器
exit
# 停止 docker compose启动的容器：
docker-compose stop
```

docker 位置：
```
/var/lib/docker

```

```
docker build -t humilton/nginx-google-proxy .
docker run --restart always -d --name nginx-google-proxy humilton/nginx-google-proxy:latest
docker exec -ti gorush /bin/sh

docker container ls
docker stop <container id>
docker rm <container id>
docker rmi <image id>

# docker中批量删除 tag为none的镜像
docker images|grep none|awk '{print $3}'|xargs docker rmi


docker-compose down -v  # 删除volumes


To show only running containers use the given command:

docker ps
To show all containers use the given command:

docker ps -a
To show the latest created container (includes all states) use the given command:

docker ps -l
To show n last created containers (includes all states) use the given command:

docker ps -n=-1
To display total file sizes use the given command:

docker ps -s
The content presented above is from docker.com.

In The New Version Of Docker, Commands Are Update, Some Management Commands Are Added:

docker container ls
Is Used to list all the running containers.

docker container ls -a
```
