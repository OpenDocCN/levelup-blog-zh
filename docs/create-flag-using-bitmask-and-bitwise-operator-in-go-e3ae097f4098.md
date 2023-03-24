# 在 Go 中使用位掩码和位运算符创建标志

> 原文：<https://levelup.gitconnected.com/create-flag-using-bitmask-and-bitwise-operator-in-go-e3ae097f4098>

![](img/382a2ae444dc1cd30ce94097bda08af5.png)

*照片由*[*@ swimtaralex*](https://unsplash.com/@swimstaralex)*在 Unsplash 上*

# 介绍

我重温了一些大学笔记，发现在大学第一年，在算法和编程课程中，提到了按位运算符，但没有突出显示。要么是我没有写那个特定话题的笔记(*我第二学期就不写笔记了*)要么是讲师没有解释任何用法。但后来我发现它对于位屏蔽非常有用。例如，Unix 的文件访问控制使用位掩码来表示访问:

```
000 => 0 => can't access anything
001 => 1 => can only execute
010 => 2 => can only read
011 => 3 => can execute+read
100 => 4 => can only write
101 => 5 => can execute+write
110 => 6 => can read+write
111 => 7 => can execute+read+write
```

所有这些组合可以仅用 3 位来构造。如您所见，位掩码使用多组位来表示是否给予了特定的访问权限。如果为 0，表示不给予特定的访问权限，否则给予。在本文中，您将学习如何在 Golang 中实现位屏蔽及其操作。

# 密码

```
package mainimport "fmt"type Permission uint8const (
 // 1
 PermissionExecute Permission = 1 << iota// 2
 PermissionRead// 4
 PermissionWrite
)const (
 PermissionAll = PermissionExecute | PermissionRead | PermissionWrite
)func Set(p, flag Permission) Permission {
 return p | flag
}func Clear(p, flag Permission) Permission {
 return p &^ flag
}func HasAll(p, flag Permission) bool {
 return p&flag == flag
}func HasOneOf(p, flag Permission) bool {
 return p&flag != 0
}func main() {
 var p Permission
    // you can use Set/Clear(p, PermissionExecute|PermissionRead|PermissionWrite) to set multiple bits
 p = Set(p, PermissionAll)
 p = Clear(p, PermissionRead)// false, because PermissionRead is cleared
 hasExecuteAndRead := HasAll(p, PermissionExecute|PermissionRead)// true, because PermissionExecute and PermissionWrite are set
 hasExecuteAndWrite := HasAll(p, PermissionExecute|PermissionWrite)// true, because PermissionExecute is set even though PermissionRead is cleared
 hasExecuteOrRead := HasOneOf(p, PermissionExecute|PermissionRead)fmt.Println(hasExecuteAndRead, hasExecuteAndWrite, hasExecuteOrRead)
}
```

让我们逐行进行解释。

# 一组

```
var p Permission // has no permission 000
p = Set(p, PermissionAll) // set all permission 111// 000 (current permission)
// 111 (PermissionAll)
// --- OR
// 111
```

# 清楚的

```
// clear the bit of PermissionRead (010)
p = Clear(p, PermissionRead)// reverse the PermissionRead first
// 010
// ---- NOT
// 101 (NOT result)// 111 (current permission)
// 101 (NOT result)
// --- AND
// 101
```

> 使用 BITCLEAR (AND NOT)运算符而不是 NOT(在本例中是 XOR)运算符是很重要的。BITCLEAR 总是将该位设置为 0，因为它首先使用 NOT 反转标志，然后使用 AND。同时，NOT 是反转位，所以如果你不操作两次，它会切换回来。例如，请参见下面的片段。

# 切换与清除

```
// toggling using NOT/XOR
foo := PermissionAll
fmt.Printf("Set All %03b\n", foo) // 111
foo ^= PermissionRead
fmt.Printf("Toggle %03b\n", foo) // 101
foo ^= PermissionRead
fmt.Printf("Toggle %03b\n", foo) // 111 it toggles back!// clearing using BITCLEAR
bar := PermissionAll
fmt.Printf("Set All %03b\n", bar) // 111
bar &^= PermissionRead
fmt.Printf("Clear %03b\n", bar) // 101
bar &^= PermissionRead
fmt.Printf("Clear %03b\n", bar) // 101
```

# 哈索尔

```
// check whether has both Execute AND Read permission
hasExecuteAndRead := HasAll(p, PermissionExecute|PermissionRead) // false// PermissionExecute|PermissionRead
// 001 (PermissionExecute)
// 010 (PermissionRead)
// --- OR
// 011 (PermissionRead+PermissionExecute)// check whether has both Execute AND Read permission
// 101 (current permission)
// 011 (PermissionRead+PermissionExecute)
// --- AND
// 001 (PermissionExecute)// 001 != 011 so it doesn't have both Execute AND Read permission// check whether has both Execute AND Write permission
hasExecuteAndWrite := HasAll(p, PermissionExecute|PermissionWrite)// PermissionExecute|PermissionWrite
// 001 (PermissionExecute)
// 100 (PermissionWrite)
// --- OR
// 101 (Write+Execute)// check whether has both Execute AND Write permission
// 101 (current permission)
// 101 (PermissionWrite+PermissionExecute)
// --- AND
// 101 (PermissionWrite+PermissionExecute)// 101 == 101 so it has both Execute AND Write permission
```

# HasOneOf

```
// check whether has either Execute OR Read permission
hasExecuteOrRead := HasOneOf(p, PermissionExecute|PermissionRead)// PermissionExecute|PermissionRead
// 001 (PermissionExecute)
// 010 (PermissionRead)
// --- OR
// 011 (PermissionRead+PermissionExecute)// check whether has either Execute OR Read permission
// 101 (current permission)
// 011 (PermissionRead+PermissionExecute)
// --- AND
// 001 (PermissionExecute)// 001 != 0 so it has either Execute OR Read permission
```

# 需要关注的事项

您也可以将位掩码存储在您的数据库中，但是在您的逻辑中使用`iota`可能不是一个好主意。根据上面的代码，假设您想要删除`PermissionRead`。那么`PermissionWrite`将由 2 而不是 4 来表示。为了避免这种情况，您可能希望为您的权限设置一个[弃用标志](https://cs.opensource.google/go/x/tools/+/master:go/packages/packages.go;l=96-117;drc=db8f89b397771c885c6218de3f383d800d72e62a)，而不是将其从代码中完全移除。或者，作为一种选择，你可以手动输入号码，但是如果你有很多权限，这又是一个麻烦。

# 结论

在这种情况下，位屏蔽是实现访问控制的有用工具。您只用 1 个字节实现了访问控制，因为您只需要使用`uint8`。例如，如果您使用布尔变量集来表示访问控制，您需要不止一个变量，并且需要不止一个字节来存储。

感谢您的阅读！

*原载于 2022 年 7 月 15 日*[*https://clavinjune . dev*](https://clavinjune.dev/en/blogs/create-flag-using-bitmask-and-bitwise-operator-in-go/)*。*