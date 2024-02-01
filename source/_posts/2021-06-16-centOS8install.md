---
title: CentOS8 安装
date: 2021-06-16 18:00:46
tags: 
  - Linux
categories: 
  - [Linux]
comments: true
cover: /img/index_img.png
---

## 系统最小化安装

![image-20210526170136155](image-20210526170136155.png)



![image-20210526170151325](image-20210526170151325.png)

​															![image-20210526170202591](image-20210526170202591.png)

​					此处没有**CentOS 8**可以选择**CentOS 7**

![image-20210526170220072](image-20210526170220072.png)

![image-20210526170348489](image-20210526170348489.png)

![image-20210526170403991](image-20210526170403991.png)

内存不要小于**768M**，否者会安装失败

![image-20210526170417417](image-20210526170417417.png)

本地使用就选择NAT模式，让其他人访问选择桥接

![image-20210526170452656](image-20210526170452656.png)

![image-20210526170530514](image-20210526170530514.png)

![image-20210526170538167](image-20210526170538167-1622019938520.png)

![image-20210526170547533](image-20210526170547533.png)

最大大小可以往大了调，占用大小是实际用量。设置小了以后扩展麻烦

![image-20210526170559677](image-20210526170559677.png)

![image-20210526170705247](image-20210526170705247.png)

完成！

![image-20210526170715276](image-20210526170715276.png)

![image-20210526170803102](image-20210526170803102.png)

![image-20210526170828415](image-20210526170828415.png)

开机

![image-20210526171625947](image-20210526171625947.png)

![image-20210526171645830](image-20210526171645830.png)

下面这几个位置需要设置一下：

![image-20210526171709347](image-20210526171709347.png)

首先设置安装目的地：

![image-20210526171737210](image-20210526171737210-1622020657737.png)

设置时间和日期：

![image-20210526171754252](image-20210526171754252.png)

设置网络和主机名（打开网络，默认是关着的）

![image-20210526171812504](image-20210526171812504.png)

选择安装的软件（最小安装即可）：

![image-20210526171827942](image-20210526171827942.png)

最后设置下root密码

123456

![image-20210526171910790](image-20210526171910790.png)

由于密码太简单，我们需要点两次完成。



安装

![image-20210526171943437](image-20210526171943437.png)

配置完成，点击重启：

![image-20210526172000683](image-20210526172000683.png)

遇到下面这里不用管（也可以按回车快速跳过）：

![image-20210526172018216](image-20210526172018216.png)

登录输入用户名和密码：root/123456 即可

![image-20210526172412351](image-20210526172412351.png)



## 配置IP

–VM:编辑>虚拟网络编辑器

<img src="image-20210526172852231.png" alt="image-20210526172852231"  />

![image-20210526173106603](image-20210526173106603.png)

![image-20210526173127973](image-20210526173127973.png)

```shell
vi /etc/sysconfig/network-scripts/ifcfg-ens33
```

![image-20210526173431297](image-20210526173431297.png)

•删除UUID和MAC地址

> TYPE=Ethernet
> PROXY_METHOD=none
> BROWSER_ONLY=no
> BOOTPROTO=static
> DEFROUTE=yes
> IPV4_FAILURE_FATAL=no
> IPV6INIT=yes
> IPV6_AUTOCONF=yes
> IPV6_DEFROUTE=yes
> IPV6_FAILURE_FATAL=no
> IPV6_ADDR_GEN_MODE=stable-privacy
> NAME=ens33
> DEVICE=ens33
> ONBOOT=yes
> IPADDR=192.168.163.100
> NETMASK=255.255.255.0
> GATEWAY=192.168.163.2
> DNS1=114.114.114.114
> PREFIX=24

修改完IPADDR的IP和Gateway的网段保存

输入nmcli c reload 重启网卡

```shell
	nmcli c reload
```
输入nmcli c up ens33启用ens33网卡

```shell
	nmcli c up ens33
```

![image-20210527092325900](image-20210527092325900.png)



## 关闭防火墙

> 1. 查看防火墙状态
>
> ```
> systemctl status firewalld.service
> ```
>
> ![image-20210527102952085](image-20210527102952085.png)
>
> 2. 开启防火墙
>
> ```shell
> systemctl start firewalld.service
> ```
>
> 
>
> 3. 关闭防火墙
>
> ```shell
> systemctl stop firewalld.service
> ```
>
> 
>
> 4. 禁用防火墙
>
> ```shell
> systemctl disable firewalld.service
> ```

## 更改主机名

1. 使用hostnamectl命令

   在CentOS 8和所有其他使用systemd的Linux发行版中，你可以使用hostnamectl命令更改系统主机名和相关设置，语法如下：

   ```shell
   hostnamectl set-hostname name [option...]
   ```

   其中 *option* 是 `--pretty`、`--static`, 会 `--transient` 中的一个或多个选项。

   如果 `--static` 或 `--transient` 选项与 `--pretty` 选项一同使用，则会将 static 和 transient 主机名简化为 pretty 主机名格式。使用 “`-`” 替换空格，并删除特殊字符。如果未使用 `--pretty` 选项，则不会发生简化。

   

   例如，要将系统静态主机名更改为node0，可以使用以下命令：

   ```shell
   hostnamectl set-hostname node0
   ```

   要验证主机名是否已成功更改，请使用hostnamectl命令。

2. 使用nmcli命令

   nmcli是用于控制NetworkManager的命令行工具，也可用于更改系统的主机名。

    > 要查看当前主机名，请输入：
    >
    > ```shell
    > nmcli g hostname
    > ```
    >
    > 要将主机名更改为node0，请使用以下命令：
    >
    > ```shell
    > nmcli g hostname node0
    > ```
    >
    > 为了使更改生效，请重新启动systemd-hostnamed服务：
    >
    > ```shell
    > systemctl restart systemd-hostnamed
    > ```



## 关机

```shell
poweroff
```

## 创建快照

![image-20210527110945130](image-20210527110945130.png)



## 克隆

1. 克隆虚拟机

![image-20210527111005574](image-20210527111005574.png)

![image-20210527111015964](image-20210527111015964.png)

![image-20210527111035983](image-20210527111035983.png)

2. 更改ip
3. 更改主机名

