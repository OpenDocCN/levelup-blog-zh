# 修改 Linux 内核

> 原文：<https://levelup.gitconnected.com/create-your-own-system-calls-part-2-process-weights-97d5e66f56f3>

## 如何添加新的系统调用

![](img/5e818d3a2effe91362bd817f70376c32.png)

马库斯·斯皮斯克在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在本文中，我们将学习如何修改 linux 内核，添加我们自己独特的系统调用，并最终用我们添加的功能构建内核。

在我们开始修改 Linux 内核之前，我们必须以某种方式下载它。我将详细说明我所采取的步骤，因此请确保您准确地遵循这些步骤，以保证获得相同的结果。

*   下载虚拟机软件，如 Vmware \ VirtualBox
*   下载一张 Ubuntu 18.04 的图片[http://releases.ubuntu.com/18.04/](http://releases.ubuntu.com/18.04/)
*   使用下载的映像从虚拟机软件设置虚拟操作系统

一旦 Ubuntu 加载完毕，打开终端并跟随操作

*   安装先决条件

```
>> sudo sed -i "s/# deb-src/deb-src/g" /etc/apt/sources.list
>> sudo apt update -y
>> sudo apt install -y build-essential libncurses-dev bison flex
>> sudo apt install -y libssl-dev libelf-dev
```

*   下载 Linux 源代码

```
>> cd ~
>> apt source linux
```

*   更改权限和重命名文件夹

```
>> sudo chown -R student:student ~/linux-4.15.0/
>> mv ~/linux-4.15.0 ~/linux-4.15.18-custom
```

*   配置内核构建过程

```
>> cd ~/linux-4.15.18-custom
>> cp -f /boot/config-$(uname -r) .config
>> geany .config
# search the CONFIG_LOCALVERSION parameter and set it to "-custom"
>> yes '' | make localmodconfig
>> yes '' | make oldconfig
```

*   编译内核

```
>> make -j $(nproc)
```

*   安装内核模块和映像

```
>> sudo make modules_install
>> sudo make install
```

*   配置 GRUB

```
>> sudo geany /etc/default/grub
```

文件打开后，请执行以下操作

*   将“GRUB_DEFAULT”设置为“Ubuntu，带 Linux 4 . 15 . 18-自定义”
*   将“GRUB_TIMEOUT_STYLE”设置为菜单
*   将“GRUB_TIMEOUT”设置为 5
*   在的末尾添加一行:“GRUB_DISABLE_SUBMENUE=y”

最后，我们必须生成 GRUB 配置文件，并使用

```
>> sudo update-grub
>> sudo reboot
```

一旦操作系统启动，确保您加载了自定义内核

```
>> uname -r
```

结果应该是“4 . 15 . 18-自定义”

就这样，我们完成了先决条件，是时候编码了。

我们将要添加的功能称为进程权重。
顾名思义，我们将为每个流程分配一个权重，以表示它有多“重”。

我们希望保持的两种行为是:

*   当一个进程被分叉时，子进程将拥有与其父进程相同的权重
*   初始化进程权重将为 0

我们将要实现的系统调用将能够

*   设置当前流程的权重
*   递归获取当前进程的总权重

首先，我们需要以某种方式告诉每个过程“仅供参考，您现在有一个重量了”。为了做到这一点，打开`~/linux-4.15.18-custom/include/linux/sched.h` 并在结构`task_struct`中添加一个属性`int weight`

```
struct task_struct {
#ifdef CONFIG_THREAD_INFO_IN_TASK
 /*
  * For reasons of header soup (see current_thread_info()), this  
  * must be the first element of task_struct.
  */ 
struct thread_info  thread_info;
#endif
 /* -1 unrunnable, 0 runnable, >0 stopped: */ 
***int                  weight;* #line 569**
volatile long   state; 
/*  
 * This begins the randomizable portion of task_struct. Only
 * scheduling-critical items should be added above here.  
 */ 
randomized_struct_fields_start
```

现在，我们想告诉每一个进程他的初始权重是多少，为了做到这一点，在与之前相同的目录下，打开`init_task.h`——转到`INIT_TASK`的宏定义，并为您刚刚添加的权重属性添加一个初始化。

```
#define INIT_TASK(tsk) \
{         \
 INIT_TASK_TI(tsk)      \
 ***.weight  = 0,      \ #line 228***
 .state  = 0,      \
 .stack  = init_stack,     \
...
```

在上一节中，我们能够告诉每个进程，它有一个名为 weight 的新属性，并且每当创建一个新进程时，该属性应该初始化为 0。

在这一节中，我们将着重于为我们的新系统调用建立基础。

导航到`~/linux-4.15.18-custom/arch/x86/entry/syscalls/`并打开`syscall_64.tbl`
滚动到文件底部并保留您的系统调用号

```
...
332 common statx   sys_statx
333 common hello   sys_hello
***334 common set_weight  sys_set_weight
335 common get_total_weight sys_get_total_weight
...***
```

现在我们将创建我们的系统调用签名。转到同一目录下的`syscalls.h`,滚动到文件底部

```
...
asmlinkage long sys_pkey_free(int pkey);
asmlinkage long sys_statx(int dfd, const char __user *path, unsigned  flags,     unsigned mask, struct statx __user *buffer);
asmlinkage long sys_hello(void);
***asmlinkage long sys_set_weight(int weight); #line 944
asmlinkage long sys_get_total_weight(void);***
#endif
```

我们已经设置好了一切，唯一缺少的是这些新系统调用的实现。

导航到`~/linux-4.15.18-custom/kernel`并创建一个名为`syscalls_weight.c`的新文件。
不要忘记进入同一个目录下的`Makefile`,并将您的新文件添加到构建过程中

```
# SPDX-License-Identifier: GPL-2.0
#
# Makefile for the linux kernel.
# 
obj-y = fork.o exec_domain.o panic.o \
cpu.o exit.o softirq.o resource.o \ 
sysctl.o sysctl_binary.o capability.o ptrace.o user.o \ 
signal.o sys.o umh.o workqueue.o pid.o task_work.o \ 
extable.o params.o \ 
kthread.o sys_ni.o nsproxy.o \ 
notifier.o ksysfs.o cred.o reboot.o \ 
async.o range.o smpboot.o ucount.o hello_syscall.o ***syscalls_weight.o*** ...
```

打开您刚刚创建的文件`syscalls_weight.c`，让我们实现新的系统调用。

首先，包括以下库

```
#include <linux/kernel.h>
#include <linux/list.h>
#include <linux/module.h>
#include <linux/sched.h>
```

让我们从`sys_set_weight`的实现开始

```
asmlinkage long sys_get_weight(int weight){
  if(weight < 0){
    return -EINVAL;
  }
  current->weight = weight;
  return 0;
}
```

需要注意的几件事

*   `current`是当前运行任务的指针
*   syscalls 的约定是如果成功就返回 0，如果出现错误就返回负值，这正是我们所做的(考虑到我们不想允许负权重)。

转到下一个 syscall 实现，我们将首先定义另一个对我们有帮助的函数

```
int traverse_children_sum_weight(struct task_struct *root_task){
  struct task_struct *task;
  struct list_head *list;
  int sum = root_task->weight;

  list_for_each(list, &root_task->children){
    task = list_entry(list, struct task_struct, sibling);
    sum += traverse_children_sum_weight(task, true);
  }
  return sum;
```

然后，我们的系统调用实现将是

```
asmlinkage long sys_get_total_weight(void){
  return traverse_children_sum_weight(current);
}
```

我们做到了。你现在所要做的就是构建你的内核，重启你的机器，然后你就可以自由地使用你刚刚创建的这些全新的内核特性了。

要构建并重启，只需执行以下命令

```
make -j $(nproc)
sudo cp -f arch/x86/boot/bzImage /boot/vmlinuz-4.15.18-custom 
sudo reboot
```

你能想到一些有趣的新功能吗？也许这将是你的下一个编码项目。

# 最后一句话

如果你想看更多关于这个话题和其他话题的内容，可以看看我的博客。