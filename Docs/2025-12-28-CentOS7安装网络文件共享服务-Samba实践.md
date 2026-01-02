---
title: CentOS7安装网络文件共享服务 Samba实践⌨️
date: 2025-12-28 18:00:00
updated: 2025-12-28 18:00:00
tag: [Linux,CentOS7,Samba]
categories: [知识库,Linux]
cover:
description: CentOS7安装网络文件共享服务 Samba实践⌨️
swiper_index: 
sticky: 
---

---

> 参考公众号【民工哥技术之路】文章[玩转企业常见应用与服务系列（五）：网络文件共享服务 Samba 原理与实践](http://mp.weixin.qq.com/s?__biz=MzI0MDQ4MTM5NQ==&mid=2247546382&idx=2&sn=54048ce83724cc61d9d990404b0e272c&chksm=e9185312de6fda047aa58a82ce58c463346a90d7bf7193bb072f7f4820e1530299552aa92a69&scene=21#wechat_redirect)

## SAMBA软件安装（服务器搭建）

```bash
yum -y install samba -y
```

查看相关软件包

```bash
rpm -qa | grep ^samba
samba-common-libs-4.10.16-19.el7_9.x86_64
samba-common-tools-4.10.16-19.el7_9.x86_64
samba-common-4.10.16-19.el7_9.noarch
samba-client-libs-4.10.16-19.el7_9.x86_64
samba-libs-4.10.16-19.el7_9.x86_64
samba-4.10.16-19.el7_9.x86_64
```

安装完成后通过命令：`vim /etc/samba/smb.conf`查看配置文件，具体配置解释如下：

```properties
[global] #全局选项
        workgroup = SAMBA #定义samba服务器所在的工作组
        security = user #认证模式：share匿名|user用户密码|server外部服务器用户密码
        
        passdb backend = tdbsam #密码格式
 
        load printers = yes #加载打印机
        cups options = raw #打印机选项

[homes] #局部选项（共享名称）
        comment = Home Directories #描述
        valid users = %S, %D%w%S #有效用户
        browseable = No #隐藏共享名称
        read only = No #是否只读
        inherit acls = Yes #继承ACL
        writable = yes      #可读可写

[printers] #共享名称
        comment = All Printers #描述
        path = /var/tmp #本地的共享目录
        printable = Yes #可打印
        guest ok = no ——>(等价于) public = no #需要帐号和密码访问
        writable = no  ——>(等价于)  read only =yes #不可写 
        browseable = No #隐藏

[print$]
        comment = Printer Drivers
        path = /var/lib/samba/drivers
        write list = @printadmin root
        force group = @printadmin
        create mask = 0664
        directory mask = 0775
```

## SAMBA使用案例

在已配置`静态ip`的服务端搭建一个SAMBA服务，共享一个目录`/samba/share`,客户端使用创建的测试用户`test`和密码`test`通过`Windows`或者`Linux`可以在该目录里创建文件删除文件。

#### 共享前的准备工作

##### 关闭防火墙和SELinux

关闭防火墙

```bash
systemctl stop firewalld
# 开机不自启
systemctl disable firewalld
```

关闭SELinux：

```bash
setenforce 0 
# 开机不自启  
vim /etc/selinux/config`
SELINUX=disabled
```

##### 在服务端创建一个共享目录并创建文件

```bash
mkdir -p /samba/share
cd /samba/share
# 创建一个测试文件
touch test.txt
# 在文件中输入hello world
echo "hello world" >> test.txt 
#查看文件属性
ll
-rw-r--r--. 1 root root 28 12月 18:00  test.txt
```

#### 有密码共享配置

执行`vim /etc/samba/smb.conf`修改配置文件，可参考如下：

```properties
# 全局配置
[global]
	workgroup = WORKGROUP
	security = user
	passdb backend = tdbsam
	printing = cups
	printcap name = cups
	load printers = yes
	cups options = raw
    server string = Samba Server Version %v
    netbios name = smbserver

# 家目录配置（可选，若不需要可注释）
[homes]
	comment = Home Directories
	valid users = %S, %D%w%S
	browseable = No
	read only = No
	inherit acls = Yes

[printers]
	comment = All Printers
	path = /var/tmp
	printable = Yes
	create mask = 0600
	browseable = No

[print$]
	comment = Printer Drivers
	path = /var/lib/samba/drivers
	write list = @printadmin root
	force group = @printadmin
	create mask = 0664
	directory mask = 0775

[samba_share]
    comment = samba service
    path = /samba/share
    guest ok = no
    writable = yes
    valid users = test
    create mask = 0775
    directory mask = 0775
    force user = root
    force group = root
    inherit permissions = yes
    browseable = yes
```

##### 创建用户

创建一个`test`用户，然后添加到samba认证中，密码设置为`123`

```bash
useradd test
smbpasswd -a test
New SMB password:   #密码设置为test
Retype new SMB password:
Added user test.
```

##### 启动nmb和smb服务

```
systemctl start nmb  
systemctl start smb
```

##### windows端挂载

在开启smb共享文件支持功能的Windows系统上，在此电脑内通过右键`添加一个网络位置`，输入`\\192.168.43.130\samba_share`后点击下一步，输入刚创建并加入到SAMBA数据库中的`用户名test）和``密码123`即可挂载共享目录。

挂载后如果没有读写权限须给用户添加写权限，或者用ACL单独给刚刚创建的`test`用户添加权限。

```bash
setfacl -m u:test:rwx /samba/share
```

##### Linux端挂载

`samb_share`参数是配置文件里标签名，先在Linux上安装SAMBA客户端 。

```bash
yum -y install samba-client
```

使用smbclient查看目录信息，命令：`smbclient //192.168.43.130/samba_share -U test`。然后，创建一个目录用来挂载：`mkdir /mnt/temp`，安装`cifs`：

```bash
yum install cifs-utils -y
```

挂载命令

```bash
mount.cifs -o user=test,pass=123 //192.168.43.130/samba_share /mnt/temp
```

#### 无密码共享配置

##### 步骤 1：修改 Samba 主配置文件

执行`vim /etc/samba/smb.conf`修改配置文件，可参考如下：

```properties
# 全局配置
[global]
    workgroup = WORKGROUP
    security = user
    map to guest = Bad User
    guest account = nobody
    passdb backend = tdbsam
    printing = cups
    printcap name = cups
    load printers = yes
    cups options = raw
    server string = Samba Server Version %v
    netbios name = smbserver

# 家目录配置（可选，若不需要可注释）
[homes]
    comment = Home Directories
    valid users = %S, %D%w%S
    browseable = No
    read only = No
    inherit acls = Yes

[printers]
    comment = All Printers
    path = /var/tmp
    printable = Yes
    create mask = 0600
    browseable = No

[print$]
    comment = Printer Drivers
    path = /var/lib/samba/drivers
    write list = @printadmin root
    force group = @printadmin
    create mask = 0664
    directory mask = 0775

# 无密码匿名共享核心配置
[samba_share]
    comment = Anonymous Samba Share (No Password)
    path = /samba/share
    guest ok = yes
    guest only = yes
    writable = yes
    browseable = yes
    create mask = 0777
    directory mask = 0777
    force user = nobody
    force group = nobody
    inherit permissions = yes
```

##### 步骤 2：创建匿名共享目录并设置权限

```bash
# 1. 创建共享目录（与配置中 path 一致）
mkdir -p /samba/share

# 2. 设置目录所属用户（与 guest account 一致）
chown -R nobody:nobody /samba/share

# 3. 开放全权限（匿名需读写执行，测试环境可用；生产可缩为 775）
chmod -R 777 /samba/share

# 4. 修复 SELinux 上下文（CentOS/RHEL 必需）
chcon -t samba_share_t /samba/share -R
```

##### 步骤 3：放行 SELinux 规则（关键）

SELinux 会拦截匿名写入，需执行以下命令放行：

```bash
# 临时关闭 SELinux 测试（立即生效）
setenforce 0

# 永久放行 Samba 匿名写入（重启生效，生产环境推荐）
setsebool -P samba_enable_home_dirs on
setsebool -P smbd_anon_write on
setsebool -P allow_smbd_full_access on
```

##### 步骤 4：重启 Samba 服务并验证配置

```bash
# 1. 检查配置语法（无 ERROR 即正常）
testparm /etc/samba/smb.conf

# 2. 重启服务
systemctl restart smb.service nmb.service

# 3. 检查服务状态（Active: active (running)）
systemctl status smb.service nmb.service
```

##### 步骤 5：挂载

* **Windows端**

打开文件资源管理器，输入共享地址：`\\192.168.43.130\samba_share`即可完成挂载。**如果遇到Windows强制使用凭据登录，需清除旧缓存：**

```cmd
# Windows 终端执行（Win+R 输入 cmd）
net use * /delete  # 删除所有网络驱动器缓存
net stop workstation && net start workstation  # 重启工作站服务
```

* **Linux端**

执行如下命令实现 Samba 匿名共享（无需账号密码）：

```bash
# 核心命令（挂载无密码的 Samba 共享）
mount -t cifs //192.168.43.130/samba_share /mnt/temp -o guest,uid=1000,gid=1000

# 参数说明：
# -t cifs：指定 Samba 协议类型
# //192.168.5.30/samba_share：Samba 匿名共享路径
# /mnt/temp：本地挂载点（需提前创建：mkdir -p /mnt/temp）
# -o guest：免密挂载（核心参数，跳过认证）
# uid=1000,gid=1000：挂载后文件归属本地普通用户（避免 root 独占）
```