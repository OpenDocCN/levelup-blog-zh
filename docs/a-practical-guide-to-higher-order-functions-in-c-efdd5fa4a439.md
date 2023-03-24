# C#高阶函数实用指南

> 原文：<https://levelup.gitconnected.com/a-practical-guide-to-higher-order-functions-in-c-efdd5fa4a439>

![](img/922b00113a5d5d4d7d776c692e312367.png)

如果你已经编程一段时间了，你可能会遇到高阶函数，但是(像我一样)可能没有完全意识到它们有多强大。如果你以前没有听说过他们，不要害怕，我在下面有一个解释。

# 什么是高阶函数？

简单来说，高阶函数就是输入或输出也是函数的函数。

在下面的示例中，Map 函数获取一个特定数据类型和一个函数的列表，并返回一个新列表，该列表中的每个元素都应用了该函数(基本上等同于 LINQs Select 方法)。

```
// definition
List<TOut> Map<TIn, TOut>(List<TIn> list, Func<TIn, TOut> mapper)
{
    var newList = new List<TOut>();
    foreach (var item in list)
    {
        var newItem = mapper(item);
        newList.Add(newItem);
    }return newList;
}// usage
var myList = new List<int> { 1, 2, 3, 4, 5 };int multiplyBy2 (int num) => num * 2;
var multipliedList = Map(myList, multiplyBy2);
// output { 2, 4, 6, 8, 10 };bool isEven (int num) => num % 2 == 0;
var isEvenList = Map(myList, isEven);
// output { false, true, false, true, false };
```

下面是一个返回函数的高阶函数的示例。首先用一个给定的整数调用 Add 函数，它返回一个函数，将第一个整数与任何给定的其他整数相加。

```
Func<int, int> Add(int a) => (int b) => a + b;

var add9 = Add(9);

var sum1 = add9(1);
// output 10var sum2 = add9(2);
// output 11
```

希望现在你能明白为什么高阶函数如此有用；通过让客户端定义自己的输入/输出函数，它们允许以高度灵活的方式重用复杂的代码。例如，如果不使用高阶函数，就必须为他们需要的每一种映射类型定义一个新的映射函数。

# 实际例子—存储库模式

存储库模式是一种非常常见的设计模式，用作数据访问抽象。它允许您执行典型的 CRUD 操作，而无需客户端直接与数据提供者交互。典型的存储库界面可能如下所示:

```
public interface IProductRepository
{
    int Create(Product product);
    bool Update(Product product);
    bool Delete(int id);
    Product GetById(int id);
    IEnumerable<Product> GetAll();
    IEnumerable<Product> GetByCategoryId(int categoryId);
    IEnumerable<Product> GetActive();
    // etc...
}
```

正如您在这个典型示例中看到的，有许多定义都返回`IEnumerable<Product>`，每次您需要另一种特定类型的产品过滤器时，都需要添加到接口中并编写一个全新的实现。这对可维护性不好...

相反，使用更高阶的函数，我们可以定义一个单独的方法来获取接受一个过滤函数作为输入的`IEnumerable<Product>`。这样，客户端可以自由定义自己的过滤器，并且 ProductRepository 不需要不断更新新的实现。

```
interface IProductRepository
{
    // create, update, delete omitted

    IEnumerable<Product> Get(Func<Product, bool> filter = null);
}public class ProductRepository : IProductRepository
{
    private readonly List<Product> _products = new List<Product>(); // data sourcepublic IEnumerable<Product> Get(Func<Product, bool> filter = null)
    {
        // typically you might use the LINQ Where method here
        // but using the foreach to be clear what is happeningif (filter is null) return _products;var filteredList = new List<Product>();
        foreach(var product in _products)
        {
            if(filter(product))
            {
                filteredList.Add(product);
            }
        }return filteredList;
    }
} // client code
var allProducts = _productRepository.Get();
var productsByCategoryId = _productRepository.Get(p => p.CategoryId == 1);
var activeProducts = _productRepository.Get(p => p.Active);
```

# 结论

在本文中，我介绍了高阶函数的概念，并展示了它们在创建易于维护和理解的可重用且灵活的代码时是多么有用。我还给出一个实际的用例，您可能会在. NET 企业应用程序中找到——使存储库模式更加可重用。

我发布的大部分内容都是关于全栈的。NET 和 Vue web 开发。为了确保你不会错过任何帖子，请关注这个博客并[订阅我的简讯](https://samwalpole.com)。如果你觉得这篇文章有帮助，请喜欢它并分享它。你也可以在[推特](https://twitter.com/dr_sam_walpole)上找到我。

*最初发表于*[*【https://samwalpole.com】*](https://samwalpole.com/a-practical-guide-to-higher-order-functions-in-c)*。*