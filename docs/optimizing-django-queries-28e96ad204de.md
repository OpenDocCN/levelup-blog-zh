# 如何编写快 35 倍的 Django 查询

> 原文：<https://levelup.gitconnected.com/optimizing-django-queries-28e96ad204de>

## 使用批量查询、预加载外键等。

# 动机

![](img/9edb677ef4bed7072177d8d4300eccb6.png)

你是否已经完成了下一个 Django Web 应用程序，但它运行得太慢了？现在你认为你应该选择 C++或者 Java？来吧，没什么好担心的，对你的代码进行一些重构可能会让你的应用程序快两倍！

例如，您可以一次插入 1000 个对象，而不是逐个插入对象。另一个例子是通过外键获取数据——它每次都命中数据库。你可以预先加载所有需要的数据，你知道吗？

## 准备

我们将在这里玩两个非常简单的模型:

```
**class** Student(models.Model):
    first_name = models.CharField(max_length=100)
    last_name = models.CharField(max_length=100)

**class** Grade(models.Model):
    student = models.ForeignKey(Student, on_delete=models.CASCADE)
    course = models.CharField(max_length=100)
    grade = models.IntegerField(default=0)
```

所以，我们有很多学生，这些学生有一些成绩。没有比这更简单的了。

## 分析你的疑问

首先，您需要看到与数据库的所有交互。也就是说，当您查询一些数据时，我们希望看到 Django 进行的所有 SQL 查询。

为此，请转到您的 *settings.py* 并添加以下代码:

```
LOGGING = {
    **'version'**: 1,
    **'disable_existing_loggers'**: **False**,
    **'handlers'**: {
        **'file'**: {
            **'level'**: **'DEBUG'**,
            **'class'**: **'logging.FileHandler'**,
            **'filename'**: **'sql.log'**,
        },
    },
    **'loggers'**: {
        **'django.db.backends'**: {
            **'handlers'**: [**'file'**],
            **'level'**: **'DEBUG'**,
            **'propagate'**: **True**,
        },
    },
}
```

就是这样。现在，所有与数据库的交互都将存储在 *sql.log* 文件中。

比如，我们添加一些学生，看看 *sql.log* 里都写了什么。

```
student = Student()
student.first_name = **"1"** student.last_name = **"2"** student.save()
```

这是日志:

```
(***0***.***002***) INSERT INTO **"student_student"** (**"first_name"**, **"last_name"**) VALUES (**'1'**, **'2'**); args=[**'1'**, **'2'**]
```

我们可以看到插入学生和查询数据所花费的时间。

## 批量插入

让我们尝试插入 1000 名学生，每个学生有 10 个年级:

```
**for** i **in** range(1000):
    student = Student()
    student.first_name = str(i)
    student.last_name = str(i)
    student.save()
    **for** j **in** range(10):
        grade = Grade()
        grade.student = student
        grade.course = **"Math"** grade.grade = j*10
        grade.save()
```

完成花费了 **9.45** 秒，并且在日志文件中有 **11000** 行。

现在批量做同样的事情:

```
students = []
grades = []
batch_size = 500
**for** i **in** range(1000):
    student = Student()
    student.first_name = str(i)
    student.last_name = str(i)
    students.append(student)
Student.objects.bulk_create(students, batch_size)**for** student **in** Student.objects.all():
    **for** j **in** range(10):
        grade = Grade()
        grade.student = student
        grade.course = **"Math"** grade.grade = j*10
        grades.append(grade)
Grade.objects.bulk_create(grades, batch_size)
```

你不会相信，但它花了 **0.26** 秒，日志由 **5** 行组成！

所以，大约是 **35 *倍*快**！！！即使我们为中间的学生做一个额外的查询！

# 批量更新

很好，更新呢？比如说，我们想让所有的分数都等于 100:

```
grades = Grade.objects.all()
**for** grade **in** grades:
    grade.grade = 100
    grade.save()
```

更新用了 **0.455** 秒。对于 10 000 条记录和一个 SQLite 数据库来说已经不错了:)

好，让我们试试批量更新:

```
batch_size = 500
grades = Grade.objects.all()
**for** grade **in** grades:
    grade.grade = 100
Grade.objects.bulk_update(grades, [**'grade'**], batch_size=batch_size)
```

用了 0.11 秒！ ***快五倍！耶！***

# **预加载数据(选择相关)**

让我们遍历所有记录并打印出每个学生的每个分数:

```
grades = Grade.objects.all()
**for** grade **in** grades:
    print(**f"The student {**grade.student.first_name**} has got {**grade.grade**}%"**)
```

***4.9 秒，10 000 次查询！***

你可能不会相信，但在代码中添加几个符号会使同样的事情在 ***0.19 秒内完成:***

```
grades = Grade.objects.select_related(**"student"**).all()
**for** grade **in** grades:
    print(**f"The student {**grade.student.first_name**} has got {**grade.grade**}%"**)
```

它只做了一个选择查询！

这个想法是，所有相关数据也在同一个查询中提取。

# 批量选择

在前面的示例中，您可能希望在单独的查询中查询一些学生。比如说，我们想让所有的学生在任何一门课程中都得 100 分。在我们的情况下，我们得到所有的学生，但我们不在乎。

请注意，我们在这里不使用“select_related ”:

```
ids = []
grades = Grade.objects.all()
**for** grade **in** grades:
    **if** grade.grade == 100:
        ids.append(grade.student_id)
students = Student.objects.in_bulk(ids)
**for** student **in** students:
    print(student.first_name, student.last_name)
```

这段代码的运行时间也是 0.19 秒。

因此，您收集所有需要的 id 并在一个查询中传递它们。

## 奖金

通过这些文章，您可以让您的应用程序运行得更快:

[](/just-one-index-in-django-makes-your-app-15x-faster-742e2f13108e) [## Django 中的一个索引就能让您的应用速度提高 15 倍！

### 你的应用有了索引会快多少？

levelup.gitconnected.com](/just-one-index-in-django-makes-your-app-15x-faster-742e2f13108e) 

***如何用 Django*** 生成并提供 PDF 文件

[](https://python.plainenglish.io/generate-and-serve-pdf-files-with-django-e3efd9fde7bc) [## 如何用 Django 生成和提供 PDF 文件

### 如何在 Django 中生成 PDF 报告

python .平原英语. io](https://python.plainenglish.io/generate-and-serve-pdf-files-with-django-e3efd9fde7bc) 

***Django:读模型比缓存好***

[](/django-read-models-are-better-than-cache-9e0d11d6b835) [## Django:读模型比缓存好

### 什么是读取模型，如何在 Django 中使用它们？

levelup.gitconnected.com](/django-read-models-are-better-than-cache-9e0d11d6b835) 

***Django 管理堆叠内联示例:多对一关系***

[](/django-admin-stacked-inline-example-many-to-many-relations-e81ae4b59f51) [## Django 管理堆叠内联示例:多对多关系

### 你如何把几样东西加入购物车？

levelup.gitconnected.com](/django-admin-stacked-inline-example-many-to-many-relations-e81ae4b59f51) 

***为什么以及如何用 Python 写冻结的数据类***

[](https://python.plainenglish.io/why-and-how-to-write-frozen-dataclasses-in-python-69050ad5c9d4) [## 为什么以及如何用 Python 编写冻结的数据类

### 冻结和非冻结数据类的区别。

python .平原英语. io](https://python.plainenglish.io/why-and-how-to-write-frozen-dataclasses-in-python-69050ad5c9d4)