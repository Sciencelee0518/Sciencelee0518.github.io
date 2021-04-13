---
title: sql50题
date: 2021-01-25 14:36:38
tags:
---

很早之前就看到过这份[SQL50题](https://www.jianshu.com/p/476b52ee4f1b)，一直没有时间刷，最近刚好有时间，遂用HQL过了一遍；看起来18题之后的题目难度与前18题趋同，故只刷到18题，记之。  

# Hive建表语句
```  
--学生表  
drop table if exists hdp_anjuke_dw_stage.lkx_student;  
create table hdp_anjuke_dw_stage.lkx_student(sid string,sname string,sage string,ssex string);  
insert into hdp_anjuke_dw_stage.lkx_student values('01' , '赵雷' , '1990-01-01' , '男');  
insert into hdp_anjuke_dw_stage.lkx_student values('02' , '钱电' , '1990-12-21' , '男');  
insert into hdp_anjuke_dw_stage.lkx_student values('03' , '孙风' , '1990-05-20' , '男');  
insert into hdp_anjuke_dw_stage.lkx_student values('04' , '李云' , '1990-08-06' , '男');  
insert into hdp_anjuke_dw_stage.lkx_student values('05' , '周梅' , '1991-12-01' , '女');  
insert into hdp_anjuke_dw_stage.lkx_student values('06' , '吴兰' , '1992-03-01' , '女');  
insert into hdp_anjuke_dw_stage.lkx_student values('07' , '郑竹' , '1989-07-01' , '女');  
insert into hdp_anjuke_dw_stage.lkx_student values('09' , '张三' , '2017-12-20' , '女');  
insert into hdp_anjuke_dw_stage.lkx_student values('10' , '李四' , '2017-12-25' , '女');  
insert into hdp_anjuke_dw_stage.lkx_student values('11' , '李四' , '2017-12-30' , '女');  
insert into hdp_anjuke_dw_stage.lkx_student values('12' , '赵六' , '2017-01-01' , '女');  
insert into hdp_anjuke_dw_stage.lkx_student values('13' , '孙七' , '2018-01-01' , '女');  

--课程表  
drop table if exists hdp_anjuke_dw_stage.lkx_course;  
create table hdp_anjuke_dw_stage.lkx_course(cid string,cname string,tid string)   ;  
insert into hdp_anjuke_dw_stage.lkx_course values('01' , '语文' , '02')           ;  
insert into hdp_anjuke_dw_stage.lkx_course values('02' , '数学' , '01')           ;  
insert into hdp_anjuke_dw_stage.lkx_course values('03' , '英语' , '03')           ;  

--教师表  
drop table if exists hdp_anjuke_dw_stage.lkx_teacher;  
create table hdp_anjuke_dw_stage.lkx_teacher(tid string,tname string)  ;  
insert into hdp_anjuke_dw_stage.lkx_teacher values('01' , '张三')      ;  
insert into hdp_anjuke_dw_stage.lkx_teacher values('02' , '李四')      ;  
insert into hdp_anjuke_dw_stage.lkx_teacher values('03' , '王五')      ;  

--成绩表  
drop table if exists hdp_anjuke_dw_stage.lkx_sc;  
create table hdp_anjuke_dw_stage.lkx_sc(sid string,cid string,score  int)       ;  
insert into hdp_anjuke_dw_stage.lkx_sc values('01' , '01' , 80)                                        ;  
insert into hdp_anjuke_dw_stage.lkx_sc values('01' , '02' , 90)                                        ;  
insert into hdp_anjuke_dw_stage.lkx_sc values('01' , '03' , 99)                                        ;  
insert into hdp_anjuke_dw_stage.lkx_sc values('02' , '01' , 70)                                        ;  
insert into hdp_anjuke_dw_stage.lkx_sc values('02' , '02' , 60)                                        ;  
insert into hdp_anjuke_dw_stage.lkx_sc values('02' , '03' , 80)                                        ;  
insert into hdp_anjuke_dw_stage.lkx_sc values('03' , '01' , 80)                                        ;  
insert into hdp_anjuke_dw_stage.lkx_sc values('03' , '02' , 80)                                        ;  
insert into hdp_anjuke_dw_stage.lkx_sc values('03' , '03' , 80)                                        ;  
insert into hdp_anjuke_dw_stage.lkx_sc values('04' , '01' , 50)                                        ;  
insert into hdp_anjuke_dw_stage.lkx_sc values('04' , '02' , 30)                                        ;  
insert into hdp_anjuke_dw_stage.lkx_sc values('04' , '03' , 20)                                        ;  
insert into hdp_anjuke_dw_stage.lkx_sc values('05' , '01' , 76)                                        ;  
insert into hdp_anjuke_dw_stage.lkx_sc values('05' , '02' , 87)                                        ;  
insert into hdp_anjuke_dw_stage.lkx_sc values('06' , '01' , 31)                                        ;  
insert into hdp_anjuke_dw_stage.lkx_sc values('06' , '03' , 34)                                        ;  
insert into hdp_anjuke_dw_stage.lkx_sc values('07' , '02' , 89)                                        ;  
insert into hdp_anjuke_dw_stage.lkx_sc values('07' , '03' , 98)                                        ;  
```

2.查询平均成绩大于等于 60 分的同学的学生编号和学生姓名和平均成绩  
```
SELECT b.sid,b.sname,a.avg_socre    
from (  
	SELECT sid,avg(score) as avg_socre  
	from hdp_anjuke_dw_stage.lkx_sc  
	group by sid  
	having avg(score)>=60  
)a  
left join hdp_anjuke_dw_stage.lkx_student b on a.sid=b.sid  
```
3.查询在 SC 表存在成绩的学生信息  
```
select a.*  
from hdp_anjuke_dw_stage.lkx_student a  
where exists (  
select sid  
from hdp_anjuke_dw_stage.lkx_sc b    
where a.sid=b.sid  
)  
;

select a.*  
from hdp_anjuke_dw_stage.lkx_student a  
where a.sid in (  
select sid  
from hdp_anjuke_dw_stage.lkx_sc  
group by sid   
)  
;  

select a.*  
from hdp_anjuke_dw_stage.lkx_student a  
join (  
select sid  
from hdp_anjuke_dw_stage.lkx_sc  
group by sid   
) b on a.sid=b.sid  
```
4.查询所有同学的学生编号、学生姓名、选课总数、所有课程的总成绩(没成绩的显示为 null )  
```
select a.sid,a.sname  
,count(b.sid) as course_num  
,sum(b.score) as course_score  
from hdp_anjuke_dw_stage.lkx_student a  
left join hdp_anjuke_dw_stage.lkx_sc b on a.sid=b.sid  
group by  a.sid,a.sname  
```
5.查询「李」姓老师的数量          
```
select count(1) as cnt 
from hdp_anjuke_dw_stage.lkx_teacher
where tname like '李%'
```
6.查询学过「张三」老师授课的同学的信息  
``` 
select a.* 
from hdp_anjuke_dw_stage.lkx_student a 
where a.sid in(
	select  c.sid
	from hdp_anjuke_dw_stage.lkx_teacher a
	left join hdp_anjuke_dw_stage.lkx_course b on a.tid=b.tid
	left join hdp_anjuke_dw_stage.lkx_sc     c on b.cid=c.cid
	where a.tname='张三'
)
;
```

7.查询没有学全所有课程的同学的信息  
```
select a.sid  
,count(case when b.cid in ('01') then 1 end) as flag1  
,count(case when b.cid in ('02') then 1 end) as flag2  
,count(case when b.cid in ('03') then 1 end) as flag3  
from  hdp_anjuke_dw_stage.lkx_student a  
left join hdp_anjuke_dw_stage.lkx_sc b on a.sid=b.sid   
group by a.sid 
```
10.查询没学过"张三"老师讲授的任一门课程的学生姓名
```
select a.sname  
from hdp_anjuke_dw_stage.lkx_student a   
left join (  
	select c.sid  
	from hdp_anjuke_dw_stage.lkx_teacher a   
	left join hdp_anjuke_dw_stage.lkx_course b on a.tid=b.tid  
	left join hdp_anjuke_dw_stage.lkx_sc     c on b.cid=c.cid  
	where a.tname='张三'   
)b on a.sid=b.sid  
where b.sid is NULL  
;  
```

11.查询两门及其以上不及格课程的同学的学号，姓名及其平均成绩
```
SELECT b.sid,b.sname,a.avg_socre  
from (  
	SELECT sid,avg(score) as avg_socre  
	from hdp_anjuke_dw_stage.lkx_sc  
	where score<60  
	group by sid  
	having count(1)>=2  
)a  
left join hdp_anjuke_dw_stage.lkx_student b on a.sid=b.sid   
;
```
12.检索" 01 "课程分数小于 60，按分数降序排列的学生信息

```
SELECT b.sid,b.sname,b.sage,b.ssex,a.score  
from (    
	SELECT sid,score  
	from hdp_anjuke_dw_stage.lkx_sc    
	where cid='01' and score<60    
)a    
left join hdp_anjuke_dw_stage.lkx_student b on a.sid=b.sid    
order by a.score desc   
limit 1000  
;
```
13.按平均成绩从高到低显示所有学生的所有课程的成绩以及平均成绩
```
select a.sid,b.cid,b.score,a.avg_socre  
from (  
	SELECT sid,avg(score) as avg_socre    
	from hdp_anjuke_dw_stage.lkx_sc    
	group by sid    
)a  
left join hdp_anjuke_dw_stage.lkx_sc b on a.sid=b.sid   
order by avg_socre desc   
limit 100   
;
```

14.查询各科成绩最高分、最低分和平均分：
以如下形式显示：课程 ID，课程 name，最高分，最低分，平均分，及格率，中等率，优良率，优秀率
及格为>=60，中等为：70-80，优良为：80-90，优秀为：>=90
要求输出课程号和选修人数，查询结果按人数降序排列，若人数相同，按课程号升序排列
```
select *
from(
     SELECT a.cid
	,b.cname  as cname 
	,count(1) as cnt
	,max(a.score) as score_max
	,min(a.score) as score_min
	,avg(a.score) as socre_avg
    ,count(case when a.score>=60 then a.sid end)/count(1) as jige
	,count(case when a.score>=70 and  a.score<80 then a.sid end)/count(1) as zhong
    ,count(case when a.score>=80 and  a.score<90 then a.sid end)/count(1) as liang
    ,count(case when a.score>=90 then a.sid end)/count(1) as you
	from hdp_anjuke_dw_stage.lkx_sc a
	left join hdp_anjuke_dw_stage.lkx_course b on a.cid=b.cid
	group by a.cid,b.cname
)a
order by a.cnt desc,a.cid asc	
limit 100
;
```

15.按各科成绩进行排序，并显示排名， Score 重复时保留名次空缺
15.1 按各科成绩进行排序，并显示排名， Score 重复时合并名次
```
select sid,cid,score
,row_number() over(partition by cid order by score desc) as row_number
,rank() over(partition by cid order by score desc) as rank
,dense_rank() over(partition by cid order by score desc) as dense_rank
from hdp_anjuke_dw_stage.lkx_sc   
```

16.查询学生的总成绩，并进行排名，总分重复时保留名次空缺
16.1 查询学生的总成绩，并进行排名，总分重复时不保留名次空缺	

```
select sid
,row_number() over(partition by 1 order by score desc) as row_number
,rank() over(partition by 1 order by score desc) as rank
,dense_rank() over(partition by 1 order by score desc) as dense_rank
from (
	select sid,sum(score) as score 
	from hdp_anjuke_dw_stage.lkx_sc 
	group by sid
)a
```

17.统计各科成绩各分数段人数：课程编号，课程名称，[100-85]，[85-70]，[70-60]，[60-0] 及所占百分比
```
select a.cid,b.cname 
,count(case when a.score<=100 and a.score>85 then 1 end)/count(1) as score_85_100
,count(case when a.score<=85 and a.score>70 then 1 end)/count(1)  as score_70_85
,count(case when a.score<=70 and a.score>60 then 1 end)/count(1)  as score_60_70
,count(case when a.score<=60 and a.score>=0 then 1 end)/count(1)  as score_0_60
from hdp_anjuke_dw_stage.lkx_sc a
left join hdp_anjuke_dw_stage.lkx_course b on a.cid=b.cid
group by a.cid,b.cname 
```

18.查询各科成绩前三名的记录
```
select sid,cid,score
from (
	select sid,cid,score
	,row_number() over(partition by cid order by score desc) as row_number
	from hdp_anjuke_dw_stage.lkx_sc 
	;
)a 
where row_number<=3
;
```