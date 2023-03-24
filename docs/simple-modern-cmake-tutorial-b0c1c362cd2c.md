# 简单的现代 CMake 教程第 1 部分

> 原文：<https://levelup.gitconnected.com/simple-modern-cmake-tutorial-b0c1c362cd2c>

## “面向对象”的现代计算机动画简介

![](img/2ade109afe8115a0640cd3ed00f75414.png)

Artem Sapegin 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

CMake 是一个用于管理软件构建过程的开源工具。CMake 是一个流行的工具，尤其是在 C++社区中。目前很多 C++项目都在使用 CMake。正如你在下面看到的 Google 趋势结果，与其他构建系统如 SCons、Bazel 相比，更多的人对 CMake 感兴趣(所以熟悉它肯定是个好主意！).

![](img/36e38a19b019f7e84c9ab58ca32cd94d.png)

不同构建系统(Bazel，CMake，SCons)上的 Google 趋势。

实际上，CMake 并不像以前那样流行，因为它的一些语法和不太优雅的特性。然而，自 CMake 3 . x(2014 年 6 月 6 日发布)以来，它增加了新的功能，使 CMake 更加强大、干净和优雅。较新版本的 CMake (3.1 或更高版本)有时被称为“现代 CMake”。

在这篇文章中，我将用一些简单的例子解释现代 CMake 的某些方面(这里是[到 Github 库的链接](https://github.com/rjhcnf/cmake_tutorial))。
特别是，我想谈的几点是:

*   在面向对象编程中，将“构建目标”(即使用 CMake 构建的可执行文件或库)视为“对象”的概念，以便更好地构建您的项目，以及构建依赖项的继承的概念是可传递的(可传递依赖项)。
*   如何导出/安装 CMake 包并从另一个 CMake 项目导入它。

Daniel Pfeifer 的有效的 CMake⁴演讲给了我很多有用的见解来理解现代的 CMake concept⁵，并帮助我写了这篇文章。

# 示例 CMake 包

首先，让我看一下我们将在本教程中使用的项目结构。

```
cmake_tutorial
|__ app                 # **MyApplication package** 
|   |__ CMakeLists.txt
|   |__ src
|       |__ main.cpp
|    
|
|__ my_libraries       # **MyLibraries package**
|   |__ CMakeLists.txt
|   |__ cmake
|   |   |__ MyLibrariesConfig.cmake
|   |
|   |__ include
|   |   |__ my_libraries
|   |      |__ my_library_a.hpp
|   |
|   |__ src
|       |__ my_library_a.cpp
|   
| 
|__ some_libraries     # **SomeLibraries package**
   |__ CMakeLists.txt
   |__ cmake
   |   |__ SomeLibrariesConfig.cmake
   |
   |__ include
   |   |__ some_libraries
   |     |__ some_library_a.hpp
   |     |__ some_library_b.hpp
   |__ src
       |__ some_library_a.cpp
       |__ some_library_b.cpp
```

*app* 目录包含 MyApplication 可执行包。 *my_libraries* 目录包含 MyLibraries 库包。 *some_libraries* 包含 SomeLibraries 库包。我的应用程序依赖于我的库，而我的库又依赖于一些库。所以 3 个包之间的关系如下。

```
MyApplication --depends--> MyLibraries --depends--> SomeLibraries
```

通常，有三种方法可以从另一个 CMake 项目访问一个 CMake 项目:子目录、导出的构建目录和导入已经安装的预构建包。在这篇文章中，我想解释第三种选择；如何安装一个 CMake 包并从另一个 CMake 项目中导入(所以，我们假设 MyApplication、MyLibraries 和 SomeLibraries 分别作为单独的包开发)。因此，这三个包在同一个 CMake 树中是**而不是**。

# 安装一些库包

由于每个人都依赖于一些库(间接的 MyApplication 和直接的 MyLibraries)，我们需要构建和安装它以便其他人可以使用它。some_libraries 目录下的 CMakeLists.txt 如下图所示，

```
## CMakeLists.txt for SomeLibraries ##cmake_minimum_required(VERSION 3.5)
project(SomeLibraries VERSION 1.0 LANGUAGES CXX)add_library(SomeLibraryA src/some_library_a.cpp)target_include_directories(SomeLibraryA 
PUBLIC 
$<INSTALL_INTERFACE:include>
$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
)add_library(SomeLibraryB src/some_library_b.cpp)target_include_directories(SomeLibraryB
PUBLIC
$<INSTALL_INTERFACE:include>
$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
)install(TARGETS SomeLibraryA SomeLibraryB
EXPORT SomeLibraries-export
LIBRARY DESTINATION lib
ARCHIVE DESTINATION lib
)install(EXPORT SomeLibraries-export
FILE
SomeLibrariesTargets.cmake
NAMESPACE
SomeLibraries::
DESTINATION
lib/cmake/SomeLibraries
)install(FILES
${CMAKE_CURRENT_SOURCE_DIR}/include/SomeLibraries/some_library_a.hpp
${CMAKE_CURRENT_SOURCE_DIR}/include/SomeLibraries/some_library_b.hpp
DESTINATION "include/SomeLibraries")install(FILES
${CMAKE_CURRENT_SOURCE_DIR}/cmake/SomeLibrariesConfig.cmake
DESTINATION "lib/cmake/SomeLibraries" )
```

首先，您可能已经注意到 SomeLibraries 包定义了两个目标，SomeLibraryA 和 SomeLibraryB。它们都有需要安装的公共头文件 some_library_a.hpp 和 some_library_b.hpp(见前面展示的目录树)。

有趣的部分从下面的部分开始。

```
target_include_directories(SomeLibraryA 
PUBLIC 
$<INSTALL_INTERFACE:include>
$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
)
```

通过*target _ include _ directory*，它为目标 SomeLibraryA 指定包含路径。实际上，你可以看到对象“SomeLibraryA”正在调用它的成员函数*target _ include _ directories*，就像面向对象的编程范例一样来设置对象的数据(在本例中是包含路径)。像一些类的普通 Setter 函数一样，它设置对象的属性。指定的包含路径用于构建 SomeLibraryA 目标，但是，由于它具有 *PUBLIC* 关键字，当它通过 *target_link_libraries(稍后解释)向 SomeLibraryA 添加依赖项时，包含路径将被传递给 SomeLibraryA 的用户。在这个例子中，SomeLibraryA 有一个公开的头。*

下面的[生成器表达式](https://cmake.org/cmake/help/v3.5/manual/cmake-generator-expressions.7.html)用于根据情况有选择地选择包含路径。在*$<BUILD _ INTERFACE:>*中指定的包含路径在构建**some library a 目标本身时使用。另一方面，假设我们构建了 SomeLibraryA 目标，并且我们成功地安装了它，以便它可以用于另一个 CMake 项目(我们将在后面深入讨论安装部分)。那么，如果我们使用[*find _ package*](https://cmake.org/cmake/help/latest/command/find_package.html)*(*SomeLibraryA*)*并在另一个依赖于 SomeLibraryA 的 CMake 项目中导入 some library a 目标，那么*$<INSTALL _ INTERFACE:>*中指定的 include 路径就是used *。同样的事情也适用于一些图书馆。***

**接下来，我们需要为 SomeLibraryA(B)的构建工件指定安装位置。**

```
**install(TARGETS SomeLibraryA SomeLibraryB
EXPORT SomeLibraries-export
LIBRARY DESTINATION lib
ARCHIVE DESTINATION lib
)install(EXPORT SomeLibraries-export
FILE
SomeLibrariesTargets.cmake
NAMESPACE
SomeLibraries::
DESTINATION
lib/cmake/SomeLibraries
)**
```

**在上面的第一次 [*安装*](https://cmake.org/cmake/help/v3.13/command/install.html) 时，设置每个目标的安装位置，并将信息输出到某个库-导出。在第二次*安装时，*它将设置为 SomeLibraries-export 的信息写入 somebrariestargets . cmake 文件，并将 somebrariestargets . cmake 文件复制到 lib/cmake/SomeLibraries 目录。可以为每个导出的目标指定名称空间。你可以把它想象成 C++这样的编程语言中的名称空间。(除了普通名称空间概念带来的好处之外，它还防止了一些特定于 CMake 上下文的小问题，但是为了不分散注意力，我现在将跳过它。一般来说，使用名称空间是一个好习惯。**

**然后，它将公共头复制到安装位置。**

```
**install(FILES
${CMAKE_CURRENT_SOURCE_DIR}/include/SomeLibraries/some_library_a.hpp
${CMAKE_CURRENT_SOURCE_DIR}/include/SomeLibraries/some_library_b.hpp
DESTINATION "include/SomeLibraries")**
```

**这个安装位置应该对应于我们之前用*target _ include _ directory、*$<INSTALL _ INTERFACE:include>指定的位置。否则，当构建依赖于 SomeLibraryA(B)的另一个包时，将找不到公共头文件，因为导入的 SomeLibraryA(B)属性与实际安装不匹配。**

```
**install(FILES
${CMAKE_CURRENT_SOURCE_DIR}/cmake/SomeLibrariesConfig.cmake
DESTINATION "lib/cmake/SomeLibraries" )**
```

**这里，它将 cmake/中的 SomeLibrariesConfig.cmake 复制到常规位置“lib/cmake/SomeLibraries”(我们复制 SomeLibrariesTargets.cmake 的同一个位置，见前面几段。**

**SomeLibrariesConfig.cmake 如下图，**

```
**include(CMakeFindDependencyMacro)include("${CMAKE_CURRENT_LIST_DIR}/SomeLibrariesTargets.cmake")**
```

**当另一个包使用*find _ package*(some libraries)时，CMake 搜索这个 SomeLibrariesConfig.cmake 文件。SomeLibrariesConfig.cmake 包含 SomeLibrariesTargets.cmake 文件，该文件包含目标 SomeLibraryA 和 SomeLibraryB 的导出信息，这就是如何将目标及其属性导入到另一个项目中。**

**就是这样。这是您需要在 CMakeLists.txt 和 SomeLibrariesConfig.cmake 中指定的最小值，以便从 SomeLibraries 包中安装 SomeLibraryA(B)目标，这样其他包就可以导入并使用它们。现在，您只需要运行 cmake 并执行 make install，如下所示。**

```
**cd some_libraries
mkdir build
cd build
cmake ..
cmake --build . --target install**
```

# **将 SomeLibraries 包中的 SomeLibraryA(B)目标导入 MyLibraries 项目**

**现在，我们已经构建并安装了一些库包，我们准备构建依赖于它的 MyLibraries 包。MyLibraries 包的 CMakeLists.txt 文件如下所示，**

```
**## CMakeLists.txt for MyLibraries ##cmake_minimum_required(VERSION 3.5)
project(MyLibraries VERSION 1.0 LANGUAGES CXX)find_package(SomeLibraries REQUIRED)add_library(MyLibraryA src/my_library_a.cpp)target_include_directories(MyLibraryA
PUBLIC
$<INSTALL_INTERFACE:include>
$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
)target_link_libraries(MyLibraryA
PUBLIC
SomeLibraries::SomeLibraryA
PRIVATE
SomeLibraries::SomeLibraryB
)install(TARGETS MyLibraryA
EXPORT  MyLibraries-export
LIBRARY DESTINATION lib
ARCHIVE DESTINATION lib
)install(EXPORT MyLibraries-export
FILE
MyLibrariesTargets.cmake
NAMESPACE
MyLibraries::
DESTINATION
lib/cmake/MyLibraries}
)install(FILES
${CMAKE_CURRENT_SOURCE_DIR}/include/MyLibraries/my_library_a.hpp
DESTINATION "include/MyLibraries")install(FILES
${CMAKE_CURRENT_SOURCE_DIR}/cmake/MyLibrariesConfig.cmake
DESTINATION "lib/cmake/MyLibraries" )**
```

**它使用[*find _ package*](https://cmake.org/cmake/help/v3.0/command/find_package.html)*(SomeLibraries REQUIRED)*来导入 some libraries 包。**

```
**find_package(SomeLibraries REQUIRED)**
```

**这将触发 CMake 搜索 SomeLibrariesConfig.cmake 文件。这最终将 *SomeLibraries* 包中的 SomeLibraryA(B)目标及其所有属性导入 MyLibraries CMake 项目。**

**然后，这里是有趣的部分，使用[*target _ link _ libraries*](https://cmake.org/cmake/help/latest/command/target_link_libraries.html)**

```
**target_link_libraries(MyLibraryA
PUBLIC
SomeLibraries::SomeLibraryA
PRIVATE
SomeLibraries::SomeLibraryB
)**
```

**“目标和属性”的基本概念以及“对象和成员函数”的类比同样适用于 *target_link_libraries* 正如它们适用于我们之前讨论过的*target _ include _ directory*。**

**您可能已经注意到， *SomeLibraries* 是我们在生成 SomeLibrariesTargets.cmake 文件时用 *install* 函数指定的名称空间。通过将 SomeLibraries::SomeLibraryA(B)指定为依赖项，MyLibraryA 目标继承了 SomeLibraryA(B)目标的所有公共属性。此外，通过添加关键字 PUBLIC，MyLibraryA 将把从 SomeLibraryA 继承的属性传递给将 MyLibraryA 作为依赖关系链接到其 CMakeLists.txt. 中的 *target_link_libraries* 的任何其他目标。另一方面，通过添加关键字 private，它指定 MyLibraryA 对 SomeLibraryB 的依赖关系为“PRIVATE”，并且从 SomeLibraryB 继承的属性将**而不是**被传递给链接 my library 的其他目标这是现代 CMake 中传递依赖如何工作的基础。**

**最后，为了使用 MyApplication 包中的 MyLibraries 包，我们需要安装 MyLibraries 包。基本上，我们需要做和安装一些库包一样的事情。**

**我们还需要编写 MyLibrariesConfig.cmake 来安装 MyLibraries 包，就像我们安装 SomeLibraries 包一样。需要注意的一点是，在 MyLibrariesConfig.cmake 内部，我们需要用*find _ dependency(SomeLibraries REQUIRED)*指定对 some libraries 的依赖关系，如下所示，**

```
**include(CMakeFindDependencyMacro)find_dependency(SomeLibraries REQUIRED)include("${CMAKE_CURRENT_LIST_DIR}/MyLibrariesTargets.cmake")**
```

**然后，我们只需要执行下面的命令，**

```
**cd my_libraries
mkdir build
cd build
cmake ..
cmake --build . --target install**
```

# **将 MyLibraries 包中的 MyLibraryA 目标导入 MyApplication 项目**

**最后，我们准备构建 MyApplication 可执行包。MyApplication 可执行包的 CMakeLists.txt 如下所示，**

```
**## CMakeLists.txt for the MyApplication executable package ##cmake_minimum_required(VERSION 3.5)
project(MyApplication VERSION 1.0 LANGUAGES CXX)find_package(MyLibraries REQUIRED)add_executable(MyApplication src/main.cpp)target_link_libraries(MyApplication PRIVATE MyLibraries::MyLibraryA)**
```

**基本上，如你所见，没什么新东西。由于 MyLibraryA 目标传递 SomeLibraryA 目标的公共属性(在本例中，是安装公共头文件的 include 路径)，所以在 MyApplication 目标的构建过程中可以找到 some_library_a.hpp。**

# **摘要**

**在本教程中，我解释了现代 CMake 的以下方面。**

*   **在面向对象编程中，为了更好地构建项目，将“构建目标”(即使用 CMake 构建的可执行文件或库)视为“对象”的概念，以及构建依赖项传递性继承的思想(传递依赖项)。**
*   **如何导出/安装 CMake 包并从另一个 CMake 项目导入它。**

**[Simple Modern CMake Tutorial Part 2](https://medium.com/@koheiotsuka701/simple-modern-cmake-tutorial-part-2-285614d6a0ce)**

**[1]: [https://cmake.org/](https://cmake.org/)**

**[2]: [https://scons.org/](https://scons.org/)**

**[3]: [https://bazel.build/](https://bazel.build/)**

**[4]: C++Now 2017:Daniel Pfeifer “Effective CMake” [https://www.youtube.com/watch?v=bsXLMQ6WgIk](https://www.youtube.com/watch?v=bsXLMQ6WgIk)**

**[5]: [https://cliutils.gitlab.io/modern-cmake/](https://cliutils.gitlab.io/modern-cmake/)**