# 如何修复 Android Studio 模拟器问题:“AVD 的模拟器进程被杀死”

> 原文：<https://levelup.gitconnected.com/how-to-fix-android-studio-emulator-issue-the-emulator-process-for-avd-was-killed-db74cb02a359>

## 一个 macOS Big Sur 11.3 和 Android Studio 4.1.3 的 bug

![](img/4abab342e5978ee67d327488b9687070.png)

每个使用 Android Studio 和 Android emulator 的 Android/Flutter 开发人员都知道常见的错误消息是多么令人沮丧:

> AVD 的模拟器进程被终止。

你可以在网上找到关于这个问题的不同解决方案，因为它可以由许多事情引起…但是，这次可能会不同。

随着新的 macOS Big Sur 11.3 更新，Android Studio 中的模拟器不再运行。出现此错误是因为 Apple 对虚拟机管理程序权限进行了更改。

> 授权是授予可执行程序使用服务或技术的权限的键值对。

在这种情况下，`/Users/<username>/Library/Android/sdk/emulator/qemu/darwin-x86_64/qemu-system-x86_64`缺少创建和运行虚拟机的实体元素。

更具体地说，`com.apple.vm.hypervisor`权限(在 macOS 10.15 中使用)已被弃用，并被`com.apple.security.hypervisor`所取代。

要解决这个问题，我们所要做的就是将授权添加到`qemu-system-x86_64`二进制文件中。

> 编辑:看起来模拟器更新 30.5.6 已经解决了这个问题。

# 解决问题的步骤:

1.  打开`Terminal`，进入目录`/Users/<username>/Library/Android/sdk/emulator/qemu/darwin-x86_64/`
2.  用`touch`或`cat`命令创建一个名为`entitlements.xml`的 xml 文件
3.  将此内容添加到`entitlements.xml`文件:

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>com.apple.security.hypervisor</key>
    <true/>
</dict>
</plist>
```

4.然后简单地在`qemu-system-x86_64`上签名:

```
codesign -s - --entitlements entitlements.xml --force qemu-system-x86_64
```

现在只需重新启动 Android Studio，Android 模拟器就可以再次工作了！

感谢阅读！我希望你喜欢这篇文章。如果你愿意，可以查看我的[推特简介](https://twitter.com/andrea_culot):)

*资源:* [*如何在 macOS 11.0 Big Sur 上运行 QEMU*](https://www.arthurkoziel.com/qemu-on-macos-big-sur/)