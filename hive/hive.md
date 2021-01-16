# 一、动态分区

```
set hive.exec.dynamic.partition=true;
set hive.exec.dynamic.partition.mode=nonstrict;
```

```
--动态分区属性：设置为true表示开启动态分区功能(默认为false hive.exec.dynamic.partition=true);

--动态分区属性：设置为nonstrict表示允许所有分区都是动态的(默认为strict)

--设置为strict，表示必须保证至少一个分区是静态的
hive.exec.dynamic.partition.mode=strict;

--动态分区属性：每个mapper或reducer可以创建的最大动态分区个数
hive.exec.dynamic.partitions.pernode=100;

--动态分区属性：一个动态分区创建语句可以创建的最大动态分区个数
hive.exec.max.dynamic.partitions=1000;

--动态分区属性：全局可以创建的最大文件个数
hive.exec.max.created.files=10000;
```

二、

```xml
1.beeline 连接不了
首先查看metastore服务是否已启动
```

