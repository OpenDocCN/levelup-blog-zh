# 数据科学家常用的 SQL 函数

> 原文：<https://levelup.gitconnected.com/common-sql-functions-for-data-scientist-dbf1359f885e>

每个数据科学家都应该知道的 SQL 函数。

![](img/52e4ea109d4dc93648037ae54c0eda47.png)

托拜厄斯·菲舍尔在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

## **什么是 SQL？**

结构化查询语言(SQL)允许您从数据库中进行查询。您可以对数据库执行不同的操作，包括:

*   向数据库中添加记录
*   将记录更新到数据库中
*   将记录删除到数据库中
*   创建表格

**什么是数据库？**

数据库是一组存储并易于检索的数据。有不同种类的数据库，如关系数据库和 NoSQL 数据库。在这个例子中，我将使用关系数据库。关系数据库意味着我们的数据中有一个*结构*。我们的表中有列(称为字段)和行(称为记录)。MySQL 是关系数据库的一个例子。NoSQL 数据库的一个例子是 MongoDB。要了解更多关于 NoSQL 数据库的信息，请点击[此处](https://www.mongodb.com/nosql-explained)。

## **数据库的结构**

数据库有几个组成部分:

*   桌子
*   字段(表格中的列)
*   记录(表格中的行)

关系数据库由一个或多个表组成。每个表代表数据库的一个实体。例如，如果我们有一个公司数据库，一个表可以代表公司的雇员或公司的客户。每个表格都是一个网格，由列和行组成。列被称为表中的字段，行被称为表中的记录。在雇员表中，字段可以是雇员的姓名、年龄和工资。在数据库中创建一行作为记录。如果公司有 20 名员工，那么该员工的表中将有 20 条记录。

每个记录将对应于表中的不同字段。例如，如果表中有雇员姓名、年龄和薪水字段，则可能如下所示:

```
Employees table
name    age   salary
Lisa    32    80000
```

如您所见，这些值可以是字符串、数字等。这些字段中的值可以是以下任何数据类型:

*   线
*   Int(整数)
*   浮动
*   布尔代数学体系的

***先说个例子。***

让我们假设你是 x 学校的体育主管，你有一份学校提供的不同运动的电子表格，每项运动的注册人数。您的任务是将它放入 SQL 表中，以便于查询和更新。给你一个运动数据库。

*   创建表格

```
create table sport_types (
    name varchar(100),
    location varchar(100),
    primary key (name)
);
```

在这个命令中，我在`sport_types`表中创建了 2 个字段。一个`name`字段和一个`location`字段。这些字段是`varchar`，它是长度为 100 的可变长度数据类型。主键唯一地指定表中的行。在这种情况下，它是 sport_type 的名称。主键必须总是唯一的**。**

*   **插入**

```
insert into sport_types values ('Track and Field','Field');
insert into sport_types values ('Soccer','Field');
insert into sport_types values ('Gymnastics','Gymnasium');
insert into sport_types values ('Fencing','Gymnasium');
insert into sport_types values ('Swimming','Pool');
insert into sport_types values ('Pole Vaulting','Field');
insert into sport_types values ('Rugby','Field');
insert into sport_types values ('Diving','Pool');
insert into sport_types values ('Hockey','Gymnasium');
insert into sport_types values ('Frisbee','Field');
insert into sport_types values ('Water Polo','Pool');
insert into sport_types values ('Tennis','Court');
insert into sport_types values ('Basketball','Court');
insert into sport_types values ('Boxing','Gymnasium');
insert into sport_types values ('Volleyball','Court');
insert into sport_types values ('Baseball','Field');
```

**我正在将记录插入到`sport_types`表中。请注意，两个字段都是字符串。第一个值对应于名称，第二个值是位置。**

**如果你想继续，我已经创建了另一个表，学生。**

```
create table students (
    id int,
    first_name varchar(40),
    last_name varchar(50),
    gender   varchar(2),
    age int,
 grade int,
    sport varchar(100),
 primary key (id)
);insert into students values (1, 'James', 'Logan', 'M', 17, 84, 'Hockey');
insert into students values (2, 'Jack', 'Gilmore', 'M', 16, 92, 'Swimming');
insert into students values (3, 'Megan', 'Vansel', 'F', 16, 75, 'Swimming');
insert into students values (4, 'Shawn', 'Cattermole', 'M', 18, 60, 'Gymnastics');
insert into students values (5, 'Nicole', 'Lee', 'F', 18, 93, 'Volleyball');
insert into students values (6, 'Maria', 'Hill', 'F', 17, 82, 'Basketball');
insert into students values (7, 'Wes', 'Owen', 'M', 18, 73, 'Gymnastics');
insert into students values (8, 'Evan', 'Parker', 'M', 17, 80, 'Water Polo');
insert into students values (9, 'Archie', 'Dale', 'M', 18, 52, 'Tennis');
insert into students values (10, 'Jack', 'Blaine', 'M', 17, 77, 'Soccer');
insert into students values (11, 'Hannah', 'Kidd', 'F', 19, 91, 'Fencing');
insert into students values (12, 'Ivan', 'Rice', 'M', 16, 82, 'Diving');
insert into students values (13, 'Vanessa', 'Tsu', 'F', 18, 71, 'Pole Vaulting');
insert into students values (14, 'Christopher', 'Beck', 'M', 18, 83, 'Track and Field');
insert into students values (15, 'Clay', 'Powers', 'M', 17, 90, 'Frisbee');
insert into students values (16, 'Owen', 'Parker', 'M', 19, 80, 'Water Polo');
insert into students values (17, 'Jill', 'Powell', 'F', 18, 62, 'Fencing');
insert into students values (18, 'Tina', 'Mane', 'F', 19, 77, 'Boxing');
insert into students values (19, 'Michael', 'James', 'M', 19, 95, 'Swimming');
insert into students values (20, 'Jane', 'Bale', 'F', 17, 91, 'Diving');
insert into students values (21, 'Tommy', 'Jones', 'M', 19, 86, 'Track and Field');
insert into students values (22, 'Matthew', 'Frommers', 'M', 16, 57, 'Rugby');
insert into students values (23, 'Jeffery', 'Walsh', 'M', 18, 86, 'Frisbee');
insert into students values (24, 'John', 'Lee', 'M', 19, 64, 'Baseball');
```

**![](img/3f36f04bccc0e673c5b06a037c566d41.png)**

**照片由 [Serena Repice Lentini](https://unsplash.com/@serenarepice?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄**

*   **挑选**

**选择功能将选择指定的字段。如果要选择所有行，可以选择***

```
SELECT * FROM sport_types;
```

**该查询将返回包含所有字段的表中的所有记录。**

```
SELECT name FROM sport_types;
```

**该查询将返回带有名称字段的表中的所有记录。该位置将不会被返回。**

```
SELECT first_name, gender, sport FROM students;
```

**该查询将从表`Students`中返回具有所选字段、名字、性别和运动的所有记录。**

*   **在哪里**

**当执行 SQL 时，有时我们希望过滤返回的记录。例如，我们想返回游泳池位置的所有运动。**

```
SELECT * FROM sport_types
WHERE location = 'Pool';
```

**这将返回 location =“Pool”的所有记录**

```
| name        | location      |
|------------ | ------------- |
| Swimming    |  Pool         |
| Diving      |  Pool         |
| Water Polo  |  Pool         |
```

*   **在哪里**

**WHERE IN 子句将返回列表或子查询中的记录**

```
SELECT * FROM sport_types
WHERE location IN ('Pool', 'Gymnasium');
```

**该查询将返回位置在“游泳池”或“体育馆”中的所有记录。**

**结果:**

```
| name        | location      |
|------------ | ------------- |
| Gymnastics  |  Gymnasium    |
| Fencing     |  Gymnasium    |
| Swimming    |  Pool         |
| Diving      |  Pool         |
| Hockey      |  Gymnasium    |
| Water Polo  |  Pool         |
| Boxing      |  Gymnasium    |
```

*   **OR 子句**

**OR 子句将返回符合条件的记录。**

```
SELECT * FROM sport_types
WHERE location = 'Pool' 
OR location = 'Gymnasium';
```

**这将返回位置=“游泳池”或位置=“体育馆”的所有记录。请注意，结果类似于前面的查询(在 query 中)。**

*   **AND 子句**

**如果满足所有条件，AND 子句将返回结果。**

```
SELECT * from sport_types
WHERE name = 'Diving'
AND location = 'Pool';
```

**这将返回名称= 'Diving '和位置= 'Pool '的所有记录。**

**结果:**

```
| name        | location      |
|------------ | ------------- |
| Diving      |  Pool         |
```

*   **NOT 子句**

**NOT 子句将返回否定指定条件的记录。**

```
SELECT * FROM sport_types
WHERE location NOT IN ('Pool', 'Gymnasium);
```

**该查询将返回位置不在“游泳池”或“体育馆”中的记录。**

**结果:**

```
| name             | location      |
|----------------  | ------------- |
| Track and Field  |  Field        |
| Soccer           |  Field        |
| Pole Vaulting    |  Field        |
| Rugby            |  Field        |
| Frisbee          |  Field        |
| Tennis           |  Court        |
| Basketball       |  Court        |
| Volleyball       |  Court        |
| Baseball         |  Field        |
```

*   **限制**

**限制功能将限制记录的数量**

```
SELECT * FROM sport_types
WHERE location = 'Pool'
LIMIT 1;
```

**这将返回位置为“Pool”的前 1 个结果。**

```
| name             | location      |
|----------------  | ------------- |
| Swimming         |  Pool         |
```

*   **以...排序**

**ORDER BY 子句将根据条件对记录进行排序。**

```
SELECT * FROM sport_types
WHERE location = 'Pool'
ORDER BY name
```

**该查询将按名称对记录进行升序排序。**

```
| name             | location      |
|----------------  | ------------- |
| Diving           |  Pool         |
| Swimming         |  Pool         |
| Water Polo       |  Pool         |
```

**您也可以按降序排序。通过追加 DESC，顺序将按降序排列。**

```
SELECT * FROM sport_types
WHERE location = 'Pool'
ORDER BY name DESC;
```

**结果:**

```
| name             | location      |
|----------------  | ------------- |
| Water Polo       |  Pool         |
| Swimming         |  Pool         |
| Diving           |  Pool         |
```

**也可以对数值进行排序。用同样的方法，我们可以用数值来排序。**

```
SELECT * FROM students
WHERE gender = 'M'
ORDER BY age;
```

****比较运算符****

*   **等于(=)**

**正如您在前面的查询中看到的，我们使用等号来表示字段是否等于值。我们也可以对数值使用等号。**

*   **大于或等于(> =)**

**大于或等于表示值大于或等于我们指定的值。**

```
SELECT * FROM students
WHERE age >= 18;
```

**该查询返回所有 18 岁或以上学生的记录。**

*   **严格大于(>)**

**该查询将选择所有严格大于指定阈值的记录。**

```
SELECT * FROM students
WHERE age > 18;
```

**该查询返回年龄大于 18 岁的学生的所有记录。**

*   **小于或等于(<=)**

**The less than or equal to signifies values less than or equal to the value we specify.**

```
SELECT * FROM students
WHERE age <= 18;
```

**This query returns all records with students that have the age of 18 or less.**

*   **Strictly Less (**

**This query will select all records that are strictly less than the threshold value specified.**

```
SELECT * FROM students
WHERE age < 18;
```

**This query returns all records with students that have the age less than 18.**

****聚合函数****

*   **计数()**

**COUNT()将计算记录的数量。**

```
SELECT COUNT(*) FROM students;
```

**结果:**

```
| count            |
|----------------  |
| 24               |
```

*   **总和()**

**SUM()将对字段中的所有值求和。**

```
SELECT SUM(grade) FROM students;
```

**该查询将汇总学生表中所有记录的成绩。**

```
| sum              |
|----------------  |
| 1883             |
```

*   **最大()**

**MAX()将获得最大值。**

```
SELECT MAX(grade) FROM students;
```

**该查询将选择所有记录的最高等级。**

```
| max              |
|----------------  |
| 95               |
```

*   **最小值()**

**MIN()将获得最小值。**

```
SELECT MIN(grade) FROM students;
```

**该查询将选择所有记录的最高等级。**

```
| min              |
|----------------  |
| 52               |
```

*   **AVG()**

**AVG()将得到平均值。**

```
SELECT AVG(grade) FROM students;
```

**该查询将选择所有记录的最高等级。**

```
| avg                   |
|---------------------- |
| 78.4583333333333333   |
```

**看看这些值，你会发现我们有很多小数点。我们可以在 SQL 中执行一些函数，将值四舍五入到小数点后 n 位。**

**![](img/975dec025f625387199aa65c06f55cbf.png)**

**照片由[在](https://unsplash.com/@thisisengineering?utm_source=medium&utm_medium=referral) [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄**

*   **轮次**

```
SELECT ROUND(AVG(grade),2) FROM students;
```

**这将把值四舍五入到小数点后两位。**

*   **TRUNC**

```
SELECT TRUNC(AVG(grade),2) FROM students;
```

**这将把值截断到 2 个小数点。**

*   **装天花板**

```
SELECT CEIL(AVG(grade)) FROM students;
```

**这将把值向上舍入到最接近的整数。**

*   **地面**

```
SELECT FLOOR(AVG(grade)) FROM students;
```

**这将把值向下舍入到最接近的整数。**

*   **分组依据**

**GROUP BY 子句将相应地对记录进行分组，您可以执行这些聚合函数中的任何一个来进行更详细的查询。**

**假设你想知道每个年龄段学生的最高分、平均分和最低分是多少。**

```
SELECT age, MAX(grade), ROUND(AVG(grade), 2) as avg, MIN(grade) FROM students
GROUP BY age
ORDER BY age desc
```

**结果:**

```
| age      | max      | avg     | min     |
|--------- | -------- |-------- |-------- |
| 19       | 95       | 82.17   | 64      |
| 18       | 93       | 72.50   | 52      |
| 17       | 91       | 84.00   | 77      |
| 16       | 92       | 76.50   | 57      |
```

**希望这将有助于过滤和收集记录的简单查询！如果您有任何问题，请随时联系我！**