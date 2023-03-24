# Java 中单链表的实现

> 原文：<https://levelup.gitconnected.com/single-linked-list-implementation-in-java-e02273818c27>

> *原载于 2019 年 12 月 23 日* [*我的个人博客*](https://blog.contactsunny.com/tech/single-linked-list-implementation-in-java) *。*

![](img/067435821108d63680a61482d8fe0098.png)

更多在 [**数据结构系列**](https://blog.contactsunny.com/tag/data-structure-implementation-in-java) 。

在上一篇文章中，我们看到了如何用 Java 实现堆栈。但是因为那是这个博客上的第一篇数据结构文章，所以我在内部使用了一个数组列表。在这篇文章中，我们将用 Java 实现一个简单的链表。从这篇文章开始，我们将认真对待这些数据结构的实现。因此，我们将编写 100%自定义的实现，而不使用任何 Java 内置类。此外，我也不打算解释数据结构是如何工作的。这意味着，在这篇文章中，我不会像在栈实现文章中那样用插图来解释每一步。因此，从现在开始，我们将直接进入实现阶段。

# 节点

我们知道任何链表都会包含一个节点，这个节点是链表的关键组成部分。这个节点将拥有被存储的实际值，以及一个指向下一个节点的指针。因为这是一个单链表，所以我们在这个节点中只有一个链接。显然，我们从节点本身开始实现。

这里有一点需要考虑。我们将在这个节点中存储什么类型的值？它是整数、字符串还是自定义数据类型？我们必须在编写代码时做出这个决定，因为我们不能在运行时改变它。但是如果我们不知道用户想要在这个链表中存储什么类型的数据呢？为了使我们的链表能够存储任何类型的数据，我们将使用 Java 的泛型类，即 *T* 类。利用这一点，我们可以在运行时决定链表应该保存什么类型的值。我们将这样定义我们的节点:

```
public class Node<T> {

    private T data;
    private Node<T> next;

    public T getData() {
        return data;
    }

    public void setData(T data) {
        this.data = data;
    }

    public Node<T> getNext() {
        return next;
    }

    public void setNext(Node<T> next) {
        this.next = next;
    }
}
```

如您所见，我们将节点类定义为，定义中的 *< T >* 表示值的类型将在运行时定义。这个节点是链表的构造块。将有一些这样的节点链接在一起，使链表。现在让我们看看如何实现链表类。

# 链表

我们将这样定义 LinkedList 类:

```
public class LinkedList<T> {

    private Node<T> head;

}
```

我们有一个 head 节点，它将是第一个插入链表的节点。添加到链表中的任何新节点都将成为新的头。前一个头将是新头的下一个节点*。我们现在必须开始添加可以在链表上执行的各种函数或操作。我们将从一个向链表添加新节点的函数开始，我将调用这个方法 *push()* ，因为为什么不呢？*

```
public void push(T value) {

	// If there's no head, that means we're adding 
	// values to a new linked list. So we'll create a head.
    if (this.head == null) {

        this.head = new Node<T>();
        this.head.setData(value);

    } else {

    	// If there's already a head, we'll create a new node,
    	// and make the new node's "next" point to the current 
    	// head of the linked list, and then make the new node
    	// the new head.

        Node<T> newNode = new Node<T>();
        newNode.setData(value);
        newNode.setNext(this.head);
        this.head = newNode;

    }
}
```

我觉得这个方法不言自明。每当我们向链表推送一个新的值时，我们就添加了一个新的头。接下来，我们需要能够遍历链表，因为这是链表的主要特性之一。在遍历单个链表时，我们从头节点开始，一直到链表的末尾。我们从头部开始，因为我们只引用了链表的头部。遍历方法可以写成这样:

```
public void traverse() {

    if (this.head != null) {

    	// We'll start the traversal from the head.
        Node<T> currentNode = this.head;

        // We'll continue the traversal until the currentNode becomes null,
        // which means we have reached the end of the linked list.
        while (currentNode != null) {
            System.out.print(currentNode.getData() + " -> ");

            // We're moving to the next node here.
            currentNode = currentNode.getNext();
        }

        System.out.println("END");

    } else {
        System.out.println("Linked list is empty.");
    }
}
```

我已经在方法中添加了注释，以清楚地说明我们在做什么。如果有些事情不是很清楚或者你需要帮助理解，请在下面的评论中告诉我。

接下来，我们需要能够从链表中删除一个节点。如果我给一个值，我们必须遍历链表，直到我们找到具有该值的节点，将前一个节点的 *next* 指向当前节点的 *next* ，这样当我们删除当前节点时，我们不会丢失它之后的所有其他节点，我们仍然需要保持链表完整。这种逻辑一开始看起来有点吓人，但是如果你试着去理解它，就会发现它非常简单。

```
public void delete(T value) {

	// We can delete a value only if the linked list is not empty.
    if (this.head != null) {
    	// Because we have the reference of only the head node, we'll assume that 
    	// both the currentNode and the previousNode is the head node.
        Node<T> currentNode = this.head;
        Node<T> previousNode = this.head;

        // We'll continue the loop until the currentNode becomes null,
        // which would mean that we've reached the end of the linked list.
        while (currentNode != null) {

        	// If the current node's value is the same as the value that we want to
        	// delete, it means that we have the node that we have to delete.
            if (currentNode.getData() == value) {
            	// In which case, we're linking the previous node's next() to the
            	// current node's next(). Doing this will by default delete the
            	// current node as we'll lose the reference to that node.
                previousNode.setNext(currentNode.getNext());

                System.out.println("Deleted first node with value " + value);

                // We'll break the while loop now as we don't need to traverse anymore.
                break;
            } else {
                previousNode = currentNode;
                currentNode = currentNode.getNext();
            }
        }
    }
}
```

我们可以给这个链表类添加更多的功能，比如在链表的尾部添加一个节点，在给定的索引后添加一个节点，在给定的索引处删除一个节点，或者在给定的索引后删除一个节点。它们都是非常简单的函数。我这里有所有这些的代码。

# 在尾部添加一个节点

```
public void addToTail(T value) {

	// If the head is null, that means the linked list is empty.
	// So we just add one node and make it the head and tail.
    if (this.head == null) {
        this.head = new Node<T>();
        this.head.setData(value);
    } else {

        Node<T> lastNode = this.head;

        // We assume that the last node in the linked list in the head.
        // We traverse the node untill the next node is null, which means 
        // we're at the last node. We'll add a new node now, and point the
        // current last node's 'next' to the new node.
        while (lastNode.getNext() != null) {
            lastNode = lastNode.getNext();
        }

        Node<T> newNode = new Node<T>();
        newNode.setData(value);

        lastNode.setNext(newNode);
    }
}
```

# 在给定索引后添加一个节点

```
public void addAfterIndex(T value, int index) {

    if (this.head == null) {
        this.head = new Node<T>();
        this.head.setData(value);
    } else {

        int nodeIndex = 0;

        Node<T> currentNode = this.head;

        while (index > nodeIndex) {
            if (currentNode.getNext() != null) {
                currentNode = currentNode.getNext();
            }

            nodeIndex++;
        }

        if (nodeIndex == index) {
            Node<T> newNode = new Node<T>();
            newNode.setData(value);
            newNode.setNext(currentNode.getNext());

            currentNode.setNext(newNode);
        }
    }
}
```

# 删除给定索引处的节点

```
public void deleteNodeAtIndex(int index) {

    if (this.head != null) {

        int nodeIndex = 0;

        Node<T> currentNode = this.head;
        Node<T> previousNode = this.head;

        while (nodeIndex != index) {

            previousNode = currentNode;

            if (currentNode.getNext() != null) {
                currentNode = currentNode.getNext();
            }

            nodeIndex++;
        }

        previousNode.setNext(currentNode.getNext());
    }
}
```

# 删除给定索引后的节点

```
public void deleteNodeAfterIndex(int index) {

    if (this.head != null) {

        int nodeIndex = 0;

        Node<T> currentNode = this.head;

        while (nodeIndex != index) {

            if (currentNode.getNext() != null) {
                currentNode = currentNode.getNext();
            }

            nodeIndex++;
        }

        if (currentNode.getNext() != null) {
            currentNode.setNext(currentNode.getNext().getNext());
        } else {
            currentNode.setNext(null);
        }
    }
}
```

# 创建链接列表

现在让我们看看如何用我们刚刚编写的类创建一个 LinkedList。在我的 main()方法中，我创建了一个新的 LinkedList 对象，并对它调用了几个方法:

```
public static void main(String[] args) {

    LinkedList<Integer> linkedList = new LinkedList<>();

    linkedList.push(1);
    linkedList.push(2);
    linkedList.push(3);

    linkedList.addToTail(4);

    linkedList.push(1);
    linkedList.push(2);
    linkedList.push(3);

    System.out.println("Traversing linked list");
    linkedList.traverse();

    System.out.println("Deleting first node with value 2");
    linkedList.delete(2);

    System.out.println("Traversing linked list after deletion");
    linkedList.traverse();

    System.out.println("Adding value 5 after the node with index 1");
    linkedList.addAfterIndex(5, 1);

    System.out.println("Traversing linked list after adding new node after index 1.");
    linkedList.traverse();

    System.out.println("Deleting node at index 2");
    linkedList.deleteNodeAtIndex(2);

    System.out.println("Traversing linked list after deleting node at index 2.");
    linkedList.traverse();

    System.out.println("Deleting node after index 3");
    linkedList.deleteNodeAfterIndex(3);

    System.out.println("Traversing linked list after deleting node after index 3.");
    linkedList.traverse();
}
```

这段代码的输出如下所示:

```
Traversing linked list
3 -> 2 -> 1 -> 3 -> 2 -> 1 -> 4 -> END
Deleting first node with value 2
Deleted first node with value 2
Traversing linked list after deletion
3 -> 1 -> 3 -> 2 -> 1 -> 4 -> END
Adding value 5 after the node with index 1
Traversing linked list after adding new node after index 1.
3 -> 1 -> 5 -> 3 -> 2 -> 1 -> 4 -> END
Deleting node at index 2
Traversing linked list after deleting node at index 2.
3 -> 1 -> 3 -> 2 -> 1 -> 4 -> END
Deleting node after index 3
Traversing linked list after deleting node after index 3.
3 -> 1 -> 3 -> 2 -> 4 -> END
```

我希望解释清楚了。如果没有，一如既往，如果您有任何问题，我将非常乐意提供帮助。请在下面的评论中留下它们。如果你想使用这个链接列表查看一个正在进行的项目，请随意在 Github 上分叉[我的项目。](https://github.com/contactsunny/LinkedList_Implementation_Java_POC)