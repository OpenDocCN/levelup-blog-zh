# JavaScript 中克隆对象的正确方法

> 原文：<https://levelup.gitconnected.com/proper-ways-to-clone-an-object-in-javascript-c7dc41bab96c>

![](img/a0d6bcdcee3ef9372528237284bdcbd7.png)

[Bimata Prathama](https://unsplash.com/@bedeviere?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral) 上拍摄的照片

JavaScript 中的对象是引用值，可以存储复杂的键值属性。

```
let story = {
    title: 'Proper Ways to Copy(Clone) an Object in JavaScript',
    author:{
            name:'pkoulianos',
            email:'petran@pkoulianos.com'
    },
    tags:['Javascript','programming']
};
```

复制一个对象可能有点棘手。但是不要担心，在这个故事中，我们将介绍如何以适当的方式复制一个对象。

# 1.致命的😡复制对象的方式

尝试复制对象的一个致命方法是使用赋值`=`操作符。原因是赋值操作符只会将引用传递给新变量。

让我们看一个简单的例子

```
let car1 = { color:’white’, type:’4X4' };// fatal way to copy an object
let car2 = car1;//change the color propertycar2.color = ‘red’;console.log(car1);
**// { color: 'red', type: '4X4' }**console.log(car2);
**// { color: 'red', type: '4X4' }**
```

在上面的例子中，我们创建了一个新的对象`car1`，并用`=`操作符将它复制到一个新的变量`car2`中，并且我们改变了颜色属性。打印两个对象我们可以看到是相同的，原因是`car1`和`car2`都有相同的对象引用。

# 2.得到一个浅薄的💧复制

> [](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/assign)**浅复制将所有* [*可枚举的*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/propertyIsEnumerable) [*自身属性*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/hasOwnProperty) *从源对象复制到目标对象。**

*简单来说，肤浅的复制**不会真正复制**:*

1.  *数组、集合等*
2.  *内部对象*

*用`Object.assign()`获得浅拷贝*

*`Object.assign()`会给你一个目标对象的浅层拷贝:*

```
*let post = {
   title:'How to copy objects in JS',
   tags:['js','js-basics','programming'],
   date: new Date(),
   author:{
         name:'petros',
         email:'petran@pkoulianos.com'
   },
   getAuthorData: function(){
              return this.author.name+'-'+this.author.email;
   }
};let newPost = Object.assign({},post);
newPost.title = 'I love js'
newPost.tags[0] = 'web-programming'
newPost.author.name = 'Petran';
newPost.date = new Date(1970);console.log(post);
console.log(newPost);//console output
{ title: 'How to copy objects in JS',
  tags: [ 'web-programming', 'js-basics', 'programming' ],
  date: 2020-07-21T18:48:29.112Z,
  author: { name: 'Petran', email: '[petran@pkoulianos.com](mailto:petran@pkoulianos.com)' },
  getAuthorData: [Function: getAuthorData] }
{ title: 'I love js',😀
  tags: [ 'web-programming', 'js-basics', 'programming' ],😂
  date: 1970-01-01T00:00:01.970Z,😀
  author: { name: 'Petran', email: '[petran@pkoulianos.com](mailto:petran@pkoulianos.com)' },😂
  getAuthorData: [Function: getAuthorData] }😀*
```

*在上面的例子中，我们创建了一个新的对象`post`，并用`Object.assign()`将它复制到一个新的变量`newPost`中，并且我们改变了所有的属性。打印两个对象我们可以看到，浅拷贝`newPost`正确地拷贝了`title`、`date`和`getAuthorData`，但是`tags`和`author`被引用通过。*

# *用… Spread 运算符获得浅层副本*

*spread 操作符还会得到目标对象的一个浅层副本:*

```
*/ *** / 
**let newPost = {...post}**
/ *** /
console.log(post);
console.log(newPost);//console output{ title: 'How to copy objects in JS',
  tags: [ 'web-programming', 'js-basics', 'programming' ],
  date: 2020-07-21T18:48:29.112Z,
  author: { name: 'Petran', email: '[petran@pkoulianos.com](mailto:petran@pkoulianos.com)' },
  getAuthorData: [Function: getAuthorData] }{ title: 'I love js',
  tags: [ 'web-programming', 'js-basics', 'programming' ],
  date: 1970-01-01T00:00:01.970Z,
  author: { name: 'Petran', email: '[petran@pkoulianos.com](mailto:petran@pkoulianos.com)' },
  getAuthorData: [Function: getAuthorData] }*
```

# *3.深入了解🌊复制*

*一个对象的深层拷贝将解决获得内部对象和数组、集合等的正确拷贝的秘密，但是日期对象将被转换成字符串，函数将根本不会被拷贝。*

*我们可以通过使用 JSON 对象获得深层副本。*

*`**let targetObj = JSON.parse(JSON.stringify(sourceObj));**`*

```
*/ *** / 
let newPost = JSON.parse(JSON.stringify(post));
/ *** /
console.log(post);
console.log(newPost);//console output
{ title: 'How to copy objects in JS',
  tags: [ 'js', 'js-basics', 'programming' ],
  date: 2020-07-21T18:54:35.964Z,
  author: { name: 'petros', email: '[petran@pkoulianos.com](mailto:petran@pkoulianos.com)' },
  getAuthorData: [Function: getAuthorData] }
{ title: 'I love js',
  tags: [ 'web-programming', 'js-basics', 'programming' ],
  date: **'2020-07-21T18:54:35.964Z'**,😂
  author: { name: 'Petran', email: '[petran@pkoulianos.com](mailto:petran@pkoulianos.com)' } }*
```

*打印这两个对象我们可以看到深度副本`newPost`正确地复制了`title`、`tags`和`author`，但是`date`被转换为一个字符串，而`getAuthorData`根本没有被复制。*

# *5.结论*

*浅拷贝和深拷贝各有利弊。在我们决定哪个副本是正确的之前，我们必须确定对象的属性。*

# *参考*

*   *[https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Reference/Global _ Objects/Object/assign](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/assign)*