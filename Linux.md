# 一、基础知识



## 1、背景

Linux是一个内核，基于这个开源的内核，很多厂商开发了自己的Linux发行版本

RedHat：Fedora

CentOS：服务器端操作系统代表

Debian：Ubuntu：个人操作系统的代表



## 2、Linux配置



- 查看IP地址信息

```java
//Windows
ipconfig /all
//Linux
ifconfig
```

- 重启Linux

```java
shutdown -r now
```

- 网络配置

```java
service iptables stop //关闭防火墙
service network restart	//重启网络
ifconfig eth0 192.168.0.202	//配置IP地址，根据实际网段来配置
route add default gw 192.168.0.1	//配置IP地址，和IP地址同一网段
ping 220.181.57.217 //用ping命令测试连接百度IP	//不需要配置DNS服务器
ping www.baidu.com	//用ping命令测试连接百度域名。//需要配置DNS服务器
```

