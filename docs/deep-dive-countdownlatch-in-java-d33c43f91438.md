# Java 中的深层 CountDownLatch

> 原文：<https://levelup.gitconnected.com/deep-dive-countdownlatch-in-java-d33c43f91438>

![](img/c5d6fd04bba30f2a5b69a432cbb8d3f4.png)

Java 中的 CountDownLatch

# 什么？

Java 中的 [CountDownLatch](https://docs.oracle.com/javase/7/docs/api/java/util/concurrent/CountDownLatch.html) 是一种[同步机制](https://docs.oracle.com/javase/tutorial/essential/concurrency/sync.html)，允许一个[线程](https://docs.oracle.com/javase/7/docs/api/java/lang/Thread.html)在开始处理之前等待一个或多个线程。这是一个非常关键的需求，在服务器端应用程序中经常需要，将这一功能内置为 [CountDownLatch](https://docs.oracle.com/javase/7/docs/api/java/util/concurrent/CountDownLatch.html) 极大地简化了开发。

# 为什么？

在 Java 中使用 [wait 和 notify](https://docs.oracle.com/javase/tutorial/essential/concurrency/guardmeth.html)可以实现类似的行为，但是这需要努力和专业知识，并且在第一次尝试中让它写起来很棘手，使用 [CountDownLatch](https://docs.oracle.com/javase/7/docs/api/java/util/concurrent/CountDownLatch.html) 只需几行就可以完成。 [CountDownLatch](https://docs.oracle.com/javase/7/docs/api/java/util/concurrent/CountDownLatch.html) 还允许主线程等待线程数量的灵活性，它可以等待一个线程或 n 个线程，代码没有太大变化。

[CountDownLatch](https://docs.oracle.com/javase/7/docs/api/java/util/concurrent/CountDownLatch.html) 保护您免受线程丢失[信号](https://www.oreilly.com/library/view/java-threads-3rd/0596007825/ch04.html)的情况，如果您使用这些其他机制来协调作业，就会发生这种情况。类似于[条件](https://docs.oracle.com/javase/7/docs/api/java/util/concurrent/locks/Condition.html)的东西对于[向](https://www.oreilly.com/library/view/java-threads-3rd/0596007825/ch04.html)[线程](https://docs.oracle.com/javase/7/docs/api/java/lang/Thread.html)发送信号是有用的，如果它们正在等待，但是如果它们不在也没关系，或者[线程](https://docs.oracle.com/javase/7/docs/api/java/lang/Thread.html)将在等待之前明确检查它是否必须等待。使用 [CountDownLatch](https://docs.oracle.com/javase/7/docs/api/java/util/concurrent/CountDownLatch.html) ，如果信号尚未被触发，我们将等待该信号，但如果该信号在我们开始等待之前已经被触发，我们将立即继续等待。

# 什么时候？

[CountDownLatch](https://docs.oracle.com/javase/7/docs/api/java/util/concurrent/CountDownLatch.html) 在[线程之一](https://docs.oracle.com/javase/7/docs/api/java/lang/Thread.html)像主线程一样，需要等待一个或多个线程完成后才开始处理时很有用。在 Java 中使用 [CountDownLatch](https://docs.oracle.com/javase/7/docs/api/java/util/concurrent/CountDownLatch.html) 的一个经典实例是，当我们[将一个文件分配](https://docs.oracle.com/cd/A87860_01/doc/server.817/a76965/c29dstpr.htm)成固定数量的数据块，我们将这些数据块发送给不同的线程进行处理，在所有线程都完成了[处理](https://en.wikipedia.org/wiki/Distributed_computing)之后，只有[文件](https://docs.oracle.com/javase/7/docs/api/java/io/File.html)应该被关闭。

# 工党

考虑这样一个例子，一个组织需要招聘三名开发人员。为此，人力资源经理邀请了三位技术主管参加面试。人力资源经理想在招聘完三名开发人员后才分发聘书。在线程术语中，人力资源经理应该等到招聘了三名开发人员。

> 导入 Java . util . concurrent . countdownlatch；
> 
> 公共类 HRManager{
> 
> public static void main(String args[]){
> CountDownLatch CountDownLatch = newCountDownLatch(3)；
> 
> tech lead tech lead 1 = new tech lead(countDownLatch，" first ")；
> tech lead tech lead 2 = new tech lead(countDownLatch，“秒”)；
> tech lead tech lead 3 = new tech lead(countDownLatch，“第三”)；
> 
> tech lead 1 . start()；
> tech lead 2 . start()；
> techlead 3 . start()；
> 
> 试试{
> System.out.println("HR 经理等待招聘完成…")；
> 
> countdownlatch . await()；
> 
> System.out.println("分发录用函")；
> } catch(interrupted exception e){
> e . printstacktrace()；
> }
> }
> }

现在，让我们创建将接受采访的技术负责人

> 导入 Java . util . concurrent . countdownlatch；
> 
> 公共类 TechLead 扩展线程{
> 
> CountDownLatch countDownLatch
> public tech lead(CountDownLatch CountDownLatch，String name){
> super(name)；
> this . countDownLatch = countDownLatch；
> 
> }
> 
> [@覆盖](http://twitter.com/Override)
> public void run(){
> try {
> 
> 线程.睡眠(2000 年)；
> }catch(中断异常 e){
> 
> e . printstacktrace()；
> }
> 
> system . out . println(thread . current thread()。getName()+":recruited ")；
> 
> countdownlatch . count down()；
> }
> 
> }

# 谨防垄断！

[CountDownLatch](https://docs.oracle.com/javase/7/docs/api/java/util/concurrent/CountDownLatch.html) 是一个非常方便的类，可以在两个或多个线程之间实现[同步](https://docs.oracle.com/javase/tutorial/essential/concurrency/sync.html)，允许一个或多个线程等待，直到一组正在其他线程中执行的操作完成。CountDownLatch 在适当的情况下可以节省你的时间，你必须意识到这个类。和往常一样，如果代码不好，线程的同步会引发[死锁](https://docs.oracle.com/javase/tutorial/essential/concurrency/deadlock.html)。

感谢您的阅读💜