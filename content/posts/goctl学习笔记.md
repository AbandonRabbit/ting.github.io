+++
title = 'goctl学习笔记'
date = 2024-10-06T15:32:30+08:00
categories =  ["学习笔记"] 
tags = ["学习笔记","goctl"]

+++

# 

goctl 是 go-zero 的内置脚手架，是提升开发效率的一大利器，可以一键生成代码、文档、部署 k8s yaml、dockerfile 等。

官方文档：[goctl 安装 | go-zero Documentation](https://link.juejin.cn/?target=https%3A%2F%2Fgo-zero.dev%2Fdocs%2Ftasks%2Finstallation%2Fgoctl%3F_highlight%3Dgoctl)

# goctl安装

```sh
go install github.com/zeromicro/go-zero/tools/goctl@latest
```

## 验证

```sh
goctl --version
```

![image-20240324183102287](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/af889cb1a43a4738b8be7dfcd14ad155~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=438&h=46&s=5585&e=png&b=2b2b2b)

# goctl使用实战

接下来和我使用goctl实现快速搭建api服务、rpc服务脚手架以及model代码的生成：

## goctl api

goctl api是goctl中的核心模块之一，通过该命令可以快速生成一个api演示项目。

我们可以通过goctl api --help查看goctl api的所有指令。

```sh
goctl api --help
```

### goctl api new

用于快速生成 Go HTTP 服务，需要指定服务名称，输出目录为当前工作目录。

- 创建demo服务

```sh
goctl api new demo
```

![image-20240324184942435](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5eb645fd4d8a4e3b9e58ac63ab14a8b1~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=520&h=42&s=4271&e=png&b=2b2b2b)

这样在当前目录下就能够生成demo的api服务了。

下图为生成的项目目录结构:

![image-20240324185033989](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/67ed37806a0041ed9711bd61be049658~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=311&h=362&s=15609&e=png&b=3e4143)

- 在logic下面的demologic.go编写逻辑

```go
func (l *DemoLogic) Demo(req *types.Request) (resp *types.Response, err error) {
	// todo: add your logic here and delete this line
	return &types.Response{
		Message: "hello world",
	}, nil
}
```

- 启动服务

```sh
# 跳到demo服务根目录
cd demo
# 启动服务（默认8888端口，可在etc/demo-api.yaml配置）
go run demo.go -f etc/demo-api.yaml
```

- 访问服务

```sh
http://localhost:8888/from/you
```

![image-20240324190756997](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bbdeaed4bc5f40e7bb90396ad37efa55~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=259&h=53&s=809&e=png&b=202124)

至此一个Go-Zero的单体服务就完成啦。

### goctl api doc

- 根据 api 文件生成 markdown 文档。
- -dir表示文档输出目录

```sh
goctl api doc -dir ./
```

![image-20240324191938334](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/402a2a50ba224d1f8160b741feac19d4~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=362&h=461&s=16016&e=png&b=2b2b2b)

### goctl api go

根据api文件生成Go HTTP代码。-api表示api文件路径，-dir表示代码输出目录，--style表示输出文件和目录的命名风格格式化符号。

详情见 [文件风格](https://link.juejin.cn/?target=https%3A%2F%2Fgo-zero.dev%2Fdocs%2Ftutorials%2Fcli%2Fstyle)

--home表示自定义模板文件目录（自定义模板我们会在后续进行讲解，别忘了关注我）

- 修改demo.api文件内容，增加一个post接口

```go
type PostDemoReq {
	Message string `json:"message"`
}

type PostDemoResp {
	Message string `json:"message"`
}

service demo-api {
	@handler PostDemoHandler
	post /postDemo(PostDemoReq) returns (PostDemoResp)
}
```

- 重新生成代码

```sh
cd demo
goctl api go -api demo.api -dir . -style gozero
```

会生成这两个文件

![image-20240324192948952](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0900ef65efc34c9f9c5dc35a3aac8684~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=372&h=375&s=17905&e=png&b=3d4042)

- 修改logic逻辑

```go
func (l *PostDemoLogic) PostDemo(req *types.PostDemoReq) (resp *types.PostDemoResp, err error) {
	// todo: add your logic here and delete this line
	return &types.PostDemoResp{
		Message: req.Message,
	}, nil
}
```

- 重新启动服务

```sh
go run demo.go -f etc/demo-api.yaml
```

- 使用ApiFox发起请求

![image-20240324193701720](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ca1837814f5f45dabb928d71d8d1f875~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=1135&h=491&s=28930&e=png&b=252525)

至此我们已经学会怎么创建单体服务，并创建get和post接口啦，已经能应对大多数单体项目的开发需求。

当然我们做接口测试的时候一个个手动输入到ApiFox十分麻烦，这时候我们可以借助goctl api的插件生成swagger导入到ApiFox当中。

### 生成swagger文档

[goctl-swagger](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fzeromicro%2Fgoctl-swagger) 用于一键生成 `api` 的 `swagger` 文档

安装goctl-swagger

#### 1. 编译goctl-swagger插件

```sh
GOPROXY=https://goproxy.cn/,direct go install github.com/zeromicro/goctl-swagger@latest
```

#### 2. 配置环境

将$GOPATH/bin中的goctl-swagger添加到环境变量

使用goctl-swagger生成swagger.json

```sh
goctl api plugin -plugin goctl-swagger="swagger -filename demo.json" -api demo.api -dir . 
```

生成如下文档

![image-20240324194904920](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a4acd06bd184446c8f7deba6d27edfcd~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=1416&h=819&s=35072&e=png&b=2e3034)

#### 3.导入ApiFox

![image-20240324195049182](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4dc4955cc6124f56a156282491aeadab~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=2335&h=1287&s=159052&e=png&b=282828)

#### 4.导入demo.json

![image-20240324195209728](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9e7c516f415644d4aed3bef1d7b10c9b~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=2335&h=1307&s=164477&e=png&b=272727)

![image-20240324195245159](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d5984e3e50bf4cb485729a6179391be0~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=514&h=133&s=8426&e=png&b=2e2e2e)

#### 5.配置开发环境路由前缀

![image-20240324195511502](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ac5bec3dc3714562996dceeaccdc04d0~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=2560&h=1307&s=162099&e=png&b=272727)

![image-20240324195600505](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/95d0bce626124fabb630dc01035074c0~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=2560&h=1307&s=158965&e=png&b=1c1c1c)

#### 6.进行接口测试

![image-20240324195701608](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a85456e0f75748fca44110bef5534eff~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=2560&h=1307&s=162735&e=png&b=282828)

## goctl rpc

goctl rpc 是 goctl 中的核心模块之一，其可以通过 .proto 文件一键快速生成一个 rpc 服务，如果仅仅是启动一个 go-zero 的 rpc 演示项目， 你甚至都不用编码，就可以完成一个 rpc 服务开发及正常运行。

### goctl rpc new

- 快速生成一个 rpc 服务，其接收一个终端参数来指定服务名称。

```sh
goctl rpc new RPCDemo
```

- 生成项目目录结构如下图所示

![image-20240324203006437](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9d5356107ca94e919393f84e709130bf~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=256&h=245&s=7068&e=png&b=3c3f41)

### goctl rpc -o

用于快速生成一个 proto 模板文件，其接收一个 proto 文件名称参数。

```sh
goctl rpc -o demoProto.proto
```

- 会在相同目录下生成proto模板文件

![image-20240324210023407](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5552299feefe40e7a33f70f6498daba5~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=1920&h=1032&s=131296&e=png&b=2b2b2b)

### goctl rpc protoc

- 根据 protobufer 文件生成 rpc 服务。

```sh
cd .\RPCDemo
goctl rpc protoc RPCDemo.proto --go_out=./ --go-grpc_out=./  --zrpc_out=./ --style=goZero
```

![image-20240324203249132](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3497fd0b6fc543f98e9c8e404370ae12~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=426&h=421&s=19486&e=png&b=3e4143)

- 由于没安装Etcd，因此我们把etc/rpcdemo.yaml的Etcd相关配置注释掉

![image-20240324204417311](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c1ce801feb19442f9bd9fc59ae719e45~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=1920&h=1032&s=120616&e=png&b=2b2b2b)

- 在logic/pingLogic.go修改为如下逻辑

```go
func (l *PingLogic) Ping(in *rPCDemo.Request) (*rPCDemo.Response, error) {
	// todo: add your logic here and delete this line

	return &rPCDemo.Response{
		Pong: in.Ping,
	}, nil
}
```

- 运行服务

```sh
cd .\RPCDemo
go run .
```

- 项目默认跑在8080端口，可根据需求修改

![image-20240324204543886](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/986438ce4b834608a166179f5c11427d~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=1429&h=137&s=40651&e=png&b=2b2b2b)

将proto导入ApiFox进行测试

- 新建项目

  ![image-20240324204644891](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/472997e61ee24c9b899b02ae26c6067c~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=2560&h=1308&s=73991&e=png&b=262626)

  - 新建gRPC项目

  ![image-20240324204726142](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a07a1b03e69b47339de589e49dc6e88c~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=2560&h=1376&s=115740&e=png&b=151515)

- 添加项目中的RPCDemo.proto

  ![image-20240324204920114](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/25eeb33797304bc0b964a8656a21801f~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=2560&h=1308&s=71662&e=png&b=121212)

- 右上角选择环境

  ![image-20240324205022648](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f0464e43e6b34c4abd9fe7fa1026f4f6~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=2560&h=1308&s=100403&e=png&b=272727)

  ![image-20240324205123891](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/82ffd0a83da84136a5abb51d7d973ce6~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=2560&h=1308&s=111204&e=png&b=1c1c1c)

- 测试接口

  ![image-20240324205233980](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f1a09c9241514727a75d594c5b4aac44~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=2560&h=1308&s=104838&e=png&b=272727)

  至此，我们已经能够根据proto文件生成gRPC接口并进行测试啦。

## goctl model

goctl model 为 goctl 提供的数据库模型代码生成指令，目前支持 MySQL、PostgreSQL、Mongo 的代码生成，MySQL 支持从 sql 文件和数据库连接两种方式生成，PostgreSQL 仅支持从数据库连接生成。

本文主要以MySQL为数据表来源生成代码，其他数据库类似。

- 创建genModel文件夹

![image-20240324211045538](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/32c2c7529f3f4f90884739bf176fc4ca~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=422&h=73&s=3996&e=png&b=3c3f41)

- 生成代码。对应位置的MySQL连接参数改为你的即可。-table表示你的表名，-dir表示生成代码的输出目录 -cache表示是否生成带缓存的代码

```sh
goctl model mysql datasource -url="root:PXDN93VRKUm8TeE7@tcp(127.0.0.1:33069)/lottery" -table="lottery" -dir="./genModel" -cache=true --style=goZero
```

- 显示Done.说明成功。

![image-20240324211227428](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d7756d087a4a46659e41b3647cd63d51~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=1527&h=45&s=10689&e=png&b=2b2b2b)

![image-20240324211454772](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/15cde1d4789b4e37b6eda7d8d980ce61~tplv-k3u1fbpfcp-jj-mark:3024:0:0:0:q75.awebp#?w=1920&h=1032&s=232239&e=png&b=2c2c2c)

至此我们成功使用goctl model根据数据库中的数据表生成了model 代码。

后续通过修改模板代码我们可以实现生成代码的定制化需求，记得关注我！

# 总结

这篇文章相比惜字如金的[官方文档](https://link.juejin.cn/?target=go-zero.dev)，详细介绍了如何使用Go-Zero的goctl工具进行api服务、rpc服务和model层代码的生成，并提供了Demo进行实际操作。

我将继续更新Go-Zero系列文章，**如果你对Go语言或者微服务感兴趣，欢迎关注我的掘金账号，也欢迎在掘金私信我。**
