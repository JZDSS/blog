---
title: hadoop单机模式
date: 2017-09-09 18:59:06
tags:
---

参考：[http://hadoop.apache.org/docs/](http://hadoop.apache.org/docs/)
选择hadoop2.7.4和java1.7.0_45
<!-- more -->
### 安装java
各版本hadoop对应的java版本：[https://wiki.apache.org/hadoop/HadoopJavaVersions](https://wiki.apache.org/hadoop/HadoopJavaVersions)
各版本java下载地址：[http://www.oracle.com/technetwork/java/javase/archive-139210.html](http://www.oracle.com/technetwork/java/javase/archive-139210.html)
```
sudo tar -xzvf jdk-7u45-linux-x64.tar.gz -C /usr/lib/jvm 
sudo vim ~/.bashrc
```
在最后加上：
```
export JAVA_HOME=/usr/lib/jvm/jdk1.7.0_45
export JRE_HOME=${JAVA_HOME}/jre
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib
export PATH=${JAVA_HOME}/bin:$PATH
```
执行：
```
source ~/.bashrc
```
使环境变量生效
如果默认shell不是bash，比如是zsh，则将前面的.bashrc改为.zshrc
执行：
```
java -version
```
出现：
```
java version "1.7.0_45"
Java(TM) SE Runtime Environment (build 1.7.0_45-b18)
Java HotSpot(TM) 64-Bit Server VM (build 24.45-b08, mixed mode)
```
说明安装成功

### 安装软件
```
sudo apt install ssh
sudo apt install rsync
```

### 下载hadoop
各版本hadoop下载链接：[http://mirror.bit.edu.cn/apache/hadoop/common/](http://mirror.bit.edu.cn/apache/hadoop/common/)
```
wget http://mirror.bit.edu.cn/apache/hadoop/common/hadoop-2.7.4/hadoop-2.7.4.tar.gz
tar zxvf hadoop-2.7.4.tar.gz
cd hadoop-2.7.4
```
之前已经配置JAVA_HOME环境变量，此时可直接执行hadoop：
```
./bin/hadoop
```
如果之前没配置JAVA_HOME，可以在./etc/hadoop/hadoop-env.sh末尾添加
```
export JAVA_HOME=/usr/lib/jvm/jdk1.7.0_45
```
在~/.bashrc（或~/.zshrc等）文件末尾添加
```
export HADOOP_HOME=/path/to/hadoop/root
export PATH=$HADOOP_HOME/bin:$PATH
```
并使之生效，这样就可以在任意目执行bin/hadoop等命令了




