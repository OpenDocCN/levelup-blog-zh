# 一种在 Go 中读取环境变量的有效方法

> 原文：<https://levelup.gitconnected.com/an-effective-way-to-read-environment-variables-in-go-7454e6613ae5>

## 权威指南

## 初学者在环境变量中工作的简单权威指南

![](img/a76d6d364207a22c9d193a46bf1474c5.png)

[https://github.com/MariaLetta/free-gophers-pack/](https://github.com/MariaLetta/free-gophers-pack/)

# 介绍

[十二因素](https://12factor.net/)应用程序的原则之一是在环境中存储应用程序配置，因为应用程序配置很可能会决定不同区域的部署变化，如试运行、生产、开发人员环境等

这里的配置如下:

1.  数据库，其他支持服务 URL
2.  应用程序秘密

GO 提供了一个标准的`os`包，其中包含了一些函数，可以帮助以编程方式处理环境变量。以下是可用于与环境变量交互的函数

# `os.Setenv()`

这个函数将值设置到系统环境中，为了设置你需要通过环境变量的键和值对，你需要设置。

**示例:**

```
package main
​
import "os"
​
func main() {
  os.Setenv("API_KEY", "A8D8S9DS90D89SD09SD8S09D8S90A")
}
```

# `os.Getenv()`

如果你需要获取环境变量，你可以这样做，你需要传递的参数是键，如果存在的话，这个键反过来获取这个键的值。

**示例:**

```
package main
​
import "os"
​
func main() {
  apiKey := os.Getenv("API_KEY")
}
```

# `os.Unsetenv()`

如果您想从运行应用程序的系统中删除任何环境变量，这个函数非常有用。此参数此函数接受您需要删除的环境变量的键。

**例如:**

```
package main
​
import "os"
​
func main() {
  os.Unsetenv("API_KEY")
}
```

# `os.ExpandEnv`

ExpandEnv 根据当前环境的值替换字符串中的${var}或$var

**示例:**

```
package main
​
import (
    "fmt"
    "os"
)
​
func main() {
    os.Setenv("NAME", "gopher")
    os.Setenv("BURROW", "/usr/gopher")
​
    fmt.Println(os.ExpandEnv("$NAME lives in ${BURROW}."))
​
}
Output:
gopher lives in /usr/gopher.
```

# `os.LookupEnv()`

该函数获取由关键字命名的值环境变量，如果变量不存在，则返回空值，布尔值为假。否则，它将返回值(可以为空)，并且布尔值为真。

**例子:**

```
package main
​
import (
    "fmt"
    "os"
)
​
func main() {
    show := func(key string) {
        val, ok := os.LookupEnv(key)
        if !ok {
            fmt.Printf("%s not set\n", key)
        } else {
            fmt.Printf("%s=%s\n", key, val)
        }
    }
​
    os.Setenv("SOME_KEY", "value")
    os.Setenv("EMPTY_KEY", "")
​
    show("SOME_KEY")
    show("EMPTY_KEY")
    show("MISSING_KEY")
​
}
Output:
SOME_KEY=value
EMPTY_KEY=
MISSING_KEY not set
```

# `os.Clearenv`

该函数删除所有环境变量。

**例如:**

```
package main
​
import "os"
​
func main() {
  os.Clearenv()
}
```

# `os.Environ()`

该函数以“key=value”的形式返回表示环境的字符串的副本。

=============================================================

此后，如果您是一名新的 GO 初学者，可以在以后阅读这篇文章，因为下面解释了如何使用另一个名为 **VIPER** 的第三方工具，它主要用于 GO 社区，通过根据您的应用程序运行的位置加载正确的环境变量，使应用程序开发变得更加容易

项目地点:[https://github.com/spf13/viper](https://github.com/spf13/viper)

**如何安装**

```
go get github.com/spf13/viper
```

Viper 可以处理所有类型的配置，例如

*   阅读 JSON，TOML，YAML，envfiles …
*   现场观看和重读配置文件
*   从环境变量中读取
*   从远程配置系统(etcd 或 Consul)读取，并观察变化
*   从命令行标志中读取
*   从缓冲区读取
*   设置显式值

# **设置默认环境变量**

这在没有通过配置文件设置键的情况下很有用，可以如下设置

```
viper.SetDefault("ContentDir", "content")
viper.SetDefault("LayoutDir", "layouts")
viper.SetDefault("Taxonomies", map[string]string{"tag": "tags", "category": "categories"})
```

# 正在读取配置文件

Viper 知道在哪里寻找配置文件，它支持 JSON、TOML、YAML、HCL、INI、envfile 和 Java 属性文件。

如何使用 Viper 搜索和读取配置文件的示例。不需要任何特定的路径，但是在需要配置文件的地方至少应该提供一个路径。

```
viper.SetConfigName("config") // name of config file (without extension)
viper.SetConfigType("yaml") // REQUIRED if the config file does not have the extension in the name
viper.AddConfigPath("/etc/appname/")   // path to look for the config file in
viper.AddConfigPath("$HOME/.appname")  // call multiple times to add many search paths
viper.AddConfigPath(".")               // optionally look for config in the working directory
err := viper.ReadInConfig() // Find and read the config file
if err != nil { // Handle errors reading the config file
	panic(fmt.Errorf("fatal error config file: %w", err))
}
```

# **观看并重新阅读配置文件**

Viper 支持让应用程序在运行时实时读取配置文件的能力。

这消除了重新启动服务器以使配置生效的需要。应用程序可以在运行时读取对配置文件的更新。这可以通过 viper 实例到 watchConfig 来实现。

```
viper.WatchConfig()
```

> 使用 ENV 变量时，重要的是要认识到 Viper 将 ENV 变量区分大小写。

# 从 Viper 获取值

在 Viper 中，以下函数和方法是根据值类型获取值的不同方式:

*   `Get(key string) : interface{}`
*   `GetBool(key string) : bool`
*   `GetFloat64(key string) : float64`
*   `GetInt(key string) : int`
*   `GetIntSlice(key string) : []int`
*   `GetString(key string) : string`
*   `GetStringMap(key string) : map[string]interface{}`
*   `GetStringMapString(key string) : map[string]string`
*   `GetStringSlice(key string) : []string`
*   `GetTime(key string) : time.Time`
*   `GetDuration(key string) : time.Duration`
*   `IsSet(key string) : bool`
*   `AllSettings() : map[string]interface{}`

需要认识到的一点是，如果没有找到，每个 Get 函数都将返回一个零值。要检查给定的键是否存在，可以使用`IsSet()`方法。

```
viper.GetString("logfile") // case-insensitive Setting & Getting
if viper.GetBool("verbose") {
	fmt.Println("verbose enabled")
}
```

这是关于 viper 的一个非常简短的介绍，关于 viper 还有更多的东西可以在所有类型的用例中实现，值得在这里阅读[https://github.com/spf13/viper](https://github.com/spf13/viper)