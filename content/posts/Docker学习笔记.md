+++
title = 'Docker学习笔记'
date = 2024-10-02T16:21:08+08:00
categories =  ["技术文档","学习笔记"] 
tags = ["技术文档","学习笔记","Docker"]

+++

docker run 创建并运行一个容器，-d 让容器在后台运行

--name ：给容器起名字，必须唯一

-p xxxx:yyyy ：xxxx宿主机端口，yyyy容器内端口

-e key=value： 设置环境变量

# 基础

## 常见命令

~~~docker
下载镜像
# docker pull

查看已有的镜像
# docker images

删除镜像
# docker rmi

构建镜像
# docker build

保存到本地
# docker save

加载本地镜像
# docker load

将镜像推送到服务器
# docker push

创建并运行容器
# docker run

停止容器内部进程，容器还在
# docker stop

将容器内部停掉的进程再次启动起来
# docker start

查看当前容器运行状态
# docker ps
  -a 打印所有容器
删除容器
# docker rm

查看日志
# docker logs

进入容器
# docker exec
~~~

## 数据卷

是一个虚拟目录，是容器内目录与宿主机目录之间映射的桥梁

当创建容器时，如果挂载了数据卷且数据卷不存在，会自动创建数据卷

~~~docker
在创建容器时加入-v参数就可以实现挂载
# docker run …… -v 数据卷:容器内目录 ……

创建数据卷
# docker volume create

查看所有数据卷
# docker volume ls

删除指定数据卷
# docker volume rm

查看指定数据卷
# docker volume inspect

清除数据卷
# docker volume prune
~~~

## 本地目录挂载

在执行 docker run 时加入-v参数就可以实现挂载

~~~docker
# docker run -v 本地目录:容器内目录
~~~

本地目录必须以`/` 或 `./` 开头，如果直接名称开头会被识别为数据卷而非本地目录

- -v mysql:/var/lib/mysql 会被识别为一个数据卷叫mysql
- -v ./mysql:/var/lib/mysql 会被识别为当前目录下的mysql目录

# 自定义镜像

**镜像结构**

1. 入口-Entrypoint : 镜像运行入口，一般是程序启动的脚本参数
2. 层-Layer : 添加安装包、依赖、配置等，每次操作都形成新的一层
3. 基础镜像-BaseImage : 应用依赖的系统函数库、环境、配置、文件等

**Dockerfile**

一个文本文件，其中包含一个个的指令(Instruction)，用指令来说明要执行什么操作来构建镜像，常见指令如下

| 指令       | 说明                                       | 示例                        |
| ---------- | ------------------------------------------ | --------------------------- |
| FROM       | 指定基础镜像                               | FROM centos:6               |
| ENV        | 设置环境变量                               | ENV key value               |
| COPY       | 拷贝本地文件到镜像的指定目录               | COPY ./xx.jar /tmp/app.jar  |
| RUN        | 执行Linux的shell命令，一般是安装过程的命令 | RUN yum install gcc         |
| EXPOSE     | 指定容器运行时监听的端口                   | EXPOSE 8080                 |
| ENTRYPOINT | 镜像中应用的启动命令，容器运行时调用       | ENTRYPOINT java -jar xx.jar |



# k8s

## 核心组件

node：一个物理机或虚拟机，在这上面可以运行一个或多个pod

pod：最小的调度单元，一个或者多个应用容器的组合，容器运行环境

svc：service 将一组pod封装成一个服务，使这个服务可以通过一个固定的入口来访问

node:port ：外部服务，在节点上开放一个端口，将这个端口映射到svc的ip地址和端口上

ingress：管理从集群外部访问集群内部服务的入口和方式，可以配置不同的转发规则，还可以配置域名，负载均衡，SSL证书

ConfigMap：将配置信息封装起来，在应用程序中读取和使用，配置信息都是明文的

Secret：和ConfigMap类似，会将一些敏感信息封装起来，只是做了base64编码，需要配合k8s的其他安全机制使用

Volumes：将持久化存储的资源挂载到集群中的本地磁盘上，或者挂载到集群外部的远程存储上

Deployment：定义和管理应用程序的副本数量，当有副本发生故障，会自动创建新的副本，滚动更新是指可以定义和管理应用程序的更新策略，轻松升级应用程序的版本，不断用新的版本替换掉旧的版本

StatefulSet：和Deployment类似，保证每个副本都有自己稳定的网络标识符和持久化存储

## 架构

### Master-Worker架构

Master-node负责管理整个集群，Worker-node负责应用程序和服务

### Worker-node

每个节点包含三个组件 kubelet、kube-proxy、container-runtime

#### **container-runtime**：

简单理解为一个运行容器的软件，负责拉去容器镜像，创建容器，启动或停止容器，所有的容器都需要使用容器运行时来运行

常用的容器运行时有 Docker-Engine、Containerd、CRI-O、Mirantis Container Runtime

#### **kubelete**：

管理和维护每个节点上的Pod，并确保他们按照预期运行，可以定期从api-server组件接收新的或者修改后的Pod规范，监控工作节点的运行情况，然后将这些信息汇报给apiserver

#### **kube-proxy**：

负责为Pod对象提供网络代理和负载均衡服务

通常情况下Kubernetes集群包含多个节点，这些节点之间通过Service来进行通信，这就需要一个负载均衡器来接收请求，然后将请求发送到不同的节点上完成负载均衡，该功能就是由 kube-proxy 负责

### Master-node

4个基本组件：kube- apiserver、etcd、ControllerManager、Scheduler

#### kube- apiserver

它负责提供Kubernetes集群的API接口服务，所有的组件都会通过这个接口来进行通信，还负责对所有资源对象的 增 删 改 查 等操作进行认证授权和访问控制。

#### Scheduler：

调度器，它负责监控集群中所有节点的资源使用情况，根据调度策略将Pod调度到合适的节点上运行

#### Controller Manager：

管理集群中各种资源对象的状态

#### etcd：

高可用键值存储系统

#### Cloud Controller Manager：（如果用云服务商提供的Kubernetes集群会有这个东西）

云控制器管理器，是一个云平台相关的控制器，负责与云平台的 API 进行交互

### minikube 和 kubectl

minikube ：轻量级k8s发行版，搭建k8s环境

kubectl ：和集群环境进行交互的命令行工具



multipas常用命令

~~~
# 查看帮助
multipass help
multipass help <command>
# 创建⼀个名字叫做k3s的虚拟机
multipass launch --name k3s
# 在虚拟机中执⾏命令
multipass exec k3s -- ls -l
# 进⼊虚拟机并执⾏shell
multipass shell k3s
# 查看虚拟机的信息
multipass info k3s
# 停⽌虚拟机
multipass stop k3s
# 启动虚拟机
multipass start k3s
# 删除虚拟机
multipass delete k3s
# 清理虚拟机
multipass purge
# 查看虚拟机列表
multipass list
# 创建一台虚拟机
multipass launch --name k3s --cpus 2 --memory 4G --disk 10G
~~~

