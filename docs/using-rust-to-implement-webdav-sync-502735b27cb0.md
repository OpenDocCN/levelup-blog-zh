# 使用 Rust 实现 WebDav 同步

> 原文：<https://levelup.gitconnected.com/using-rust-to-implement-webdav-sync-502735b27cb0>

![](img/d8eb8494c8d4be2e0560482f8b862136.png)

由[帕万·特里库塔姆](https://unsplash.com/@ptrikutam?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄的照片

在这里，在 Rust 中找到一个简单而有效的应用程序，它将目录与 WebDav 服务器同步。

## 为什么生锈？

去年我听说了 Rust，并对它的承诺很感兴趣。作为一名前 C++开发人员，我理解像 C++这样的编译语言给你带来的好处。性能、快速启动时间、与其他语言相比较小的二进制文件。

我也理解 C++的缺点。与其他语言相比，处理内存管理、运行边界检查器、硬崩溃、复杂性。当然，它有智能指针，它们当然有帮助，但是保持头文件与源文件同步总是不愉快的。微软世界里也有一些像 MFC、ATL 这样过于复杂的库。

当我第一次看到 Java 并开始用它编程时，我对它的简单性感到很满意。再也不用担心崩溃了(除非 JVM 真的耗尽了内存)。垃圾收集器可以在很大程度上保护你不搬起石头砸自己的脚。但是对于大型应用程序，您仍然需要担心 GC 周期何时运行，因为它可能会使您的应用程序停止。随着时间的推移，Java 的一些优雅已经被 Spring 这样的畸形库所掩盖。

像 Ruby(和 Ruby on Rails)和 Python 这样的语言可以让事情变得非常简单。有机会在 RoR 编程一年多之后，我对其他一些较新的语言产生了兴趣，比如 Rust。

我已经用 Rust 编写了一个简单的程序，它的性能给我留下了非常深刻的印象。我想尝试更多的功能。

## WebDav 和 Rust 通知

WebDav 是 20 世纪 90 年代对 HTTP 协议的扩展，可用于提供文件服务。添加的一些命令包括复制、重命名、移动和 MKCOL(像`mkdir`)。WebDav 有一个 Rust 客户端库。

Rust 还有一个名为 Notify 的模块，它是一个基本的文件系统监视器。

如果我可以将 Rust 文件系统监视器与 WebDav 协议结合起来，并在两者之间同步文件，会怎么样？

## Rust 库

幸运的是，Rust 似乎有一个很好的社区来提供支持。在他们的 API 文档、Rust " [book](https://doc.rust-lang.org/book/title-page.html) "、stackoverflow.com 以及一些幸运的谷歌搜索之间，你通常可以得到你需要的帮助。

Rust 的 WebDav 客户端叫做 [hyperdav](https://docs.rs/hyperdav/0.2.0/hyperdav/) 。文件系统观察器组件被称为[通知](https://docs.rs/notify/4.0.15/notify/)。

## WebDav 设置

对于 WebDav，有来自不同供应商的多种实现。我想要免费的东西。我在 T21 的 dockerhub 上找到了一个。下面是启动它的示例命令(需要 Docker)。这在内部使用 Apache HTTP 的 webdav 支持。

```
docker run --restart always -v /tmp/webdav:/var/lib/dav \
    -e AUTH_TYPE=Basic -e USERNAME=YOUR_USERNAME \
    -e PASSWORD=YOUR_PASSWORD \
    --publish 80:80 -d bytemark/webdav
```

对于上述内容，您最有可能改变的是:

*   提供文件的本地路径。我用了`/tmp/webdav`
*   用户名(您的用户名)
*   密码(您的 _ 密码)

如果您的计算机上运行的是另一个 HTTP 服务器，您可能还想更改端口 80。

启动后，请确保您可以从浏览器访问它，并且它会提示您输入凭据。也可以使用 CyberDuck 这样的客户端进行连接。您可以使用 Docker“Dashboard”来查看日志，并对连接到 WebDav 的任何错误进行故障排除。

# 编写应用程序

第一件事是确保您可以在文件更改时接收事件。为了简单起见，从处理`Create`和`Write`事件开始。注意`write_file`方法是调用 WebDav 的占位符。

分解一下，现在的情况是:

1.  创建观察器，并通过`watch`方法告诉它开始观察。
2.  在接收通道上监听`match`的任何输入事件。
3.  如果发生`Create`或`Write`事件，尝试`write_file`。如果失败，打印一个错误。
4.  其他事件将被忽略，并转到默认的无操作情况。

还可以观看其他事件，包括`Chmod`、`Remove`和`Rename`。

## 写入 WebDav

这是大部分核心逻辑所在。

这里的情况是:

1.  打开本地文件系统上的文件，我们将把它发送到 WebDav 并创建一个`BufReader`。
2.  构建一个`Vec<String>`来保存我们将要创建的文件的路径部分(`make_path_vec`)。
3.  构建一个包含文件完整路径(包括文件名)的`path_and_file_vec`
4.  用`mkdir` (MKCOL)创建包含目录，然后在`put_file`中调用`PUT`

## PUT 和 MKCOL 方法

这个`PUT`调用非常简单。注意，路径部分需要一个`Vec`。如果您试图传递类似于`this/is/my/file.txt`的字符串，`hyperdav`客户端将对路径进行 URL 编码，WebDav 服务器将无法创建它，因此请确保您将路径部分分割成一个`Vec`。

```
fn put_file(&self, path_and_file_vec: Vec<String>, reader: BufReader<File>) {
  match self.client.put(reader, path_and_file_vec) {
    Err(err) => {
      println!("problem writing file {}", err);
    }
    _ => (),
  }
}
```

`MKCOL`调用稍微健壮一点，因为如果路径为空，它可以防止不必要的调用。

```
fn mkdir(&self, path_vec: Vec<String>) {
  let pathstr = path_vec.join("/");
  if pathstr.len() == 0 {
    // nothing to do
    return;
  }
  match self.client.mkcol(&path_vec) {
    Err(err) => {
      println!("problem making directory '{}' {}", pathstr, err);
    }
    _ => (),
  }
}
```

## 主要入口点

主入口点确保用户通过使用`clap`模块(用于命令行参数)传入有效变量，但也可选地支持`stdin`。因为其中一个参数是密码，所以使用了另一个名为`rpassword`的模块，用于在用户输入密码时隐藏密码。这个逻辑由一个通用的`read_parameter`方法抽象。

```
fn read_parameter(name: &str, password: bool) -> String {
    let msg = format!("Enter {}: ", name);
    if password {
        rpassword::prompt_password_stdout(&msg).unwrap()
    } else {
        println!("{}", msg);
        let mut val = String::new();
        match io::stdin().read_line(&mut val) {
            _ => String::from(val.trim_end()),
        }
    }
}
```

其他想法是通过 Apple Keychain services 存储和收集凭证信息(这是在 Mac 上完成的)，但将它保存到下一天。

## 技术性能分析

可以通过检测代码来收集基本的性能指标。`std::time`模块有一个`Instant`结构，可用于收集持续时间以便计时。这显示了如何为`PUT`操作打印出 Kbps 度量的示例。

```
let md = fs::metadata(pb)?;
let size: u64 = md.len();
let now = Instant::now();// make the PUT call
self.put_file(path_and_file_vec, reader);let duration = now.elapsed();
let kbps = (size as u128 / duration.as_millis()) as f64 / 1000.0;
println!(
  "{:?} elapsed {} ms, {} Kbps, {} bytes",
  thread::current().id(),
  duration.as_millis(),
  kbps,
  size
);
```

# 关于铁锈的思考

入门肯定很难。一些难倒我的事情:

*   借支票。我发现自己一直在和借货员斗争。为了避免进一步的混淆，我尽可能地避免了有生之年。
*   弦乐。字符串处理不是最佳的。我希望他们能隐藏`String`和`str`的区别，或者让它们可以互换。
*   伊努斯。我花了一段时间才“明白”一个枚举可能包含一个可以在`matches`块中提取的值。
*   错误消息。虽然它们很冗长，但你必须真正理解 Rust 及其习惯用法，才能正确地解释它们。
*   成熟。似乎它还处于成熟周期的早期，以后的版本可能会使这些事情变得更容易。

往好的方面想，有铁锈:

*   写不好代码真的很难。
*   不用再处理头文件了。
*   最终产品的速度是惊人的。
*   外面有一堆“板条箱”可能已经做了你想做的事情。

我还没有尝试过 Rust 的任何并发功能(这是我的下一个列表)。Rust 有一种非常活跃和令人兴奋的新语言的一般感觉。

# 摘要

已经演示了一个简单但有效的应用程序，它展示了如何观察文件系统事件并将这些更改同步到 WebDav 服务器。

代码可以在 GitHub 上找到，网址是:

[https://github.com/tweissin/webdav-sync](https://github.com/tweissin/webdav-sync)