# 文件和目录备份，第 1 部分

> 原文：<https://levelup.gitconnected.com/file-and-directory-backup-part-1-5be98cd7441e>

## 使用 Python 和 PyQt5

![](img/07ee1bc1b026f425580c589e96a4b6f2.png)

来自 Unsplash 的 Jan Antonin Kolar 的照片

我们都知道备份文件很重要。我想用 python 写自己的备份程序。这个项目可以从 Github 下载，网址是:

https://github.com/zazen000/File-and-Directory-Backup

我的要求是在程序运行时，不要引人注目，只显示一条小消息。很简单，对吧？毕竟，这是有着过多内部和外部模块的 python。

我花了很长时间完成最后一部分，需要三个独立的文件，我们将会看到。但是，首先，主备份程序的导入。

```
import osimport sysimport timeimport filecmpimport subprocess
```

我们需要设置想要备份的源和目标目录。我在一个元组中有 10 个源/目标对:

```
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
```

对于每一行，第一个参数是源目录，第二个参数是目标目录。如你所见，这几乎是我电脑上的所有数据目录。现在来写一些函数。

我用 robocopy.exe 的*作为复制引擎。Robocopy 将现有的源目录复制到现有的目的目录，但是如果目的目录不存在呢？我们需要一个函数来检查并建立一个子目录，如果它没有。*

```
def ensure_directory*(* destination *)*: directory = os.path.dirname*(* destination *)*
    if not os.path.exists*(* directory *)*:
    os.makedirs*(* directory *)*
```

函数*确保目录*检查目标文件夹是否存在。如果没有，则*操作系统*创建目录。

接下来，我们需要比较源目录和目标目录的内容。如果它们不匹配(大小、日期等)，则启动复制例程。所以，当然，我们需要一个函数来比较目录。我叫它，*比较 _ 目录*。

```
def compare_directories*(* source, destination *)*: try:comp = filecmp.dircmp*(* source, destination *)* common = sorted*(* comp.common *)* except:return Falseleft = sorted*(* comp.left_list *)* right = sorted*(* comp.right_list *)* if left != common or right != common:return Falseif len*(* comp.diff_files *)*:return Falsefor subdir in comp.common_dirs:left_subdir = os.path.join*(* source, subdir *)* right_subdir = os.path.join*(* destination, subdir *)* return compare_directories*(* left_subdir, right_subdir *)* return True
```

使用 *filecmp.dircmp* ，将源目录与目标目录进行比较，并对对象进行排序。有三种可能会返回布尔值 False，从而启动备份序列。

如果 *sorted(comp.common)* 有错误，则返回 False。

如果*排序(比较左列表)*或*排序(比较右列表)*对象不同于*排序(比较公共)*对象，则返回 False。

最后，如果源目录和目标目录中的文件数量不同，则返回 False。

当三个都为真时，返回一个真；不需要后援。

我决定在我的项目中添加日志功能。在研究 *robocopy 的时候，*我发现它有一个丰富的日志工具包。这意味着我们需要另一个函数， *log_directory* 。

```
def log_directory*()*: now = time.strftime*(*“%Y-%m-%d___%H-%M”*)*
    direct = os.path.dirname*(*“C:\\Users\\mount\\source\\repos\\MyDashboard\\LOG\\”*)*
    directory = direct + ‘\\’ + now + ‘\\’ if not os.path.exists*(* directory *)*:
        os.makedirs*(* directory *)* return directory
```

第一行创建日期/时间字符串。该函数的第二行指向存储日志目录的路径。如果语句写入文件夹，则第三行命名文件夹和*。*

*Robocopy* 使用 *_BACKUP.log* 作为它的日志文件名，并且为每个源/目标对拷贝序列写入，或者在我们的例子中，写入 10 次。然后我发现，对于每次备份运行，可以将单独的日志附加到一个日志文件中。问题解决了！

不完全是。

我早上备份了一次，下午又备份了一次。当我去检查日志文件时，只有一个，因为旧的那个被覆盖了。

如何更改每个备份的文件名？当时，我不知道。

我想到的解决方案是创建一个文件夹，并以备份的日期和时间命名。该运行的 *_BACKUP.log* 放在该文件夹中。

或者，您可以根据日期和时间来命名文件本身，而放弃多个目录。

这是一个源/目标对的日志文件的样子。

```
 — — — — — — — — — — — — — — — — — — — — — — — — — — — — — — — — — — 
 ROBOCOPY :: Robust File Copy for Windows 
 — — — — — — — — — — — — — — — — — — — — — — — — — — — — — — — — — — 

 Started : Monday, January 18, 2021 7:53:59 PM
 Source : C:\Users\mount\source\repos\
 Dest : M:\_BACKUP\REPOS\

 Files : *.*

 Options : *.* /S /DCOPY:DA /COPY:DAT /XX /XO /MT:128 /R:2 /W:5 

 — — — — — — — — — — — — — — — — — — — — — — — — — — — — — — — — — — 

 Newer 353 C:\Users\mount\source\repos\trading\get_symbols.py
 100% 
 Newer 26307 C:\Users\mount\source\repos\trading\ubStock_Research.py
 100% 
 — — — — — — — — — — — — — — — — — — — — — — — — — — — — — — — — — —        Total  Copied Skipped Mismatch FAILED Extras
  Dirs : 618    618    612      0        0      89
 Files : 34096   9     34087    0        0     119
 Bytes : 2.964g 76.6k  2.964g   0        0     108.21 m
 Times : 0:01:09 0:00:16 0:00:00 0:00:15
```

这些日志中的一个被附加到序列中十个源/目标对的日志文件中。现在来写一些逻辑。

```
count = 0
logg = log_directory*()*
lenn = len*(*dirs*)*
```

首先，我们初始化一些变量。 *logg* 调用 *log_directory* ，c*count*被初始化为 0，并且 *lenn* 是 dirs 的长度，我们的源/目的地对列表。

在继续之前，我想展示一下 *robocopy* 的语法:

```
>robocopy source_directory destination_directory switches
```

我们为上述参数分配变量。

src =源目录
dst =目标目录
swt = robo copy 的开关

这些是我使用的开关及其用途:

```
 /xo = only newer versions of file,
 /s = all occupied sub-directories,
 /MT:nn = # of threads (maximum=128, default=8)
 /xx = copy source file even when destination file does not exist
 /LOG+ = a log is created for every d in dirs. /LOG+ appends all logs to one file
 /r:n = number of times to retry, default = 1,000,000
 /w:nn = number of seconds to wait before retrying
```

请注意，所使用的开关在备份日志中的 Options:下有注释。

现在，我们可以迭代通过 *dirs* 。

```
for dir in dirs: count += 1
    src, dst, swt = dir*[*0*]*, dir*[*1*]*, f”/XX /r:2 /xo /s /w:5 /MT:128 /LOG+:*{*logg*}*_BACKUP.log” status = *(*compare_directories*(*src, dst*))*
```

剩下的代码。

```
 if status == False:

        ensure_directory*(* dst *)*
        compare_directories*(* src, dst *)*
        cmnd = f”robocopy *{*src*} {*dst*} {*swt*}”*
        copi = subprocess.Popen*(* cmnd, shell=False *)*
        code = copi.wait*()*

     codes = range(0,9)

     if count == lenn and code in codes:
         write_txt_file*( “*oo.txt”, “this file has changed!” *)*

 sys.exit*()*
```

当 *compare_directories* 返回 False 时， *if status == False* 语句生效。首先，我们确定目标目录是否存在，如果不存在就创建它们。然后，*比较目录*再次被调用。

接下来，初始化 robocopy 复制序列的变量。剩下的代码是我在尝试将这个程序与显示“工作中…”标签的 GUI 集成时遇到的一个问题的解决方案。

如果我们去掉上面的五行代码，这个程序应该运行得很好(实际上，它运行得很好)。那么，我们如何知道备份何时完成？我们怎么知道它是否在运行？

我花了点时间，但我想出了一个办法。

但这是第二部分的内容。

如果你喜欢阅读这样的故事，并想支持我成为一名作家，可以考虑每月 5 美元订阅 Medium。作为会员，你可以无限制地访问媒体上的故事。如果你用我的[链接](https://zenndogg-52643-medium.com/membership)注册，我会赚一小笔佣金。