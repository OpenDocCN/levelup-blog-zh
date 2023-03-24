# 文件和目录备份，第 2 部分

> 原文：<https://levelup.gitconnected.com/file-and-directory-backup-part-2-9397dc8b885e>

## 使用 Python 和 PyQt5

![](img/07ee1bc1b026f425580c589e96a4b6f2.png)

来自 Unsplash 的 Jan Antonin Kolar 的照片

在项目的[第一部分](https://zenndogg-52643.medium.com/file-and-directory-backup-part-1-5be98cd7441e)中，我们使用 *Robocopy* 作为实际的文件传输引擎编写了主备份程序。对于一个基本的文件传输脚本来说，它工作得很好，但是正如我们在第 1 部分中提到的，我们如何判断传输何时完成或者程序是否正在运行？该项目可从以下网址下载:

[https://github.com/zazen000/File-and-Directory-Backup](https://github.com/zazen000/File-and-Directory-Backup)

我想要某种视觉指示器向用户显示程序确实在运行。这意味着将 GUI 集成到我们的备份程序中。我选择的 GUI 模块？PyQt5。

在我们继续之前，我想解释一下我对 PyQt5 的使用。我的大部分图形用户界面都是在我的个人电脑桌面上使用的。我有一种特定的“风格”用于我所有的项目；黑底蓝字，无标题。我们稍后会看到为什么这很重要。

另外，我在开始的时候试着把它写成一个文件。我不能让它正常工作。要么指示器会显示，但备份不会工作，要么备份会工作，但指示器不会显示。我最近了解到这是一个被称为“阻塞”的问题。我的解决方案是专门为指示器创建第二个文件。

让我们从进口开始。

```
import sys, os
from PyQt5.QtGui import QFont, QMovie
from PyQt5.QtCore import Qt, QFileSystemWatcher
from PyQt5.QtWidgets import QApplication, QLabel, QWidget
```

由于将这个项目分成两部分，我意识到需要有一种方法让备份脚本告诉 GUI 脚本它已经完成并关闭。我以前曾使用临时文件在脚本间传输数据，并决定用这个来试试。我把那个文件命名为 *oo.txt* 。下一条语句初始化该文件的路径。

```
path = “C:/Users/mount/source/repos/MyDashboard/oo.txt”
```

我在 PyQt5 中的 gui 是一个类。下一节将对 gui 进行初始化、调整大小和定位。

```
class Spinner*(*QWidget*)*:

    def __init__*(*self*)*:
        super*()*.__init__*()*
        self.left   = 315
        self.top    = 1
        self.width  = 20
        self.height = 23
```

从高度和宽度可以看出，实际的 gui 相当小。这是我不想要窗口标题栏的一个原因。另外，你可以看到我选择了一个. gif 文件，而不是一个显示“正在工作”的标签。我们的下一步是设置一些变量。

```
def qblack*(*self*)*:
    self.setWindowFlags*(*Qt.FramelessWindowHint*)*
    self.setGeometry*(*self.left,self.top,self.width,self.height*)*
    self.setAutoFillBackground*(*True*)*
    p = self.palette*()*
    p.setColor*(*self.backgroundRole*()*, Qt.black*)*
    self.setPalette*(*p*)*def qlabel*(*self*)*:
    return “QLabel {color: rgba(80,130,255,255); background-color: black;}”
```

*qblack* 是一个用我想要的参数构建窗口的函数。

qlabel 是一个 PyQt5 QLabel 样式表，带有我想要的参数，我总是将它赋给一个变量。

```
qblack*(*self*)* center = Qt.AlignCentercanda_8 = QFont*(*‘Candalara’, 8*)* q_label = qlabel*(* self *)*
```

接下来，我们设置. gif 文件的实际显示。

```
self.gif = QMovie*(*r’blu_circ.gif’*)*
self.lbl = QLabel*(*self*)*
self.lbl.setStyleSheet*(*q_label*)*
self.lbl.setAlignment*(*center*)*
self.lbl.setGeometry*(*0, 0, 25, 23*)*
self.lbl.setMovie*(*self.gif*)*
self.gif.start*()*
```

在第一行中，微调文件通过 *QMovie* 调用，并分配给 *self.gif.*

下一行调用 *QLabel* ，接下来的四行设置了所需的参数。

*。gif* 被分配给标签，然后开始播放。这是微调器的样子(放大)。

接下来，我们需要一种在适当的时候关闭程序的方法，所以我写了一个退出例程。但是首先，我们需要第一部分中的一个函数:

```
def write_txt_file*(*filename=’’, txt=’’, option=”a”*)*:
    with open*(*filename, option*)* as file:
    file.write*(*‘\n’*)*
    file.write*(*txt*)*
```

我们将它用于以下功能

```
def xit*(*self*)*:
    write_txt_file*(*‘oo.txt’, ‘’, “w”*)*
    self.close*()*
```

当备份程序停止时，它会在我们的文本文件中写入一个空行。

接下来，回忆一下第 1 部分:

```
 count = 0
     logg = log_directory*()*
     lenn = len*(*dirs*)* count += 1 if status == False:

         ensure_directory*(* dst *)*
         compare_directories*(* src, dst *)*
         cmnd = f’robocopy *{*src*} {*dst*} {*swt*}*’
         copi = subprocess.Popen*(* cmnd, shell=False *)*
         code = copi.wait*()*

     codes = range(0, 9)

     if count == lenn and code in codes:
         write_txt_file*(* ‘oo.txt’, ‘this file has changed!’ *)*

 sys.exit*()*
```

我将在这里解释第 1 部分(backup.py)中剩余的代码。在使用*子进程*之前，我是用 *os.system* 调用 *robocopy* 。当备份脚本完成时，它会写入到 *oo.txt* 。x *it()* 用于清除 *oo.txt* 并关闭文件。

这很有效，但不是我想要的方式。时机不对。文本文件是在 python 文件关闭时写入的，而不是在 *robocopy* 完成时。那是不行的。我点燃了互联网，开始寻找答案。经过几个小时的研究和几天的无所事事，我的意思是实验，我想出了解决办法；*子流程. Popen.*

*子流程。Popen* 不仅打开 *os* 文件，它还使用 *wait()* 等待完成代码。太棒了。我运行了备份，得到代码=2。如果语句为，则*的版本 1；*如果代码= 2:* 。*

我运行过一次。成功了！我又运行了一次。成功了！我又运行了一次。失败了！

猜猜我学到了什么？还有更多代码。

由于我真的不在乎代码是什么，所以只返回了那个代码。答案是:

```
codes = range(0, 9)
```

并且:

```
if code in codes:
```

到目前为止，一切顺利。但是每隔一段时间，在这个过程中会返回一个代码，而不是之后。我认为这与 dirs 被迭代的方式有关。

我需要别的东西。我的解决方案是计算迭代次数，将这个数字与我的目录列表的长度进行比较。当两者匹配并返回代码时，文本文件被写入。

哈利路亚！我们完了。

不完全是。

我们已经让备份程序在正确的时间写入我们的文本文件，但是 *Spinner()* 如何知道该文件何时发生了变化？

我不知道。

我在 StackOverflow 上发布了这个问题，一个名叫 musicmante 的响应者建议 *QFileSystemWatcher* 。一个建议把一切都联系在了一起。这是 Spinner 类的最后一部分。

```
if __name__ == ‘__main__’:
    watcher = QFileSystemWatcher*()*
    watcher.addPath*(*path*)*
    app = QApplication*([])*
    e = Spinner*()*
    e.show*()*
    watcher.fileChanged.connect*(*e.xit*)*
    sys.exit*(*app.exec_*())*
```

我们分配观察器和要观察的路径(和文件)。路径已在前面指定。

当备份程序写到 *oo.txt* 时，那就是对文件的改变。Watcher 检测到这一变化，调用 *xit()* 清理文件并关闭微调器。

找到了。现在我是一个快乐的露营者。

我用一个“交通警察”程序来打开备份文件和微调文件。完整的代码如下。

```
import os

os.system*(*r”start /min python backup.py”*)*
os.system*(*r”start /min python backup_main.py”*)*
```

这是 Spinner 的完整代码。请注意:我是从我写的一个 PyQt5 支持模块中导入 *qblack、qlabel* 和 *write_text_file* 的。您可以选择做同样的事情，或者将这些功能合并到下面的脚本中。

如果你喜欢阅读这样的故事，并想支持我成为一名作家，可以考虑每月 5 美元订阅 Medium。作为会员，你可以无限制地访问媒体上的故事。如果你用我的[链接](https://zenndogg-52643-medium.com/membership)注册，我会赚一小笔佣金。

```
import sys, os, subprocess
from qtpy_cfg import qblack, qlabel, write_text_file
from PyQt5.QtGui import QFont, QMovie
from PyQt5.QtCore import Qt, QFileSystemWatcher
from PyQt5.QtWidgets import QApplication, QLabel, QWidget

path = “C:/Users/mount/source/repos/MyDashboard/oo.txt”

class Spinner*(* QWidget *)*:
*‘’’*

 *Creates a window for blu_circ.gif, a spinning indicator*
 *that shows the backup operation is being performed*

*‘’’*

    def __init__*(*self*)*:
        super*()*.__init__*()*
        self.left = 315
        self.top = 1
        self.width = 20
        self.height = 23

        qblack*(* self *)* # PyQt5 window initialization function
        center = Qt.AlignCenter
        canda_8 = QFont*(*‘Candalara’, 8*)*
        q_label = qlabel*(* self *)* # PyQt5 label stylesheet function

        self.gif = QMovie*(* r’blu_circ.gif’ *)*
        self.lbl = QLabel*(* self *)*
        self.lbl.setStyleSheet*(* q_label *)*
        self.lbl.setAlignment*(* center *)*
        self.lbl.setFont*(* canda_8 *)*
        self.lbl.setGeometry*(* 0, 0, 25, 23 *)*
        self.lbl.setMovie*(* self.gif *)*
        self.gif.start*()*

    def xit*(*self*)*:
    *‘’’*

    *Resets the status-check file ‘oo.txt’*
    *to a blank file and closes the spinner*

    '*’’*
        write_txt_file*(*‘oo.txt’, ‘’, “w”*)*
        self.close*()*

 if __name__ == ‘__main__’:
 watcher = QFileSystemWatcher*()*
 watcher.addPath*(* path *)* # Path to the watched file
 app = QApplication*( [] )*
 e = Spinner*()*
 e.show*()*
 watcher.fileChanged.connect*(* e.xit *)*
 sys.exit*(* app.exec_*() )*
```

backup.py 的代码是:

```
import os
import sys
import time
import filecmp
import subprocess

 dirs = *(*
     *(*r”C:\Users\mount\source\repos”, r”M:\_BACKUP\REPOS”*)*,
     *(*r”D:\mount\Downloads”, r”M:\_BACKUP\DOWNLOADS”*)*,
     *(*r”D:\mount\Documents”, r”M:\_BACKUP\DOCUMENTS”*)*,
     *(*r”D:\Internet-Marketing”, r”M:\_BACKUP\IM”*)*,
     *(*r”D:\_HOLOSYNC”, r”M:\_BACKUP\HOLOSYNC”*)*,
     *(*r”D:\mount\Music”, r”M:\_BACKUP\MUSIC”*)*,
     *(*r”D:\_BIZ”, r”M:\_BACKUP\BIZ”*)*,
     *(*r”D:\_PWA”, r”M:\_BACKUP\PWA”*)*,
     *(*r”C:\data\db”, r”M:\_BACKUP\DB”*)*,
     *(*r”C:\ProgramData\MongoDB”, r”M:\_BACKUP\MONGODB “*)*,
 *)*

 def ensure_directory*(* dst *)*:
 *‘’’*

 *If the destination folder doesn’t exist, create it*

 *‘’’*

     directory = os.path.dirname*(* dst *)*
     if not os.path.exists*(* directory *)*:
         os.makedirs*(* directory *)*

 def compare_directories*(* src, dst *)*:
 *‘’’*

 *Compares the source and destination directories.*
 *Returns False if not the same.*
 *True means both directories and files match and no backup is required.*

 *‘’’*

     try:
         comp = filecmp.dircmp*(* src, dst *)*
         common = sorted*(* comp.common *)*
     except:
         return False

     left = sorted*(* comp.left_list *)*
     right = sorted*(* comp.right_list *)*
     if left != common or right != common:
         return False

     if len*(* comp.diff_files *)*:
         return False

     for subdir in comp.common_dirs:
     left_subdir = os.path.join*(* src, subdir *)*
     right_subdir = os.path.join*(* dst, subdir *)*
     return compare_directories*(* left_subdir, right_subdir *)*

 return True

 def log_directory*()*:
 *‘’’*

 *Creates a LOG directory, if it does not exist*
 *then creates a sub-directory named for date and*
 *time for the backup log*

 *‘’’*
     now = time.strftime*(*“%Y-%m-%d___%H-%M”*)*
     direct = os.path.dirname*(*“C:\\Users\\mount\\source\\repos\\MyDashboard\\LOG\\”*)*
     directory = direct + ‘\\’ + now + ‘\\’
     if not os.path.exists*(* directory *)*:
         os.makedirs*(* directory *)*
 return directory

 count = 0
 logg = log_directory*()*
 lenn = len*(*dirs*)*

 for dir in dirs:
 '''
 src = source directory
 dst = destination directory
 swt = switches for robocopy:
 /xo = only newer versions of file,
 /s = all occupied sub-directories,
 /MT:nn = # of threads (maximum=128, default=8)
 /xx = copy source file even when destination file does not exist
 /LOG+ = a log is created for every dir in dirs. /LOG+ appends all logs to one file
 /r:n = number of times to retry, default = 1,000,000 ### you WILL want to use this switch
 /w:nn = number of seconds to wait before retrying, default is 30
 ''' count += 1
     src, dst, swt = dir*[*0*]*, dir*[*1*]*, f”/XX /r:2 /xo /s /w:5 /MT:128 /LOG+:*{*logg*}*_BACKUP.log”
     status = *(*compare_directories*(*src, dst*))*

     if status == False:

         ensure_directory*(* dst *)*
         compare_directories*(* src, dst *)*
         cmnd = f’robocopy *{*src*} {*dst*} {*swt*}*’
         copi = subprocess.Popen*(* cmnd, shell=False *)*
        code = copi.wait*()*

     codes = range*(*0, 9*)*

     if count == lenn and code in codes:
         write_txt_file*(* ‘oo.txt’, ‘this file has changed!’ *)*

 sys.exit*()*
```