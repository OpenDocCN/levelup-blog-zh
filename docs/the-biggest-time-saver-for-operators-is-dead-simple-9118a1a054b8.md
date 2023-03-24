# 比别名好得多——Shell 函数加快了您的工作速度

> 原文：<https://levelup.gitconnected.com/the-biggest-time-saver-for-operators-is-dead-simple-9118a1a054b8>

## Shell |效率|脚本|工具| K.I.S.S. |节省时间

你有化名，但你与它们的局限性抗争？你用不同的参数定期重复命令？你输入 SQL 查询来检查你的数据？那么这将大大加快你的速度！还有你的团队！

![](img/d966b2b97cbe33e969813996d540e7fb.png)

卡尔·海尔达尔在 [Unsplash](https://unsplash.com/collections/8953605/computer?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片

# 知识共享系统

生活中最美好的事情就是简单和自由。虽然下面的解决方案不是最漂亮、最现代或最具革命性的——但它非常有效，而且非常简单。我已经用了 10 多年了，它节省了大量的时间，同时让我可以轻松快速地处理请求或检查数据。

过去，我负责一个支付系统，有大量预定义的 SQL 查询来快速从数据库中获取数据，以验证错误、检查账户、支付……甚至触发操作。我不再每次都输入查询，而是在几秒钟内就能得到结果，并且查询集迅速增长，成为一个复杂的命令行管理界面。最大的好处是:我能够与团队分享这些功能，每个人都可以使用无需解释的功能，这些功能可以通过 tab 补全来实现。

# 利益

在你想开始写你自己的函数之前，让我们看看它的好处。

*   在终端中，函数名是自动用制表符补全的，因此您不必全部记住。
*   函数可以调用公共函数、处理(可选)参数、运行多个命令、请求确认等等。基本上任何 shell 脚本能做的事情(别名不能)。
*   你可以轻松地与同事分享一组功能，例如通过 git。
*   外壳函数支持开箱即用。
*   也可以，但对我来说，它有不同的使用情形。

# 用三个简单的步骤自己尝试一下

# 步骤 1:为你的函数创建一个文件

创建一个以`.sh`结尾的文件，并添加您的第一个函数

```
# Hello world!
function demo.helloWorld() {
  if [ -z "$1" ]; then
    echo "Hello World!"
  else
    echo "Hello $1!"
  fi
}
```

保存它，然后在终端输入`source my-functions.sh`——在你当前的 shell 中加载函数。用`demo<tab><tab>`试试看。

要在打开终端时自动包含您的功能，请将`source $HOME/my-functions.sh`添加到您个人目录中的`.bashrc`、`.zsh`或`.profile`。

# 步骤 2:定义命名方案

要利用制表符补全来浏览您的函数，请定义一个符合您需要的命名方案。这里有一些例子可以让你更好的理解。

## **案例 1:数据库查询**

您的案例是查询用户数据库。作为输入，你通常会收到一封电子邮件，用户 id，交易 id，…

```
# 1\. <namespace/project> = mydb
# 2\. <input1> = email
# 3\. <output> = user
mydb.email.user foo@bar.com
mydb.email.user.events foo@bar.com
mydb.email.user.transaction/payments/logs/actions/logins/...# 1\. <namespace/project> = mydb
# 2\. <input1> = user uuid
# 3\. <output> = user
mydb.userid.user 2fa71c31-a48a-47f3-8f65-1172eb0839ae
mydb.userid.user.events/...
```

这里的目的是从你所拥有的开始，例如，我有一个错误报告，并获得了用户或交易的 UUID，或者…现在我想获得相关的数据和记录，如帐户、支付方式、事件…所以当我键入`mydb.userid.<tab><tab>`时，可用的功能应该会显示出来。

查询数据库的实际“代码”可以像这样简单:

```
devdb="psql --username=admin --expanded"function mydb._query() {
  db=$1
  query=$2
  echo "$query" | $devdb $db
}# Get user by UUID
function mydb.userid.user() {
  [...]
  mydb._query user "SELECT * FROM user WHERE id='$1'"
}
```

一旦掌握了窍门，您可能会想到各种非常有用且更复杂的查询(连接、子查询),其输出特定于您的需求。

**要点是**:当你需要它的时候写一次查询(并且知道它会再次被需要)，随着时间的推移重用和改进它，节省大量手工输入连接或多个查询的时间。让 wiki 页面充满有文档记录的查询也已经节省了时间，但是它远没有那么灵活，并且您仍然必须搜索查询并将其复制到 SQL 控制台中…

## **案例 2: GitHub 问题和存储库**

从控制台管理 git 问题、分支、拉请求……因为您的开发周期可能是独立的。

```
# 1\. <tool> = git
# 2\. <object> = issue
# 3\. <action> = create
git.issue.create "Add feature B"
git.issue.branchPush 42 # create a branch from issue and push
git.issue.createPullRequest 23 # create PR from issue
git.issue.close/comment/...
```

您的函数可以使用`hub`与 GitHub API 通信:

```
# Normalize a title so it can be used as branch name
function git.branch.normalizeName() {
  [ -z "$1" ] && {
    echo "Usage: $0 <some issue title>"
    return
  }
  echo "$1" | tr -d "'\`" \
    | sed -E 's#[\. \"\*\#\!\$\?~\^:\\]#-#g' \
    | sed -E 's#-+#-#g' \
    | sed -E 's#^[-/]##' \
    | sed -E 's#[-/](.lock)?$##'
}# Git function: create and push a branch
function git.branch.createPush() {
  if [ -z "$1" ]; then
    echo "Usage: $0 <branch name>"
    return
  fi
  git branch "$1" && \
  git checkout "$1" && \
  git push --set-upstream origin "$1"
}# GitHub function: create and push a branch from an issue
# to be run in a github project directory
function git.issue.branchPush() {
  if [ -z "$1" ]; then
    echo "Usage: $0 <github issue no>"
    return
  fi
  issue=$(hub issue show $1 | head -n1)
  if [ $? -ne 0 -o -z "$issue" ]; then
    echo "Issue $1 not found"
  elif [ -n "$(echo "$issue" | grep "CLOSED")" ]; then
    echo "Issue $1 is closed"
  else
    name="$1-$(git.branch.normalizeName $issue)"
    git.branch.createPush $name
  fi
}
```

## **案例 3:你的项目管理**

你能想到在你的项目中你经常触发的任务和行动吗？不属于你的发展管道？你的同事也经常执行的任务？你上一次想“要是有个小工具能帮我摆脱这种无脑的复制粘贴任务就好了……”是什么时候？或者听起来更像是“我已经有很多事情要做，现在我不得不浪费时间在数据库中搜索这个愚蠢的支持案例的交易…”

为什么不在项目中添加一组功能来帮助完成这些事情，并在每次使用时节省时间。

命名方案:`myproj.<subsystem>[.<input1>[.<input2>].<output/action>`

```
PROJECTPATH=$HOME/git/myproject
DB_CMD="docker exec -it myproj-db -- psql myproj"
REDIS_CMD="docker exec -it myproj-redis -- redis-cli"# start up my local instance
function myproj.start() {
  cd $PROJECTPATH
  docker-compose up
}# in case of k8s, get pod by labels
function myproj.userPod() {
  kubectl -n myproj get pod -l mykey=value \
    -o jsonpath="{ .items[0].metadata.name }"
}
function myproj.dbPod() {
  kubectl -n myproj get pod -l component=database \
    -o jsonpath="{ .items[0].metadata.name }"
}# and use the pod function in other functions
function myproj.user.shell() {
  kubectl -n myproj exec -it $(myproj.userPod) -- /bin/bash
}
function myproj.db.shell() {
  kubectl -n myproj exec -it $(myproj.dbPod) -- psql
}# dump user records
function myproj.db.dumpUsers() {
  echo "SELECT * FROM user;" | $DB_CMD
}
function myproj.db.trxId.userId() {
  echo "SELECT user_id FROM transaction WHERE id='$1';" | $DB_CMD
}# show redis keys for user ID
function myproj.redis.get() {
  $REDIS_CMD get "user:$1"
}
# list redis keys by pattern
function myproj.redis.pattern.list() {
  $REDIS_CMD --scan --pattern "'$1'"
}# get everything we know about a transaction for debugging/review
function myproj.db.trxId.dumpAll() {
  userid=$(myproj.db.trxId.userId $1)
  [ -n "$userid" ] && myproj.db.userid.user $userid
  myproj.db.trxId.orders $trxid
  myproj.db.trxId.events $trxid
  myproj.db.trxId.transaction $trxid
}
```

你可以想象，唯一的限制就是你自己的创造力。考虑到您将键入查询五个具有两个连接的表所需的所有查询…每次有人要求您研究一个问题时…想象一下您使用一个函数节省了多少时间。

## 命名有什么帮助

如前所述，使用制表符补全时，命名很方便。如果您没有完全定义您的命名方案，也不用担心，因为用户可以通过使用“tab”键简单地“探索”所有功能。

```
$ myproj.<tab><tab>
myproj.db.dumpUsers        myproj.db.trxId.userId
myproj.redis.pattern.list  myproj.db.trxId.dumpAll
myproj.redis.get           myproj.start$ myproj.db.<tab><tab>
myproj.db.dumpUsers      myproj.db.trxId.dumpAll  myproj.db.trxId.userId
```

# 疯狂一把

叶，已经这样了。剩下你要做的就是编写你自己的函数，无论何时你遇到一个经常重复的，或者耗时的任务。

## 关于常用函数的一些想法

一旦您的收藏增加，您会注意到重复的命令或相同问题的解决方案。以下是您可能希望考虑为其创建通用函数的一些内容:

*   参数检查:一个检查你是否得到了所有需要的参数和/或它们是否有效的功能。如果没有给出参数，应该打印参数的“用法”信息，以帮助探索函数。
*   可重用函数:一个实际执行数据库查询的函数，所有其他函数都使用它。也就是说:在彼此的基础上构建函数，这样您就可以自己在函数中重用基本命令。
*   一种功能，用于检查环境中是否有缺失的命令，并在用户确认后有选择地安装这些命令。有点像`brew doctor`。
*   解析 ID 的简单助手函数(即通过事务 ID 获取用户 ID，反之亦然)，因为您可以使用它们在函数中获取查询某些数据所需的 ID。
*   利用函数附带的变量，设置有用的默认值，但允许它们被覆盖:`VAR=${VAR:-”default”}`
*   您正在进行大量的字符串操作，还是更喜欢用 python 来编写脚本？那么为什么不从 shell 函数中运行 python 脚本呢？
*   向 slack/discord/…您最喜欢的 web hook 端点发送通知。
*   用`curl`做一个 HTTP 请求，可以选择打印标题

# 摘要

*   每个人都可以即时访问 shell 函数——不需要任何先决条件(除非您需要一个 macOS/Linux/WSL 终端)。例如，通过 git，团队可以很容易地共享功能。
*   您可以像在 SQL 控制台中编写查询一样快地编写一个新函数，那么为什么不使它可重用并随着时间的推移改进/扩展它呢？
*   当与允许您“探索”功能树的功能命名方案相结合时，Tab 补全非常方便——对于新同事或长假后。
*   漂亮吗？不。有关系吗？号码
*   这很简单，可以节省你的时间去关注重要的事情。
*   保持简单和具体。如果你需要更多，时间会证明的。
*   将您的 shell 函数与其他文章中的技巧结合起来，例如这个关于有用的 k8s 命令的故事来自function myproj.user.multipleQueries() {
    # build queries with simple strings to DRY
    subquery="SELECT DISTINCT user_id FROM user WHERE type='special'"
    query1="SELECT * FROM message WHERE user_id IN ($subquery)"
    query2="SELECT * FROM transaction WHERE user_id IN ($subquery)"
    myproj.db.query "$query1"
    myproj.db.query "$query2"
    }## scale deployments
    function myproj.scale() {
    [ -z "$1" ] && {
    echo "Usage: $0 <replicas>"
    return
    }
    kubectl -n myproj scale deployment $(myproj.user.deployment) \
    --replicas $1
    }## change log level
    function myproj.logLevel() {
    loglevel=${1:-"DEBUG"}
    index=$(kubectl -n myproj get deployment $(myproj.user.deployment) -o jsonpath="{ .spec.template.spec.containers[0].env|map(.name == "LOGLEVEL")|index(true)') kubectl -n myproj patch deployment $(myproj.user.deployment) --type=json -p='[{"op":"replace", "path":"/spec/template/spec/containers/0/env/'$index'/value", "value": "'$loglevel'"}]'
    }## tail logs of multiple pods
    function myproj.user.tailLogs() {
    pods=$(kubectl -n user get pod -l component=service \
    -o jsonpath="{ .items[*].metadata.name }") trap 'kill $(jobs -p)' TERM INT
    for p in $pods; do
    kubectl -n kube-system logs --tail 10 -f $p &
    done
    wait
    }## YOUR CONTRIBUTION
    function mycrazy.func() {
    # what would you put here?
    }

    GitHub 上的例子:

    *   基于`app.kubernetes.io/instance`标签的 k8s 功能

    GitHub Gist 上可用的函数示例