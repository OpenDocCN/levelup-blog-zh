# 如何使用 PHPickerViewController 和 SwiftUI 选择图像

> 原文：<https://levelup.gitconnected.com/how-to-select-images-using-phpickerviewcontroller-with-swiftui-da8bd3ec3d05>

在更改个人资料图片、发布更新或分享宠物照片时，需要从我们的 iPhone 图库中选择图像。在本帖中，我们将探讨如何在 SwiftUI 中使用`PHPickerViewController`。苹果在 WWDC2020 上公布了这款视图控制器。

![](img/4523887085c6cfa133f12fa5cccf4d85.png)

[丹金](https://unsplash.com/@danielcgold)在 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral) 上拍照。

# 什么是 PHPickerViewController？

`PHPickerViewController`是一个视图控制器，允许我们的应用程序用户从他们的照片库中选择资源。它提供了一个众所周知的用户界面，我们不需要费心去构建它。

这种方法带来的一个好处是，我们不需要担心添加信息来访问用户在`Info.plist`文件中的照片库。用户可以决定允许访问所有照片库或者选择性地授权访问特定照片。这解决了隐私问题，应用程序可以读取整个图书馆，甚至跟踪人们。

# 创建一个`PHPickerViewController`

为了创建一个`PHPickerViewController`，我们需要通过传递配置来初始化它，这是`PHPickerConfiguration`的一个实例。对于配置，我们需要指定我们想要什么类型的图片，并设置选择限制。

对于`PHPickerViewController`本身，我们设置需要实现`PHPickerViewControllerDelegate`协议的委托。它只有一个方法，当我们的用户选择照片时，它会给出一个信号。

# 与 SwiftUI 集成

为了在 SwiftUI 应用中使用`PHPickerViewController`，我们需要使用`UIViewControllerRepresentable`来表示一个 UIKit 视图控制器。让我们回顾一下做这件事所需的所有步骤。

# 设置 UIKit 视图控制器

`UIViewControllerRepresentable`是一个协议，需要实现两个方法:

*   `makeUIViewController` -创建并配置视图控制器；
*   `updateUIViewControoler` -更新视图控制器的状态。

我们将在`makeUIViewController`方法中创建并配置`PHPickerViewController`。我们不需要更新它，所以我们可以让`updateUIViewControoler`为空。

```
struct PhotoPicker: UIViewControllerRepresentable {
  @Binding var pickerResult: [UIImage] // pass images back to the SwiftUI view
  @Binding var isPresented: Bool // close the modal view

  func makeUIViewController(context: Context) -> some UIViewController {
    var configuration = PHPickerConfiguration(photoLibrary: PHPhotoLibrary.shared())
    configuration.filter = .images // filter only to images
    configuration.selectionLimit = 0 // ignore limit

    let photoPickerViewController = PHPickerViewController(configuration: configuration)
    photoPickerViewController.delegate = context.coordinator // Use Coordinator for delegation
    return photoPickerViewController
  }

  func updateUIViewController(_ uiViewController: UIViewControllerType, context: Context) { }
}
```

# 协调者

现在我们可以呈现`PHPickerViewController`了，但是我们如何将选中的图像传输回来呢？我们需要使用`makeCoordinator`方法并创建实现`PHPickerViewControllerDelegate`协议的`Coordinator`类。对于 SwiftUI 如何与 UIKit 委托模式思想进行通信，这是一种经过深思熟虑的方法。

```
struct PhotoPicker: UIViewControllerRepresentable { // ... func makeCoordinator() -> Coordinator {
    Coordinator(self)
  }

  // Create the Coordinator, in this case it is a way to communicate with the PHPickerViewController
  class Coordinator: PHPickerViewControllerDelegate {
    private let parent: PhotoPicker

    init(_ parent: PhotoPicker) {
      self.parent = parent
    }

    func picker(_ picker: PHPickerViewController, didFinishPicking results: [PHPickerResult]) {
    }
  }
}
```

# 将 UIKit 与 SwiftUI 一起使用

让我们把它们放在一起。我们需要创建一个 SwiftUI 视图来呈现照片选择器视图，并将其呈现为一个模态。你可以在我的[上一篇文章](https://kristaps.me/blog/swiftui-modal-view/)中读到更多关于如何在 SwiftUI 中显示模态视图的信息。

```
.sheet(isPresented: $photoPickerIsPresented) {
  // Present the photo picker view modally
  PhotoPicker(pickerResult: $pickerResult,
              isPresented: $photoPickerIsPresented)
}
```

# 将数据从 UIKit 传递到 SwiftUI

要获取用户所选内容的照片，让我们使用`@State`变量，并使用`@Binding` [属性包装器](https://swiftuipropertywrappers.com/#binding)将其传递给`PhotoPicker`视图。

现在我们可以为我们的`Coordinator`类完全完成`PHPickerViewControllerDelegate`协议了。

```
func picker(_ picker: PHPickerViewController, didFinishPicking results: [PHPickerResult]) {
  parent.pickerResult.removeAll() // remove previous pictures from the main view

  // unpack the selected items
  for image in results {
    if image.itemProvider.canLoadObject(ofClass: UIImage.self) {
      image.itemProvider.loadObject(ofClass: UIImage.self) { [weak self] newImage, error in
        if let error = error {
          print("Can't load image \(error.localizedDescription)")
        } else if let image = newImage as? UIImage {
          // Add new image and pass it back to the main view
          self?.parent.pickerResult.append(image)
        }
      }
    } else {
      print("Can't load asset")
    }
  }

  // close the modal view
  parent.isPresented = false
}
```

我们在这里做的是从照片库中解包选定的项目，并将它们设置到父项的`@Binding`变量。通过这样做，我们将数据传输回主视图。

# 展示选定的图像

为了呈现选中的照片，我们可以迭代并在一个`ScrollView`中显示它们。

```
ScrollView {
  ForEach(pickerResult, id: \.self) { uiImage in
    ImageView(uiImage: uiImage)
  }
}
```

一个重要的事实是图像的类型是`UIImage`，但是幸运的是 SwiftUI 为传递`UIImage`类型的`Image`视图提供了一个很好的初始化器。

你可以点击查看完整的实现[。](https://github.com/fassko/PHPickerViewController-SwiftUI)

# TL；速度三角形定位法(dead reckoning)

选择图像并在我们的应用程序中使用它们是现代 iOS 应用程序的一个基本功能。苹果在 WWDC2020 上宣布了一种更安全、更精细的新方法——`PHPickerViewController`。注意，只有 iOS14 及更高版本才有。

要将`PHPickerViewController`与 SwiftUI 一起使用，我们需要实现`UIViewControllerRepresentable`协议。它允许与 UIKit view 控制器进行完美的通信。

# 链接

*   [样本代码](https://github.com/fassko/PHPickerViewController-SwiftUI)
*   [PHPickerViewController 文档](https://developer.apple.com/documentation/photokit/phpickerviewcontroller)
*   [iOS Privacy:detect . location——无需实际访问即可轻松访问用户的 iOS 位置数据](https://krausefx.com/blog/ios-privacy-detectlocation-an-easy-way-to-access-the-users-ios-location-data-without-actually-having-access)
*   [采用新的 PHPicker](https://www.kairadiagne.com/2020/11/04/adopting-the-new-photo-picker.html)
*   [在 iOS 14 中检查新的 PHPickerViewController](https://nemecek.be/blog/30/checking-out-the-new-phpickerviewcontroller-in-ios-14-to-select-photos-or-videos)
*   [用 PHPickerViewController 替换 UIImagePickerController】](https://ohmyswift.com/blog/2020/08/29/replacing-uiimagepickercontroller-with-phpickerviewcontroller/)
*   [以内存高效的方式使用 PHPickerViewController 图像](https://christianselig.com/2020/09/phpickerviewcontroller-efficiently/)