# 如何在 CMake 中设置和请求特定的包版本:简单的现代 CMake 教程第 2 部分

> 原文：<https://levelup.gitconnected.com/simple-modern-cmake-tutorial-part-2-285614d6a0ce>

![](img/be3985dcb47e20adeef2d59051a1ad11.png)

保罗·埃施-洛朗在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

在我之前的帖子《 [*简单的现代 CMake 教程第 1 部分~面向对象的现代 CMake*](/simple-modern-cmake-tutorial-b0c1c362cd2c) 简介》中，我解释了现代 CMake 最重要的方面，也就是目标和属性的概念。使用现代 CMake 特性，如 *target_link_libraries* 、*target _ include _ directory*，我们可以在 CMake 包结构中实现模块化设计。在教程中，我们实际上创建了示例 CMake 包来演示如何导出、安装自定义 CMake 包以及如何从另一个 CMake 包导入它(这里是[到 Github 库的链接](https://github.com/rjhcnf/cmake_tutorial))。

现在，您知道了如何导出自己的包供他人使用。在您发布您的包之后，最终，其他库或应用程序开始使用您的库并依赖它(至少这是您应该期望和准备的)。与此同时，您仍然在向您的包中添加更改(比如新特性、错误修复等)并更新它。很快您会注意到您需要对您的包进行版本控制。这不仅是为了您自己跟踪软件版本的开发，也是为了您的软件包的用户指定对您的软件包的正确版本的依赖。实际上，CMake 可以指定您创建的包的版本，也可以指定请求的其他包的版本。

在这篇文章中，我将解释如何在分发软件包时设置软件包版本，以及如何请求其他软件包的特定版本。

# 生成包版本文件

我们将使用来自[教程第 1 部分](/simple-modern-cmake-tutorial-b0c1c362cd2c)的相同[代码](https://github.com/rjhcnf/cmake_tutorial)通过实际例子来浏览主题。这是项目的简要概述(请阅读第一部分了解更多细节)。

首先我们来看看 SomeLibraries 的 CMakeLists.txt 文件(在 some_libraries 目录下)。下面的粗体文本是 CMakeLists.txt 文件中新添加的部分。

```
## CMakeLists.txt for SomeLibraries ##cmake_minimum_required(VERSION 3.5)
project(SomeLibraries VERSION 1.0 LANGUAGES CXX)add_library(SomeLibraryA src/some_library_a.cpp)target_include_directories(SomeLibraryA
PUBLIC
$<INSTALL_INTERFACE:include>
$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>)add_library(SomeLibraryB src/some_library_b.cpp)target_include_directories(SomeLibraryB
PUBLIC
$<INSTALL_INTERFACE:include>
$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>)install(TARGETS SomeLibraryA SomeLibraryB
EXPORT SomeLibraries-export
LIBRARY DESTINATION lib
ARCHIVE DESTINATION lib)install(EXPORT SomeLibraries-export
FILE
SomeLibrariesTargets.cmake
NAMESPACE
SomeLibraries::
DESTINATION
lib/cmake/SomeLibraries)install(FILES
${CMAKE_CURRENT_SOURCE_DIR}/include/SomeLibraries/some_library_a.hpp
${CMAKE_CURRENT_SOURCE_DIR}/include/SomeLibraries/some_library_b.hpp
DESTINATION
"include/SomeLibraries")install(FILES
${CMAKE_CURRENT_SOURCE_DIR}/cmake/SomeLibrariesConfig.cmake
DESTINATION "lib/cmake/SomeLibraries")**#### The below block was newly added for part 2 ####
#### This is the block specifying & exporting the version of package** **include(CMakePackageConfigHelpers)****write_basic_package_version_file(
${CMAKE_CURRENT_SOURCE_DIR}/cmake/SomeLibrariesConfigVersion.cmake
VERSION ${PROJECT_VERSION}
COMPATIBILITY SameMajorVersion)****install(FILES
${CMAKE_CURRENT_SOURCE_DIR}/cmake/SomeLibrariesConfigVersion.cmake
DESTINATION "lib/cmake/SomeLibraries")**
```

让我们一个一个地看添加的文本:

```
include(CMakePackageConfigHelpers)
```

这里它正在加载一个名为 *CMakePackageConfigHelpers 的模块。*它是 CMake 中包含的一个模块，它定义了可以用来为给定的包生成配置文件的实用函数。我们在这里包含了这个模块，这样我们就可以使用*write _ basic _ package _ version _ file*函数，它写在下一行。

```
write_basic_package_version_file( ${CMAKE_CURRENT_SOURCE_DIR}/cmake/SomeLibrariesConfigVersion.cmake VERSION ${PROJECT_VERSION} 
COMPATIBILITY SameMajorVersion)
```

*write _ basic _ package _ version _ file*用于生成一个配置文件，该文件指定了包的版本和兼容性信息(详见下文)。第一个参数是将要生成的文件的名称。名称必须是 *<包名>配置版本. cmake* 。

*版本*是您想要指定的包版本。可以指定像*<major . minor . patch>*这样的。如果没有给出版本，则使用用*PROJECT(some libraries VERSION 1.0 LANGUAGES CXX)*设置的 PROJECT_VERSION 变量(在上面的例子中，为了清楚起见，我明确地写了它)。如果没有设置，它会给出错误。

*兼容性*是关于软件包的兼容性信息。假设其他包请求您的包的特定版本 1.0.0，但是安装在构建环境上的版本是 2.0.0，那么它需要知道安装的版本(2.0.0)是否与请求的版本(1.0.0)兼容，即安装的版本是否向后兼容。这种信息只有您作为您的包的开发者知道，并且您需要提供它。在上述情况下，指定了 *SameMinorVersion* 。这意味着主版本和次版本必须与要求的相同，例如，如果要求 2.1 版本，则 2.0 版本将不兼容。您还可以指定其他兼容性类型(*any new erver version | same major version | same minor version | exact version*)。更多细节请参考*CMakePackageConfigHelpers*的文档。

最后，它将生成的 someblibrariesconfigversion . cmake 文件复制到指定的常规位置，以便在另一个包使用它时由 *find_package* 函数找到。

```
install(FILES
${CMAKE_CURRENT_SOURCE_DIR}/cmake/SomeLibrariesConfigVersion.cmake
DESTINATION "lib/cmake/SomeLibraries")
```

# 请求包的特定版本

如前所述，MyLibraries 包使用了 SomeLibraries 包。让我们检查它的 CMakeLists.txt。此时，唯一新添加的是带有 *find_package* 的请求包版本，正如您在下面以粗体显示的那样。

```
cmake_minimum_required(VERSION 3.5)
project(MyLibraries VERSION 1.0 LANGUAGES CXX)find_package(SomeLibraries **1.0** REQUIRED)add_library(MyLibraryA src/my_library_a.cpp)target_include_directories(MyLibraryA
PUBLIC
$<INSTALL_INTERFACE:include>
$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>)target_link_libraries(MyLibraryA
PUBLIC
SomeLibraries::SomeLibraryA
PRIVATE
SomeLibraries::SomeLibraryB)install(TARGETS MyLibraryA
EXPORT  MyLibraries-export
LIBRARY DESTINATION lib
ARCHIVE DESTINATION lib)install(EXPORT MyLibraries-export
FILE
MyLibrariesTargets.cmake
NAMESPACE
MyLibraries::
DESTINATION
lib/cmake/MyLibraries)install(FILES
${CMAKE_CURRENT_SOURCE_DIR}/include/MyLibraries/my_library_a.hpp
DESTINATION "include/MyLibraries")install(FILES
${CMAKE_CURRENT_SOURCE_DIR}/cmake/MyLibrariesConfig.cmake
DESTINATION "lib/cmake/MyLibraries")
```

使用 *find_package(需要 some libraries 1.0)*，正在请求特定版本为 1.0 的 *SomeLibraries* 包。然后，它找到我们为 *SomeLibraries* 生成的 somelibrariesconfigversion . cmake 文件，并检查版本和兼容性信息。如果它与所请求的不匹配，它会给出错误。

# 摘要

在这篇文章中，我解释了如何在发布包时设置包版本，以及如何用 CMake 请求其他包的特定版本。

[1]:软件版本，[https://en.wikipedia.org/wiki/Software_versioning](https://en.wikipedia.org/wiki/Software_versioning)

[2]: CMakePackageConfigHelpers，[https://cmake . org/cmake/help/latest/module/CMakePackageConfigHelpers . html](https://cmake.org/cmake/help/latest/module/CMakePackageConfigHelpers.html)

[3]: *找 _ 包*()。[https://cmake.org/cmake/help/v3.0/command/find_package.html](https://cmake.org/cmake/help/v3.0/command/find_package.html)