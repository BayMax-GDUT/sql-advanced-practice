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
GROUP BY student.SNO
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

12、查询至少有一门课与学号为"01"的同学所学相同的同学的信息

13、查询和"01"号的同学学习的课程完全相同的其他同学的信息

```sql
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

16、检索"01"课程分数小于60，按分数降序排列的学生信息

17、按平均成绩从高到低显示所有学生的所有课程的成绩以及平均成绩

18、查询各科成绩最高分、最低分和平均分：以如下形式显示：课程ID，课程name，最高分，最低分，平
均分，及格率，中等率，优良率，优秀率（及格为>=60，中等为：70-80，优良为：80-90，优秀为：=90）

19、按各科成绩进行排序，并显示排名

20、查询学生的总成绩并进行排名

21、查询不同老师所教不同课程平均分从高到低显示

22、查询所有课程的成绩第2名到第3名的学生信息及该课程成绩

23、统计各科成绩各分数段人数：课程编号,课程名称,[100-85],[85-70],[70-60],[0-60]及所占百分比

24、查询学生平均成绩及其名次

25、查询各科成绩前三名的记录

26、查询每门课程被选修的学生数

27、查询出只有两门课程的全部学生的学号和姓名

28、查询男生、女生人数

29、查询名字中含有"风"字的学生信息

30、查询同名同性学生名单，并统计同名人数

31、查询1990年出生的学生名单

32、查询每门课程的平均成绩，结果按平均成绩降序排列，平均成绩相同时，按课程编号升序排列

33、查询平均成绩大于等于85的所有学生的学号、姓名和平均成绩

34、查询课程名称为"数学"，且分数低于60的学生姓名和分数

35、查询所有学生的课程及分数情况

36、查询任何一门课程成绩在70分以上的姓名、课程名称和分数

37、查询不及格的课程

38、查询课程编号为01且课程成绩在80分以上的学生的学号和姓名

39、求每门课程的学生人数

40、查询选修"张三"老师所授课程的学生中，成绩最高的学生信息及其成绩

41、查询不同课程成绩相同的学生的学生编号、课程编号、学生成绩

42、查询每门功成绩最好的前两名

43、统计每门课程的学生选修人数（超过5人的课程才统计）。要求输出课程号和选修人数，查询结果按
人数降序排列，若人数相同，按课程号升序排列

44、检索至少选修两门课程的学生学号

45、查询选修了全部课程的学生信息

46、查询各学生的年龄

47、查询本周过生日的学生

48、查询下周过生日的学生

49、查询本月过生日的学生

50、查询下月过生日的学生

