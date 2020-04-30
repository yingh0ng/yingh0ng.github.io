---
title: 使用Maven打包并运行jar
date: 2018-07-11
tags: Maven
---



平时运行项目的时候都在被idea或者在公司的测试环境用Jenkins自动构建，很少作用java命令直接
运行。果然，抱着好奇的心理去试一下的时候就发现了一些问题。

1.首先使用idea新建一个maven的hello world项目，pom文件不做修改。注意：此时项目里没有
MANIFEST.MF文件！

2.直接在idea或者cmd打包&运行

```shell
mvn package
java -jar xxx.jar
```

cmd告诉我“xxx.jar中没有主清单属性”，初步猜测是jvm找不到程序的入口类。网上查了下都是说
缺少MANIFEST.MF文件或者MANIFEST.MF文件里没有指定main-class。

<!-- more -->

4.根据以上问题，在java目录下新建一个META-INF目录，并在其下新建一个MANIFEST.MF文件。
指定main-class，重新打包运行。
但上面那个“没有主清单属性”的问题依然存在。打开jar包里的MANIFEST.MF文件发现是这样的。

```yaml
Manifest-Version: 1.0
Archiver-Version: Plexus Archiver
Built-By: Administrator
Created-By: Apache Maven 3.3.9
Build-Jdk: 1.8.0_111
```

里面并没有我定义的main-class属性，而且多了以上其他属性？？

5.原来maven自带的jar包（具体哪个jar包有待探讨）会自动生成一个MANIFEST.MF文件，所以把
我自己建的MANIFEST.MF文件覆盖掉了。修改pom文件,引入一个plugin如下：

```xml
<build>
        <finalName>testjar</finalName>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-jar-plugin</artifactId>
                <configuration>
                    <archive>
                        <manifest>
                            <addClasspath>true</addClasspath>
                            <mainClass>com.joy.Test1</mainClass>
                        </manifest>
                    </archive>
                </configuration>
            </plugin>
        </plugins>
    </build>
```

重新打包运行，搞定。