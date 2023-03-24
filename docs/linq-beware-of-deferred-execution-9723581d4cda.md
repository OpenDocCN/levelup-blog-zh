# LINQ:当心延期执行

> 原文：<https://levelup.gitconnected.com/linq-beware-of-deferred-execution-9723581d4cda>

![](img/f0dee08a1322bbead57d2643705ab0f4.png)

如果你曾经接触过 C#和。NET，你可能已经遇到过 [LINQ(语言集成查询)](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/linq/)，它允许你直接在 C#语言中使用一系列强大的查询功能。

下面的例子演示了 LINQ 的几个常见特性(注意，我使用的是扩展方法语法，而不是 LINQ 表达式)。在本例中，我们有一个人的列表，并希望获得该列表中成年人的姓名列表。然后，我们将对这些名称进行两次迭代(这将有助于演示立即执行和延迟执行之间的区别)。

使用 LINQ，我们可以:

*   使用`Where`按年龄过滤
*   使用`Select`从 Person 对象映射到 name 字符串
*   使用`ToList`将查询评估为列表

```
public class Person
{
    public string Name { get; set; }
    public int Age { get; set; }
}var people = new List<Person>
{
    new Person { Name = "Sam", Age = 27 },
    new Person { Name = "Suzie", Age = 17 },
    new Person { Name = "Harry", Age = 23 },
};var adultNames = people
    .Where(person => 
    {
        Console.WriteLine("Filtering by age...");
        return person.Age >= 18;
    })
    .Select(person => person.Name)
    .ToList();foreach(var name in adultNames)
    Console.Writeline(name);foreach(var name in adultNames)
    Console.Writeline(name);/* output
Filtering by age
Filtering by age
Filtering by age
Sam
Harry
Sam
Harry
*/
```

注意，在上面的例子中，我们显式地将查询转换为列表。这将立即执行查询，给出一个只包含成人姓名的新列表，然后我们可以对其进行迭代。

如果我们去掉`ToList`，会发生什么？

```
var adultNames = people
    .Where(person => 
    {
        Console.WriteLine("Filtering by age...");
        return person.Age >= 18;
    })
    .Select(person => person.Name);foreach(var name in adultNames)
    Console.Writeline(name);foreach(var name in adultNames)
    Console.Writeline(name);/* output
Filtering by age
Sam
Filtering by age
Filtering by age
Harry
Filtering by age
Sam
Filtering by age
Filtering by age
Harry
*/
```

现在输出看起来很不一样。不是首先进行所有的过滤，然后遍历成人姓名，而是在我们评估每一项之前立即对该项进行过滤。重要的是，过滤也发生在我们每次迭代条目的时候。这就是所谓的延迟执行，因为我们要等到真正需要值来评估查询时才执行。

# 延期执行的好处

看起来延迟执行是 LINQ 的默认行为，除非你明确地告诉它立即求值(使用 ToList，ToDictionary 等)。).所以这么做肯定有好处吧？

## 1.更好的性能

在大多数情况下，预计延迟执行会带来更好的性能，因为您不必一次对整个数据集执行查询。相反，您可以一次对一个项目执行查询，因为您已经对它进行了迭代。

## 2.查询构造

由于查询不需要立即执行，您可以分几个步骤构建它，也许要通过附加的条件逻辑。这为您创建更复杂的查询提供了额外的能力。

```
public IEnumerable<Person> GetNames(IEnumerable<Person> people, bool onlyAdults)
{
    var query = people.AsEnumerable(); if (onlyAdults)
    {
        // only add this filter when onlyAdults is true
        query = query.Where(person => person.Age >= 18);
    } query = query.Select(person => person.Name); return query.ToList();
}
```

## 3.总是重估

由于查询总是在每个枚举上被重新赋值，所以在查询被构造之后，你可以添加/移除/改变你的集合的元素，并且查询将知道这些改变。这样，您就知道您总是在迭代最新的数据。

```
var people = new List<Person>
{
    new Person { Name = "Sam", Age = 27 },
 new Person { Name = "Suzie", Age = 17 },
 new Person { Name = "Harry", Age = 23 },
};

var adultNames = people
 .Where(person => person.Age >= 18)
 .Select(person => person.Name);

foreach(var name in adultNames)
 Console.WriteLine(name);

people.Add(new Person { Name = "Sally", Age = 26 });

foreach(var name in adultNames)
 Console.WriteLine(name);/* output
Sam
Harry
Sam
Harry
Sally
*/
```

# 延期执行的陷阱

尽管目前为止对 LINQs 延迟执行赞不绝口，这篇文章还是受到了我在使用它时遇到的一些问题的启发。如果您在编写代码时不够小心，它的好处之一也是一个陷阱——查询总是被重新评估。

虽然延迟执行通常被认为是一种性能优势，但是如果不小心的话，有时它会大大降低应用程序的速度。任何时候，如果您知道您需要多次重复相同的集合(例如嵌套的 for/foreach 循环)，请确保首先调用 list。否则，您将每次都评估整个集合，这将极大地降低性能。如果源集合特别大，尤其如此，因为即使您的查询进行了大量过滤，查询每次都会应用到整个源集合。

要提到的最后一个陷阱是使用`Select`来运行一组任务。我看到有人争论说这是你根本不应该做的事情，但是我在代码库中看到的足够多，知道这是人们确实在做的事情，也是你应该意识到的事情。想象以下场景:

```
var listOfIds = new List<int> { 1, 5, 8, 15 };var tasks = listOfIds.Select(id => _repository.GetAsync(id));
await Task.WhenAll(tasks);
var results = tasks.Select(task => task.Result).ToList();
```

在上面的例子中，GetAsync 方法实际上为每个 ID 执行了两次，第一次是在第一次声明时，第二次是在使用`ToList`评估查询时。这不仅会因为多次执行昂贵的操作而对性能产生巨大的影响，而且，由于任务是重新执行的，当您真正开始评估它时，并不能保证它会完成。正如您可能想象的那样，如果您正在运行的任务实际上是一个创建或更新操作(是的，我也见过这种情况)，这也是非常危险的。为了安全地执行 get，您需要立即评估查询:

```
var tasks = listOfIds.Select(id => _repository.GetAsync(id)).ToList();
```

在本文中，我介绍了。LINQ 篮网队。我已经展示了它的一些特性，以及为什么它比立即执行更有益。最后，我讨论了使用 LINQ 和 deffered 执行时要注意的一些常见陷阱。

我发布的大部分内容都是关于全栈的。NET 和 Vue web 开发。为了确保你不会错过任何帖子，请关注这个博客并[订阅我的简讯](https://samwalpole.com)。如果你觉得这篇文章有帮助，请喜欢它并分享它。你也可以在[推特](https://twitter.com/dr_sam_walpole)上找到我。

*原载于*[*https://samwalpole.com*](https://samwalpole.com/linq-beware-of-deferred-execution)*。*