# sql-advanced-practice
SQL进阶练习题

1、查询"01"课程比"02"课程成绩高的学生的信息及课程分数

```sql
SELECT student.*, sc1.SCORE AS 01score, sc2.SCORE AS 02score FROM student
LEFT JOIN sc sc1 ON sc1.SNO = student.SNO
LEFT JOIN sc sc2 ON sc2.SNO = student.SNO
WHERE sc1.CNO = '01'
AND sc2.CNO = '02'
AND sc1.SCORE > sc2.SCORE
```

2、查询"01"课程比"02"课程成绩低的学生的信息及课程分数

```sql
SELECT student.*, sc1.SCORE AS 01score, sc2.SCORE AS 02score FROM student
LEFT JOIN sc sc1 ON sc1.SNO = student.SNO
LEFT JOIN sc sc2 ON sc2.SNO = student.SNO
WHERE sc1.CNO = '01'
AND sc2.CNO = '02'
AND sc1.SCORE < sc2.SCORE
GROUP BY student.SNO
```



3、查询平均成绩大于等于60分的同学的学生编号和学生姓名和平均成绩

```sql
SELECT student.SNO, student.SNAME, AVG(sc.SCORE) AS avg_score
FROM student LEFT JOIN sc ON student.SNO = sc.SNO
GROUP BY student.SNO
having avg_score >= 60 
```



4、查询平均成绩小于60分的同学的学生编号和学生姓名和平均成绩

```sql
SELECT student.SNO, student.SNAME, AVG(sc.SCORE) AS avg_score
FROM student LEFT JOIN sc ON student.SNO = sc.SNO
GROUP BY student.SNO
having avg_score < 60 
```



5、查询所有同学的学生编号、学生姓名、选课总数、所有课程的总成绩

```sql
SELECT s.SNO, s.SNAME, COUNT(sc.SCORE), SUM(sc.SCORE) FROM student s
LEFT JOIN sc ON s.SNO = sc.SNO
GROUP BY s.SNO
```



6、查询"李"姓老师的数量

```sql
SELECT count(teacher.TNAME) FROM teacher
WHERE teacher.TNAME LIKE '李%'
```



7、查询学过"张三"老师授课的同学的信息

```sql
SELECT distinct student.* FROM student
LEFT JOIN sc ON student.SNO = sc.SNO
LEFT JOIN course ON sc.CNO = course.CNO
LEFT JOIN teacher ON course.TNO = teacher.TNO
WHERE teacher.TNAME = '张三'
```



8、查询没学过"张三"老师授课的同学的信息

```sql
SELECT * FROM student s1 WHERE s1.SNO NOT IN 
(SELECT distinct student.SNO FROM student
LEFT JOIN sc ON student.SNO = sc.SNO
LEFT JOIN course ON sc.CNO = course.CNO
LEFT JOIN teacher ON course.TNO = teacher.TNO
WHERE teacher.TNAME = '张三')
```



9、查询学过编号为"01"并且也学过编号为"02"的课程的同学的信息

```sql
SELECT student.`*` FROM student
LEFT JOIN sc sc1 ON student.SNO = sc1.SNO
LEFT JOIN sc sc2 ON student.SNO = sc2.SNO
WHERE sc1.CNO = '01' and sc2.CNO = '02'
```



10、查询学过编号为"01"但是没有学过编号为"02"的课程的同学的信息

```sql
SELECT distinct student2.`*` FROM student student2
LEFT JOIN sc sc1 ON student2.SNO = sc1.SNO
WHERE sc1.CNO = '01' AND student2.SNO NOT IN (
SELECT student1.SNO FROM student student1
LEFT JOIN sc sc2 ON sc2.SNO = student1.SNO AND sc2.CNO = '02'
)
```



11、查询没有学全所有课程的同学的信息

```sql
select student.*
from student, sc where student.SNO = sc.SNO
group by student.SNO
having count(sc.CNO) < (select count(*) from course)
```



12、查询至少有一门课与学号为"01"的同学所学相同的同学的信息

```sql
select * from student where SNO in (
    select distinct student.SNO
    from student, sc
    where student.SNO = sc.SNO
    and sc.SNO <> 01
    and sc.CNO in (
        select CNO from sc where sc.SNO = 01
        )
    )
```



13、查询和"01"号的同学学习的课程完全相同的其他同学的信息

```sql
# 条件1：上过的课01号同学都上过
# 条件2：学号不等于01号
# 条件3：上过的课数量等于01号同学上过的课数量
SELECT * FROM student
LEFT JOIN sc ON student.SNO = sc.SNO
WHERE sc.CNO IN (SELECT  sc.CNO FROM sc WHERE sc.SNO = 01)
GROUP BY sc.SNO
HAVING COUNT(sc.CNO) = (SELECT COUNT(*) FROM sc WHERE sc.SNO = 01)

SELECT * FROM student WHERE SNO IN (
SELECT student.SNO FROM student
LEFT JOIN sc ON student.SNO = sc.SNO
WHERE sc.CNO IN (SELECT  sc.CNO FROM sc WHERE sc.SNO = 01)
GROUP BY sc.SNO
HAVING COUNT(sc.CNO) = (SELECT COUNT(*) FROM sc WHERE sc.SNO = 01)
)
```



14、查询没学过"张三"老师讲授的任一门课程的学生姓名

```sql
SELECT student.SNAME FROM student WHERE student.SNO NOT IN
(
SELECT distinct student.SNO FROM student
INNER JOIN sc ON student.SNO = sc.SNO
INNER JOIN course ON sc.CNO = course.CNO
INNER JOIN teacher ON course.TNO = teacher.TNO
WHERE teacher.TNAME = '张三'
)
```



15、查询两门及其以上不及格课程的同学的学号，姓名及其平均成绩

```sql
SELECT student.SNO, student.SNAME, AVG(sc.SCORE) FROM student
INNER JOIN sc ON student.SNO = sc.SNO
GROUP BY sc.SNO
HAVING SUM(case when sc.SCORE >= 60 then 0 ELSE 1 END) >= 2
```



16、检索"01"课程分数小于60，按分数降序排列的学生信息

```sql
SELECT student.* FROM student
INNER JOIN sc ON student.SNO = sc.SNO
WHERE sc.CNO = '01' AND sc.SCORE < 60
ORDER BY sc.SCORE desc
```



17、按平均成绩从高到低显示所有学生的所有课程的成绩以及平均成绩

```sql
SELECT SNO, GROUP_CONCAT(CNO), GROUP_CONCAT(SCORE), AVG(SCORE)
FROM sc
GROUP BY SNO
ORDER BY AVG(SCORE) desc
```



18、查询各科成绩最高分、最低分和平均分：以如下形式显示：课程ID，课程name，最高分，最低分，平
均分，及格率，中等率，优良率，优秀率（及格为>=60，中等为：70-80，优良为：80-90，优秀为：=90）

```sql
#一开始直接在count里进行运算，好像是不起作用的
SELECT course.CNO AS 课程id, course.CNAME AS 课程名称, 
MAX(sc.SCORE) AS 最高分, 
MIN(sc.SCORE) AS 最低分,
AVG(sc.SCORE) AS 平均分, 
SUM(case when sc.SCORE >= 60 then 1 ELSE 0 END) / COUNT(sc.SCORE) AS 及格率 ,
SUM(case when sc.SCORE >= 70 AND sc.SCORE < 80 then 1 ELSE 0 END) / COUNT(sc.SCORE) AS 中等率,
SUM(case when sc.SCORE >= 80 AND sc.SCORE < 90 then 1 ELSE 0 END) / COUNT(sc.SCORE) AS 优良率,
SUM(case when sc.SCORE >= 90 then 1 ELSE 0 END) / COUNT(sc.SCORE) AS 优秀率
FROM course, sc WHERE course.CNO = sc.CNO
GROUP BY sc.CNO
```



19、按各科成绩进行排序，并显示排名

```sql
SELECT a.SNO, a.CNO, a.SCORE, (SELECT COUNT(*) + 1 FROM sc WHERE sc.SCORE > a.SCORE AND sc.CNO = a.CNO) rank
FROM sc a
ORDER BY a.CNO, a.SCORE desc
```



20、查询学生的总成绩并进行排名

```sql
select sc.SNO
from sc
group by SNO
order by sum(sc.SCORE) desc 
```



21、查询不同老师所教不同课程平均分从高到低显示

```sql
select course.CNAME, AVG(sc.SCORE)
from course, sc where course.CNO = sc.CNO
group by sc.CNO
order by avg(sc.SCORE) desc 
```



*22、查询所有课程的成绩第2名到第3名的学生信息及该课程成绩

```sql
select student.*, s.CNO, s.SCORE
from student
right join sc s on student.SNO = s.SNO
where (select count(1) + 1 from sc s2 where s2.SCORE > s.SCORE and s.CNO = s2.CNO) in (2, 3)
```



*23、统计各科成绩各分数段人数：课程编号,课程名称,[100-85],[85-70],[70-60],[0-60]及所占百分比

```sql
select sc.CNO, c.CNAME, sum(case when sc.SCORE >= 85 then 1 else 0 end) / count(sc.SCORE) as '[100-85]',
       sum(case when sc.SCORE >= 70 and sc.SCORE < 85 then 1 else 0 end) / count(sc.SCORE) as '[85-70]',
       sum(case when sc.SCORE >= 60 and sc.SCORE < 70 then 1 else 0 end) / count(sc.SCORE) as '[70-60]',
       sum(case when sc.SCORE >= 0 and sc.SCORE < 60 then 1 else 0 end) / count(sc.SCORE) as '[0-60]'
from sc
join course c on sc.CNO = c.CNO
group by sc.CNO
```



*24、查询学生平均成绩及其名次

```sql
SELECT A.SNO,A.AVG,COUNT(*) '名次'
FROM (SELECT SNO,AVG(SCORE) AVG FROM SC GROUP BY SNO) A
INNER JOIN (SELECT SNO,AVG(SCORE) AVG FROM SC GROUP BY SNO) B
ON A.AVG<=B.AVG
GROUP BY A.SNO
ORDER BY A.AVG DESC
```



*25、查询各科成绩前三名的记录

```sql
select student.SNO, student.SNAME, s.CNO, s.SCORE
from student
join sc s on student.SNO = s.SNO
where (select count(1) + 1 from sc sc1 where sc1.SCORE > s.SCORE and sc1.CNO = s.CNO) in (1,2,3)
order by s.CNO, s.SCORE desc
```



26、查询每门课程被选修的学生数

```sql
SELECT sc.CNO, COUNT(sc.SNO) FROM sc
RIGHT JOIN course ON sc.CNO = course.CNO
GROUP BY sc.CNO
```



27、查询出只有两门课程的全部学生的学号和姓名

```sql
SELECT student.SNO, student.SNAME
FROM student
INNER JOIN sc ON student.SNO = sc.SNO
GROUP BY sc.SNO
having COUNT(sc.SNO) = 2
```



28、查询男生、女生人数

```sql
SELECT SUM(case when student.SSEX = '男' then 1 ELSE 0 END) AS '男生',
SUM(case when student.SSEX = '女' then 1 ELSE 0 END) AS '女生'
FROM student
```



29、查询名字中含有"风"字的学生信息

```sql
SELECT student.`*` FROM student
WHERE student.SNAME LIKE '%风%'
```



30、查询同名同性学生名单，并统计同名人数

```sql
SELECT SNAME, COUNT(1) FROM student
GROUP BY SNAME HAVING COUNT(1) > 1
```



31、查询1990年出生的学生名单

```sql
SELECT * FROM student
WHERE date_format(student.SAGE, '%Y') = '1990'
```



32、查询每门课程的平均成绩，结果按平均成绩降序排列，平均成绩相同时，按课程编号升序排列

```sql
SELECT sc.CNO, AVG(sc.SCORE)
FROM sc
GROUP BY sc.CNO
ORDER BY AVG(sc.SCORE) DESC, sc.CNO 
```



33、查询平均成绩大于等于85的所有学生的学号、姓名和平均成绩

```sql
SELECT student.SNO, student.SNAME, AVG(sc.SCORE)
FROM student
INNER JOIN sc ON student.SNO = sc.SNO
GROUP BY student.SNO
having AVG(sc.SCORE) >= 85
```



34、查询课程名称为"数学"，且分数低于60的学生姓名和分数

```sql
SELECT student.SNAME, sc.SCORE
FROM student, sc, course
WHERE student.SNO = sc.SNO AND sc.CNO = course.CNO
AND course.CNAME = '数学' AND sc.SCORE < 60
```



35、查询所有学生的课程及分数情况

```sql
SELECT student.SNAME, course.CNAME, sc.SCORE
FROM student, sc, course
WHERE student.SNO = sc.SNO AND sc.CNO = course.CNO
```



36、查询任何一门课程成绩在70分以上的姓名、课程名称和分数

```sql
SELECT DISTINCT student.SNAME, course.CNAME, sc.SCORE
FROM student, sc, course
WHERE student.SNO = sc.SNO AND sc.CNO = course.CNO
and sc.SCORE >= 70
```



37、查询不及格的课程

```sql
SELECT DISTINCT student.SNAME, course.CNAME, sc.SCORE
FROM student, sc, course
WHERE student.SNO = sc.SNO AND sc.CNO = course.CNO
and sc.SCORE < 60
```



38、查询课程编号为01且课程成绩在80分以上的学生的学号和姓名

```sql
SELECT student.SNO, student.SNAME
FROM student
INNER JOIN sc ON student.SNO = sc.SNO
WHERE sc.CNO = '01' AND sc.SCORE >= 80
```



39、求每门课程的学生人数

```sql
SELECT sc.CNO, COUNT(sc.SNO)
FROM sc
GROUP BY sc.CNO
```



40、查询选修"张三"老师所授课程的学生中，成绩最高的学生信息及其成绩

```
SELECT student.`*`, sc.SCORE
FROM student, sc, course, teacher
WHERE student.SNO = sc.SNO and sc.CNO = course.CNO and course.TNO = teacher.TNO
AND teacher.TNAME = '张三'
ORDER BY sc.SCORE DESC
LIMIT 1
```



41、查询不同课程成绩相同的学生的学生编号、课程编号、学生成绩

```sql
SELECT distinct sc1.*
FROM sc sc1
INNER JOIN sc sc2
ON sc1.SNO = sc2.SNO AND sc1.CNO <> sc2.CNO AND sc1.SCORE = sc2.SCORE
```



*42、查询每门功成绩最好的前两名

```sql
#解法1
(SELECT sc.`*`
FROM sc
WHERE sc.CNO = '01'
ORDER BY sc.SCORE DESC
LIMIT 2)
UNION
(SELECT sc.`*`
FROM sc
WHERE sc.CNO = '02'
ORDER BY sc.SCORE DESC
LIMIT 2)
UNION
(SELECT sc.`*`
FROM sc
WHERE sc.CNO = '03'
ORDER BY sc.SCORE DESC
LIMIT 2)

#解法2
select sc.SNO, sc.CNO
from sc
where (select count(1) + 1 from sc sc1 where sc1.SCORE > sc.SCORE and sc.CNO = sc1.CNO) in (1, 2)
```



43、统计每门课程的学生选修人数（超过5人的课程才统计）。要求输出课程号和选修人数，查询结果按
人数降序排列，若人数相同，按课程号升序排列

```sql
SELECT sc.CNO, count(sc.SNO)
FROM sc
GROUP BY sc.CNO
HAVING COUNT(sc.CNO) >= 5
ORDER BY COUNT(sc.CNO) DESC , sc.CNO
```



44、检索至少选修两门课程的学生学号

```sql
SELECT sc.SNO
FROM sc
GROUP BY sc.SNO
HAVING COUNT(sc.CNO) >= 2
```



45、查询选修了全部课程的学生信息

```sql
SELECT student.`*` 
FROM student
INNER JOIN sc ON student.SNO = sc.SNO
GROUP BY sc.SNO
HAVING COUNT(sc.CNO) = (SELECT COUNT(*) FROM course)
```



46、查询各学生的年龄

```sql
SELECT student.SNO, student.SNAME, (DATE_FORMAT(NOW(), '%Y') - DATE_FORMAT(student.SAGE, '%Y')) AS '年龄'
FROM student
```



*47、查询本周过生日的学生

*48、查询下周过生日的学生

49、查询本月过生日的学生

```sql
SELECT student.`*`
FROM student
WHERE DATE_FORMAT(student.SAGE, '%m') = DATE_FORMAT(NOW(), '%m')
```



50、查询下月过生日的学生

```sql
SELECT student.`*`
FROM student
WHERE DATE_FORMAT(student.SAGE, '%m') = DATE_FORMAT(NOW(), '%m') + 1
```



