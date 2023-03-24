# C#中的 LINQ 简介

> 原文：<https://levelup.gitconnected.com/introduction-to-linq-in-c-b6cd4cea297e>

INQ 是对代码优化最有用的 C#库之一，它可以让你的代码更短、更清晰、更易读。为了理解 LINQ 的概念，你应该首先理解 [**什么是委托和扩展函数**](https://medium.com/codex/intermediate-c-oop-structs-delegates-4ffd0cd70cf) 。

![](img/318132dc2709f4d1d77436846985a76d.png)

# **什么是 Linq？**

LINQ 是一个系统库，它为 IEnumerable 接口添加了扩展。在 LINQ，我们可以找到许多委托函数，它们为我们提供了有用的函数结构，我们可以使用委托来满足我们的需求。

LINQ 扩展的一个例子是 Sum 函数。我们以 Builder 类为例。

```
public class Builder
{
   public string builderName { get; set; }
   public double builderSalary { get; set; }
   public int builderAge { get; set; }
}
```

如果没有 LINQ，如果我们想要合计所有建筑商的工资，我们必须执行以下操作:

```
public static double GetFullSalary(Builder[] builders_)
{
   double fullSalary = 0.0d;

   for(int i = 0; i < builders_.Length; i++)
   {
     fullSalary += builders_[i].builderSalary;
   }

   return fullSalary;
}
```

然而，Linq 已经有了一个名为 Sum 的扩展函数，它的结构与 GetFullSalary 相同。

**下面的代码是我对真正的 C# Sum 函数实现的建议，它不一定是真正的实现**

```
/*
Because the following code contains some advanced concepts here is a shortcut of what it means:
Step 1 - We create an extention for IEnumerable<T>.
Step 2 - We check to see if the struct we accepted is a numeric type or not. If it's not a numeric type we check if the class\struct supports the addition operator using operator overloading, if both cases fail then we return the default value of the type (Step 2 - Everything between the region "Check_If_Its_Number").
Step 3 - After we know it's a numeric type or a class\struct that supports addition we can sum all the elements and return the result.
*/public static class LINQExtensions
{
        public static T Sum<T>(this IEnumerable<T> li, Func<T, T> func_) where T: struct //we use T:struct because numerics are //structs
        {
            T full = default; //gives 'full' the default value of T#region Check_If_Its_Number
            try
            {
                sbyte mySByte = (sbyte)((dynamic)(default(T))); 
                //if we can cast to sbyte then it's a number type.
            }
            catch
            {                               
//if the class overloads the addition operator we can also make a sum
           if(!(typeof(T).GetMethods().Any(x => x.Name == "op_Addition"))) /*Any is a LINQ function that checks in that case if there is a function called op_Addition which means that the class/struct supports the addition operator.*/
                {
                    Console.WriteLine("The following object cannot use addition operator.");
                    return default(T); /*if can't cast and the class/struct doesn't support addition return the default value of T*/
                }
            }
#endregion for (int i = 0; i < li.Count(); i++)
            {
                full += (dynamic)(func_(li.ElementAt(i))); 
                //sums all the elements
            } return full;
        }
    }
```

这意味着我们可以简单地写而不是写 GetFullSalary 函数。

```
using System.Linq;
...
double fullSalary = builders_.Sum(x => x.builderSalary);
```

正如你在上面的例子中看到的，LINQ 扩展使我们更容易使用它，因为我们可以使用它，而不必构建 GetFullSalary 函数。

# **更多 LINQ 结构**

## 挑选

select 函数返回一个新的 IEnumerable，其大小与调用 IEnumerable 的大小相同。该函数对 IEnumerable 中的每个元素进行委托操作。

```
Builder[] builders = 
{
  new Builder() { builderName = "David" },
  new Builder() { builderName = "John" }, 
  new Builder() { builderName = "Mikel"}
};string[] builderNames = builders.Select(x => x.builderName).ToArray();
//builderName = ["David", "John", "Mikel"]/*--Possible Implementation of Select method--public static class LINQExtensions
{
   public static IEnumerable<TResult> Select<T,TResult>(this IEnumerable<T> li, Func<T, TResult> func_)
   {
      foreach(var item in li)
      {
         yield return func_(item);//adds current item to returned IEnumerable
      }
   }
}*/
```

在上面的例子中，对于数组中的每个构建器，我们将返回构建器的名称。

## 在哪里

where 函数筛选 IEnumerable 中的元素，并返回一个新的 IEnumerable，它只包含通过条件的元素。

```
Builder[] builders = 
{
  new Builder() { builderName = "David", builderSalary = 45.0d },
  new Builder() { builderName = "Mark", builderSalary = 450_000 },
  new Builder() { builderName = "Smith", builderSalary = 100_000}
};IEnumerable<Builder> a = builders.Where(x => x.builderSalary < 100).ToArray();
// a will contain only David because he is the only one with a salary that's below 100/*--possible implementation of Where method--
public static class LINQExtensions
{
public static IEnumerable<T> Where<T>(this IEnumerable<T> li, Func<T, bool> func_)
    {
        foreach (var item in li)
        {
            if(func_(item))
               yield return item;//adds current item to the returned IEnumerable
        }
    }
}*/
```

## 任何和所有

如果 IEnumerable 中有支持该条件的元素，则任何函数都返回 true。如果在没有参数的情况下调用 Any，它将确定 IEnumerable 的长度是否为 0。

如果 IEnumerable 中的所有元素都满足某个条件，All 函数将返回 true。

```
int[] arr = {1,2,3,4};bool biggerThan5 = arr.Any(x => x > 5); //false
bool any = arr.Any(); //true
bool all = arr.All(x => x < 5); //true
```

## 最大&最小&平均

max 和 min 函数将返回 IEnumerable 中的最大/最小值

Average 返回元素的平均值。

```
int[] arr = { 1, 2, 30, 100, 5, 10 };
int max_value = arr.Max(); //100
int min_value = arr.Min(); //1
int average_ = arr.Average();int minSalaryBuilder = builders.Min(x => x.builderSalary); //45
int maxSalaryBuilder = builders.Max(x => x.builderSalary); //450,000
double averageSalary = builders.Average(x => x.builderSalary); //183348.333333
```

## 姓氏和名字

返回条件的最后/第一个匹配项，如果没有条件，将返回 IEnumerable 的最后/第一个元素。

```
int[] arr = { 1, 2, 3, 4, 5, 100, 200, 3, 50 };
int last_ = arr.Last(x => x > 50); //200
int last_element = arr.Last(); // 50
int first_ = arr.First(x => x > 50); //100
int first_element = arr.First(); //1
```

## 横断

Intersect 函数将 IEnumerable 作为参数，并返回一个新的 IEnumerable，其中只保留两个列表共有的元素。

新列表中公共元素的顺序将与调用 IEnumerable(不是参数)的顺序相同。

```
int[] arr = { 1, 2, 3, 4, 6 };
int[] other_ = { 1, 2, 3, 4, 5 };arr = arr.Intersect(other_).ToArray(); //[1,2,3,4]arr = { 1, 2, 3, 5, 4, 6 };
other_ = { 6 , 2, 3, 4, 7 };arr = arr.Intersect(other_); //[2,3,4,6]
```

## 前置和追加

Prepend 函数接受一个参数并返回一个新的 IEnumerable，其中参数位于 IEnumerable 的开头。

Append 函数接受一个参数并返回一个新的 IEnumerable，其中参数位于 IEnumerable 的末尾。

```
int[] arr = { 1, 2, 3, 4 };arr = arr.Prepend(0).ToArray(); //arr = [0,1,2,3,4]
arr = arr.Append(5).ToArray(); //arr = [0,1,2,3,4,5]
```

## 分组依据

GroupBy 函数允许我们创建共享相同标识符的元素组。例如，如果我们有一个工作列表，我们可以组成一组程序员和一组建筑工人。

```
Person[] arr =
{
   new Person(){ job_ = Job.Builder, Name = "Dan"},
   new Person(){ job_ = Job.Builder, Name = "Frank"},
   new Person(){ job_ = Job.Programmer, Name = "Dave"},
};var group_ = arr.GroupBy(x => x.job_).ToDictionary(x => x.Key);
/*group_ = [(Job.Builder):[Person Dan, Person Frank], (Job.Programmer):[Person Dave]]*/
```

## OrderBy

OrderBy 函数按委托参数以升序对 IEnumerable 进行排序。

```
int[] myArr = {4,3,2,1};
myArr = myArr.OrderBy(x => x).ToArray(); //[1,2,3,4]Builder[] arr =
{
   new Builder() { Name = "David", Salary = 180 },
   new Builder() { Name = "Frank", Salary = 120 },
   new Builder() { Name = "Jake", Salary = 150 },
};arr = arr.OrderBy(x => x.Salary).ToArray(); /*[Builder Frank, Builder Jake, Builder David]*/
```

## order by 降序

OrderByDescending 函数按委托参数以降序对 IEnumerable 进行排序。

```
int[] myArr = {1,2,3,4};
myArr = myArr.OrderByDescending(x => x).ToArray(); //[4,3,2,1]Builder[] arr =
{
    new Builder() { Name = "David", Salary = 180 },
    new Builder() { Name = "Frank", Salary = 120 },
    new Builder() { Name = "Jake", Salary = 150 },
 };arr = arr.OrderByDescending(x => x.Salary).ToArray();
//[Builder David, Builder Jake, Builder Frank]
```

# LINQ 没有代表也能运作

## 明显的

Distinct 函数删除所有重复的元素。

```
int[] arr = {1,2,1};
arr = arr.Distinct().ToArray(); //[1,2]
```

## 反面的

Reverse 函数反转 IEnumerable 中元素的位置。

```
int[] arr = {1,5,6,7};
int[] reversed_ = arr.Reverse().ToArray(); //[7,6,5,1]
```

# LINQ 询问

我们可以使用 LINQ 提供的关键字进行 Linq 查询。

让我们举个例子:

```
int[] arr = { 1, 2, 3, 4, 5 };var get_element = from i in arr
                  select i;//----------------------------------------------------
//Equivalent topublic static IEnumerable<T> Copy<T>(IEnumerable<T> arr)
{
   foreach(var item in arr)
   { 
     yield return item; 
   }
}
```

在上面的例子中，I 是 foreach 循环中的项，arr 是我们对其进行 foreach 循环的 IEnumerable，我们在每次迭代中选择项。

因此，上面的例子只是将 arr 的元素复制到 get_element 中。

' **where** 关键字可以帮助我们过滤元素，只将符合条件的元素放入新的 IEnumerable 中。

```
int[] arr = { 1, 2, 3, 4, 5 };var new_ = from i in arr
           where i < 3
           select i; //[1,2]
```

“ **let** 关键字允许我们在查询中创建一个额外的变量。

```
int[] arr = {1, 2, 3, 4, 5};var new_ = from i in arr
           where i < 3
           let num_ = i + 1
           select num_; //[2,3]
```

' **orderby** '关键字允许我们按照升序\降序对 IEnumerable 中的元素进行排序。

```
int[] arr = {1, 2, 3, 10, 5};var new_ = from i in arr
           orderby i descending
           select i; //[10,5,3,2,1]
new_ = from i in arr
       orderby i ascending
       select i + 1;//[2,3,4,6,11]new_ = from i in arr
       where i < 3
       orderby i descending
       select i + 1;//[3,2]
```

“ **group & by** ”关键字允许我们按键对 IEnumerable 进行分组。

```
`Person[] myPerson =
 {
   new Person(){ job_ = Job.Builder, Name = "Mike" },
   new Person(){ job_ = Job.Builder, Name = "Jack"},
   new Person(){ job_ = Job.Teacher, Name = "David"},
   new Person(){ job_ = Job.Programmer, Name = "Ben"}
 };var groups_ = (from i in myPerson
              group i by i.job_).ToDictionary(x => x.Key); /*Now we have a group of Builders, Programmers and teachers. [(Job.Builder):[Person Mike, Person Jack], (Job.Teacher):[Person David],(Job.Programmer):[Person Ben]]*/
```

我们可以使用' **into** '关键字将组的值保存到查询中的变量中。

```
var groups_ = (from i in myPerson
              group i by i.job_ into myProj //myProj stores group
              where myProj.Count() == 1
              select myProj).ToDictionary(x => x.Key); 
//[(Job.Teacher):[Person David],(Job.Programmer):[Person Ben]]
```

恭喜你。您已经完成了 LINQ 简介！:)

# 分级编码

感谢您成为我们社区的一员！[订阅我们的 YouTube 频道](https://www.youtube.com/channel/UC3v9kBR_ab4UHXXdknz8Fbg?sub_confirmation=1)或者加入 [**Skilled.dev 编码面试课程**](https://skilled.dev/) 。

[](https://skilled.dev) [## 编写面试问题+获得开发工作

### 掌握编码面试的过程

技术开发](https://skilled.dev)