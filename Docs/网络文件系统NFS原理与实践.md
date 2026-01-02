# 网络文件系统NFS原理与实践

> 本文转载自：公众号【民工哥技术之路】的文章--[玩转企业常见应用与服务系列（一）：网络文件系统 NFS 原理与实践](https://mp.weixin.qq.com/s?__biz=MzI0MDQ4MTM5NQ==&mid=2247546196&idx=2&sn=eac4b43fe3b8c438c876ec6bc1437645&chksm=e9185048de6fd95eb5f864cd742de9d5f53b5e0aaa273b1cf8d0d170a81b0f5efd5afc2e932e&scene=21#wechat_redirect)

## NFS 概述

NFS是一种基于TCP/IP 传输的网络文件系统协议。通过使用NFS协议，客户机可以像访问本地目录一样访问远程服务器中的共享资源。

NAS存储: NFS服务的实现依赖于RPC (Remote Process Call，远端过程调用)机制，以完成远程到本地的映射过程。

## NFS的文件传输

NFS这个服务器的端口开在2049,但由于文件系统非常复杂。因此NFS还有其他的程序去启动额外的端口，这些额外的用来传输数据的端口是随机选择的，是小于1024的端口；既然是随机的那么客户端又是如何知道NFS服务器端到底使用的是哪个端口呢？这时就需要通过远程过程调用（Remote Procedure Call,RPC）协议来实现了RPC服务（portmap 或rpcbind服务）。

#### 什么是RPC服务？

RPC（Remote Procedure Call）即远程过程调用。RPC 最主要的功能就是在指定每个 NFS 功能所对应的 port number ，并且回报给客户端，让客户端可以连结到正确的port上去。

## NFS工作原理

NFS服务器可以让PC将网络中的NFS服务器共享的目录挂载到本地端的文件系统中，而在本地端的系统中来看，那个远程主机的目录就好像是自己的一个磁盘分区一样，在使用上相当便利。

#### NFS工作流程

![图片](https://bu.dusays.com/2026/01/02/6957851b8b750.png)

- 1.首先服务器端启动RPC服务，并开启111端口
- 2.服务器端启动NFS服务，并向RPC注册端口信息
- 3.客户端启动RPC（portmap服务），向服务端的RPC(portmap)服务请求服务端的NFS端口
- 4.服务端的RPC(portmap)服务反馈NFS端口信息给客户端。
- 5.客户端通过获取的NFS端口来建立和服务端的NFS连接并进行数据的传输。

#### 挂载原理

![图片](https://bu.dusays.com/2026/01/02/695785345f400.webp)当我们在NFS服务器设置好一个共享目录/opt后，其他的有权访问NFS服务器的NFS客户端就可以将这个目录挂载到自己文件系统的某个挂载点，这个挂载点可以自己定义，如上图客户端A与客户端B挂载的目录就不相同。并且挂载好后我们在本地能够看到服务端/opt的所有数据。

三种挂载方式：

- 手动挂载（临时挂载）
- 开机自动挂载
- autofs挂载（自动挂载）

## NFS的优缺点

#### 优点

- a.节省本地存储空间将常用的数据存放在一台服务器可以通过网络访问。
- b.简单容易上手。
- c.方便部署非常快速，维护十分简单。

#### 缺点

- a.局限性容易发生单点故障，及server机宕机了所有客户端都不能访问。
- b.在高并发下NFS效率/性能有限。
- c.客户端没用用户认证机制，且数据是通过明文传送，安全性一般（一般建议在局域网内使用）。
- d.NFS的数据是明文的，对数据完整性不做验证。
- e.多台机器挂载NFS服务器时，连接管理维护麻烦。

## NFS 安装部署

在Centos 7系统中，需要安装nfs-utils、rpcbind 软件包来提供NFS共享服务，前者用于NFS共享发布和访问，后者用于RPC支持。手动加载NFS共享服务时，应该先启动rpcbind，再启动nfs。

- nfs端口：2049
- RPC端口：111

#### 安装nfs和rpcbind软件

```bash
rpm -q rpcbind nfs-utils     #查询是否安装
yum install -y nfs-utils rpcbind   #安装nfs和rpc的软件包
```

#### 设置共享目录

NFS 的配置文件为 /etc/exports ,文件内容默认为空（无任何共享）。在exports 文件中设置共享资源时，记录格式为“目录位置 客户机地址（权限选项）。

例如，将文件夹 /opt/web 共享给 192.168.43.0/24网段使用，允许读写操作，配置如下:

```bash
mkdir /opt/web
vim /etc/exports
/opt/web 192.168.43.0/24(rw,sync,no_root_squash)
```

**常用参数说明**

```bash
rw #允许读写
ro #只读
sync #同步写入
async #先写入缓冲区，必要时才写入磁盘，速度快，但会丢数据
subtree_check #若输出一个子目录，则nfs服务将检查其父目录权限
no_subtree_check #若输出一个字目录，不检查父目录，提高效率
no_root_squash #客户端以root登录时，赋予其本地root权限
oot_squash #客户端以root登录时，将其映射为匿名用户
all_squash #将所有用户映射为匿名用户
```

#### 启动 NFS服务并验证结果

需要先启动rpc服务，因为nfs要向rpc注册端口。

```bash
systemctl start nfs      #开启nfs服务
systemctl start rpcbind   #开启rpcbind服务
systemctl enable nfs     #开机自启nfs服务
systemctl enable rpcbind   #开机自启rpcbind服务

netstat -anpu | grep rpc  #过滤出rpc所有UDP连接信息

rpcinfo -p localhost   #查看nfs向rpc注册的端口信息

exportfs -v     #验证结果
exportfs -r     #刷新结果

showmount -e localhost   #验证共享
```

#### 客户机中访问 NFS 共享资源

```bash
yum install -y nfs-utils rpcbind
showmount -e 192.168.43.20   #客户端验证共享
mount -t nfs 192.168.43.20:/opt/web /var/www/html   #将共享目录挂载到网页目录
# 
yum install -y httpd
systemctl start httpd   #启动web服务
curl 127.0.0.1   #访问主页内容
echo "到此一游" >> /var/www/html/index.html    #客户端修改主页文件（服务端会同步）
```

- NFS 协议的目标是提供一种网络文件系统，因此对 NFS 共享的访问也使用 mount 命令来进行挂载，对应的文件系统类型为 nfs 。

## 实践拓展练习

> 准备工作：
>
> 服务器：虚拟机CentOS7  IP:192.168.43.130  // 已安装nfs-utils rpcbind
>
> 客户机1：虚拟机CentOS7  IP:192.168.43.20  // 已安装nfs-utils rpcbind
>
> 客户机2：宿主机Windows10  IP:192.168.58.147  // 开启Windows功能中的NFS服务（NFS客户端）

#### 配置共享目录

在`192.168.43.130`服务端创建` NFS `共享目录：

```bash
mkdir /opt/test  //作为测试文件
vim /etc/exports
/opt/test 192.168.43.0/24(rw,sync,no_root_squash) //写入读写权限

systemctl restart nfs      #重启nfs服务

exportfs -v     #验证结果
exportfs -r     #刷新结果

showmount -e localhost   #验证共享
```

#### 挂载共享目录

在`192.168.43.20`客户机中挂载` NFS `共享目录：

```bash
showmount -e 192.168.43.130   #客户端验证共享
mount -t nfs 192.168.43.130:/opt/test /opt/share   #将共享目录挂载到客户端目录
```

在`Windows`客户端挂载` NFS `共享目录，共有三种方式：

1. 在此电脑中右键**添加一个网络位置**，输入服务端地址：`\\192.168.43.130\opt\test`即可挂载；
2. 在此电脑中点击**映射网络驱动器**，输入服务端地址：`\\192.168.43.130\opt\test`即可挂载；
3. 以管理员方式打开`CMD`，使用`mount`命令挂载，挂载命令为：`mount -o 192.168.43.130:/opt/test Y:`，挂载后效果与第二种方法一样。

<span style="color:#CC0000; font-size:1.1em;">`Windows`客户端挂载` NFS `共享目录后会遇到文件只要只读权限，要想获取所有权限，需进行如下操作：</span>

在服务端进一步放开目录访问权限，并非必须步骤，但以防万一，还是配置一下：

```
/opt/test *(rw,sync,no_root_squash,no_all_squash) # *号表示所有客户端都能访问
```

这一步是` Windows `突破只读的唯一关键，严格按步骤操作.

1. 按` Win+R `输入` regedit`，打开注册表编辑器，定位到`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\ClientForNFS\CurrentVersion\Default`,

2. 右键空白处 → 新建 →` DWORD (32 位)` 值，创建(修改)以 3个值（确保数值为十进制）：

|       数值名称       | 数值数据 |                            作用                            |
| :------------------: | :------: | :--------------------------------------------------------: |
|     AnonymousUid     |    0     |     将 Windows 匿名 NFS 用户映射为 Linux root（UID 0）     |
|     AnonymousGid     |    0     |      将 Windows 匿名 NFS 组映射为 Linux root（GID 0）      |
| EnableUnmappedAccess |    1     | 允许未映射的用户（如 root）直接访问 NFS 共享（关键兼容项） |

3. 以管理员方式打开`CMD`，输入`net stop nfsclnt && net start nfsclnt`，重启` NFS `客户端服务，使注册表生效。









