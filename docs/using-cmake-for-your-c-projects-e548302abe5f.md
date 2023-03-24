# 为您的 C++项目使用 CMAKE

> 原文：<https://levelup.gitconnected.com/using-cmake-for-your-c-projects-e548302abe5f>

如果你和我一样，当我听说我需要学习一门全新的语言来编译你的代码时，我觉得太费力了。所以，就像当时的弱者一样，我爬回了 visual studio，那里一切都是解决方案。当我从事一个在 visual studio 之外编写的项目时，这一切都改变了，所以它使用了 CMAKE，所以我认为学习 CMAKE 可能是一个好主意，这样我就可以了解正在发生的事情。

![](img/93f82dda341526d053df4fd53cee0bd4.png)

沙哈达特·拉赫曼在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 一个起点

首先，我们需要创建一个 CMakeLists.txt 文件。我们还需要指定一个 CMAKE 版本来使用，我们的项目名称是什么，并添加我们试图运行什么。

```
CMakeLists.txtcmake_minimum_required(VERSION 3.10)project(Application)add_executable(Application application.cpp)
```

所以更进一步，我们可能想要指定需要什么版本的 C++并且我们可能想要指定我们的项目是什么版本。

```
cmake_minimum_required(VERSION 3.10)
set(CMAKE_CXX_STANDARD_17)
set(CMAKE_CXX_REQUIRED ON)project(Application VERSION 1.0)add_executable(Application application.cpp)
```

这对于小型的单个文件项目来说很好，但是如果您想在项目中添加类呢？我们不能让 CMakeLists 文件保持原样，假设 CMakeLists 文件可以编译，但项目不会构建，因为它不知道头文件在哪里。要向 CMAKE 文件添加头文件，我们必须指定该文件的位置。

```
cmake_minimum_required(VERSION 3.10)
set(CMAKE_CXX_STANDARD_17)
set(CMAKE_CXX_REQUIRED ON)project(Application VERSION 1.0)
add_executable(Application application.cpp)
target_include_directories(Application PUBLIC "${PROJECT_BINARY_DIR}")
```

然而，上述实现假设您的函数都是内联函数，因此没有必要添加源文件。如果您的类有一个头文件和 cpp 文件，您需要将它包含在 CMakeLists 文件中。有两种方法可以做到这一点，第一种方法是 CMake 认可的方法，第二种方法不是。

## 准许的方法

```
cmake_minimum_required(VERSION 3.10)
set(CMAKE_CXX_STANDARD_17)
set(CMAKE_CXX_REQUIRED ON)project(Application VERSION 1.0)
add_executable(Application application.cpp class_1.cpp)
target_include_directories(Application PUBLIC "${PROJECT_BINARY_DIR}")
```

## 第二种方法

```
cmake_minimum_required(VERSION 3.10)
set(CMAKE_CXX_STANDARD_17)
set(CMAKE_CXX_REQUIRED ON)project(Application VERSION 1.0)file(GLOB_RECURSE SRC FILES src/*.cpp)
add_executable(Application application.cpp ${SRC FILES})
target_include_directories(Application PUBLIC "${PROJECT_BINARY_DIR}")
```

当处理较大的项目时，比如一个渲染引擎，你将需要使用一些库，比如你的图形 API。

```
add_library(glew STATIC IMPORTED GLOBAL)set_target_properties(glew PROPERTIES IMPORTED_LOCATION ${CMAKE_SOURCE_DIR}/dependencies/glew/lib/windows/glew32s.lib)
set_target_properites(glew PROPERTIES INCLUDE_DIRECTORIES ${CMAKE_SOURCE_DIR}/dependencies/glew/include)target_link_libraries(${PROJECT_NAME} PUBLIC glew)
```

一些外部库，如上图所示，对于不同的操作系统有不同的文件。您可以为您的项目所在的每个操作系统编写不同的 CMakeLists 文件，或者您可以将一些逻辑放入 CMakeLists 文件中，以便根据您所在的操作系统导入库的不同部分。

```
if(CMAKE_SYTEM_NAME MATCHES WIN32)
    #Import windows stuff
elseif(CMAKE_SYSTEM_NAME MATCHES Linux)
    #Import linux stuff
elseif(CMAKE_SYSTEM_NAME MATHCES UNIX) #UNIX is MacOS
    #Import UNIX stuff
```

同样重要的是要知道，使用 CMAKE 我们可以从自己的源文件创建自己的库，如果您打算在其他地方使用代码片段，或者创建一个非常大的项目，例如一个游戏引擎，您需要将多个软件片段组合在一起，这将非常有用。

```
cmake_minimum_required(VERSION 3.10)
set(CMAKE_CXX_STANDARD_17)
set(CMAKE_CXX_REQUIRED ON)project(Application VERSION 1.0)
add_library(Library_1 STATIC class1/class_1.cpp)
target_include_directories(Library_1 PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/class1/include)add_executable(Application application.cpp ${SRC FILES}
target_link_libraries(Application PUBLIC Library_1)
```

我们可以为我们创建的每个库这样做，并且总是在顶部的 CMakeLists 文件中这样做，或者(这将使我们管理 CMAKE 文件更容易)我们可以在子目录中创建一个 CMAKE 文件。

## class1/CMakeLists.txt

```
add_library(Library_1 STATIC class_1.cpp)
target_include_directories(Library_1 PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/include)
```

## CMakeLists.txt

```
cmake_minimum_required(VERSION 3.10)
set(CMAKE_CXX_STANDARD_17)
set(CMAKE_CXX_REQUIRED ON)add_subdirectory(Library_1)add_executable(Application application.cpp ${SRC FILES}
target_link_libraries(Application PUBLIC Library_1)
```

如你所见，这是一种更干净的做事方式。

最后，如果你想把你的程序分发给其他人，在你的项目中包含一个安装程序是有意义的，为此我们只需要使用 CPACK。要使用 CPACK，我们需要在主 CMakeLists 文件的底部添加几行代码，并在我们的项目中添加一个许可证，关于许可证代码的更多信息可以在这里找到。

```
cmake_minimum_required(VERSION 3.10)
set(CMAKE_CXX_STANDARD_17)
set(CMAKE_CXX_REQUIRED ON)add_subdirectory(Library_1)add_executable(Application application.cpp ${SRC FILES}
target_link_libraries(Application PUBLIC Library_1)include(InstallRequiredSystemLibraries)
set(CPACK_RESOURCE_FILE_LICENSE "${CMAKE_CURRENT_SOURCE_DIR}/License.txt")
set(CPACK_PACKAGE_VERSION_MAJOR "${APPLICATION_VERSION_MAJOR}")
set(CPACK_PACKAGE_VERSION_MINOR "${APPLICATION_VERSION_MINOR}")
include(CPack)
```

希望本文已经向您展示了 CMAKE 并不是什么可怕的东西，而是应该包含在您的 C++项目中的东西。就 CMAKE 能做什么而言，本文实际上只看到了冰山一角，如果正确使用，它会非常有用。要更详细地了解 CMAKE，网上有大量的视频和文章，我也建议您查看 CMAKE 文档，可在[这里](https://cmake.org/cmake/help/latest/index.html)找到。