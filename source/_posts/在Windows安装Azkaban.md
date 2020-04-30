---
title: 在Windows安装Azkaban
date: 2018-08-13 
tags: 工具
---



1. 去github把azkaban的源码下到本地，用IDE打开。根据官方文档，执行一下四条命令进行打包:

   ```shell
   gradlew build
   
   gradlew clean
   
   gradlew installDist
   
   gradlew test
   
   gradlew build -x test
   ```

   <!-- more -->

2. azkaban分为单服务模式和多服务模式，我选择的是单服务模式，即solo-server。我的源码
   路径是E:\azkaban-master，去E:\azkaban-master\azkaban-solo-server\build\distributions
   下找到对应的zip包（windows下用zip，linux下用tar.gz)解压。

   

3. 运行cmd，进入上面解压的路径下，运行sh bin/start-solo.sh。看到日志报错了，说hadoop
   找不到了。按照网上的教程安装了hadoop后再次运行，说java jdk找不到了。找到bin/internal下
   的internal-start-solo-server.sh，发现CLASSPATH的路径用的是”:”来分隔。把”:”换成”;”后
   （linux下用”:”,windows下用”;”），启动成功。