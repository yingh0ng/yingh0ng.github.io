---
title: 使用-Djava.ext.dirs参数的一个问题
date: 2018-08-13 
tags: Java
---





在cmd运行java程序时报了一个“javax.net.ssl.SSLException: java.lang.RuntimeException: Could not generate DH keypair”。

<!-- more -->

根据百度上几个说法，包括加jar包，换jdk之类的都不行。发现生成那个keypair会用到\jre\lib\ext里面的jar包，而我的cmd中的命令
有 -Djava.ext.dirs这个参数。原来这个参数会把系统原来的$JAVA_HOME\jre\lib\ext覆盖掉，故在我的”-Djava.ext.dirs=”后面加上
$JAVA_HOME/jre/lib/ext(这个路径要写在其他路径前面)。问题到这就解决了。