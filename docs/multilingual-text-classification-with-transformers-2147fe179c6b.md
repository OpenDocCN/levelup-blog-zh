# 使用转换器的多语言文本分类

> 原文：<https://levelup.gitconnected.com/multilingual-text-classification-with-transformers-2147fe179c6b>

## 微调的 mBERT/distill mBERT 并导出到 ONNX 进行推理

![](img/317e08e26430a1237ebea143e9b95e98.png)

[梁杰森](https://unsplash.com/@ninjason?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

之前，我写过一篇关于使用 Python 中的 spaCy 进行[文本分类的文章。spaCy 背后的架构和概念很简单，但对大多数用例来说已经足够好了。事实上，我已经在我的一些项目中广泛使用了它。比如亵渎和广告分类。然而，现有的空间架构存在以下问题:](https://towardsdatascience.com/sarcasm-text-classification-using-spacy-in-python-7cd39074f32e)

*   我必须为每种语言独立训练一个新模型
*   我必须为某些语言(中文、日文、韩文、俄文、越南文等)设置和安装专门的分词器。)

在本教程中，我将解释如何使用`transformers`训练单个多语言文本分类模型:

*   微调预训练的拥抱脸的文本分类的变形金刚
*   基于 DistilmBERT 架构(也支持普通的 mBERT 架构)
*   将微调模型导出到 ONNX 进行推理

让我们继续下一部分，设置所有需要的包。

# 设置

强烈建议在安装之前创建一个新的虚拟环境。激活虚拟环境，安装`transformers`包，如下所示:

```
pip install transformers
```

> 请注意，官方文档是基于克隆最新的存储库并将其安装在本地。本文基于 PyPI 的最新稳定版本。

接下来，创建一个名为`requirements.txt`的新文件，并在其中添加以下文本:

```
accelerate >= 0.12.0
datasets >= 1.8.0
sentencepiece != 0.1.92
scipy
scikit-learn
protobuf
torch >= 1.3
evaluate
```

> 请注意，本教程是基于变压器版本 4.24.0，请参考更新的[需求文件](https://github.com/huggingface/transformers/blob/main/examples/pytorch/text-classification/requirements.txt)如果您使用的是更新的版本。

运行以下命令安装所有必需的软件包:

```
pip install -r requirements.txt
```

转到下面的[库](https://github.com/huggingface/transformers/tree/main/examples/pytorch/text-classification)并将 [run_glue.py](https://github.com/huggingface/transformers/blob/main/examples/pytorch/text-classification/run_glue.py) 文件保存到工作目录。是官方对一个`transformers`模型进行微调的训练脚本。

> `run_glue.py`是用于单机微调的标准 Pytorch 版本。还有另一个名为`run_glue_no_trainer.py`的训练脚本，它是使用`accelerate`包在多台机器上进行分布式训练的。

打开训练脚本并注释掉`check_min_version`函数(这是防止异常所必需的，因为本教程是基于 PyPI 的`transformers`的旧稳定版本):

```
# Will error if the minimal version of Transformers is not installed. Remove at your own risks.
# check_min_version("4.26.0.dev0")
```

# 数据集

训练脚本接受 CSV 或 JSON 作为训练/验证文件。每个文件都应该有以下头或键值对:

*   `sentence` —示例文本
*   `label`—代表类别的数字。例如，0 表示阴性，1 表示阳性。

```
|------------|--------|
|  sentence  | label  |
|------------|--------|
| bad movie  |   0    |
|------------|--------|
```

对于那些没有自己训练数据的人，你可以使用 HuggingFace 的[斯坦福情感树库](https://huggingface.co/datasets/sst2)数据集(情感分析)。

将训练和验证数据集放在与`run_glue.py`文件相同的工作目录中。当前的培训脚本只接受一个培训文件和一个验证文件。

# 培养

运行以下命令开始培训:

```
python run_glue.py \
  --model_name_or_path distilbert-base-multilingual-cased \
  --train_file ./data/train.csv \
  --validation_file ./data/eval.csv \
  --do_train \
  --do_eval \
  --max_seq_length 128 \
  --per_device_train_batch_size 16 \
  --learning_rate 2e-5 \
  --num_train_epochs 3 \
  --output_dir ./output/
```

*   使用`bert-base-multilingual-cased`对 mBERT 而不是 DistilmBERT 进行微调。也可以在其他非多语言`transformers`车型上进行培训。
*   将`per_device_train_batch_size`的值增加或减少到 4/8/32/64/等。基于 GPU 的内存。
*   相应修改`train_file`、`validation_file`和`output_dir`的路径。

> 当您第一次运行该命令时，它会将所需的模型下载到缓存中。此外，它还将对数据集进行令牌化，并存储在同一文件夹中。

此外，还有其他对培训有用的输入参数:

*   `cache_dir` —默认情况下，HuggingFace 会在`.cache/huggingface/` (Linux)下载并存储模型。此参数确保缓存将保存在所需的位置。
*   `fp16` —以混合精度训练模型。使用混合精度可以在不降低模型性能的情况下将训练时间减少一半。
*   `save_strategy` —不按特定时间间隔保存检查点。该标志将在每个时期保存一个检查点。

```
CUDA_VISIBLE_DEVICES=2 python run_glue.py \
  --model_name_or_path distilbert-base-multilingual-cased \
  --train_file ./data/train.csv \
  --validation_file ./data/eval.csv \
  --do_train \
  --do_eval \
  --max_seq_length 128 \
  --per_device_train_batch_size 16 \
  --learning_rate 2e-5 \
  --num_train_epochs 3 \
  --output_dir ./output/ \
  --save_strategy epoch \
  --cache_dir models/ \
  --fp16
```

环境变量`CUDA_VISIBLE_DEVICES`可用于分配训练所需的 GPU。以下命令将使用第三个 GPU 训练模型(索引从 0 开始):

```
CUDA_VISIBLE_DEVICES=2 python run_glue.py ...
```

根据数据集的不同，整个培训可能需要几个小时。在培训结束时，它将在`output_dir`中生成以下重要文件:

*   配置. json
*   pytorch_model.bin
*   special _ tokens _ map.json
*   tokenizer.json
*   tokenizer_config.json
*   vocab.txt

下一节将介绍如何使用新训练的模型进行推理。

# 推理

在同一个工作目录下创建一个名为`inference.py`的新文件。在其中追加以下代码:

```
from transformers import AutoTokenizer, DistilBertForSequenceClassification, TextClassificationPipeline

# tokenizer initialization
tokenizer = AutoTokenizer.from_pretrained("./output/", local_files_only=True)
# model initialization, use BertForSequenceClassification for mBERT/BERT architecture
model = DistilBertForSequenceClassification.from_pretrained("./output/", local_files_only=True)
# pipeline initialization
pipe = TextClassificationPipeline(model=model, tokenizer=tokenizer)

text_list = ["I love this movie!", "I hate this movie!"]
# inference
result = pipe(text_list)
print(result)
```

保存文件并运行以下命令:

```
python inference.py
```

以下结果将打印在终端上:

```
[{'label': 0, 'score': 0.9951834082603455}, {'label': 1, 'score': 0.9082725048065186}]
```

# ONNX

我们不直接用 Pytorch 进行推理，而是探索如何将模型转换成 ONNX 格式，并使用 ONNX 运行时进行推理。

ONNX 运行时相比 Pytorch 具有以下优势:

*   ML 型号的性能加速(适用于 CPU 和 GPU)
*   能够在不同的硬件和操作系统上运行
*   支持其他语言的推理(C#/C++/Java)
*   独立于培训框架(Pytorch、TensorFlow 等。)

`transformers`库自带了用于 ONNX 支持的`[transformers.onnx](https://huggingface.co/docs/transformers/v4.24.0/en/serialization#export-to-onnx)`包。按照以下方式安装:

```
pip install transformers[onnx]
```

运行以下命令，将新微调的模型导出为 ONNX 格式:

```
python -m transformers.onnx --model=output/ --feature=sequence-classification --framework=pt onnx_output/
```

所有支持的功能列表如下:

```
|--------------------------------|-------------------------------------|
|            Feature             |             Auto Class              |
|--------------------------------|-------------------------------------|
|causal-lm, causal-lm-with-past  |  AutoModelForCausalLM               |
|default, default-with-past      |  AutoModel                          |
|masked-lm                       |  AutoModelForMaskedLM               |
|question-answering              |  AutoModelForQuestionAnswering      |
|seq2seq-lm, seq2seq-lm-with-past|  AutoModelForSeq2SeqLM              |
|sequence-classification         |  AutoModelForSequenceClassification |
|token-classification            |  AutoModelForTokenClassification    |
|--------------------------------|-------------------------------------|
```

终端将显示以下输出，表明导出成功:

```
Validating ONNX model...
        -[✓] ONNX model output names match reference model ({'logits'})
        - Validating ONNX Model output "logits":
                -[✓] (2, 2) matches (2, 2)
                -[✓] all values close (atol: 1e-05)
All good, model saved at: onnx_output/model.onnx
```

它将在`onnx_ouput`文件夹中生成以下文件:

*   model.onnx

创建一个名为`inference_onnx.py`的新脚本，并在其中添加以下代码:

```
from transformers import AutoTokenizer
from onnxruntime import InferenceSession
import numpy as np

# utility function to calculate probabilities
def softmax(vec):
    exponential = np.exp(vec)
    probabilities = exponential / np.sum(exponential)
    return probabilities

# initialization
sentiment_dict = {0: "positive", 1: "negative"}
tokenizer = AutoTokenizer.from_pretrained("./output/", local_files_only=True)
session = InferenceSession("onnx_output/model.onnx")

text_list = ["I love this movie!", "I hate this movie!"]
result = []

# loop over each item in text_list
for text in text_list:
    # ONNX accepts numpy as input
    inputs = tokenizer(text, return_tensors="np")

    # inference, returns numpy array
    outputs = session.run(output_names=["logits"], input_feed=dict(inputs))
    # [array([[ 4.1819444, -2.2060142]], dtype=float32)]

    # convert to probability score (0 to 1)
    probabilities = softmax(outputs[0][0])
    # [0.9951833  0.00481664]

    # get the index with the highest probability
    index = np.argmax(outputs)
    # 0

    print({'label': sentiment_dict[index], 'score': probabilities[index]})
    # {'label': positive, 'score': 0.9951833}
```

保存文件并运行以下命令进行推断:

```
python inference_onnx.py
```

> 与使用 Pytorch 进行推理相比，推理速度应该有所提高。

# 结论

让我们回顾一下本教程的学习内容。

首先简要介绍了作为多语言文本分类替代架构的`transformers`。

然后，本文介绍了所有必需包的设置和安装。它还解释了数据集的格式，并链接到一个免费使用的情感分析数据集。

随后，它继续使用 Pytorch 进行训练和推理。它还强调了一些重要的训练参数，如混合精度训练的 fp16。

本教程继续详细解释 ONNX 以及如何将 Pytorch 模型转换为 ONNX 格式。最后一节展示了使用转换后的 ONNX 模型执行推理的完整脚本。

感谢你阅读这篇文章。祝你有美好的一天！

# 参考

1.  [hugging face—distilbert-base-多语言环境](https://huggingface.co/distilbert-base-multilingual-cased)
2.  [拥抱脸—Bert-base-多语言环境](https://huggingface.co/bert-base-multilingual-cased)
3.  [HuggingFace 博客—导出到 ONNX](https://huggingface.co/docs/transformers/v4.24.0/en/serialization#export-to-onnx)
4.  [拥抱脸数据集— SST](https://huggingface.co/datasets/sst2) 2
5.  [Github — HuggingFace 文本分类示例](https://github.com/huggingface/transformers/tree/main/examples/pytorch/text-classification)

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[升级编码出版物](https://levelup.gitconnected.com/?utm_source=pub&utm_medium=post)中的更多内容
*   🔔关注我们:[Twitter](https://twitter.com/gitconnected)|[LinkedIn](https://www.linkedin.com/company/gitconnected)|[时事通讯](https://newsletter.levelup.dev)

🚀👉 [**加入人才集体，找到一份令人惊喜的工作**](https://jobs.levelup.dev/talent/welcome?referral=true)