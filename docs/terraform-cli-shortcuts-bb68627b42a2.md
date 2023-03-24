# Terraform CLI 快捷方式

> 原文：<https://levelup.gitconnected.com/terraform-cli-shortcuts-bb68627b42a2>

## 使用这些快捷方式简化日常地形使用

![](img/04852cd8fab768bf21137ff3bec089b7.png)

我想分享一些我日常使用的 CLI 快捷方式，以简化和加快我的 Terraform 工作流程。需求——bash 兼容的解释器，因为下面描述的别名和函数将与 bash、zsh 和 ohmyzsh 一起工作。

为了使用所描述的任何一个函数的别名，您需要将它放在您的`~/.bashrc`或`~/.zshrc`文件中(或者您为 shell 准备的任何其他配置文件中)。

然后只需将这个文件作为源文件，例如:`source ~/.zshrc`

# 函数:列出给定模块的输出和变量

你需要提供模块目录的路径，这个函数将列出模块所有声明的变量和输出。当你记不住所有的单词，只需要快速浏览一下的时候，它会非常有用。

```
# TerraForm MOdule Explained
function tfmoe {
  echo -e "\nOutputs:"
  grep -r "output \".*\"" $1 |awk '{print "\t",$2}' |tr -d '"'
  echo -e "\nVariables:"
  grep -r "variable \".*\"" $1 |awk '{print "\t",$2}' |tr -d '"'
}
```

示例用法:

```
user@localhost $: tfmoe ./module_albOutputs:
  alb_arnVariables:
  acm_certificate_arn
  lb_name
  alb_sg_list
  subnets_id_list
  tags
```

# 功能:用配置文件预填充模块目录

你需要提供一个模块目录的路径，这个函数会创建一堆空的‘默认’。tf 文件在里面。

```
#TerraForm MOdule Initialize
function tfmoi {
  touch $1/variables.tf
  touch $1/outputs.tf
  touch $1/versions.tf
  touch $1/main.tf
}
```

示例用法:

```
user@localhost $: mkdir ./module_foo && temoi $_user@localhost $: ls ./module_foo
main.tf      outputs.tf   variables.tf versions.tf
```

# 别名

这些别名的目的只是防止您在执行简单操作时输入长命令。

```
alias tf='terraform'alias tfv='terraform validate'alias tfi='terraform init'alias tfp='terraform plan'
```

这是有用的，因为它使格式工具深入(递归)通过目录。

```
alias tfm='terraform fmt -recursive'
```

示例用法:

```
user@localhost $: tfm 
module_ecs_cluster/ecs.tf
module_alb/alb.tf
```

*如果你喜欢这篇文章，你也可以在* [*Twitter*](https://twitter.com/vasylenko) *上关注我，我偶尔会在那里发布我对 AWS、Terraform、Ansible 和其他 DevOps 相关事物的发现*