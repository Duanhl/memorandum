
# Spark Source Code

## Architecture

### org.apache.spark.SparkContext

> spark上下文抽象类

- spark设置程序名、运行方式、启动、关闭等
- 从hadoopFile、localFile、HDFS、Cassandra, HBase, Amazon S3、程序生成等地方生成RDD
- 获取环境、配置等信息

### org.apache.spark.scheduler.DAGScheduler

> spark基本的有向无环图调度类，从sparkContext中获取任务并最优调度到cluster中执行任务并汇总

## RDD
