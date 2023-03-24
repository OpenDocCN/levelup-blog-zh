# 存储过程的基本介绍

> 原文：<https://levelup.gitconnected.com/a-basic-introduction-to-stored-procedures-7f5d5a99c11d>

![](img/f6b9c3603063a3c522b978b2ed0ea805.png)

SQL Server 存储过程用于将 Transact-SQL 语句分组为可重用的逻辑单元。在 SQL Server 数据库上工作时，存储过程存储为命名对象。

## 存储过程的好处

在程序中使用存储过程有很多好处。其中包括:

*   SQL Server 中的存储过程可以接受几个输入参数，并返回多个值作为输出参数。
*   存储过程在用户界面和数据库之间提供了一个重要而可靠的安全层。它通过控制数据访问来提供安全性，因为最终用户可以输入或更改数据，但不能编写过程。它有助于保持数据的完整性，因为输入的信息是一致的。
*   使用存储过程可以大大减少客户端和服务器之间的网络流量，因为命令是作为一批(或一组)代码执行的。这意味着唯一发送到网络的是执行过程的调用，而不是昂贵的单独发送每一行代码。
*   此外，它还有助于提高整体性能。因为当您第一次调用存储过程时，它会创建一个*执行计划*并将其存储在缓存中。在存储过程的后续执行中，SQL Server 重用该计划以非常快速和可靠的性能执行存储过程。

## 存储过程基础

创建简单的存储过程

我们将使用一个返回 select 语句的存储过程的示例，您可以按如下方式使用`CREATE PROCEDURE`语句:

```
CREATE PROCEDURE sp_name
AS
BEGIN
    SELECT 
        column1, column2
    FROM 
        table1
    ORDER BY 
        column1;
END;
```

理解上面的语法:

*   usp_PersonData 是我们的存储过程的名称。
*   关键字`AS`分隔了存储过程的标题和主体。
*   语句周围的`BEGIN`和`END`关键字是我们存储过程的主体，也有助于使代码清晰。

快速注意，代替`CREATE PROCEDURE`关键字，你可以使用`CREATE PROC`来使语句更短。

如果您在 SSMS (SQL Server Management Studio)中，可以在对象资源管理器中找到存储过程。就在可编程性>存储过程下面。有时，您需要单击刷新按钮来更新数据库对象。

要执行一个存储过程，可以使用`EXECUTE`或`EXEC`语句，后跟存储过程的名称:

```
EXECUTE sp_name;
EXEC sp_name;
```

要修改现有的存储过程，可以使用`ALTER PROCEDURE`语句。

```
ALTER PROCEDURE sp_name
AS
BEGIN
    SELECT 
        column1, column2
    FROM 
        table1
    ORDER BY 
        column2; 
END;
```

要删除一个存储过程，可以使用`DROP PROCEDURE`或`DROP PROC`语句:

```
DROP PROCEDURE sp_name;
DROP PROC sp_name;
```

## **存储过程中的参数**

另一件需要知道的事情是，你可以将一个参数传递给一个存储过程。允许代码更加通用和可重用的特性。

```
CREATE PROCEDURE sp_name
    (@parameter1 varchar(50))
AS
BEGIN
    SELECT 
        column1, column2
    FROM 
        table1
    WHERE
        column1 = @parameter1; 
END;
```

在本例中，我们将@ pafameter1 传递给存储过程，并使用它来过滤我们的 select 语句。请注意，为了执行我们的存储过程，我们现在应该编写:(我们将' Example '作为参数传递)

```
EXEC sp_name 'Example'
```

要掌握存储过程并有效地使用它们，需要更多的东西。这是一个简短的介绍，希望能鼓励你进一步研究。

希望您首先理解了 Sored 过程为什么有用，然后理解了使用它们的基础知识。您已经学习了如何管理 SQL Server 存储过程，包括创建、执行、修改和删除它们。

感谢您的阅读，祝您度过愉快的一天！继续编码。