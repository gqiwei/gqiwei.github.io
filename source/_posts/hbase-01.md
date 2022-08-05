---
title: hbase单机配置，使用外部zookeeper，java调用
categories:
  - 笔记
tags:
  - java
  - hbase
  - zookeeper
date: 2022-08-05 11:37:45
description:
---


> 本文只是简单记录。
标题中说的外部zookeeper，并不是在其他服务器上的zookeeper，是在本机上运行的zookeeper。

需要java环境，之前出过linux配置jdk，这里不需要赘述。  
目前系统环境是centos7.8。  
jdk是1.8，zookeeper是3.6.3，hbase是2.4.13

# hbase单机安装配置
## 创建基本文件夹
``` linux
cd /usr/loacal
mkdir hbase
mkdir zookeeper
cd zookeeper
mkdir data
mkdir log
cd ../hbase
mkdir data
```
## 下载zookeeper
``` linux
cd /usr/local/zookeeper
wget https://dlcdn.apache.org/zookeeper/zookeeper-3.6.3/apache-zookeeper-3.6.3-bin.tar.gz
tar -zxf apache-zookeeper-3.6.3-bin.tar.gz
```
## 修改zookeeper配置
``` linux
cd /usr/local/zookeeper/apache-zookeeper-3.6.3-bin/conf
cp zoo_sample.cfg zoo.cfg
vim zoo.cfg
```
在zoo.cfg中添加修改配置
```
dataDir=/usr/local/zookeeper/data
dataLogDir=/usr/local/zookeeper/log
```

## 下载hbase
``` linux
cd /usr/local/hbase
wget https://dlcdn.apache.org/hbase/2.4.13/hbase-2.4.13-bin.tar.gz
tar -zxf hbase-2.4.13-bin.tar.gz
```

## 修改hbase配置
``` linux
cd /usr/local/hbase/hbase-2.4.13/conf
vim hbase-env.sh
```
在hbase-env.sh中添加
```
export JAVA_HOME=/usr/local/java/jdk1.8.0_111
export HBASE_MANAGES_ZK=false
```
``` linux
vim hbase-site.xml
```
在hbase-site.xml中添加修改
``` xml
<configuration>
<!-- HBase集群中所有RegionServer共享目录，用来持久化HBase的数据，一般设置的是hdfs的文件目录，如hdfs://namenode.[example.org:9000/hbase](http://example.org:9000/hbase)  -->
  <property>
    <name>hbase.rootdir</name>
    <value>file:///usr/local/hbase/data</value>
  </property>
   <!-- ZooKeeper的zoo.conf中的配置。 快照的存储位置 -->
  <property>
    <name>hbase.zookeeper.property.dataDir</name>
    <value>/usr/local/zookeeper/data</value>
  </property>
   <!-- ZooKeeper端口 -->
  <property>
      <name>hbase.zookeeper.property.clientPort</name>
      <value>2181</value>
  </property>
    <!-- ZooKeeper连接机器名或者ip,多个用','号分隔 -->
  <property>
      <name>hbase.zookeeper.quorum</name>
      <value>master</value>
  </property>
  <!-- ZooKeeper存储hbase数据的节点名称 -->
  <property>
      <name>zookeeper.znode.parent</name>
      <value>/hbase</value>
  </property>
   <!-- 集群的模式，分布式还是单机模式，如果设置成false的话，HBase进程和Zookeeper进程在同一个JVM进程  -->
  <property>
    <name>hbase.cluster.distributed</name>
    <value>true</value>
  </property>
  <!-- 本地文件系统tmp目录 -->
  <property>
    <name>hbase.tmp.dir</name>
    <value>./tmp</value>
  </property>
  <property>
    <name>hbase.unsafe.stream.capability.enforce</name>
    <value>false</value>
  </property>
</configuration>
```

## 启动zookeeper
``` linux
cd /usr/local/zookeeper/apache-zookeeper-3.6.3-bin/bin/
./zkServer.sh start
```
可使用`./zkServer.sh status`查看zookeeper状态，也可以使用`jps`查看进程。

## 启动hbase
``` linux
cd /usr/local/hbase/hbase-2.4.13/bin/
./start-hbase.sh
```
可使用jps查看进程。
使用`./hbase shell`进入控制台进行hbase操作。

# java连接hbase
添加maven依赖
``` xml
<dependency>
    <groupId>org.apache.hbase</groupId>
    <artifactId>hbase-client</artifactId>
    <version>2.1.0</version>
</dependency>
```
创建`HbaseConnect.java`文件，并添加以下代码
``` java
import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.client.Admin;
import org.apache.hadoop.hbase.client.Connection;
import org.apache.hadoop.hbase.client.ConnectionFactory;

import java.io.IOException;

public class HbaseConnect {
    public static void main(String[] args){
        //1、创建hbase的配置
        Configuration configuration = HBaseConfiguration.create();
        configuration = HBaseConfiguration.create();
        configuration.set("hbase.zookeeper.quorum","43.242.200.154");
        configuration.set("hbase.zookeeper.property.clientPort","2181");
        configuration.setInt("hbase.rpc.timeout",2000);
        configuration.setInt("hbase.client.operation.timeout",3000);
        configuration.setInt("hbase.client.scanner.timeout.period",6000);
        //2、创建hbase的连接
        Connection connection;

        {
            try {
                connection = ConnectionFactory.createConnection(configuration);
                System.out.println(connection);
                //3、创建admin对象
                Admin admin = connection.getAdmin();
                System.out.println(admin);
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```
运行程序，控制台会打印出以下类似信息
```
hconnection-0x26ceffa8
org.apache.hadoop.hbase.client.HBaseAdmin@512baff6
```