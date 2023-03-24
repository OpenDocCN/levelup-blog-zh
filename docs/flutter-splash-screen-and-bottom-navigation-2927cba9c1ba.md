# 颤动闪屏和底部导航

> 原文：<https://levelup.gitconnected.com/flutter-splash-screen-and-bottom-navigation-2927cba9c1ba>

![](img/178ae1289ed0a121e0628a9936fe2e8c.png)

1.  在主项目文件中创建一个名为“assets”的目录，并向其中添加另一个名为“images”的目录。
2.  将您想要在闪屏上显示的图像添加到图像目录中。(姑且称之为 picture.png)
3.  导航到测试目录中的 widget_test.dart 文件，并从第 16 行的构造函数调用中删除“const”。

widget_test.dart:

```
// This is a basic Flutter widget test.
//
// To perform an interaction with a widget in your test, use the WidgetTester
// utility that Flutter provides. For example, you can send tap and scroll
// gestures. You can also use WidgetTester to find child widgets in the widget
// tree, read text, and verify that the values of widget properties are correct.import 'package:flutter/material.dart';
import 'package:flutter_test/flutter_test.dart';import 'package:temporary/main.dart';void main() {
  testWidgets('Counter increments smoke test', (WidgetTester tester) async {
    // Build our app and trigger a frame.
    await tester.pumpWidget(MyApp()); // Verify that our counter starts at 0.
    expect(find.text('0'), findsOneWidget);
    expect(find.text('1'), findsNothing); // Tap the '+' icon and trigger a frame.
    await tester.tap(find.byIcon(Icons.add));
    await tester.pump(); // Verify that our counter has incremented.
    expect(find.text('0'), findsNothing);
    expect(find.text('1'), findsOneWidget);
  });
}
```

4.导航到 pubspec.yaml 文件，并将以下内容添加到依赖项部分:

```
url_launcher:
```

另外，在颤振部分增加以下内容:

```
assets:
    - assets/images/logo.png
```

pubspec.yaml

```
name: temporary
description: A new Flutter project.# The following line prevents the package from being accidentally published to
# pub.dev using `flutter pub publish`. This is preferred for private packages.
publish_to: 'none' # Remove this line if you wish to publish to pub.dev# The following defines the version and build number for your application.
# A version number is three numbers separated by dots, like 1.2.43
# followed by an optional build number separated by a +.
# Both the version and the builder number may be overridden in flutter
# build by specifying --build-name and --build-number, respectively.
# In Android, build-name is used as versionName while build-number used as versionCode.
# Read more about Android versioning at [https://developer.android.com/studio/publish/versioning](https://developer.android.com/studio/publish/versioning)
# In iOS, build-name is used as CFBundleShortVersionString while build-number used as CFBundleVersion.
# Read more about iOS versioning at
# [https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CoreFoundationKeys.html](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CoreFoundationKeys.html)
version: 1.0.0+1environment:
  sdk: ">=2.15.1 <3.0.0"# Dependencies specify other packages that your package needs in order to work.
# To automatically upgrade your package dependencies to the latest versions
# consider running `flutter pub upgrade --major-versions`. Alternatively,
# dependencies can be manually updated by changing the version numbers below to
# the latest version available on pub.dev. To see which dependencies have newer
# versions available, run `flutter pub outdated`.
dependencies:
  flutter:
    sdk: flutter
  url_launcher:
 # The following adds the Cupertino Icons font to your application.
  # Use with the CupertinoIcons class for iOS style icons.
  cupertino_icons: ^1.0.2dev_dependencies:
  flutter_test:
    sdk: flutter # The "flutter_lints" package below contains a set of recommended lints to
  # encourage good coding practices. The lint set provided by the package is
  # activated in the `analysis_options.yaml` file located at the root of your
  # package. See that file for information about deactivating specific lint
  # rules and activating additional ones.
  flutter_lints: ^1.0.0# For information on the generic Dart part of this file, see the
# following page: [https://dart.dev/tools/pub/pubspec](https://dart.dev/tools/pub/pubspec)# The following section is specific to Flutter.
flutter: # The following line ensures that the Material Icons font is
  # included with your application, so that you can use the icons in
  # the material Icons class.
  uses-material-design: true # To add assets to your application, add an assets section, like this:
  # assets:
  #   - images/a_dot_burr.jpeg
  #   - images/a_dot_ham.jpeg
  assets:
    - assets/images/picture.png # An image asset can refer to one or more resolution-specific "variants", see
  # [https://flutter.dev/assets-and-images/#resolution-aware.](https://flutter.dev/assets-and-images/#resolution-aware.) # For details regarding adding assets from package dependencies, see
  # [https://flutter.dev/assets-and-images/#from-packages](https://flutter.dev/assets-and-images/#from-packages) # To add custom fonts to your application, add a fonts section here,
  # in this "flutter" section. Each entry in this list should have a
  # "family" key with the font family name, and a "fonts" key with a
  # list giving the asset and other descriptors for the font. For
  # example:
  # fonts:
  #   - family: Schyler
  #     fonts:
  #       - asset: fonts/Schyler-Regular.ttf
  #       - asset: fonts/Schyler-Italic.ttf
  #         style: italic
  #   - family: Trajan Pro
  #     fonts:
  #       - asset: fonts/TrajanPro.ttf
  #       - asset: fonts/TrajanPro_Bold.ttf
  #         weight: 700
  #
  # For details regarding fonts from package dependencies,
  # see [https://flutter.dev/custom-fonts/#from-packages](https://flutter.dev/custom-fonts/#from-packages)
```

运行 pub get 来注册更改。

5.导航到 lib 目录中的 main.dart 文件，并用以下内容替换整个代码:

主.镖

```
import 'dart:async';import 'package:flutter/cupertino.dart';
import 'package:flutter/material.dart';
import 'package:url_launcher/url_launcher.dart';void main() {
  runApp(MyApp());
}class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
        home: SplashScreen(),
        debugShowCheckedModeBanner: false
    );
  }
}class SplashScreen extends StatefulWidget {
  @override
  _SplashScreenState createState() => _SplashScreenState();
}class _SplashScreenState extends State<SplashScreen> {
  @override
  void initState() {
    // TODO: implement initState
    super.initState();
    Timer(Duration(seconds: 3), () {
      Navigator.of(context)
          .pushReplacement(MaterialPageRoute(builder: (_) => HomePage()));
    });
  } @override
  Widget build(BuildContext context) {
    return Scaffold(
      backgroundColor: Colors.blue,
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            // logo here
            Image.asset(
              'assets/images/picture.png',
              height: 200,
            ),
            SizedBox(
              height: 40,
            ),
            CircularProgressIndicator(
              valueColor: AlwaysStoppedAnimation<Color>(Colors.black),
            )
          ],
        ),
      ),
    );
  }
}class HomePage extends StatefulWidget {
  @override
  _MyHomePageState createState() => _MyHomePageState();
}class _MyHomePageState extends State<HomePage> {
  //List<String> _titles = ["Home", "Profile", "Shop"];
  List<Widget> _items = [
    Text("First page"),
    Text("Second Page"),
    Text("Third Page")
  ];
  int _selectedIndex = 0; @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text("Application Name"),
      ),
      body:Center(
          child: IndexedStack(
              index: _selectedIndex,
              children: _items
          )//_items.elementAt(_index),
      ),
      bottomNavigationBar: _showBottomNav(),
    );
  } Widget _showBottomNav()
  {
    return BottomNavigationBar(
      items: [
        BottomNavigationBarItem(
          icon: Icon(Icons.home),
          label: 'Home',
        ),
        BottomNavigationBarItem( icon: Icon(Icons.image),
          label: 'Images',
        ),
        BottomNavigationBarItem(
          icon: Icon(Icons.phone),
          label: 'Contact',
        ),
      ],
      currentIndex: _selectedIndex,
      selectedItemColor: Colors.blue,
      unselectedItemColor: Colors.grey,
      onTap: _onTap,
    );
  } void _onTap(int index)
  {
    _selectedIndex = index;
    setState(() { });
  }
}
```

6.您的多页应用程序已经准备好了！替换

`Text("First page"),
Text("Second Page"),
Text("Third Page")`

在 main.dart 文件中使用您自己的应用程序组件！！