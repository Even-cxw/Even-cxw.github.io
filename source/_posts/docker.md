---
title: docker
date: 2022-10-12 16:15:24
categories: docker
tags: [docker]
comments: false
---


## docker命令
`docker --help`

### docker镜像相关命令
- yum install docker-io -y 下载

- /etc/init.d/docker start 启动docker

- docker搜索镜像
> docker serach centos // docker搜索centos这个镜像

- docker pull 镜像 
> docker pull centos // docker pull centos 镜像

- docker查看镜像
> docker images

- docker删除镜像
> docker rmi centos


### docker容器相关命令

- docker ps -a 查看容器
> docker ps -a  // 查看所有容器 
1. | grep web-htsc ：grep过滤为 web-htsc
2. ps : （Process Status）进程状态

- docker进入某个容器
> docker exec -it containerid /bin/sh  进入containerid容器

- 容器退出
> exit 

- docker基于镜像创建容器
> docker run --name mydocker -it centos /bin/bash
`当执行run时，如没有centos 会自动pull centos`
1. --name : 定义容器名字
2. -i ： 保持容器打开
3. -it : 保持容器打开并开个未终端
4. -d : 在后台运行

- docker启动某个id容器
> docker start <id> 

- docker停止某个id容器
>  docker stop <id>  
1. -d : 在后台运行

- docker查看mynginx容器Pid
> docker inspect --format "{{.State.Pid}}" mynginx


## nginx
- docker中nginx常在位置
> etc/nginx/conf.d/app/

## linux命令
- 查看当前目录所在位置
> pwd： 全拼（print working directory）打印工作目录

- 重新命名文件
> mv: 全拼（move）会将源文件删除或移动
1. 将a.txt命名为b.txt
> mv a.txt b.txt

- 删除文件
> rm


## 华讯项目-云网
- docer-compose.yml文件
```yml
htsc:
  image: "web-htsc"
  container_name: web-htsc
  restart: always
  cpus: 1
  mem_linit: lg
  volumes: 
    - /app/cloudnet/web-tsc:/web
```
1. volumes的作用
> 将/app/cloudnet/web-tsc此文件 解压到 `容器名：web-htsc/web`下 


## example
- 通过docker创建nginx容器
> docker run -d --name mynginx nginx