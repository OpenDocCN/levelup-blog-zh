# 在 Node.js 模块中使用 TypeORM 和 TypeScript 进行数据持久化的完整指南

> 原文：<https://levelup.gitconnected.com/complete-guide-to-using-typeorm-and-typescript-for-data-persistence-in-node-js-module-bfce169959d9>

TypeORM 是一个运行在 Node.js 中的高级对象关系管理模块。在本文中，我们将了解如何使用 TypeORM 来设置实体对象以在数据库中存储数据，如何使用 CustomRepository 实例来操作数据库表，以及如何使用实体实例之间的关系来模拟数据库连接。

![](img/d4d213bdb792e019b2afb6f25a5b482f.png)

首先，您必须阅读本系列的前两篇文章:

1.  [选择 TypeScript vs JavaScript:技术，流行度](https://itnext.io/choosing-typescript-vs-javascript-technology-popularity-ea978afd6b5f)
2.  [如何用 Node.js 设置 Typescript 编译器和编辑环境](https://medium.com/@7genblogger/how-to-set-up-typescript-compiler-and-editing-environment-with-node-js-68952aebbe1d)(TechSparx 上也有[)](https://techsparx.com/nodejs/typescript/setup.html)
3.  [如何使用 Typescript 创建 Node.js 模块](https://techsparx.com/nodejs/typescript/modules-01.html)

*这篇文章摘自我的书* [*在 Node.js 应用程序中使用 Typescript 和 TypeORM 的快速入门*](https://www.amazon.com/gp/product/B07S87X4ZK/ref=dbs_a_def_rwt_bibl_vppi_i1)

在本文中，我们将为 Node.js 创建一个简单的 TypeScript 模块来处理应用程序在数据库中存储数据的问题。我们遵循的概念是，大学注册办公室需要一个数据库和相应的应用程序来存储有关学生和课程的数据。

因为我们使用的是 TypeScript，所以使用 TypeORM 来简化数据库管理是很自然的。我们将创建两个实体— *学生*和*提供的类* —每个实体都有一个对应的*自定义存储库*实例。CustomRepository 类将为数据库提供高级功能。

# 初始化模块 package.json 和测试目录

正如我们对所有 Node.js 项目所做的那样，我们首先使用`npm init`或`yarn init`来初始化目录，并进行其他初始化。

我们正在初始化的是一个模块，用于处理这个概念化的大学注册应用程序的数据库。我们稍后将创建代码，在这个阶段，我们只是在打基础。

创建一个目录`ts-example`，并在其中创建一个目录`registrar`。

```
$ mkdir ts-example
$ cd ts-example
$ mkdir registrar
$ cd registrar
$ npm init
.. answer questions
```

初始化`package.json`。

```
$ npm install @types/node --save-dev
$ npm install typescript ts-node --save-dev
$ npm install typeorm sqlite3 reflect-metadata --save
```

这些包中的大部分已经在前面的文章中讨论过了。`typeorm`包当然提供了 TypeORM 库。对于这个例子，我们将使用 SQLite3 来存储数据库，因此是`sqlite3`包。最后，TypeORM 需要`reflect-metadata`包。

创建一个名为`tsconfig.json`的文件，其中包含:

```
{
    "compilerOptions": {
        "lib": [ "es5", "es6", "es7",
                 "es2015", "es2016",
                 "es2017", "es2018",
                 "esnext" ],
        "target": "es2017",
        "module": "commonjs",
        "moduleResolution": "Node",
        "outDir": "./dist",
        "rootDir": "./lib",
        "declaration": true,
        "declarationMap": true,
        "inlineSourceMap": true,
        "inlineSources": true,
        "emitDecoratorMetadata": true,
        "experimentalDecorators": true
    }
}
```

这与“[如何使用 Typescript](https://techsparx.com/nodejs/typescript/modules-01.html) 创建 Node.js 模块”中的略有不同。这些参数意味着:

*   `target`行表示输出 ES2017 代码，我们需要它，因为该版本支持异步/等待功能。
*   `module`行描述将要使用的输出模块格式，`commonjs`匹配使用 CommonJS 模块格式的决定。
*   `moduleResolution`参数表示像 NodeJS 一样在`node_modules`目录中查找模块。
*   `outDir`参数表示将文件编译到指定目录中，`rootDir`参数表示将文件编译到指定目录中。
*   `declaration`和`declarationMap`参数表示生成申报文件。
*   `inlineSourceMap`和`inlineSources`表示在 JavaScript 源文件中生成源映射数据。
*   TypeORM 在生成代码时使用`emitDecoratorMetadata`和`experimentalDecorators`。

这意味着我们的源代码将在`lib`中，TypeScript 将把它编译成`dist`。

我们在`package.json`中有一点小小的改变:

```
{
  ...
  "main": "dist/index.js",
  "types": "./dist/index.d.ts",
  "type": "commonjs",
  "scripts": {
    "build": "tsc",
    "watch": "tsc --watch",
    "test": "cd test && npm run test"
  },
  ...
}
```

`build`脚本只是运行`tsc`来编译源代码。`test`脚本切换到`test`目录来运行测试套件。

这个包的`main`是生成的`index`模块，具体是`dist/index.js`。对于前面显示的`tsconfig.json`，TypeScript 源代码在`lib`目录中，因此模块的主接口应该在`lib/index.ts`中。TypeScript 编译器编译`lib/index.ts`到`dist/index.js`。`type`属性是 NodeJS 12.x 的新特性，它允许我们声明这个包中使用的模块种类。如果`dist/index.js`改为 ES6 模块格式，属性值将改为`module`。

`types`字段向世界声明这个模块包含类型定义。自动生成类型定义是一种很好的形式，前面显示的`tsconfig.json`文件就是这样做的，然后确保全世界都知道包含了类型定义。

# 设置测试目录

与应用程序代码一起创建单元测试套件是很有用的。单元测试有很多种方法，所以把这当成一个人的意见。

在`ts-example/registrar`目录中，创建一个名为`test`的目录，并初始化一个新的`package.json`。

```
$ mkdir test
$ cd test
$ npm init
.. answer questions
$ npm install chai mocha --save-dev
```

我们将使用 Chai 和 Mocha 来编写测试。因为我们已经配置了 TypeScript 来生成 CommonJS 模块，所以我们将以默认方式使用 Mocha。Mocha 目前支持测试 CommonJS 模块，而使用 Mocha 测试 ES6 模块需要经过几道关卡。

现在编辑`test`目录中的`package.json`，使其看起来像这样:

```
{
    "name": "registrar-test",
    "version": "1.0.0",
    "description": "Test suite for student registrar library",
    "main": "index.js",
    "scripts": {
        "pretest": "cd .. && npm run build",
        "test": "rm -f registrardb.sqlite && mocha ./index"
    },
    "author": "David Herron <david@davidherron.com>",
    "license": "ISC",
    "devDependencies": {
        "chai": "^4.2.0",
        "mocha": "^6.1.4"
    },
    "dependencies": {}
}
```

对于 Mocha 和 Chai 版本号，请使用最新版本。这里重要的是两个脚本。为了运行测试，我们使用了`mocha`命令，但是在运行测试之前，我们希望确保源代码被重新构建。因此`pretest`脚本转到父目录并运行构建脚本。

因为我们还没有编写测试代码，或者应用程序代码，所以我们还不能运行任何东西。耐心点，我们会在文章结束前进行测试。

# index.ts —注册模块的主要编程接口

打下基础后，我们可以开始编写代码来处理数据库。在`lib`目录下创建一个名为`index.ts`的文件。记住，`lib/index.ts`被编译成`dist/index.js`，并将作为这个模块的主入口点。

```
import "reflect-metadata";
import { createConnection, Connection } from "typeorm";
import { Student } from './entities/Student';
export { Student } from './entities/Student';
import { StudentRepository } from './StudentRepository';
export { StudentRepository } from './StudentRepository';
import { OfferedClassRepository } from './OfferedClassRepository';
export { OfferedClassRepository } from './OfferedClassRepository';
import { OfferedClass } from './entities/OfferedClass';
export { OfferedClass } from './entities/OfferedClass';var  _connection: Connection;export async function connect(databaseFN: string) {
    _connection = await createConnection({
        type: "sqlite",
        database: databaseFN,
        synchronize: true,
        logging: false,
        entities: [
            Student, OfferedClass
        ]
     });
}export function connected() { 
    return typeof _connection !== 'undefined'; 
}export function getStudentRepository(): StudentRepository {
    return _connection.getCustomRepository(StudentRepository);
}export function getOfferedClassRepository(): OfferedClassRepository {
    return _connection.getCustomRepository(OfferedClassRepository);
}
```

如果这看起来不太像，请考虑所有的功能都存在于这里导入的文件中。考虑如何构建覆盖大型数据库的 API。你想把所有的功能都集成到一个模块中吗？不，那一个模块会很笨重。相反，你应该把事情模块化，就像这里看到的。这里所有的都是管理数据库连接的函数。名为 *StudentRepository* 和 *OfferedClassRepository* 的类包含了相应表的 CRUD 操作。

这两个类是 TypeORM CustomRepository 类的实例。CustomRepository 本身是 Repository 的一个实例，提供了处理对应于 TypeORM 实体的数据库表的基本功能。

通常情况下，您会像这样使用基本存储库类:

```
const studentRepository = getRepository(Student);
```

但是我们想在我们的应用程序中添加一些自定义函数。因此，我们将实现所谓的*自定义存储库*类。 *getStudentRepository* 函数处理定制存储库实现的生成。

另一段重要的代码是建立数据库连接的 *connect* 函数。这应该在应用程序启动后立即调用。它只是使用`createConnection`初始化数据库连接。该函数使用一个描述符对象来连接实际的数据库，并进行其他配置。

TypeORM 当然支持多种数据库，为了简单起见，我们使用 SQLite3，因为它不需要设置数据库服务器。

`entities`数组是我们告诉 TypeORM 可用对象类型的方式。它创建一个匹配每个实体的数据库表，从实体字段定义表模式。

在这种情况下,`entities`数组包含稍后将定义的实体类。每当我们添加另一个实体类时，我们都必须记住更新这个文件，以便 TypeORM 知道这个实体。也可以传递一个通配符文件名，以便 TypeORM 自动获取所有实体。

如果你搞乱了`entities`配置，就有可能花很长时间来调试。TypeORM 会告诉你它找不到实体的元数据，而且不清楚为什么会这样。如果我们要像这里显示的那样做，并在`entities`数组中传递实体，那么我们必须小心。它必须是实体类引用，而不是包含实体类的模块。

# 创建类型实体类和 CRUD 方法

在上一节中，我们实现了注册数据库的主 API。但是我们引用了几个提供大部分功能的类。

在这个应用程序中，我们使用了 TypeORM 的两个主要特性:

1.  实体类-将简单的对象定义映射到数据库表
2.  自定义存储库类——用于操作数据库表的有用 API 函数

每个存储库类都与一个实体类相关联，因此它的函数操作与该实体相关联的表。通过实现一个定制的存储库类，我们将创建额外的功能。

# 类型实体

在 TypeORM 中，一个*实体*在一个 TypeScript 类和一个数据库表之间映射。通过在类定义的顶部添加`@Entity()`注释，然后为类中的每个字段添加一个`@Column`(或类似的)注释，可以创建一个实体。这些注释为 TypeORM 提供了设置数据库表所必需的信息。

看来我们不会给实体类添加任何函数。相反，我们简单地添加字段定义。在后台，TypeORM 必须设置 getter 和 setter 函数以及其他支持函数。

# 学生实体

在`lib`目录下创建一个名为`entities`的目录。我们将把所有实体类定义放在这个目录中。然后创建一个名为`lib/entities/Student.ts`的文件

```
import { 
    Entity, Column, PrimaryGeneratedColumn,
    ManyToMany, JoinTable
} from "typeorm";
import { OfferedClass } from './OfferedClass';@Entity()
export class Student {
    @PrimaryGeneratedColumn()  id: number;
    @Column({
        length: 100
    })  name: string;
    @Column("int")  entered: number;
    @Column("int")  grade: number;
    @Column()  gender: string; @ManyToMany(() => OfferedClass, oclass => oclass.students)
    @JoinTable()
    classes: OfferedClass[];
}
```

正如我们前面所说的,`@Entity()`说类将被 TypeORM 视为一个实体。为了让 TypeORM 识别实体，我们必须在连接描述符中配置`entities`数组，以便 TypeORM 能够找到实体类。

我们有五个字段:

*   `id`是学生表的主键
*   `name`是学生的名字
*   `entered`学生进入大学的年份
*   `grade`是他们目前所在的年级
*   `gender`表示他们是男性还是女性。这本来是一个名为`Gender`的`enum`，但是 SQLite3 似乎不支持枚举字段。

# TypeORM 中的实体关系—多对多

还有一个额外的字段`students`，需要更深入的解释。它有两个注解，`@ManyToMany`和`@JoinTable`。这是一个实体关系的例子，是 TypeORM 的特性之一。

对于实体关系，TypeORM 自动处理表之间的连接。在这种情况下，我们希望支持以下情况

1.  一个学生可以注册多门课程
2.  提供的课程可以有多个学生注册

换句话说，许多学生实例引用许多提供的类实例，反之亦然。这就是 TypeORM 所说的双向多对多关系。

这里的`@JoinTable`注释表明`Student`类是关系的所有者。在幕后，该注释导致创建一个表，即*连接表*，它帮助将*学生*和*提供的类*的实例连接在一起。

对于 ManyToMany，两个实体都需要`@ManyToMany`注释。现在让我们看看提供的类实体。

# 提供的类实体

显然，我们的学生会很高兴能够注册上课。到目前为止，我们只有一份学生名单，没有办法让他们报名参加任何活动。我们是哪种注册服务商？

为了纠正这一点，我们需要设置一个或多个附加表来保存关于类的信息。为此，我们可以拿出 Enterprise Architect 的副本，设计出一个完整的类层次结构。为了简洁起见，让我们做简单的事情。我们将添加一个名为*提供类*的实体，然后使用 TypeORM 注释来分配*学生*实例和*提供类*实例之间的关系。

创建名为`lib/entities/OfferedClass.ts`的文件

```
import { 
    Entity, 
    Column, 
    PrimaryColumn, 
    OneToMany,
    ManyToMany
} from "typeorm";
import { Student } from './Student';@Entity()
export class OfferedClass { @PrimaryColumn({
        length: 10
    }) code: string;
    @Column({
        length: 100
    })  name: string;
    @Column("int")  hours: number; @ManyToMany(type => Student, student => student.classes)
    students: Student[];
}
```

这个实体记录了大学提供的课程的数据。这些字段是:

*   `code`是班级编号，例如`CS101`
*   `name`是类的描述性名称，例如`Computer Science 101`
*   `students`是`@ManyToMany`关系的另一面

`students`字段上的`@ManyToMany`注释是学生实体中相应注释的另一半。多对多关系要求双方都声明。

# 自定义存储库类来管理学生和提供的类实体— CRUD 操作等等

现在我们已经定义了数据库实体，我们需要一些有用的函数来管理这些实体。正如我们前面所说的，开箱即用的 TypeORM 提供了一个默认的*存储库*类，其中包含了许多有用的函数。但是我们希望 RegistrarDB 提供更高的抽象层。

在`index.ts`中，我们已经声明将会有两个类， *StudentRepository* 和 *OfferedClassRepository* 。这些将是自定义存储库实例，提供我们想要的功能。

# StudentRepository —学生实体的 CRUD 操作

因此 *StudentRepository* 类将实现 CRUD 函数(创建、读取、更新和删除)来管理学生实例，以及一些其他有用的函数。

创建一个名为`lib/StudentRepository.ts`的文件:

```
import { 
    EntityRepository, Repository, getRepository 
} from "typeorm";
import { Student } from "./entities/Student";
import * as util from 'util';export type GenderType = "male" | "female";export enum Gender {
    male = "male", female = "female"
}@EntityRepository(Student)
export class StudentRepository extends Repository<Student> {
    ...
}
```

我们将添加更多的内容，但这只是开始的结构。我们从`typeorm`以及学生实体引进了一些东西。正如我们所说的，`Student`中的`gender`字段是一个名为 Gender 的枚举，我们已经在这里定义了它。

这个模块的主要特点是 StudentRepository 类。实现定制存储库的指令是以这种方式扩展`Repository`类的定义，并使用这里所示的`EntityRepository`注释。

通过扩展`Repository`类，`StudentRepository`类自动访问`Repository`类的函数。因此，我们的功能将建立在存储库 API 之上，并遵循该 API 设置的模式。

在`StudentRepository`类中添加这个函数:

```
async createAndSave(student: Student): Promise<number> {
    let stud = new Student();
    stud.name = student.name;
    stud.entered = normalizeNumber(student.entered, 'Bad year entered');
    stud.grade = normalizeNumber(student.grade, 'Bad grade');
    stud.gender = student.gender;
    await this.save(stud);
    return stud.id;
}
```

在 TypeORM 文档中建议使用名称`createAndSave`。顾名思义，这个函数处理创建一个学生对象，然后将其保存到数据库中。这是 CRUD 操作的第一步。

我们创建一个新的`Student`对象，以确保保存到数据库的对象只有在`Student`中定义的字段。传递的对象可以很容易地拥有其他字段，并且仍然与`Student`类型兼容。

设置好学生对象后，我们将它保存到数据库中。TypeORM 的作用是将它转换为已配置的底层数据库连接。

将这些函数添加到`StudentRepository`类中:

```
async allStudents(): Promise<Student []> {
    let students = await this.find();
    return students;
}async findOneStudent(id: number):
            Promise<Student> {
    let student = await this.findOne({ 
        where: { id: id }
    });
    if (!StudentRepository.isStudent(student)) {
        throw new Error(`Student id ${util.inspect(id)} did not retrieve a Student`);
    }
    return student;
}
```

在`findOneStudent`中，我们有下一个 CRUD 操作，从数据库中读取一个学生对象。`findOne`方法是搜索数据库以检索数据的一种方式。`where`子句让我们描述如何从数据库中选择我们想要检索的项目。

在`StudentRepository`类中添加这个函数:

```
async updateStudent(id: number, student: Student):
            Promise<number> {
    if (typeof student.entered !== 'undefined') {
        student.entered = normalizeNumber(student.entered, 'Bad year entered');
    }
    if (typeof student.grade !== 'undefined') {
        student.grade = normalizeNumber(student.grade, 'Bad grade');
    }
    if (!StudentRepository.isStudentUpdater(student)) {
        throw new Error(`Student update id ${util.inspect(id)} did not receive a Student updater ${util.inspect(student)}`);
    }
    await this.manager.update(Student, id, student);
    return id;
}
```

在`updateStudent`中，我们有下一个 CRUD 操作，更新一个学生对象。我们需要学生的 ID 来更新，然后是一个允许更新对象的对象。在 TypeORM 中，`update`方法让我们指定一个部分对象，它将更新已设置的字段。`isStudentUpdater`方法检查它是否有对更新学生对象有效的字段。

在`StudentRepository`类中添加这个函数:

```
async deleteStudent(student: number | Student) {
    if (typeof student !== 'number'
     && !StudentRepository.isStudent(student)) {
        throw new Error('Supplied student object not a Student');
    }
    await this.manager.delete(Student, 
            typeof student === 'number' 
                ? student : student.id);
}
```

在`deleteStudent`中，我们有最后一个 CRUD 操作，删除一个学生。我们既可以使用 ID 号，也可以使用 Student 对象(从中我们可以获得 ID 号)。

在`StudentRepository`类的主体之外添加这个函数:

```
export function normalizeNumber(
            num: number | string, errorIfNotNumber: string)
        : number {
    if (typeof num === 'undefined') {
        throw new Error(`${errorIfNotNumber} -- ${num}`);
    }
    if (typeof num === 'number') return num;
    let ret = parseInt(num);
    if (isNaN(ret)) {
        throw new Error(`${errorIfNotNumber} ${ret} -- ${num}`);
    }
    return ret!;
}
```

这个函数用在几个地方。根据数据源的不同，例如从 web 浏览器提交的表单，我们可能会收到一个实际上是数字的字符串。该功能通过使用`parseInt`来适应这种可能性。

# 用于学生的类型检查功能

TypeScript 不支持运行时类型检查。要进行运行时类型检查，匹配 TypeScript 提供的编译时类型检查，我们必须自己实现它。这是 TypeScript 与 Java 或 C#等语言的另一个区别。

为了实现运行时类型检查，TypeScript 希望我们实现名为*类型保护*的函数。这些函数接受一个对象，并且应该测试对象的特征以查看它是否是正确的类型，然后返回一个布尔指示符。

将这些静态函数添加到 StudentRepository 类中:

```
static isStudent(student: any): student is Student {
    return typeof student === 'object'
        && typeof student.name === 'string'
        && typeof student.entered === 'number'
        && typeof student.grade === 'number'
        && StudentRepository.isGender(student.gender);
}static isStudentUpdater(updater: any): boolean {
    let ret = true;
    if (typeof updater !== 'object') {
        throw new Error('isStudentUpdater must get object');
    }
    if (typeof updater.name !== 'undefined') {
        if (typeof updater.name !== 'string') ret = false;
    }
    if (typeof updater.entered !== 'undefined') {
        if (typeof updater.entered !== 'number') ret = false;
    }
    if (typeof updater.grade !== 'undefined') {
        if (typeof updater.grade !== 'number') ret = false;
    }
    if (typeof updater.gender !== 'undefined') {
        if (!StudentRepository.isGender(updater.gender)) ret = false;
    }
    return ret;
}static isGender(gender: any): gender is Gender {
    return typeof gender === 'string'
        && (gender === 'male' || gender === 'female');
}
```

还记得我们之前说过`gender`字段应该是一个`enum`字段。我们在顶部定义了`enum Gender`，函数`isGender`验证`string`是否与`enum Gender`中允许的值相匹配。

仔细看，你会发现`isGender`有一个返回类型`gender is Gender`。还有什么语言有这样的返回类型？这就是 TypeScript 所描述的*类型谓词*。TypeScript 文档对此没有进一步解释。但是从上下文中可以清楚地看出，“`object is Class`”谓词是用来表示单词所说的内容，意思是表示该对象与该类匹配。

`isStudent`功能检查一个对象，看它是否与`Student`对象的形状相匹配。当我们希望对象包含所有对应于 Student 的字段时，将会用到这一点。

相比之下,`isStudentUpdater`应该用在对象拥有学生类中的一些字段的环境中。在这两种情况下，我们都确定字段类型与学生类中的字段类型相匹配。

# OfferedClassRepository —对 OfferedClass 实体的 CRUD 操作和测试

为了遵循已经设置的模式，创建一个名为`lib/OfferedClassRepository.ts`的文件。这将处理 OfferedClass 表。

```
import {
    EntityRepository, 
    Repository, 
    getRepository 
} from "typeorm";
import { 
    OfferedClass 
} from './entities/OfferedClass';
import { 
    normalizeNumber, 
    StudentRepository 
} from './StudentRepository';
import { 
    getStudentRepository 
} from './index';
import * as util from 'util';
import * as yaml from 'js-yaml';
import * as fs from 'fs-extra'; @EntityRepository(OfferedClass)
export class OfferedClassRepository extends Repository<OfferedClass> {
    ...
}
```

这就像我们放在`StudentRepository.ts`顶部的一样，但是增加了一些内容。也就是说，我们正在导入`js-yaml`和`fs-extra`。我们将使用这些来读取班级数据库。要支持这些模块，请键入:

```
$ npm install --save js-yaml fs-extra
$ npm install --save-dev @types/js-yaml
```

我们将编写的第一个测试用例将使用来自 YAML 文件的类描述初始化 RegistrarDB。我们将遵循的理论是，注册器偶尔会添加一个类，删除一个类，或者改变一个类的某些内容。虽然他们可以从 CRUD API 中完成，但是他们也可以使用 YAML 文件。

在`OfferedClassRepository`类中添加这些函数:

```
async updateClasses(classFN: string) { const yamlText = await fs.readFile(classFN, 'utf8');
    const offered = yaml.safeLoad(yamlText); if (typeof offered !== 'object'
     || !Array.isArray(offered.classes)) {
        throw new Error(`updateClasses read incorrect data file from ${classFN}`);
    } let all = await this.allClasses();
    for (let cls of all) {
        let stillOffered = false;
        for (let ofrd of offered.classes) {
            if (ofrd.code === cls.code) {
                stillOffered = true;
                break;
            }
        }
        if (!stillOffered) {
            this.deleteOfferedClass(cls.code);
        }
    }
    for (let updater of offered.classes) {
        if (!OfferedClassRepository
                    .isOfferedClassUpdater(updater)) {
            throw new Error(`updateClasses found classes entry that is not an OfferedClassUpdater ${util.inspect(updater)}`);
        }
        let cls;
        try { 
            cls = await this.findOneClass(updater.code); 
        } catch (e) { cls = undefined }
        if (cls) {
            await this.updateOfferedClass(updater.code, updater)
        } else {
            await this.createAndSave(updater)
        }
    }

}static isOfferedClass(offeredClass: any): offeredClass is OfferedClass {
    return typeof offeredClass === 'object'
        && typeof offeredClass.code === 'string'
        && typeof offeredClass.name === 'string'
        && typeof offeredClass.hours === 'number';
}static isOfferedClassUpdater(updater: any): boolean {
    let ret = true;
    if (typeof updater !== 'object') {
        throw new Error('isOfferedClassUpdater must get object');
    }
    if (typeof updater.code !== 'undefined') {
        if (typeof updater.code !== 'string') ret = false;
    }
    if (typeof updater.name !== 'undefined') {
        if (typeof updater.name !== 'string') ret = false;
    }
    if (typeof updater.hours !== 'undefined') {
        if (typeof updater.hours !== 'number') ret = false;
    }
    return ret;
}
```

`updateClasses`函数读入一个 YAML 文件，该文件必须有一个`classes`数组。该数组将包含与`OfferedClassUpdater`模式匹配的对象。还有两种类型的保护功能，`isOfferedClass`和`isOfferedClassUpdater`用于测试对象。

因为 YAML 文件可以是任何东西，所以我们必须测试文件是否包含我们期望的内容，否则将抛出一个错误。

因为 YAML 文件中的对象可以是任何东西，我们没有任何类型信息来帮助。但这也是我们写`isOfferedClassUpdater`的原因。有了这个，我们可以测试来自 YAML 文件的对象。

第一阶段是检测 YAML 文件中没有列出的类实例。这样的课程将从课程目录中删除，因为它已经不存在了。我们通过循环所有现有的类来检测这些类，如果它没有在 YAML 中列出，那么我们知道它应该被删除。

第二阶段是根据 YAML 文件中的数据添加一个新的类，或者更新一个现有的类。

顺便说一句，我们本来打算单独测试这个函数，但是因为它引用了其他 CRUD 函数，所以我们必须实现它们。

在`OfferedClassRepository`类中添加这个函数:

```
async allClasses(): Promise<OfferedClass []> {
    let classes = await this.find({
        relations: [ "students" ]
    });
    return classes;
}
```

使用`allClasses`,我们检索所有提供的类实例。

带有`relations`的位告诉 TypeORM 加载关系中的数据，如果有的话。出于某种原因，TypeORM 似乎认为关系中的数据是可选的，并不总是需要加载。为了加载关系数据，您需要传递这个`relations`字段，该字段包含一个字符串数组，该数组指定了您想要加载的关系。

当加载一个`OfferedClass`时，我们希望总是加载学生关系，因此我们必须记住总是列出这个关系字段。

不同的是:

*   无`relations`:仅加载`code`和`name`和`hours`字段。
*   With `relations`:这些字段加上`students`字段是一个数组，包含与给定 OfferedClass 关联的学生。

在`OfferedClassRepository`类中添加这个函数:

```
async createAndSave(offeredClass: OfferedClass)
            : Promise<any> {
    let cls = new OfferedClass();
    cls.code = offeredClass.code;
    cls.name = offeredClass.name;
    cls.hours = normalizeNumber(offeredClass.hours, 'Bad number of hours');
    if (!OfferedClassRepository
            .isOfferedClass(cls)) {
        throw new Error(`Not an offered class ${util.inspect(offeredClass)}`);
    }
    await this.save(cls);
    return cls.code;
}
```

通过`createAndSave`,我们向数据库添加了一个新的 OfferedClass。这类似于 StudentsRepository 中的相同功能。

在`OfferedClassRepository`类中添加该函数:

```
async findOneClass(code: string)
        : Promise<OfferedClass> {
    let cls = await this.findOne({ 
        where: { code: code },
        relations: [ "students" ]
    });
    if (!OfferedClassRepository.isOfferedClass(cls)) {
        throw new Error(`OfferedClass id ${util.inspect(code)} did not retrieve a OfferedClass`);
    }
    return cls;
}
```

通过`findOneClass`我们找到一个代码名为的提供类。我们再次将`relations`字段添加到选项中，以确保并拉入关系数据。这类似于 StudentsRepository 中的对应函数。

在`OfferedClassRepository`类中添加该函数:

```
async updateOfferedClass(
                code: string,
                offeredClass: OfferedClass)
            : Promise<any> {
        if (typeof offeredClass.hours !== 'undefined') {
            offeredClass.hours = normalizeNumber(
                    offeredClass.hours, 'Bad number of hours');
        }
        if (!OfferedClassRepository
                .isOfferedClassUpdater(offeredClass)) {
            throw new Error(`OfferedClass update id ${util.inspect(code)} did not receive a OfferedClass updater ${util.inspect(offeredClass)}`);
        }
        await this.manager.update(OfferedClass,
                    code, offeredClass);
        return code;
    }
```

对于`updateOfferedClass`,我们采用一个`OfferedClassUpdater`对象，并使用它来更新数据库中的条目。TypeORM `update`函数负责根据设置或未设置的字段有选择地更新项目。这类似于 StudentsRepository 中的对应函数。

在`OfferedClassRepository`类中添加这个函数:

```
async deleteOfferedClass(
                offeredClass: string | OfferedClass) {
    if (typeof offeredClass !== 'string'
        && !OfferedClassRepository.isOfferedClass(offeredClass)) {
        throw new Error('Supplied offeredClass object not a OfferedClass');
    }
    await this.manager.delete(OfferedClass,
        typeof offeredClass === 'string'
                ? offeredClass : offeredClass.code);
}
```

使用`deleteOfferedClass`,我们试图删除一个 OfferedClass 实例。这类似于 StudentsRepository 中的对应函数。

接下来，我们想支持学生注册一个课程。

在`OfferedClassRepository`类中添加这个函数:

```
async enrollStudentInClass(
            studentid: any, code: string) {
    let offered = await this.findOneClass(code);
    if (!OfferedClassRepository
            .isOfferedClass(offered)) {
        throw new Error(`enrollStudentInClass did not find OfferedClass for ${util.inspect(code)}`);
    }
    let student = await getStudentRepository()
                        .findOneStudent(studentid);
    if (!StudentRepository.isStudent(student)) {
        throw new Error(`enrollStudentInClass did not find Student for ${util.inspect(studentid)}`);
    }

    if (!student.classes) student.classes = [];
    student.classes.push(offered);
    await getStudentRepository().manager.save(student);
}
```

第一部分验证我们已经得到了一个已知学生和一个已知班级的良好标识符。

然后，将学生添加到提供的班级就很容易了。只需将 OfferedClass 实例添加到 Student 对象的`classes`数组中，然后将其保存回数据库。在幕后，TypeORM 负责一切。

另一个期望的操作是一次在多个班级中注册学生。与其一次注册一个，不如接收一组课程代码并确保学生注册了每个课程代码，这样可能更有效。此外，如果该学生目前注册的班级不在该阵列中，我们应该将该学生从该班级中除名。

在`OfferedClassRepository`类中添加这个函数:

```
async updateStudentEnrolledClasses(
            studentid: any, codes: string[]) {
    let student = await getStudentRepository()
                        .findOneStudent(studentid);
    if (!StudentRepository.isStudent(student)) {
        throw new Error(`enrollStudentInClass did not find Student for ${util.inspect(studentid)}`);
    }
    let newclasses = [];
    for (let sclazz of student.classes) {
        for (let code of codes) {
            if (sclazz.code === code) {
                newclasses.push(sclazz);
            }
        }
    }
    for (let code of codes) {
        let found = false;
        for (let nclazz of newclasses) {
            if (nclazz.code === code) {
                found = true;
            }
        }
        if (!found) {
            newclasses.push(await this.findOneClass(code));
        }
    }
    student.classes = newclasses;
    await getStudentRepository().save(student);
}
```

遵循的方法是首先检索学生实例，然后操作学生注册的`classes`，最后保存学生。

我们创建一个空数组来保存学生注册的新班级。然后我们使用几个`for`循环来正确设置这个数组。首先，我们复制仍然列在类代码数组中的提供的类实例。在第二个例子中，我们传递可用类的数组，对于任何不在数组中的类，我们将它的 OfferedClass 实例推入数组。

结果是`newclasses`提供的类实例与我们得到的代码相对应。我们只需保存学生实例。

# 单元测试学生和提供的类实体

在`test`目录中，我们已经建立了 Node.js 项目的框架。

在该目录中，创建一个名为`index.js`的文件。这将包含一个摩卡/柴测试套件。因为我们想要测试注册器模块的 JavaScript 接口，所以测试套件是用 JavaScript 编写的。

```
const util = require('util');
const path = require('path');
const assert = require('chai').assert;
const { 
    connect, 
    connected,
    Student,
    getStudentRepository,
    StudentRepository,
    getOfferedClassRepository,
    OfferedClassRepository
} = require('../dist/index');describe('Initialize Registrar', function() {
    before(async function() {
        try {
            await connect("registrardb.sqlite");
        } catch (e) {
            console.error(`Initialize Registrar failed with `, e);
            throw e;
        }
    }); it('should successfully initialize the Registrar', async function() {
        assert.isTrue(connected());
    });
});
```

这就建立了所需的模块，并实现了一个初始测试用例。

因为`test`目录是`registrar`模块的子目录，我们可以像这里一样使用相对路径引用来加载模块。生成的源代码在`dist`目录中，因此我们从那里加载模块。

这里的`before`函数用于初始化 RegistrarDB 连接。没有什么需要测试的，因为我们所做的只是实例化数据库。因此，这个测试的主要目的是初始化数据库，但是它确实对数据库是否成功配置做了一点验证。

测试可以这样运行:

```
$ npm test> registrar@1.0.0 test /Volumes/Extra/ebooks/typescript-nodejs/examples/registrar
> cd test && npm run test > registrar-test@1.0.0 pretest /Volumes/Extra/ebooks/typescript-nodejs/examples/registrar/test
> cd .. && npm run build > registrar@1.0.0 build /Volumes/Extra/ebooks/typescript-nodejs/examples/registrar
> tsc > registrar-test@1.0.0 test /Volumes/Extra/ebooks/typescript-nodejs/examples/registrar/test
> mocha ./index Initialize Registrar
    ✓ should successfully initialize the Registrar 1 passing (13ms)
```

# 测试学生对象

让我们稍微充实一下测试套件。我们在本书中没有足够的空间来全面测试所有的东西，所以让我们只展示几个测试案例。

```
describe('Add students to registry', function() {
    let stud1 = {
        name: "John Brown", 
        entered: 1997, grade: 4,
        gender: "male"
    };
    let stud2 = {
        name: "John Brown", 
        entered: "trump1", grade: "senior",
        gender: "male"
    };
    let studentid1;
    let studentid2; it('should add a student to the registry', async function() {
        studentid1 = await getStudentRepository().createAndSave(stud1);
        let student = await getStudentRepository().findOneStudent(studentid1);
        assert.exists(student);
        assert.isObject(student);
        assert.isString(student.name);
        assert.equal(student.name, stud1.name);
        assert.isNumber(student.entered);
        assert.equal(student.entered, stud1.entered);
        assert.isNumber(student.grade);
        assert.equal(student.grade, stud1.grade);
        assert.isString(student.gender);
        assert.equal(student.gender, stud1.gender);
    }); it('should fail to add a student with bad data', async function() {
        let sawError = false;
        try {
            await getStudentRepository().createAndSave(stud2);
        } catch (err) {
            sawError = true;
        }
        assert.isTrue(sawError);
    });});
```

两个对象，`stud1`和`stud2`，分别代表肯定测试(预期成功)和否定测试(预期失败)。首先，我们调用`addStudent`，然后检索 Student 对象，然后对照该对象检查它的值。

在柴的断言中有一个非常有用的方法，`deepEqual`，在这里就更可取了。但是，从`getStudent(studentid1)`返回的对象是一个 TypeScript 对象。通过检查对象，我发现它有名为`_name`等的字段，您会记得这些字段是保存实际数据的私有字段。getter 方法未被识别为字段名，因此我们无法执行:`assert.deepEqual(student, stud1)`，因为字段名不匹配。

因此，我们最终使用单独的`assert.isNumber`和`assert.isString`检查。或者，我们可以简单地使用`isStudent`类型的 guard 来检查返回的对象是否如预期的那样。

在否定的测试用例中(*应该无法添加*)，我们会传入`stud2`。这个对象有字符串而不是数字字段，这些字符串不能转换成数字。`createStudent`函数检测到这个问题并抛出一个错误。我们捕获该错误，并将`sawError`标志设置为`true`，断言将会成功。如果没有抛出错误，标志保持`false`，断言将失败。

```
$ npm test> registrar@1.0.0 test /Volumes/Extra/ebooks/typescript-nodejs/examples/registrar
> cd test && npm run test> registrar-test@1.0.0 pretest /Volumes/Extra/ebooks/typescript-nodejs/examples/registrar/test
> cd .. && npm run build> registrar@1.0.0 build /Volumes/Extra/ebooks/typescript-nodejs/examples/registrar
> tsc> registrar-test@1.0.0 test /Volumes/Extra/ebooks/typescript-nodejs/examples/registrar/test
> mocha ./index Initialize Registrar
    ✓ should successfully initialize the Registrar Add students to empty registrar
    ✓ should add a student to the registrar
    ✓ should fail to add a student with bad data 3 passing (46ms)
```

在更多的工作之后，测试结果可能看起来像这样:

```
> registry-test@1.0.0 test /Volumes/Extra/ebooks/typescript-nodejs-quick-start/registrar/test
> rm -f registrardb.sqlite && mocha ./index Initialize Registrar
    ✓ should successfully initialize the Registrar Add students to registry
    ✓ should add a student to the registry
    ✓ should fail to add a student with bad data Update student in registry
    ✓ should update student (44ms)
    ✓ should fail to update student with bad data Delete student from registry
    ✓ should not fail to delete student using bad ID
    ✓ should delete student using good ID 7 passing (861ms)
```

# 测试提供的类实体

写了几个学生实体的测试之后，我们需要验证所提供的类实体。与学生实体不同，我们需要初始化一个提供的类对象列表。

第一步是创建一个 YAML 格式的数据文件。创建一个名为`test/students.yaml`的文件:

```
classes:
  - code: BW101
    name: Introduction to Basket Weaving
    hours: 3
  - code: BW102
    name: Underwater Basket Weaving
    hours: 3
  - code: BW103
    name: Basket Weaving while Sky Diving
    hours: 3
  - code: BW201
    name: Basket Weaving Fundamentals
    hours: 3
  - code: BW202
    name: Historical Basket Weaving
    hours: 3
  - code: BW203
    name: Development of Modern Basket Weaving
    hours: 3
  - code: BW301
    name: Topics on Contemporary Basket Weaving
    hours: 3
  - code: BW302
    name: Basket Weaving Theory
    hours: 3
  - code: BW303
    name: Basket Weaving and Graph Theory
    hours: 3
  - code: BW401
    name: Advanced Basket Weaving
    hours: 3
  - code: BW402
    name: Basket Weaving Research Practicum
    hours: 3
```

这个数据文件包含一个对篮子编织感兴趣的假想大学的班级列表。

然后添加这个测试组:

```
describe('Initialize Offered Classes in registry', function() {
    before(async function() {
        await getOfferedClassRepository()
            .updateClasses(path.join(__dirname, 'classes.yaml'));
    }); it('should have offered classes', async function() {
        let classes = await getOfferedClassRepository()
                            .allClasses();
        assert.exists(classes);
        assert.isArray(classes);
        for (let offered of classes) {
            assert.isTrue(OfferedClassRepository
                        .isOfferedClass(offered));
        }
    });
});
```

`before`函数将 YAML 文件中的任何内容插入数据库。初始化我们的测试数据。我们终于可以实现大学时代的梦想，在课程目录上看到*水下篮子编织*。

在测试用例中，我们读取所有的类，并验证所有的东西确实是一个提供的类。

```
> rm -f registrardb.sqlite && mocha ./index Initialize Registrar
    ✓ should successfully initialize the Registrar Add students to registry
    ✓ should add a student to the registry
    ✓ should fail to add a student with bad data Update student in registry
    ✓ should update student
    ✓ should fail to update student with bad data Delete student from registry
    ✓ should not fail to delete student using bad ID
    ✓ should delete student using good ID Initialize Offered Classes in registry
    ✓ should have offered classes 8 passing (369ms)
```

酷，我们已经验证了所提供的类实例存在并且具有正确的类型。现在，我们需要测试将学生添加到班级和其他地方。

```
let stud1 = {
    name: "Mary Brown", 
    entered: 2010, grade: 2,
    gender: "female"
};
let studentid1;it('should add student to a class', async function() {
    studentid1 = await getStudentRepository()
                        .createAndSave(stud1);
    await getOfferedClassRepository()
            .enrollStudentInClass(studentid1, "BW102");
    let student = await getStudentRepository()
            .findOneStudent(studentid1);
    assert.isTrue(StudentRepository
                .isStudent(student));
    assert.isArray(student.classes);
    let foundbw102 = false;
    for (let offered of student.classes) {
        assert.isTrue(OfferedClassRepository
                    .isOfferedClass(offered));
        if (offered.code === "BW102") foundbw102 = true;
    }
    assert.isTrue(foundbw102);
});
```

和前面一样，我们有一个静态对象，可以从中创建一个学生。然后我们给学生注册一个班级。然后我们再次检索学生，并检查它的`classes`数组以确保她正在取 BW102。

在一些额外的测试用例开发之后，我们有:

```
> rm -f registrardb.sqlite && mocha ./index Initialize Registrar
    ✓ should successfully initialize the Registrar Add students to registry
    ✓ should add a student to the registry
    ✓ should fail to add a student with bad data Update student in registry
    ✓ should update student
    ✓ should fail to update student with bad data Delete student from registry
    ✓ should not fail to delete student using bad ID
    ✓ should delete student using good ID Initialize Offered Classes in registry
    ✓ should have offered classes
    ✓ should add student to a class
    ✓ should add student to three classes
    ✓ should show students registered in class 11 passing (372ms)
```

*本文原载于*[*TechSparx*](https://techsparx.com/nodejs/typescript/typeorm.html)*。*

[](https://skilled.dev) [## 编写面试问题

### 一个完整的平台，在这里我会教你找到下一份工作所需的一切，以及…

技术开发](https://skilled.dev)