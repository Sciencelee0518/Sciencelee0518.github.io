---
title: Hive优化
date: 2021-01-21 10:16:30
tags:
---
# 1.MapReduce过程[参考](https://www.cnblogs.com/yx-zhang/p/9572221.html)

MapReduce是将待处理的HDFS数据块经过Map局部化执行，经过网络化Shuffle将数据块按照一定的规则（排序）进行传输，最终数据块儿进入Reduce端进行归并，最终的HDFS块儿即为输出。

##（1）Map

文件形态：File -->Block --> Split --> partition  

- Map个数取决于Split个数；
- Reduce个数取决于Paritions个数；
- Shuffle即分布式排序，而排序操作以k-v形式进行[sql to mr](mei团) 

---
1. Collect阶段将数据放进环形缓冲区，缓冲区分为数据区和索引区。
2. Sort阶段对在同一partition内的索引按照key排序。
3. Spill阶段跟胡排好序的索引将数据按照顺序写到文件中。
4. Merge阶段将Spill生成的小文件分批合并排序成一个大文件

##（2）Reduce  
文件状态：partition-->...-->partitons-->block

1. Copy阶段将Map端的数据分批拷贝到Reduce的缓冲区。
2. Spill阶段将内存缓存区的数据按顺序写到文件中。
3. Merge阶段将溢出的文件合并成一个排序的数据集。
在局部数据排序后，spill之前均小范围的combine优化操作，类似于spark中combineByKey操作。 


此处缺MapReduce的一张示例图，搞好图床后在补[图床](https://www.jianshu.com/p/ea1eb11db63f)


比如wordcount：
map阶段将文档分词，并将每个单词转换成（key，1）操作，然后sort->spill->merge;
 Reduce阶段copy相同单词至同一partition，逐步规约spill，merge成block输出。
#2.Hive优化概览

#3.Shuffle优化

#4.调整Map数和Reduce个数
##（1）Map个数优化
由于Map是按照split对文件的block进行处理（文件形态：File –>Block –> Split –> partition），文件的block数直接决定了Mapper个数：  

1. 关闭Map结束文件合并和MR文件合并
```
 SET hive.merge.mapfiles=false                               //开启Map-only任务结束后的小文件合并
 SET hive.merge.mapredfiles=false                          //开启Map-Reduce任务结束后的小文件合并（默认false）
 SET hive.merge.size.per.task=256000000              //控制每个任务合并小文件后的文件大小（默认256M）
 SET hive.merge.smallfiles.avgsize=150000000      //告诉hadoop什么样的文件属于小文件（默认16M）
```
2. 设置Reduce个数，并且随机打散文件
```
set mapreduce.job.reduces=25;
create table...
distribute by rand(123);
```
3. Split切分和文件类调整
```
SET mapreduce.input.fileinputformat.split.maxsize=256000000;                    //每个split最大值
SET mapreduce.input.fileinputformat.split.minsize.per.node=256000000;     //一个节点上split的最小值 ，多个节点文件是否需要合并
SET mapreduce.input.fileinputformat.split.minsize.per.rack=256000000;       //一个交换机下split的最小值，多个交换机文件是否需要合并
SET hive.input.format=org.apache.hadoop.hive.ql.io.CombineHiveInputFormat;   //执行Map前进行小文件合并
```

当然除了block的个数，其他因素比如文件是否***压缩***，压缩的后的文件是否支持***切分***，比如文件默认的inputfort格式，不同实现类的***split算法***也不同，那么map的个数调优方式也不同。[参考](https://blog.csdn.net/qq_26442553/article/details/99438121)已有详细地说明，以下结合自己的理解和实战多参考进行归纳。

如果hive处理的文件是压缩模式，且压缩模式不支持文件切分，那么这个时候我们只能通过控制参数来减少map个数，而不能通过配置参数来增加map个数。

所以如果对于Hive调优，想通过控制map个数进行调优，首先确定集群是否启动了压缩，且压缩的算法是否直接文件切分，然后再确定集群配置的默认的hive.input.format是什么实现类，不同实现类对于split的算法不同，当然控制map的参数也不同




#5.常见Hive参数设置