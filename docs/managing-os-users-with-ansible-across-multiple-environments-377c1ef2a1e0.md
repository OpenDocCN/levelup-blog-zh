# 使用 Ansible 跨多个环境管理操作系统用户

> 原文：<https://levelup.gitconnected.com/managing-os-users-with-ansible-across-multiple-environments-377c1ef2a1e0>

![](img/2f270e1061d9ccad53ece1b62163b8f3.png)

过去五年左右，我一直在使用 Ansible 管理操作系统用户。Ansible 内置用户模块非常方便易用。Ansible Galaxy 和 Github 上有许多现成的角色，功能几乎相同。但是 Ansible 本身意味着一种强制性的做事方式。创建一个用户是一件非常简单的事情，但是在拥有成百上千个服务器的多个环境中管理用户却是一件棘手的事情。也许使用像 Puppet 或 Salt 这样的工具来管理具有复杂层次结构的大型企业中的用户听起来是一个更好的主意。但也许这不是必要的，有一种方法可以让我们的角色适应大规模的环境。

## 它是如何开始的

先来温习一下如何在一个小的初创公司管理用户。我将试着解释一个与我工作过的任何公司都没有直接关系的假设案例。通常有一个包含几个组的用户列表，在非常常见的情况下，用户拥有完全的管理权限—admins；和具有基本系统访问权限的用户—开发人员。此外，提升的特权是通过 sudoers 管理的，但我还不想触及这个话题。

示例用户列表应该如下所示:

```
users:
  - name: 'John D'
    username: 'john'
    groups:
      - 'admin'
  - name: 'Jane D'
    username: 'jane'
    groups:
      - 'developers'
```

好的，我们有一个用户列表和一个包含十几台服务器的主机列表(清单),我们需要做的就是对照清单运行行动手册，就这样。我们不太关心安全性、可观测性等。简单。

```
ansible-playbook -i inventory playbooks/users.yml
```

日常运营流程看起来是这样的:
添加新服务器—更新库存，运行剧本。
添加新用户—更新 group_vars/vars，运行剧本。
删除用户账号—更新 group_vars/vars，运行剧本。
简单，不复杂，没有问题。

随着公司的发展，我们有一堆环境/项目和一个额外的部门，如分析部门。

```
users:
  - name: 'John D'
    username: 'john'
    groups:
      - 'admin'
  - name: 'Dave K'
    username: 'dave'
    groups:
      - 'admin'
  - name: 'Bob O'
    username: 'bob'
    groups:
      - 'admin'
  - name: 'Jane D'
    username: 'jane'
    groups:
      - 'developers'
  - name: 'Mark S'
    username: 'mark'
    groups:
      - 'developers'
  - name: 'Sven J'
    username: 'sven'
    groups:
      - 'developers'
  - name: 'Alice C'
    username: 'alice'
    groups:
      - 'analytics'
```

现在我们做几乎相同的事情:
添加新服务器—更新库存，运行行动手册。
添加新用户—更新 group_vars/vars，说三个魔字，跑剧本，一个脏话，再跑剧本。像这样:

```
ansible-playbook -i hosts playbooks/users.yml -l dev -e '@dev.yaml'
ansible-playbook -i hosts playbooks/users.yml -l analytics -e '@analytics.yaml'
ansible-playbook -i hosts playbooks/users.yml -l prod
```

删除用户帐户—更新 group_vars/vars，…，再次运行行动手册。

从技术上讲，有很多方法可以完成这项任务。但无论如何，它将包括几个步骤，如管理用户列表，可能还有几个额外的列表。

没有那么复杂，更多的步骤，没有问题，如果过程是有据可查的。

## 下一关

在某个时间点，公司发展到一个新的水平，员工增加到十几个部门的一百多名工程师。现在是团队找出如何改进现有工具以满足增长预期或者决定采用一些众所周知的企业级软件的时候了。然而，仍然没有必要为所有用户提供对服务器的访问，所以列表仍然没有那么长，它是可审计的，坚持 IaC 范例是有意义的。

在此阶段实现哪些目标是有意义的:

*   保持简单、可维护——所有环境/站点等的共享用户列表；
*   可视流程—针对所有服务器运行的一个作业，并显示其完成的时间和方式。

如果团队想在不增加额外步骤的情况下以更具声明性的方式应用更改，那么区分在哪里创建用户以及如何创建用户是非常重要的。假设团队想要在所有机器上创建**管理员**用户，然后只在开发服务器上创建**开发者**账户，在数据服务器上创建**分析**账户，等等。这里可以为用户添加一个新参数— **target_hosts** ，它是用户所属的主机组的列表。

看起来可能是这样的:

```
users:
  - name: 'John D'
    username: 'john'
    groups:
      - 'admin'
    # We do not declare target_hosts for admins because we want to have them on all hosts
...
  - name: 'Jane D'
    username: 'jane'
    groups:
      - 'developers'
    # We declaring target_hosts groups list which is an Ansible inventory hosts group where we adding the user
    target_hosts:
      - dev
...
  - name: 'Alice C'
    username: 'alice'
    groups:
      - 'analytics'
    target_hosts:
      - data
```

就是这样，看起来简单。但是如何实现呢？

也许这不是显而易见的，但我们在这里需要做的是在一个剧本的任务集和一个特殊条件集之前增加一个额外的步骤。

```
- name: "Determine target hosts"
  set_fact:
     do_run: True
  with_subelements:
    - "{{ users }}"
    - target_hosts
    - skip_missing: True
  when:
    - item.1 in group_names
```

这里我们迭代 target_hosts 列表，检查是否至少有一个组在 **group_names** 中，该变量是一个包含当前主机所属组列表的可转换的特殊变量。如果条件为真，我们将事实(var) **do_run** 设置为*真。*

现在我们在任务内部做额外的检查:

```
- name: "Manage user accounts"
  user:
    name: "{{ item.username }}"
...
    state: "{{ item.state }}"
  loop: "{{ users }}"
  when:
    - users is iterable
    - do_run is defined or item.target_hosts is not defined
```

这里我们增加了一个额外的检查:在没有定义的**上 **do_run** 是否为真。我们没有为管理员定义 **target_hosts** 来确保我们在所有机器上都有他们的帐户，当我们只需要在某一组机器上有这样的帐户时，我们为开发人员和其他帐户类型声明 target_hosts。另外，请注意，**状态**参数是必需的，因为我们不想维护像 **users_retired、**这样的额外列表，我们只是显式声明状态 **present** 或**exist。****

因此，现在我们可以通过只运行一个自动作业来控制在一个列表中创建用户的位置。Target_host group 可以是动态云清单中的一个**标签**，比如 AWS/GCP/WhateverCloud、可用性区域、地区、项目。

详情请查看我在 Github 上的 [**用户**](https://github.com/1it/ansible-role-users) 角色。希望能有用。感谢阅读！