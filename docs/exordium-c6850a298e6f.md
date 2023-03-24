# 核心位置—与地图工具包集成

> 原文：<https://levelup.gitconnected.com/exordium-c6850a298e6f>

![](img/91770f136cba586a691d89c294015610.png)

照片由[德尔菲·德拉鲁阿](https://unsplash.com/@delfidelarua7?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/map?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

# 绪论

在这篇文章中，我将带你经历集成核心位置和地图工具包的过程。我们将从这里开始的[系列的](https://rustynailsoftware.com/?utm_source=site&utm_medium=blog&utm_campaign=core_location_mapkit)开始。你可以在 GitHub 上找到本教程的代码。

核心位置是 Cocoa Touch 的一部分，是用于获取用户位置详细信息的框架。位置详细信息有多种不同的用途，如何使用这些信息取决于应用程序的功能。无论你是在构建一个送餐应用程序、音乐会预订系统，还是一个寻找最近浴室的工具，核心位置都将是你产品的关键组成部分。在这种情况下，用户位置将显示在承载地图的视图控制器的中间。

我们开始吧。

# 添加地图

先从上面提到的回购的`mapkit`分支说起。您将在`ViewController`中看到一个新按钮和一个名为`MapViewController.`的新视图控制器(我已经为您添加了这两个组件，与`geocoder`分支相比，您可以看到)。

在`MapViewController`中要做的第一件事是添加地图视图，这将是一个`MKMapView`对象。将该属性添加到`MapViewController`类中:

```
private var mapView: MKMapView!
```

接下来要做的事情是创建`MapViewController.`的布局`ViewController`类连接到`Main.storyboard`文件，但是我将通过编程来构建`MapViewController`。当用代码构建视图控制器接口时，我喜欢将所有的 UI 工作放在一个`setupView`方法中，该方法最终调用另一个应用所有自动布局约束的方法。

下面是`MapViewController`的简单设置:

```
import UIKit
import CoreLocation
import MapKit

class MapViewController: UIViewController {

    // MARK: - Properties
    private var mapView: MKMapView!

    // MARK: - Methods

    // 1\. Complete any user interface work.
    func setupView() {
        mapView = MKMapView()
        mapView.translatesAutoresizingMaskIntoConstraints = false
        view.addSubview(mapView)

        applyAutoConstraints()
    }

    // 2\. Complete any Auto Layout work.
    func applyAutoConstraints() {
        NSLayoutConstraint.activate([
            mapView.topAnchor.constraint(equalTo: view.topAnchor),
            mapView.leadingAnchor.constraint(equalTo: view.leadingAnchor),
            mapView.trailingAnchor.constraint(equalTo: view.trailingAnchor),
            mapView.bottomAnchor.constraint(equalTo: view.bottomAnchor)
        ])
    }

    // MARK: - Overrides
    override func viewDidLoad() {
        super.viewDidLoad()

        // 3\. Call the method that sets up the user interface.
        setupView()
    }
}
```

这里我导入了`Core Location`和`MapKit`。导入框架后要做的第一件事是设置用户界面。

1.)方法`setupView`将保存任何涉及设置组件及其属性的 UI 工作。在这种情况下，我们实例化了`MKMapView`对象，并将其`translatesAutoresizingMaskIntoConstraints`设置为`false`。将该属性设置为`false`会让 UI 组件受到自动布局的影响。[如 Apple 的文档](https://developer.apple.com/documentation/uikit/uiview/1622572-translatesautoresizingmaskintoco) ' ***中所述，如果您想使用自动布局来动态计算视图的大小和位置，您必须将该属性设置为 false，然后为视图*** 提供一组明确、无冲突的约束。只有从`UIView`继承的对象才能访问`translatesAutoresizingMaskIntoConstraints`属性。对`mapView`对象做的最后一件事是将其作为子视图添加到视图中。

2.)任何需要为 UI 组件完成的自动布局工作都将在`applyAutoConstraints`方法中完成。这里，`mapView`将填充整个屏幕，因为锚都等于视图的相应锚。

3.)要让这些代码做任何事情，调用`viewDidLoad`方法中的`setupView`方法。

UI 设置要做的最后一点是在`MapViewController`的导航栏上添加一个标题和取消按钮。在调用`setupView`之前，将以下内容添加到`viewDidLoad`方法的开头。

```
title = "View Your Location"
navigationItem.leftBarButtonItem = UIBarButtonItem(barButtonSystemItem: .cancel, target: self, action: #selector(dismissToMainView))
```

`dismissToMainView`选择器是位于`MapViewController`中的物镜 C 选择器。它只是关闭了当前的视图控制器。

```
@objc func dismissToMainView() {
    dismiss(animated: true, completion: nil)
}
```

# 传递用户的位置

为了让用户的位置显示在地图上，它必须从`ViewController`传递到`MapViewController`。现在，`MapViewController`不包含我们可以存储位置的属性——所以添加一个默认访问级别的属性。

```
var userLocation: CLLocation!
```

然后，在`ViewController`中，类定位到名为`viewLocationOnMapButtonPressed`的`IBAction`并且:

1.)创建一个`MapViewController`的实例。
2。)将当前位置传递给`mapViewController`的`userLocation`属性。
3。)将`mapViewController`嵌入`UINavigationController`中，并展示`navigationController`。

```
@IBAction func viewLocationOnMapButtonPressed(_ sender: Any) {
    // 1
    let mapViewController = MapViewController()
    // 2
    mapViewController.userLocation = currentLocation
    // 3
    let navigationController = UINavigationController(rootViewController: mapViewController)
    present(navigationController, animated: true, completion: nil)
}
```

# 在地图上显示用户的位置

地图上显示位置之前:
1。)地图的`showsUserLocation`属性必须设置为`true`。
2。)代理将被设置为`self`(确保`MapViewController`与`MKMapViewDelegate`一致)。
3。)区域将基于从`ViewController`传入的用户位置。地图的区域是当前显示在屏幕上的地图的一部分。它的类型是`MKCoordinateRegion`，将用包含`center`和`span`参数的初始化器创建。`center`将是用户的坐标，`span`是类型`MKCoordinateSpan`。该属性定义区域的高度和宽度。较低的增量值将增加地图的缩放级别。

`setUpView`方法现在应该是这样的:

```
func setupView() {
    mapView = MKMapView()
    mapView.translatesAutoresizingMaskIntoConstraints = false

    // 1
    mapView.showsUserLocation = true
    // 2
    mapView.delegate = self
    // 3
    mapView.region = MKCoordinateRegion(center: userLocation.coordinate, span: MKCoordinateSpan(latitudeDelta: 0.1, longitudeDelta: 0.1))

    view.addSubview(mapView)   
    applyAutoConstraints()
}
```

最后要做的是在`MKMapViewDelegate`中实现`regionDidChangeAnimated`方法，并在区域更新后设置地图的区域。这不是必须的，但是当用户用手指滚动离开他们的位置时，这有助于重新将地图居中。

```
extension MapViewController: MKMapViewDelegate {
    func mapView(_ mapView: MKMapView, regionDidChangeAnimated animated: Bool) {
        mapView.setRegion(MKCoordinateRegion(center: userLocation.coordinate, span: MKCoordinateSpan(latitudeDelta: 0.1, longitudeDelta: 0.1)), animated: true)
    }
}
```

如果应用程序正在运行，位置将被获取，点击“查看地图上的位置”按钮——将显示`MapViewController`,您将在地图中央看到您的位置。使用 MapKit 还可以做更多的事情，这篇博文只是触及了 MapKit 是什么以及如何使用它的基础知识。

核心位置与地图工具包完美集成，是任何基于位置的 iOS 应用程序的关键组件。核心定位还使开发人员能够对 GPS 坐标进行反向地理编码，并创建人类可读的地址。在我们的博客上了解更多关于使用核心位置[进行反向地理编码的信息。](https://rustynailsoftware.com/dev-blog/core-location-reverse-geocoding-locations-using-clgeocoder?utm_source=site&utm_medium=blog&utm_campaign=core_location_mapkit)

你可以在 rustynailsoftware.com 的[阅读更多与 iOS 开发、自由职业、软件工程等相关的文章。欢迎随时在](https://rustynailsoftware.com/dev-blog/)[推特](https://twitter.com/andrewlundydev)上联系。或者，[联系](https://rustynailsoftware.com/andrew-lundy#contact)如果你正在为你的下一个项目寻找一个可靠的 iOS 开发者。