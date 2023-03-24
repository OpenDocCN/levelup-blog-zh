# 通过使用容器组件实现可重用性

> 原文：<https://levelup.gitconnected.com/container-and-presentational-components-in-react-c56aca7713ba>

![](img/78b432b3407dea2fa63ee2c830d1fdc2.png)

弗兰克·麦肯纳在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

React 旨在通过改进我们构建 UI 组件的方式来解决现代前端开发中的一些复杂性。其中的一个核心原则是构建可重用的组件。

提高组件可重用性的一个方法是让它们具有单一的职责。获取将要用来构建 UI 的数据的组件有两个职责，获取数据和呈现数据。为了避免这种情况，解决方案是制造容器组件。

容器组件是呈现另一个组件的组件，它是一种高阶组件。容器组件负责获取数据，然后将其传递给其他组件。当然，容器组件并不严格地用于获取数据，它们可以做其他计算或逻辑相关的杂务。

为了让我自己更清楚一点，这里有一个例子:一个`PostList`组件可能循环遍历一个 post 数组，并为数组中的每个 post 对象呈现一个`Post`组件。如果`PostList`正在获取帖子的数组，那么每当我们想要重用组件时，我们就不得不请求数据。

如果我们想在应用程序的另一部分呈现一个帖子列表，而这次我们不想要来自我们在`PostList`组件中调用的端点的帖子，该怎么办？我们可以让`PostList`把端点作为一个参数。然后，我们可能希望使用来自两个端点的帖子，并连接数组并呈现它们。关键是我们很快就会遇到问题。

为了避免在`PostList`组件中获取数据，我们可以创建一个`PostListContainer`组件来请求数据，并在其 render 方法中呈现一个`<PostList posts={posts} />`。这样,`PostList`只关心表示，数据作为参数传递。现在可以重用`PostList`了，如果我们有一个来自上述端点的帖子数组，我们就有了规范化的数据(一个帖子数组)，我们可以将它传递给`PostList`。

在代码中，容器组件可能如下所示:

```
**// PostListContainer.js**const endpoint = `https://jsonplaceholder.typicode.com/posts`const **PostListContainer** = () => {
  const [posts, setPosts] = useState([]) useEffect(() => {
    const fetchData = async (id) => {
      const response = await fetch(endpoint);
      const posts = await response.json()
      setPosts(posts)
    }
    fetchData();
  },[posts]);

  return(
    <**PostList** posts={posts} />
  )
}
```

容器获取数据并将其传递给`PostList`，只要得到一个 posts 数组，`PostList`就会一直正常工作。

```
**// PostList.js**const **PostList** = ({ posts }) => (
  <div>
    {posts.map(post =>
      <**Post**
        key={post.id}
        title={post.title}
        body={post.body}
      />)}
  </div>
)
```

现在我们可以在其他组件中重用`PostList`。如果我们在`PostList`中获取数据，组件就不容易重用。

制作容器组件的另一个好处是，当`PostList`只关心表示时，为它编写测试更容易，因为现在它只有一个职责。

## 概括起来

具有单一职责的组件更容易重用和测试。容器组件是分离关注点的一种方式。容器组件可能负责获取数据，并在其 render 方法中，将数据传递给表示组件，表示组件反过来只负责 UI 显示数据。