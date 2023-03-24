# 使用 ChakraUI 创建响应卡

> 原文：<https://levelup.gitconnected.com/create-responsive-cards-with-chakraui-18ed895af4a>

## 卡片很难制作，尤其是当里面有图像的时候。在本帖中，我们将看到如何在查克拉伊的帮助下创建感应卡。

# 介绍

从个人网站到电子商务网站，卡片无处不在。响应卡可以成就或破坏你的网站。

![](img/57da7380554a90ca4595a9ecc69b92c8.png)

好吧，怎么做？ [Chakra UI](https://chakra-ui.com) 让入门变得容易，尤其是对于新开发者，因为它提供了 web 开发所需的大部分常用组件。让我们用卡片列出[博客文章](https://www.raravind.com/blog/data-science)。

# 创建卡片

ChakraUI 提供了`Box`组件，使得创建卡片更加容易。为了更好地与搜索引擎打交道，盒子的属性被设置为文章。

将图像宽度设置为 100%，使其占据`Box`的整个宽度。现在，我们可以为图像的高度提供一个固定值，这决定了盒子的高度。用`borderWidth` & `borderRadius`等属性修改框。

```
const Card = ({ img }) => {
  return (
    <Box  p={5} bg="cyan.400"  borderRadius={20} as="article">
    <Image h="350px" objectFit='fill' w="100%" src={img} alt="stock image"/>
   <Heading size="xl" fontWeight="bold"> Blog Title </Heading> 
   <Text noOfLines={2}> Lorem ipsum dolor sit amet, consectetur adipiscing elit. Suspendisse elementum urna quam. Aenean risus turpis, aliquet id diam et, lobortis pellentesque ex. Nulla facilisi. Maecenas. </Text>
    </Box>  
)
```

![](img/0c690647794dfbfca91c08f5329c43ca.png)

# 堆叠的卡片

`SimpleGrid` component 是 ChakraUI 提供的众多将物品堆叠在一起的组件之一。我们可以使用属性`columns`设置每行的卡片数量。我们将每排放 3 张牌。

```
const CardLayout = ({ imgs}) => {
  return (
    <SimpleGrid columns={3} spacing={5}>
      {imgs.map((img, index) => <Card key={index} img={img}/>)}
    </SimpleGrid>
  )
}
```

![](img/855844fed09e9e3f5f102f591166692e.png)

# 响应式布局

![](img/8ddd60d5bfe025158977eb818f6b098f.png)

3 列布局不能很好地跨设备扩展。通过向`columns`属性传递一组值，我们可以使布局对所有设备做出响应。有多简单？

```
const CardResponsive = ({ imgs}) => {
  return (
    <SimpleGrid columns={[1,2,3]} spacing={5}>
      {imgs.map((img, index) => <Card key={index} img={img}/>)}
    </SimpleGrid>
  )
}
```

![](img/92bc1994c3418395e89bda8a67c337a1.png)

固定的图像高度似乎可以跨设备工作，所以我们将使用`useBreakpointValue`钩子将其固定在`Card`组件内。来自 ChakraUI 的钩子非常强大，它根据设备返回不同的值。

```
const h = useBreakpointValue({base: '350px', md: '350px', xl: '300px'})
```

# 外卖食品

ChakraUI 提供了许多类似的组件，知道什么时候使用什么将需要一些时间，但现在对于新开发人员来说入门更容易了。如果你喜欢这篇文章，那就在推特上关注我的类似文章。新年快乐

【https://www.raravind.com】原载于[](https://www.raravind.com/blog/web-development/create-responsive-cards)**。**