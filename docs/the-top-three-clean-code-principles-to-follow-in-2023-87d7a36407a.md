# 2023 年要遵循的三大清洁代码原则

> 原文：<https://levelup.gitconnected.com/the-top-three-clean-code-principles-to-follow-in-2023-87d7a36407a>

![](img/9f18c29597a913dbf95c257d0221354e.png)

[JESHOOTS.COM](https://unsplash.com/@jeshoots?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

*随着世界变得越来越依赖技术，干净代码原则的重要性只会继续增长。所以，一个好的新年决心是写更干净的代码。即使你已经很擅长了，我们开发人员也知道总有需要改进的地方。*

# 为什么编写干净的代码如此重要？

*   提高了可维护性和可读性。干净的代码易于阅读和理解，这使得维护和修改更加容易。在更新或修复代码中的错误时，这可以节省大量的时间和精力。
*   降低错误和缺陷的风险。清晰易懂的代码不太可能包含错误和缺陷。从长远来看，这可以节省大量的时间和精力，因为修复 bug 既费时又费钱。
*   提高协作和团队精神。干净的代码易于其他人阅读和理解，这使得开发人员更容易在项目中协作和工作。这可以提高生产力和效率，并最终导致更好的软件。
*   增加代码重用。干净的代码更容易在其他项目中重用，这可以节省时间和精力。这对于大型项目或涉及多个开发人员的项目尤其重要。

总的来说，编写干净的代码是很重要的，因为它会带来更好、更高效和更有效的软件开发。从长远来看，它可以节省时间和精力，并最终导致更好的软件。

# 每个开发人员都应该遵循的三大原则

2023 年，每个开发人员都应该知道的三大干净代码原则是:

1.  **简单明了。**

在软件开发的世界里，简单和清晰是关键。简单易懂的代码不仅更容易维护和修改，而且还减少了出错的可能性。作为一名开发人员，重要的是努力使代码清晰简洁，易于他人阅读和理解。

简单明了的代码示例如下:

```
function addNumbers(a, b) {
   return a + b; 
}
```

这段代码简单明了，因为它易于阅读和理解。该函数命名明确，只有两个参数。代码也易于阅读，因为它使用了适当的缩进，并且没有不必要的注释或代码行。

简单明了的代码的另一个例子是:

```
const user = {
  name: "John Doe",
  age: 32,
  email: "johndoe@gmail.com"
};
```

这段代码简单明了，因为它易于阅读和理解。变量命名清晰，代码使用适当的缩进，没有不必要的注释或代码行。它也很容易理解，因为它遵循一致的命名约定和结构。

**2。一致性。**

当谈到干净的代码时，一致性是至关重要的。命名约定、格式和结构的一致性使其他人更容易理解和使用您的代码。这也有助于防止混淆，降低出错的风险。

下面是一致代码的一个示例:

```
function addNumbers(a, b) {
  const result = a + b;
  return result;
}

function subtractNumbers(a, b) {
  const result = a - b;
  return result;
}

function multiplyNumbers(a, b) {
  const result = a * b;
  return result;
}

function divideNumbers(a, b) {
  const result = a / b;
  return result;
}
```

这段代码是一致的，因为它遵循一致的命名约定、结构和格式。这些函数被清晰一致地命名，并且它们都使用相同的结构和格式。这使得其他人阅读和理解代码变得容易，并且降低了出错或混淆的风险。

**3。可读性。**

可读性对于干净的代码至关重要。易于阅读和理解的代码不仅更容易使用，而且还减少了对文档的需求，使其他人更容易协作和贡献。作为开发人员，编写易于阅读和理解的代码非常重要，即使对于那些不熟悉项目或语言的人也是如此。

可读代码的示例如下:

```
function addNumbers(a, b) {
  // check if both parameters are numbers
  if (typeof a === "number" && typeof b === "number") {
    // add the numbers and return the result
    return a + b;
  } else {
    // if the parameters are not numbers, return an error message
    throw new Error("both parameters must be numbers");
  }
}
```

这段代码是可读的，因为它易于阅读和理解。代码使用了适当的缩进，并且有清晰简洁的注释来解释代码的每个部分是做什么的。这使得其他人很容易阅读和理解代码，即使他们不熟悉项目或语言。

# 结论

通过遵循这三个干净的代码原则，开发人员可以确保他们的代码不仅易于使用，而且易于维护和修改。这允许更高效和有效的开发，并最终导致更好的软件。

# 参考

[必须知道的干净代码原则](https://medium.com/swlh/the-must-know-clean-code-principles-1371a14a2e75)