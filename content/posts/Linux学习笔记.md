+++
title = 'Linux学习笔记'
date = 2024-10-02T16:26:10+08:00
categories =  ["技术文档","学习笔记"] 
tags = ["技术文档","学习笔记","Linux"]

+++

# Linux 基本命令

根目录从  /  开始，后面的表示层级关系

命令通用格式

~~~c
command [-options] [parameter]
~~~

- command ：命令本身
- -options ：可选，命令的一些选项，可以通过选项控制命令的行为细节
- parameter ：可选，命令的参数，多用于命令的指向目标

## ls命令

~~~c
ls [-a -l -h] [Linux路径]
~~~

- -a ： 显示所有文件，包括隐藏文件，在文件名前面会有一个  .  来表示这是一个隐藏文件
- -l ： 用列表显示文件可以显示更详细的内容
- -h： 不可以单独使用通常和 -l 一起使用，显示文件大小

这些命令可以组合在一起使用

## cd - pwd命令

~~~Linux
无需选项，只有参数，表示切换到这个目录下
直接执行，不写参数，表示回到用户的HOME目录
cd [Linux路径]
~~~

pwd 打印当前所在的目录

## 相对路径和绝对路径

- ```.```表示当前目录
- ```..``` 表示上一级目录
- ```~``` 表示HOME目录

## 创建目录命令 mkdir

~~~Linux
mkdir [-p] Linux路径
~~~

-p ：可选参数，表示自动创建不存在的父目录

## 文件操作命令（touch、cat、more、mv、rm）

~~~Linux
//创建文件命令
touch Linux路径

//查看文件内容，显示所有的
cat Linnux路径

//查看文件，按页显示文件，按空格键显示下一页 按q键推出
more Linux路径
~~~

**复制文件夹命令**

~~~Linux
 cp [-r] 参数1 参数2
~~~

- 参数1 ：被复制文件地址
- 参数2 ：目的地址
- -r ：用于复制文件夹使用，表示递归

**移动文件命令**

~~~Linux
mv 参数1  参数2
~~~

- 参数1 ：被移动文件地址
- 参数2 ：目的地址
- 当目的不存在，会创建

**删除文件命令**

~~~Linux
rm [-r -f] 参数1，参数2 ………… 参数n
~~~

- -r ：同cp命令一样，用于删除文件夹
- -f ：强制删除

> rm命令支持通配符```*``` ，用来做模糊匹配
>
> ```*``` 表示匹配任何内容，包含空

## 查找命令 which、find

~~~Linux
//查找一系列命令的程序文件存放在哪里
which 要查找的命令
~~~

**find命令——按文件名查找**

~~~Linux
find 起始路径 -name "被查找文件名"
~~~

> 被查找文件名支持通配符```*```

**find命令——按文件大小查找文件**

~~~Linux
find 起始路径 -size +|-n[kMG]

//示例 查找小于10kb的文件
find / -size -10k
~~~

- +、- ： 表示大于和小于
- n ： 表示数字
- kMG ： 表示大小单位

## grep-wc-管道符

**grep命令**

~~~LInux
//从文件中通过关键字过滤文件行
greo [-n] 关键字 文件路径
~~~

- -n ： 可选，表示在结果中显示匹配的行的行号
- 关键字：必填项，表示过滤的关键字，如果有空格用双引号括起来
- 文件路径：必填项，表示过滤内容的文件内容，可作为内容输入端口

**wc命令**

~~~Linux
//统计文件的行数、单词数量等
wc [-c -m -l -w] 文件路径
~~~

- -c ：统计bytes数量
- -m：统计字符数量
- -l ：统计行数
- -w：统计单词数量
- 文件路径： 被统计的文件

**管道符  

含义：将管道符左边命令的结果作为右边命令的输入

## echo-tail-重定向符

**echo**

~~~Linux
echo 输出内容
~~~

**反引号 `** 

被 `` 包裹的内容将作为命令来执行

**重定向符 >和>>**

- ```>``` 将左侧命令的结果，覆盖写入到符号右侧指定的文件中
- ```>>``` 将左侧命令的结果，追加写入到符号右侧指定的文件中

**tail命令**

~~~Linux
tail [-f -num] Linux路径
~~~

- -f ：表示持续跟踪尾部更改
- -num ： 表示查看尾部多少行，默认10行

## root用户

管理员用户

### su和exit命令

~~~Linux
//切换用户
su [-] [用户名]
// - 表示切换用户后加载环境变量

//退回上一个用户
exit
~~~

### sudo命令

用来临时用root身份执行命令

~~~Linux
sudo 其他命令
~~~

- 在其他命令之前加上sudo，就可以将这条命令赋予roo授权
- 需要为普通用户配置sudo认证才有权利使用sudo

配置步骤：

~~~Linux
1、切换到root用户，执行visudo命令，会自动通过vi编辑器打开/etc/sudoers

2、在文件的最后添加
用户名 ALL=(ALL)	NOPASSWD:ALL

3、通过wq保存 

4、撤销权限只需要将添加的内容删除即可
~~~

# 用户和用户租

> Linux系统中可以：
>
> - 配置多个用户
> - 配置多个用户组
> - 用户可以加入多个用户组中
>
> 权限管控的两个级别
>
> - 针对用户的权限控制
> - 针对用户组的权限控制

## 用户组管理

需要root用户执行

~~~Linux
//创建用户组
groupadd 用户组名

//删除用户组
groupdel 用户组名
~~~

## 用户管理

### 创建用户

~~~Linux
useradd [-g -d] 用户名
~~~

- -g ： 指定用户的组，不指定-g会创建同名组，并自动加入，如果存在同名组则必须使用-g
- -d ： 指定用户的HOME路径，不指定，HOME路径默认在：/home/用户名

### 删除用户

~~~Linux
userdel [-r] 用户名
~~~

- -r ： 删除用户的HOME目录，不适用-r，该用户的HOME目录将保留

### 查看用户所属组

~~~Linux
id [用户名]
~~~

- 用户名 ： 被查看的用户，不指定为当前用户

### 修改用户属组

~~~Linux
usermod -aG 用户组 用户名
~~~

## getent命令

~~~Linux
//查看当前系统之有哪些用户
getent passwd

//查看当前系统中有哪些组
getent group
~~~

> 查询用户返回7份信息：
>
> 用户名:密码(x):用户ID:组ID:描述信息(无用):HOME目录:执行终端(默认bash)
>
> 查询组返回3份信息
>
> 组名称:组认证(显示为x):组ID

# 权限控制

## 查看权限控制信息

ls -l 返回的权限信息格式：

权限细节  硬链接数  所属用户  所属用户组

## 权限细节

一共10个槽位，第1个表示这是一个文件还是文件夹，-表示文件，d表示文件夹，l表示软链接，第2-4槽位表示所属用户权限，5-7表示所属用户组权限，8-10表示所属用户组权限。

## 权限代码

- r ：读权限
- w ： 写权限
- x ： 执行权限，针对文件夹表示是否可以cd到这个工作目录下

## chmod 命令

**修改权限命令**

只有文件、文件夹的所属用户和root用户可以修改

~~~Linux
chmod [-R] 权限 文件或文件夹

//示例，u表示所属用户，g表示所属用户组，o表示其他用户
chmod u=rwx,g=rwx,o=rwx test.txt

//简写 r=4,w=2,x=1,用相应权限数字加起来的数字表示拥有的权限
chmod 751 test.txt
~~~

- -R ： 对文件夹中的全部内容应用同样的操作

## chown命令

**修改所属用户或用户组**

> 普通用户无法修改所属为其他用户或组，所以此命令只适用于root用户执行

~~~Linux
chown [-R] [用户][:][用户组] 问价或文件夹
~~~

# 各类小技巧快捷键

| 功能                                                   | 快捷键   |
| :----------------------------------------------------- | :------- |
| 强制退出                                               | ctrl + c |
| 退出或登出                                             | ctrl + d |
| 历史命令搜索                                           | history  |
| 历史命令匹配(向上搜索第一个匹配的命令，会直接执行)     | ！命令   |
| 历史命令搜索(向上搜索第一个匹配的命令，会显示完整命令) | ctrl + r |

### 光标移动

| 动作           | 快捷键    |
| -------------- | --------- |
| 跳到命令开头   | ctrl + a  |
| 跳到命令结尾   | ctrl + e  |
| 向左跳一个单词 | ctrl + -> |
| 向右跳一个单词 | ctrl + <- |

# 软件安装

## CentOS安装

通过yum命令。yum：RPM包软件管理器，用于自动化安装配置Linux软件，并可以自动解决依赖问题

> 该命令需要root权限，并联网

~~~Linux
yum [-y] [install | remove | search] 软件名称
~~~

- -y：自动确认，无需手动确认安装或卸载的过程
- install：安装
- remove：卸载
- search：搜索

## Ubuntu安装

> 使用的是apt包管理器，同样需要root权限并联网

~~~Linux
apt [-y] [install | remove | search] 软件名称
~~~

- -y：自动确认，无需手动确认安装或卸载的过程
- install：安装
- remove：卸载
- search：搜索

## systemctl命令

服务控制命令

只有软件集成到这个systemctl中才能使用这个命令

~~~Linux
systemctl start | stop | tatus | enable | disable 服务名
~~~

- start ： 启动
- stop：关闭
- status：查看状态
- enable：开启开机自启
- disable：关闭开机自启

## 软连接

将文件或文件夹链接到其他位置，类似快捷方式

~~~Linux
ln -s 参数1~参数2
~~~

- -s ：创建软连接
- 参数1 ：被链接文件
- 参数2：要链接去的目的

# 日期和时区

~~~Linux
date [-d] [+格式化字符串]

//显示年月日，如果有空格使用双引号包裹
day +%Y-%M-%d

//增加一天时间
day -d "-1 day" +%Y-%M-%d
~~~

- -d：按照给定格式显示日期，一般用于日期计算

**支持的时间标记**

- year 年
- month 月
- day 天
- hour 小时
- minute 分钟
- second 秒

**日期格式化字符串**

- %Y：年
- %y：年的后两位数字
- %M：月
- %d：日
- %H：小时
- %M：分钟
- %S：秒
- %s：自1970-01-01到现在的秒数

## 修改Linux时区

~~~Linux
//删除时区文件
rm -f /etc/localtime
//创建软连接
sudo ln -s /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
~~~

### 自动校准时间 ntp 程序

安装```yum -y install ntp```

启动```systemctl start ntpd```

设置开机启动 ```systemctl enable ntpd```

### 手动校准

~~~Linux
ntpdate -u ntp服务器地址
~~~

# IP地址和主机名

*如果无法使用 ifconfig 命令，安装 net-toole*

## 主机名

### 查看主机名

~~~Linux
hostname
~~~

### 修改主机名

~~~Linux
//重新登陆就可以看到主机名已经更改
hostnamectl set-hostname newnname
~~~

## 配置固定IP地址

~~~Linux
1、打开文件
vim /etc/sysconfig/network-scripts/ifcfg-ens33

2、修改BOOTPROTO参数为static

3、添加如下内容
IPADDR="固定ip地址"
NETMASK="子网掩码"
GATEWAY="网关地址"
DNS1="dns地址，可以设置成网关地址"

4、重新启动网卡
systemctl restart network
~~~

# 网络请求和下载

## ping命令

检查网络是否可联通状态

~~~Linux
ping [-c num] ip或主机名
~~~

- -c：检查的次数

## wget命令

非交互式的文件下载器，可以在命令行内下载网络文件

~~~Linux
wget [-b] url
~~~

- -b：可选，后台下载，会将日志写入到当前工作目录的wget-log文件

## curl命令

发送http网络请求，可用于：下载文件、获取信息等

~~~Linux
curl [-O] url
~~~

- -O：用于下载文件，当url是下载链接时，可以使用这个选项保存文件

*cip.cc返回当前主机 ip 地址*

# 端口

Linux系统可以支持 65535 个端口，这些端口分为 3 类

- 公认端口：1~1023，系统内置或知名程序的预留使用，非特殊需要，不占用这个端口
- 注册端口：1024~49151，随意使用，用于松散的绑定一些程序、服务
- 动态端口：49152~65535，不固定绑定程序，而是当程序对外进行网络连接时，用于临时使用

## 查看端口占用

### nmap 命令

**查看指定ip地址的端口占用情况**

~~~Linux
//需要先安装 nmap
nmap 被查看的ip地址
~~~

### netstat 命令

**查看本机指定端口占用情况**

~~~Linux
//需要安装 net-tools
netstat -anp | grep 端口号
~~~

# 进程管理

## 查看进程

~~~Linux
ps [-e -f]
~~~

- -e：显示全部进程
- -f：以完全格式化的形式展示出信息

*可以配合管道符查看指定的进程*

从左到右显示的信息分别是

| 标题  | 描述                                           |
| ----- | ---------------------------------------------- |
| UID   | 进程所属的用户ID                               |
| PID   | 进程号                                         |
| C     | CPU占用率                                      |
| STIME | 启动时间                                       |
| TTY   | 启动此进程的终端序号，如显示？，表示非终端启动 |
| TIME  | 占用CPU时间                                    |
| CMD   | 进程对应的名称或启动路径或启动命令             |

## 关闭进程

~~~Linux
kill [-9] 进程ID
~~~

- -9：强制关闭

# 主机状态

## 系统资源监控 top 命令

### 显示内容介绍

- 第一行：``` 命令名称 - 当前系统时间，启动了多长时间，登录用户数量，系统在1分钟，5分钟，15分钟的平均负载```
- 第二行：``` 当前进程数，在运行的进程，睡眠的进程，停止的进程，僵尸进程```
- 第三行： ``` cpu使用率，us:用户cpu使用率，sy:系统cpu使用率，ni:高优先级进程占用CPU时间百分比，id:空闲cpu率，wa:IO等待cpu占用率，hi:cpu硬件中断率，si:cpu软件中断率，st:强制等待占用cpu率``` 
- 第四、五行：``` Kib Mem:物理内存，KibSwap：虚拟内存，total总量，free空闲，used使用，buff/cachebuff和cache占用，```

#### 后面表格中的内容

| 字段    | 描述                                              |
| ------- | ------------------------------------------------- |
| PID     | 进程id                                            |
| USER    | 进程所属用户                                      |
| PR      | 进程优先级，越小越高                              |
| NI      | 负值表示高优先级，正表示低优先级                  |
| VIRT    | 进程使用虚拟内存，单位KB                          |
| RES     | 进程使用物理内存，单位KB                          |
| SHR     | 进程使用共享内存，单位KB                          |
| S       | 进程状态(S休眠，R运行，Z僵死，N负数优先级，I空闲) |
| %CPU    | cpu占用率                                         |
| %MEM    | 内存占用率                                        |
| TIME+   | CPU使用时间，单位10毫秒                           |
| COMMAND | 进程命令或名称或程序文件路径                      |

### top命令支持的选项

- -p：只显示某个进程信息
- -d：设置刷新时间，默认是5s
- -c：显示产进程的完整命令，默认是进程名
- -n：指定刷新次数
- -b：以非交互全屏模式运行，
- -i：不显示任何闲置idle或无用zombie的进程
- -u：查找特定用户启动的进程

### top交互式选项

以非-b方式启动，就可以产生交互，通过按下相应的键，产生不一样的功能

| 按键 | 功能                           |
| ---- | ------------------------------ |
| h    | 显示帮助画面                   |
| c    | 显示产生进程的完整命令         |
| f    | 可以选择需要展示的项目         |
| M    | 按照驻留内存大小排序           |
| P    | 根据cpu使用百分比大小排序      |
| T    | 根据时间/累计时间排序          |
| E    | 切换顶部内存显示单位           |
| e    | 切换内存显示单位               |
| l    | 切换显示平均负载和启动时间信息 |
| i    | 不显示限制或无用的进程等同于-i |
| t    | 显示cpu状态信息                |
| m    | 切换显示内存信息               |

## 磁盘信息监控

### df命令

查看硬盘的使用情况

~~~Linux
df [-h]
~~~

- -h：以更加人性化的单位显示

### iostat命令

查看cpu、磁盘的相关信息

~~~Linux
iostat [-x][num1][num2]
~~~

- -x：显示更多信息
- num1：刷新间隔，num2：刷新几次

> rrqm/s：每秒这个设备相关的读取请求有多少被merge
>
> rKB/s：每秒发送到设备的读取请求数
>
> wkb/s：每秒发送到设备的写入请求数
>
> %util：磁盘利用率

## 网络监控

sar命令

~~~Linux
sar -n DEV num1 num2
~~~

- -n：查看网络，DEV表示查看网络接口
- num1：刷新间隔，num2：刷新次数

# 环境变量

环境变量是操作系统在运行的时候记录的一些关键性信息，用以辅助系统运行

## 查看环境变量

env命令

~~~Linux
env
~~~

*环境变量是一种 KeyValue 型数据结构*

环境变量**PATH**：记录了系统执行任何命令的搜索路径，用冒号隔开，当执行命令时，会按照顺序从路径中搜索要执行的程序的本体

**$**符号：用于取“变量”的值，通过语法 $环境变量名来获取，当和其他内容混合在一起的时候，通过{}将变量名包裹起来

## 设置环境变量

临时设置：

~~~Linux
 export 变量名 = 变量值
~~~

永久生效：

- 针对当前用户生效，配置在当前用户的 ~/.bashrc 文件中
- 针对所有用户生效，配置在系统的 ~/etc/.profile 文件中
- 通过语法source 配置文件，进行立即生效，或者重新登录

环境变量配置过程

~~~Linux
//配置环境变量针对当前用户生效
vi ~/bashrc

//添加配置信息
export pathName=Value

//立即生效
source .bashrc
~~~

## 自定义环境变量PATH

~~~Linux
//在配置文件中添加
export PATH=$PATH:/程序目录

//立即生效
source 配置文件目录
~~~

# 上传和下载

通过鼠标拖拽实现

rz、sz命令

*需要有对源文件和目的地的读写权限*

# 压缩和解压

常见的压缩格式

1. zip：Linux、win、Mac常用
2. 7zip：win常用
3. rar：win常用
4. tar：linux、Mac常用
5. gzip：linux、Mac常用

## tar命令

> - .tar：称为tarball，归档文件，简单的将文件组装到一个.tar的文件内，没有太多文件体积的减少，只是简单的封装
> - .gz：常见为.tar.gz、gzip格式压缩，使用gzip压缩算法将文件压缩到一个文件内，可以极大的减少压缩后的体积

~~~Linux
tar [-c -v -x -f -z -C] 参数1 参数2 …… 参数n
~~~

- -c：创建压缩文件，用于压缩模式
- -v：显示压缩，解压过程，用于查看进度
- -x：解压模式
- -f：要创建的文件，或解压的文件
- -z：gzip模式，不适用-z就是普通的 tarball 格式
- -C：选择解压的目的地，用于解压模式

### 参数使用注意事项

> -z选项在使用时，一般处于选项位第一个
>
> -f选项在使用时，**必须**在选项位最后一个
>
> -C选项在使用时，和解压所需的其他参数分开

### 常用的 tar 解压组合

~~~Linux
//解压test.tar，将文件解压到当前目录
tar -xvf test.tar

//解压test.tar，将文件解压到指定目录(/home/admin)
tar -xvf test.tar -C /home/admin

//以Gzip模式解压test.tar.gz，将文件解压到指定目录
tar -zxvf test.tar.gz -C /home/admin
~~~

## zip — unzip 命令

将文件压缩为zip格式

### 压缩文件 zip

~~~Linux
zip [-r] 参数1，参数2 …… 参数n
~~~

- -r：被压缩文件包含文件夹时，使用该选项
- 参数1：创建的压缩文件名
- 参数2~参数n：压缩的文件

### 解压文件 unzip

~~~Linux
unzip [-d] 参数1
~~~

- -d：指定解压之后存放位置
- 参数1：被解压的文件

*解压时同名内容会替换*

# MySQL安装

## 5.7版本

### 安装

1. 配置yum仓库

   ~~~Linux
   //更新密钥
   rpm --import https://repo.mysql.com/RPM-GPG-KEY-MYSQL-2022
   
   //安装Mysql yum库
   rpm -Uvh http://repo.mysql.com//mysql57-community-release-el7-7.noarch.rpm
   ~~~

2. 安装MySQL

   ~~~Linux
   yum install -y mysql -community-server
   ~~~

3. 设置开机启动

   ~~~Linux
   systemctl start mysqld
   systemctl enable mysqld
   ~~~

### 配置

主要配置管理员用户的密码以及允许远程登录的权限

1. 获取MySQL的初始密码

   ~~~Linux
   # 过滤 /var/log/mysqld.log 文件中的temporary password关键字，该文件是mysql安装运行过程中的日志文件
   cat /var/log/mysqld.log | grep "temporary password"
   
   ~~~

2. 登录MySQL数据库系统

   ~~~Linux
   执行
   mysql -uroot -p
   
   输入前面获取到的初始密码
   ~~~

3. 修改 root 用户密码

   ~~~Linux
   ALTER USER 'root'@'localhost' IDENTIFIED BY '密码';
   ~~~

   

   # Redis安装和部署

4. 配置 EPEL 仓库

   ~~~Linux
   yum install -y epel-release
   ~~~

5. 安装redis

   ~~~Linux
   yum install -y redis
   ~~~

6. 设置 redis 服务

   ~~~Linux
   //开机自启动
   systemctl enable redis
   //关闭开机自启动
   systemctl disable redis
   //启动
   systemctl start redis
   //关闭
   systemctl stop redis
   //查看状态
   systemctl status redis
   ~~~

7. 放行防火墙

   ~~~Linux
   //方式一，关闭防火墙
   systemctl stop firewalld
   systemctl disable firewalld
   
   //方式二，放行 redis 服务端口(6379)
   firemall-cmd --add-port=6379
   ~~~

   
