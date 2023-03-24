# Java 中循环双向链表的实现

> 原文：<https://levelup.gitconnected.com/circular-double-linked-list-implementation-in-java-72a35a86ef1b>

![](img/f272197bcd892c2a1f3e063572dbf9de.png)

> 原载于[我的个人博客](https://blog.contactsunny.com/tech/circular-double-linked-list-implementation-in-java)2020 年 1 月 10 日。

更多在 [**《数据结构系列》**](https://blog.contactsunny.com/tag/data-structure-implementation-in-java) 。

在这篇关于如何用 Java 实现循环双向链表(DLL)的文章中，我们将继续我们的数据结构之旅。这与标准 DLL 非常相似，唯一的区别是头部和尾部的连接。这意味着，我们把头和尾连接在一起，我们可以把它想象成一个圆，因为圆没有起点也没有终点。

因为列表的头和尾是连在一起的，所以可以说有始无终。但是当然，我们引用了头部和尾部，使我们的遍历变得容易。如果你还没有看完我的[对双向链表](https://blog.contactsunny.com/tech/double-linked-list-implementation-in-java)的解释，请现在就看，因为我们将在这里使用完全相同的类，只有很小的改动。另外，确保你看了一下我在这里解释的其他数据结构。

# 节点

像往常一样，我们将从节点类开始。下面是该类的定义:

```
public class Node<T> {

    private T data;
    private Node<T> next;
    private Node<T> previous;

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

    public Node<T> getPrevious() {
        return previous;
    }

    public void setPrevious(Node<T> previous) {
        this.previous = previous;
    }
}
```

如您所见，我们在这里仍然使用通用的 *T* 类，这样我们就可以在运行时在这个链表中存储任何类型的数据。接下来，我们将看看 DoubleLinkedList 类是什么样子的。

# 链表类

对于任何一个 DLL 类，我们都有一个头和一个尾，它们是我们之前定义的 Node <t>类型:</t>

```
public class DoubleLinkedList<T> {

    private Node<T> head;
    private Node<T> tail;

}
```

请记住，仅仅因为我将该类命名为 DoubleLinkedList，您不必做同样的事情，您可以将其命名为 LinkedList，或 CircularLinkedList，或任何其他名称。因为我用这个来举例说明，所以我用了这个名字。

接下来，我们将看到 push()方法。这是第一个方法，我们将区别规则 DLL 和循环 DLL。

```
public void push(T value) {

	// If the list is empty, the head will be null.
	// So create a new node for the head, add the value,
	// and then make the tail the same as the head.
    if (this.head == null) {
        this.head = new Node<T>();
        this.head.setData(value);
        this.tail = this.head;
    } else {

    	// If the head is not empty, it means that there are already
    	// node in the list. So we create a new node, save the value,
    	// add it in front of the head, make the head the tail connections.
        Node<T> newNode = new Node<T>();
        newNode.setData(value);
        newNode.setNext(this.head);
        newNode.setPrevious(this.tail);

        // We also chagne the links in the current head and tail, 
        // so that we don't lose any references.
        this.head.setPrevious(newNode);
        this.tail.setNext(newNode);

        // It is also important to make the new node the head,
        // so that when we add a new node later, we're  maintaining
        // the chain properly.
        this.head = newNode;
    }
}
```

我在代码中添加了注释来解释这个方法中发生了什么。这应该是一个足够的解释，但如果不是，请让我在评论中知道，我很乐意帮助你理解。

DoubleLinkedList 类中的所有其他方法与常规的 DoubleLinkedList 类保持相同。你可以[查看 DLL 发布](https://blog.contactsunny.com/tech/double-linked-list-implementation-in-java)获取更多信息。在任何情况下，我都在这里提供了该类的所有其他方法以供快速参考。

```
public void traverse() {

    if (this.head != null) {
        Node<T> currentNode = this.head;

        System.out.print("Forward traverse: ");

        while (currentNode != null) {
            System.out.print(currentNode.getData() + " -> ");

            if (currentNode.getNext() == this.head) {
                break;
            }

            currentNode = currentNode.getNext();
        }

        System.out.println("END");

    } else {
        System.out.println("Linked list is empty.");
    }
}

public void reverseTraverse() {

    if (this.tail != null) {
        Node<T> currentNode = this.tail;

        System.out.print("Reverse traverse: ");

        while (currentNode != null) {
            System.out.print(currentNode.getData() + " -> ");

            if (currentNode.getPrevious() == this.tail) {
                break;
            }

            currentNode = currentNode.getPrevious();
        }

        System.out.println("END");

    } else {
        System.out.println("Linked list is empty.");
    }
}

public void addToTail(T value) {

    if (this.head == null) {
        this.head = new Node<T>();
        this.head.setData(value);
        this.tail = this.head;
    } else {
        Node<T> newNode = new Node<T>();
        newNode.setData(value);
        newNode.setPrevious(this.tail);
        newNode.setNext(this.head);

        this.tail.setNext(newNode);
        this.tail = newNode;
    }
}

public void delete(T value) {

    if (this.head != null) {
        Node<T> currentNode = this.head;
        Node<T> previousNode = this.head;

        while (currentNode != null) {
            if (currentNode.getData() == value) {
                previousNode.setNext(currentNode.getNext());

                if (currentNode.getNext() != null) {
                    currentNode.getNext().setPrevious(previousNode);
                }

                System.out.println("Deleted first node with value " + value);
                break;
            } else {
                previousNode = currentNode;
                currentNode = currentNode.getNext();
            }
        }
    }
}

public void addAfterIndex(T value, int index) {

    if (this.head == null) {
        this.head = new Node<T>();
        this.head.setData(value);
        this.tail = this.head;
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
            newNode.setPrevious(currentNode);

            if (currentNode.getNext() != null) {
                currentNode.getNext().setPrevious(newNode);
            }

            currentNode.setNext(newNode);
        }
    }
}

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

        if (currentNode.getNext() != null) {
            currentNode.getNext().setPrevious(previousNode);
        }
    }
}

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

            Node<T> nodeToBeDeleted = currentNode.getNext();

            currentNode.setNext(nodeToBeDeleted.getNext());

            if (nodeToBeDeleted.getNext() != null) {
                nodeToBeDeleted.getNext().setPrevious(nodeToBeDeleted.getPrevious());
            }
        } else {
            currentNode.setNext(null);
        }
    }
}
```

# 测试循环 DLL

像往常一样，我有一小段代码来检查我们编写的代码是否按预期工作。为此，我们将创建一个整数类型的循环 DLL，向它添加一些值，删除一些值，并在它们之间进行遍历，以测试我们是否做得正确。这方面的代码片段如下:

```
DoubleLinkedList<Integer> linkedList = new DoubleLinkedList<>();

linkedList.push(1);
linkedList.push(2);
linkedList.push(3);

linkedList.addToTail(4);

linkedList.push(1);
linkedList.push(2);
linkedList.push(3);

linkedList.traverse();

System.out.println("Deleting first node with value 2");
linkedList.delete(2);

linkedList.traverse();

System.out.println("Adding value 5 after the node with index 1");
linkedList.addAfterIndex(5, 1);

linkedList.traverse();

System.out.println("Deleting node at index 2");
linkedList.deleteNodeAtIndex(2);

linkedList.traverse();

System.out.println("Deleting node after index 3");
linkedList.deleteNodeAfterIndex(3);

linkedList.traverse();
linkedList.reverseTraverse();
```

其输出如下所示:

```
Forward traverse: 3 -> 2 -> 1 -> 3 -> 2 -> 1 -> 4 -> END
Deleting first node with value 2
Deleted first node with value 2
Forward traverse: 3 -> 1 -> 3 -> 2 -> 1 -> 4 -> END
Adding value 5 after the node with index 1
Forward traverse: 3 -> 1 -> 5 -> 3 -> 2 -> 1 -> 4 -> END
Deleting node at index 2
Forward traverse: 3 -> 1 -> 3 -> 2 -> 1 -> 4 -> END
Deleting node after index 3
Forward traverse: 3 -> 1 -> 3 -> 2 -> 4 -> END
Reverse traverse: 4 -> 2 -> 3 -> 1 -> 3 -> END
```

那个产量符合我们的期望。我们可以有把握地说，我们已经创建了一个循环 DLL 实现。

如果你想直接进入代码，你可以把我的项目放在[我的 Github 库](https://github.com/contactsunny/Circular_Double_LinkedList_Implementation_Java_POC)里，然后开始摆弄它。此外，如果这些没有我想的那么清楚，请在下面的评论中告诉我。

> 在[推特](https://twitter.com/contactsunny)上关注我，了解更多[数据科学](https://blog.contactsunny.com/tag/data-science)、[机器学习](https://blog.contactsunny.com/tag/machine-learning)，以及通用[技术更新](https://blog.contactsunny.com/category/tech)。还有，你可以[关注我的个人博客](https://blog.contactsunny.com/)。