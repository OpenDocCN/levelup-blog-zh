# 动态量子电路，第五课

> 原文：<https://levelup.gitconnected.com/dynamic-quantum-circuits-lesson-5-ed3d24101dd0>

![](img/bfbd7757ce73cc1acb4104c987992bb3.png)

[https://pix abay . com/photos/woman-Mariah-Carey-singer-190897/](https://pixabay.com/photos/woman-mariah-carey-singer-190897/)

# 动态经典寄存器

动态量子电路在 [IBM 量子峰会 2022](https://medium.com/@bsiegelwax/ibm-quantum-summit-2022-d1c646169189) 上宣布，并在第二天的 [IBM 量子从业者论坛](https://bsiegelwax.medium.com/ibm-quantum-practitioners-forum-2022-67a31a23407f)上展示了一些细节。简而言之，你在将静态量子电路排队运行之前，先设计它们的整体，而在它们排队运行之后和实际运行期间，你使用经典逻辑来生成动态电路。

动态电路的局限性之一是条件逻辑测试涉及整个经典寄存器。虽然您可以在一个大寄存器中测量不同的经典位，但您不能只使用有限的一组位来进行比较。使用寄存器只是一个要么全有要么全无的命题。虽然您可以重用经典寄存器，但这样做会破坏您以前的测量结果。如果您需要检索您的中间电路测量，您需要使用单独的经典寄存器。

假设您在设计电路时知道需要多少个寄存器，因此每个教程都会显示每个寄存器都是单独初始化的。但是，我不只是设计动态电路，我是动态设计动态电路。我事先不知道需要多少个寄存器，但是我的代码会算出来。因此，我需要能够有效地初始化所需寄存器的确切数量。

```
qr = QuantumRegister(number_of_qubits, name="qr")
qc = QuantumCircuit(qr)
```

## 量子优先

当我输入这个的时候，我意识到我们也可以动态地添加量子寄存器，但是我并不迫切需要这样做，所以我将只使用经典寄存器来演示这个原理。根据我的需要，我只初始化了量子寄存器和量子电路，如上所示。

```
iterations = 5
cr = []
for i in range(0, iterations):
    name = "cr" + str(i)
    cr.append(ClassicalRegister(number_of_bits, name=name))
    register = cr[i]
    qc.add_register(register)
```

## 动态初始化经典寄存器

虽然我在上面展示了 5 次迭代，但这是我实际上事先并不知道的值。我需要的经典寄存器的数量是在运行时计算的。因此，我计算了我需要的迭代次数，然后创建一个列表来存储寄存器。

*名称*和*寄存器*变量是关键。在初始化一个*classic register*的时候你不能做任何动态的事情，你也不能把一个列表项传递给 *add_register()* ，但是你可以先做动态的事情，然后传递静态的变量。

```
for i in range(1, iterations):
    previous_register = cr[i-1]
    current_register = cr[i] 
    with qc.if_test((previous_register, i)): 
        ... something ... 
    qc.measure(qr, current_register)
```

## 动态使用经典寄存器

最后一句话也适用于此。不能将列表项传递给 *if_test()* 或 *measure()* ，但是可以将那些列表项分配给变量，比如 *previous_register* 和 *current_register* ，然后将那些静态变量传递给那些函数。

瞧啊。动态经典寄存器。

## 动态量子电路系列

*   [动态量子电路，第四课](https://bsiegelwax.medium.com/dynamic-quantum-circuits-lesson-4-4527c4527a2c)
*   [动态量子电路，第三课](https://bsiegelwax.medium.com/dynamic-quantum-circuits-lesson-3-7b9e29e85daa)
*   [动态量子电路，第二课](https://medium.com/gitconnected/dynamic-quantum-circuits-lesson-2-d31d7a287c62)
*   [动态量子电路，第一课](https://medium.com/@bsiegelwax/dynamic-quantum-circuits-lesson-1-f712bc6a9e1d)