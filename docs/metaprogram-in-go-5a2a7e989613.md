# Go 中的元程序

> 原文：<https://levelup.gitconnected.com/metaprogram-in-go-5a2a7e989613>

![](img/d8d9dd7db99236e93d83d37e556291e3.png)

标志归功于 golang.org

## 如何使用 Golang 对 CRUD API 进行元编程？

大多数现代网站都正式或非正式地实施了某种 MV*框架。如果你写了很多代码，你可能会一遍又一遍地写很多模型(M ),这些模型在结构上非常相似，只是在模式的细节上有所不同。您定义 SQL 模式，创建结构，并在两者之间组装一些基本的 [CRUD](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) API。然后，您可以在开发应用程序逻辑的过程中对其进行调整。将其中一些自动化，以减少时间和错误，不是很好吗？

在本文中，我们将这样做。我们将在 Go 中探索元编程，以基于 SQL 表定义自动创建简单的 CRUD API。

## 介绍

元编程本质上就是写程序写程序。因此，你是从特定的程序移出一个层次，而是求解一个更一般的程序类。Rails 生成器(如 Ruby on Rails)就是一个众所周知的例子。只需运行`rails new test`就可以创建一个名为“test”的全功能项目。元编程通常需要更长的时间，但是最终您有了一个可以在类似问题上重用的工具。

我通常将 Postgres 用于我的数据库，所以我想将一组 SQL Create Table 定义自动转换为 Go code API。如果你看过 CREATE TABLE 的 Postgres 语法，它的[非常丰富](https://www.postgresql.org/docs/12/sql-createtable.html)，尽管它只有一条语句。幸运的是，我们不需要解析整个语法来满足我们的目标。为了构建我们的 CRUD API，我们需要:

1.  捕获整个 CREATE TABLE 语句
2.  捕获表名
3.  捕获表列名
4.  捕获表列类型

这可能看起来很多，但实际上它将允许我们忽略 CREATE TABLE 语法的大部分可选部分。我们可以寻找我们想要的东西，忽略其他的。

我们将构建一个简单的编译器来筛选 SQL 表定义，保存我们关心的部分，以便我们稍后可以基于我们发现的内容生成新代码。我们的简单编译器有三个主要部分:一个词法分析器、一个语法分析器和一个生成器。我们的语法，基于 Postgres 文档，看起来像:

```
CREATE [some_stuff]* TABLE [IF NOT EXISTS] table_name (
    column_name data_type [some_stuff]*
    [, ...]
) [some_stuff]* ;
```

我们的秘籍是允许但忽略所有的[some_stuff]* SQL 语法。我们必须解析[IF NOT EXISTS]选项，因为如果我们将它视为任意文本，我们可能会错过 table_name。同样为了简单起见，我们不支持列列表中的 table_constraints 和 LIKE 语句**，因为这将迫使我们进行更复杂的解析。该语句必须以“；”结尾。我们将支持所有主要的数据类型，并将它们转换成 Go，如下所示:**

```
SQL              GO  
----------------|-------                                                 
BOOLEAN          bool                                                 
BOOL             bool                                                 
CHAR(n)          string                                               
VARCHAR(n)       string                                               
TEXT             string                                               
SMALLINT         int16                                                
INT              int32                                                
INTEGER          int32                                                
BIGINT           int64                                                
SMALLSERIAL      int16                                                
SERIAL           int32                                                
BIGSERIAL        int64                                                
FLOAT(n)         float64                                              
REAL             float32                                              
FLOAT8           float32                                              
DECIMAL          float64                                              
NUMERIC          float64                                              
NUMERIC(p,s)     float64                                              
DOUBLE PRECISION float64                                              
DATE             time.Time                        
TIME             time.Time                
TIMESTAMPTZ      time.Time
TIMESTAMP        time.Time
INTERVAL         time.Time
JSON             []byte                                               
JSONB            []byte                                               
UUID             string
```

我们的目标是为任何 SQL 表(foo)自动构建以下内容:

*   func create table**Foo**(db * SQL。DB) (err error){}
*   类型 **Foo** struct{}
*   func(**Foo * Foo**)Create**Foo**(db * SQL。DB)(结果 **Foo** ，err error){}
*   func ( **foo *Foo** )检索 **Foo** (db *sql。DB)(结果 **Foo** ，err error){}
*   func(**Foo * Foo**)retrieve all**Foo**(db * SQL。DB) ( **foo []Foo** ，err error){}
*   func(**Foo * Foo**)Update**Foo**(db * SQL。DB)(结果 **Foo** ，err error){}
*   func(**Foo * Foo**)Delete**Foo**(db * SQL。DB) (err error){}
*   func delete all**Foo**(db * SQL。DB) (err error){}

# 词法分析程序

词法分析是从字符流中检测标记的过程。它可以简单到寻找空格分隔的单词，或者更常见的是识别特定的关键字和 id。Go 有用于[扫描](https://golang.org/pkg/go/scanner/)和 [lexing](https://godoc.org/github.com/cznic/golex/lex) 的包来使这变得更容易。我选择了 Tim Henderson 的一个叫做 [Lexmachine](https://github.com/timtadh/lexmachine) 的包，这个包有很好的文档记录。

使用 Tim 出色的点词法分析器示例作为模板，我们可以为上面简化的 SQL 子集构建一个词法分析器。关键字和文字非常简单。关键字是**创建，表格，**如果不存在，和我们的静态列表**数据类型**。同样，我们必须检测“如果不存在”选项来消除 table_name 的歧义。文字只是:**()，；**

标识符有点复杂。这些将包括: **CHAR(n)、VARCHAR(n)、FLOAT(n)、NUMERIC(p，s)、ID(ID)、table_name 和 column_name，** Lexmachine 使用 regex 作为标识符，因此我们将对 SQL 使用以下内容:

```
VARCHAR(n):   [vV][aA][rR][cC][hH][aA][rR]\([0-9]+\)
CHAR(n):      [cC][hH][aA][rR]\([0-9]+\)
FLOAT(n):     [fF][lL][oO][aA][tT]\([0-9]+\)
NUMERIC(p,s): [nN][uU][mM][eE][rR][iI][cC]\([0-9]+,[0-9]+\)
ID(ID):       ([a-z]|[A-Z]|_|#|@)([a-z]|[A-Z]|[0-9]|_|#|@|\$)*
              \(([a-z]|[A-Z]|_|#|@)([a-z]|[A-Z]|[0-9]|_|#|@|\$)*\)
ID:           ([a-z]|[A-Z]|_|#|@)([a-z]|[A-Z]|[0-9]|_|#|@|\$)*
```

注意，Lexmachine 还不支持类似(？I)，这解释了上面的正则表达式。ID(ID)是允许引用所必需的，否则由于括号的原因，这些引用将是错误的。ID 可以用于表名和列名。lexer 将跳过任何空白。就这样，莱克瑟完成了。

metaapi/metasql/lexer.go:

```
//Derived from: [https://hackthology.com/writing-a-lexer-in-go-with-lexmachine.html](https://hackthology.com/writing-a-lexer-in-go-with-lexmachine.html)package metasqlimport (
    "strings"lex "github.com/timtadh/lexmachine"
    "github.com/timtadh/lexmachine/machines"
)var Literals []string       
var Keywords []string       
var Tokens []string         
var TokenIds map[string]int 
var Lexer *lex.Lexer // Called at package initialization. Creates the lexer and populates token lists.
func init() {
    initTokens()
    var err error
    Lexer, err = initLexer()
    if err != nil {
        panic(err)
    }
}func initTokens() {
    Tokens = []string{
        "VARCHARID",
        "CHARID",
        "FLOATID",
        "NUMERICID",
        "REFID",
        "ID",
    }
    Keywords = []string{
        "CREATE",
        "TABLE",
        "IF",
        "NOT",
        "EXISTS",
        "BOOLEAN",
        "BOOL",
        "TEXT",
        "SMALLINT",
        "INTEGER",
        "BIGINT",
        "INT",
        "SMALLSERIAL",
        "BIGSERIAL",
        "SERIAL",
        "REAL",
        "FLOAT8",
        "DECIMAL",
        "NUMERIC",
        "DOUBLE",
        "PRECISION",
        "DATE",
        "TIMESTAMPTZ",
        "TIMESTAMP",
        "TIME",
        "INTERVAL",
        "JSONB",
        "JSON",
        "UUID",
    }
    Literals = []string{
        "(",
        ")",
        ",",
        ";",
    }
    Tokens = append(Tokens, Keywords...)
    Tokens = append(Tokens, Literals...)
    TokenIds = make(map[string]int)
    for i, tok := range Tokens {
        TokenIds[tok] = i
    }
}// Creates the lexer object and compiles the NFA.
func initLexer() (*lex.Lexer, error) {
    lexer := lex.NewLexer()for _, lit := range Literals {
        r := "\\" + strings.Join(strings.Split(lit, ""), "\\")
        lexer.Add([]byte(r), token(lit))
    }
    for _, name := range Keywords {
        lexer.Add([]byte(strings.ToLower(name)), token(name))
    }
    lexer.Add([]byte(`[vV][aA][rR][cC][hH][aA][rR]\([0-9]+\)`), token("VARCHARID"))
    lexer.Add([]byte(`[cC][hH][aA][rR]\([0-9]+\)`), token("CHARID"))
    lexer.Add([]byte(`[fF][lL][oO][aA][tT]\([0-9]+\)`), token("FLOATID"))
    lexer.Add([]byte(`[nN][uU][mM][eE][rR][iI][cC]\([0-9]+,[0-9]+\)`), token("NUMERICID"))
    lexer.Add([]byte(`([a-z]|[A-Z]|_|#|@)([a-z]|[A-Z]|[0-9]|_|#|@|\$)*\(([a-z]|[A-Z]|_|#|@)([a-z]|[A-Z]|[0-9]|_|#|@|\$)*\)`), token("REFID"))
    lexer.Add([]byte(`([a-z]|[A-Z]|_|#|@)([a-z]|[A-Z]|[0-9]|_|#|@|\$)*`), token("ID"))
    lexer.Add([]byte("( |\t|\n|\r)+"), skip) err := lexer.Compile()
    if err != nil {
        return nil, err
    }
    return lexer, nil
}// a lex.Action function which skips the match.
func skip(*lex.Scanner, *machines.Match) (interface{}, error) {
    return nil, nil
}// a lex.Action function with constructs a Token of the given token 
// type by the token type's name.
func token(name string) lex.Action {
    return func(s *lex.Scanner, m *machines.Match) (interface{}, error) {
        return s.Token(TokenIds[name], string(m.Bytes), m), nil
    }
}
```

# 句法分析程序

解析是从词法分析器创建的标记流中检测特定语法部分的过程。

我们现在将为上面的受限 SQL 语法构建一个简单的解析器。对于复杂的语法，我们倾向于使用基于递归下降或某种解析器生成器的真正解析器。我们的方法很简单，有限状态机就可以完成这项工作。一个简单的状态机遵循这样的模式:如果我处于状态 **CurState** 并且我看到一些**输入**然后移动到一个 **NextState** 并且**执行一些**动作**。这样做，直到达到某个终端状态，通常是 EOF。在我们的例子中，输入将是令牌类型，动作将是我们调用来保存表数据或忽略它的某个函数。这是我们的状态表，您会注意到它的大部分都与检测不同的数据类型有关:**

```
Cur                 Next
State Input         State Action
---------------------------------------
0    Error          0     error_state
1    CREATE         2     create_table
2    TABLE          3     nop
2    ID             2     some_stuff
3    IF             4     nop
4    NOT            5     nop
5    EXISTS         3     nop
3    ID             6     table_name
6    (              7     nop
7    ID             8     column_name
7    UUID           8     column_name
8    BOOLEAN        9     data_type
8    BOOL           9     data_type
8    CHARID         9     data_type
8    VARCHARID      9     data_type
8    TEXT           9     data_type
8    SMALLINT       9     data_type
8    INT            9     data_type
8    INTEGER        9     data_type
8    BIGINT         9     data_type
8    SMALLSERIAL    9     data_type
8    SERIAL         9     data_type
8    BIGSERIAL      9     data_type
8    FLOATID        9     data_type
8    REAL           9     data_type
8    FLOAT8         9     data_type
8    DECIMAL        9     data_type
8    NUMERIC        9     data_type
8    NUMERICID      9     data_type
8    DOUBLE         10    nop
10   PRECISION      9     data_type
8    DATE           9     data_type
8    TIME           9     data_type
8    TIMESTAMPTZ    9     data_type
8    TIMESTAMP      9     data_type
8    INTERVAL       9     data_type
8    JSON           9     data_type
8    JSONB          9     data_type
8    UUID           9     data_type
9    ,              7     nop
9    )              11    nop
9    REFID          9     some_stuff
9    NOT            9     some_stuff
9    ID             9     some_stuff
11   ;              1     end_table
11   ID             11    some_stuff
```

除了理解和执行我们的语法，解析器还必须捕获它生成代码所需的所有数据。在真正的编译器中，它可能会构建一个[抽象语法树](https://en.wikipedia.org/wiki/Abstract_syntax_tree)来做这件事，然后代码生成器会分析它。在我们的例子中，我们将只构建一个表及其列名和类型的列表，用于我们的生成器。我们的状态机本身只是一个 Go map，它接受一个字符串作为输入，并返回下一个要触发的状态和动作(方法)的结构。映射字符串将当前状态和输入令牌连接在一起，形成映射的一个字符串。" CurState，InToken": {NextState，FunctionToCall}

metaapi/metasql/parse.go:

```
package metasqlimport (
    "errors"
    "fmt"
    "log" lex "github.com/timtadh/lexmachine"
)type Column struct {
    Name string
    Type string
}type Table struct {
    Name    string
    Query   string
    Columns []Column
}type StateMachine struct {
    FName    string
    CurState int
    Tables   []Table
}type NextAction struct {
    State int
    Fn    func(*StateMachine, *lex.Token)
}func getColumn(sm *StateMachine) *Column {
    if len(sm.Tables) > 0 {
        table := &(sm.Tables[len(sm.Tables)-1])
        if len(table.Columns) > 0 {
            return &(table.Columns[len(table.Columns)-1])
        } else {
            return nil
        }
    } else {
        return nil
    }}func InitState(fname string) *StateMachine {
    sm := new(StateMachine)
    sm.FName = fname
    sm.CurState = 1
    return sm
}func error_state(sm *StateMachine, token *lex.Token) {
    //no state found
    log.Fatal("Error in SQL Syntax!")
}func nop(sm *StateMachine, token *lex.Token) {
    //nop
}func create_table(sm *StateMachine, token *lex.Token) {
    sm.Tables = append(sm.Tables, Table{})
}func table_name(sm *StateMachine, token *lex.Token) {
    if len(sm.Tables) > 0 {
        sm.Tables[len(sm.Tables)-1].Name = string(token.Lexeme)
    }
}func column_name(sm *StateMachine, token *lex.Token) {
    if len(sm.Tables) > 0 {
        table := &(sm.Tables[len(sm.Tables)-1])
        table.Columns = append(table.Columns, Column{})
        table.Columns[len(table.Columns)-1].Name = string(token.Lexeme)
    }
}func data_type(sm *StateMachine, token *lex.Token) {
    column := getColumn(sm)
    column.Type = Tokens[token.Type]
}func some_stuff(sm *StateMachine, token *lex.Token) {
    //nop
}func end_table(sm *StateMachine, token *lex.Token) {
    //nop
}func appendQuery(sm *StateMachine, st string) {
    if len(sm.Tables) > 0 {
        (&(sm.Tables[len(sm.Tables)-1])).Query += st + " "
    }
}func printQuery(sm *StateMachine) {
    if len(sm.Tables) > 0 {
        fmt.Println("query: ", (&(sm.Tables[len(sm.Tables)-1])).Query, " <<")
    }
}func ProcessState(sm *StateMachine, token *lex.Token) (err error) { //State Machine, format is:
    //"CurState, InToken": {NextState, FunctionToCall} stateMap := map[string]NextAction{
        "Error":         {0, error_state},
        "1,CREATE":      {2, create_table},
        "2,TABLE":       {3, nop},
        "2,ID":          {2, some_stuff},
        "3,IF":          {4, nop},
        "4,NOT":         {5, nop},
        "5,EXISTS":      {3, nop},
        "3,ID":          {6, table_name},
        "6,(":           {7, nop},
        "7,ID":          {8, column_name},
        "7,UUID":        {8, column_name},
        "8,BOOLEAN":     {9, data_type},
        "8,BOOL":        {9, data_type},
        "8,CHARID":      {9, data_type},
        "8,VARCHARID":   {9, data_type},
        "8,TEXT":        {9, data_type},
        "8,SMALLINT":    {9, data_type},
        "8,INT":         {9, data_type},
        "8,INTEGER":     {9, data_type},
        "8,BIGINT":      {9, data_type},
        "8,SMALLSERIAL": {9, data_type},
        "8,SERIAL":      {9, data_type},
        "8,BIGSERIAL":   {9, data_type},
        "8,FLOATID":     {9, data_type},
        "8,REAL":        {9, data_type},
        "8,FLOAT8":      {9, data_type},
        "8,DECIMAL":     {9, data_type},
        "8,NUMERIC":     {9, data_type},
        "8,NUMERICID":   {9, data_type},
        "8,DOUBLE":      {10, nop},
        "10,PRECISION":  {9, data_type},
        "8,DATE":        {9, data_type},
        "8,TIME":        {9, data_type},
        "8,TIMESTAMPTZ": {9, data_type},
        "8,TIMESTAMP":   {9, data_type},
        "8,INTERVAL":    {9, data_type},
        "8,JSON":        {9, data_type},
        "8,JSONB":       {9, data_type},
        "8,UUID":        {9, data_type},
        "9,,":           {7, nop},
        "9,)":           {11, nop},
        "9,REFID":       {9, some_stuff},
        "9,NOT":         {9, some_stuff},
        "9,ID":          {9, some_stuff},
        "11,;":          {1, end_table},
        "11,ID":         {11, some_stuff},
    }mapStr := fmt.Sprintf("%d,%s", sm.CurState, Tokens[token.Type])
    nextState := stateMap[mapStr]
    //map zeros all fields of struct if not found
    if nextState.State == 0 {
        nextState = stateMap["Error"]
        printQuery(sm)
        err = errors.New("Syntax Error: " + Tokens[token.Type])
        return
    }
    sm.CurState = nextState.State
    nextState.Fn(sm, token)
    appendQuery(sm, string(token.Lexeme))
    return nil
}
```

# 发电机

生成器是我们“编译器”的代码生成部分。它采用我们通过词法分析和解析创建的 SQL 表的内部表示，并从中为表生成 CRUD API。如果一切顺利，产生的代码应该准备好进入另一个项目，编译并运行。

我们构建生成器的方法是从一些已知的工作代码开始，这些代码代表我们想要生成的 API，我们的目标。我们将把它重命名为一个 txt 文件，作为模板输入到生成器中，然后一段一段地慢慢转换，直到它完全“模板化”我们将在 generate.go 中编写相应的接收器方法，并将其传递给模板。运行生成器时，应该重新创建原始目标。这个过程使我们很容易将生成的内容与原始目标进行比较，并在此过程中纠正任何错误。

在我的例子中，目标 API 将是来自于 [govueintro](https://github.com/exyzzy/govueintro) 项目 todo.go 的 Todo CRUD API，我将转换它以使用来自于 metaapi 的自动生成的 todo_generated.go。下面的 Crud.txt 开始看起来像 todo_generated.go(也在下面),当我在 generate.go 中用 receiver 方法替换部分时，我迭代地将其转换为 crud . txt。crud . txt 在这一点上看起来很难看，但相信我，迭代转换过程很容易。Generate.go 只是使用 go 模板将特定的表数据折叠到通用的 crud.txt 中。

metaapi/metasql/generate.go:

```
package metasqlimport (
    "errors"
    "io/ioutil"
    "os"
    "strconv"
    "strings"
    "text/template"
)//Generate assumes that the primary ID is in the first column (index 0)
func Generate(sm *StateMachine, txtFile string) error {
    if sm.FName == "" {
        return (errors.New("No file name"))
    }
    dot := strings.Index(sm.FName, ".")
    var prefix string
    if dot > 0 {
        prefix = sm.FName[:dot]
    } else {
        prefix = sm.FName
    }
    dat, err := ioutil.ReadFile("./" + txtFile)
    if err != nil {
        return err
    } tt := template.Must(template.New(prefix).Parse(string(dat)))
    dest := prefix + "_generated.go"
    file, err := os.Create(dest)
    if err != nil {
        return err
    }
    tt.Execute(file, sm)
    file.Close()
    return nil
}//======== string helpers//should use: [https://github.com/blakeembrey/pluralize](https://github.com/blakeembrey/pluralize)
func singularize(s string) string {
    if strings.HasSuffix(strings.ToLower(s), "s") {
        return strings.TrimSuffix(strings.ToLower(s), "s")
    } else {
        return strings.ToLower(s)
    }
}func capitalize(s string) string {
    return strings.Title(s)
}func lowerize(s string) string {
    return strings.ToLower(s)
}//turns submitted_at into SubmittedAt, and otherwise capitalizes
func camelize(s string) string {
    return     strings.ReplaceAll(strings.Title(strings.ReplaceAll(strings.ToLower(s), "_", " ")), " ", "")
}func comma(i int, length int) string {
    if i < (length - 1) {
        return ","
    } else {
        return ""
    }
}//======== template methodsfunc (sm *StateMachine) Package() string {
    return os.Getenv("GOPACKAGE")
}// Writing it to be extended
func (sm *StateMachine) Import() string {var s string
    var includeTime boolincludeTime = false
    for _, table := range sm.Tables {
        for _, column := range table.Columns {
            switch column.Type {
            case "DATE", "TIME", "TIMESTAMP", "TIMESTAMPTZ", "INTERVAL":
                includeTime = true
            default:
            }
        }
    }
    s += "import (\n\t\"database/sql\"\n"
    if includeTime {
        s += "\t\"time\"\n"
    }
    s += ")"
    return s
}func (table Table) SingName() string {
    return singularize(table.Name)
}func (table Table) CapName() string {
    return capitalize(lowerize(table.Name))
}func (table Table) CapSingName() string {
    return capitalize(singularize(table.Name))
}func (table Table) DropTableStatement() string {
    var s string
    s += "(\"DROP TABLE IF EXISTS " + table.Name + "\")"
    return s
}func (table Table) CreateTableStatement() string {
    var s string
    s += "(`" + table.Query + "`)"
    return s
}func (table Table) StructFields() string {var typeMap = map[string]string{
        "BOOLEAN":     "bool",
        "BOOL":        "bool",
        "CHARID":      "string",
        "VARCHARID":   "string",
        "TEXT":        "string",
        "SMALLINT":    "int16",
        "INT":         "int32",
        "INTEGER":     "int32",
        "BIGINT":      "int64",
        "SMALLSERIAL": "int16",
        "SERIAL":      "int32",
        "BIGSERIAL":   "int64",
        "FLOATID":     "float64",
        "REAL":        "float32",
        "FLOAT8":      "float32",
        "DECIMAL":     "float64",
        "NUMERIC":     "float64",
        "NUMERICID":   "float64",
        "PRECISION":   "float64", //DOUBLE PRECISION
        "DATE":        "time.Time",
        "TIME":        "time.Time",
        "TIMESTAMPTZ": "time.Time",
        "TIMESTAMP":   "time.Time",
        "INTERVAL":    "time.Time",
        "JSON":        "[]byte",
        "JSONB":       "[]byte",
        "UUID":        "string",
    }
    var s string for _, column := range table.Columns {
        s += "\t" + camelize(column.Name)
        s += " " + typeMap[column.Type]
        s += "`xml:\"" + camelize(column.Name) + "\" json:\"" +      lowerize(camelize(column.Name)) + "\"`"
        s += "\n"
    }
    return s
}func (table Table) Star() string {
    var s string
    for i, column := range table.Columns {
        s += " " + column.Name
        s += comma(i, len(table.Columns))
    }
    return s
}func (table Table) ScanAll() string {var s string
    s += ".Scan("
    for i, column := range table.Columns {
        s += " &result." + camelize(column.Name)
        s += comma(i, len(table.Columns))
    }
    s += ")"
    return s
}func (table Table) CreateStatement() string {
    var s string
    s += "(\"INSERT INTO " + table.Name + " ("for i, column := range table.Columns {
        if i == 0 {
            continue
        }
        s += " " + column.Name
        s += comma(i, len(table.Columns))
    }
    s += ") VALUES ("
    index := 1
    for i, _ := range table.Columns {
        if i == 0 {
            continue
        }
        s += "$"
        s += strconv.Itoa(index)
        s += comma(i, len(table.Columns))
        index++
    }
    s += ") RETURNING"
    for i, column := range table.Columns {
        s += " " + column.Name
        s += comma(i, len(table.Columns))
    }
    s += "\")"
    return s
}func (table Table) CreateQuery() string {
    var s string
    s += "("
    for i, column := range table.Columns {
        if i == 0 {
            continue
        }
        s += " " + table.SingName() + "." + camelize(column.Name)
        s += comma(i, len(table.Columns))
    }
    s += ")"
    s += table.ScanAll()
    return s
}func (table Table) RetrieveStatement() string {
    var s string
    s += "(\"SELECT" + table.Star() + " FROM " + table.Name + " WHERE (" index := 1
    for i, column := range table.Columns {
        if i == 0 {
            s += column.Name + " = $" + strconv.Itoa(index)
            s += ")\", " + table.SingName() + "." + camelize(column.Name) + ")"
        }
        break
    }
    s += table.ScanAll()
    return s
}func (table Table) RetrieveAllStatement() string {
    var s string
    s += "(\"SELECT" + table.Star() + " FROM " + table.Name + " ORDER BY " for i, column := range table.Columns {
        if i == 0 {
            s += column.Name
        }
        break
    }
    s += " DESC\")"
    return s
}func (table Table) UpdateStatement() string {
    var s string
    s += "(\"UPDATE " + table.Name + " SET" index := 2
    for i, column := range table.Columns {
        if i == 0 {
            continue
        }
        s += " " + column.Name + " = $" + strconv.Itoa(index)
        index++
        s += comma(i, len(table.Columns))
    }
    s += " WHERE ("
    index = 1
    for i, column := range table.Columns {
        if i == 0 {
            s += column.Name + " = $" + strconv.Itoa(index)
            s += ") RETURNING"
        }
        break
    }
    for i, column := range table.Columns {
        s += " " + column.Name
        s += comma(i, len(table.Columns))
    }
    s += "\")"
    return s
}func (table Table) UpdateQuery() string { var s string
    s += "("
    for i, column := range table.Columns {
        s += " " + table.SingName() + "." + camelize(column.Name)
        s += comma(i, len(table.Columns))
    }
    s += ")"
    s += table.ScanAll()
    return s
}func (table Table) DeleteStatement() string {
    var s string
    s += "(\"DELETE FROM " + table.Name + " WHERE (" index := 1
    for i, column := range table.Columns {
        if i == 0 {
            s += column.Name + " = $" + strconv.Itoa(index)
        }
        break
    }
    s += ")\")"
    return s
}func (table Table) DeleteQuery() string { var s string
    for i, column := range table.Columns {
        if i == 0 {
            s += "(" + table.SingName() + "." + camelize(column.Name) + ")"
        }
        break
    }
    return s
}func (table Table) DeleteAllStatement() string {
    var s string
    s += "(\"DELETE FROM " + table.Name + "\")"
    return s
}
```

metaapi/metasql/crud.txt:

```
//Auto generated with MetaApi [https://github.com/exyzzy/metaapi](https://github.com/exyzzy/metaapi)
package {{ .Package }}{{ .Import }}{{ range $index, $table := .Tables }}
//Create Table
func CreateTable{{ $table.CapName }}(db *sql.DB) (err error) {
    _, err = db.Exec{{ $table.DropTableStatement }}
    if err != nil {
        return
    }
    _, err = db.Exec{{ $table.CreateTableStatement }}
    return
}//Struct
type {{ $table.CapSingName }} struct {
{{ $table.StructFields }}
}//Create
func ({{ $table.SingName }} *{{ $table.CapSingName }}) Create{{ $table.CapSingName }}(db *sql.DB) (result {{ $table.CapSingName }}, err error) {
    stmt, err := db.Prepare{{ $table.CreateStatement }}
    if err != nil {
        return
    }
    defer stmt.Close()
    err = stmt.QueryRow{{ $table.CreateQuery }}
    return
}//Retrieve
func ({{ $table.SingName }} *{{ $table.CapSingName }}) Retrieve{{ $table.CapSingName }}(db *sql.DB) (result {{ $table.CapSingName }}, err error) {
    result = {{ $table.CapSingName }}{}
    err = db.QueryRow{{ $table.RetrieveStatement }}
    return
}//RetrieveAll
func ({{ $table.SingName }} *{{ $table.CapSingName }}) RetrieveAll{{ $table.CapName }}(db *sql.DB) ({{ $table.Name }} []{{ $table.CapSingName }}, err error) {
    rows, err := db.Query{{ $table.RetrieveAllStatement }}
    if err != nil {
        return
    }
    for rows.Next() {
        result := {{ $table.CapSingName }}{}
        if err = rows{{ $table.ScanAll }}; err != nil {
            return
        }
        {{ $table.Name }} = append({{ $table.Name }}, result)
    }
    rows.Close()
    return
}//Update
func ({{ $table.SingName }} *{{ $table.CapSingName }}) Update{{ $table.CapSingName }}(db *sql.DB) (result {{ $table.CapSingName }}, err error) {
    stmt, err := db.Prepare{{ $table.UpdateStatement }}
    if err != nil {
        return
    }
    defer stmt.Close()err = stmt.QueryRow{{ $table.UpdateQuery }}
    return
}//Delete
func ({{ $table.SingName }} *{{ $table.CapSingName }}) Delete{{ $table.CapSingName }}(db *sql.DB) (err error) {
    stmt, err := db.Prepare{{ $table.DeleteStatement }}
    if err != nil {
        return
    }
    defer stmt.Close()_, err = stmt.Exec{{ $table.DeleteQuery }}
    return
}//DeleteAll
func DeleteAll{{ $table.CapSingName }}s(db *sql.DB) (err error) {
    stmt, err := db.Prepare{{ $table.DeleteAllStatement}}
    if err != nil {
        return
    }
    defer stmt.Close()_, err = stmt.Exec()
    return
}
{{ end }}
```

一个简短的 main 将所有内容放在一起，并使用 go 标志来传递要使用的文件名。

metaapi/main.go:

```
package mainimport (
    "flag"
    "fmt"
    "io/ioutil"
    "log"
    "strings" "github.com/exyzzy/metaapi/metasql" lex "github.com/timtadh/lexmachine"
)// Turn on debug prints
var DEBUG = falsefunc main() {
    sqlPtr := flag.String("sql", "", ".sql input file to parse")
    txtPtr := flag.String("txt", "crud.txt", "go template as .txt file")
    flag.Parse()
    sqlFile := strings.ToLower(*sqlPtr)
    txtFile := strings.ToLower(*txtPtr) if (sqlFile == "") || (!strings.HasSuffix(sqlFile, ".sql")) {
        log.Fatal("No .sql File")
    }
    if (txtFile == "") || (!strings.HasSuffix(txtFile, ".txt")) {
        log.Fatal("No .txt File")
    } dat, err := ioutil.ReadFile("./" + sqlFile)
    if err != nil {
        log.Fatal(err)
    } s, err := metasql.Lexer.Scanner([]byte(dat))
    if err != nil {
        log.Fatal(err)
    } sm := metasql.InitState(sqlFile)
    for tok, err, eof := s.Next(); !eof; tok, err, eof = s.Next() {
        if err != nil {
            log.Fatal(err)
        }
        token := tok.(*lex.Token)
        if DEBUG {
            fmt.Printf("%-10v | %-12v | %v:%v-%v:%v\n",
                metasql.Tokens[token.Type],
                string(token.Lexeme),
                token.StartLine,
                token.StartColumn,
                token.EndLine,
                token.EndColumn)
        }
        err = metasql.ProcessState(sm, token)
        if err != nil {
            log.Fatal(err)
        }
    }
    err = metasql.Generate(sm, txtFile)
    if err != nil {
        log.Fatal(err)
    }
    if DEBUG {
        fmt.Printf("Table Capture:\n%+v\n", sm)
    }
}
```

# 有用吗？

要将其作为工具运行，您需要:

*   克隆 metaapi 项目并安装它
*   为新项目创建一个目录
*   将 crud 模板(crud.txt)、您的 sql 表定义(todo.sql)和一个启动 go generate (todo.go)的可选文件复制到您的新项目中。注意:您可以为您的特定项目更改这些内容。
*   运行 go generate 或手动运行 metaapi
*   新的 go 文件将被创建为*_generated.go(基于 sql 文件的名称)

对于我的 Todo api，我使用了以下代码:

metasql/todo.go:

```
//go:generate metaapi -sql=todo.sql -txt=crud.txt
package metasql//Now see: todo_generated.go
```

metasql/todo.sql:

```
create table todos (
  id           integer generated always as identity primary key,
  updated_at   timestamptz,
  done         boolean,
  title        text
);
```

让我们运行它，看看会有什么结果。

```
go get github.com/exyzzy/metaapi
go install $GOPATH/src/github.com/exyzzy/metaapi
mkdir myproj
cd myproj
cp $GOPATH/src/github.com/exyzzy/metaapi/metasql/crud.txt .
cp $GOPATH/src/github.com/exyzzy/metaapi/metasql/todo.sql .
cp $GOPATH/src/github.com/exyzzy/metaapi/metasql/todo.go .
go generate
```

现在会自动创建 todo_generated.go 文件:

```
//Auto generated with MetaApi [https://github.com/exyzzy/metaapi](https://github.com/exyzzy/metaapi)
package metasqlimport (
    "database/sql"
    "time"
)//Create Table
func CreateTableTodos(db *sql.DB) (err error) {
    _, err = db.Exec("DROP TABLE IF EXISTS todos")
    if err != nil {
        return
    }
    _, err = db.Exec(`create table todos ( id integer generated always as identity primary key , updated_at timestamptz , done boolean , title text ) ; `)
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
func (todo *Todo) RetrieveAllTodos(db *sql.DB) (todos []Todo, err error) {
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
    defer stmt.Close() err = stmt.QueryRow( todo.Id, todo.UpdatedAt, todo.Done, todo.Title).Scan( &result.Id, &result.UpdatedAt, &result.Done, &result.Title)
    return
}//Delete
func (todo *Todo) DeleteTodo(db *sql.DB) (err error) {
    stmt, err := db.Prepare("DELETE FROM todos WHERE (id = $1)")
    if err != nil {
        return
    }
    defer stmt.Close() _, err = stmt.Exec(todo.Id)
    return
}//DeleteAll
func DeleteAllTodos(db *sql.DB) (err error) {
    stmt, err := db.Prepare("DELETE FROM todos")
    if err != nil {
        return
    }
    defer stmt.Close() _, err = stmt.Exec()
    return
}
```

任务完成。我们现在有了一个工具，可以轻松地为任意 SQL 表生成基本 CRUD API，或者我们可以扩展/定制 lexer、解析器、生成器和模板来生成新文件。不喜欢我的 API？随意克隆 [Github repo](https://github.com/exyzzy/metaapi) ，改变模板，制作你自己的 CRUD！