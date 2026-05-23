---
url: /54.部署/Docker常用操作.md
---

## 初识Docker与容器

### 介绍

::: note 什么是Docker
Docker是基于Go语言实现的开源容器项目。\
Docker容器技术是基于Linux容器技术的基础上开发而来的。\
Docker是一个开源的软件部署解决方案，也是轻量级的应用部署容器框架，它轻巧，且易移植
:::

::: note Docker能做什么
docker可以打包、发布、运行任何应用\
如何正确的构建应用，通过容器来打包应用、解耦应用和运行平台。在迁移的时候，只需要在新的服务器上启动需要的容器就可以了，无论新旧服务器是否是一类型的平台。这样为我们节约了时间，降低部署应用的风险。

使用Docker提高了开发的运维的优势，能更快速的交付和部署、更搞笑的资源利用、更轻松的迁移和扩展、更简单的更新管理等。
:::

### 比较

容器技术与传统虚拟机技术的比较

| 特性 | 容器 | 虚拟机 |
|------|------ | ------ |
| 启动速度 | 秒级 | 分钟级 |
| 性能 | 接近原生| 较弱 |
| 内存代价 | 很小 | 较多 |
| 硬盘使用 | 一般为MB  | 一般为GB |
| 运行密度 | 单机支持上千个容器 | 一般几十个 |
| 隔离性 | 安全隔离 | 完全隔离 |
| 迁移性 | 优秀 | 一般 |

**特点**

* 速度飞快以及优雅的隔离框架
* 物美价廉
* CPU/内存的低消耗
* 快速开/关机
* 跨云计算基础构架

**核心理念**

1. Docker镜像类似于虚拟机镜像，可以将它理解为一个只读的模板。
2. Docker容器类似于一个轻量级的沙箱，Docker利用容器来运行和隔离应用。
3. Docker仓库类似于代码仓库，是Docker集中存放镜像文件的地方。

## 安装启动

### 安装

CentOS 7下通过yum安装Docker

```sh
#安装yum工具包
yum install -y yum-utils

#添加yum源
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

#安装
yum -y install docker-ce
 
 #或者下载rpm包安装
yum -y localinstall docker-ce-18.03.1.ce-1.el7.centos.x86_64.rpm
```

### 启动

```sh
#启动
systemctl start docker

#停止Docker
systemctl stop docker

#查看Docker运行状态
systemctl status docker

#重启docker
systemctl restart docker

#设置Docker开机启动
systemctl enable docker

#取消docker开机自启动
systemctl disable docker

#查看版本
docker version	
```

## 镜像

### 加速访问

```sh
vi  /etc/docker/daemon.json
#往里面添加
{
    "registry-mirrors": ["https://registry.docker-cn.com"],
    "live-restore": true
}
```

### 拉取镜像

```sh
docker [image] pull NAME [ :TAG]

#指定仓库地址
docker [image] pull [仓库地址/]<镜像名>[:TAG]	

#指定镜像代理服务地址
docker [image] pull --registry-mirror=proxy_URL NAME [ :TAG]	

#比如下载MySQL5.7镜像
docker pull mysql:5.7
```

### 查看

Docker镜像保存在/var/lib/docker目录下

```sh
docker images
```

### 删除

```sh

docker rmi docker.io/tomcat:7.0.77-jre7     
#或者
docker rmi 镜像id
```

## 容器

### 启停

```sh
#查看在运行的容器
docker ps

#查看所有容器
docker ps -a

#启动容器
docker start 容器名或id

#停止容器
docker stop 容器名或id

#重启容器
docker restart 容器名或id

#使用镜像创建容器并运行
docker run -dit --privileged --name=centos7 daocloud.io/centos:latest /usr/sbin/init
```

::: note 参数说明
centos7：自定义容器名\
\--privileged：大约在0.6版，privileged被引入docker。使用该参数，container内的root拥有真正的root权限。否则，container内的root只是外部的一个普通用户权限。privileged启动的容器，可以看到很多host上的设备，并且可以执行mount。甚至允许你在docker容器中启动docker容器
:::

### 修改容器启动参数

**1、Docker 命令修改**

```sh
docker container update --restart=always 容器名字
```

**2、直接改配置文件**

首先停止容器，不然无法修改配置文件

配置文件路径为：`/var/lib/docker/containers/容器ID`

在该目录下找到一个文件 `hostconfig.json` ，找到该文件中关键字 `RestartPolicy`

修改前配置：`"RestartPolicy":{"Name":"no","MaximumRetryCount":0}`

修改后配置：`"RestartPolicy":{"Name":"always","MaximumRetryCount":0}`

最后启动容器。

### 看run启动日志

```sh
docker logs -f -t --tail 20 <容器id/容器名>
```

### 使用容器构建镜像

```sh
docker commit -a authorName -m "提交说明" centos7 authorName/cent7
```

::: note 参数说明
centos7 容器名\
-a :提交的镜像作者\
-c :使用Dockerfile指令来创建镜像\
-m :提交时的说明文字\
-p :在commit时，将容器暂停
:::

更多可以参考：[Docker中文文档](http://www.dockerinfo.net/document)

### 安装MySQL

```sh
#下载MySQL5.7镜像
docker pull mysql:5.7

#运行
#-e MYSQL_ROOT_PASSWORD 设置mysql的root用户秘密
docker run --name hi-mysql -e MYSQL_ROOT_PASSWORD=my-pwd -d mysql:5.7	

#端口映射
docker run --name hi-mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=my-pwd -d mysql:5.7	
```

### 安装Nginx

```sh
#下载nginx镜像
docker pull nginx:1.14

#创建并启动容器
docker run -dit -p 80:80 --name nginx nginx:1.14
```
