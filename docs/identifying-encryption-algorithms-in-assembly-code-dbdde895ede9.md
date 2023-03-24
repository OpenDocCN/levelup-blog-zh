# 识别汇编代码中的加密算法

> 原文：<https://levelup.gitconnected.com/identifying-encryption-algorithms-in-assembly-code-dbdde895ede9>

## 综合指南

## 关于如何识别汇编代码中的加密算法的详细说明。

对于那些对逆向工程感兴趣的人(无论是作为一个潜在的职业还是仅仅作为一个爱好)，你在旅途中将不可避免地遇到加密算法。也许您正在逆转一些通过互联网通信的未记录的软件，或者也许您只是在解决一个涉及到如何生成密钥的难题。无论您的原因是什么，您都将受益于对常见加密算法的工作原理、实现方式以及如何在汇编代码中识别这些算法的透彻理解。

在这一系列文章中，我将介绍一些常见的加密算法，希望这些信息对您有所帮助。

# 第一种加密算法:异或加密

这可以说是最简单的加密算法之一，至少是最容易实现的算法之一。这也使得在汇编代码中很容易识别。我们先来看看什么是异或加密算法。

XOR 密码是一种使用密钥对某些输入进行 XOR 运算的加密算法。这实现起来非常简单，但是要破坏它也很容易。因为许多其他更高级的密码使用 XOR，所以我认为先介绍这个算法是个好主意。

这里有一个实现异或加密的小程序。

```
char* encryptString(std::string message)
{
 size_t messageLength = message.length();
 char key = ‘L’;
 char* encrypted = new char[messageLength + 1](); for (int i = 0; i < messageLength; i++) {
   encrypted[i] = message[i] ^ key;
 } return encrypted;}
```

我们只是使用一个仅由一个字符组成的密钥对消息进行异或运算:“L”。通常情况下，这个键不止一个字符，但是我决定在本文中保持简单。

通过使用调试器，我们可以通过查看程序如何处理来自用户的输入来判断程序是否使用了 XOR 加密。让我带你看看我解决这个问题的过程。

# 用调试器分析代码

使用任何你喜欢的调试器。我将使用 [Ollydbg](http://www.ollydbg.de/) ,所以如果我包含任何快捷方式，这些快捷方式将专门应用于 Ollydbg，并且它可能不会在其他调试器上同样工作。让我们从将程序加载到调试器中开始。

![](img/8abf82c5d1592cd9acde15b031b97c31.png)

我们需要做的第一件事是定位主函数。我喜欢使用的一个技巧是在 ucrtbased.dll 模块中为 __p_argc 和 __p_argv 函数设置一个断点。这是因为程序处于调试模式。对主函数应该是什么样子有一些先验知识是有帮助的，但是在本文中我不会对此进行过多的描述。

要设置断点，请按 ALT + E 获取所有已加载模块的列表。

![](img/66ea5e011086505acd5d659b0db668d6.png)

如果您突出显示 ucrtbased.dll 模块，您可以通过按 CTRL + N 找到一个函数列表。

![](img/0416a89a2fbf6c2e4f9fc35cce9a9187.png)

现在可以定位 argv 和 argc 函数名，并为每个函数设置一个断点。只为 argc 设置一个断点也足够了，但是让我们为两者都设置一个断点。

![](img/3b7be4fa174c433ec52561c65d26c5af.png)

您可以通过突出显示这些函数并按 F2 键来为它们设置断点。

我们需要做的下一件事是运行程序，直到它到达我们的一个断点，然后我们遍历调用堆栈，直到我们找到将调用我们的主函数的代码。

![](img/546381b369c0045168bf59d1cbd721f4.png)

根据过去的经验，我知道这个函数将调用我们代码的主函数。图中突出显示的函数调用是调用我们的 main 函数，而上面的其他函数是 argv 和 argc 函数调用。

如果我们进入主函数，我们会立即看到一个值为“message here”的字符串。

![](img/cfadc12b726398f6d249c367178c9ef5.png)

在我之前浏览这个程序的时候，我还擅自给某个函数调用添加了一个注释。评论真的很有用，所以好好利用它们。我这里当然指的是“Encrypt func”的注释。

# 加密功能

您可能注意到的第一件事是字符串“message here”被推送到堆栈上，ecx 被设置为包含指向某个堆栈变量的指针，然后调用一个函数。您可能会怀疑这个函数调用就是调用我们的加密函数的那个，但它不是。这实际上是一个遵循 __thiscall 约定的函数调用，其中 ecx 包含指向隐式 *this* 变量的指针。如果你在函数调用时设置一个断点，然后你看一下 *this* 指针的内容(右击 ecx 寄存器，然后转储)，你会发现它只是一堆 *CC*

![](img/8630f3b618463d065a5fc3821153559c.png)

很美，不是吗？

这意味着指针所指向的堆栈变量是未初始化的。当你在调试模式下编译一个程序时，CC 代表未初始化的内存。所有这些都告诉我们，函数调用是构造函数(或多或少)，而不是我们感兴趣的东西。

再往下我们会看到更多的函数调用，最终我们会看到加密函数调用。我解决这个问题的方法只是为每个函数调用设置一个断点，然后单步执行每个调用，看看 CPU 寄存器中是否出现了一些有趣的东西。我注意到 EAX 寄存器中存储了一个字符串文字，它与“message here”字符串的长度相同。所以，那一定是函数调用。让我们进入函数，看看我们是否能识别 xor 加密。

```
008557A0 55 PUSH EBP
008557A1 8BEC MOV EBP,ESP
008557A3 6A FF PUSH -1
008557A5 68 88B78500 PUSH Encrypti.0085B788
008557AA 64:A1 00000000 MOV EAX,DWORD PTR FS:[0]
008557B0 50 PUSH EAX
008557B1 81EC 18010000 SUB ESP,118
008557B7 53 PUSH EBX
008557B8 56 PUSH ESI
008557B9 57 PUSH EDI
008557BA 8DBD DCFEFFFF LEA EDI,DWORD PTR SS:[EBP-124]
008557C0 B9 46000000 MOV ECX,46
008557C5 B8 CCCCCCCC MOV EAX,CCCCCCCC
008557CA F3:AB REP STOS DWORD PTR ES:[EDI]
008557CC A1 08108600 MOV EAX,DWORD PTR DS:[861008]
008557D1 33C5 XOR EAX,EBP
008557D3 50 PUSH EAX
008557D4 8D45 F4 LEA EAX,DWORD PTR SS:[EBP-C]
008557D7 64:A3 00000000 MOV DWORD PTR FS:[0],EAX
008557DD C745 FC 00000000 MOV DWORD PTR SS:[EBP-4],0
008557E4 B9 27408600 MOV ECX,Encrypti.00864027
008557E9 E8 B9BBFFFF CALL Encrypti.008513A7
008557EE 8D4D 08 LEA ECX,DWORD PTR SS:[EBP+8]
008557F1 E8 27B9FFFF CALL Encrypti.0085111D
008557F6 8945 EC MOV DWORD PTR SS:[EBP-14],EAX
008557F9 C645 E3 4C MOV BYTE PTR SS:[EBP-1D],4C
008557FD 8B45 EC MOV EAX,DWORD PTR SS:[EBP-14]
00855800 83C0 01 ADD EAX,1
00855803 8985 FCFEFFFF MOV DWORD PTR SS:[EBP-104],EAX
00855809 8B8D FCFEFFFF MOV ECX,DWORD PTR SS:[EBP-104]
0085580F 51 PUSH ECX
00855810 E8 1CB9FFFF CALL Encrypti.00851131
00855815 83C4 04 ADD ESP,4
00855818 8985 F0FEFFFF MOV DWORD PTR SS:[EBP-110],EAX
0085581E 83BD F0FEFFFF 00 CMP DWORD PTR SS:[EBP-110],0
00855825 74 26 JE SHORT Encrypti.0085584D
00855827 8B95 FCFEFFFF MOV EDX,DWORD PTR SS:[EBP-104]
0085582D 52 PUSH EDX
0085582E 6A 00 PUSH 0
00855830 8B85 F0FEFFFF MOV EAX,DWORD PTR SS:[EBP-110]
00855836 50 PUSH EAX
00855837 E8 5EB9FFFF CALL Encrypti.0085119A
0085583C 83C4 0C ADD ESP,0C
0085583F 8B8D F0FEFFFF MOV ECX,DWORD PTR SS:[EBP-110]
00855845 898D DCFEFFFF MOV DWORD PTR SS:[EBP-124],ECX
0085584B EB 0A JMP SHORT Encrypti.00855857
0085584D C785 DCFEFFFF 00>MOV DWORD PTR SS:[EBP-124],0
00855857 8B95 DCFEFFFF MOV EDX,DWORD PTR SS:[EBP-124]
0085585D 8955 D4 MOV DWORD PTR SS:[EBP-2C],EDX
00855860 C745 C8 00000000 MOV DWORD PTR SS:[EBP-38],0
00855867 EB 09 JMP SHORT Encrypti.00855872
00855869 8B45 C8 MOV EAX,DWORD PTR SS:[EBP-38]
0085586C 83C0 01 ADD EAX,1
0085586F 8945 C8 MOV DWORD PTR SS:[EBP-38],EAX
00855872 8B45 C8 MOV EAX,DWORD PTR SS:[EBP-38]
00855875 3B45 EC CMP EAX,DWORD PTR SS:[EBP-14]
00855878 73 1F JNB SHORT Encrypti.00855899
0085587A 8B45 C8 MOV EAX,DWORD PTR SS:[EBP-38]
0085587D 50 PUSH EAX
0085587E 8D4D 08 LEA ECX,DWORD PTR SS:[EBP+8]
00855881 E8 7BBBFFFF CALL Encrypti.00851401
00855886 0FBE08 MOVSX ECX,BYTE PTR DS:[EAX]
00855889 0FBE55 E3 MOVSX EDX,BYTE PTR SS:[EBP-1D]
0085588D 33CA XOR ECX,EDX
0085588F 8B45 D4 MOV EAX,DWORD PTR SS:[EBP-2C]
00855892 0345 C8 ADD EAX,DWORD PTR SS:[EBP-38]
00855895 8808 MOV BYTE PTR DS:[EAX],CL
00855897 ^EB D0 JMP SHORT Encrypti.00855869
00855899 8B45 D4 MOV EAX,DWORD PTR SS:[EBP-2C]
0085589C 8985 E4FEFFFF MOV DWORD PTR SS:[EBP-11C],EAX
008558A2 C745 FC FFFFFFFF MOV DWORD PTR SS:[EBP-4],-1
008558A9 8D4D 08 LEA ECX,DWORD PTR SS:[EBP+8]
008558AC E8 B0BAFFFF CALL Encrypti.00851361
008558B1 8B85 E4FEFFFF MOV EAX,DWORD PTR SS:[EBP-11C]
008558B7 8B4D F4 MOV ECX,DWORD PTR SS:[EBP-C]
008558BA 64:890D 00000000 MOV DWORD PTR FS:[0],ECX
008558C1 59 POP ECX
008558C2 5F POP EDI
008558C3 5E POP ESI
008558C4 5B POP EBX
008558C5 81C4 24010000 ADD ESP,124
008558CB 3BEC CMP EBP,ESP
008558CD E8 F3BAFFFF CALL Encrypti.008513C5
008558D2 8BE5 MOV ESP,EBP
008558D4 5D POP EBP
008558D5 C3 RETN
```

我在这里做的第一件事是立即扫描任何 XOR 指令。你瞧，我发现了两条异或指令！这是第一个，以及与之相关的代码。

```
008557BA 8DBD DCFEFFFF LEA EDI,DWORD PTR SS:[EBP-124]
008557C0 B9 46000000 MOV ECX,46
008557C5 B8 CCCCCCCC MOV EAX,CCCCCCCC
008557CA F3:AB REP STOS DWORD PTR ES:[EDI]
008557CC A1 08108600 MOV EAX,DWORD PTR DS:[861008]
008557D1 33C5 XOR EAX,EBP
```

我们可以立即忽略这一点，因为它是调试模式代码的一部分。你认出 C 了吗？；)

下一条 XOR 指令及其相关代码如下所示:

```
00855869 8B45 C8 MOV EAX,DWORD PTR SS:[EBP-38]
0085586C 83C0 01 ADD EAX,1
0085586F 8945 C8 MOV DWORD PTR SS:[EBP-38],EAX
00855872 8B45 C8 MOV EAX,DWORD PTR SS:[EBP-38]
00855875 3B45 EC CMP EAX,DWORD PTR SS:[EBP-14]
00855878 73 1F JNB SHORT Encrypti.00855899
0085587A 8B45 C8 MOV EAX,DWORD PTR SS:[EBP-38]
0085587D 50 PUSH EAX
0085587E 8D4D 08 LEA ECX,DWORD PTR SS:[EBP+8]
00855881 E8 7BBBFFFF CALL Encrypti.00851401
00855886 0FBE08 MOVSX ECX,BYTE PTR DS:[EAX]
00855889 0FBE55 E3 MOVSX EDX,BYTE PTR SS:[EBP-1D]
0085588D 33CA XOR ECX,EDX
0085588F 8B45 D4 MOV EAX,DWORD PTR SS:[EBP-2C]
00855892 0345 C8 ADD EAX,DWORD PTR SS:[EBP-38]
00855895 8808 MOV BYTE PTR DS:[EAX],CL
00855897 ^EB D0 JMP SHORT Encrypti.00855869
```

就在异或指令之前，一个字节存储在 ECX 和 EDX 中。乍一看，这很有希望。如果我们在 XOR 指令处设置一个断点，我们会看到 EAX 包含字符串文字:“message here”。太好了！这肯定是加密消息的代码部分。ECX 寄存器包含字符串的第一个字母，而 EDX 寄存器包含字母“L”。

如果我们继续往下看，我们可以看到我们最终会到达一条 JMP 指令，它会跳回到地址 00855869。这只是将一个值加载到 EAX 中，然后加 1。最终，它将 EAX 与某种价值相比较。

![](img/fac421c74da75253e758ab5e4a270dce.png)

如果在 cmp 指令处设置一个断点，并突出显示它，则可以在“汇编代码”面板下的面板中看到此信息。

它将 EAX 与十进制数为 12 的 0C 值进行比较。这个数字恰好也是我们的字符串“message here”的长度。巧合吗？我认为不是。这看起来只是一个简单的 for 循环，它将输入字符串的每个字节与字符“L”进行异或运算。

这应该涵盖了如何识别异或加密。显然，我的程序非常简单，要找到正确的代码并不总是那么容易，但这肯定会帮助您在野外识别 XOR 加密。

因为我不希望这篇文章有一本书那么长，所以我将在本系列的第二部分中讨论下一个加密算法。写完之后我会在这篇文章的底部留一个文章的链接。

感谢阅读！