# 在 UITableView 的单元格之间添加间距

> 原文：<https://levelup.gitconnected.com/adding-space-between-the-cells-of-a-uitableview-590a0cfd2e22>

![](img/e251aaae592942968814cf6c76df1a91.png)

照片由 [Tim Hüfner](https://unsplash.com/@huefnerdesign?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

# 绪论

在这篇文章中，我将向你展示如何创建一个单元格之间有间距的`UITableView`。我最近在开发一个客户的应用程序时，偶然发现了这个设计。我想摆脱基本的表格视图外观，使用一些更适合客户端应用程序的 UI/UX 的东西。对于那些从事自由软件开发的人来说——设计的多样性是必不可少的，因为不是每个客户都想要同样的东西。

通过单元格之间的间距实现整洁的表格视图设计；您必须使用节而不是行。让我们来了解一下如何创建一个带有间距和简洁设计的自定义`UITableView`。这篇文章假设你以前有 Swift、Xcode 和`UITableViews`的经验。

你可以在 GitHub 上找到这篇文章的源代码。务必从回购的“开始”分支开始。

# 什么已经设置好了

如果您打开项目并在设备或模拟器上运行它，您将看到当前的 UI 布局。我已经建立了一个基本的餐馆评论应用程序，全部通过编程完成，我们将使用虚构的 Rusty Nail 汉堡店。您看到的第一个视图控制器是`RestaurantReviewsViewController`类。`View`组有两个班；一个用于定制`UITableViewCell`，一个用于定制`UIView`。在应用程序的当前状态下，您可以看到`UIView`正在运行，因为这是一个包含餐厅标志的橙色圆圈。

第一回`RestaurantReviewsViewController`。首先，我存储了一个名为`Review`的自定义结构，它包含三个属性；评论的作者、接收评论的餐馆以及评论的文本。还有一个名为`setupView`的方法设置视图的 UI 元素，还有一个名为`applyAutoConstraints`的方法激活对所述 UI 元素的约束。

此外，在`viewDidLoad`方法中，设置了视图的背景颜色，并且调用了`setupView`方法。最后，您将看到一个标有`Extensions`的部分和一个将`UIView`圈起来的方法。

现在我们已经浏览了主视图控制器的当前代码，让我们构建那个表视图。如果您想看看项目中提供的其他代码，请慢慢来，了解它们是如何协同工作的。简言之，`RestaurantReviewCell`是一个包含两个标签的定制表格视图单元格；一个用于评论餐馆的人的名字，另一个用于评论。

# 配置表格视图

首先，用一些评论初始化`data`数组。在这个例子中，我使用了《开心汉堡店》中的角色。数据在`setupView`方法中初始化。

```
data = [
    Review(writer: "Calvin", receiver: "Rusty Nail Burgers",       reviewText: "Okay burgers. Okay tenant. Would like to see them pay rent on time."),
    Review(writer: "Felix", receiver: "Rusty Nail Burgers", reviewText: "Though my brother isn't a fan of Bob's cooking, I can't find a better burger on the wharf."),
    Review(writer: "Teddy", receiver: "Rusty Nail Burgers", reviewText: "I'm here everyday. Couldn't ask for a better hangout spot."),
    Review(writer: "Mickey", receiver: "Rusty Nail Burgers", reviewText: "Hey Bob! Thanks for feeding me during that heist. I will definitely be back to visit."),
    Review(writer: "Marshmellow", receiver: "Rusty Nail Burgers", reviewText: "Hey Bob."),
    Review(writer: "Rudy", receiver: "Rusty Nail Burgers", reviewText: "My dad drops me off here every once in a while. I like to sit in the back corner and enjoy my food."),
]
```

接下来，设置`reviewTableView`的`delegate`和`dataSource`。我将遵循标记为`Extensions`的部分中的`UITableViewDelegate`和`UITableViewDataSource`协议——尽管您可以在`RestaurantReviewsViewController`类之外直接这么做。在`reviewTableView`初始化后，将以下内容添加到`setupView`方法中:

```
reviewTableView.delegate = self
reviewTableView.dataSource = self
```

`setupView`方法现在应该是这样的:

```
func setupView() {
    data = [
        Review(writer: "Calvin", receiver: "Rusty Nail Burgers", reviewText: "Okay burgers. Okay tenant. Would like to see them pay rent on time."),
        Review(writer: "Felix", receiver: "Rusty Nail Burgers", reviewText: "Though my brother isn't a fan of Bob's cooking, I can't find a better burger on the wharf."),
        Review(writer: "Teddy", receiver: "Rusty Nail Burgers", reviewText: "I'm here everyday. Couldn't ask for a better hangout spot."),
        Review(writer: "Mickey", receiver: "Rusty Nail Burgers", reviewText: "Hey Bob! Thanks for feeding me during that heist. I will definitely be back to visit."),
        Review(writer: "Marshmellow", receiver: "Rusty Nail Burgers", reviewText: "Hey Bob."),
        Review(writer: "Rudy", receiver: "Rusty Nail Burgers", reviewText: "My dad drops me off here every once in a while. I like to sit in the back corner and enjoy my food."),
    ]

    restaurantImageViewBackground = RestaurantImageViewBackground()
    view.addSubview(restaurantImageViewBackground)

    restaurantImageView = UIImageView(image: UIImage(named: "cheese-burger"))
    restaurantImageView.translatesAutoresizingMaskIntoConstraints = false
    restaurantImageViewBackground.addSubview(restaurantImageView)

    restaurantNameLabel = UILabel()
    restaurantNameLabel.translatesAutoresizingMaskIntoConstraints = false
    restaurantNameLabel.text = "Rusty Nail Burgers"
    restaurantNameLabel.font = UIFont.boldSystemFont(ofSize: 28)
    restaurantNameLabel.textColor = .black
    view.addSubview(restaurantNameLabel)

    starStackView = UIStackView()
    starStackView.translatesAutoresizingMaskIntoConstraints = false
    starStackView.alignment = .center
    starStackView.axis = .horizontal
    starStackView.spacing = 5

    for _ in 0…5 {
        let starView = UIImageView(image: UIImage(systemName: "star.fill"))
        starView.frame = CGRect(x: 0, y: 0, width: 40, height: 40)
        starView.tintColor = .black
        starStackView.addArrangedSubview(starView)
    }

    view.addSubview(starStackView)

    reviewTableView = UITableView()
    reviewTableView.translatesAutoresizingMaskIntoConstraints = false
    reviewTableView.delegate = self
    reviewTableView.dataSource = self
    view.addSubview(reviewTableView)

    applyAutoConstraints()
}
```

`UITableViewDataSource`的设置类似于任何其他表格视图。我们在每个部分返回一行，并根据我们的自定义`RestaurantReviewCell`类配置单元格。在设置单元格之前，注册与表格视图一起使用的`UITableViewCell`类。向`UITableView`设置添加以下内容:

```
reviewTableView.register(RestaurantReviewCell.self, forCellReuseIdentifier: "reviewCell")
```

要设置单元格，请将以下内容添加到`cellForRowAt`方法中:

```
func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
    // 1
    let cell = tableView.dequeueReusableCell(withIdentifier: "reviewCell", for: indexPath) as! RestaurantReviewCell

    // 2
    if let writer = data[indexPath.section].writer {
       cell.reviewerLabel.text = "\(writer) said:"
    } 
    if let reviewText = data[indexPath.section].reviewText {
       cell.reviewLabel.text = "\(reviewText)"
    }

   // 3
   return cell
}
```

这就是它的作用:

**1** 。使用`reviewCell`标识符和`RestaurantReviewCell`类使单元格出队。
**2** 。根据显示的部分提取数据。首先，打开我们数据源的`writer`属性的值(记住这是类型`Review`)。然后，解开`reviewText`的值。
**3** 。归还牢房。

# UI 使用表格视图

接下来，在`UITableViewDelegate`中，将表格视图的节数设置为数组`data`的`count`，以创建显示正确数据量所需的节数。

```
func numberOfSections(in tableView: UITableView) -> Int {
    return data.count
}
```

将行高设置为 140(这可以根据您的使用情况进行更改)。

```
func tableView(_ tableView: UITableView, heightForRowAt indexPath: IndexPath) -> CGFloat {
    return 140
}
```

现在，为了给单元格增加间距，我们必须为每个部分的标题创建一个视图。调用`viewForHeaderInSection`方法，并添加以下内容:

```
func tableView(_ tableView: UITableView, viewForHeaderInSection section: Int) -> UIView? {
    // 1
    let headerView = UIView()
    // 2
    headerView.backgroundColor = view.backgroundColor
    // 3
    return headerView
}
```

下面是刚刚添加的内容的细分:
**1** 。将标题视图创建为一个`UIView`。
2**3**。将标题的背景颜色设置为视图控制器的`view.backgroundColor`属性。(这样就产生了让页眉看起来透明的设计效果)。
3。返回`headerView`设置表格视图各部分的标题。

这产生了单元格之间的基本间距。割台的高度可按如下方式设置:

```
func tableView(_ tableView: UITableView, heightForHeaderInSection section: Int) -> CGFloat {
    return 20
}
```

`UITableViewDelegate`和`UITableViewDataSource`应该是这样的:

```
extension RestaurantReviewsViewController: UITableViewDelegate {
    func numberOfSections(in tableView: UITableView) -> Int {
        return data.count
    }

    func tableView(_ tableView: UITableView, heightForRowAt indexPath: IndexPath) -> CGFloat {
        return 140
    }

    func tableView(_ tableView: UITableView, viewForHeaderInSection section: Int) -> UIView? {
        let headerView = UIView()
        headerView.backgroundColor = view.backgroundColor
        return headerView
    }

    func tableView(_ tableView: UITableView, heightForHeaderInSection section: Int) -> CGFloat {
        return 20
    }
}

extension RestaurantReviewsViewController: UITableViewDataSource {
    func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return 1
    }

    func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        let cell = tableView.dequeueReusableCell(withIdentifier: "reviewCell", for: indexPath) as! RestaurantReviewCell

        if let writer = data[indexPath.section].writer {
            cell.reviewerLabel.text = "\(writer) said:"
        }

        if let reviewText = data[indexPath.section].reviewText {
           cell.reviewLabel.text = "\(reviewText)"
        }

        return cell
    }
}
```

# 收尾工作

前面我提到了“干净”的设计。现在，每个细胞的设计看起来像一个基本的`UITableViewCell`；白色矩形。为了增加设计的“整洁度”,改变`leadingAnchor`和`trailingAnchor`,将工作台视图侧推离视图的每一侧 20 点:

```
reviewTableView.leadingAnchor.constraint(equalTo: view.leadingAnchor, constant: 20),
reviewTableView.trailingAnchor.constraint(equalTo: view.trailingAnchor, constant: -20),
```

单元格仍然是基本的白色矩形，即使`RestaurantReviewCell`的`layer.cornerRadius`设置为 20。这是因为表视图的背景色是白色的。要解决这个问题，我们必须删除表视图的背景颜色，目前是白色。通过将表格视图的`backgroundColor`属性设置为`.clear`来移除表格视图的背景。

```
reviewTableView.backgroundColor = .clear
```

最后，由于这是一个基本列表，点击单元格是没有用的，也不会增加用户体验。要禁用表格视图上的点击，请将`allowsSelection`属性设置为`false`。

```
reviewTableView.allowsSelection = false
```

# 结束语

感谢您的阅读！如果你想查看更多关于计算机科学和软件工程的帖子，试试吧:

*   软件架构模式:MVVM
*   [CS 数据结构:固定数组](https://andrew-lundy.medium.com/cs-data-structures-fixed-array-76f36c054f7)
*   [什么是 API？](https://andrew-lundy.medium.com/what-is-an-api-23a3e7e7a660)
*   [联轴器的概念](https://medium.com/swlh/the-concept-of-coupling-a5e910d852ae)

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[升级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级人才集体，找到一份神奇的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)