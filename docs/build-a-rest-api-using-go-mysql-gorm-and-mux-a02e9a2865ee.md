# 使用 Go、MySQL、Gorm 和 mux 构建一个简单的 REST API

> 原文：<https://levelup.gitconnected.com/build-a-rest-api-using-go-mysql-gorm-and-mux-a02e9a2865ee>

![](img/e4911a6424d82298e9e20dcba7746610.png)

Go 是谷歌设计的。Golang 受 C 语言的影响，但更简单，可移植性更强。本文将带您使用 Go 构建一个简单的 REST API。

**关于应用**
该应用是一个简单的 REST API 服务器，将为足球场预订记录上的 CRUD 操作提供端点。

为了完成这项工作，我们必须先安装一些依赖项。

1.Gorilla `mux`:用于创建路由和 HTTP 处理程序。

```
go get github.com/gorilla/mux
```

2.`gorm`:MySQL 的 ORM 工具。

```
go get github.com/jinzhu/gorm
```

3.`mysql`:MySQL 驱动。

```
go get github.com/go-sql-driver/mysql
```

在开始之前，首先我们必须手动创建一个数据库。我正在使用 MySQL shell 创建一个数据库。登录 MySQL shell，使用以下语句创建数据库

```
CREATE DATABASE Football;
```

现在在您的项目目录中创建一个文件`main.go`并开始烹饪。我们希望将应用程序连接到数据库:

```
**db, err = gorm.Open(“mysql”, “user:password@tcp(127.0.0.1:3306)/dbname?charset=utf8&parseTime=True”)**
```

> **注意**:为了处理`time.Time`，需要包含`parseTime`作为参数。([更多支持参数](https://github.com/go-sql-driver/mysql#parameters))

现在让我们来看看我们的`main.go`文件:

让我们测试这个文件

```
abhi@abhi-Inspiron-N4050:~/$ **go build**
abhi@abhi-Inspiron-N4050:~/$ **go run main.go** 
**2019/07/13 00:49:33 Connection Established**
```

现在，创建一个简单的`Booking`结构，它包含`id`、`user`、`total_members`

```
type Booking struct{
 Id      int    `json:”id”`
 User    string `json:”user”`
 Members int  `json:”members”`
}
```

创建结构之后，是时候迁移我们的模式了

```
db.AutoMigrate(&Booking{})
```

**警告** : *自动迁移*只会创建表、缺失列和缺失索引，不会更改现有列的类型或删除未使用的列以保护您的数据。

# 创建 Web 服务器

现在我们创建一个 web 服务器来处理 HTTP 请求。为此，我创建了一个名为`handleRequests()`的新函数。在此功能下，创建一个`mux`路由器的新实例:

```
myRouter := mux.NewRouter().StrictSlash(true)
```

现在通过键入`go run main.go`运行代码

```
abhi@abhi-Inspiron-N4050:~/$ **go run main.go** 
**2019/07/13 00:59:54 Connection Established
2019/07/13 00:59:54 Starting development server at** [**http://127.0.0.1:10000/**](http://127.0.0.1:10000/)**2019/07/13 00:59:54 Quit the server with CONTROL-C.**
```

然后在浏览器中打开`[http://localhost:10000/](http://localhost:10000/)`，您应该会看到一个`Welcome to Homepage!`

# CRUD 操作

在这一部分，我们将进行**创建**、**读取**、**更新**和**删除**的操作

*   **创建**新的预订

将路线添加到在`handleRequests`功能中定义的路线列表中。然而，我们将在路由的末尾添加`.Methods("POST")`,以指定我们只希望在传入请求是`HTTP POST`请求时调用这个函数。

```
myRouter.HandleFunc(“/new-booking”, createNewBooking).Methods(“POST”)
```

让我们创建一个新函数`createNewBooking()`，它将获取 POST 请求数据，并在数据库中创建一个新的预订条目。但是首先我们必须将请求体中的 JSON 数据解组到一个新的`Booking`结构中，该结构随后可以附加到表中，为了创建一个新的记录，我们使用下面的函数:

```
func createNewBooking(w http.ResponseWriter, r *http.Request) {
    // get the body of our POST request
    // return the string response containing the request body
    reqBody, _ := ioutil.ReadAll(r.Body) var booking Booking
    json.Unmarshal(reqBody, &booking)
    db.Create(&booking)    fmt.Println("Endpoint Hit: Creating New Booking")
    json.NewEncoder(w).Encode(booking)
}
```

*   **阅读**所有预订

再次将路线添加到在`handleRequests`功能中定义的路线列表中

```
myRouter.HandleFunc(“/all-bookings”, returnAllBookings)
```

创建一个新函数`returnAllBookings()`来获取我们使用的所有记录`db.Find(&bookings)`

```
func returnAllBookings(w http.ResponseWriter, r *http.Request){
 bookings := []Booking{}
 db.Find(&bookings)
 fmt.Println(“Endpoint Hit: returnAllBookings”)
 json.NewEncoder(w).Encode(bookings)
}
```

*   **通过 Id 读取预订详情**

为了完成这项工作，我们对所有预订运行一个循环，如果`booking.id`等于我们在 UTL 中传递的键，它返回编码为 JSON 的预订细节。

```
//handleRequests()
myRouter.HandleFunc("/booking/{id}", returnSingleBooking) func returnSingleBooking(w http.ResponseWriter, r *http.Request){
 vars := mux.Vars(r)
 key := vars[“id”]
 bookings := []Booking{}
 db.Find(&bookings) for _, booking := range bookings {
     // string to int
     s , err:= strconv.Atoi(key)
     if err == nil{
        if booking.Id == s {
           fmt.Println(booking)
           fmt.Println(“Endpoint Hit: Booking No:”,key)
           json.NewEncoder(w).Encode(booking)
        }
     }
  }
}
```

现在轮到您构建更新和删除操作了。如果你需要任何帮助，请在评论区提问。您也可以从以下链接获得帮助:

*   [http://jinzhu.me/gorm/crud.html](http://jinzhu.me/gorm/crud.html)
*   [https://gorm.io/docs/](https://gorm.io/docs/)

最后，我们完成了在 Golang 的第一个项目。希望你喜欢:)