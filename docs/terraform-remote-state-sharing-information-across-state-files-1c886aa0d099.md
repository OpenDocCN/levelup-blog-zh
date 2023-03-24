# Terraform 远程状态—跨状态文件共享信息

> 原文：<https://levelup.gitconnected.com/terraform-remote-state-sharing-information-across-state-files-1c886aa0d099>

![](img/bc61adb43b77b1cc5eb73bb02e031e6c.png)

我最近使用的一个非常酷的功能是跨多个 terraform 项目共享一个 terraform 状态。

这里有一个我用过的结构的例子，它对我很有效。

```
.
├── management
│   ├── outputs.tf
│   ├── route53.tf
│   └── vpc.tf
├── service-1
│   ├── api-gateway.tf
│   └── data.tf
└── service-2
    ├── api-gateway.tf
    └── data.tf
```

我以这种方式进行了拆分，以处理将处理核心基础架构的核心“管理”配置，我希望将核心基础架构与基础架构中托管的单个服务分离开来。

以这种方式分割项目还有其他原因，比如简化部署，有时是为了循环依赖，有时是配置问题。

在我的例子中，服务 1 和服务 2 依赖于管理，以访问 VPC 和路由 53 信息。将所有的 terraform 配置包含在一个“项目”中是可能的，但是我想避免创建一个单一的基础设施配置。

为了在 service-1、service-2 和管理之间创建松散的连接，并从其状态的最新版本中引用基础结构，您的项目可以访问另一个服务的远程状态。这在 [terraform 网站](https://www.terraform.io/docs/providers/terraform/d/remote_state.html)上有很好的记录

通过使用输出来共享数据，例如，我有一个在管理中运行的容器，我希望能够输出 Route 53 区域 id，以便与其他服务共享

```
output "route53_zone_id" {
  value = "${aws_route53_zone.zone.zone_id}"
}
```

当您运行带有输出的应用程序时，您也会在控制台中看到它

```
Apply complete! Resources: 0 added, 1 changed, 0 destroyed.Outputs:route53_zone_id = Z1J2WFZ3ZFF4B5
```

为了从另一个项目引用它，我添加了一个指向远程状态的数据块:

```
data "terraform_remote_state" "management" {
  backend = "s3"
  config {
    bucket = "craig-godden-payne-terraform-remote-state"
    key    = "infrastructure/management/terraform.tfstate"
    region = "eu-west-1"
  }
}
```

现在，当我想使用远程状态的数据时，我可以这样做:

```
"${data.terraform_remote_state.management.route53_zone_id}"
```

如果您有问题，请确保您已经在源环境上运行了`terraform apply`,即使您没有做任何更改。

![](img/d270ea1bc487aa00a4f9f7aa51b4a6aa.png)