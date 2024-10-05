+++
title = 'Go Zero学习笔记'
date = 2024-10-02T16:25:32+08:00
categories =  ["技术文档","学习笔记"] 
tags = ["技术文档","学习笔记","go-zero"]

+++

# 开始

## 目录结构

~~~c
Demo
└─app	微服务文件
    └─admin		其中一个微服务	
        ├─cmd	
        │  ├─api	api接口层	对外提供服务
        │  │  │  admin.go	启动函数
        │  │  │
        │  │  ├─desc	定义接口的
        │  │  │  │  main.api	定义路由
        │  │  │  │  main.json
        │  │  │  │
        │  │  │  └─admin
        │  │  │          admin.api	定义路由中的req和resp
        │  │  │
        │  │  ├─etc
        │  │  │      admin.yaml		配置文件
        │  │  │
        │  │  └─internal	goctl生成的文件，重点关注
        │  │      ├─config
        │  │      │      config.go
        │  │      │
        │  │      ├─handler
        │  │      │      createAdminHandler.go
        │  │      │      deleteAdminHandler.go
        │  │      │      getAdminsHandler.go
        │  │      │      routes.go
        │  │      │      updateAdminHandler.go
        │  │      │
        │  │      ├─logic
        │  │      │      createAdminLogic.go
        │  │      │      deleteAdminLogic.go
        │  │      │      getAdminsLogic.go
        │  │      │      updateAdminLogic.go
        │  │      │
        │  │      ├─svc
        │  │      │      serviceContext.go
        │  │      │
        │  │      └─types
        │  │              types.go
        │  │
        │  └─rpc	内部服务
        │      │  main.go
        │      │
        │      ├─admin
        │      │      admin.go
        │      │
        │      ├─etc
        │      │      admin.yaml
        │      │
        │      ├─internal
        │      │  ├─config
        │      │  │      config.go
        │      │  │
        │      │  ├─logic
        │      │  │      addAdminLogic.go
        │      │  │      delAdminLogic.go
        │      │  │      getAdminByIdLogic.go
        │      │  │      searchAdminLogic.go
        │      │  │      updateAdminLogic.go
        │      │  │
        │      │  ├─server
        │      │  │      adminServer.go
        │      │  │
        │      │  └─svc
        │      │          serviceContext.go
        │      │
        │      └─pb
        │              admin.pb.go
        │              admin.proto
        │              admin_grpc.pb.go
        │
        └─model
                adminModel.go
                adminModel_gen.go
                vars.go


~~~



## 开发流程

1. 首先设计数据库和数据表
2. 使用工具先生成model
3. 先开发api层
4. 再开发rpc层
5. 在api层注册rpc服务，调用rpc方法，对外提供接口
6. 生成接口文档

## go-zero 的 API 语法规范

~~~c

type (	
    // 定义对象
    NameStruct {
        字段名 类型 `json:"字段名"`
    }
)
~~~

字段类型：

- 基础类型：`int`、`int32`、`int64`、`uint`、`uint32`、`uint64`、`float32`、 `float64`、`boll`、`string`、`bytes`
- 结构体可以嵌套
- 不支持 Map 但是可以自定义一个 key-value 的结构进行嵌套
- 切片：`[]T` T 可以是任意基础类型或结构体
-  时间类型使用 `string`

# 安装

1. 安装goctl

   `go install github.com/zeromicro/go-zero/tools/goctl@latest` 使用 `goctl`测试安装是否成功

2. 安装组件

   `goctl env check --install --verbose --force`

   > go-zero微服务框架下的代码生成工具。使用goctl可显著提升开发效率，让开发人员将时间重点放在业务开发上，其功能有
   >
   > api服务生成
   >
   > rpc服务生成
   >
   > model代码生成
   >
   > 模板管理

3. 安装protoc & protoc-gen-go

   `goctl env check -i -f --verbose`

快速创建 api 服务

`goctl api new api`

# ETCD

Etcd是一个高可用的分布式键值存储系统，主要用于共享配置信息和服务发现。它采用Raft一致性算法来保证数据的强一致性，并且支持对数据进行监视和更新。 *可以理解为加强版的 redis* ，比 redis 的数据可靠性更强

主要是用于微服务的配置中心，服务发现

## 基本命令

~~~c
// 设置或更新值
etcdctl put name 张三
// 获取值
etcdctl get name
// 只要value
etcdctl get name --print-value-only
// 获取name前缀的键值对
etcdctl get --prefix name
// 删除键值对
etcdctl del name
// 监听键的变化
etcdctl watch name
~~~

## 微服务拆分原则

创业型项目使用单体架构、大型项目使用微服务架构，避免后续拆分麻烦

- 拆分目标

​	**高内聚:**每个微服务的职责要尽量单一，包含的业务相互关联度高、完整度高。

​	**低耦合:**每个微服务的功能要相对独立，尽量减少对其它微服务的依赖。

- 拆分原则

​	**纵向拆分:**按照业务模块来拆分

​	**横向拆分:**抽取公共服务，提高复用性
