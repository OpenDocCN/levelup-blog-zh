# 围棋自动测试

> 原文：<https://levelup.gitconnected.com/automatic-testing-in-go-ce581238eb57>

![](img/962663fcaa8e49df01f29d786d3906b6.png)

标志归功于 golang.org

## Go 元编程的进一步探索。其中，我们构建了 Golang 元编程工具。

这是上一篇文章的续篇，Go 中的[元程序，如果你还没有读过的话，可以从这里开始。在本文中，我们将关注为我们的 CRUD api 自动创建测试。回想一下，我们已经基于 PostgreSQL 表定义自动制作了 api 本身。在这个过程中，我们还将构建几个实用程序，使我们的元编程工具更加有用。](/metaprogram-in-go-5a2a7e989613)

从几行 SQL 表定义中自动创建数百行可编译的、可运行的、无错误的* (*不尽然，请继续阅读)代码让我们兴奋不已，我们就像是在寻找钉子的锤子。我们还能做什么元编程？很多，事实证明，但一切都需要时间。从哪里开始？

让我们为改进 [metaapi](https://github.com/exyzzy/metaapi) 设定四个目标。

1.  为我们的 metaapi 生成的 api 自动创建测试
2.  使用内部模板生成默认代码
3.  支持 metaapi 之外的代码生成
4.  自动创建初始 metaapi 项目

我们将更详细地讨论所有这些。

# 自动测试

许多项目缺少的一件事是测试代码。这里有一个问题——您应该为自动生成的代码构建测试吗？您可能会认为，如果生成器工作正常，那么它正在生成无错误的代码。对吗？

当然，您不能假设生成的代码是没有错误的，原因很简单，生成的代码是生成器的产品，而生成器本身仍然很容易出现人为错误。此外，生成器作者**有义务**提供自动测试，就好像他们在编写非元(正常？)节目。考虑以下几点:

*   自动测试成为一个契约和文档，记录了生成的代码被测试的程度。
*   用户可能会随着时间的推移修改/扩展生成的代码，并且在代码发生变化时需要进行测试。
*   生成的代码所在的上下文(其他代码)可能会以生成器作者从未考虑过的方式使用它。
*   覆盖率统计需要测试。
*   自动测试是对生成器本身的测试，因此应该作为构建生成器的过程来实现(我错了，事实上我确实发现了一些我不经常使用的 sql 类型的原始 metaapi 生成代码的问题)。
*   锦上添花:如果可以自动生成测试，为什么要强迫用户手动编写测试呢？

有了这些论据，让我们在 go 中进行一些自动测试。幸运的是，我们的 metaapi 程序为我们提供了几乎所有我们需要的跳板，使这变得容易。这是应该的——如果你能元编程一些东西 x.go，你也应该能元编程一些东西 x_test.go，而不需要太多额外的努力。我们可以保留所有原始的词法分析器和语法分析器。我们只需要为我们的测试文件创建一个新的模板，在 generate.go 中创建一些新的 receiver 方法，并集成这些东西。

作为复习，让我们首先看看我们将基于一个简单的 todo sql 表定义测试的自动生成的 metaapi api。

newtodo/todo.sql:

```
create table todos (
  id           integer generated always as identity primary key,
  updated_at   timestamptz,
  done         boolean,
  title        text
);
```

newtodo/todo_generated_api.go:

```
//Auto generated with MetaApi [https://github.com/exyzzy/metaapi](https://github.com/exyzzy/metaapi)
package mainimport (
  "database/sql"
  _ "github.com/lib/pq"
  "time"
)//Create Table
func CreateTableTodos(db *sql.DB) (err error) {
  _, err = db.Exec("DROP TABLE IF EXISTS todos CASCADE")
  if err != nil {
    return
  }
  _, err = db.Exec(`create table todos ( id integer generated always as identity primary key , updated_at timestamptz , done boolean , title text ) ; `)
  return
}//Drop Table
func DropTableTodos(db *sql.DB) (err error) {
  _, err = db.Exec("DROP TABLE IF EXISTS todos CASCADE")
  return
}//Struct
type Todo struct {
  Id int32`xml:"Id" json:"id"`
  UpdatedAt time.Time`xml:"UpdatedAt" json:"updatedat"`
  Done bool`xml:"Done" json:"done"`
  Title string`xml:"Title" json:"title"`}//Create
func (todo *Todo) CreateTodo(db *sql.DB) (result Todo, err error) {
  stmt, err := db.Prepare("INSERT INTO todos ( updated_at, done, title) VALUES ($1,$2,$3) RETURNING id, updated_at, done, title")
  if err != nil {
    return
  }
  defer stmt.Close()
  err = stmt.QueryRow( todo.UpdatedAt, todo.Done, todo.Title).Scan( &result.Id, &result.UpdatedAt, &result.Done, &result.Title)
  return
}//Retrieve
func (todo *Todo) RetrieveTodo(db *sql.DB) (result Todo, err error) {
  result = Todo{}
  err = db.QueryRow("SELECT id, updated_at, done, title FROM todos WHERE (id = $1)", todo.Id).Scan( &result.Id, &result.UpdatedAt, &result.Done, &result.Title)
  return
}//RetrieveAll
func RetrieveAllTodos(db *sql.DB) (todos []Todo, err error) {
  rows, err := db.Query("SELECT id, updated_at, done, title FROM todos ORDER BY id DESC")
  if err != nil {
    return
  }
  for rows.Next() {
    result := Todo{}
    if err = rows.Scan( &result.Id, &result.UpdatedAt, &result.Done, &result.Title); err != nil {
      return
    }
    todos = append(todos, result)
  }
  rows.Close()
  return
}//Update
func (todo *Todo) UpdateTodo(db *sql.DB) (result Todo, err error) {
  stmt, err := db.Prepare("UPDATE todos SET updated_at = $2, done = $3, title = $4 WHERE (id = $1) RETURNING id, updated_at, done, title")
  if err != nil {
    return
  }
  defer stmt.Close()err = stmt.QueryRow( todo.Id, todo.UpdatedAt, todo.Done, todo.Title).Scan( &result.Id, &result.UpdatedAt, &result.Done, &result.Title)
  return
}//Delete
func (todo *Todo) DeleteTodo(db *sql.DB) (err error) {
  stmt, err := db.Prepare("DELETE FROM todos WHERE (id = $1)")
  if err != nil {
    return
  }
  defer stmt.Close()_, err = stmt.Exec(todo.Id)
  return
}//DeleteAll
func DeleteAllTodos(db *sql.DB) (err error) {
  stmt, err := db.Prepare("DELETE FROM todos")
  if err != nil {
    return
  }
  defer stmt.Close()_, err = stmt.Exec()
  return
}
```

我们将采用与构建 api 生成器相同的方式来创建 api 测试函数文件，也就是说，我们将从一个已知良好的 go 测试文件开始，并慢慢地将其转换为模板。因此，第一项工作是为上面生成的 CRUD api 构建一个工作测试文件。让我们先这么做，记住我们的目标是在完成后使用 metaapi 自动生成**这个完全相同的测试文件**。

newtodo/api_test.go:

```
package mainimport (
  "database/sql"
  "encoding/json"
  "fmt"
  "log"
  "os"
  "reflect"
  "strings"
  "testing"
  "time"
)var testDb *sql.DB
var configdb map[string]interface{}const testDbName = "testtodo"// ======= helpers//assumes a configlocaldb.json file as:
//{
//    "Host": "localhost",
//    "Port": "5432",
//    "User": "dbname",
//    "Pass": "dbname",
//    "Name": "dbname",
//    "SSLMode": "disable"
//}
func loadConfig() {
  fmt.Println("  loadConfig")
  file, err := os.Open("configlocaldb.json")
  if err != nil {
    log.Fatalln("Cannot open configlocaldb file", err)
  }
  decoder := json.NewDecoder(file)
  err = decoder.Decode(&configdb)
  if err != nil {
    log.Fatalln("Cannot get local configurationdb from file", err)
  }
}func createDb(db *sql.DB, dbName string, owner string) (err error) {
  ss := fmt.Sprintf("CREATE DATABASE %s OWNER %s", dbName, owner)
  fmt.Println("  " + ss)
  _, err = db.Exec(ss)
  return
}func setTzDb(db *sql.DB) (err error) {
  ss := fmt.Sprintf("SET TIME ZONE UTC")
  fmt.Println("  " + ss)
  _, err = db.Exec(ss)
  return
}func dropDb(db *sql.DB, dbName string) (err error) {
  ss := fmt.Sprintf("DROP DATABASE %s", dbName)
  fmt.Println("  " + ss)
  _, err = db.Exec(ss)
  return
}func rowExists(db *sql.DB, query string, args ...interface{}) (exists bool, err error) {
  query = fmt.Sprintf("SELECT EXISTS (%s)", query)
  fmt.Println("  " + query)
  err = db.QueryRow(query, args...).Scan(&exists)
  return
}func tableExists(db *sql.DB, table string) (valid bool, err error) { valid, err = rowExists(db, "SELECT 1 FROM pg_tables WHERE tablename = $1", table)
  return
}func initTestDb() (err error) {
  loadConfig()
  psqlInfo := fmt.Sprintf("host=%s port=%s user=%s password=%s "+
    "sslmode=%s", configdb["Host"], configdb["Port"], configdb["User"], configdb["Pass"], configdb["SSLMode"])
  testDb, err = sql.Open("postgres", psqlInfo)
  return
}func TestMain(m *testing.M) {
  //test setup
  err := initTestDb()
  if err != nil {
    log.Panicf("cannot initTestDb " + err.Error())
  } err = createDb(testDb, testDbName, configdb["User"].(string))
  if err != nil {
    log.Panicf("cannot CreateDb " + err.Error())
  } err = setTzDb(testDb)
  if err != nil {
    log.Panicf("cannot setTzDb " + err.Error())
  } //run tests
  exitVal := m.Run() //test teardown
  err = dropDb(testDb, testDbName)
  if err != nil {
    log.Panicf("cannot DropDb " + err.Error())
  }
  os.Exit(exitVal)
}type compareType func(interface{}, interface{}) boolfunc noCompare(result, expect interface{}) bool {
  fmt.Printf("noCompare: %v, %v -  %T, %T \n", result, expect, result, expect)
  return (true)
}func defaultCompare(result, expect interface{}) bool {
  fmt.Printf("defaultCompare: %v, %v -  %T, %T \n", result, expect, result, expect)
  return (result == expect)
}func jsonCompare(result, expect interface{}) bool {
  fmt.Printf("jsonCompare: %v, %v -  %T, %T \n", result, expect, result, expect) //json fields can be any order after db return, 
  //so read into   map[string]interface and look up
  resultMap := make(map[string]interface{})
  err := json.Unmarshal([]byte(result.(string)), &resultMap)
  if err != nil {
    log.Panic(err)
  }
  expectMap := make(map[string]interface{})
  err = json.Unmarshal([]byte(expect.(string)), &expectMap)
  if err != nil {
    log.Panic(err)
  }for k, v := range expectMap {
    if v != resultMap[k] {
      fmt.Printf("Key: %v, Result: %v, Expect: %v", k, resultMap[k], v)
      return false
    }
  }
  return true
}func stringNotEqual(result, expect []byte) bool { return (strings.TrimSpace(string(result)) != strings.TrimSpace(string(expect)))}func stringCompare(result, expect interface{}) bool {resultJson, err := json.Marshal(result)
  if err != nil {
    log.Panic(err)
  }
  expectJson, err := json.Marshal(expect)
  if err != nil {
    log.Panic(err)
  }
  fmt.Printf("stringCompare: %v, %v -  %T, %T \n", string(resultJson), string(expectJson), result, expect)
  return (strings.TrimSpace(string(resultJson)) == strings.TrimSpace(string(expectJson)))
}//iterate through each field of struct and apply the 
//compare function to each field based on compareType map
func equalField(result, expect interface{}, compMap map[string]compareType) error { u := reflect.ValueOf(expect)
  v := reflect.ValueOf(result)
  typeOfS := u.Type() for i := 0; i < u.NumField(); i++ { if !(compMap[typeOfS.Field(i).Name])(v.Field(i).Interface(), u.Field(i).Interface()) {
      return fmt.Errorf("Field: %s, Result: %v, Expect: %v", typeOfS.Field(i).Name, v.Field(i).Interface(), u.Field(i).Interface())
    }
  }
  return nil
}//table specificconst todostableName = "todos"//test data - note: double brackets in test data need space 
//between otherwise are interpreted as template actionvar testTodo = [2]Todo{ {1, time.Now().UTC().Truncate(time.Microsecond), false, "TaoLVzKbOmA7o6XG"}, {2, time.Now().UTC().Truncate(time.Microsecond), false, "mkty9P5syMWIFQHs"} }var updateTodo = Todo{1, time.Now().UTC().Truncate(time.Microsecond), false, "gtJYE5QGUfrlzMzw"}//compare functions
var compareTodos = map[string]compareType{
  "Id":        defaultCompare,
  "UpdatedAt": stringCompare,
  "Done":      defaultCompare,
  "Title":     defaultCompare,
}// ======= tests: Todofunc reverseTodos(todos []Todo) (result []Todo) { for i := len(todos) - 1; i >= 0; i-- {
    result = append(result, todos[i])
  }
  return
}func TestCreateTableTodos(t *testing.T) {
  fmt.Println("==CreateTableTodos") err := CreateTableTodos(testDb)
  if err != nil {
    t.Errorf("cannot CreateTableTodos " + err.Error())
  } else {
    fmt.Println("  Done: CreateTableTodos")
  }
  exists, err := tableExists(testDb, todostableName)
  if err != nil {
    t.Errorf("cannot tableExists " + err.Error())
  }
  if !exists {
    t.Errorf("tableExists(todos) returned wrong status code: got %v want %v", exists, true)
  } else {
    fmt.Println("  Done: tableExists")
  }
}func TestCreateTodo(t *testing.T) {
  fmt.Println("==CreateTodo") result, err := testTodo[0].CreateTodo(testDb)
  if err != nil {
    t.Errorf("cannot CreateTodo " + err.Error())
  } else {
    fmt.Println("  Done: CreateTodo")
  } err = equalField(result, testTodo[0], compareTodos)
  if err != nil {
    t.Errorf("api returned unexpected result. " + err.Error())
  }
}func TestRetrieveTodo(t *testing.T) {
  fmt.Println("==RetrieveTodo") result, err := testTodo[0].RetrieveTodo(testDb)
  if err != nil {
    t.Errorf("cannot RetrieveTodo " + err.Error())
  } else {
    fmt.Println("  Done: RetrieveTodo")
  }
  err = equalField(result, testTodo[0], compareTodos)
  if err != nil {
    t.Errorf("api returned unexpected result. " + err.Error())
  }
}func TestRetrieveAllTodos(t *testing.T) {
  fmt.Println("==RetrieveAllTodos") _, err := testTodo[1].CreateTodo(testDb)
  if err != nil {
    t.Errorf("cannot CreateTodo " + err.Error())
  } else {
    fmt.Println("  Done: CreateTodo")
  }
  result, err := RetrieveAllTodos(testDb)
  if err != nil {
    t.Errorf("cannot RetrieveAllTodos " + err.Error())
  } else {
    fmt.Println("  Done: RetrieveAllTodos")
  }//reverse because api is DESC, [:] is slice of all array elements
  expect := reverseTodos(testTodo[:])
  for i, _ := range expect {
    err = equalField(result[i], expect[i], compareTodos)
    if err != nil {
      t.Errorf("api returned unexpected result. " + err.Error())
    }
  }
}func TestUpdateTodo(t *testing.T) {
  fmt.Println("==UpdateTodo") result, err := updateTodo.UpdateTodo(testDb)
  if err != nil {
    t.Errorf("cannot UpdateTodo " + err.Error())
  } else {
    fmt.Println("  Done: UpdateTodo")
  }
  err = equalField(result, updateTodo, compareTodos)
  if err != nil {
    t.Errorf("api returned unexpected result. " + err.Error())
  }
}func TestDeleteTodo(t *testing.T) {
  fmt.Println("==DeleteTodo") err := testTodo[0].DeleteTodo(testDb)
  if err != nil {
    t.Errorf("cannot DeleteTodo " + err.Error())
  } else {
    fmt.Println("  Done: DeleteTodo")
  }
  _, err = testTodo[0].RetrieveTodo(testDb)
  if err == nil {
    t.Errorf("api returned unexpected result: got Row want NoRow")
  } else {
    if err == sql.ErrNoRows {
      fmt.Println("  Done: RetrieveTodo with no result")
    } else {
      t.Errorf("cannot RetrieveTodo " + err.Error())
    }
  }
}func TestDeleteAllTodos(t *testing.T) {
  fmt.Println("==DeleteAllTodos") err := DeleteAllTodos(testDb)
  if err != nil {
    t.Errorf("cannot DeleteAllTodos " + err.Error())
  } else {
    fmt.Println("  Done: DeleteAllTodos")
  }
  result, err := RetrieveAllTodos(testDb) if err != nil {
    t.Errorf("cannot RetrieveAllTodos " + err.Error())
  }
  if len(result) > 0 {
    t.Errorf("api returned unexpected result: got Row want NoRow")
  } else {
    fmt.Println("  Done: RetrieveAllTodos with no result")
  }
}
```

在这个测试文件中有一些事情需要注意。

**一个新的测试数据库在测试开始时被创建。**这有许多原因:

1.  为了防止生产数据库中的污染
2.  无需保存即可知道测试数据序列主键 id
3.  测试创建表功能

**JSON 测试数据可以解释为 go 模板动作**。如果您创建 JSON 测试数据，例如，一个结构数组，并且使用默认的 go 模板操作分隔符，那么您可能会得到一个字符模式{{}，{}，..{ } }编译器会将其解释为一个[模板动作](https://golang.org/pkg/text/template/)。修复很容易，只需在任何双括号之间加一个空格: **{ {** }，{}，..{ **} }。**这在 txt 模板中变得有点棘手，因为在我们不想要的地方旁边会有我们想要的双括号解释为模板动作的地方，它们之间的区别是一个空格字符——这是在乞求错误。一个更好的解决方案是改变 go 用来标识模板动作的分隔符。我们可以通过使用[模板来做到这一点。generate.go 中的 Delims()](https://golang.org/pkg/text/template/#Template.Delims) 将分隔符从{{}}更改为< < > >。如果我们的模板是 html，这可能会有问题，但是因为它们是 go，所以会更好。

**输入 PostgreSQL 的数据可能会被修改后返回**:这可能会搞砸你的测试，除非你做好准备。这里有几个例子。[这个有趣的问题](https://github.com/lib/pq/issues/227)在 [Go(纳秒)](https://golang.org/pkg/time/)和 [Postgres(微秒)](https://www.postgresql.org/docs/12/datatype-datetime.html)之间的时间分辨率是不同的。这在 linux 上似乎是个问题，但在 OS X 上不是，但可能会影响您的本地测试。解决方案是将时间截断到最低(普通)分辨率，以便通过时间测试:

`time.Now().UTC().Truncate(time.Microsecond)`

另一个是时间格式化，其中 pq 将使用一个[时间。timestamp 和 date 的 Time](https://golang.org/pkg/time/) 结构，因此它们可以带着时区进入 PG，然后不带时区返回。一种解决方案是将它们整理成字符串，然后比较这些字符串。

还有一点是，JSON 结构键/字段[从 PG 返回时可以是任何顺序](https://stackoverflow.com/questions/36698065/ordering-difference-in-json-marshaled-from-map-and-struct)，这对于 JSON 来说是正常的[。所以我们需要遍历每个键，并在目标中查找以进行比较。](https://www.ietf.org/rfc/rfc4627.txt)

由于这些问题，测试文件在每个表上使用一个映射，允许测试函数为每个字段调用一个唯一的比较函数。我们将在 generate.go 中生成这个映射，它可以在“编译时”访问 sql 表数据。

```
//compare functionsvar compareTodos = map[string]compareType{
  "Id":        defaultCompare,
  "UpdatedAt": stringCompare,
  "Done":      defaultCompare,
  "Title":     defaultCompare,
}
```

如果我们运行`go test`，它看起来确实与我们的 api 相违背，所以现在我们将复制我们的 go 测试文件为 api_test.txt，并开始对它进行模板化，同时在需要的地方添加新的 receiver 方法来生成. go。

metaapi/metasql/api_test.txt:

```
//Auto generated with MetaApi [https://github.com/exyzzy/metaapi](https://github.com/exyzzy/metaapi)
package << .Package >>import (
  "database/sql"
  "encoding/json"
  "fmt"
  "log"
  "os"
  "reflect"
  "strings"
  "testing"
  "time"
)var testDb *sql.DB
var configdb map[string]interface{}
const testDbName = "test<< .FilePrefix >>"// ======= helpers//assumes a configlocaldb.json file as:
//{
//    "Host": "localhost",
//    "Port": "5432",
//    "User": "dbname",
//    "Pass": "dbname",
//    "Name": "dbname",
//    "SSLMode": "disable"
//}
func loadConfig() {
  fmt.Println("  loadConfig")
  file, err := os.Open("configlocaldb.json")
  if err != nil {
    log.Panicln("Cannot open configlocaldb file", err.Error())
  }
  decoder := json.NewDecoder(file)
  err = decoder.Decode(&configdb)
  if err != nil {
    log.Panicln("Cannot get local configurationdb from file", err.Error())
  }
}func createDb(db *sql.DB, dbName string, owner string) (err error) {
  ss := fmt.Sprintf("CREATE DATABASE %s OWNER %s", dbName, owner)
  fmt.Println("  " + ss)
  _, err = db.Exec(ss)
  return
}func setTzDb(db *sql.DB) (err error) {
  ss := fmt.Sprintf("SET TIME ZONE UTC")
  fmt.Println("  " + ss)
  _, err = db.Exec(ss)
  return
}func dropDb(db *sql.DB, dbName string) (err error) {
  ss := fmt.Sprintf("DROP DATABASE %s", dbName)
  fmt.Println("  " + ss)
  _, err = db.Exec(ss)
  return
}func rowExists(db *sql.DB, query string, args ...interface{}) (exists bool, err error) {
  query = fmt.Sprintf("SELECT EXISTS (%s)", query)
  fmt.Println("  " + query)
  err = db.QueryRow(query, args...).Scan(&exists)
  return
}func tableExists(db *sql.DB, table string) (valid bool, err error) { valid, err = rowExists(db, "SELECT 1 FROM pg_tables WHERE tablename = $1", table)
  return
}func initTestDb() (err error) {
  loadConfig()
  psqlInfo := fmt.Sprintf("host=%s port=%s user=%s password=%s "+
    "sslmode=%s", configdb["Host"], configdb["Port"], configdb["User"], configdb["Pass"], configdb["SSLMode"])
  testDb, err = sql.Open("postgres", psqlInfo)
  return
}func TestMain(m *testing.M) {
  //test setup
  err := initTestDb()
  if err != nil {
    log.Panicln("cannot initTestDb ", err.Error())
  } err = createDb(testDb, testDbName, configdb["User"].(string))
  if err != nil {
    log.Panicln("cannot CreateDb ", err.Error())
  } err = setTzDb(testDb)
  if err != nil {
    log.Panicln("cannot setTzDb ", err.Error())
  } //run tests
  exitVal := m.Run() //test teardown
  err = dropDb(testDb, testDbName)
  if err != nil {
    log.Panicln("cannot DropDb ", err.Error())
  }
  os.Exit(exitVal)
}type compareType func(interface{}, interface{}) boolfunc noCompare(result, expect interface{}) bool {
  fmt.Printf("noCompare: %v, %v -  %T, %T \n", result, expect, result, expect)
  return (true)
}func defaultCompare(result, expect interface{}) bool {
  fmt.Printf("defaultCompare: %v, %v -  %T, %T \n", result, expect, result, expect)
  return (result == expect)
}func jsonCompare(result, expect interface{}) bool {
  fmt.Printf("jsonCompare: %v, %v -  %T, %T \n", result, expect, result, expect) //json fields can be any order after db return, 
  //so read into map[string]interface and look up
  resultMap := make(map[string]interface{})
  err := json.Unmarshal([]byte(result.(string)), &resultMap)
  if err != nil {
    log.Panic(err)
  }
  expectMap := make(map[string]interface{})
  err = json.Unmarshal([]byte(expect.(string)), &expectMap)
  if err != nil {
    log.Panic(err)
  } for k, v := range expectMap {
    if v != resultMap[k] {
      fmt.Printf("Key: %v, Result: %v, Expect: %v", k, resultMap[k], v)
      return false
    }
  }
  return true
}func stringCompare(result, expect interface{}) bool { resultJson, err := json.Marshal(result)
  if err != nil {
    log.Panic(err)
  }
  expectJson, err := json.Marshal(expect)
  if err != nil {
    log.Panic(err)
  }
  fmt.Printf("stringCompare: %v, %v -  %T, %T \n", string(resultJson), string(expectJson), result, expect)
  return (strings.TrimSpace(string(resultJson)) == strings.TrimSpace(string(expectJson)))
}//iterate through each field of struct and apply the 
//compare function to each field based on compareType map
func equalField(result, expect interface{}, compMap map[string]compareType) error { u := reflect.ValueOf(expect)
  v := reflect.ValueOf(result)
  typeOfS := u.Type() for i := 0; i < u.NumField(); i++ { if !(compMap[typeOfS.Field(i).Name])(v.Field(i).Interface(), u.Field(i).Interface()) {
      return fmt.Errorf("Field: %s, Result: %v, Expect: %v", typeOfS.Field(i).Name, v.Field(i).Interface(), u.Field(i).Interface())
    }
  }
  return nil
}//table specific<< range $index, $table := .Tables >>
const << $table.Name >>tableName = "<< $table.Name >>"var test<< $table.CapSingName >> = [2]<< $table.CapSingName >>{ << $table.TestData 1 >>, << $table.TestData 2 >> }var update<< $table.CapSingName >> = << $table.CapSingName >><< $table.TestData 1 >>//compare functions
var compare<< $table.CapName >> = map[string]compareType{
<< $table.CompareMapFields >>
}// ======= tests: << $table.CapSingName >>func reverse<< $table.CapName >>(<< $table.Name >> []<< $table.CapSingName >>) (result []<< $table.CapSingName >>) {for i := len(<< $table.Name >>) - 1; i >= 0; i-- {
    result = append(result, << $table.Name >>[i])
  }
  return
}func TestCreateTable<< $table.CapName >>(t *testing.T) {
  fmt.Println("==CreateTable<< $table.CapName >>") err := CreateTable<< $table.CapName >>(testDb)
  if err != nil {
    t.Errorf("cannot CreateTable<< $table.CapName >> " + err.Error())
  } else {
    fmt.Println("  Done: CreateTable<< $table.CapName >>")
  }
  exists, err := tableExists(testDb, << $table.Name >>tableName)
  if err != nil {
    t.Errorf("cannot tableExists " + err.Error())
  }
  if !exists {
    t.Errorf("tableExists(<< $table.Name >>) returned wrong status code: got %v want %v", exists, true)
  } else {
    fmt.Println("  Done: tableExists")
  }
}func TestCreate<< $table.CapSingName >>(t *testing.T) {
  fmt.Println("==Create<< $table.CapSingName >>") result, err := test<< $table.CapSingName >>[0].Create<< $table.CapSingName >>(testDb)
  if err != nil {
    t.Errorf("cannot Create<< $table.CapSingName >> " + err.Error())
  } else {
    fmt.Println("  Done: Create<< $table.CapSingName >>")
  } err = equalField(result, test<< $table.CapSingName >>[0], compare<< $table.CapName >>)
  if err != nil {
    t.Errorf("api returned unexpected result. " + err.Error())
  }
}func TestRetrieve<< $table.CapSingName >>(t *testing.T) {
  fmt.Println("==Retrieve<< $table.CapSingName >>") result, err := test<< $table.CapSingName >>[0].Retrieve<< $table.CapSingName >>(testDb)
  if err != nil {
    t.Errorf("cannot Retrieve<< $table.CapSingName >> " + err.Error())
  } else {
    fmt.Println("  Done: Retrieve<< $table.CapSingName >>")
  }
  err = equalField(result, test<< $table.CapSingName >>[0], compare<< $table.CapName >>)
  if err != nil {
    t.Errorf("api returned unexpected result. " + err.Error())
  }
}func TestRetrieveAll<< $table.CapName >>(t *testing.T) {
  fmt.Println("==RetrieveAll<< $table.CapName >>") _, err := test<< $table.CapSingName >>[1].Create<< $table.CapSingName >>(testDb)
  if err != nil {
    t.Errorf("cannot Create<< $table.CapSingName >> " + err.Error())
  } else {
    fmt.Println("  Done: Create<< $table.CapSingName >>")
  }
  result, err := RetrieveAll<< $table.CapName >>(testDb)
  if err != nil {
    t.Errorf("cannot RetrieveAll<< $table.CapName >> " + err.Error())
  } else {
    fmt.Println("  Done: RetrieveAll<< $table.CapName >>")
  }//reverse because api is DESC, [:] is slice of all array elements
  expect := reverse<< $table.CapName >>(test<< $table.CapSingName >>[:])
  for i, _ := range expect {
    err = equalField(result[i], expect[i], compare<< $table.CapName >>)
    if err != nil {
      t.Errorf("api returned unexpected result. " + err.Error())
    }
  }
}func TestUpdate<< $table.CapSingName >>(t *testing.T) {
  fmt.Println("==Update<< $table.CapSingName >>") result, err := update<< $table.CapSingName >>.Update<< $table.CapSingName >>(testDb)
  if err != nil {
    t.Errorf("cannot Update<< $table.CapSingName >> " + err.Error())
  } else {
    fmt.Println("  Done: Update<< $table.CapSingName >>")
  }
  err = equalField(result, update<< $table.CapSingName >>, compare<< $table.CapName >>)
  if err != nil {
    t.Errorf("api returned unexpected result. " + err.Error())
  }
}func TestDelete<< $table.CapSingName >>(t *testing.T) {
  fmt.Println("==Delete<< $table.CapSingName >>") err := test<< $table.CapSingName >>[0].Delete<< $table.CapSingName >>(testDb)
  if err != nil {
    t.Errorf("cannot Delete<< $table.CapSingName >> " + err.Error())
  } else {
    fmt.Println("  Done: Delete<< $table.CapSingName >>")
  }
  _, err = test<< $table.CapSingName >>[0].Retrieve<< $table.CapSingName >>(testDb)
  if err == nil {
    t.Errorf("api returned unexpected result: got Row want NoRow")
  } else {
    if err == sql.ErrNoRows {
      fmt.Println("  Done: Retrieve<< $table.CapSingName >> with no result")
    } else {
      t.Errorf("cannot Retrieve<< $table.CapSingName >> " + err.Error())
    }
  }
}func TestDeleteAll<< $table.CapName >>(t *testing.T) {
  fmt.Println("==DeleteAll<< $table.CapName >>") err := DeleteAll<< $table.CapName >>(testDb)
  if err != nil {
    t.Errorf("cannot DeleteAll<< $table.CapName >> " + err.Error())
  } else {
    fmt.Println("  Done: DeleteAll<< $table.CapName >>")
  }
  result, err := RetrieveAll<< $table.CapName >>(testDb) if err != nil {
    t.Errorf("cannot RetrieveAll<< $table.CapName >> " + err.Error())
  }
  if len(result) > 0 {
    t.Errorf("api returned unexpected result: got Row want NoRow")
  } else {
    fmt.Println("  Done: RetrieveAll<< $table.CapName >> with no result")
  }
}
<< end >>
```

你会注意到事情没有太大的变化，直到我们到达这条线`<< range $index, $table := .Tables >>`,在这条线之前，主要是帮助函数，它们对所有的表都是一样的。在那一行之后，我们创建特定于表的函数，generate.go 需要访问我们从。sql 文件。

在这条线的正下方还有几个有趣的部分。`<< $table.TestData 1 >>`是我们创建测试数据的地方，测试数据由 go struct 字段驱动，而 go struct 字段又对应于。表中的 sql 字段。我们传入我们知道 PostgreSQL 将生成的主键 id。`<< $table.CompareMapFields >>`是我们生成上面提到的比较函数映射的地方，因此我们可以根据表字段类型使用唯一的比较函数。

我在第一篇文章里说过，但是值得记住过程。我们的模板化的 txt 文件，就像上面的 api_test.txt，看起来非常不可读。然而，这不是一个真正的问题，因为我们没有把它开发成一个 txt 文件。我们首先编写一个很好的完全可行的版本，我们编译它，迭代它，清理它，重构它，测试它。然后，当我们都完成了它，我们把它变成一个丑陋的 txt 文件，以便我们可以在以后重建它的参数。大部分工作发生在创建初始 go 文件和向 generate.go 添加接收器方法的过程中，将 go 文件转换为模板化的 txt 文件很快，如果基本 go 代码发生重大变化，我们可以很容易地重复多次。

```
<< range $index, $table := .Tables >>
const << $table.Name >>tableName = "<< $table.Name >>"var test<< $table.CapSingName >> = [2]<< $table.CapSingName >>{ << $table.TestData 1 >>, << $table.TestData 2 >> }var update<< $table.CapSingName >> = << $table.CapSingName >><< $table.TestData 1 >>//compare functions
var compare<< $table.CapName >> = map[string]compareType{
<< $table.CompareMapFields >>
}
```

我们需要添加到原始 generate.go 中的惟一东西是为任何 sql 数据类型创建测试数据的能力。我们现在的简单测试策略是将数据保存到 PostgreSQL 数据库中，然后确保同样的东西再次返回。对于 metaapi 支持的 27 种不同的 sql 数据类型，这意味着大量的测试生成函数。只需要三个比较函数:defaultCompare、stringCompare 和 jsonCompare —您可以在上面的 api_test.txt 中找到这些函数。

**在 metapi/metasql/generate.go 中增加了**:

```
..abridged//TEST SPECIFICtype GenerateFunc func(int, int) stringtype testFuncs struct {
  GenerateData GenerateFunc
  CompareData  string
}var dataMap = map[string]testFuncs{
  "BOOLEAN":     {boolTestData, "defaultCompare"},
  "BOOL":        {boolTestData, "defaultCompare"},
  "CHARID":      {stringTestData, "defaultCompare"}, 
  "VARCHARID":   {stringTestData, "defaultCompare"},
  "TEXT":        {stringTestData, "defaultCompare"},
  "SMALLINT":    {int16TestData, "defaultCompare"},
  "INT":         {int32TestData, "defaultCompare"},
  "INTEGER":     {int32TestData, "defaultCompare"},
  "BIGINT":      {int32TestData, "defaultCompare"},
  "SMALLSERIAL": {serialTestData, "defaultCompare"},
  "SERIAL":      {serialTestData, "defaultCompare"},
  "BIGSERIAL":   {serialTestData, "defaultCompare"},
  "FLOATID":     {float64TestData, "defaultCompare"}, 
  "REAL":        {float32TestData, "defaultCompare"},
  "FLOAT8":      {float32TestData, "defaultCompare"},
  "DECIMAL":     {float64TestData, "defaultCompare"},
  "NUMERIC":     {float64TestData, "defaultCompare"},
  "NUMERICID":   {float64TestData, "defaultCompare"}, 
  "PRECISION":   {float64TestData, "defaultCompare"}, 
  "DATE":        {dateTestData, "stringCompare"},     
  "TIME":        {timeTestData, "stringCompare"},
  "TIMESTAMPTZ": {timestampTestData, "stringCompare"},
  "TIMESTAMP":   {timestampTestData, "stringCompare"},
  "INTERVAL":    {durationTestData, "defaultCompare"},
  "JSON":        {jsonTestData, "jsonCompare"}, 
  "JSONB":       {jsonbTestData, "jsonCompare"},
  "UUID":        {uuidTestData, "defaultCompare"},
}func (table Table) CompareMapFields() string {
  var s string for _, column := range table.Columns {
    s += "\t\"" + camelize(column.Name) + "\": "
    s += dataMap[column.Type].CompareData + ",\n"
  }
  return s
}func (table Table) TestData(dataid int) string {
  rand.Seed(time.Now().UnixNano()) var s string s = "{"
  for columnid, column := range table.Columns {
    s += " " + dataMap[column.Type].GenerateData(dataid, columnid)
    s += comma(columnid, len(table.Columns))
  }
  s += "}"
  return s
}func boolTestData(dataid int, columnid int) string {
  return (strconv.FormatBool(rand.Intn(2) != 0))
}
func stringTestData(dataid int, columnid int) string {
  return ("\"" + randString(16) + "\"")
}
func int16TestData(dataid int, columnid int) string {
  if columnid == 0 { //assume serial
    return (strconv.FormatInt(int64(dataid), 10))
  } else {
    return (strconv.FormatInt(int64(rand.Intn(32767)), 10))
  }
}
func int32TestData(dataid int, columnid int) string {
  if columnid == 0 { //assume serial
    return (strconv.FormatInt(int64(dataid), 10))
  } else {
    return (strconv.FormatInt(int64(rand.Int31()), 10))
  }
}
func int64TestData(dataid int, columnid int) string {
  if columnid == 0 { //assume serial
    return (strconv.FormatInt(int64(dataid), 10))
  } else {
    return (strconv.FormatInt(rand.Int63(), 10))
  }
}
func serialTestData(dataid int, columnid int) string {
  return strconv.Itoa(dataid)
}
func float64TestData(dataid int, columnid int) string {
  return (strconv.FormatFloat(rand.NormFloat64(), 'f', -1, 64))
}
func float32TestData(dataid int, columnid int) string {
  return (strconv.FormatFloat(float64(rand.Float32()), 'f', -1, 32))
}
func timeTestData(dataid int, columnid int) string {
  return "time.Date(0000, time.January, 1, time.Now().UTC().Hour(), time.Now().UTC().Minute(), time.Now().UTC().Second(), time.Now().UTC().Nanosecond(), time.UTC)"
}
func timestampTestData(dataid int, columnid int) string {
  return "time.Now().UTC().Truncate(time.Microsecond)"
}
func durationTestData(dataid int, columnid int) string {
  return "\"12:34:45\""
}
func dateTestData(dataid int, columnid int) string {
  return "time.Now().UTC().Truncate(time.Hour * 24)"
}
func jsonTestData(dataid int, columnid int) string {
  return randJson()
}
func jsonbTestData(dataid int, columnid int) string {
  return randJson()
}
func uuidTestData(dataid int, columnid int) string {
  return "\"" + randUUID() + "\""
}func randString(length int) string {
  const charset = "abcdefghijklmnopqrstuvwxyz" +
    "ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789" b := make([]byte, length)
  for i := range b {
    b[i] = charset[rand.Intn(len(charset))]
  }
  return string(b)
}func randJson() string {
  return "\"{\\\"name\\\": \\\"" + randString(16) + "\\\", \\\"age\\\": " + strconv.FormatInt(int64(rand.Int31()), 10) + ", \\\"city\\\": \\\"" + randString(20) + "\\\"}\""
}func randUUID() (uuid string) {
  u := new([16]byte)
  _, err := rand.Read(u[:])
  if err != nil {
    log.Panicln("Cannot generate UUID", err.Error())
  }
  u[8] = (u[8] | 0x40) & 0x7F
  u[6] = (u[6] & 0xF) | (0x4 << 4)
  uuid = fmt.Sprintf("%x-%x-%x-%x-%x", u[0:4], u[4:6], u[6:8], u[8:10], u[10:])
  return
}
```

现在，当我们使用 todo.sql 表定义在 go generate 语句中运行新版本的 metaapi 时，它将创建 todo_api.go 和 todo_api_test.go

newtodo/todo.go:

```
//go:generate  metaapi -sql=todo.sql  -txt=api.txt
//go:generate  metaapi -sql=todo.sql  -txt=api_test.txt
package main
```

毫无疑问，我们已经重建了原始的 api 文件和测试文件。它真的适用于任何 sql 类型吗？让我们尝试一个 sql 文件，它定义了几个表，包括所有 27 种类型:

metaapi/examples/alltypes.sql:

```
create table todos (
  id           integer generated always as identity primary key,
  updated_at   timestamptz,
  done         boolean,
  title        text
);create table allbools (
  id         serial primary key,
  abool      boolean,
  abool2     bool
);create table allchars (
  id         serial primary key,
  achar      char(16),
  avarchar   varchar(16),
  atext      text
);create table allints (
  id         serial primary key,
  asmallint  smallint,
  aint       int,
  aint2      integer,
  asmallser  smallserial,
  aser       serial,
  abigser    bigserial
);create table allfloats (
  id         serial primary key,
  afloat     float(53),
  areal      real,
  afloat8    float8,
  adecimal   decimal,
  anumeric   numeric,
  anumeric2  numeric(36,18),
  adouble    double precision
);create table alltimes (
  id         serial primary key,
  adate      date,
  atime      time,
  ats        timestamp,
  atsz       timestamptz,
  ainterval  interval
);create table alljsons (
  id         serial primary key,
  ajson      json,
  ajsonb     jsonb
);create table uuids (
  id         serial primary key,
  auuid      uuid
);
```

是的，它工作了。好吧，算是吧。我在 char()、float()和 numeric()上作弊，选择了我知道可以工作的参数，如果没有更好的测试数据生成函数，这些类型将无法处理任何任意参数。实现正确的测试数据生成功能仍然是待定的。

另一个需要做更多工作的领域是处理外键的通用测试策略。这不会影响 CRUD api 的生成，但是会影响测试。现在，metaapi 一次测试一个表，因此外键不会通过测试。让我们解决这个问题。例如:

metaapi/examples/todoref.sql

```
create table owners (
    id           integer generated always as identity primary key,
    name         text
);create table todos (
    id           integer generated always as identity primary key,
    updated_at   timestamptz,
    done         boolean,
    title        text,
    owner        integer references owners(id)
);
```

只要做一些小的修改，我们就可以改变我们的测试来允许整数外键。我不会在这里展示代码的变化，但你可以在回复中看到它们。我们的策略是:

1.  向列结构添加一个 bool 来跟踪外键字段(generate.go)
2.  当我们解析`REFERENCES table(field)` sql 语法(parse.go)时，添加一个函数来设置该字段
3.  更改我们的整数测试数据生成器，如果它们是外键，使用已知的 id(generator . go)
4.  更改我们的测试模板，在测试结束时以相反的顺序删除所有的表。

这些更改将保证我们的被引用表在外键需要它们时仍然存在，并且我们以正确的顺序展开所有的删除。如果您使用上面的 todoref.sql 创建一个新项目，生成 api/test 代码，并运行测试，您将看到所有测试都通过了。

# 内部模板

在这一点上，我们有了创建 api 的 metaapi，也测试了该 api，但是，我们要求用户将我们的模板(api.txt 和 api_test.txt)复制到他们的本地项目，以便运行 go generate。如果我们可以访问 metaapi 中的这些内容，这样用户就不必管理它们，这对用户来说会好得多。原来有这样一个应用程序！实际上，这是一个 CLI 工具，叫做 [go-bindata](https://github.com/jteeuwen/go-bindata) ，虽然已经几年没人碰它了，但它仍然非常好用。我们将使用 go-bindata 来压缩源文件，并将其转换成可以在 metaapi 中直接访问的资源。我们首先安装 go-bindata `go get -u github.com/jteeuwen/go-bindata/...`，然后我们将创建一个名为 data 的新目录，将我们的 txt 模板复制到该目录中，并在该目录上运行 go bindata。

```
cd metaapi/data
go-bindata .
```

这将创建一个新的文件 metaapi/data/bindata.go，它将我们的文件压缩到资源中，并提供一些很好的方法来与它们进行交互——所有这些都是自包含的，不需要更多的东西。对我们来说唯一重要的方法是 Asset()。我们只是交换了我们的借据。Readfile()，将包名从 main 改为 data，我们就可以开始读取我们自己的资产了。

```
 //for external templates, instead of data.Asset, use:
  // dat, err := ioutil.ReadFile("./" + txtFile)
  // if err != nil {
  //  return err
  // } dat, err := data.Asset(txtFile)
  if err != nil {
   return err
  }
```

不要忘记，每当你改变你的资产，在我们的例子中是 api.txt 和 api_test.txt，你必须再次在它们上面运行 go-bindata 并编译你的应用程序。所有的资产都存储在 bindata.go 中，这很好，因为这意味着用户不需要 go-bindata 工具，但是如果开发人员改变了资产，他们就需要了。

# 外部生成

现在我们已经切换到 api 模板的内部访问，我们不再有一个好的方式让用户轻松地定制和扩展模板和生成器。让我们把它加回去，让它变得更好一点。现在，metaapi 负责所有的词法分析、解析和代码生成。作为一个选项，我们希望让 metaapi 进行词法分析和解析，然后将其解析结果(sm 结构)传递给我们正在处理的任意程序。如果我们想使用自己的模板和生成器，这将改善我们的工作流，因为我们在对生成器进行更改时不需要重新编译 metaapi。

有很多方法可以让我们看到这一点。最初，您可能会尝试将 json 形式的 sm 数据从 metaapi 传输到您的项目中，类似于:

```
//go:generate  metaapi -sql=todo.sql -txt=api.txt | myproj
//this does not work
```

这里的问题是 go generate 不是一个 shell，只是一个原始命令，所以这不起作用——管道是一个 shell 函数。我们可以尝试在 go generate 中执行一个 shell 命令，但是将 shell 放入其中是不可移植的。您可以使用多行 go generate 命令，因此在一个命令中从 metaapi 保存到中间 statemachine.json 文件，然后在另一个命令中读入 myproj 将会有效..但这是一个多么丑陋的解决方案。最后，golang 的 net/rpc 包可以在两端添加几行新代码，然后通过过程调用传递 sm 数据。我尝试过这种方法，它很有效，但是对于按需启动 rpc 服务器来说似乎有点大材小用了。

让我们改为修复 go generate，我们可以用一个小的实用程序来完成，我们称它为“管道”。它的工作是执行任意数量的带有参数的命令，将一个命令的输出传递给下一个命令的输入。这是简单、干净和有效的。

管道/干管. go:

```
//pipe: use in go generate statements to pipe commands
//commands are executed left to right
//stdout from preceding is piped into stdin of next
//commands are separated by ::
//format is: pipe cmd0 arg0 arg1.. :: cmd1 arg0 arg1.. :: cmdn..
//use pipe -v (verbose) to print output from each command
package mainimport (
  "fmt"
  "io"
  "io/ioutil"
  "log"
  "os"
  "os/exec"
)func main() {
  if len(os.Args) == 1 {
    fmt.Println(" valid usage is:")
    fmt.Println("  //go generate pipe cmd0 arg0 arg1.. :: cmd1 arg0 arg1.. :: cmdn..")
    fmt.Println("  //go generate pipe -v cmd0 arg0 arg1.. :: cmd1 arg0 arg1.. :: cmdn..")
    os.Exit(1)
  }
  //parse command string into nxm slice of strings
  verbose := false
  startindex := 0
  if os.Args[1] == "-v" { //verbose
    verbose = true
    startindex = 1
  }
  var cmda [][]string
  for i := startindex; i < len(os.Args); i++ {
    if (i == startindex) || (os.Args[i] == "::") {
      cmda = append(cmda, []string{})
    } else {
      cmda[len(cmda)-1] = append(cmda[len(cmda)-1], os.Args[i])
    }
  } var out []byte
  //execute all commands hooking up outputs to inputs
  for i := 0; i < len(cmda); i++ {
    fmt.Println("Command: ", cmda[i][0], cmda[i][1:])
    cmd := exec.Command(cmda[i][0], cmda[i][1:]...)
    stderr, err := cmd.StderrPipe()
    if err != nil {
      log.Fatal(err)
    }
    stdout, err := cmd.StdoutPipe()
    if err != nil {
      log.Fatal(err)
    }
    if i > 0 {
      stdin, err := cmd.StdinPipe()
      if err != nil {
        log.Fatal(err)
      } go func() {
        defer stdin.Close()
        io.WriteString(stdin, string(out))
      }()
    }
    if err := cmd.Start(); err != nil {
      log.Fatal(err)
    }
    newout, _ := ioutil.ReadAll(stdout)
    if verbose {
      fmt.Println(string(newout))
    }
    errtxt, _ := ioutil.ReadAll(stderr)
    if string(errtxt) != "" {
      fmt.Printf("%s\n", errtxt)
    }
    if err := cmd.Wait(); err != nil {
      log.Fatal(err)
    }
    out = newout
  }
}
```

你可以像这样安装这个工具，注意，它被设计成在 go generate 中作为一个原始命令运行，而不是作为一个 shell 命令，shell 会干扰它。

```
go get github.com/exyzzy/pipe
go install $GOPATH/src/github.com/exyzzy/pipe
```

要使用 pipe，您只需将它作为第一件事包含在您的 go generate 语句中，其他应用程序和它们使用的任何标志用以下符号分隔:

```
//go generate pipe app1 -flag=app1flag :: app2 -flag=app2flag
```

因此，现在将执行 app1，它的任何 stdout 都将通过管道传入 app2 的 stdin，您可以将任意多的应用程序连接在一起。Pipe 还有一个-v 选项，用于使用详细模式，打印出正在执行的应用程序和参数。

我们还需要对 metaapi 进行一些更改，提供一个选项，通过 stdout 将 statemachine 数据的 json 版本传递给项目中的新生成器，后者将通过 stdin 接受它。

metaapi/main.go:

```
pipePtr := flag.Bool("pipe", false, "use piped generation")
flag.Parse() ..some code..if !*pipePtr {
  err := metasql.Generate(*sm, txtFile)
  if err != nil {
    log.Panic(err)
  }
} else {
  //send to stdio
  psm, err := json.Marshal(sm)
  if err != nil {
    log.Panic(err)
    return
  }
  fmt.Println(string(psm))
}
```

这允许我们现在在新项目中接收 sm 数据，并在那里编译生成器。

newtodo/main.go:

```
package mainimport (
  "flag"
  "log"
  "os"
  "strings"
)func main() {
  txtPtr := flag.String("txt", "api.txt", "go template as .txt file")
  pipePtr := flag.Bool("pipe", false, "use piped generation")
  flag.Parse()
  if *pipePtr { //set up for more code
    txtFile := strings.ToLower(*txtPtr)
    if (txtFile == "") || (!strings.HasSuffix(txtFile, ".txt")) {
      log.Panic("No .txt File")
    } sm, err := ReadSM()
    if err != nil {
      log.Panic(err)
    }
    err = Generate(*sm, txtFile)
    if err != nil {
      log.Panic(err)
    }
    os.Exit(0)
  }
    //more stuff...
}
```

newtodo/todo.go

```
//go:generate  pipe metaapi -pipe=true -sql=todo.sql  :: newtodo -pipe=true -txt=api.txt
//go:generate  pipe metaapi -pipe=true -sql=todo.sql  :: newtodo -pipe=true -txt=api_test.txt
//Note requires:
//      [https://github.com/exyzzy/metaapi](https://github.com/exyzzy/metaapi)
//      [https://github.com/exyzzy/pipe](https://github.com/exyzzy/pipe)package main//before first go test:
//createuser -P -d newtodo <pass: newtodo>
//createdb newtodo
```

现在，在使用 metaapi 的这个变体中，我们已经将 generate.go 和两个模板文件:api.txt 和 api_test.txt 移到了我们当前的项目 newtodo 中。这允许我们在当前项目中修改 txt 模板和 generate.go，并在 metaapi 中使用它们，而无需重新编译 metaapi。如果您正在开发一个带有自定义模板的项目，这是一个不错的选择。

# 自动创建项目

我们现在有很多关于 metaapi 和 pipe 的事情。即使有好的文档，我们的用户也可能很难设置和使用，所以让我们通过构建一个自动创建初始 metaapi 项目的小命令行实用程序来简化它。我们将使用上面已经使用过的相同技术，使用 go-bindata 创建内部资产文件，然后使用它们和 go 模板来参数化我们的模板。我们的目标是在一个 CLI 行中为内部或外部 metaapi 项目创建一个项目。

我们在这里要做的是将我们项目的所有相关文件复制到。根据需要模板化的 txt 文件。然后我们将使用 go-bindata 将它们转化为内部资产。最后，我们将在 main.go 中访问它们，并将它们作为模板来创建我们的项目文件。这是一个简单的模式，可以用来从模板化的 txt 文件自动生成目标文件。

metaproj/main.go:

```
package mainimport (
  "errors"
  "flag"
  "fmt"
  "io/ioutil"
  "log"
  "os"
  "path/filepath"
  "strings"
  "text/template" "github.com/exyzzy/metaproj/data"
)//This struct passed into all templates for generation
//also use receiver methods such as Project, below
type ProjData struct {
  ProjName string
  SqlFile  string
  ProjType string
}// Create a metaapi base project from a .sql table definition, example:
//  metaproj -proj=newtodo -sql=todo.sql -type=external  (or, for internal:  metaproj -proj=newtodo -sql=todo.sql )
//  cd newtodo
//  go generate
func main() {
  projPtr := flag.String("proj", "myproj", "project to create")
  sqlPtr := flag.String("sql", "", ".sql input file to parse")
  typePtr := flag.String("type", "internal", "project type to create") var p ProjData flag.Parse()
  if flag.NFlag() == 0 {
    fmt.Println(" valid usage is:")
    fmt.Println("  metproj -proj=yourproj -sql=yoursql.sql")
    fmt.Println("  metproj -proj=yourproj -sql=yoursql.sql -type=external")
    os.Exit(1)
  } p.ProjName = strings.ToLower(*projPtr)
  p.SqlFile = strings.ToLower(*sqlPtr)
  p.ProjType = strings.ToLower(*typePtr) if (p.SqlFile != "") && (!strings.HasSuffix(p.SqlFile, ".sql")) {
    log.Panic("Invalid .sql File")
  } err := createProj(&p)
  if err != nil {
    log.Panic(err)
  }
}func createProj(pp *ProjData) error {
  if (pp.ProjName == "") || (pp.SqlFile == "") {
    log.Panic(errors.New("Must have projName and sqlFile"))
  }var projTypes = map[string]int{"internal": 0, "external": 1}
  pIndex, ok := projTypes[pp.ProjType]
  if !ok {
    log.Panic(errors.New(fmt.Sprintf("Invald project type, use: %v", keys(projTypes))))
  }err := os.MkdirAll(pp.ProjName, os.FileMode(0755))
  if err != nil {
    return err
  }type FileList struct {
    Name       string
    IsGenerate bool //if false just copy the file with no template actions applied
  }var files = [][]FileList{{{"sqlname.txt", true}, {"configlocaldb.txt", true}},
    {{"sqlname2.txt", true}, {"configlocaldb.txt", true}, {"generate.txt", true}, {"main.txt", true}, {"api.txt", false}, {"api_test.txt", false}}} for _, f := range files[pIndex] {
    dat, err := data.Asset(f.Name)
    if err != nil {
      return err
    }
    if f.IsGenerate {
      err = generateFile(dat, pp, getDest(pp, f.Name))
      if err != nil {
        return err
      }
    } else {
      err = writeFile(dat, getDest(pp, f.Name))
    }
  }
  err = copyFile(pp, pp.SqlFile)
  return err
}func keys(ms map[string]int) []string {
  kys := make([]string, len(ms))
  i := 0
  for k := range ms {
    kys[i] = k
    i++
  }
  return kys
}func getDest(pp *ProjData, name string) string {
  var dest string
  switch name {
  case "sqlname.txt", "sqlname2.txt":
    dest = prefix(pp.SqlFile) + ".go"
  case "configlocaldb.txt":
    dest = "configlocaldb.json"
  case "generate.txt":
    dest = "generate.go"
  case "main.txt":
    dest = "main.go"
  case "api.txt":
    dest = "api.txt"
  case "api_test.txt":
    dest = "api_test.txt"
  }
  return filepath.Join(pp.ProjName, dest)
}func prefix(name string) string {
  dot := strings.Index(name, ".")
  if dot > 0 {
    return name[:dot]
  } else {
    return name
  }
}func generateFile(templatesrc []byte, data interface{}, dest string) error {
  tt := template.Must(template.New("file").Parse(string(templatesrc)))
  file, err := os.Create(dest)
  if err != nil {
    return err
  }
  err = tt.Execute(file, data)
  file.Close()
  return err
}func copyFile(pp *ProjData, namesrc string) error {
  dat, err := ioutil.ReadFile("./" + namesrc)
  if err != nil {
    return err
  }
  err = ioutil.WriteFile(filepath.Join(pp.ProjName, namesrc), dat, 0644)
  if err != nil {
    return err
  }
  return nil
}func writeFile(templatesrc []byte, dest string) error {
  err := ioutil.WriteFile(dest, templatesrc, 0644)
  if err != nil {
    return err
  }
  return nil
}func (pp *ProjData) Package() string {
  proj := os.Getenv("GOPACKAGE")
  if proj == "" {
    proj = "main"
  }
  return proj
}
```

请注意，我们使用文件名的嵌套数组来确定哪些文件用于内部还是外部 metaapi 项目。如果应用了模板操作(参数),则尾部布尔值为真，如果是直接的文件副本，则为假:

```
var files = [][]FileList{{{"sqlname.txt", true}, 
     {"configlocaldb.txt", true}},
    {{"sqlname2.txt", true}, {"configlocaldb.txt", true}, 
     {"generate.txt", true}, {"main.txt", true}, {"api.txt", false}, 
     {"api_test.txt", false}}}
```

此外，我们将返回到 metaproj 中模板的默认 go 分隔符，因为我们将模板化的 txt 文件之一是 generate.go 本身，它包括行`tt := template.Must(template.New(“file”).Delims(“<<”, “>>”).Parse(string(templatesrc)))`注意，Delim 语句将始终作为(失败的)模板操作执行，因为它包括开始和结束模板操作分隔符`<<>>`。所以我们需要 metaproj 和 metaapi 中不同的分隔符。

查看 github repo 以查看完整的文件集，因为有一个 txt 文件的数据/文件夹和从它们创建的 bindata.go。

现在，我们有了一个简单的命令行实用程序，可以基于 sql 表定义创建两种不同风格的初始 metaapi 项目。

```
metaproj -sql=mysqltables -proj=myprojectname
```

您可以添加可选的`-type=external`，让它创建一个使用 pipe 的 metaapi 项目，这样您就可以定制 api txt 模板和 generate.go

好了，现在让我们将所有这些放在一起并进行测试，包括内部和外部的 metaapi 项目。

首先创建您的 PostgreSQL 项目数据库，以便测试可以工作:

```
createuser -P -d myproj <pass: myproj>
createdb myproj
```

现在，创建一个默认的内部 metaapi 项目并测试它:

```
go get github.com/exyzzy/metaapi
go install $GOPATH/src/github.com/exyzzy/metaapi
go get github.com/exyzzy/metaproj
go install $GOPATH/src/github.com/exyzzy/metaproj
cp $GOPATH/src/github.com/exyzzy/metaapi/examples/alltypes.sql .
# or your own postgreSQL table definition
rm -rf myproj
#clean out the old directory if neededmetaproj -sql=alltypes.sql -proj=myproj 
cd myproj
go generate
go test
```

该项目将看起来像:

```
myproj/
    alltypes.go
    alltypes.sql
    alltypes_generated_api.go
    alltypes_generated_api_test.go
    configlocaldb.json
```

最后，让我们创建一个外部 metaapi 项目，您希望在其中开发定制模板:

```
go get github.com/exyzzy/metaapi
go install $GOPATH/src/github.com/exyzzy/metaapi
go get github.com/exyzzy/metaproj
go install $GOPATH/src/github.com/exyzzy/metaproj
go get github.com/exyzzy/pipe
go install $GOPATH/src/github.com/exyzzy/pipe
cp $GOPATH/src/github.com/exyzzy/metaapi/examples/alltypes.sql .
rm -rf myproj
#clean out the old directory if neededmetaproj -sql=alltypes.sql -proj=myproj -type=external 
# or your own postgreSQL table definition
cd myproj
go install
go generate
go test
```

该项目将看起来像:

```
myproj/
    alltypes.go
    alltypes.sql
    alltypes_generated_api.go
    alltypes_generated_api_test.go
    configlocaldb.json
    api.txt
    api_test.txt
    generate.go
    main.go
```

这两者都应该:

*   生成初始项目
*   为 sql 表生成 CRUD api
*   为 CRUD api 生成基本测试
*   通过所有测试

我们已经成功地将我们最初的 metaapi 工具扩展为一个能够为大多数 PostgreSQL 类型创建 CRUD api 的工具，并创建相应的 api 测试函数。我们创建了一个实用程序 pipe，允许多个应用程序在 go generate 调用中链接在一起。我们创建了另一个实用程序 metaproj，它是从参数化的基础文件创建任何新项目的通用模式。这些应该会给我们从 PostgreSQL 表定义构建 go 项目一个巨大的开端。

您可以在以下网址找到所有源代码:

*   [https://github.com/exyzzy/metaapi](https://github.com/exyzzy/metaapi)
*   [https://github.com/exyzzy/pipe](https://github.com/exyzzy/pipe)
*   【https://github.com/exyzzy/metaproj 号

玩得开心。