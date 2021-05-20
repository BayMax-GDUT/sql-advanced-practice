0503

```sql
#1、查询"01"课程比"02"课程成绩高的学生的信息及课程分数

select student.*, sc1.score as '01', sc2.score as '02' from student
join sc sc1 on student.SNO = sc1.SNO and sc1.CNO = '01'
join sc sc2 on student.SNO = sc2.SNO and sc2.CNO = '02'
where sc1.SCORE > sc2.SCORE

#2、查询"01"课程比"02"课程成绩低的学生的信息及课程分数

select student.*, sc1.score as '01', sc2.score as '02' from student
join sc sc1 on student.SNO = sc1.SNO and sc1.CNO = '01'
join sc sc2 on student.SNO = sc2.SNO and sc2.CNO = '02'
where sc1.SCORE < sc2.SCORE

#3、查询平均成绩大于等于60分的同学的学生编号和学生姓名和平均成绩

select student.SNO, student.SNAME, AVG(s.score)
from student
join sc s on student.SNO = s.SNO
group by student.SNO
having avg(s.SCORE) >= 60

#4、查询平均成绩小于60分的同学的学生编号和学生姓名和平均成绩

select student.SNO, student.SNAME, AVG(s.score)
from student
join sc s on student.SNO = s.SNO
group by student.SNO
having avg(s.SCORE) < 60

#5、查询所有同学的学生编号、学生姓名、选课总数、所有课程的总成绩

select student.SNO, student.SNAME, count(sc.CNO), sum(sc.score) from student
join sc on student.SNO = sc.SNO
group by student.SNO
```



0504

```sql
#6、查询"李"姓老师的数量

select count(*) from teacher where TNAME like '李%'

#7、查询学过"张三"老师授课的同学的信息

select distinct * from student
join sc s on student.SNO = s.SNO
join course c on s.CNO = c.CNO
join teacher t on c.TNO = t.TNO
where t.TNAME = '张三'

#8、查询没学过"张三"老师授课的同学的信息

select student1.SNO from student student1 where student1.SNO not in
(select distinct student2.SNO from student student2
join sc s on student2.SNO = s.SNO
join course c on s.CNO = c.CNO
join teacher t on c.TNO = t.TNO
where t.TNAME = '张三')

#9、查询学过编号为"01"并且也学过编号为"02"的课程的同学的信息

select * from student
join sc sc1 on student.SNO = sc1.SNO and sc1.CNO = '01'
join sc sc2 on student.SNO = sc2.SNO and sc2.CNO = '02'

#10、查询学过编号为"01"但是没有学过编号为"02"的课程的同学的信息

select * from student student1
join sc sc1 on student1.SNO = sc1.SNO and sc1.CNO = '01'
where student1.SNO not in
(select SNO from student student2
join sc sc2 on student2.SNO = sc2.SNO and sc2.CNO = '02'
    )
```



0505

```sql
#11、查询没有学全所有课程的同学的信息

select student.* from student
join sc s on student.SNO = s.SNO
group by s.SNO
having count(s.CNO) < (select count(1) from course)

#12、查询至少有一门课与学号为"01"的同学所学相同的同学的信息

select distinct student.* from student
join sc sc2 on student.SNO = sc2.SNO and sc2.SNO <> '01'
where sc2.CNO in
(select sc1.CNO from sc sc1 where sc1.SNO = '01')

#13、查询和"01"号的同学学习的课程完全相同的其他同学的信息

select student.* from student
join sc on sc.SNO = student.SNO and sc.SNO <> '01' and sc.CNO in (select sc2.CNO from sc sc2 where sc2.SNO = '01')
group by sc.SNO
having count(sc.CNO) = (select count(1) from sc sc1 where sc1.SNO = '01')

#14、查询没学过"张三"老师讲授的任一门课程的学生姓名

select student1.SNAME from student student1
where student1.SNO not in
(select distinct student.SNO from student
join sc s on student.SNO = s.SNO
join course c on s.CNO = c.CNO
join teacher t on c.TNO = t.TNO
where t.TNAME = '张三')

#15、查询两门及其以上不及格课程的同学的学号，姓名及其平均成绩

select student.SNO, student.SNAME, avg(s.SCORE)
from student
join sc s on student.SNO = s.SNO
group by s.SNO
having sum(case when s.SCORE >= 60 then 0 else 1 end) >= 2
```



0506

```sql
#16、检索"01"课程分数小于60，按分数降序排列的学生信息

select student.* from student
join sc s on student.SNO = s.SNO and s.CNO = 01 and s.SCORE < 60
order by s.SCORE desc

#17、按平均成绩从高到低显示所有学生的所有课程的成绩以及平均成绩

select SNO, group_concat(CNO), group_concat(SCORE), avg(SCORE)
from sc
group by SNO
order by avg(SCORE)

#18、查询各科成绩最高分、最低分和平均分：以如下形式显示：课程ID，课程name，最高分，最低分，平均分，及格率，中等率，优良率，优秀率（及格为>=60，中等为：70-80，优良为：80-90，优秀为：>=90）

select course.CNO, course.CNAME, max(s.SCORE),min(s.SCORE), avg(s.SCORE),
sum(case when s.SCORE >= 60 and s.SCORE < 70 then 1 else 0 end) / count(s.SCORE) as '及格率',
sum(case when s.SCORE >= 70 and s.SCORE < 80 then 1 else 0 end) / count(s.SCORE) as '中等率',
sum(case when s.SCORE >= 90 then 1 else 0 end) / count(s.SCORE) as '优秀率'
from course
join sc s on course.CNO = s.CNO
group by s.CNO

#19、按各科成绩进行排序，并显示排名

select A.SNO, A.CNO, A.SCORE, (select count(1) + 1 from sc B where B.SCORE > A.SCORE and B.CNO = A.CNO) as rank
from sc A
order by A.CNO, rank desc


#20、查询学生的总成绩并进行排名

select SNO
from sc
group by SNO
order by sum(SCORE) desc
```



0507

```sql
#21、查询不同老师所教不同课程平均分从高到低显示

select CNO, avg(SCORE)
from sc
group by CNO
order by avg(SCORE)

#22、查询所有课程的成绩第2名到第3名的学生信息及该课程成绩

select student.*, s.CNO, s.SCORE
from student
right join sc s on student.SNO = s.SNO
where (select count(1) + 1 from sc s2 where s2.SCORE > s.SCORE and s.CNO = s2.CNO) in (2, 3)

#23、统计各科成绩各分数段人数：课程编号,课程名称,[100-85],[85-70],[70-60],[0-60]及所占百分比

select sc.CNO, c.CNAME, sum(case when sc.SCORE >= 85 then 1 else 0 end) / count(sc.SCORE) as '[100-85]',
       sum(case when sc.SCORE >= 70 and sc.SCORE < 85 then 1 else 0 end) / count(sc.SCORE) as '[85-70]',
       sum(case when sc.SCORE >= 60 and sc.SCORE < 70 then 1 else 0 end) / count(sc.SCORE) as '[70-60]',
       sum(case when sc.SCORE >= 0 and sc.SCORE < 60 then 1 else 0 end) / count(sc.SCORE) as '[0-60]'
from sc
join course c on sc.CNO = c.CNO
group by sc.CNO

#24、查询学生平均成绩及其名次

SELECT A.SNO,A.AVG,COUNT(*) '名次'
FROM (SELECT SNO,AVG(SCORE) AVG FROM SC GROUP BY SNO) A
INNER JOIN (SELECT SNO,AVG(SCORE) AVG FROM SC GROUP BY SNO) B
ON A.AVG<=B.AVG
GROUP BY A.SNO
ORDER BY A.AVG DESC

#25、查询各科成绩前三名的记录

select student.SNO, student.SNAME, s.CNO, s.SCORE
from student
join sc s on student.SNO = s.SNO
where (select count(1) + 1 from sc sc1 where sc1.SCORE > s.SCORE and sc1.CNO = s.CNO) in (1,2,3)
order by s.CNO, s.SCORE desc
```



0508

```sql
#26、查询每门课程被选修的学生数

select sc.CNO, count(sc.SNO)
from sc
group by sc.CNO

#27、查询出只有两门课程的全部学生的学号和姓名

select student.SNO, student.SNAME
from student
join sc s on student.SNO = s.SNO
group by s.SNO
having count(s.CNO) = 2

#28、查询男生、女生人数

select sum(case when student.SSEX = '男' then 1 else 0 end) '男', sum(case when student.SSEX = '女' then 1 else 0 end) '女'
from student

#29、查询名字中含有"风"字的学生信息

select *
from student
where SNAME like '%风%'

#30、查询同名同姓学生名单，并统计同名人数

select *, count(1)
from student
group by SNAME
having count(1) > 1
```



0509

```sql
#31、查询1990年出生的学生名单

select student.* from student where date_format(student.SAGE, '%Y') = '1990'

#32、查询每门课程的平均成绩，结果按平均成绩降序排列，平均成绩相同时，按课程编号升序排列

select sc.CNO, avg(sc.SCORE)
from sc
group by sc.CNO
order by avg(sc.SCORE) desc, sc.CNO

#33、查询平均成绩大于等于85的所有学生的学号、姓名和平均成绩

select student.SNO. student.SNAME, avg(sc.SCORE)
from student
join sc on student.SNO = sc.SNO
group by sc.SNO
having avg(sc.SCORE) >= 85

#34、查询课程名称为"数学"，且分数低于60的学生姓名和分数

select student.SNAME, sc.SCORE
from student
join sc on student.SNO = sc.SNO and sc.SCORE < 60
join course on sc.CNO = course.CNO and course.CNAME = '数学'

#35、查询所有学生的课程及分数情况

select student.SNAME, course.CNAME, sc.SCORE
from student
join sc on student.SNO = sc.SNO
join course on sc.CNO = course.CNO
```



0510

```sql
#36、查询任何一门课程成绩在70分以上的姓名、课程名称和分数

select student.SNAME, course.CNAME, sc.SCORE
from student
join sc on student.SNO = sc.SNO
join course on sc.CNO = course.CNO
where sc.SCORE >= 70

#37、查询不及格的课程

select student.SNAME, course.CNAME, sc.SCORE
from student
join sc on student.SNO = sc.SNO
join course on sc.CNO = course.CNO
where sc.SCORE < 60

#38、查询课程编号为01且课程成绩在80分以上的学生的学号和姓名

select student.SNO, student.SNAME
from student
join sc on student.SNO = sc.SNO
where sc.CNO = 01 and sc.SCORE >= 80

#39、求每门课程的学生人数

select sc.CNO, count(sc.SNO)
from sc
group by sc.CNO

#40、查询选修"张三"老师所授课程的学生中，成绩最高的学生信息及其成绩

select student.*, sc.SCORE
from student
join sc on student.SNO = sc.SNO
join course on sc.CNO = course.CNO
join teacher on course.TNO = teacher.TNO
where teacher.TNAME = '张三'
order by sc.SCORE desc
limit 1

```



0511

```sql
#41、查询不同课程成绩相同的学生的学生编号、课程编号、学生成绩

select sc.SNO, sc.CNO, sc.SCORE
from sc sc1
join sc sc2 on sc1.SNO = sc2.SNO and sc1.CNO <> sc2.CNO and sc1.SCORE = sc2.SCORE

#42、查询每门功成绩最好的前两名

select sc.SNO, sc.CNO
from sc
where (select count(1) + 1 from sc sc1 where sc1.SCORE > sc.SCORE and sc.CNO = sc1.CNO) in (1, 2)

#43、统计每门课程的学生选修人数（超过5人的课程才统计）。要求输出课程号和选修人数，查询结果按人数降序排列，若人数相同，按课程号升序排列

select sc.CNO, count(sc.SNO)
from sc
group by sc.CNO
having count(sc.SNO) > 5
order by count(sc.SNO) desc, sc.CNO

#44、检索至少选修两门课程的学生学号

select sc.SNO
from sc
group by sc.SNO
having count(sc.CNO) > 1

#45、查询选修了全部课程的学生信息

select student.*
from student
join sc on student.SNO = sc.SNO
group by sc.SNO
having count(sc.CNO) = (select count(1) from course)

```



0512

```sql
#46、查询各学生的年龄

select student.SNO, student.SNAME, date_format(now(), "%Y") - date_format(SAGE, "%Y")
from student

#47、查询本周过生日的学生



#48、查询下周过生日的学生



#49、查询本月过生日的学生

select student.* 
from student
where date_format(SAGE, "%m") = date_format(now(), "%m")

#50、查询下月过生日的学生

select student.* 
from student
where date_format(SAGE, "%m") = date_format(now(), "%m") + 1

```



0513

```sql
#1、查询"01"课程比"02"课程成绩高的学生的信息及课程分数

select student.*,sc1.SCORE, sc2.SCORE
from student,sc sc1, sc sc2
where student.SNO = sc1.SNO and sc1.CNO = 01
and student.SNO = sc2.SNO and sc2.CNO = 02
and sc1.SCORE > sc2.SCORE

#2、查询"01"课程比"02"课程成绩低的学生的信息及课程分数

select student.*, sc1.SCORE, sc2.SCORE
from student, sc sc1, sc sc2
where student.SNO = sc1.SNO and sc1.CNO = 01
and student.SNO = sc2.SNO and sc2.CNO = 02
and sc1.SCORE < sc2.SCORE

#3、查询平均成绩大于等于60分的同学的学生编号和学生姓名和平均成绩

select student.SNO, student.SNAME, avg(sc.SCORE)
from student, sc
where student.SNO = sc.SNO
group by student.SNO
having avg(sc.SCORE) >= 60

#4、查询平均成绩小于60分的同学的学生编号和学生姓名和平均成绩

select student.SNO, student.SNAME, avg(sc.SCORE)
from student, sc
where student.SNO = sc.SNO
group by student.SNO
having avg(sc.SCORE) < 60

#5、查询所有同学的学生编号、学生姓名、选课总数、所有课程的总成绩

select student.SNO, student.SNAME, count(sc.CNO), sum(sc.SCORE)
from student, sc
where student.SNO = sc.SNO
group by student.SNO
```



0514

```sql
#6、查询"李"姓老师的数量

select count(1) from teacher where TNAME like '李%'

#7、查询学过"张三"老师授课的同学的信息

select distinct student.*
from student, sc, course, teacher
where student.SNO = sc.SNO and sc.CNO = course.CNO and course.TNO = teacher.TNO
and teacher.TNAME = '张三'

#8、查询没学过"张三"老师授课的同学的信息

select student.* from student where student.SNO not in
(select distinct student.SNO
from student, sc, course, teacher
where student.SNO = sc.SNO and sc.CNO = course.CNO and course.TNO = teacher.TNO
and teacher.TNAME = '张三')

#9、查询学过编号为"01"并且也学过编号为"02"的课程的同学的信息

select student.*
from student, sc sc1, sc sc2
where student.SNO = sc1.SNO and student.SNO = sc2.SNO and sc1.CNO = 01 and sc2.CNO = 02

#10、查询学过编号为"01"但是没有学过编号为"02"的课程的同学的信息

select student.*
from student, sc sc1, sc sc2
where student.SNO = sc1.SNO and student.SNO <> sc2.SNO and sc1.CNO = 01 and sc2.CNO = 02

```



0515

```sql
#11、查询没有学全所有课程的同学的信息

select student.*
from student,sc
where student.SNO = sc.SNO
group by sc.SNO
having count(sc.CNO) < (select count(1) from course)

#12、查询至少有一门课与学号为"01"的同学所学相同的同学的信息

select distinct student.*
from student, sc
where student.SNO = sc.SNO
and sc.SNO <> 01 and sc.CNO in (select sc.CNO from sc where sc.SNO = 01)

#13、查询和"01"号的同学学习的课程完全相同的其他同学的信息

select student.*
from student, sc
where student.SNO = sc.SNO
and sc.SNO <> 01 and sc.CNO in (select sc.CNO from sc where sc.SNO = 01)
group by sc.SNO
having count(sc.CNO) = (select count(1) from sc where sc.SNO = 01)

#14、查询没学过"张三"老师讲授的任一门课程的学生姓名

select student.SNAME
from student
where student.SNO not in
(select distinct sc.SNO from sc, course, teacher
where sc.CNO = course.CNO and course.TNO = teacher.TNO and teacher.TNAME = '张三')

#15、查询两门及其以上不及格课程的同学的学号，姓名及其平均成绩

select student.SNO, student.SNAME, avg(sc.SCORE)
from student, sc
where student.SNO = sc.SNO
group by sc.SNO
having sum(case when sc.SCORE < 60 then 1 else 0 end) >= 2

```



0516

```sql
#16、检索"01"课程分数小于60，按分数降序排列的学生信息

select student.*
from student, sc
where sc.CNO = 01 and sc.SCORE < 60
order by sc.SCORE desc

#17、按平均成绩从高到低显示所有学生的所有课程的成绩以及平均成绩

select GROUP_CONCAT(CNO), GROUP_CONCAT(SCORE), avg(SCORE)
from sc
group by sc.SNO
order by avg(SCORE) desc

#18、查询各科成绩最高分、最低分和平均分：以如下形式显示：课程ID，课程name，最高分，最低分，平均分，及格率，中等率，优良率，优秀率（及格为>=60，中等为：70-80，优良为：80-90，优秀为：>=90）

select course.CNO, course.CNAME, max(sc.SCORE), min(sc.SCORE), avg(sc.SCORE), sum(case when sc.SCORE >= 60 and sc.SCORE < 70 then 1 else 0 end) / count(sc.SCORE) '优良率', sum(case when sc.SCORE >= 70 and sc.SCORE < 80 then 1 else 0 end) / count(sc.SCORE) '中等率',sum(case when sc.SCORE >= 80 and sc.SCORE < 90 then 1 else 0 end) / count(sc.SCORE) '优良率', sum(case when sc.SCORE >= 90 then 1 else 0 end) / count(sc.SCORE) '优秀率'
from sc, course
where sc.CNO = course.CNO
group by sc.CNO

#19、按各科成绩进行排序，并显示排名

select sc.CNO, sc.SNO, (select count(1) + 1 from sc sc1 where sc1.SCORE > sc.SCORE and sc.CNO = sc1.CNO) 'rank'
from sc
order by sc.CNO, sc.SCORE desc

#20、查询学生的总成绩并进行排名

select sc.SNO, sum(sc.SCORE)
from sc
group by sc.SNO
order by sum(sc.SCORE) desc

```



0518

```sql
#21、查询不同老师所教不同课程平均分从高到低显示

select course.TNO, avg(sc.SCORE)
from sc, course
where sc.CNO = course.CNO
group by sc.CNO
order by avg(sc.SCORE) desc

#22、查询所有课程的成绩第2名到第3名的学生信息及该课程成绩

select student.*, sc.SCORE, (select count(1) + 1 from sc sc1 where sc1.CNO = sc.CNO and sc1.SCORE > sc.SCORE) 'rank'
from student, sc
where student.SNO = sc.SNO
and rank in (2, 3)

#23、统计各科成绩各分数段人数：课程编号,课程名称,[100-85],[85-70],[70-60],[0-60]及所占百分比

select sc.CNO, course.CNAME, sum(case when sc.SCORE >= 85 then 1 else 0 end) / count(sc.SNO) '[100-85]', sum(case when sc.SCORE >= 70 and sc.SCORE < 85 then 1 else 0 end) / count(sc.SNO) '[85-70]', sum(case when sc.SCORE >= 60 and sc.SCORE < 70 then 1 else 0 end) / count(sc.SNO) '[70-60]', sum(case when sc.SCORE < 60 then 1 else 0 end) / count(sc.SNO) '[0-60]'
from sc, course
where sc.CNO = course.CNO
group by sc.CNO

#24、查询学生平均成绩及其名次

SELECT A.SNO, A.AVG, COUNT(*) '名次'
FROM (SELECT SNO, AVG(SCORE) AVG FROM sc GROUP BY sc.SNO) A
JOIN (SELECT SNO, AVG(SCORE) AVG FROM sc GROUP BY sc.SNO) B
ON A.AVG < B.AVG
GROUP BY A.SNO
ORDER BY A.AVG desc

#25、查询各科成绩前三名的记录

select sc.SNO, sc.CNO, sc.SCORE
from sc
where (select count(1) + 1 from sc sc1 where sc.CNO = sc1.CNO) in (1,2,3)
order by sc.CNO, sc.SCORE

```



0518

```sql
#26、查询每门课程被选修的学生数

select sc.CNO, count(sc.SNO)
from sc
group by sc.CNO

#27、查询出只有两门课程的全部学生的学号和姓名

select student.SNO,student.SNAME
from student, sc
where student.SNO = sc.SNO
group by sc.SNO
having count(sc.CNO) = 2

#28、查询男生、女生人数

select sum(case when student.SEX = '男' then 1 else 0 end) '男', sum(case when student.SEX = '女' then 1 else 0 end) '女'
from student

#29、查询名字中含有"风"字的学生信息

select student.*
from student
where student.SNAME like '%风%'

#30、查询同名同性学生名单，并统计同名人数

select SNAME, count(1)
from student
group by SNAME
having count(1) > 1

```



0520

```sql
#31、查询1990年出生的学生名单

select student.*
from student
where date_format(student.SAGE, '%Y') = '1990'

#32、查询每门课程的平均成绩，结果按平均成绩降序排列，平均成绩相同时，按课程编号升序排列

select sc.CNO, avg(sc.SCORE)
from sc
group by sc.CNO
order by avg(sc.SCORE) desc, sc.CNO

#33、查询平均成绩大于等于85的所有学生的学号、姓名和平均成绩

select student.SNO, student.SNAME, avg(sc.SCORE)
from student, sc
where student.SNO = sc.SNO
group by student.SNO
having avg(sc.SCORE) >= 85

334、查询课程名称为"数学"，且分数低于60的学生姓名和分数

select student.SNAME, sc.SCORE
from student, sc, course
where student.SNO = sc.SNO and sc.CNO = course.CNO
and course.CNAME = '数学' and sc.SCORE < 60

#35、查询所有学生的课程及分数情况

select student.SNAME, course.CNAME, sc.SCORE
from student, sc, course
where student.SNO = sc.SNO and sc.CNO = course.CNO

```



























