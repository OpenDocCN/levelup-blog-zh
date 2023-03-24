# 在 Go 中监听随机可用端口

> 原文：<https://levelup.gitconnected.com/listening-to-random-available-port-in-go-3541dddbb0c5>

![](img/1d3908b6bba2054473a907754d7e964c.png)

Unsplash 上 [@mbaumi](https://unsplash.com/@mbaumi) 的照片

要使用 Golang 中随机可用的端口，可以使用`:0`。我相信端口`0`可以适用于另一种语言，就像我在 python 中尝试的那样。

```
$ python -m SimpleHTTPServer 0
Serving HTTP on 0.0.0.0 port 43481 ...
```

根据 [lifewire](https://www.lifewire.com/port-0-in-tcp-and-udp-818145) ，端口`0`是一个`non-ephemeral port`，它作为通配符告诉系统**寻找任何可用的端口，特别是在 Unix 操作系统**中。

# Go 代码

```
package mainimport (
    "log"
    "net"
    "net/http"
)func createListener() (l net.Listener, close func()) {
    l, err := net.Listen("tcp", ":0")
    if err != nil {
        panic(err)
    }return l, func() {
        _ = l.Close()
    }
}func main() {
    l, close := createListener()
    defer close()http.Handle("/", http.HandlerFunc(func(rw http.ResponseWriter, r *http.Request) {
        // handle like normal
    }))log.Println("listening at", l.Addr().(*net.TCPAddr).Port)
    http.Serve(l, nil)
}
```

执行它:

```
$ go run main.go 
2022/01/04 17:40:16 listening at 33845
```

感谢您的阅读！

*原载于 2022 年 1 月 4 日*[*https://clavinjune . dev*](https://clavinjune.dev/en/blogs/listening-to-random-available-port-in-go/)*。*