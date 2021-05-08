

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

select student.SNO, student.SNAME, avg(s.SCORE), (select count(1) + 1 from sc sc1 group by sc1.SNO having avg(sc1.SCORE) > avg(s.SCORE)) as rank
from student
join sc s on student.SNO = s.SNO
group by s.SNO
order by rank


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



