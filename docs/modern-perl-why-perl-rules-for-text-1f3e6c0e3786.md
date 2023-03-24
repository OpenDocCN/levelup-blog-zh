# 现代 Perl:为什么 Perl 为文本制定规则

> 原文：<https://levelup.gitconnected.com/modern-perl-why-perl-rules-for-text-1f3e6c0e3786>

![](img/1968373b6b842924b42beb0d04c35f96.png)

由来自 [Pixabay](https://pixabay.com/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=1655783) 的[尼诺·卡雷](https://pixabay.com/users/ninocare-3266770/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=1655783)拍摄的特色图片

Perl 的*瑞士军队电锯*可能不是热门的新语言，但它仍然是文本处理的强大工具[。我最初学习 Perl 是为了以日志处理的形式处理文本。当我开始认真学习中文时，我用它来消化大量的文本，看看我应该把重点放在什么地方。CGI 和 Dancer 使我能够创建 web 应用程序，这真正启动了我目前的编程生涯。](https://medium.com/swlh/perl-in-2020-is-it-still-worth-learning-today-6c88fa435fb4)

现代 Perl 对文本如此有效有很多原因。它还支持更新的数据格式，以保持其相关性。Perl 并不是解决每个问题的最佳选择，但是在文本处理方面，它可以胜过许多语言。

# Perl 和文本

Perl 最初是作为一种通用语言创建的，用来帮助生成报告。报告功能需要文本提取和处理，以及处理和输出相关数据的简单方法。Perl 支持一行程序，具有特定于文本的函数，这些函数不一定在每种(通用)语言中都有，并且有一种适合处理数据的类型。

# Perl 一行程序

*一行程序*是小型语言语句，传统上可以从一行运行。这听起来没那么强大，但是想象一下，如果你想把一个 DOS 或 Windows 文件转换成 Unix 或 POSIX 格式，反之亦然。你不需要写一个完整的脚本，你只需要用一种特殊的方式运行一些语言。从 DOS 转到 POSIX 所要做的就是运行:

```
perl -pe 's/\r//g' myfile.dos > myfile.unix
```

这个简单的操作快捷简单，可以在任何有 Perl 的机器上运行。使用 Perl 也可以更快更有效地挖掘日志。就拿假日志 *fakelog* 来说吧:

```
WARN: Line 234 more than 140 characters, which is more than old Tweets could have
ERROR: There's a problem on line 234
PRINT: There are characters on line 234, but no semicolons
PRINT: Some junk no one wants
PRINT: More junk!!
WARN: Oh no, some stuff that doesn't hurt anything
ERROR: There's a big problem on line 234
PRINT: I'm a fake log!
WARN: Here's some problem that will affect you in 202020 if you don't patch me
PRINT: Line 234 ends with a newline
ERROR: Seriously, line 234, it's bad!
PRINT: Did you know that 'today' ends in 'y'?
```

这个日志有很多无用的行，这些行在查找 bug 时只是噪音。让我们过滤掉所有的错误:

```
perl -ne 'print if m/^ERROR/g' fakelog 
ERROR: There's a problem on line 234
ERROR: There's a big problem on line 234
ERROR: Seriously, line 234, it's bad!
```

好吧，我们知道“第 234 行”有一些东西，让我们看看日志中是否包括一些可以帮助进一步缩小范围的东西。我们只要跑:

```
perl -ne 'print if m/^ERROR/g' fakelog 
ERROR: There's a problem on line 234
ERROR: There's a big problem on line 234
ERROR: Seriously, line 234, it's bad!
```

通过使用这种方法，我们可以推断出我们的问题是第 234 行缺少分号或类似的内容。虽然这对于您将要处理的日志来说有点夸张，但是同样的过程也适用于现实生活中的数据。警告和标准调试通常没有多大意义，直到它们突然变得有意义。大量的主要问题归结为一些基本的调试或详细的输出，如果实际的错误不能解决问题，则指出实际问题和实际解决方案的方向。

尽管 Perl 是一种只写的语言，但就我在一行程序中所做的事情来看，它对我来说也是如此。我用 Perl 编写了尽可能多的项目，但是它提供的粘合剂最终在我的标准计算中表现得足够好，以至于它在许多方面远远超过了我的编程。当我可以直接使用 Perl 时，为什么要打开一个文本处理程序，点击 Ctrl+h 并找出内部正则表达式系统？

# Perl 正则表达式

当大多数编码应用程序实现 regex 时，它们在“正则表达式”和“Perl 正则表达式”(现在许多更新的工具称之为“高级正则表达式”)之间进行划分。Perl 正则表达式引擎非常强大。理论上，您可以用它编写一个基本的 XML 解析器(但不要这么做)。编写一个基本的解析器实际上是可行的(当规则相对可预测时)。

我们在前面的一行程序示例中使用了 Perl 的正则表达式，因为该引擎非常适合这项任务。我已经用一行程序和复杂的正则表达式的组合编写了几个[数据管理](https://somedudesays.com/2019/12/data-munging-reverse-engineering-a-format/)工具。像 [Lua](https://somedudesays.com/2019/12/lua-string-operations/) 这样的语言有正则表达式，但是它们的实现看起来像是 Perl 完整课程的第一课，Lua 被认为在这方面相当强大。这两个系统都不简单，但是 Perl 只是比大多数系统更进了一步，可以说更富于表现力。

# Perl 中的字符串处理

Perl 中的某些内置函数是有意义的。像 *chomp* 这样的东西使得处理用户数据变得轻而易举。有一种简单的方法可以去掉最后一行对传统输入没有贡献的新行。如果你想要的话，你必须在 Lua(和其他软件)中从头开始制作。

**Perl:**

```
#!/usr/bin/perl -l

require strict;

my $str = "junk\r\n";
chomp( $str );
print( "[" . $str . "]" );
```

**Lua:**

```
#!/usr/bin/lua5.1

function chomp( line )
	line = string.gsub( line, "\n*\r*\n*$", "" ) --in case we care about being cross platform
	return string.gsub( line, "\n$", "" )
end

local str = "test\n"

print( "Shortened str to: " .. chomp( str ) )
```

这只是生活质量的提高，但它也显示了开发商的努力方向。Perl 使文本工作变得轻而易举，而其他语言需要您构建越来越多的样板文件。当我需要构建一个解析器或报告过程时，Perl 是我的第一语言。

还有其他一些基本函数，如 *join* ，它们存在于 C#和类似的语言中，但不存在于其他语言中，如 Lua。

**Perl:**

```
#!/usr/bin/perl -l

require strict;

my @arr = ( "a", "b", "c" );
my @arr2 = ();
my @arr3 = ( "a", "b" );

print( join( " AND ", @arr ) );
print( join( " AND ", @arr2 ) );
print( join( " AND ", @arr3 ) );
```

**卢阿:**

```
#!/usr/bin/lua5.1

function join( table, jstring )
	local str = ""

	local tlength = #table

	if( tlength == 0 ) then
		return str
	end

	str = table[ 1 ]

	if( tlength == 1 ) then
		return str
	end

	for i = 2, tlength, 1 do
		str = str .. jstring .. table[ i ]
	end

	return str
end

local arr = { "a", "b", "c" }
local arr2 = {  }
local arr3 = { "a" }
local arr4 = { "a", "b" }

print( join( arr, " AND " ) )
print( join( arr2, " ANYTHING " ) )
print( join( arr3, " ANYTHING " ) )
print( join( arr4, " AND " ) )
```

我们的 Lua 函数是我能写的最轻的版本。我们不考虑整个事情不是一个表和许多其他简单的处理点。Perl 版本可以工作，Lua 版本是一个教学练习。像 C#这样的语言提供了一个 *join* 函数，但是对于类似的文本处理任务，它们的类型使它们比 Perl 稍弱一些。

# Perl 类型和习惯用法

Perl 在任何用例中都是弱类型的，但是静态类型的严格 Perl 和动态类型的普通 Perl 是有区别的。[弱类型](https://medium.com/swlh/static-typing-vs-dynamic-typing-83f0d8b82ef)意味着一个*标量*是一个*标量*，我们可以用一个*字符串*把一个 *int* 放进去。Perl 还允许我们做一些大多数语言不会做的事情。Perl 中的*“cat”+1 = 1*。字符串和数字之间的转换不会真的发生，它只是发生了。

Perl 的类型有助于数据处理，这是大多数语言所没有的。借助某些语言设计特性，它在许多方面更加直观。

例如，以下任何一项都是有效的:

```
if( [condition] ) { [do thing]; }
[do thing] if( [condition] );
[do thing] unless [condition];
```

这让我们在实践中得到一些东西，比如:

```
#!/usr/bin/perl -l

if( 1 + 1 == 2 ) { print( "1 + 1 = 2" ); }
print( "1 + 1 = 2" ) if( 1 + 1 == 2 );
print( "1 + 1 = 2" ) unless 1 + 1 > 2;
```

# Perl 与世界

借助 CPAN T21 的力量，Perl 拥有了它所需要的一切，包括 XML、JSON、CSV、SQL 等等。有多种不同类型的库来处理 CSV，从最普通的到最模糊的。XML 和 JSON 在 Perl 中也有很好的表现。标准的 SQL 类型和晦涩的数据库几乎都有与 Perl 交互的模块。

由于 Perl 在文本处理和正则表达式方面的其他优势，解析基于文本的格式通常很容易。我写过许多解析器，将人类可读的数据转换成其他类型的人类可读的数据，并因此获得了丰厚的报酬。在其他语言中需要几周时间的项目在 Perl 中可能需要几个晚上。一个一年 10，000 美元的程序被一些聪明的 Perl 脚本和开源软件所取代。

# 堆叠语言

Perl 是我的第一门真正的语言，从那以后一直是我最喜欢的语言。我继续前进，因为我不得不这样做，但语言是我对每一个新范式的比较。它并不完美，但它胜过了我曾经考虑过的用于文本处理的任何东西。

Lua 对某些事情更有用，但是 Perl 仍然是最好的通用脚本语言，在任何你不能远程控制已安装软件的地方。C#在 Windows 上更好，但是这种语言本身不需要太多的工作就能应付某些解析场景。C、C++和许多其他通用语言只是位于 Lua 之下。

没有哪种语言一定更好，有些只是更适合特定的任务。Perl 擅长文本处理，没有其他语言能与之匹敌。Perl 可能不适合每一个项目，但是我参与的几乎所有大型项目都是用它粘在一起的。一行程序可以节省您的时间，正则表达式让您感觉您处于计算的未来，尽管有许多报告说这种语言已经死亡。

*最初发表于*[T5【https://somedudesays.com】](https://somedudesays.com/2020/02/modern-perl-why-perl-rules-for-text/)*。*