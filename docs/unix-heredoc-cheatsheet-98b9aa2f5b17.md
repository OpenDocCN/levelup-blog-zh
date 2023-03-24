# Unix Heredoc 备忘单

> 原文：<https://levelup.gitconnected.com/unix-heredoc-cheatsheet-98b9aa2f5b17>

![](img/855f1bf9fe9a27bc4f67350ce7d72bbd.png)

# 句法

```
[cmd] <<[-] delimeter [cmd]
    contents
delimeter
```

所有的`contents`都将作为输入传递给 cmd，下面的例子将使用`EOF`作为指示器，使用`cat`作为命令，你可以随意更改。

# 带变量

```
cat <<EOF
    echo "$HOME"
EOF
```

结果:

```
 echo "/home/clavinjune"
```

# 转义变量

用`\$`代替`$`对特定变量进行转义

```
cat <<EOF
    echo "$HOME"
    echo "\$HOME"
EOF
```

结果:

```
 echo "/home/clavinjune"
    echo "$HOME"
```

# 转义所有变量

用`'EOF'`代替`EOF`来转义所有变量

```
cat <<'EOF'
    echo "$HOME"
    echo "\$HOME"
EOF
```

结果:

```
 echo "$HOME"
    echo "\$HOME"
```

# 删除前导制表符

使用`<<-`而非`<<`移除前导标签

```
cat <<-EOF
    echo "$HOME"
    echo "\$HOME"
EOF
```

结果:

```
echo "/home/clavinjune"
echo "$HOME"
```

# 添加更多管道

```
cat <<EOF | grep june
    echo "$HOME"
    echo "\$HOME"
EOF
```

结果:

```
 echo "/home/clavinjune"
```

# 写入文件

```
cat <<-'EOF' > /tmp/foo
    echo "$HOME"
    echo "\$HOME"
EOF
```

结果:

```
$ cat /tmp/foo 
echo "$HOME"
echo "\$HOME"
```

感谢您的阅读！

*原载于 2022 年 1 月 13 日*[*https://clavinjune . dev*](https://clavinjune.dev/en/blogs/unix-heredoc-cheatsheet/)*。*