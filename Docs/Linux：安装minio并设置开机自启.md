# Linux：安装minio并设置开机自启

## 1. 安装

1、创建minio[文件夹](https://so.csdn.net/so/search?q=文件夹&spm=1001.2101.3001.7020)，以及后续存储数据和日志的文件夹

```shell
mkdir -p /data/minio
# 存储数据目录
mkdir -p /data/minio/data
# 存储日志目录
mkdir -p /data/minio/log
# 配置文件存储目录
mkdir -p /data/minio/conf
cd /data/minio

```

2、下载minio二进制文件

```shell
wget https://dl.min.io/server/minio/release/linux-amd64/minio
```

3、将下载的minio文件添加为可执行文件

```shell
chmod +x minio
```

4、创建配置文件

```shell
vim /data/minio/conf/minio.conf
```

脚本内容：

```shell
MINIO_VOLUMES="/data/minio/data"
MINIO_OPTS="-C /data/minio/conf --address 0.0.0.0:9000 --console-address 0.0.0.0:9001"
MINIO_ROOT_USER=admin
MINIO_ROOT_PASSWORD=xxx%123456
```

5、创建系统启动脚本

```shell
vim /etc/systemd/system/minio.service
```

脚本内容

```shell
[Unit]
Description=Minio
Documentation=https://docs.min.io
Wants=network-online.target
After=network-online.target
[Service]
# User and group
User=root
Group=root
EnvironmentFile=/data/minio/conf/minio.conf
ExecStart=/data/minio/minio server $MINIO_OPTS 
ExecReload=/bin/kill -HUP $MAINPID
# Let systemd restart this service always
Restart=always
# Specifies the maximum file descriptor number that can be opened by this process
LimitNOFILE=65536
TimeoutStopSec=5
SendSIGKILL=no
SuccessExitStatus=0 1
StandardOutput=syslog
StandardError=syslog
SyslogIdentifier=minio

[Install]
WantedBy=multi-user.target
```

6、启动minio

```shell
# 启动
systemctl start minio
# 停止
systemctl stop minio
# 查看服务状态
systemctl status minio
# 重载服务脚本
systemctl daemon-reload
systemctl reload minio
```

7、查看启动状态，如下图表示启动成功

```shell
systemctl status minio
```

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/4dc27d5c955fb823fb1f7cd3d4988b8c.png)

8、访问minio，端口是脚本中设置的9001端口：http://192.168.6.25:9001/login

这里账号就用上述第4部设置的账号密码

```shell
MINIO_ROOT_USER=admin
MINIO_ROOT_PASSWORD=xxx@123456
12
```

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/55981e720d7c5a90713c4c87b5c74557.png)

9、如下则安装并登陆成功，后续可以根据自己的需要创建桶配置权限等
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/3c7ec0c21eb97704466652b999b7db67.png)

10、因为我们这里要使用java连接minio。因此我们还需要创建一个access key，点击进入`Access Keys`，点击`Create access key`

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/f43bf4293f7a1069cdd8387d5ae691ca.png)

11、填写相关信息，其中accesskey可以自定义或者用系统自动生成的

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/3ea013e090e8aece060269482bb65c0b.png)

12、生成的accessKey和secretKey一定要妥善保管，后续client端连接minio就通过该信息
![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/db265e0080fd497e7fd28b86fbd730b5.png)

## 2. 设置自启

1、因为我们上述已经创建了systemctl启动脚本了，将该服务加入开机自启列表就行

```shell
systemctl enable minio
```

2、重启服务器，查看服务是否自动启动

![在这里插入图片描述](https://i-blog.csdnimg.cn/blog_migrate/349c188d71419c03fb728ecb4696bcf2.png)