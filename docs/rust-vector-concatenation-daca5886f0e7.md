# Rust:向量拼接

> 原文：<https://levelup.gitconnected.com/rust-vector-concatenation-daca5886f0e7>

学习如何使用`.append()`和`.extend()`方法连接 Rust 中的两个向量。使用`.append()`在适当的位置修改第一个向量，或者使用`.extend()`连接而不改变任何一个向量。

![](img/27f985263bba3747eabc6a34e4ea75cd.png)

## 。追加()

要在 Rust 中连接两个向量，可以在第一个向量上使用`.append()`方法。此方法将第二个向量作为参数，并适当修改第一个向量，将第二个向量的元素追加到第一个向量的末尾。这里有一个例子:

```
let mut vec1 = vec![1, 2, 3];
let mut vec2 = vec![4, 5, 6];

vec1.append(&mut vec2);

assert_eq!(vec1, [1, 2, 3, 4, 5, 6]);
assert_eq!(vec2, []);
```

注意，`.append()`方法将对第二个向量的可变引用作为参数，因此必须使用`&mut`关键字将向量传递给该方法。

最后，元件从`vec2`移动到`vec1`，因此`vec2`被清空。

## 。扩展()

或者，您可以使用`.extend()`方法来连接两个向量。这个方法类似于`.append()`，但是它采用一个迭代器作为参数，而不是对另一个向量的可变引用。这里有一个例子:

```
let mut vec1 = vec![1, 2, 3];
let vec2 = vec![4, 5, 6];

vec1.extend(vec2);

assert_eq!(vec1, [1, 2, 3, 4, 5, 6]);
```

如果您想要连接两个向量，而不使用对第二个向量的可变引用，那么`.extend()`方法会很有用。但是，请记住，`.extend()`方法的行为与`.append()`稍有不同，因为它不会按照第二个向量元素在向量中出现的顺序添加它们。相反，它将按照迭代器指定的顺序追加向量元素。在上面的例子中，`vec2`向量是一个普通的向量，所以元素将按照它们在向量中出现的顺序被追加。然而，如果您使用一个迭代器，它以不同的顺序返回向量的元素，`.extend()`方法会以那个顺序追加元素。

此外，`.extend()`方法将移动`vec2`，因此您以后将无法再引用它。

## 源向量上没有可变引用

要在 Rust 中连接两个向量而不使用可变引用，可以使用`.into_iter()`方法从其中一个向量创建一个迭代器，然后将这个迭代器传递给另一个向量的`.extend()`方法。这允许您连接向量，而不需要对它们的可变引用。这里有一个例子:

```
let vec1 = vec![1, 2, 3];
let vec2 = vec![4, 5, 6];

let mut vec3 = vec1;
vec3.extend(vec2.into_iter());

assert_eq!(vec3, [1, 2, 3, 4, 5, 6]);
```

在上面的例子中，`.into_iter()`方法用于从`vec2`向量创建一个迭代器。然后这个迭代器被传递给`vec3`向量上的`.extend()`方法，该方法将两个向量的元素连接在一起，而不改变它们中的任何一个。结果存储在一个新的`vec3`向量中，该向量包含两个向量的连接元素。

尽管如此，这种方法也移动了两个向量`vec1`和`vec2`，因此它们以后不再可用。

## 你想联系吗？

如果你想联系我，请在 LinkedIn 上打电话给我。

另外，请随意查看[我的书籍推荐](https://medium.com/@mr-pascal/my-book-recommendations-4b9f73bf961b)📚。

[](https://mr-pascal.medium.com/my-book-recommendations-4b9f73bf961b) [## 我的书籍推荐

### 在接下来的章节中，你可以找到我对所有日常生活话题的书籍推荐，它们对我帮助很大。

mr-pascal.medium.com](https://mr-pascal.medium.com/my-book-recommendations-4b9f73bf961b) [](https://mr-pascal.medium.com/membership) [## 通过我的推荐链接加入 Medium—Pascal Zwikirsch

### 作为一个媒体会员，你的会员费的一部分会给你阅读的作家，你可以完全接触到每一个故事…

mr-pascal.medium.com](https://mr-pascal.medium.com/membership)