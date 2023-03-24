# 我是如何把整个公司锁在亚马逊 S3 桶外的

> 原文：<https://levelup.gitconnected.com/how-i-locked-the-whole-company-out-of-an-amazon-s3-bucket-1781de51e4be>

## 我学到了什么

## 要避免的 S3 政策反模式；以及如何打开一个 S3 桶

![](img/a80576034c37740db3c542299b03c8ea.png)

美国国家航空航天局、欧空局和 STScI 拍摄的船底座星云

最近，我为我的一个客户做了一个安全策略的回顾。在这个过程中，我设法不小心把自己和公司里的每个人都锁在了一个 S3 桶外面。这不是一个普通的桶，不，这是一个装有所有客户媒体文件的桶。想象一下，你不得不要求你所有的客户重新上传他们所有的照片、视频等…

# 要避免的反模式 S3 政策

当我将下面的策略附加到 S3 桶时，让我们看看我做错了什么。

将使存储桶完全锁定的反模式 S3 策略声明。

将上述策略附加到 S3 时段会将所有人锁定在该时段之外。在我们进入细节之前，让我先告诉你两件重要的事情:

*   如果有可能的话，尝试一个[无效*无效、*](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_notprincipal.html) 无效。如果你决定使用，它很可能会在你最意想不到的时候回来咬你！
*   有一种方法可以修复它。这实际上很容易，但是你需要根访问键。在文章的最后，我展示了需要做些什么来修复锁定的 bucket。

现在让我们仔细看看上面的 S3 政策，以及为什么它是有问题的。

# 非主体反模式

简而言之，示例策略旨在阻止任何人对数据(对象)的访问，但除了拥有角色`ROLE_NAME`的用户或服务之外，桶本身也是如此。不幸的是，我们的示例策略没有正确地指定这个意图，即使假设角色也不会授予用户访问权限。

乍一看，如果您试图用 IAM 用户访问 bucket，在他们承担了角色之后，访问将被授予。不会的。该操作(无论是什么)将被拒绝。即使用户具有 *Admin* 访问权限或具有 *S3FullAccess* 的策略，他们也会被拒绝。

问题在于“颠倒的逻辑”，即双重否定方法，在这种情况下最终总是正确的。对于 IAM 角色， *NotPrincipal* 被证明是有问题的，因为当角色由用户或服务承担时，角色的主体 ARN 值会略有变化。特别是，AWS 安全令牌服务(STS)将会话 ID 附加到角色 ARN 中。查看以下两个命令的输出:

```
$ aws sts get-caller-identity
{
  "UserId": "UNIQUE_ROLE_ID:SESSIONID"
 "Arn"**:** "arn:aws:sts::111111111111:assumed-role/**ROLE_NAME/SESSID**"
}$ aws iam get-role --role-name ROLE_NAME
{
  "RoleName": "ROLE_NAME",
  "RoleId": "UNIQUE_ROLE_ID",
  "Arn": "arn:aws:iam::111111111111:role/**ROLE_NAME**",
  "AssumeRolePolicyDocument": {...}
}
```

我们可以看到，角色的 ARN 不同于 IAM 调用者(用户或服务)向存储桶发出请求的 ARN。角色 ARN 是 IAM 角色本身的标识符，假定角色 ARN 是日志中标识角色会话的标识符。人们可能想在 *NotPrincipal 中使用通配符。*然而，这是不允许的，而且理由很充分。

所以我首先应该做的就是不要使用 *NotPrincipal。*相反，我应该使用带有 *aws:userId，*的*条件*，如下面的代码片段所示。

最佳实践 S3 策略使用 aws:userId 而不是 NotPrincipal。

需要注意的是，在我们的条件中，我们没有使用角色的 ARN，而是使用其唯一的*角色 Id* 来指定期望的角色*。*为了检索*角色 Id* ，我们可以运行`$ aws iam get-role --role-name ROLE_NAME`。人们可以很容易地识别角色的惟一 ID，因为在 AWS 中，所有角色的惟一 ID 都带有前缀`AROA`。眼尖的读者也会注意到，我们必须在角色的唯一 ID 后面加上`:*`。原因是 STS 还会将一个会话 ID 附加到角色的 ID 上，如上面的示例输出所示。

# 怎么修

虽然在上述策略下，不允许任何用户或服务对 bucket 或其对象执行任何操作，但是有一个例外。是的，它是根帐户，总是被允许查看和删除 S3 安全策略。

> 解锁不可访问桶的唯一可靠方法是实际删除有问题的 S3 安全策略。为此，只需运行以下命令:

```
$ aws s3api delete-bucket-policy --bucket BUCKET --profile ROOT
```

如果通过环境变量管理凭证，可以省略`--profile`参数。要将凭证添加到 env，可以运行:

```
$ export AWS_DEFAULT_REGION= us-east-1 
$ export AWS_ACCESS_KEY_ID=ROOT_KEY_ID
$ export AWS_SECRET_ACCESS_KEY=ROOT_SECRET_KEY
```

# 结束语

在本文中，我们研究了用于编写 S3 安全策略的反模式以及它可能导致的问题。我们还了解了如何修复这些问题，前提是用户有权访问 root 帐户凭据。然而，我们首先应该明确避免撰写糟糕的 S3 政策。以下是撰写 S3 保单时的一些(非常概括和简洁的)最佳实践:

*   在*动作*和*原则*中，尽量避免使用“*”或“s3:*”等通用通配符，不要用*条件*缩小语句的范围 a。
*   尽量避免 *NotPrincipal。*几乎总是可以用一个*条件*和 aws:u *serId* 或 *aws:PrincipalArn* 来替换。
*   尝试使用 IAM 访问分析器。它远非完美，但在某些情况下非常有用。尤其是当一个人必须处理许多策略和资源时。