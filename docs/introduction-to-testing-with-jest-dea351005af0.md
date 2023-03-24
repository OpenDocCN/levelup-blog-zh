# Jest 测试简介

> 原文：<https://levelup.gitconnected.com/introduction-to-testing-with-jest-dea351005af0>

![](img/467590229733c259596a99f0d4ddaa03.png)

照片由[JESHOOTS.COM](https://unsplash.com/@jeshoots?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

随着应用程序变得如此复杂，我们需要一些方法来验证我们对应用程序的更改没有导致倒退。为此，我们需要单元测试。

使用 Jest test runner，我们可以轻松地将单元测试添加到 JavaScript 应用程序中。它很容易运行，我们可以用它编写大量运行迅速的测试。

在本文中，我们将看看如何从头开始设置 Jest，并用它编写一些测试。同步和异步功能都将被测试。

# 入门指南

首先，我们只需运行几个命令。在我们应用程序的项目文件夹中，我们运行:

```
yarn add --dev jest
```

或者

```
npm install --save-dev jest
```

为我们的 JavaScript 应用程序添加 Jest。

我们下面的例子将有 2 个简单的脚本，每个脚本包含一个有几个功能的模块。

首先，我们创建要测试的代码。我们将测试调用和不调用 API 的代码。

我们创建`example.js`并放入以下代码:

```
const add = (a, b) => a + b;
const identity = a => a;
const deepCopy = (obj, copiedObj) => {
    if (!copiedObj) {
        copiedObj = {};
    }
    for (let prop of Object.keys(obj)) {
        copiedObj = {
            ...copiedObj,
            ...obj
        };
        if (typeof obj[prop] === 'object' && !copiedObj[prop]) {
            copiedObj = {
                ...copiedObj,
                ...obj[prop]
            };
            deepCopy(obj[prop], copiedObj);
        }
    }
    return copiedObj;
}
module.exports = {
    add,
    identity,
    deepCopy
}
```

上面的代码有两个函数，一个是将数字相加的函数，另一个是返回它传入的相同内容的函数。

然后我们用下面的代码创建`request.js`:

```
const fetchJokes = async () => {
    const response = await fetch('[http://api.icndb.com/jokes/random/'](http://api.icndb.com/jokes/random/'));
    return response.json();
};
module.exports = {
    fetchJokes
}
```

上面的代码从 [Chuck Norris 笑话 API](http://www.icndb.com/api/) 中获取随机笑话。

# 为同步代码编写测试

现在我们有了想要测试的代码，我们准备为它们创建一些测试代码。

首先，我们为`example.js`中的函数创建测试。

我们添加了以下测试:

```
const { add, identity } = require('./example');test('adds 1 + 2 to equal 3', () => {
    expect(add(1, 2)).toBe(3);
});test('adds 1 + 2 is truthy', () => {
    const sum = add(1, 2);
    expect(sum).toBeTruthy();
});test('adds 1 + 2 to be defined', () => {
    const sum = add(1, 2);
    expect(sum).not.toBeUndefined();
    expect(sum).toBeDefined();
});test('identity(null) to be falsy', () => {
    expect(identity(null)).toBeFalsy();
    expect(identity(null)).not.toBeTruthy();
});
```

上面的代码非常简单。它从`example.js`导入函数，并用它运行测试。

第一个测试通过传入 2 个数字从`example.js`调用`add`,并检查返回的结果是否是我们所期望的。

下面的两个测试非常相似，除了我们使用不同的匹配器函数来检查结果。

最后一个测试使用`null`运行`identity`函数，他们使用`toBeFalsy`和`not.toBeTruthy`匹配器来检查`null`是否确实是假的。

总之，Jest 有以下匹配器:

*   `toBeNull` —仅匹配`null`
*   `toBeUndefined` —仅匹配`undefined`
*   `toBeDefined` —与`toBeUndefined`相反
*   `toBeTruthy` —匹配任何真实的东西
*   `toBeFalsy` —匹配任何虚假的东西
*   `toBeGreaterThan` —检查一个值是否比我们期望的大
*   `toBeGreaterThanOrEqual` —检查一个值是否大于或等于我们期望的值
*   `toBeLessThan` —检查值是否小于我们的期望值
*   `toBeLessThanOrEqual` —检查值是否小于或等于我们期望的值
*   `toBe` —使用`Object.is`比较数值，检查数值是否相同
*   `toEqual` —递归检查两个对象的每个字段，看它们是否相同
*   `toBeCloseTo` —检查浮点值是否相等。
*   `toMatch` —检查字符串是否与给定的正则表达式匹配
*   `toContain` —检查数组是否有给定值
*   `toThrow` —检查是否抛出异常

这里的是`expect`方法的完整列表[。](https://jestjs.io/docs/en/expect)

通过将它们添加到`example.test.js`中，我们可以如下使用其中一些:

```
test('but there is a "foo" in foobar', () => {
    expect('foobar').toMatch(/foo/);
});test(
    'deep copies an object and returns an object with the same properties and values',
    () => {
        const obj = {
            foo: {
                bar: {
                    bar: 1
                },
                a: 2
            }
        };
        expect(deepCopy(obj)).toEqual(obj);
    }
);
```

在上面的代码中，我们使用`toMatch`匹配器来检查`'foobar'`在第一次测试中是否有`'foo'`子串。

在第二个测试中，我们运行之前添加的`deepCopy`函数，并测试它是否真的用`toEqual`匹配器递归地复制了一个对象的结构。

`toEqual`非常方便，因为它通过递归检查对象中的所有内容来检查深度相等，而不仅仅是检查引用相等。

![](img/7bc4471a4f9eabb1226f33218e1728d3.png)

照片由[拉结](https://unsplash.com/@noguidebook?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 为异步和 HTTP 请求代码编写测试

为 HTTP 测试编写测试需要一些思考。我们希望测试可以在任何有或没有互联网连接的地方进行。此外，测试不应该依赖于实时服务器的结果，因为它应该是自包含的。

这意味着我们必须模拟 HTTP 请求，而不是直接调用我们的代码。

为了测试我们在`request.js`中的`fetchJokes`函数，我们必须模拟这个函数。

为此，我们在项目文件夹中创建一个`__mocks__`文件夹，并在其中创建`requests.js`。文件名应该总是与我们正在模仿的代码的文件名相匹配。

我们可以这样嘲笑它:

```
const fetchJokes = async () => {
    const mockResponse = {
        "type": "success",
        "value": {
            "id": 407,
            "joke": "Chuck Norris originally wrote the first dictionary. The definition for each word is as follows - A swift roundhouse kick to the face.",
            "categories": []
        }
    };
    return Promise.resolve(mockResponse);
};
module.exports = {
    fetchJokes
}
```

真实和模拟的`fetchJokes`函数都返回一个承诺。真实函数从`fetch`函数返回一个承诺，而模拟函数直接调用`Promise.resolve`。

这意味着真实的测试实际上会发出 GET 请求，模拟测试会解析出我们想要测试的响应。

然后在`example.test.js`中，我们添加:

```
jest.mock('./request');
const { fetchJokes } = require('./request');
```

添加到文件的顶部，并且:

```
test('jokes to be fetched', async () => {
    const mockResponse = {
        "type": "success",
        "value": {
            "id": 407,
            "joke": "Chuck Norris originally wrote the first dictionary. The definition for each word is as follows - A swift roundhouse kick to the face.",
            "categories": []
        }
    };
    await expect(fetchJokes()).resolves.toEqual(mockResponse)
});
```

追根究底。

上面的测试只是调用了 mock `fetchJokes`函数，因为我们有:

```
jest.mock('./request');
```

然而，我们仍然需要导入真实的，因为当测试运行时，真实的会被模拟的替换。

`resolves`将解析从模拟`fetchJokes`函数返回的承诺，而`toEqual`将根据承诺的解析值检查响应的结构。

最后，在`package.json`的`scripts`部分，我们放入:

```
"test": "jest"
```

所以我们可以运行`npm test`来运行测试。

那么当我们运行`npm test`时，我们应该得到:

```
PASS  ./example.test.js
  √ adds 1 + 2 to equal 3 (2ms)
  √ adds 1 + 2 is truthy
  √ adds 1 + 2 to be defined (1ms)
  √ identity(null) to be falsy
  √ but there is a "foo" in foobar
  √ deep copies an object and returns an object with the same properties and values (1ms)
  √ jokes to be fetched (1ms)Test Suites: 1 passed, 1 total
Tests:       7 passed, 7 total
Snapshots:   0 total
Time:        1.812s, estimated 2s
Ran all test suites.
```

无论我们何时以何种方式运行测试，它们都应该通过，因为它们彼此独立，不依赖于外部网络请求。

# 结论

当我们编写单元测试时，我们必须确保每个测试都是相互独立的，并且应该发出任何网络请求。

Jest 使测试变得容易，它允许模仿像使 HTTP 请求变得容易的代码这样的东西。

它也很容易设置，并且有许多匹配器用于各种测试。