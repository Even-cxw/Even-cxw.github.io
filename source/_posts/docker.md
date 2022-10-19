---
title: docker
date: 2022-10-12 16:15:24
categories: docker
tags: [docker]
comments: false
---


## docker命令
`docker --help`
- yum install docker-io -y 下载
- /etc/init.d/docker start 启动docker
- docker serach 搜索镜像
> docker serach centos // docker搜索centos这个镜像
- docker pull 镜像 
> docker pull centos // docker pull centos 镜像
- docker images 查看镜像
> docker images
- docker rmi 删除镜像
> docker rmi centos
- docker run --name mydocker -it centos /bin/bash 基于镜像创建容器
`当执行run时，如没有centos 会自动pull centos`
1. --name : 定义容器名字
2. -i ： 保持容器打开
3. -it : 保持容器打开并开个未终端tty
4. -d : 在后台运行

- docker start <id> 启动某个id容器
- docker attach <id> 进入某个容器 `会有问题`
> docker exec -it containerid /bin/sh  进入containerid容器
- docker stop <id>  停止某个id容器
1. -d : 在后台运行

- 查看mynginx容器Pid
> docker inspect --format "{{.State.Pid}}" mynginx
- 退出容器
> exit 

- docker ps -a 查看容器
> docker ps -a  // 查看所有容器 
1. | grep web-htsc ：grep过滤为 web-htsc
2. ps : （Process Status）进程状态


## example
- 通过docker创建nginx容器
> docker run -d --name mynginx nginx