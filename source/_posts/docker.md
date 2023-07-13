---
title: docker
date: 2022-10-12 16:15:24
categories: docker
tags: [docker]
comments: false
---

## 基础知识

- 常见的操作系统：
1、UNIX操作系属于服务器专用；
2、MACos操作系统 这是苹果自己的系统；
3、Linux操作系统一般是用在服务器上，客户端都有；
4、Solaris操作系统这也是服务器专用。
5、Window操作系统

- linux内核 - 用来控制硬件设备
` 服务器的内核一般是： linux内核`
` macOS 和 iOS 的内核是：NeXTSTEP`
``

## 查看docker是否安装
- `docker version` docker版本
- `docker-compose version` docker-compose版本

## docker的理解
- docker daemon的意思为：Docker的守护进程；daemon的主要功能包括镜像管理、镜像构建、REST API、身份验证、安全、核心网络以及编排。
- docker容器 =  new class docker镜像
- Docker 容器通过 Docker 镜像来创建。


## docker命令
`docker --help`

### docker镜像相关命令
- yum install docker-io -y 下载

- /etc/init.d/docker start 启动docker

- docker搜索镜像
` docker serach centos // docker搜索centos这个镜像`

- docker pull 镜像 
`docker pull centos // docker pull centos 镜像`

- docker查看镜像
`docker images`

- docker删除镜像
`docker rmi centos`

- 构建镜像 根据当前目录下的Dockerfile文件构建标签为1.0的web-htsc镜像
`docker build -t web-htsc:1.0 .`
  1. -t : 指定镜像标签 1.0

### docker容器相关命令

- 容器docker的yum源位置 - 并配置yum源
`/etc/yum.repos.d/`
  1. CentOS-Linux-AppStream.repo
  ```yml
    name=CentOS Linux $releasever - AppStream
    baseurl=https://mirrors.aliyun.com/centos/8.5.2111/AppStream/x86_64/os/
    gpgcheck=0
    enabled=1
  ```
  2. CentOS-Linux-BaseOS.repo
  ```yml
    name=CentOS Linux $releasever - BaseOS
    baseurl=https://mirrors.aliyun.com/centos/8.5.2111/BaseOS/x86_64/os/
    gpgcheck=0
    enabled=1
  ```


- 指定镜像创建容器
`docker run -itd centos-nginx:1.1`
  1. -i ： 保持容器打开
  2. -t : 保持容器打开并开个未终端
  3. -d : 在后台运行

- docker ps -a 查看容器
`docker ps -a  // 查看所有容器`
  1. | grep web-htsc ：grep过滤为 web-htsc
  2. ps : （Process Status）进程状态

- docker进入某个容器
`docker exec -it containerid /bin/sh  进入containerid容器`
  1. deocker exec: 运行Docker容器内的命名
  2. -it : 保持容器打开并开个未终端
  3. containerid : 容器id
  4. /bin/bash : 进入容器后执行的命令 

- 容器退出
`exit` 

- 查看容器安装了哪些依赖项 - 过滤nginx
`yum list installed | grep nginx`

- docker基于镜像创建容器
`docker run --name mydocker -it centos /bin/bash`
`当执行run时，如没有centos 会自动pull centos`
  1. --name : 定义容器名字
  2. -i ： 保持容器打开
  3. -it : 保持容器打开并开个未终端
  4. -d : 在后台运行

- docker启动某个id容器
`docker start <id>`

- docker停止某个id容器
`docker stop <id>`
  1. -d : 在后台运行

- 查看当前docker nginx暴漏ip等配置命令
`docker inspect <id>`



## Dockerfile常用命令
1. FROM: 制作image时依据的基本image
2. RUN：制作image时执行的命令，一般在Dockerfile中多次出现
3. CMD：启动docker时执行的命令，在Dockerfile中只出现一次
4. ENV：设置环境变量
5. COPY：制作image时，将文件系统中的文件复制到Docker镜像中
6. WORKDIR：设置工作目录
7. EXPOSE：设置向外暴露的端口
8. VOLUME：设置容器与外界映射的目录
9. ENTRYPOINT：设置容器启动时执行的命令
10. USER：设置容器启动时的用户名或UID
11. MAINTAINER：设置镜像作者

 - dockerfile文件
 1. -y选择yes选项继续执行下去
```yml
  FROM centos:8.4.2105

  COPY CentOS-Linux-AppStream.repo /etc/yum.repos.d/CentOS-Linux-AppStream.repo
  COPY CentOS-Linux-BaseOS.repo /etc/yum.repos.d/CentOS-Linux-BaseOS.repo

  RUN yum repolist

  RUN yum install nginx.x86_64 -y
```


## nginx
- docker中nginx常在位置
`etc/nginx/conf.d/app/`
  1. etc : 全拼（etcetera）等同于windows中的C盘 - 用于存放系统级别的配置文件

## linux命令
- 查看当前目录所在位置
`pwd： 全拼（print working directory）打印工作目录`

- 重新命名文件
`mv: 全拼（move）会将源文件删除或移动`
1. 将a.txt命名为b.txt
`mv a.txt b.txt`

- 删除文件
`rm`


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


## 测试环境
- 通过docker创建nginx容器
> docker run -d --name mynginx nginx

- 华泰服务目录 /app/cloudnet/web/
1. 查看web补丁包 是否是一级目录

2. 在web目录下查看是否有docker-compose.yml文件，查看哪些目录被挂在

```
volumes:
  - /app/cloudnet/web-tsc:/web
```

3. 将更新的目录mv备份

```
mv web-htsc web-htsc.20220235.bak
```
4. 将新的web-htsc.zip文件解压到web
```
unzip web-htsc.zip
```
5. 查看解压后的目录权限
```
ll
```
6. 重启docker
```
docker-compose down
docker-compose up -d
```
```
```


### 生产环境发包

1. 将web压缩包放到测试环境
2. 生产环境连接测试环境ip, 将web压缩包放到生产环境 `sz 包名`
3. 切换要发版的ip服务，删除当前版本包 `rm -rf 报名`
4. 上传包到当前目录 `rz`
5. 解压包 `unzip 包名`
6. 锁包 `chomd -R 777 web-htsc`
6. 重启docker `docker-compose down && docker-compose up -d`