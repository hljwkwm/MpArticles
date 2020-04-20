# Ubuntu安装JDK1.8并解决命令失效问题

> 本文介绍了如何在Ubuntu下安装Oracle JDK 1.8，并如何解决关闭终端后java等命令失效的问题。

<span style="display:block;text-align:right;color: #ffb11b;">这是我的第1篇原创文章，该篇约1639字，总阅读时长约4分钟。</span>

## 懒人目录

前言

一、JDK下载

二、JDK安装

三、关闭终端命令失效问题

## 前言

系统使用环境：

VMware 15.1.0，Ubuntu 18.04，Oracle JDK SE 8u151。

## 一、JDK下载

JDK从公司上划分有OpenJDK和Oracle JDK，从版本上来看Java还有SE、EE和ME。

Open JDK和Oracle JDK有以下区别：

- Oracle JDK版本将每三年发布一次，而OpenJDK版本每三个月发布一 次；
- OpenJDK 是一个参考模型并且是完全开源的，而Oracle JDK是 OpenJDK的一个实现，并不是完全开源的；
- Oracle JDK 比 OpenJDK 更稳定。OpenJDK和Oracle JDK的代码几乎 相同，但Oracle JDK有更多的类和一些错误修复。因此，如果您想开发 企业/商业软件，我建议您选择Oracle JDK，因为它经过了彻底的测试和 稳定。某些情况下，有些人提到在使用OpenJDK 可能会遇到了许多应 用程序崩溃的问题，但是，只需切换到Oracle JDK就可以解决问题；
- 顶级公司正在使用Oracle JDK，例如Android Studio，Minecraft和 IntelliJ IDEA开发工具，其中Open JDK不太受欢迎；
- 在响应性和JVM性能方面，Oracle JDK与OpenJDK相比提供了更好的 性能；
- Oracle JDK不会为即将发布的版本提供长期支持，用户每次都必须通过 更新到最新版本获得支持来获取最新版本；
- Oracle JDK根据二进制代码许可协议获得许可，而OpenJDK根据GPL v2许可获得许可。

JDK SE和JDK EE有以下区别：

- Java SE（Java Platform，Standard Edition），以前称为 J2SE。它允许开发和部署在桌面、服务器、嵌入式环境和实时环境中使用的 Java 应用程序。Java SE 包含了支持 Java Web 服务开发的类，并为 Java Platform，Enterprise Edition（Java EE）提供基础。**Java SE 是做电脑上运行的软件**。
- Java EE（Java Platform，Enterprise Edition），以前称为 J2EE。企业版本帮助开发和部署可移植、健壮、可伸缩且安全的服务器端 Java 应用程序。Java EE 是在 Java SE 的基础上构建的，它提供 Web 服务、组件模型、管理和通信 API，可以用来实现企业级的面向服务体系结构（service-oriented architecture，SOA）和 Web 2.0 应用程序。**Java EE 是用来做网站的**（我们常见的JSP技术，虽然已慢慢退出舞台，逐渐被微服务所代替）。
- Java ME（Java Platform，Micro Edition），以前称为 J2ME。Java ME 为在移动设备和嵌入式设备（比如手机、PDA、电视机顶盒和打印机）上运行的应用程序提供一个健壮且灵活的环境。Java ME 包括灵活的用户界面、健壮的安全模型、许多内置的网络协议以及对可以动态下载的连网和离线应用程序的丰富支持。基于 Java ME 规范的应用程序只需编写一次，就可以用于许多设备，而且可以利用每个设备的本机功能。**Java ME 是做手机软件的**。

**对于实际安装来说，推荐使用Oracle JDK SE，因为Oracle版本使用更稳定，用户群体更大，而EE相比于SE，其实就是多了几个包，在使用Web容器时会给提供。**

这里提供Oracle JDK 1.8的下载链接：

JDK 8u211 and later：[https://www.oracle.com/java/technologies/javase/javase8u211-later-archive-downloads.html](https://www.oracle.com/java/technologies/javase/javase8u211-later-archive-downloads.html)

JDK 8u202 and earlier：[https://www.oracle.com/java/technologies/javase/javase8-archive-downloads.html](https://www.oracle.com/java/technologies/javase/javase8-archive-downloads.html)

**注：**下载时需要登录Oracle账号。

Ubuntu请下载Java SE Development Kit中的jdk-8uxxx-linux-x64.tar.gz。

## 二、JDK安装

首先将JDK的压缩包进行解压，解压至自己的理想位置，比如`/usr/local/lib`或者自己的家目录`/home/你的用户名`。

```shell
# 首先到你的 JDK 包的目录下打开终端，然后解压它
$ tar -zxvf jdk-8uxxx-linux-x64.tar.gz
# 可以考虑将目录进行重命名
$ mv jdk1.8.0_xxx jdk8
# 将压缩包移动到指定的目录
$ sudo mv jdk8 /usr/local/lib
# 或者
$ sudo mv jdk8 /home/ziliu
```

接下来设置环境变量。Ubuntu下设置环境变量有三种方法，一种用于当前终端，一种用于当前用户，一种用于所有用户。

用于当前终端：在当前终端中输入：`export PATH=$PATH:<你的要加入的路径>`，不过上面的方法只适用于当前终端，一旦当前终端关闭或在另一个终端中，则无效。

用于当前用户：在用户主目录下有一个`.bashrc`隐藏文件，可以在此文件中加入`export PATH=<你的要加入的路径>:$PATH`，如果要加入多个路径，只要加入`export PATH=<你要加入的路径1>:<你要加入的路径2>: ...... :$PATH`，当中每个路径要以冒号分隔，这样每次使用该用户登录都会生效。

用于所有用户：在/etc/profile 文件中加入：export PATH=<你要加入的路径>:$PATH就可以了。若果是多个路径，和用于当前用户的方法是一样哒。

注：在终端输入`echo $PATH`可以查看当前的环境变量。

这里我们选择在profile中加入环境变量：

```shell
# 用vim或者gedit等文本编辑工具打开profile，注意请使用sudo
$ sudo gedit /etc/profile
# 在行末添加如下内容，注意JAVA_HOME需要填写JDK所在的目录，其他的可以不用动
export JAVA_HOME=/home/ziliu/jdk8
export JRE_HOME=${JAVA_HOME}/jre  
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib  
export PATH=${JAVA_HOME}/bin:$PATH
```

编辑好保存后，更新配置：

```shell
# 更新配置，注意这里不需要使用sudo命令
$ source /etc/profile
# 测试命令
$ java -version
```

至此Ubuntu上的JDK配置就完成了。

## 三、关闭终端命令失效问题

在安装完成关闭终端后再次打开终端会出现java等命令均失效，遇到这类问题，只需要注销用户或者重启电脑，该问题就可以解决。

<span style="display:block;text-align:right;color: #ffb11b;">沉迷学习，点亮在看！</span>