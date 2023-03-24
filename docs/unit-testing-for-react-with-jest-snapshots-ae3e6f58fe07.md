# 用 Jest 快照反应单元测试

> 原文：<https://levelup.gitconnected.com/unit-testing-for-react-with-jest-snapshots-ae3e6f58fe07>

![](img/9b1f4a1938112a6bf40a6a4e2a1974d9.png)

Jest 快照越来越多地用于测试 React 组件。快照通常用于测试组件的初始呈现，而 DOM 风格测试则用于更具交互性和可操作性的测试。

例如

*   为了测试切换的按钮，我们可以使用快照来测试渲染和样式的初始状态。然后，我们将编写一个 dom 样式测试来测试切换功能，模拟按钮单击，并测试按钮是否如预期的那样在单击时发生了变化。

这篇文章将专门讨论快照，但是为了学习更多关于单元测试的知识，请查看:[https://jestjs.io/docs/en/tutorial-react](https://jestjs.io/docs/en/tutorial-react)。

# **快照测试基础知识 **

*什么是快照测试:*

一个典型的 web 应用程序快照测试用例呈现一个 UI 组件，获取一个快照，然后将其与测试中存储的参考快照文件进行比较。如果两个快照不匹配，测试将失败:要么是意外的更改，要么是引用快照需要更新到 UI 组件的新版本。

*   内置于 Jest 中——不使用额外的库——单元测试类型
*   这只是随 Jest 一起发布的 20 多个断言中的一个
*   针对呈现的 HTML 进行测试
*   这并不意味着要取代逻辑和交互式单元测试，只是对呈现的标记的额外测试

*文档:*

*   [https://jestjs.io/docs/en/snapshot-testing](https://jestjs.io/docs/en/snapshot-testing)

*Jest 文档中我最喜欢的常见问题:*

**快照测试和视觉回归测试有什么区别？**

快照测试和视觉回归测试是两种不同的测试 ui 的方法，它们有不同的用途。视觉回归测试工具对网页进行截屏，并逐个像素地比较结果图像。通过快照测试，值被序列化，存储在文本文件中，并使用 diff 算法进行比较。有不同的权衡需要考虑，我们在 [Jest 博客](https://jestjs.io/blog/2016/07/27/jest-14.html#why-snapshot-testing)中列出了快照测试的原因。

**快照测试会取代单元测试吗？**

快照测试只是 Jest 附带的 20 多个断言中的一个。快照测试的目的不是取代现有的单元测试，而是提供额外的价值并使测试变得轻松。在某些场景中，快照测试可以潜在地消除对一组特定功能(例如 React 组件)的单元测试的需要，但是它们也可以一起工作。

# *为什么使用快照:*

1.  节省时间——测试小型基本组件时，需要编写的代码行更少。

*   快照断言示例:

```
expect(button).toMatchSnapshot();
```

*   没有快照的示例断言:

```
expect(button).toBeTruthy();
expect(button.find(‘ButtonText’)).toHaveLength(1);
expect(button.find(‘ButtonText’).text()).toMatch(‘Save Class’);
expect(button.find(‘Image’)).toHaveLength(1);
expect(button.find(‘Image’).prop(‘src’)).toBeTruthy();
expect(button.find(‘Image’).prop(‘src’)).toContain(‘saveWhite.svg’);
```

*   更详细的测试——不仅意味着测试所有的元素都被渲染，还包括相关的样式。
*   突出显示在共享资源中对其他组件进行的更改，例如可重用的组件/主题值。
*   例如:更改中等字体大小的主题值将导致使用该指定主题值的快照失败。因此，一个看似微小的变化将在所有受影响的组件中突显出来。

*升降机*:

*   您需要读取初始快照，并确保它包含您想要/期望该组件呈现的所有内容。
*   然后，随着变化的到来，你必须承认任何被改变、删除或添加的东西。

*快照测试的良好用例:*

*   如果组件不经常更新——不太依赖可变属性或数据
*   如果组件不太复杂——涉及的逻辑很少，依赖关系很少
*   如果很容易看出您实际测试的是什么——带有简单标记的小组件

*示例:创建并测试横幅组件的快照:*

```
describe('<Banner/>', () => {
  it(‘Snapshot renders correctly’, () => {
    const wrapper = toJson(
      <Banner message=’Snapshot Test’ />
    );
    expect(wrapper).toMatchSnapshot();
  });
})
```

当您运行`yarn test`时，这将在本地`__snapshots__`文件夹中创建一个新文件，其中包含呈现的 html 标记。您将需要通读标记，并验证它是否按预期呈现了组件。一旦通过验证，您就可以提交此代码。

当您继续工作并进行更改时，可能需要更新一些快照。基于个人喜好，你可以运行`yarn test -w`来实时观察更新，或者你可以运行`yarn test`来一次执行所有的测试。当一个变更影响到一个组件的快照时，您将会得到该快照的一个失败通知，并显示代码 diff。在检查 diff 时，您可以决定要么更改代码以保留快照，要么生成新的快照。为了根据代码变化生成新的快照，您可以运行`yarn test -u`来进行更新。同样，您希望提交这个快照变更，以便其他项目参与者可以与之进行比较。

希望这有助于您更好地理解快照测试。编码快乐！:)

参考

*   [https://jest js . io/blog/2016/07/27/jest-14 . html # why-snapshot-testing](https://jestjs.io/blog/2016/07/27/jest-14.html#why-snapshot-testing)
*   [https://jestjs.io/docs/en/snapshot-testing](https://jestjs.io/docs/en/snapshot-testing)
*   [https://jestjs.io/docs/en/tutorial-react](https://jestjs.io/docs/en/tutorial-react)
*   [https://www.valentinog.com/blog/testing-react/](https://www.valentinog.com/blog/testing-react/)
*   [https://medium . com/JavaScript-in-plain-English/should-I-be-writing-snapshot-tests-47da 13a 62085](https://medium.com/javascript-in-plain-english/should-i-be-writing-snapshot-tests-47da13a62085)
*   【https://reactjs.org/docs/testing.html 
*   [https://react js . org/docs/testing-recipes . html # snapshot-testing](https://reactjs.org/docs/testing-recipes.html#snapshot-testing)