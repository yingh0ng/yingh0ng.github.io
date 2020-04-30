---
title: CentOS安装Shadowsocks和开启BBR算法
date: 2018-08-20
tags: Linux
---





#### Shadowsocks的安装和使用

- 安装

  ```shell
  yum install python-setuptools && easy_install pip
  pip install shadowsocks
  ```

  <!-- more -->

- 运行

  ```shell
  ssserver -p 443 -k password -m aes-256-cfb
  ```

  

- 后台运行、停止

  ```shell
  sudo ssserver -p 443 -k password -m aes-256-cfb --user nobody -d start
  sudo ssserver -d stop
  ```

- 使用配置文件运行

  ```shell
  ssserver -c /etc/shadowsocks.json -d start
  ssserver -c /etc/shadowsocks.json -d stop
  ```

  ```json
  {
      "server":"my_server_ip",
      "server_port":8388,
      "local_address": "127.0.0.1",
      "local_port":1080,
      "password":"mypassword",
      "timeout":300,
      "method":"aes-256-cfb",
      "fast_open": false
  }
  ```

  

#### 开启BBR算法

bbr算法需要linux升级到4.9及以上的内核才能支持。

- 安装eprl的源

  ```shell
  rpm --import https://www.elrepo.org/RPM-GPG-KEY-elrepo.org
  rpm -Uvh http://www.elrepo.org/elrepo-release-7.0-2.el7.elrepo.noarch.rpm
  ```

  

- 安装最新的内核版本

  ```shell
  yum --enablerepo=elrepo-kernel install kernel-ml -y
  ```

  

- 修改 grub2 的启动项，设置启动之后选择最新的内核。以防万一，选择第一个为默认启动项。

  ```shell
  egrep ^menuentry /etc/grub2.cfg | cut -f 2 -d \'
  grub2-set-default 0
  ```

  

- 重启

  ```shell
  reboot
  ```

  

- 开启bbr

  ```shell
  [root@server ~]# vi /etc/sysctl.conf
  #在文件末尾添加如下内容
  net.core.default_qdisc = fq
  net.ipv4.tcp_congestion_control = bbr
  
  #加载系统参数
  [root@vultr ~]# sysctl -p
  net.ipv6.conf.all.accept_ra = 2
  net.ipv6.conf.eth0.accept_ra = 2
  net.core.default_qdisc = fq
  net.ipv4.tcp_congestion_control = bbr
  [root@vultr ~]#
  #如上，输出了我们添加的那两行配置代表正常。
  ```

  

- 确认是否开启成功

  ```shell
  [root@vultr ~]# sysctl net.ipv4.tcp_available_congestion_control
  net.ipv4.tcp_available_congestion_control = bbr cubic reno
  [root@vultr ~]# lsmod | grep bbr
  tcp_bbr                20480  2
  [root@vultr ~]#
  ```

  