# Hadoop 权威指南

## 第Ⅰ部分  Hadoop基础知识

- 第1章  初识Hadoop
- 第2章  关于MapReduce
- 第3章  Hadoop分布式文件系统
- 第4章  关于YARN
- 第5章  Hadoop的I/O操作

## 第Ⅱ部分  关于MapReduce

- 第6章  MapReduce应用开发
- 第7章  MapReduce的工作机制
- 第8章  MapReduce的类型与格式
- 第9章  MapReduce的特性

## 第Ⅲ部分  Hadoop的操作

- 第10章  构建Hadoop集群
- 第11章  管理Hadoop

## 第Ⅳ部分  Hadoop相关开源项目

- 第12章  关于Avro
- 第13章  关于Parquet
- 第14章  关于Flume
- 第15章  关于Sqoop
- 第16章  关于Pig
- 第17章  关于Hive
- 第18章  关于Crunch
- 第19章  关于Spark
- 第20章  关于HBase
- 第21章  关于ZooKeeper

## 第Ⅴ部分  案例学习

- 第22章  医疗公司塞纳(Cerner)的可聚合数据
- 第23章  生物数据科学：用软件拯救生命
- 第24章  开源项目Cascading

## 位数-32/64位

hadoop在安装的时候，我们需要知道hadoop版本是32位还是64位。hadoop官网本来提供的都是32位，因为我们大部分都是64位，所以不得不编译。后来官网从hadoop2.5版本开始就提供了64位。
为了防止我们用错，下面我们来实际操作和观察。我们来从官网下载的hadoop.x.x.x.tar.gz安装包.
hadoop是32位还是64位，我们查看本地库即可。解压之后，我们进入`hadoop-2.4.1/lib/native`使用file命令：
``` bash
[hadoop@iZ25gzhy1pdZ native]$ file libhadoop.so.1.0.0
libhadoop.so.1.0.0: ELF 64-bit LSB shared object, x86-64,
 version 1 (SYSV), dynamically linked, not stripped
```

## 默认地址

### hdfs

- hdfs-namenode-http: http://localhost:9870
- hdfs-datanode-http: http://localhost:9864
- hdfs-datanode-address: http://localhost:9866
- hdfs-datanode-ipc.address: http://localhost:9867
- hdfs-secondary-http: http://localhost:9868

### yarn

- yarn-resourcemanager.address: http://localhost:8032/
- yarn-resourcemanager.webapp.address: http://localhost:8088/
- yarn-nodemanager.webapp.address:  http://localhost:8042/

## 伪分布式

> 参考网址：http://blog.csdn.net/edwinbalance/article/details/78640323

### core-site.xml

```
<configuration>
    <property>
        <name>hadoop.tmp.dir</name>
        <value>file:/usr/local/hadoop/tmp</value>
        <description>Abase for other temporary directories.</description>
    </property>
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://localhost:9000</value>
    </property>
</configuration>
```
