# 回顾:Q-CTRL 火蛋白石

> 原文：<https://levelup.gitconnected.com/review-q-ctrl-fire-opal-6bcc187ae1bd>

![](img/81a2287eb5a80eeb60ac8a26735a8934.png)

[https://pix abay . com/photos/耳罩-降噪-耳机-2755553/](https://pixabay.com/photos/earmuffs-noise-cancelling-headphones-2755553/)

# 将测量噪声转化为有用的结果。

我有幸参与了 Fire Opal 的 pre-beta 测试，但它并没有对我的各种项目公开。因此，在等待它的正式发布时，我不得不尽可能地模仿它的功能。这意味着以某种顺序实现多个库，从而改进我的测量结果，尽管还没有达到 Fire Opal 的程度。

我已经把教程精简到了最基本的部分，我将回顾一下要点。然后我会回顾好的，坏的，和丑陋的。

```
import fireopal

token = "" # Insert your IBM Quantum API token here.
hub = "ibm-q"
group = "open"
project = "main"

credentials = {"token": token, "hub": hub, "group": group, "project": project}

#supported_devices = fireopal.show_supported_devices(hardware_provider="IBM")["supported_devices"]
#for name in supported_devices:print(name)
backend = "" # Insert your selected backend here.

qc_list = [circuit_qasm, circuit_qasm]

validate_results = fireopal.validate(circuits=qc_list, credentials=credentials, backend=backend)
if validate_results["results"] == []:
    print("No errors found.")
else:
    print("The following errors were found:")
    for error in validate_results["results"]:print(error)

real_hardware_results = fireopal.execute(circuits=qc_list, shot_count=100, credentials=credentials, backend=backend,)
bitstring_counts = real_hardware_results["results"]
print(bitstring_counts)
```

## 代码

当您一行一行地理解 Fire Opal 时，您将有望体会到它的相对简单性和易用性。

```
import fireopal
```

我希望更多的进口积木看起来像这样。

```
token = "" # Insert your IBM Quantum API token here.
hub = "ibm-q"
group = "open"
project = "main"
```

这是为了访问您的 IBM Quantum 帐户。请注意，您的 API 令牌是必需的。IBM Quantum 的资深用户知道，您的 API 令牌在本地只需要一次，在 Quantum Lab 中根本不需要。但是，你将需要它来使用火蛋白石。

```
credentials = {"token": token, "hub": hub, "group": group, "project": project}
```

这是特定于 Fire Opal 的，因为同样需要 IBM Quantum API 令牌。如果您想正常运行您的电路，将结果与 Fire Opal 进行比较，则需要一行不同的代码。

```
#supported_devices = fireopal.show_supported_devices(hardware_provider="IBM")["supported_devices"]
#for name in supported_devices:print(name)
```

Fire Opal 支持 IBM 后端，但不是所有的 IBM 后端。取消对这些行的注释以显示您的选项…

```
backend = "" # Insert your selected backend here.
```

…然后从列表中选择您最喜欢的后端。

*重要！后端只是设备的名称。使用 Qiskit，我们使用设备的名称来获取提供者后端。但是，提供者后端是一个 Qiskit 对象。火蛋白石不承认这个 Qiskit 对象；它只需要与我们用来获取 Qiskit 对象的名称相同。*

```
qc_list = [circuit_qasm, circuit_qasm]
```

我没有在任何地方看到这个广告，但是你可以创建一批电路，Fire Opal 将执行批中的每个电路。那是一次排队时间而不是多次。

*重要！circuit_qasm 变量是 OpenQASM 2 的字符串，而不是 Qiskit 电路对象。因此，如果您没有导入 OpenQASM 2，您需要在执行之前用 circuit.qasm()转换您的电路。*

```
validate_results = fireopal.validate(circuits=qc_list, credentials=credentials, backend=backend)
if validate_results["results"] == []:
    print("No errors found.")
else:
    print("The following errors were found:")
    for error in validate_results["results"]:print(error)
```

这个街区真的很有趣。你电路太深怎么办？执行时间比量子位的相干时间长，因此不可能得到有用的结果。如果你付费使用，这是非常有用的信息。不要浪费你的钱去跑那条线路，即使你没有为接入付费，也不要浪费你的时间。

我不知道它可能会给你的每一个警告，但我发现如果你的电路需要比你选择的后端更多的量子位，这个代码也会提醒你。这也是一个潜在的节省时间的方法。另外，所有这些警告对 Q-CTRL 来说都是明智的保险:不要让 Fire Opal 由于它无法控制的问题而失败；停止执行，说明问题。

```
real_hardware_results = fireopal.execute(circuits=qc_list, shot_count=100, credentials=credentials, backend=backend,)
bitstring_counts = real_hardware_results["results"]
print(bitstring_counts)
```

而这一切都归结为 *fireopal.execute()* 。一行代码让你离实用的量子计算更近了一步。

## 好人

**自由了！这让我大吃一惊。多年来我一直确信火蛋白石会很贵。我错了。**

**简单。**没什么可配置的。当然，您必须提供一些信息，但是这里没有任何 IBM Quantum 用户应该努力解决的问题。

**批量。正如我前面提到的，Fire Opal 支持批处理作业，这是一个真正的省时器。您在一个队列中等待，批处理中的所有电路顺序执行，您就完成了。**

**验证。**不要运行不可能返回有用结果的电路，这样可以节省时间和金钱，因为量子位根本无法保持足够长的时间来执行所有你想发送给它们的指令，或者你选择了太小的后端，和/或该功能检测到的任何其他东西。

**硬件。** Fire Opal 独树一帜，因为它不仅优化了电路，还减少了测量误差。它建立在 Q-CTRL 的 Boulder Opal 产品之上，这意味着它在执行过程中会影响硬件。

**结果。**结果不是模拟器质量，因为我们仍然没有逻辑量子位，但是你绝对可以把均匀分布(所有可能的测量结果大致相同)变成有用的结果(一些测量结果比其他的更有可能)。

## 坏事

**没有动态电路。** Fire Opal 还不支持 OpenQASM 3，因此还不支持动态电路。尽管 OpenQASM 3 是新的，所以它的受众可能还是有限的。我是观众之一，但是，嘿，我也设计静态电路。*好消息是，对动态电路的支持已列入 2023 年初的 Fire Opal 路线图。*

**没有记忆。** Qiskit 允许检索单个测量值。例如，您运行一个有 100 个镜头的电路，并且通常关心每个测量的计数，但是如果需要，您可以逐个检索它们。此时，火蛋白石只返回计数。

**有限的后端。** Fire Opal 目前只在 IBM 的特定后端上工作。这实际上不会以任何方式对我产生负面影响，但它在“坏”下面，以防它对你产生负面影响。Q-CTRL 已经在 AWS Braket 后端测试了 Fire Opal，并计划在 2023 年初推出。

**API 令牌。这使得你的代码不太容易被共享，因为你必须记住首先移除你的 API 令牌。**

## 丑陋的

**没什么。火蛋白石很棒，只要不需要动态电路和/或单独测量，它将在我所有可预见的代码中占有一席之地。**

## 结论

[今天就从](https://docs.q-ctrl.com/fire-opal/get-started)[火蛋白石](https://q-ctrl.com/fire-opal)开始。我说过它是免费的吗？

## 火蛋白石系列:

*   [量子切片面包](https://bsiegelwax.medium.com/quantum-sliced-bread-bd3dd048f)
*   [我从火蛋白石中学到了什么…](https://bsiegelwax.medium.com/what-i-learned-from-fire-opal-50608282972b)
*   [Q-CTRL 的火猫眼石很牛逼](/fire-opal-is-awesome-c642347ec89d)