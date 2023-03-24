# 使用 Ionic 和 React 开始移动开发

> 原文：<https://levelup.gitconnected.com/getting-started-with-mobile-development-with-ionic-and-react-badc98fef59d>

![](img/3be3bc858e83de3fe0e59d5767f3f75b.png)

照片由[尤拉鲜](https://unsplash.com/@mr_fresh?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在本文中，我们将看看如何使用带有 React 的 Ionic 框架开始移动开发。

# 入门指南

我们可以从安装一些东西开始。

首先，我们通过运行以下命令来全局安装 Ionic CLI:

```
npm install -g @ionic/cli native-run cordova-res
```

接下来，我们可以通过运行以下命令来创建 Ionic 应用程序项目:

```
ionic start ionic-app tabs --type=react --capacitor
```

`tabs`在应用程序中添加标签。

`type`设置为`react`意味着我们将创建一个 React 项目

`--capacitor`意味着我们添加了电容器，这样我们就可以从我们的项目文件中运行和构建一个本地应用程序。

然后我们运行:

```
npm install @ionic/react-hooks @ionic/pwa-elements
```

在`ionic-app`项目文件夹中为我们的项目安装 React 钩子。

然后，为了在浏览器中运行应用程序，我们运行:

```
ionic serve
```

# 使用 Genymotion 运行应用程序

要使用 Genymotion 运行我们的应用程序并构建一个原生应用程序，我们需要做更多的事情。

首先，我们运行:

```
ionic build
```

创建资产文件夹。

然后我们运行:

```
npx cap add android 
npx cap sync
```

添加 Android 依赖项。

然后需要安装 Android Studio 和 Genymotion。

在我们安装了 Android Studio 之后，我们安装了 Android Studio 的 Genymotion 插件。

一旦我们这样做了，我们运行:

```
ionic capacitor run android --livereload --external --address=0.0.0.0
```

在 Genymotion 中预览我们的应用程序。

现在，我们应该看到应用程序重新加载生活。

# 创建相机应用程序

我们可以使用 Ionic 轻松创建一个相机应用程序。

为此，我们转到`Tab1.tsx`，写下:

`pages/Tab1.tsx`

```
import React, { useEffect, useState } from 'react';
import { IonButton, IonCol, IonContent, IonGrid, IonHeader, IonImg, IonPage, IonRow, IonTitle, IonToolbar } from '@ionic/react';
import ExploreContainer from '../components/ExploreContainer';
import './Tab1.css';
import { useCamera } from '@ionic/react-hooks/camera';
import { CameraResultType, CameraSource } from "@capacitor/core";interface Photo {
  filepath: string;
  webviewPath?: string;
}function usePhotoGallery() {
  const { getPhoto } = useCamera();
  const [photos, setPhotos] = useState<Photo[]>([]); const takePhoto = async () => {
    const cameraPhoto = await getPhoto({
      resultType: CameraResultType.Uri,
      source: CameraSource.Camera,
      quality: 100
    }); const fileName = new Date().getTime() + '.jpeg';
    const newPhotos = [{
      filepath: fileName,
      webviewPath: cameraPhoto.webPath
    }, ...photos];
    setPhotos(newPhotos)
  }; return {
    photos,
    takePhoto
  };
}const Tab1: React.FC = () => {
  const { photos, takePhoto } = usePhotoGallery(); return (
    <IonPage>
      <IonHeader>
        <IonToolbar>
          <IonTitle>Tab 1</IonTitle>
        </IonToolbar>
      </IonHeader>
      <IonContent fullscreen>
        <IonGrid>
          <IonRow>
            <IonButton onClick={takePhoto}>take photo</IonButton>
          </IonRow>
          <IonRow>
            {photos.map((photo, index) => (
              <IonCol size="6" key={index}>
                <IonImg src={photo.webviewPath} />
              </IonCol>
            ))}
          </IonRow>
        </IonGrid>
      </IonContent>
    </IonPage>
  );
};export default Tab1;
```

这是整个相机应用程序的代码。

我们创建了`usePhotoGallery`钩子，它使用`useCamera`钩子调用`getPhoto`函数来创建`cameraPhoto`对象。

有了它，摄像机就会显示。

然后我们添加`newPhotos`数组来获取照片并放入`photos`数组。

`webviewPath`有照片的路径。

在`Tab1`组件中，我们添加了一个`IonButton`来显示拍照按钮。

我们将`onClick`道具设置为`takePhoto`功能，以显示相机并拍摄照片。

然后，一旦我们完成了拍照，我们就从`photos`数组中获取照片并显示它们。

# 结论

我们可以用 Ionic 轻松创建一个简单的应用程序。