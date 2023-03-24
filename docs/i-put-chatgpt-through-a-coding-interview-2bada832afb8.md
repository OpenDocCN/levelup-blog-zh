# 我让 ChatGPT 通过了一次编码面试

> 原文：<https://levelup.gitconnected.com/i-put-chatgpt-through-a-coding-interview-2bada832afb8>

## 这让我担心我的工作

![](img/a46194d93c3da37c4696b9ae1ae99e3c.png)

由[附身摄影](https://unsplash.com/@possessedphotography)来自 [Unsplash](https://unsplash.com/photos/U3sOwViXhkY)

如果你没有听说过，ChatGPT 是一个人工智能网络，以类似聊天的方式进行交互。起初我以为它只是另一个“聊天机器人”，但当我让它写一些代码时，我震惊了。它写了一些意想不到的连贯代码，我认为这些代码对未来的开发者会有所帮助。

起初，我让它写一个散列密码的 Javascript 函数。具体来说，我问:*编写一个 Javascript 函数，它接受密码的字符串输入并打印出散列密码。*

这是输出结果:

```
function hashPassword(password) {
  return crypto.createHash('sha256').update(password).digest('hex');
}
```

然后我想我应该把它提升一个档次，我问它:

*编写一个快速路由处理程序，在请求体中包含用户名和密码，散列密码，在 MySQL 数据库中找到一个现有用户，并检查散列密码是否匹配。*

产出令人印象深刻:

```
const express = require('express');
const bcrypt = require('bcrypt');
const mysql = require('mysql');

// create connection to MySQL database
const db = mysql.createConnection({
    host: 'localhost',
    user: 'root',
    password: '',
    database: 'mydb'
});

// connect to database
db.connect((err) => {
    if (err) {
        throw err;
    }
    console.log('MySQL connected...');
});

 // create express app
const app = express();

// route handler
app.post('/login', (req, res) => {
    // get username and password from request body
    const { username, password } = req.body;

    // hash the password
    const hashedPassword = bcrypt.hashSync(password, 10);

    // find user in database
    let sql = `SELECT * FROM users WHERE username = '${username}'`;
    let query = db.query(sql, (err, result) => {
        if (err) throw err;

        // check if passwords match
        if (bcrypt.compareSync(result[0].password, hashedPassword)) {
            res.send('Login successful!');
        } else {
            res.send('Incorrect username or password');
        }
    });
});
```

好了，我的结论是，这是一点 Javascript 知识。但是 Javascript 是一种非常流行的编程语言，我相信我问的问题是相当普遍的。所以我决定用下面的问题来改变事情:

*用 C 语言为一个链表结构编写代码，根据一个名为“标识”的属性在链表中添加、删除和查找节点。*

下面是代码输出:

```
#include <stdio.h>
#include <stdlib.h>

// Structure for a node in the linked list
typedef struct Node {
    int identity; // Property of the node
    struct Node *next; // Pointer to the next node
} Node;
// Function to add a node to the linked list
void addNode(Node **head, int identity) {
    // Create a new node
    Node *newNode = (Node*)malloc(sizeof(Node));
    // Set the identity of the new node
    newNode->identity = identity;
    // Set the next pointer of the new node to point to the head
    newNode->next = *head;
    // Set the head to point to the new node
    *head = newNode;
}
// Function to delete a node from the linked list
void deleteNode(Node **head, int identity) {
    // Create a temporary node to traverse the linked list
    Node *temp = *head;
    // Check if the head is the node to be deleted
    if (temp != NULL && temp->identity == identity) {
        // Set the head to point to the next node
        *head = temp->next;
        // Free the memory allocated for the node
        free(temp);
        return;
    }
    // Traverse the linked list to find the node to be deleted
    while (temp != NULL && temp->identity != identity) {
        // Set the previous node to the current node
        Node *prev = temp;
        // Set the current node to the next node
        temp = temp->next;
    }
    // Check if the node was found
    if (temp == NULL) return;
    // Set the previous node to point to the next node
    prev->next = temp->next;
    // Free the memory allocated for the node
    free(temp);
}
// Function to find a node in the linked list
Node* findNode(Node *head, int identity) {
    // Create a temporary node to traverse the linked list
    Node *temp = head;
    // Traverse the linked list to find the node
    while (temp != NULL) {
        // Check if the current node has the given identity
        if (temp->identity == identity) {
            return temp;
        }
        // Set the current node to the next node
        temp = temp->next;
    }
    // Return NULL if the node was not found
    return NULL;
}
int main() {
    // Create a head node for the linked list
    Node *head = NULL;
    // Add some nodes to the linked list
    addNode(&head, 1);
    addNode(&head, 2);
    addNode(&head, 3);
    // Find a node in the linked list
    Node *node = findNode(head, 2);
    // Print the identity of the node
    printf("Node with identity 2: %d\n", node->identity);
    // Delete a node from the linked list
    deleteNode(&head, 2);
    // Find the deleted node in the linked list
    node = findNode(head, 2);
    // Check if the node was deleted
    if (node == NULL) {
        printf("Node with identity 2 was deleted\n");
    }
    return 0;
}
```

这时候我开始担心了。借助大量的谷歌搜索，我可能要花大约一个小时来记住如何做到这一点，但这在几秒钟内就被吐出来了。如果这种人工智能在我的大学时代可用，那么完成许多算法和数据结构作业将是轻而易举的事情。它展示了使用我在问题中输入的自定义参数编写功能代码的能力。因此，它不仅能从互联网上“复制粘贴”代码，还能很好地理解代码，进行排列。

但我认识到，我弱小的人类大脑无法与一个训练有素的人工智能模型在极其庞大的数据集上的计算能力相匹敌。所以我决定测试它的“理解力”，没有比让它给我解释什么更好的测试理解力的方法了。

于是我就问它:*解释一下有监督的和无监督的机器学习的区别，并给出每个*的真实生活例子。是的，我意识到问它一个机器学习问题的讽刺。

不出所料，它给出了一个完美的答案:

*监督机器学习:监督机器学习是一种机器学习算法，使用标记数据来训练模型。标记数据是已经用正确答案或输出标记的数据。例如，监督机器学习算法可以用来对猫和狗的图像进行分类。该算法将在标记数据上进行训练，例如标记为“猫”的猫的图像和标记为“狗”的狗的图像。一旦模型被训练，它就可以被用来对新的猫和狗的图像进行分类。*

*无监督机器学习:无监督机器学习是一种机器学习算法，它使用未标记的数据来训练模型。未标记数据是没有正确答案或输出的数据。例如，可以使用无监督的机器学习算法对客户数据进行聚类。该算法将在未标记的数据上进行训练，如客户的购买历史、年龄、性别等。一旦模型被训练，它就可以被用来根据客户的相似性将他们分成不同的组。*

如果 ChatGPT 是一个候选人，它以优异的成绩通过了技术面试。我承认这不是最结构化的面试过程，但我觉得问更多的问题只是浪费我的时间和它的计算能力，因为我完全有信心它会提供正确的答案。

我可以预见有一天开发人员和公司会使用这种技术来编写代码，而开发人员的角色将是对模型产生的代码进行质量保证。ChatGPT 在开发环境中的主要用途是生成几乎不需要上下文的起始代码。然而，害怕的不是开发商；在这一点上，你不能为它提供一个完整的代码库来代替你的工作。然而，它可以用作一种工具；当开发人员确切地知道哪些文件需要更改以及更改是什么时，开发人员可以在 ChatGPT 中编写一些伪代码，这至少会为需要编写的代码提供一个很好的模板。

感谢阅读！如果你喜欢这篇文章，请考虑关注我。我经常发技术文章。

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看更多内容请参见[升级编码刊物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入升级人才集体，找到一份神奇的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)