# eNSP实例

## 实例1：简单小型网络1

### 一、网络拓扑图

![image-20260101212041745](https://bu.dusays.com/2026/01/01/695674aa20142.png)

### 二、原理介绍

本拓扑实现 **“终端 VLAN 隔离 + DHCP 自动分配 IP+NAT 公网访问”** 的小型企业网络功能，核心原理如下：

1. **VLAN 隔离：**通过交换机划分 VLAN10、VLAN20，实现不同终端的网段隔离，提升网络安全性；
2. **DHCP 自动分配：**路由器通过子接口对接交换机 Trunk 链路，为不同 VLAN 终端分配对应网段的 IP 与网关
3. **NAT 地址转换：**路由器将内网私有 IP 转换为出口公网 IP（本拓扑中为10.0.0.2），实现内网终端访问外网；
4. **静态路由：**路由器通过缺省路由将外网流量指向 ISP，完成数据的跨网络转发。

### 三、具体实现方法

#### （一）路由器 R1 配置（核心网关）

1. 全局基础配置
  ```bash
  [R1]dhcp enable  # 开启DHCP服务，为终端分配IP做准备
  ```


2. 子接口配置（对接交换机 Trunk）
通过子接口承载不同 VLAN 流量，并作为对应 VLAN 的 DHCP 服务器：

```bash
#配置VLAN10对应的子接口
[R1]interface GigabitEthernet0/0/0.10
[R1-GigabitEthernet0/0/0.10]dot1q termination vid 10  # 绑定VLAN10
[R1-GigabitEthernet0/0/0.10]ip address 192.168.10.1 255.255.255.0  # 配置VLAN10网关
[R1-GigabitEthernet0/0/0.10]arp broadcast enable  # 开启ARP广播，实现VLAN间互通
[R1-GigabitEthernet0/0/0.10]dhcp select interface  # 开启接口级DHCP

#配置VLAN20对应的子接口
[R1]interface GigabitEthernet0/0/0.20
[R1-GigabitEthernet0/0/0.20]dot1q termination vid 20
[R1-GigabitEthernet0/0/0.20]ip address 192.168.20.1 255.255.255.0
[R1-GigabitEthernet0/0/0.20]arp broadcast enable
[R1-GigabitEthernet0/0/0.20]dhcp select interface
```


3. 出口与 NAT 配置（实现内网访问外网）
  ```bash
  #创建ACL，抓取所有内网IP
  [R1]acl 2000
  [R1-acl-basic-2000]rule permit source any
  
  #配置出口接口，并应用NAT转换
  [R1]interface GigabitEthernet0/0/1
  [R1-GigabitEthernet0/0/1]ip address 10.0.0.2 255.255.255.0  # 配置出口IP
  [R1-GigabitEthernet0/0/1]nat outbound 2000  # 将ACL抓取的流量做源NAT转换
  ```

4. 静态缺省路由配置（指向外网）
  ```bash
  [R1]ip route-static 0.0.0.0 0 10.0.0.1  # 所有外网流量转发到ISP路由器
  ```

  #### （二）交换机 SW1 配置（接入层）
5. 基础与 VLAN 配置
  ```bash
  [SW1]undo info-center enable  # 关闭日志提示，简化配置过程
  [SW1]vlan batch 10 20  # 批量创建VLAN10、VLAN20
  ```


6. 接口模式配置

```bash
#对接路由器的接口配置为Trunk（承载所有VLAN）
[SW1]interface GigabitEthernet0/0/1
[SW1-GigabitEthernet0/0/1]port link-type trunk
[SW1-GigabitEthernet0/0/1]port trunk allow-pass vlan all

#对接PC1的接口配置为Access，加入VLAN10
[SW1]interface GigabitEthernet0/0/2
[SW1-GigabitEthernet0/0/2]port link-type access
[SW1-GigabitEthernet0/0/2]port default vlan 10

#对接PC2的接口配置为Access，加入VLAN20
[SW1]interface GigabitEthernet0/0/3
[SW1-GigabitEthernet0/0/3]port link-type access
[SW1-GigabitEthernet0/0/3]port default vlan 20
```

7. 交换机管理地址配置
   
```bash
[SW1]interface Vlanif10
[SW1-Vlanif10]ip address 192.168.10.2 255.255.255.0  # 配置VLAN10内的管理IP
```


## 实例2：简单小型网络2

### 一、拓扑图

![image-20260102151823791](https://bu.dusays.com/2026/01/02/695771467c621.png)

> 实验目的：本拓扑实现**终端 VLAN 隔离 + DHCP 手动分配 IP+NAT 公网访问**的小型企业网络功能，核心原理如下：

### 二、基础配置

#### （一）交换机LSW1的配置

##### 1.创建vlan

```bash
<Huawei>system-view
[Huawei]sysname LSW1
[LSW1]vlan batch 10 20  # 批量创建VLAN10和VLAN20
```

##### 2. 配置接入端口（连接 PC 的接口）

- 连接 PC1 的 GE0/0/2 接口：

```plaintext
[LSW1]interface GigabitEthernet 0/0/2
[LSW1-GigabitEthernet0/0/2]port link-type access
[LSW1-GigabitEthernet0/0/2]port default vlan 10
[LSW1-GigabitEthernet0/0/2]quit
```

- 接 PC2 的 GE0/0/3 接口：

```plaintext
[LSW1]interface GigabitEthernet 0/0/3
[LSW1-GigabitEthernet0/0/3]port link-type access
[LSW1-GigabitEthernet0/0/3]port default vlan 20
[LSW1-GigabitEthernet0/0/3]quit
```

##### 3. 配置 Trunk 端口（连接 AR1 的接口）

```plaintext
[LSW1]interface GigabitEthernet 0/0/1
[LSW1-GigabitEthernet0/0/1]port link-type trunk
[LSW1-GigabitEthernet0/0/1]port trunk allow-pass vlan 10 20  # 允许VLAN10、20通过
[LSW1-GigabitEthernet0/0/1]quit
```

#### （二）路由器 AR1 的配置（单臂路由 + 静态路由）

##### 1. 配置子接口（实现单臂路由，承载 VLAN10/20）

- 进入连接 LSW1 的 GE0/0/0 接口：

```plaintext
<Huawei>system-view
[Huawei]sysname AR1
[AR1]interface GigabitEthernet 0/0/0
[AR1-GigabitEthernet0/0/0]undo shutdown  # 确保接口启用
[AR1-GigabitEthernet0/0/0]quit
```

- 配置 VLAN10 的子接口（GE0/0/0.10）：

```plaintext
[AR1]interface GigabitEthernet 0/0/0.10
[AR1-GigabitEthernet0/0/0.10]dot1q termination vid 10  # 封装VLAN10的802.1q标签
[AR1-GigabitEthernet0/0/0.10]ip address 192.168.10.1 255.255.255.0  # 作为VLAN10的网关
[AR1-GigabitEthernet0/0/0.10]arp broadcast enable  # 启用ARP广播（单臂路由必须配置）
[AR1-GigabitEthernet0/0/0.10]quit
```

- 配置 VLAN20 的子接口（GE0/0/0.20）：

```plaintext
[AR1]interface GigabitEthernet 0/0/0.20
[AR1-GigabitEthernet0/0/0.20]dot1q termination vid 20  # 封装VLAN20的802.1q标签
[AR1-GigabitEthernet0/0/0.20]ip address 192.168.20.1 255.255.255.0  # 作为VLAN20的网关
[AR1-GigabitEthernet0/0/0.20]arp broadcast enable
[AR1-GigabitEthernet0/0/0.20]quit
```

##### 2. 配置连接 ISP 的接口

```plaintext
[AR1]interface GigabitEthernet 0/0/1
[AR1-GigabitEthernet0/0/1]ip address 10.0.0.2 255.255.255.0  # 与ISP的10.0.0.1互联
[AR1-GigabitEthernet0/0/1]undo shutdown
[AR1-GigabitEthernet0/0/1]quit
```

##### 3. 配置静态路由（访问公网 / ISP 侧）

```plaintext
[AR1]ip route-static 0.0.0.0 0.0.0.0 10.0.0.1  # 所有未知流量下一跳指向ISP的10.0.0.1
```

#### （三）ISP 路由器的配置（补充，确保与 AR1和PC 互通）

```plaintext
<Huawei>system-view
[Huawei]sysname ISP
[ISP]interface GigabitEthernet 0/0/0
[ISP-GigabitEthernet0/0/0]ip address 10.0.0.1 255.255.255.0
[ISP-GigabitEthernet0/0/0]undo shutdown
[ISP-GigabitEthernet0/0/0]quit
```

### 三、验证互通

1. PC1 ping PC2：`ping 192.168.20.2`（应通）
2. PC1 ping ISP 的 10.0.0.1：`ping 10.0.0.1`（应通）
3. PC1 ping AR1：`ping 10.0.0.2`​（应通）
4. AR1 ping ISP：`ping 10.0.0.1`（应通）
5. ISP ping PC1/2：`ping 192.168.10.2 / 192.168.20.2`（应通）