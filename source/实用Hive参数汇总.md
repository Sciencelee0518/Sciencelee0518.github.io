---
title: 实用Hive参数汇总
date: 2021-01-22 15:26:39
tags:
---
 set mapred.max.split.size;
 
set mapred.min.split.size

set dfs.block.size


hive>  set io.compression.codecs; --配置了哪些压缩算法

hive> set hive.exec.compress.output;

hive> set mapreduce.output.fileoutputformat.compress.codec;  --使用的压缩算法

set hive.input.format; --hive0.5开始的默认值，这个值会影响map个数的控制

hadoop fs -du -s -h命令查看文件的存储大小结果

set hive.auto.convert.join=true

set hive.mapred.mode=nonstrict;
  
set hive.exec.autorouter.assignEngine=Hive;


