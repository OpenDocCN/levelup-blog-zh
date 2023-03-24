# MATLAB 到 NumPy 数组

> 原文：<https://levelup.gitconnected.com/matlab-to-numpy-array-eee6de42b5de>

![](img/e1fa51df0c9e83488b45d411bc7eca78.png)

[https://pix abay . com/vectors/evolution-walking-Charles-Darwin-297234/](https://pixabay.com/vectors/evolution-walking-charles-darwin-297234/)

# 将一种数据结构转换成另一种数据结构

我最近被要求解决一个有趣的数据结构转换问题。它从下面一行 Python 代码开始:

```
return unknown_data_type
```

unknown_data_type 原来是一个 NumPy 数组，看起来像这样:

```
array([ ...numerical data... ])
```

挑战在于，我需要放弃函数，直到返回语句，并返回从 MATLAB 加载的数据。改为 mat 文件。我从未听说过。所以需要做一些调查。当然，从. mat 文件加载的数据与 NumPy 数组不匹配。剩下的代码寻找一个 NumPy 数组，这意味着我必须将。mat 文件来精确匹配 NumPy 数组。

> [文件带着。mat 扩展名是 MATLAB 程序使用的二进制数据容器格式的文件。](https://www.reviversoft.com/file-extensions/mat#:~:text=Files%20with%20the.mat%20extension%20are%20files%20that%20are,that%20include%20variables%2C%20functions%2C%20arrays%20and%20other%20information.)

```
# jupyter notebook --NotebookApp.iopub_data_rate_limit=10000000000import scipy.io as sio
mat = sio.loadmat('mat_filename.mat') # insert your filename here
print(mat) # find the fieldname you're looking format_load = [[element for element in upperElement] for upperElement in mat['fieldname_in_mat_file']] # insert your fieldname heremat_list = [] # create an empty list
length = 100 # update with the number of values you want/need...
for i in range(length): # ...or just hardcode it here
    mat_list.append(mat_load[i][0]) # append the values to the listnp_array = np.array(mat_list) # convert the list to a NumPy arrayint32_array = np_array.astype(np.int32) # convert values to int32
int32_array # visually confirm that the output is a NumPy array
```

您的默认 data_rate_limit 旨在保护您的计算机/服务器，但您可能需要更改它以与配合使用。mat 文件。上面已经注释掉了，但是这是我需要执行的代码行，以便在本地运行这段代码。

前几行加载并显示。mat 文件。我这样做是为了找到包含我需要的数据的字段。然后，我只加载了那个字段。然后，我获取该字段数据并创建一个列表，然后将它添加到一个 NumPy 数组中。最后，我必须将数据转换为 int32。在所有这些之后。mat 文件与 return 语句之前的代码生成的 NumPy 数组完全匹配。

## 收场白

在我做这件事的时候，我几乎找不到关于。mat 文件。因此，我分享这个，以防其他人会觉得有帮助。