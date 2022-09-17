# 基于VirtualBox的网络攻防基础环境搭建

---

## 实验目的

* 掌握VirtualBox虚拟机的安装与使用；
* 掌握VirtualBox的虚拟网络类型和按需配置；
* 掌握VirtualBox的虚拟硬盘多重加载；

<br>

## 实验环境

以下是本次实验需要使用的网络节点说明和主要软件举例：

* VirtualBox虚拟机
* 攻击者主机(Attacker)：kali Rolling 2019.2
* 网关(Gateway,GW)：Debian Buster
* 靶机(Victim)：From Sqli to shell /xp-sp3 /kali

<br>

## 实验要求

* 虚拟硬盘配置多重加载
* 搭建满足拓扑图所示的虚拟网络拓扑；

​		根据实验宿主机的性能条件，可以适度精简靶机数量

* 完成以下网络连通性测试；

  * [x] 靶机可以直接访问攻击者主机

  * [x] 攻击者主机无法直接访问靶机

  * [x] 网关可以直接访问攻击者主机和靶机

  * [x] 靶机的所有对外上下行流量必须经过网关

  * [x] 所有节点均可以访问互联网

    <br>

## 实验过程

#### 1.虚拟硬盘多重加载

* 进入虚拟介质管理器，将类型选择为多重加载

  ![](/Users/jiji/Desktop/大三/网络安全/H1/配置多重加载.png)

  ![](/Users/jiji/Desktop/大三/网络安全/H1/多重加载.png)

#### 2.搭建满足拓扑图所示的网络拓扑

* 首先根据拓扑图配置好虚拟机

  * 网关：GW-Debian10需要配置四块网卡

    * 网卡1:网络地址转换NAT
    * 网卡2:Host-only
    * 内部网络intnet1
    * 内部网络intnet2

    ![](/Users/jiji/Desktop/大三/网络安全/H1/网关配置.png)

  * 四台靶机：各自配置一个内部网络

    * Kali-victim-1、xp-victim-1在一个内网，配置为内部网络intnet1

    ![](/Users/jiji/Desktop/大三/网络安全/H1/局域网1.png)

    * Debian-victim-2、xp-victim-2在一个内网，配置为内部网络intnet2

    ![](/Users/jiji/Desktop/大三/网络安全/H1/局域网2.png)

  * 攻击者主机：配置一个NAT网卡

    * Kali-attacker配置NAT和网关连通

    ![](/Users/jiji/Desktop/大三/网络安全/H1/攻击者主机配置.png)

​			<br>

* 各节点分配的ip地址如下：

  |      节点       |     ip地址     |
  | :-------------: | :------------: |
  |   Xp-victim-1   | 172.16.111.108 |
  |  Kali-victim-1  | 172.16.111.145 |
  |   Xp-victim-2   | 172.16.222.112 |
  | Debian-victim-2 | 172.16.222.150 |
  |  Kali-attacker  |    10.0.2.4    |

<br>

#### 3.连通性测试

* 靶机可以直接访问攻击者主机

  * 局域网1内的靶机xp-victim-1可以访问攻击者主机

    ![](/Users/jiji/Desktop/大三/网络安全/H1/局域网1的靶机攻击attacker.png)

  * 局域网2内的靶机Debian-victim-2可以访问攻击者主机

    ![](/Users/jiji/Desktop/大三/网络安全/H1/局域网2的靶机攻击attacker.png)

* 攻击者无法直接访问靶机

  * 使用kali-attacker 访问两个局域网中的主机

    ![](/Users/jiji/Desktop/大三/网络安全/H1/攻击者主机访问靶机.png)

* 网关可以访问攻击者主机和靶机

  * 网关访问局域网1内的靶机

    ![](/Users/jiji/Desktop/大三/网络安全/H1/网关访问局域网1.0.png)

    ![](/Users/jiji/Desktop/大三/网络安全/H1/网关访问局域网1.1.png)

  * 网关访问局域网2内的靶机

    ![](/Users/jiji/Desktop/大三/网络安全/H1/网关访问局域网2.0.png)

    ![](/Users/jiji/Desktop/大三/网络安全/H1/网关访问局域网2.1.png)

  * 网关访问攻击者的主机

    ![](/Users/jiji/Desktop/大三/网络安全/H1/网关访问攻击者主机.png)

* 靶机的所有对外上下流量必须经过网关

  * 首先在网关中使用`tmux`对靶机中的流量进行抓包，然后将抓包文件传到本地使用`wireshark`进行分析，可以看到流量经过网关

    ![](/Users/jiji/Desktop/大三/网络安全/H1/wireshark抓包.png)

  * 其次查看`dnsmasq.log`我们也能看出网关监听了所有靶机；

    ![](/Users/jiji/Desktop/大三/网络安全/H1/各个节点访问互联网.png)

* 所有节点可以访问互联网

  * 首先测试网关是否能访问互联网

    ![](/Users/jiji/Desktop/大三/网络安全/H1/网关访问互联网.png)

  * 其次测试各个靶机是否能访问互联网，在`dnsmasq.log`中我们可以看到各个靶机均可以ping通互联网

    ![](/Users/jiji/Desktop/大三/网络安全/H1/各个节点访问互联网.png)

  * 嘴周测试攻击者主机是否能访问互联网

    ![](/Users/jiji/Desktop/大三/网络安全/H1/attacker访问互联网.png)

  

## 遇到的问题

#### 问题1:

在导入kali虚拟机作为靶机和攻击者主机后，打开它们时无法正常进入页面，而是出现黑屏，并且屏幕上显示`BusyBox v1.30.1(Debian 1:1.30.1-4) built-in shell(ash)`的问题

![](/Users/jiji/Desktop/大三/网络安全/H1/BusyBox报错.png)

* 查了一下大概明白这个意思是说文件系统受损了，需要进行手动修复；
* 根据[指示](https://blog.csdn.net/weixin_43845335/article/details/105784758)输入`fsck -y /dev/sda1`命令，当出现`file system was modified`字样时输入`exit`，问题成功解决。

## 参考链接

* [第一章课后实验详解](http://courses.cuc.edu.cn/course/90732/learning-activity/full-screen#/378195)
* [kali系统开机显示BusyBox v1.30.1(Debian 1:1.30.1-4) built-in shell(ash) 解决方法](https://blog.csdn.net/weixin_43845335/article/details/105784758)