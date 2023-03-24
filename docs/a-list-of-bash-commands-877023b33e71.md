# bash 命令列表

> 原文：<https://levelup.gitconnected.com/a-list-of-bash-commands-877023b33e71>

## 我发现自己在所有项目中都经常使用 bash，所以这里列出了您可能需要的所有命令

![](img/742acddf0833d05d0778684eeb3e491f.png)

加布里埃尔·海因策在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

Bash 是 Unix 中使用最多的 shell，因为它是我们都有的默认 shell。在这篇博客中，我的目标是广泛涵盖所有我认为重要的 bash 命令。

## 谁应该阅读这个博客？

*   刚刚开始 Unix 之旅的人
*   希望在 Unix 之旅中前进的人
*   认为巴什很酷的人
*   想通过学习 bash 变酷的人(这就是我)
*   每个人都可以为了娱乐或额外学习而阅读

## 这个博客是如何构建的？

我列出了一个 bash 函数的列表，并对它们进行了排列，第一个是我使用最多/最有用的函数，最后一个是最少使用的。根据我的经验，结构是主观的，所以可以跳过你不需要的功能。每个人都可以根据自己的需要选择在哪里停止阅读列表。

你可以解析这个博客的另一种方法是通过在博客中找到这些标签，我已经使用它们来根据它们做什么或者如何使用来分类所有的命令。

#插入 _ 更新 _ 删除 _ 文件 _ 文件夹| #终端 _ 进程| #搜索 _ 显示 _ 文件| #显示 _ 数据

我们开始吧。

[吉菲](https://giphy.com/gifs/disneychannelofficial-4xWGyVKoXqg2eVCiq9)

**pwd** :检查你当前所在目录的路径(#display_data)

```
$ pwd
/home/harsh/test_directory
```

**mkdir :** 在当前目录(# insert _ update _ delete _ file _ folder)下新建一个目录

```
$ mkdir directory_name
$ mkdir /path/to/directory/directory_name 
```

**触摸:**在当前目录(# insert _ update _ delete _ file _ folder)下新建一个文件

```
$ touch filename.extension
$ touch /path/to/file/filename.extension
```

**ls** :显示一个目录的所有内容(#display_data)

```
//display for current directory
$ ls//display for some folder
$ ls /path/to/folder//run ls with different options
$ ls [options]
```

**cd :** 转到特定文件夹(# insert _ update _ delete _ file _ folder)

```
$ cd /absolute/path/to/folder
$ cd relative/path/to/folder
```

**rm :** 删除文件或文件夹(#插入 _ 更新 _ 删除 _ 文件 _ 文件夹)

```
$ rm /path/to/file
$ rm -r /path to folder
```

删除文件夹时需要-r 选项，因为它确保递归删除完整的内容。还有其他像-f 这样的标签，但是最好不要使用-f。

**rmdir :** 删除一个空目录(# insert _ update _ Delete _ file _ folder)

```
$ rmdir /path/to/directory
```

将文件和文件夹从一个路径复制到另一个路径(# insert _ update _ delete _ file _ folder)

```
//copy a source file to a target folder
$ cp path/to/source/file path/to/target/folder//copy a source folder to a target folder
$ cp -r path/to/source/folder path/to/target/folder
```

**mv :** 将文件或文件夹从源文件夹剪切并粘贴到目标文件夹(# insert _ update _ delete _ file _ folder)

```
//move a source file to a target folder
$ mv path/to/source/file path/to/target/folder//move a source folder to a target folder
$ mv path/to/source/folder path/to/target/folder//rename a file
$ mv present_filename new_filename
```

**man :** 显示任何 Linux 命令的用户手册的命令。(#display_data)

```
$ man <command_name>
$ man printf
```

**清除:**清除整个终端(#display_data)

```
$ clear
```

**nano :** 大多数 linux 终端中默认的文本编辑器(# insert _ update _ delete _ file _ folder)

```
//create and open a new file
$ nano new_filename
//then save the file using Ctrl+o, check other commands [here](https://linux.die.net/man/1/nano)
```

**echo :** 显示文本或字符串的值，环境变量(#display_data)

```
$ echo Hello
Hello$ x=10
$ echo $x
10$ echo -e "Hello \bWorld"
HelloWorld
```

**cat :** 在终端上显示文件内容(#display_data)

```
$ cat filename
```

**sudo :** 允许您以 root 用户身份运行命令(这需要用于需要更高批准的功能)(#terminal_processes)

```
$ sudo <command>
$ sudo nano filename
$ sudo apt update
```

**chmod :** 更改文件或文件夹的权限(# insert _ update _ delete _ file _ folder)

```
$ chmod <permission_flags> filename//make a file readable, writeable and exeuctable
$ chmod 777 filename//chmod for folder
$ chmod -R 777 foldername
```

**ps :** 显示正在进行的进程(#terminal_processes)

```
$ ps//print processes for all users, with the user name and processes //not attached to terminal
$ ps aux//find process with a keyword
$ ps aux | grep string
```

**top :** 显示处理器活动(#终端 _ 进程)

```
//list all running linux processes
$ top
//list user specific processes
$ top -u username
```

我个人总是使用 ps aux，但为了连续性，我在这里提到它

**kill :** 手动终止进程(#terminal_processes)

```
$ kill <flags> <process_id>
//pass SIGKILL to shutdown process immediately
$ kill -9 <process_id>
```

**grep :** 搜索特定字符串的文件和其他内容(#search_display_files)

```
$ grep [options] pattern [files]
//case insensitive search
$ grep -i "UNix" filename.txt
```

**查找:**搜索特定文件或文件夹(#search_display_files)

```
$ find <root_folder> <options> <what_to_find>//execute a command on the found files
$ find . -name sample.txt -exec <command> -i {} \;//delete all files named sample.txt starting in the current folder
$ find . -name sample.txt -exec rm -i {} \;
```

**定位:**定位特定文件(#search_display_files)

```
$ locate filename
```

**退出:**从 shell (#terminal_processes)中退出

```
$ exit//exit with a status 10
$ exit 10
```

**历史:**打印之前执行的命令(#display_data)

```
//print all commands
$ history
//print last 10 commands
$ history 10
//use history with grep to find a past command
$ history | grep sudo 
```

查看如何使用感叹号(！)也是

**其中:**打印文件的完整路径(#display_data)

```
$ which <options> filename
$ which ping
/bin/ping
//print all paths
$ which -a touch
```

**curl :** 从服务器传输数据或向服务器传输数据。Curl 用于下载上传数据(#插入 _ 更新 _ 删除 _ 文件 _ 文件夹)

```
$ curl <option> url
$ curl example.com
//download a file and save using a particular filename
$ curl -o filename.json https://www.example.com/file.json
```

**wget :** 从网络下载文件(# insert _ update _ delete _ file _ folder)

```
$ wget [https://www.example.com/file.json](https://www.example.com/file.json)
$ wget -O filename.json https://www.example.com/file.json
```

使用此流编辑器(# insert _ update _ delete _ file _ folder)从终端编辑或查看文件等

```
//display 5th to 10th line in myfile.txt
$ sed -n '5,10p' myfile.txt//replace all occurrence of hi with bye
$ sed 's/hi/bye/g' myfile.txt
```

sed 可以添加行和做更多的事情，请谨慎使用

**少:**一次显示文件内容或命令输出一页(#display_data)

```
$ less filename
$ ps aux | less
```

**更多:**显示可滚动浏览的文件内容或命令输出(#display_data)

工作方式类似于更少，唯一的区别是更少更快

**if :** 在终端上实现 if(# terminal _ processes)

```
if COMMANDS; then COMMANDS; elif COMMANDS; then COMMANDS; else COMMANDS; fi
```

**compgen :** 列出所有 linux 命令(#display_data)

```
$ compgen -c
//find a command
$ compgen -c | grep find
```

**头:**显示文件的前 n 行(#display_data)

```
//Display first 10 lines
$ head filename
//Display first 30 lines
$ head -n 30 filename
$ head -30 filename
```

**tail :** 显示从文件末尾开始的行(#display_data)

只是在上面的例子中用尾巴代替了头

**睡眠:**创建下一个命令(#终端进程)执行的延迟

```
//sleep for 5 seconds
$ sleep 5
```

## 运算符和控制命令

(~):主目录的简写

(.) :当前目录

（..) :父目录

(;) :命令分隔符

(#):注释(bash 将忽略以#开头的行)

(|):使用上一个命令的输出作为下一个命令的输入

```
$ ps aux | grep username
//here the output of ps aux will be parsed by grep to find keyword username
```

(>) :输入重定向

```
$ sort < words.txt
//here sort accepts word.txt as an input
```

(

```
$ ls > file.txt
//output of ls is stored in file.txt
```

($) : access value of bash variables

```
$ echo $HOME
/home/username
```

Ctrl + C : kill the current process running in the terminal

Ctrl + Z : suspend the current process running in the terminal

Ctrl + D : close the current bash shell, works similar to exit command

# Conclusion-:

Well the list is huge and I hope you have found some new learning from this blog. Feel free to comment if any command is skipped which you feel is important. Hope you enjoyed reading this blog.

Follow us on [medium](https://medium.com/@AnveeNaik) 获取更多此类内容。

*成为* [*介质会员*](https://medium.com/@AnveeNaik/membership) *解锁并阅读介质上的许多其他故事。*