# 如何像 Airbnb 一样在 Android 上显示谷歌地图中的地点

> 原文：<https://levelup.gitconnected.com/how-to-display-places-in-map-like-airbnb-4cd7fde2279c>

在本教程中，我们将创建一个 API 和移动应用程序来在谷歌地图上显示大学。

![](img/0d6e35c250c9be1ee3d3778be191573b.png)

照片由 [henry perks](https://unsplash.com/@hjkp?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄(不是我们将在本教程中构建的应用程序的视觉效果)

# 我们将做什么

在本教程中，我们将学习开发一个后端 API 和 Android 应用程序，在地图上显示印度的大学(使用 Google Maps SDK for Android)，就像 AirBnB 在世界地图上显示酒店/住宅的位置一样。

如果你以前没有使用过 Airbnb，看看下面的视频就知道了。您也可以**通过点击链接[这里](https://play.google.com/store/apps/details?id=com.webtutsplus.collegefinder)从我们将在本教程系列中创建的 play store 下载应用**

大学搜索器应用程序演示

我们将学习如何使用经纬度坐标在地图上放置标记。

在这个系列中，每个部分都被划分为任务，每个任务又被划分为步骤。这将有助于您了解我们在做什么，以及我们在每一步是如何做的。

# 数据库设计

我们从各种网站上收集了一些大学的信息。我们可以使用网络搜集来获取这些信息。请参考本教程了解更多关于网页抓取。

[](https://medium.com/swlh/lets-parse-the-web-a7f5300b244f) [## 让我们来解析网络

### 介绍

medium.com](https://medium.com/swlh/lets-parse-the-web-a7f5300b244f) 

# 表格设计

但是我们需要学院的`lat`和`long`，为此我们将使用谷歌地理定位 API。

我们将使用谷歌的地理编码 API

[https://maps.googleapis.com/maps/api/geocode/json?address = { address }&KEY =](https://maps.googleapis.com/maps/api/geocode/json?address=Indian+School+of+Business+Management+Administration+(ISBM),+bangalore&key=AIzaSyBx6Z-A8Ne9pMc57p3LcVnWuoE5yRVz5Og){ API _ KEY }

如果我们将地址作为`college name + city` 传递，我们将得到这个响应。从那里我们将提取`lat`、`long`并更新数据库。

![](img/76635a6fcaf7fdaf3f098b860f6f4cd6.png)

# 最终 API

现在，我们的 API 已经准备好了，我们将开始构建 Android 部分。

# 先决条件

本教程假设你熟悉 Android 开发和面向对象的编程概念。更具体地说，您应该知道如何使用 XML 创建基本的 ui，以及如何使用 Android 对 API 执行网络调用。还有，你要知道什么是 JSON 格式，什么是 API。

# 必须安装 Android Studio 4.0 及以上版本。

# 任务 1 —在 ANDROID STUDIO 中创建一个谷歌地图项目

**第一步:安装 Google Play 服务 SDK。**
打开此[链接](https://developer.android.com/studio/intro/update.html#sdk-manager)了解更多详细信息。要安装 SDK

1.  转到`Tools` → `SDK Manager.`
2.  切换到名为`SDK Tools`的选项卡
3.  勾选`Google Play Services`并点击应用。下载并安装 Google Play 服务 SDK 后，单击 OK。

![](img/a8b442ce91984911ad3c7b0fa0a7c68a.png)

**步骤 2:在 Android Studio 中创建一个谷歌地图项目**
按照以下步骤创建一个新的应用程序项目，其中包括一个地图活动:

1.  启动 Android Studio。
2.  创建一个新项目如下:
    *如果你看到`Welcome to Android Studio`对话框，选择`Start a new Android Studio project`，在对话框右边的“快速启动”下。
    *否则，点击 Android Studio 菜单栏中的`File`，然后点击`New`、`**New Project.**`
3.  在`Choose your project`对话框中，选择标签`Phone and Tablet.`
4.  选择`Google Maps Activity`，然后点击`Next`。
5.  输入您的应用名称、包名称和项目位置，编程语言为`Java`，以及您的应用支持的最低 Android API 级别，然后单击`Finish`。
6.  Android Studio 启动 Gradle 并构建您的项目。这可能需要几秒钟的时间。
7.  构建完成后，Android Studio 在编辑器中打开`google_maps_api.xml`和`MapsActivity.java`文件。下一节将更详细地描述如何获取 API 密钥。

**第三步:设置谷歌地图 API 密钥**

1.  在您的项目上启用[计费](https://console.cloud.google.com/project/_/billing/enable)。有关详细信息，请参见[创建计费账户](https://developers.google.com/maps/gmp-get-started#create-billing-account)。
2.  在[云控制台](https://console.cloud.google.com/apis/library/maps-android-backend.googleapis.com)中，在您的项目上启用 **Maps SDK for Android** 。详情请参见[启用 API](https://developers.google.com/maps/gmp-get-started#enable-api-sdk)。
3.  遵循[获取 API 密钥](https://developers.google.com/maps/documentation/android-sdk/get-api-key)指南，以**获取并添加**API 密钥。对于本教程，没有必要限制 API 键。我们也**不需要我们应用程序的 SHA-1 指纹**信息
4.  记住将您的密钥添加到`local.properties`文件中，这样当您将代码上传到远程 git 存储库时，您不会最终也上传您的密钥。

![](img/7e548b1e9d593d9f894928ad53d86b92.png)

**第四步:检查模板提供的代码**

检查模板提供的代码。特别是，看看您的 Android Studio 项目中的以下文件。

1.  XML 布局文件— `activity_maps.xml`

*   默认情况下，定义应用程序布局的 XML 文件位于`res/layout/activity_maps.xml`。它包含以下代码:

```
<fragment xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:id="@+id/map"
    tools:context=".MapsActivity"
    android:name="com.google.android.gms.maps.SupportMapFragment" />
```

2.地图活动文件— `MapsActivity.java`

*   默认情况下，定义地图活动的 Java 文件被命名为`MapsActivity.java`。它应该在您的软件包名称后包含以下代码:

```
// Copyright 2020 Google LLC
//
// Licensed under the Apache License, Version 2.0 (the "License");
// you may not use this file except in compliance with the License.
// You may obtain a copy of the License at
//
//      http://www.apache.org/licenses/LICENSE-2.0
//
// Unless required by applicable law or agreed to in writing, software
// distributed under the License is distributed on an "AS IS" BASIS,
// WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
// See the License for the specific language governing permissions and
// limitations under the License.

package com.google.maps.example;

import androidx.appcompat.app.AppCompatActivity;

import android.os.Bundle;

import com.google.android.gms.maps.CameraUpdateFactory;
import com.google.android.gms.maps.GoogleMap;
import com.google.android.gms.maps.OnMapReadyCallback;
import com.google.android.gms.maps.SupportMapFragment;
import com.google.android.gms.maps.model.LatLng;
import com.google.android.gms.maps.model.MarkerOptions;

public class MapsActivity extends AppCompatActivity implements OnMapReadyCallback {

    private GoogleMap mMap;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_maps);
        // Obtain the SupportMapFragment and get notified when the map is ready to be used.
        SupportMapFragment mapFragment = (SupportMapFragment) getSupportFragmentManager()
                .findFragmentById(R.id.map);
        mapFragment.getMapAsync(this);
    }

    /**
     * Manipulates the map once available.
     * This callback is triggered when the map is ready to be used.
     * This is where we can add markers or lines, add listeners or move the camera. In this case,
     * we just add a marker near Sydney, Australia.
     *
     * If Google Play services is not installed on the device, the user will be prompted to install
     * it inside the SupportMapFragment. This method will only be triggered once the user has
     * installed Google Play services and returned to the app.
     */
    @Override
    public void onMapReady(GoogleMap googleMap) {
        mMap = googleMap;

        // Add a marker in Sydney and move the camera
        LatLng sydney = new LatLng(-34, 151);
        mMap.addMarker(new MarkerOptions()
                .position(sydney)
                .title("Marker in Sydney"));
        mMap.moveCamera(CameraUpdateFactory.newLatLng(sydney));
    }
}
```

*   如果您的地图活动文件不包含上述代码，请在您的包名称后用上述代码替换文件的内容。

在这个阶段，建议您检查 Google Maps SDK 是否与应用程序正确集成。要检查谷歌地图 API for Android 是否正常工作，请在你觉得合适的物理设备或模拟器上运行该应用程序。打开应用程序时，您应该会看到以悉尼为中心的地图，如下图所示。

![](img/c17b36efe9e4611909dd7f9fae96474c.png)

如果看不到地图，那就是有问题。检查 Android Studio 中的`LogCat`以查看错误。如果您无法解决该错误，您可以创建一个新项目并再次遵循上述步骤。

# 任务 2 —使用 Android 版谷歌地图 SDK 在地图上放置标记

让我们学习如何在地图上放置标记。

**第一步:创建一个** `**LatLng**` **对象**

*   通过将纬度和经度传递给类的构造函数，可以创建一个`LatLng`对象。`LatLng`Google Maps SDK 使用对象来识别地点的坐标。
*   使用以下代码在`MapsActivity.java` 类的`onMapReady()`方法中创建`LatLng`对象

`LatLng india= new LatLng(20.5937, 78.9629);`

*   在这一步之后，MapsActivity.java 的`onMapReady()`方法如下所示

**第二步:使用 Android 版谷歌地图 SDK 添加标记**

*   现在我们将使用`GoogleMap`类的`addMarker()`方法在谷歌地图中添加标记。以下代码说明了:-

```
**mMap**.addMarker(**new** MarkerOptions().position(sydney).title(**"Marker in Sydney"**));
```

*   `title(String)`方法设置用户点击标记时显示的字符串。
*   在这个步骤之后，`MapsActivity.java`的`onMapReady()`方法如下所示

**第三步:运行 app**

*   现在让我们运行应用程序。运行该应用程序时，您会看到地图的中心位于非洲上空(纬度:0，经度:0)。如果你把地图移到印度，你会看到一个标记。下图说明了:-

![](img/58ea4ce04ff6282d014f0ecc91bcef5a.png)

默认情况下，地图以非洲为中心

![](img/9483db16de99f02c4d348c6c95dee953.png)

放置在印度上方的标记

**第四步:更改谷歌地图的默认位置**

*   如你所知，当我们运行应用程序时，默认情况下，地图以非洲为中心。这是因为谷歌地图居中的默认位置被设置为纬度:0 &纬度 0。
*   要改变默认位置，我们可以使用`GoogleMap`类的`moveCamera()`方法。我们将在`MapsActivity.java`的`onMapsReady()`方法中使用以下代码。`india`是我们之前创建的`LatLng`对象。它包含了印度的坐标。

```
**mMap**.moveCamera(CameraUpdateFactory.*newLatLng*(india));
```

*   在这个步骤之后，`MapsActivity.java`的`onMapReady()`方法看起来如下

*   现在运行应用程序。默认情况下，你会看到地图以印度为中心

![](img/9483db16de99f02c4d348c6c95dee953.png)

地图的默认视图

# 恭喜

祝贺您完成了本系列的第一部分。你做得很好！！！在下一部分，我们将学习如何像 AirBnB 一样在地图的当前视图上放置标记。

[](https://medium.com/webtutsplus/lets-make-an-android-app-to-search-colleges-like-airbnb-b63834a0d4a8) [## 让我们制作一个 Android 应用程序，像 Airbnb 一样搜索大学

### 在之前的教程中，我们在 android 中的地图上显示了学院。今天，我们将实现一个搜索功能…

medium.com](https://medium.com/webtutsplus/lets-make-an-android-app-to-search-colleges-like-airbnb-b63834a0d4a8)