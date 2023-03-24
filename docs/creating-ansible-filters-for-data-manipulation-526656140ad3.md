# 创建用于数据操作的可变过滤器

> 原文：<https://levelup.gitconnected.com/creating-ansible-filters-for-data-manipulation-526656140ad3>

![](img/cb0c5b3b0602424341f117459fa30de1.png)

在编写可翻译的剧本时，您可能需要执行一些文本转换。这些转换可能包括一些简单的事情，比如直接数据类型转换，或者使用 Ansible 中的一些[内置 Jinja2 过滤器对字符串变量运行算法。](https://docs.ansible.com/ansible/latest/user_guide/playbooks_filters.html)

```
# For human-readable output, you can use:
as_json: "{{ some_variable | to_nice_json }}"
as_yaml: "{{ some_variable | to_nice_yaml }}"# Get the min and max of lists
min_num: "{{ [3, 4, 2] | min }}"
max_num: "{{ [3, 4, 2] | max }}"# Perform complex operations on strings
sha1_hash: "{{ 'test1' | hash('sha1') }}"
checksum: "{{ 'test1' | checksum }}"
```

但是，如果只使用内置的 Ansible 过滤器进行复杂的转换，您最终会得到一串不可读的连锁过滤器，很难理解您的数据发生了什么，并且会使您的行动手册和角色可读性更差。

```
yucky_value: "{{ some_variable | filter_foo | filter_bar | when_will_it_end | theres_got_to_be_a_better_way }}"
```

在本文中，我将介绍如何通过使用一些 python 代码创建一个自定义过滤器来简化这些复杂的转换，然后您可以在您的 Ansible 剧本中使用这些代码，以便通过可重用的过滤器保持它们的可读性。

# 创建自定义的可变筛选器

以这个例子为例，可以很好地为我们想要的特定输入数据和输出格式创建一个定制的 Ansible 过滤器。我们有一个 S3 存储桶，它将其对象输出为一个长的路径列表，其中包含表示目录和下面的文件夹的对象。

```
- folder1/file1.js
- folder1/sub-folder/file1.js
- folder2/
- folder3/file1.js
- folder3/file2.js
- folder3/file3.js
```

然而，对于我们的需求，我们想知道对于下面的预期输出，bucket 的顶层文件夹是什么样的。

```
folder1
folder2
folder3
```

要创建新的过滤器模块，在文件树中与我们的剧本相邻的名为`filter_plugins`的目录中创建一个具有任意名称的`.py`文件(Ansible 将在剧本运行时为过滤器搜索在该过滤器中找到的任何`.py`文件)。我们将创建一个 python 模块，它从 Ansible 库中定义了一个`FilterModule`类。这个类必须有一个`filters`方法，为每个定义的过滤器返回名称和可调用函数的映射。

满足我们过滤 S3 路径的简单用例的过滤器模块看起来像下面的类。

```
#!/usr/bin/pythonclass FilterModule(object):
    def filters(self):
        return {'unique_folders': self.unique_base_paths}def unique_base_paths(self, keys):
        # A Set allows for no duplication of keys
        base_paths = set()
        for key in keys:
            # A path contains a directory if it has a "/"
            if "/" in key:
                # Get the first folder in each path
                base_folder = key.split("/")[0]
                base_paths.add(base_folder)
        # Sets aren't handled by ansible so convert to list
        return list(base_paths)
```

既然已经定义了 filter 类，我们可以使用 filters 方法`unique_folders`中指定的名称在我们的 Ansible 剧本和角色中使用它。

```
- hosts: localhost
  vars:
    deployment_bucket:
      name: "some-bucket-with-packages"
      region: "ap-southeast-2"

   tasks:
    - name: List keys
      aws_s3:
        bucket: "{{ deployment_bucket.name }}"
        region: "{{ deployment_bucket.region }}"
        mode: list
      register: bucket_content - name: All bucket paths
      debug:
        var: bucket_content.s3_keys - name: Get all top level folders in bucket
      set_fact:
        deployed_packages: "{{ bucket_content | unique_folders }}" - name: Unique top level folders
      debug:
        var: deployed_packages
```

如果我们对一个具有与原始示例数据相同的对象的 bucket 运行下面的剧本，我们将得到

```
TASK [Unique top level folders] *********************
ok: [localhost] => {
    "deployed_packages": [
        "folder1",
        "folder2",
        "folder3"
    ]
}
```

# 包扎

正如您所看到的，很容易创建一个定制的 Ansible 过滤器来操作您的剧本和角色中的数据。当您将配置作为代码开发时，它是一个强大的工具。因为我们只是在编写 python，所以我们可以做相当复杂的事情，同时仍然保持良好的可读性，这样我们就可以在将来轻松地修改我们的配置。这些过滤器，像 python 代码一样，可以跨项目重用，并允许您为您的组织快速构建有价值的标准过滤器。

您可以查看以下参考资料，了解更多关于创建可解析过滤器的信息。

*   [可变过滤器文档](https://docs.ansible.com/ansible/latest/user_guide/playbooks_filters.html)
*   [查看内置的可变过滤器源代码](https://github.com/ansible/ansible/tree/devel/lib/ansible/plugins/filter)

# 进一步连接

*   如果你正在考虑获得一个中型订阅，你可以通过使用我的[推荐链接](https://aaron-kt-berry.medium.com/membership)来帮助我。
*   查看我在[媒体](https://medium.com/@aaron-kt-berry)上的其他文章，如果你想了解最新消息，请通过[电子邮件](https://aaron-kt-berry.medium.com/subscribe)订阅。
*   如果你想聊天，请在 Twitter 或 LinkedIn 上联系我，如果你想雇佣我，我在 Codementor 上。