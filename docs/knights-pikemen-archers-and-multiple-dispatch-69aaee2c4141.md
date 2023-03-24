# 骑士、枪兵、弓箭手和多重派遣

> 原文：<https://levelup.gitconnected.com/knights-pikemen-archers-and-multiple-dispatch-69aaee2c4141>

![](img/c677af6a6273afc2af284d4591f3b48f.png)

## 面向程序员的 Julia 介绍

厌倦了以银行账户、雇员和雇主为特征的编程示例？我也是！让我通过编码一场骑士、长枪兵和弓箭手之间的战斗，向你介绍一些 Julia 编程语言的独特之处。

我们的目标是对 Julia 编程语言做一个简单的介绍，主要集中在使 Julia 成为有趣而强大的语言的各个方面。

在我们能走之前，我们得先爬，所以让我先简单介绍一下朱莉娅·REPL(阅读-评估-打印-循环)。[安装 Julia](https://julialang.org/downloads/) 并在 unix 风格的 shell 中启动它:

```
$ julia
              _
  _       _ _(_)_     |  Documentation: https://docs.julialang.org
 (_)     | (_) (_)    |
  _ _   _| |_  __ _   |  Type "?" for help, "]?" for Pkg help.
 | | | | | | |/ _` |  |
 | | |_| | | | (_| |  |  Version 1.2.0 (2019-08-20)
_/ |\__'_|_|_|\__'_|  |  Official https://julialang.org/ release
|__/                   |

julia> println("hello world")
hello world
```

## 字符串连接和插值

让我们对文本字符串做一些更有趣的事情。我们将看看变量是如何与字符串结合在一起的。

```
julia> engine = "RD-180";
julia> company = "Energomash";
julia> thrust = 3830;

julia> string("The ", engine, " rocket engine, produced by ", company, " produces ", thrust, " kN of thrust")
"The RD-180 rocket engine, produced by Energomash produces 3830 kN of thrust"
```

这是一个字符串插值的例子。您可以看到语法是受 unix shell 的启发。

```
julia> println("The $company $engine produce $thrust kN of thrust")
The Energomash RD-180 produce 3830 kN of thrust
```

在 Julia 中,`$`有各种各样的相关用法。当从 Julia 调用 shell 命令时，也可以使用它。

```
julia> run(echo The $company $engine produce $thrust kN of thrust)
The Energomash RD-180 produce 3830 kN of thrust
Process(`echo The Energomash RD-180 produce 3830 kN of thrust`, ProcessExited(0))
```

## 功能

在 Julia 中，你可以写一个函数，就像在数学中一样:

```
julia> f(x) = 3x + 2
julia> f(1)
5
```

还要注意 Julia 支持文字系数。`3x`与`3*x`相同。当你有多行代码时，你需要标记函数的开始和结束。

```
function f(x)
   3x + 2
end
```

Julia 是一种动态类型语言，所以变量没有类型，值有类型。

但是，您可以根据输入类型定制函数行为。实际调用什么函数实现仍然是在运行时决定的。我们把函数的每个实现都称为方法。是的，我知道这让人们对面向对象语言感到奇怪。

```
hello(x::Number) = 3x
hello(name::AbstractString) = "Hi $name"
hello(x::Bool) = !x
```

让我们尝试一下:

```
julia> hello(3)
9

julia> hello("Bilbo")
"Hi Bilbo"

julia> hello(false)
true
```

## 类型

在朱莉娅的作品中，我们主要有两种类型:

*   抽象类型
*   具体类型，分为位类型和复合类型。

我们将首先制作一些复合类型来表示一个骑士。

```
struct Knight {            mutable struct Knight
   string name;               name::String
   int health;                health::Int
   int armour;                armour::Int
};                         end
```

左边是 C++版本，右边是 Julia 版本。注意，在 Julia 中，我们只使用冒号`;`来分隔同一行中的多个语句。换行符也分隔语句。

对于外面的花括号爱好者，很抱歉我们没有在朱莉娅。在 C++ `{ ... }`中形成了一个代码块。在朱莉娅中，我们使用`begin ... end`。然而，当我们启动一个程序块作为某些语句的一部分时，如`struct`、`if`、`while`和`for`，就会省略`begin`。

注意我们如何在`struct`前面写`mutable`。这是因为像许多其他现代语言一样，Julia 倾向于默认使事物不可变。除非你有充分的理由，否则你应该选择不可变的数据类型。

在我们的例子中，可变性是实用的。我们正在处理一个特殊的骑士，并希望能够修改这个骑士的健康，因为他在战斗中受伤。

让我们创建一个数据类型来代表我们将要模拟的每一种士兵。为了简单起见，我们将只记录他们的健康状况。当生命值为零或更低时，我们的士兵就会死亡。

```
mutable struct Archer
   health::Int
end

mutable struct Pikeman
   health::Int
end

mutable struct Knight
   health::Int
end
```

这就是我们如何在朱莉娅中创造这些士兵的一些例子。

```
julia> archer = Archer(4)
Archer(4)

julia> pikeman = Pikeman(5)
Pikeman(5)

julia> knight = Knight(6)
Knight(6)
```

我们给骑士最好的健康，因为我们认为他是最好的训练，食物和装甲。

## 多重分派和函数重载

我们将模拟每种士兵之间的战斗，类似于石头、布、剪刀的比赛:

*   弓箭手将击败枪兵，因为他们移动缓慢，当他们逼近弓箭手时，弓箭手将设法射下他们中的大多数。
*   然而骑士会击败弓箭手，因为他们移动迅速。弓箭手无法击中快速移动的目标，而且在骑士包围并砍倒他们之前，他们无法射出许多箭。
*   另一方面，枪兵会击败骑士，因为楔形矛会阻止马匹攻击。

因此，谁对谁造成伤害完全取决于我们面对的是哪种士兵组合。

## 单一派遣的问题

动态面向对象语言在解决这个问题时会有问题，因为它们就是我们所说的单一分派。下面是一个尝试用 Python 解决这个问题的例子。首先我们定义`Archer`类。

```
class Archer:
    def __init__(self, health):
        self.health = health

    def fight(self, opponent):
        if type(opponent) == Pikeman:
            opponent.health -= 4
            if opponent.health <= 0:
                print("Archer killed pikeman")
        elif type(opponent) == Knight:
            opponent.health -= 2
            if opponent.health <= 0:
                print("Archer killed knight")
                return

            self.health -= 6
            if self.health <= 0:
                print("Knight killed archer")
```

然后我们定义`Pikeman`类。

```
class Pikeman:
    def __init__(self, health):
        self.health = health

    def fight(self, opponent):
        if type(opponent) == Archer:
            opponent.fight(self)
        elif type(opponent) == Knight:
            opponent.health -= 4
            if opponent.health <= 0:
                print("Pikeman killed knight")
```

对于这个演示，我们并不真正需要 Knight 的完整实现，所以我们将使它变得简单。

```
class Knight:
    def __init__(self, health):
        self.health = health
```

我们可以启动 Python REPL(读取-评估-打印-循环)来测试这段代码:

```
>>> pikeman = Pikeman(5)
>>> archer = Archer(4)
>>> knight = Knight(6)
>>> archer.fight(pikeman)
>>> archer.fight(pikeman)
Archer killed pikeman
>>> archer.fight(knight)
Knight killed archer
```

虽然我们得到了这个工作，这个解决方案的问题是，添加一个新类型的士兵将需要修改每个类的`fight()`方法的代码。精心制作的`if-else`声明会很快失控。

## 函数重载的问题

让我们探索用支持函数重载的静态类型语言解决这个问题，比如 C++。

```
#include <iostream>

struct Archer {
   int health;
};

struct Pikeman {
   int health;
};

struct Knight {
   int health;
};

using namespace std;
```

现在我们可以更好地定义士兵类型的每种组合会发生什么。

```
void fight(Archer *a, Pikeman *b) {
  b->health -= 4;
  if (b->health <= 0) {
      cout << "Archer killed pikeman" << endl;
  }
}

void fight(Archer *a, Knight *b) {
  b->health -= 2;
  if (b->health <= 0) {
      cout << "Archer killed knight" << endl;
      return;       
  }

  a->health -= 6;
  if (a->health <= 0) {
      cout << "Knight killed archer" << endl;
  }
}
```

这是一个创造一些士兵并让他们在`main`战斗的例子。

```
int main (int argc, char const *argv[]) {
   Pikeman pikeman = {5};
   Archer archer = {4};
   Knight knight = {6};

   fight(&archer, &pikeman);
   fight(&archer, &pikeman);
   fight(&archer, &knight);
   return 0;
}
```

因此，这个解决方案看似可行，看似优雅。有什么问题？

这是对问题的完全不切实际的描述。在任何真实的游戏中，你都不知道每个玩家在编译时会有什么样的士兵。玩家将在运行时创造不同种类的士兵。

因此实际上我们只知道他们是某种士兵，但不知道是哪种。让我们更准确地模拟这个问题。这将需要对`struct`定义进行一些修改。

我们引入了一个新的基类，现在每个人都需要一个构造函数，因为我们不再有普通的旧结构，而是类。

```
struct Soldier {
   int health;

   Soldier(int health) : health(health) {}    
};

struct Archer : public Soldier {
   Archer(int health) : Soldier(health) {}
};

struct Pikeman : public Soldier {
   Pikeman(int health) : Soldier(health) {}
};

struct Knight : public Soldier {
   Knight(int health) : Soldier(health) {}
};
```

现在让我们改变测试代码，这样士兵的精确类型是未知的。

```
int main (int argc, char const *argv[]) {
   Pikeman pikeman(5);
   Archer archer(4);
   Knight knight(6);

   Soldier *a = &archer;
   Soldier *b = &pikeman;

   fight(a, b);
   fight(a, b);

   b = &knight;
   fight(a, b);
   return 0;
}
```

但是，如果我们试图编译它，就会遇到麻烦。我们得到的错误消息之一是:

```
note: candidate function not viable: cannot convert from base class pointer 'Soldier *' to derived class pointer 'Archer *' for 1st argument
void fight(Archer *a, Pikeman *b) {
```

基本上，它是在告诉我们，C++编译器对我们试图用一个`Soldier`类型的变量调用`fight`函数并不感冒，因为它不知道调用哪个特定的重载函数。这种东西必须在编译时决定。当您使用函数重载时，在运行时无法选择正确的函数。

我们可以通过添加一个将`Solider`类型作为输入的函数来演示这一点。

```
void fight(Soldier *a, Soldier *b) {
   cout << "Could not pick right overloaded function" << endl;
}
```

这编译了 find，但是当我们运行这个程序时，对`fight`的三个调用产生了这个输出:

```
Could not pick right overloaded function
Could not pick right overloaded function
Could not pick right overloaded function
```

显然，我们不能在运行时选择合适的函数。

## 茱莉亚多重派遣救援

所以我们看到，像 Python 这样的动态面向对象语言不能令人满意地解决这个问题，静态类型语言也不能。朱莉娅能做得更好吗？

为了与 C++版本进行比较，我们将把我们的类型修改为抽象类型`Soldier`的子类型。与 C++不同，抽象类型不能保存数据。

```
abstract type Soldier end

mutable struct Archer <: Soldier
   health::Int
end

mutable struct Pikeman <: Soldier
   health::Int
end

mutable struct Knight <: Soldier
   health::Int
end
```

为了与 C++版本进行比较，我们可以实现一个以`Solider`类型作为参数的`fight`函数。

```
function fight!(a::Soldier, b::Soldier)
   error("You have not registered a method for fight!($(typeof(a)), $(typeof(b))")
end
```

注意`fight!`中的感叹号，这是标记函数的 Julia 约定，它改变或变异它们的输入。这是由 LISP 和 Ruby 推广的一种约定。

这里有一个针对各种组合的`fight!`实现。我们提供了一个更完整的实现，同时也考虑了同类型士兵之间的战斗。

`Archer`是第一个参数的情况。

```
function fight!(a::Archer, b::Pikeman)
  b.health -= 4
  if b.health <= 0
      println("Archer killed pikeman")
  end 
end
```

请注意与骑士互动的小变化。我们让弓箭手在靠近拱门之前对骑士造成一些伤害，并造成更多伤害。

```
function fight!(a::Archer, b::Knight)
  b.health -= 2
  if b.health <= 0
      println("Archer killed knight")
      return       
  end

  a.health -= 6
  if a.health <= 0
      println("Knight killed archer")
  end 
end

function fight!(a::Archer, b::Archer)
  a.health -= 2
  b.health -= 2
  if a.health <= 0 && b.health <= 0
      println("Archers killed each other")
  elseif a.health <= 0 || b.health <= 0
      println("One archer was killed")
  end 
end
```

`Pikeman`是第一个参数的情况。

```
fight!(a::Pikeman, b::Archer) = fight!(b, a)

function fight!(a::Pikeman, b::Pikeman)
  a.health -= 4
  b.health -= 4
  if a.health <= 0 && b.health <= 0
      println("Pikemen killed each other")
  elseif a.health <= 0 || b.health <= 0
      println("One pikeman was killed")
  end 
end

function fight!(a::Pikeman, b::Knight)
  b.health -= 4
  if a.health <= 0
      println("Pikeman killed cavalry")
  end 
end
```

`Knight`是第一个参数的情况。

```
fight!(a::Knight, b::Archer) = fight!(b, a)
fight!(a::Knight, b::Pikeman) = fight!(b, a)

function fight!(a::Knight, b::Knight)
  a.health -= 4
  b.health -= 4
  if a.health <= 0 && b.health <= 0
      println("Knights killed each other")
  elseif a.health <= 0 || b.health <= 0
      println("One knight was killed")
  end 
end
```

如果我们在朱丽亚·REPL 中运行这个，我们得到这些结果:

```
julia> pikeman = Pikeman(5)
Pikeman(5)

julia> archer = Archer(4)
Archer(4)

julia> knight = Knight(6)
Knight(6)

julia> fight!(archer, pikeman)

julia> fight!(archer, pikeman)
Archer killed pikeman

julia> fight!(archer, knight)
Knight killed archer
```

换句话说，Julia 能够在运行时选择正确的函数实现。在 Julia 术语中，每个不同的函数实现称为一个方法。对于习惯于面向对象编程的人来说，这是对单词 method 的一种不寻常的使用。

但确实有一定道理。在 OOP 中，我们也在运行时分派给一个特定的方法。所以在 Julia 术语中,`fight!`是一个函数，而`fight!(a::Pikeman, b::Archer)`是一个方法定义。

Julia 会根据需要自动创建函数。但是你可以在 Julia 中显式地创建一个函数。这将创建`fight!`函数。

```
function fight! end
```

但是如果没有相应的方法，这将是毫无用处的。

## 元编程

下一个让你领略 Julia 威力的话题是元编程。这是一个更大的主题，所以我们在这里只讨论代码生成。

你可能注意到每个士兵类型都是一样的。如果我们添加更多属性，如`armor`和`name`，这种重复将变得乏味。

Python 或 C++开发人员可以通过将成员变量放入基类来解决这个问题。但是 Julia 没有可以保存数据的基类。

相反，我们可以使用代码生成。

```
for T in [:Archer, :Pikeman, :Knight]
   @eval mutable struct $T <: Soldier
      health::Int
      damage::Int 
   end
end
```

我们使用 for 循环来迭代包含树符号`:Archer`、`:Pikeman`和`:Knight`的数组中的每个元素。符号类似于字符串。`"Archer"`是字符串，而`:Archer`是符号。符号主要用于表示程序代码中的标识符。当键不是面向用户时，它们也经常被用作字典中的键。

`@eval`是宏，对代码表达式求值。你几乎可以把接下来的看作是一个包含被求值的代码的字符串。就像你可以用美元符号`$`在字符串中插入值一样，你也可以在代码中这样做。

`$T`用于将`Archer`、`Pikeman`和`Knight`连续放入定义`Soldier`子类型的表达式中。

与用 Python 或 C++解决同样的问题相比，这可能看起来很复杂。但是你必须记住这是一个更加通用和强大的特性，它允许你做更多的事情，而不仅仅是避免打出相似的复合类型。你也可以用它来生成看起来非常相似但有细微变化的函数。

它是 Python decorators 的一个更强大的版本，几乎可以用来做 decorators 做的任何事情。