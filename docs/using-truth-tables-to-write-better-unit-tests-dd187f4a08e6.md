# 使用真值表编写更好的单元测试

> 原文：<https://levelup.gitconnected.com/using-truth-tables-to-write-better-unit-tests-dd187f4a08e6>

![](img/4f3550b908eefb78c58ee7a703ffaf8f.png)

除了外星阴谋论——这个家伙是用真值表编写单元测试的大力提倡者

几年前，我在一家小初创公司工作，该公司忙于测试，这对我来说是一个巨大的精神转变，因为我在一家大得多的公司工作了两年，编写了大约 0 个测试。是的，我以前的公司依赖于一个 QA 团队，在将我们的代码发布到野外之前，对我们能想象到的所有用户行为场景进行艰苦的测试。令人惊讶的是，这在很大程度上起了作用。

虽然不编写测试肯定会使开发更快(至少在最初)，但单元测试是大多数现代科技公司的标准，现在我发现自己不仅在为编写单元和集成测试的语法而奋斗，而且整个想法非常陌生，我经常不知道从哪里开始。当测试一个可能有许多排列的组件或方法时，我的测试看起来会像这样:

```
//coolMethod.jsconst maybeTruncate = (word, length = 5) => { if(!word.length){ return 'N/A'; } return word.length > length ? `${word.slice(0, length)}...` : word;} //coolMethod.spec.jsit('supports long words', () => { expect(maybeTruncate('really big word')).to.equal('reall...')})it('supports short words', () => { expect(maybeTruncate('short')).to.equal('short')})it('supports custom length', () => { expect(maybeTruncate('kinda big', 10)).to.equal('kinda big')})it('supports no word', () => { expect(maybeTruncate('')).to.equal('N/A')})
```

有一天，我团队中的一个聪明的开发人员审查了我的代码，看了一个与上面类似的测试，给了我一些好的建议:“不要再写那样的测试了，你这个傻瓜”或者类似的话。即使以软件开发者的标准来看，他也有点古怪。然后他打开了他神奇的编码包，向我展示了他写测试的方法。

现在这个聪明的家伙特别适合提供测试建议，因为他已经为 EmberJS 开发人员编写了一个流行的库来模拟数据，他与我分享的这个测试知识的小金块从那时起就一直伴随着我。

## 使用真值表

在布尔代数中，真值表通常用来表示由值的组合产生的数学结果。我们可以通过编写一组条件和预期结果，将同样的逻辑应用于创建测试。这不仅使我们的代码更容易推理，还使测试更简洁，当我们的功能或组件不可避免地发生变化时，可以很容易地修改测试以添加不同的测试条件。

重构上面的测试以使用真值表看起来会像这样:

```
const tests = [
  //test              word              length      expected
  ['no word',         '',               undefined,  'N/A'],
  ['short word',     'Short',           undefined,  'Short'],
  ['long word',      'Not so short',    undefined,  'Not s...'],
  ['custom length',  'Not short',       15,         'Not short'],
]test.forEach((test) => {
  const [assertion, word, length, expected] = test;  
  it(`supports ${assertion}`, () => {
    expect(maybeTruncate(word, length)).to.equal(expected)
  })
})
```

现在，当一个新的场景需要测试时，只需向我们的真值表中插入一些值，然后让我们的测试套件自然运行。这减少了样板代码和过于冗长的测试，还明确定义了我们应该知道的输入可能性，而不是通过搜索一组测试来寻找怪异的边缘情况。

## 结论

编写测试本身就是一种艺术形式，虽然我最初没有看到它们的价值，但我已经开始欣赏它们如何捕捉我最初没有准备好的场景，并且在重构不属于你的代码时，也可以让你感觉舒服得多。

具有良好覆盖率的强大测试套件可以让您更加确信，您在五年前的文件中的第 183 行所做的更改没有破坏管道中的其他东西。真值表很有希望成为一种模式，你可以用它来使你的测试更简洁，更明确，也许更重要的是，写起来更有趣。