# hadoop大数据

## 一．基础

[1.原理概要](hadoop-introduce.md)

[2.hadoop2.x安装](hadoop2.x-install.md)

[3.hadoop3.x安装](hadoop3.x-install.md)

## 二。大数据体系介绍

1.hadoop大数据生态，hdfs分布式存储，yarn分布式资源管理器

2.spark,storm,流式计算框架（基于hadoop生态），还有Samza基于kafka

3.hbase数据库，基于列式存储（基于hadoop生态）

4.hive数据仓库工具，提供sql语句查询（基于hadoop生态）

5.Flink大数据内存计算框架,用于实时计算的场景，不直接依赖hadoop，可以独立运行。

但底层可以选择性依赖hadoop的文件存储HDFS,YARN资源管理器

6.ClickHouse联机分析**OLAP**的**列式数据库**（列式存储）系统

ClickHouse可以构建实时的数仓，也可以构建离线的数仓。

7.elasticsearch分布式检索系统（百度，google,等搜索引擎）

