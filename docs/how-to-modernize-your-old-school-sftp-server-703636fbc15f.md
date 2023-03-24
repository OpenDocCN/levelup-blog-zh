# 如何更新您的老式 SFTP 服务器

> 原文：<https://levelup.gitconnected.com/how-to-modernize-your-old-school-sftp-server-703636fbc15f>

![](img/c95f7bd49adc77f182bf931ebd029d1d.png)

乔里斯·维瑟在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

甚至在 15 年前，当有人谈到服务器时，他们指的是数据中心的一个硬件。“专用托管”和“协同定位”是更受欢迎和众所周知的术语。快进到今天，世界是一个非常不同的地方。硬件服务器变成了 VM，VM 变成了 Virtuozzo 容器(Docker 的前身)，然后我们到了 Docker。与上述发展类似，我们提供服务的方式也发生了变化。除了托管自己的服务，您还可以利用 Paas(平台即服务)、Iaas(基础设施即服务)、Saas(软件即服务)等等。

如果你读过我以前的文章，如[如何像工程师一样思考](https://medium.com/dev-genius/how-to-think-like-an-engineer-3b53fb42ac13#7757)，你会知道如果定制/自我管理工具是手头工作的最佳候选，我会很好。不过，通常情况下，现有的工具/回购/产品能更好地完成这项工作。对于自托管服务和 Saas 产品，我也有同感。托管我自己的服务会带来开销，如修补、更新、保护、调整、扩展等。与 Saas 产品相反，公司会担心开销，而我会使用服务。即使 Saas 产品是有价格的，想想你不用继续维护“另一项服务”所节省的时间。很多时候，Saas 产品比自主托管便宜很多。

我最近开始使用的最喜欢的 Saas 产品之一是 AWS 的 transfer family。对于不熟悉这项服务的人:

> AWS 传输系列为直接进出亚马逊 S3 的文件传输提供全面的管理支持。支持安全文件传输协议(SFTP)、基于 SSL 的文件传输协议(FTPS)和文件传输协议(FTP)

概括地说，AWS 将为您托管一个使用 S3 作为后端的 SFTP 服务器！事实上，他们将为你托管一个 SFTP 服务器本身是有价值的，但事实上，所有的文件最终在 S3 也是令人兴奋的。大多数 AWS 服务已经自然地与 S3 集成。一旦文件在 S3，你的 Lambdas，Instances，StepFunction，_ _ _ _ _[你最喜欢的服务]可以抓取文件并处理它们。因为文件在 S3，所以认证是通过标准 IAM 进行的，而您的代码/服务甚至不知道或不关心 SFTP 服务器从那里获得了文件。

最后但同样重要的是，因为这是一项 AWS 服务，所以设置可以完全地形化/自动化。你能从 SFTP 服务器设置中要求更多吗？

如果您准备放弃旧的 SFTP 服务器，让我们来看看如何使用 Terraform 提供 AWS SFTP 服务器及其依赖项。到此结束时，你不仅会有一个 SFTP 服务器，而且它将是完全自动化的，它将能够支持尽可能多的用户。

# 策略

这个计划很简单。我们将有一个单一的 SFTP 服务器和 S3 桶，由所有用户共享。我们用户的主目录将是`${var.s3_bucket_name}/${var.username}`。我们将定义权限，以便每个用户只能访问其主目录中的内容。

为此，我们将创建两个代码集:

1.设置 SFTP 服务器的代码。这将包括以下所有地形资源:

*   aws_transfer_server — SFTP 服务器资源
*   iam_role —允许服务器记录流量的角色
*   iam_policy —允许服务器记录流量的策略
*   aws_s3_bucket — s3 bucket 将为我们的用户存放所有主目录。
*   aws_route53_record —为我们的服务器创建一个 DNS 友好主机名(例如 sftp.domain.com)

2.为 SFTP 访问每个用户实例化一次的模块。

*   aws _ transfer _ user 在 SFTP 服务中创建用户
*   aws_transfer_ssh_key —定义将用于身份验证的 ssh 密钥
*   aws_s3_bucket_object —在上面定义的 SFTP s3 存储桶中创建特定于用户的主目录
*   aws_iam_role —定义用户在其主目录中的权限
*   aws_iam_role_policy —定义用户在其主目录中的权限

最终结果是一个完全自动化的设置。它将能够根据需要容纳尽可能多的用户，并且易于更新。如果你使用的是 Terraform 0.13，你将能够采取额外的步骤来保持你的代码干燥。还将提供 Terraform 0.12 的方法。

## SFTP 服务器

如上所述，使用 Terraform 和 AWS Transfer 系列创建我们的 SFTP 服务器只需要 4 种资源。第五个可选资源用于创建 DNS 条目。自定义 DNS 条目不是必需的，因为 AWS 会代表您创建一个 DNS 记录，但是告诉您的客户更清楚:

*   "转到 sftp.domain.com "

和...相对

*   "请转到 s-1 x1 x1 x1 x1 x1 x1 x1 x1x 11 x . server . transfer . us-east-1 . Amazon AWS . com "

让我们进入代码，然后解释我们在做什么。

```
resource "aws_transfer_server" "sftp" {
  identity_provider_type = "SERVICE_MANAGED"

  logging_role = aws_iam_role.sftp-logging.arn

  tags = {
    Terraform  = "true"
  }
}

resource "aws_iam_role" "sftp-logging" {
  name = "sftp-logging-role"

  assume_role_policy = <<EOF
{
    "Version": "2012-10-17",
    "Statement": [
        {
        "Effect": "Allow",
        "Principal": {
            "Service": "transfer.amazonaws.com"
        },
        "Action": "sts:AssumeRole"
        }
    ]
}
EOF

  tags = {
    Name       = "sftp-transfer-logging-role"
    Terraform  = "true"
  }
}

resource "aws_iam_role_policy" "sftp-logging" {
  name = "sftp-logging-policy"
  role = aws_iam_role.sftp-logging.id

  policy = <<POLICY
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
                "logs:CreateLogStream",
                "logs:DescribeLogStreams",
                "logs:CreateLogGroup",
                "logs:PutLogEvents"
            ],
            "Resource": "*"
        }
    ]
}
POLICY

}

resource "aws_s3_bucket" "sftp" {
  bucket = "sftp-homedirs"

  server_side_encryption_configuration {
    rule {
      apply_server_side_encryption_by_default {
        sse_algorithm = "AES256"
      }
    }
  }

  tags = {
    Name       = "sftp-homedirs"
    Terraform  = "true"
  }
}

resource "aws_route53_record" "sftpserver" {

  zone_id = var.aws_route53_id
  name    = "sftp.domain.com"
  type    = "CNAME"
  ttl     = "300"

  records = [aws_transfer_server.sftp.endpoint]
}
```

如果您熟悉 AWS，这些资源对您来说应该不陌生。IAM 角色和策略定义了 SFTP 服务器所需的权限。s3 存储桶是我们所有用户的主目录存放的地方。这里唯一不熟悉的资源是“aws_transfer_server”。

正如您可以从名称中推断的那样，这就是实际的 SFTP 服务器本身。我们只为我们的设置定义了三个选项:

*   身份提供者—服务器应该使用什么身份验证机制？“SERVICE_MANAGED”意味着我们将在服务本身中配置访问权限。或者，您也可以使用 API 网关进行身份验证。
*   logging_role —它附加了我们创建的 IAM 角色，以允许 SFTP 服务器将日志写入 CloudWatch。
*   标签— AWS 标签是不言自明的。

要获得完整的选项列表，请查看 Terraform 的文档:[这里](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/transfer_server)

## SFTP 用户模块

有了服务器，现在我们需要为每个用户实例化的用户模块。

```
resource "aws_iam_role" "sftp" {
  name = "sftp-${var.username}-user-role"

  assume_role_policy = <<EOF
{
    "Version": "2012-10-17",
    "Statement": [
        {
        "Effect": "Allow",
        "Principal": {
            "Service": "transfer.amazonaws.com"
        },
        "Action": "sts:AssumeRole"
        }
    ]
}
EOF

  tags = {
    Name       = "sftp-${var.username}-user-role"
    Terraform  = "true"
  }
}

resource "aws_iam_role_policy" "sftp" {
  name = "sftp-${var.username}-user-policy"
  role = aws_iam_role.sftp.id

  policy = <<POLICY
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowListingFolder",
            "Effect": "Allow",
            "Action": [
                "s3:ListBucket",
                "s3:GetBucketLocation"
            ],
            "Resource": "${var.s3_bucket_arn}",
            "Condition": {
                "StringLike": {
                    "s3:prefix": [
                        "${var.username}/*",
                        "${var.username}"
                    ]
                }
            }
        },
        {
            "Sid": "AllowReadWriteToObject",
            "Effect": "Allow",
            "Action": [
                "s3:PutObject",
                "s3:GetObject",
                "s3:DeleteObjectVersion",
                "s3:DeleteObject",
                "s3:GetObjectVersion"
            ],
            "Resource": "${var.s3_bucket_arn}/${var.username}*"
        }
    ]
}
POLICY
}

resource "aws_transfer_user" "user" {
  server_id      = var.sftp_server_id
  user_name      = var.username
  role           = aws_iam_role.sftp.arn
  home_directory = "/${var.s3_bucket_name}/${var.username}"

  tags = {
    Terraform  = "true"
  }
}

resource "aws_transfer_ssh_key" "user" {
  server_id = var.sftp_server_id
  user_name = aws_transfer_user.user.user_name
  body      = var.sshkey

} 
```

请注意我们正在定义的权限。由于我们所有的用户都共享一个 s3 存储桶，我们将他们的权限锁定到他们特定的主目录，以确保他们只能访问他们自己的内容。这是通过如下设置实现的:

```
"Condition": {
                "StringLike": {
                    "s3:prefix": [
                        "${var.username}/*",
                        "${var.username}"
                    ]
```

或者

```
"Resource": "${var.s3_bucket_arn}/${var.username}*"
```

对于您自己的设置，您可以为每个用户创建一个 s3 bucket，但是感觉没有必要，特别是如果您正确地锁定了权限。将所有东西放在一个 S3 桶中也为数据分析提供了很多好处。

在权限之外，唯一值得突出显示的资源是“aws_transfer_ssh_key”和“aws_transfer_user”。正如您所看到的，这些资源创建了我们的用户，并定义了应该用于身份验证的 SSH 密钥。同样，您可以看到我们如何继续利用共享的 s3 存储桶。

```
home_directory = "/${var.s3_bucket_name}/${var.username}"
```

您可以做的一件可选的事情是在用户的主目录中配置文件夹，您可以通过以下方式实现这一点:

```
resource "aws_s3_bucket_object" "outbound" {
  bucket = var.s3_bucket_name
  acl    = "private"
  key    = "${var.username}/OUTBOUND/"
  source = "/dev/null"
}
```

## 把所有的放在一起

由于 SFTP 服务器不是一个模块，您可以简单地运行`teraform apply`，Terraform 会完成剩下的工作。SFTP 用户代码是一个模块，所以它需要实例化。根据您的 Terraform 版本，您有两种选择:

地形≤ 0.12:

在 Terraform 0.13 之前，不能在模块上使用`for_each`。这意味着您需要为每个用户定义模块。这看起来像是:

```
module "exampleuser" {
  source = "../modules/aws-sftp-user"

  username       = "exampleuser"
  sshkey         = "ssh-rsa XXXXXXXXX"
  s3_bucket_arn  = aws_s3_bucket.sftp.arn
  s3_bucket_name = aws_s3_bucket.sftp.id
  sftp_server_id = aws_transfer_server.sftp.id
}
module "exampleuser2" {
  source = "../modules/aws-sftp-user"

  username       = "exampleuser2"
  sshkey         = "ssh-rsa YYYYYYYYYY"
  s3_bucket_arn  = aws_s3_bucket.sftp.arn
  s3_bucket_name = aws_s3_bucket.sftp.id
  sftp_server_id = aws_transfer_server.sftp.id
}
module "exampleuser3" {
  source = "../modules/aws-sftp-user"

  username       = "exampleuser3"
  sshkey         = "ssh-rsa ZZZZZZZZZZ"
  s3_bucket_arn  = aws_s3_bucket.sftp.arn
  s3_bucket_name = aws_s3_bucket.sftp.id
  sftp_server_id = aws_transfer_server.sftp.id
}
```

地形 0.13 ( **最优**):

随着在 Terraform 0.13 中添加了`for_each`支持，我们可以将上述内容简化如下:

*   创建用户及其 SSH 密钥的映射。

```
variable "user_map" {
  type = "map"
  default = {
    exampleuser1 = "ssh-rsa XXXXXXXXXX"
    exampleuser2 = "ssh-rsa YYYYYYYYYY"
    exampleuser3 = "ssh-rsa ZZZZZZZZZZ"
  }
}
```

然后使用`for_each`实例化您的模块:

```
module "sftpusers" {
  for_each = var.user_map
  source = "../modules/aws-sftp-user"

  username       = each.key
  sshkey         = each.value
  s3_bucket_arn  = aws_s3_bucket.sftp.arn
  s3_bucket_name = aws_s3_bucket.sftp.id
  sftp_server_id = aws_transfer_server.sftp.id
}
```

接下来，只需更新“user_map”变量并运行 terraform，就可以轻松添加新用户。

# 结论

通过 10-20 分钟的编码，我们能够提供一个全功能的 SFTP 服务器。它是完全自动化的，与 AWS 集成，永远不需要打补丁、进行冗余/容错、监控磁盘空间或加固。所有的开销都已经解决了，你可以享受所有的好处。因为这些都在 AWS 中，你甚至可以做一些在普通 SFTP 服务器上不可能做的事情。例如，您可以在 S3 中添加触发器，以便在文件上传时启动 Lambdas。我们刚刚打开了可能性，同时消除开销，感觉像一个胜利！

为了获得无限的故事，你还可以考虑注册[](https://blog.rhel.solutions/membership)**成为中等会员，只需 5 美元。如果您使用* [*我的链接*](https://blog.rhel.solutions/membership) *注册，我会收到一小笔佣金(无需您额外付费)。**