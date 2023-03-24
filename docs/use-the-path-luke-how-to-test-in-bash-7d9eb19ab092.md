# 走小路路克！如何在 bash 中进行测试

> 原文：<https://levelup.gitconnected.com/use-the-path-luke-how-to-test-in-bash-7d9eb19ab092>

在本文中，我将展示如何 stub 入 bash，这是编写正确测试的关键。

# 为什么是巴什？

简而言之，因为它具有普遍性。如果您需要编写一个跨团队的脚本，那么 bash 是一个安全的选择。任何其他语言都会给不经常使用它的开发人员增加一些摩擦。Bash 是稳定的，它总是在那里。

最好的例子是 git 挂钩。如果您希望团队中的所有开发人员都使用这个钩子，您将很难说服他们安装另一个工具链来执行它，除非您使用 bash。

# 为什么要测试？

根据我的经验，bash 中的脚本往往非常脆弱。我没有一个明确的答案，但我认为主要原因是剧本非常间接。也就是说，他们现在在这个背景下解决了一个问题。但是没有检查当环境改变时会发生什么。这就是测试至关重要的地方。

> 在这种情况下，Bash 脚本现在解决了一个问题。但是没有检查当环境改变时会发生什么。这就是测试至关重要的地方。

我们可能永远也不想在 master 中提交，但是知道在这种情况下 git 钩子将会运行是很重要的，为此，我们需要 stub。

![](img/9234376734a32a8e70fc07b57e855621.png)

# 如何存根

存根实际上非常简单，我们只需要使用路径。

PATH 变量只是一个用冒号分隔的路径列表:

```
PATH=/usr/local/bin:/bin:...
```

它告诉 bash 在哪里寻找可执行文件。列表中的位置越靠前，就越重要，正如我们在下一个示例中看到的:

```
PATH=$(pwd)/bar:$PATH
PATH=$(pwd)/foo:$PATH
mkdir bar
mkdir foo
echo 'echo 1' > foo/a
echo 'echo 2' > bar/a
chmod +x foo/a
chmod +x bar/a
a
> 1
PATH=$(pwd)/bar:$PATH
a
> 2
```

# 真实的例子

让我们继续看一个实际的例子，将提交链接到票据。吉拉说，如果你和一个问题追踪者一起工作，你的票很可能会有一个类似 GLEE-313 的 id，其中 GLEE 是团队的代码，313 是票的号码。这些标签很可能有一个详细的描述，甚至可能包括图片，关于手边的特性或 bug。

如果您正在遵循类似于 git flow 的任何东西，那么很可能您有一个分支名称的约定，您首先写票 id，然后是一个简短的描述，例如“GLEE-313-we-need-to-test-this”。然而，分支只是指针，真正持久的是提交，所以我们希望分支上的所有提交都引用标签。比如以“【GLEE-313】”开头。这显然是 git 挂钩的一个例子，您可以在每次编写提交消息时键入票据代码，但是该信息已经存在于分支名称中。

经过一番谷歌搜索，我们发现正确的钩子是“prepare-commit-msg ”,并且`git rev-parse --abbrev-ref HEAD`返回分支名称。所以让我们写第一个版本:

```
#!/bin/bash
BRANCH_NAME=$(git rev-parse --abbrev-ref HEAD)
if [ -z "$BRANCH_NAME" ]; then
    echo "Not a git repo"
    exit 1
fi## Get code from branch
CODE=$(echo $BRANCH_NAME | sed -E "s/^([A-Z]+-[0-9]+).*/\1/")
sed -i -e "1s/^/[$CODE] /" $1
```

请注意，我们没有收到提交的文本，而是收到了文件“$1”。Git 将提交的初稿写到一个文件中，并将文件名处理给钩子。然后，我们只需在该文件上应用 sed 来预先计划我们的代码。

我们可能会多次尝试这个脚本，并试图说我们已经完成了。我们甚至检查如果 git 不返回分支名称会发生什么！然而，我们确定这就是结局吗？

*   如果分行没有代码会怎样？我们可能会在提交消息中添加“[]”，但我们不希望这样。
*   如果提交已经有了代码，会发生什么？我们很可能会得到重复的身份证。修改或应用提交时会发生这种情况。
*   合并提交会发生什么？我们可能会留下来自 git 的自动消息，因为它已经包含了分支名称。此外，我们的 git 存储库或其他工具可能会假设合并消息遵循标准模式。

在我们试图解决这些问题之前，我们需要设置测试环境。

# 测试 git 挂钩

在前面的例子中，我们执行了几个命令:git、echo、sed、[ (yes [是命令，你甚至可以做`man '['`)。

> *你知道* `*[*` *是一个程序，别名为* `*test*` *？一个人甚至可以做*`*man [*`*`*ll '/usr/bin/['*`*。这就是为什么在“[”后面总是需要一个空格。另一方面，* `*[[*` *是 bash 中的语法标记。**

*从这些中，有一个明显的异常值:`git`。Git 给我们“上下文”,而其他人提供“逻辑”。如果我们想测试这个脚本，我们需要 stub git，让它返回我们提到的不同情况。*

*在我们继续测试之前，让我们后退一步，想想这是否是正确的方法。在一个合适的编程环境中，我们会将函数分成两个不同的部分。一个用于获取分支名称，另一个用于修复提交，它们将进入脚本的库文件夹。然后主文件将两个组件粘合在一起。然而，如果我们想在 bash 中像这样组织代码并对其进行测试，我们需要将脚本分割成多个文件，这又会在采用过程中产生摩擦。*

*回到我们的测试，让我们首先考虑我们在存根中需要什么。因为我们知道 git 已经返回了分支名称，所以我们只需要创建一个简单的脚本来返回我们想要的分支名称。我们还需要能够在测试中修改 git 存根的行为。以下满足两个条件:*

```
*#!/bin/bash
## File named git which contains our git mock## Read the fake branch name from a file, so that test code can change the output of git
cat test/.test-branch*
```

*测试将由两部分组成，一个分支和一个提交消息。所以我们的测试跑步者看起来像:*

```
*#!/bin/bash## Make sure that our mock overrides git
PATH=$(pwd)/test:$PATHTOTAL_FAILURES=0# $1 is the branch name
# $2 is the commit text
# $3 is the expected commit after modification
function testCase () {
    echo "$1" > ./test/.test-branch
    echo "$2" > ./test/.test-commit
    ./prepare-commit-msg-hook.sh ./test/.test-commit RESULT=$(cat ./test/.test-commit)
    if [ "$RESULT" != "$3" ]; then
        TOTAL_FAILURES=$(($TOTAL_FAILURES + 1))
        echo "Expected $3 got $RESULT"
    fi
}## Yikes we are testing in bash!!
testCase GLEE-313-my-first-branch "Such an awesome commit" "[GLEE-313] Such an awesome commit"ANSI_RESET='\e[39m'
ANSI_RED='\e[31m'
ANSI_GREEN='\e[32m'if [ "$TOTAL_FAILURES" -eq 0 ]; then
    echo -e ${ANSI_GREEN}Success!!${ANSI_RESET}
else
    echo -e  ${ANSI_RED}Failure. $TOTAL_FAILURES tests failed.${ANSI_RESET}
fi
exit $TOTAL_FAILURES*
```

*在前一个脚本中有两个关键行。将会用我们的模拟内部测试文件夹替换原来的 git。`echo "$1" > ./test/.test-branch`修改我们存根的输出。*

*如果我们执行测试，我们会看到它按预期工作:*

```
*./test.sh
> Success!!*
```

*我们准备添加那些棘手的案例:*

```
*testCase my-branch-has-no-code "Sad commit" "Sad commit"
testCase GLEE-313-my-first-branch "[GLEE-313] Commit to be amended, so it already has code" "[GLEE-313] Commit to be amended, so it already has code"
testCase GLEE-313-my-first-branch "Merge branch master into 'GLEE-313-my-first-branch'" "Merge branch master into 'GLEE-313-my-first-branch'"
> Expected Sad commit got [my-branch-has-no-code] Sad commit
> Expected [GLEE-313] Commit to be amended, so it already has code got [GLEE-313] [GLEE-313] Commit to be amended, so it already has code
> Expected Merge branch master into 'GLEE-313-my-first-branch' got [GLEE-313] Merge branch master into 'GLEE-313-my-first-branch'
> Failure. 3 tests failed.*
```

*这可不好，三个案子都失败了。但是我们现在有测试，所以更容易修复。经过一番激烈的讨论，这是最终版本:*

```
*#!/bin/bash
BRANCH_NAME=$(git rev-parse --abbrev-ref HEAD)
if [ -z "$BRANCH_NAME" ]; then
    echo "Not a git repo"
    exit 1
fiif [[ ! "$BRANCH_NAME" =~ ^[A-Z]+-[0-9]+ ]]; then
  ## Branch is not following team convention. This could happen for example in master. Nothing to be done
  exit 0
fiCOMMIT=$(cat $1)
if [[ "$COMMIT" =~ ^\[[A-Z]+-[0-9]+\] ]]; then
  ## There is already some code in the commit. For example from amending or applying. Nothing to be done.
  exit 0
fiif [[ "$COMMIT" =~ ^Merge[[:space:]]branch[[:space:]] ]]; then
  ## Let's not modify automated merge messages
 exit 0
fi## Get code from branch
CODE=$(echo $BRANCH_NAME | sed -E "s/^([A-Z]+-[0-9]+).*/\1/")
sed -i -e "1s/^/[$CODE] /" $1*
```

*四项测试都通过了。*

# *总结*

*如果我们修改 PATH 变量，我们可以让 bash 脚本使用所选程序的存根。这允许我们检查我们的程序在不同场景下的行为，这是编写健壮脚本的关键。*

*如果你想通过测试检查脚本的最终版本，你可以在 [github](https://github.com/furstenheim/git-hooks) 中查看。*