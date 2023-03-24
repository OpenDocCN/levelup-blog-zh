# iOS 系统视图控制器

> 原文：<https://levelup.gitconnected.com/ios-system-view-controller-f571a03defb>

# 摘要

系统视图控制器是预定义的 UIViewController 子类，有助于在应用程序中显示信息。UIKit 包括许多 UIViewController 子类，可以轻松呈现、访问和共享应用程序内容。

# 如何呈现一个活动控制器？

*   UIActivityViewController 使用户能够与其他已安装的应用程序共享内容

# 如何呈现一个 Safari 视图控制器？

*   SFSafariViewController 在不退出应用程序的情况下显示来自互联网的信息

# 如何在警报控制器中呈现和响应动作？

*   UIAlertController 提供了新的信息和选项，以及如何响应用户的操作。

# 如何访问和响应图像拾取器中的选择？

*   UIImagePickerController 访问设备的相机和照片库，并允许用户选择图像。
*   必须采用 UIImagePickerControllerDelegate 和 UINavigationController 委托
*   要从相机或照片库中访问图像，必须在 Info.plist 文件中包含 NSCameraUsageDescription。

# 如何呈现邮件撰写视图控制器？

*   MFMailComposeViewController 从应用程序内部发送电子邮件
*   导入表单 MessageUI 框架