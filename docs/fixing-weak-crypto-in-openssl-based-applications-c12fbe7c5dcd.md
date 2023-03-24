# 修复基于 OpenSSL 的应用程序中的弱加密

> 原文：<https://levelup.gitconnected.com/fixing-weak-crypto-in-openssl-based-applications-c12fbe7c5dcd>

![](img/d99b30e53a9ca82074befad50a2ff78a.png)

本杰明·雷曼在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

【https://pqsec.org】原载于 2020 年 4 月 13 日[](https://pqsec.org/2020/04/13/fixing-weak-crypto-in-openssl-based-applications.html)**。**

# *为什么加密变弱了*

*没有人会故意设计弱密码算法。嗯，几乎没有人——有时国家情报机构为了他们自己的目的试图后门加密([),但希望这是一个例外，一般来说，人们都有最好的意图。](https://en.wikipedia.org/wiki/Dual_EC_DRBG#Weakness:_a_potential_backdoor)*

*那么，为什么有些 crypto 会突然变弱呢？所有实用的密码算法都是围绕一些困难的计算问题而设计的。也就是说，实际上很难(但不是不可能！)在不知道某些秘密信息(密钥)的情况下有效地执行算法。强加密的基本假设是:*

*   *不存在允许在不拥有密钥的情况下进行特定信息转换(例如，解密或签名)的有效算法*
*   *允许进行上述操作的所有现有算法都需要大量资源(通常是计算时间和/或内存)，这使得它们不切实际，因为使用当前的技术，要么需要数百年或数千年来破解单个字节，要么整个世界都没有足够的内存来容纳该算法的状态*

*当这些假设中的一个或者甚至两个都不再成立时，特定的加密算法变得脆弱。第一个假设可能会被打破，当一些研究人员发明并公布了一种算法，使一个困难的计算问题不再困难:公布的方法可能会大大降低破解受保护信息的计算/内存需求。例如，查看为什么 [RC4 密码不再用于 TLS](https://www.rc4nomore.com/)。*

*随着时间的推移，第二个假设自然会被打破，主要是因为快速的技术进步。不仅计算机每天都变得更强大，更多的计算和内存资源可用，而且全新的技术也出现了，这些技术允许[在瞬间完全破解一些现代和最安全的非对称密码系统](https://en.wikipedia.org/wiki/Quantum_computing#Cryptography)。*

# *假设案例研究*

*假设你是 SaaS 一家公司的安全工程师，该公司提供云文档存储。您的云运行来自某个供应商的第三方专有软件堆栈。系统中的所有文档都按其 id 进行索引，这些 id 是在文档首次上传时生成的。您的第三方软件供应商认为，为文档生成这个 ID 的最简单方法是计算它的 **SHA-1 值**。*

*有一天你来到办公室，看到一片混乱:世界不再一样了，因为 [SHA-1 在实践中被正式宣布破](https://shattered.io/)(这部分是真的！).您的公司联系供应商提供修补程序，但是，正如供应商经常发生的那样，他们要么说需要几个月或几年才能提供修补程序，要么说攻击“不适用于软件安全模型”。无论哪种方式，你的公司不同意，你的工作是提供一个补丁，而企业正在寻找替代方案。*

# *工具分析*

*我们之前一致认为第三方供应商软件是专有的，但出于本练习的目的(因此您可以在家里编译并运行它)，这里是假设工具的源代码版本:*

**customhash.c:**

```
***#include <stdio.h>
#include <errno.h>** 
**#include <openssl/evp.h>
#include <openssl/sha.h>** 
**static** **int** **hash**(**FILE** *****f)
{
    **int** err, i;
    **unsigned** **char** md[SHA_DIGEST_LENGTH];
    **unsigned** **int** md_size;

    **unsigned** **char** buf[4096], *****pos;
    **size_t** bytes_read;

    pos **=** buf;
    bytes_read **=** fread(buf, 1, buf **+** **sizeof**(buf) **-** pos, f);
    **while** (bytes_read **&&** pos **<** (buf **+** **sizeof**(buf)))
    {
        pos **+=** bytes_read;
        bytes_read **=** fread(buf, 1, buf **+** **sizeof**(buf) **-** pos, f);
    }

    **if** (**!**feof(f))
    {
        errno **=** EIO;
        **return** errno;
    }

    **if** (**!**EVP_Digest(buf, pos **-** buf, md, **&**md_size, EVP_sha1(), NULL))
    {
        errno **=** EFAULT;
        **return** errno;
    }

    **for** (i **=** 0; i **<** md_size; i**++**)
        printf("%02x", md[i]);
    puts("");

    **return** 0;
}
**int** **main**(**int** argc, **char** ******argv)
{
    **int** err;
    **FILE** *****f **=** stdin;
    **if** (argc **>** 1) {
        f **=** fopen(argv[1], "rb");
        **if** (**!**f) {
            perror(NULL);
            **return** errno;
        }
    }

    err **=** hash(f);
    **if** (err)
        perror(NULL);

    **if** (argc **>** 1)
        fclose(f);

    **return** err;
}*
```

*因此，简而言之，该工具只是将文件的内容读入缓冲区，并计算其 SHA-1。让我们通过将其输出与众所周知的 SHA-1 实现进行比较来验证它的工作情况:*

```
*$ gcc -o customhash customhash.c -lcrypto
$ echo abc | ./customhash
03cfd743661f07975fa2f1220c5194cbaff48451
$ echo abc | sha1sum
03cfd743661f07975fa2f1220c5194cbaff48451  -*
```

*确实有效。但是请记住:这个工具不是我们自己编译的——它是专有的。但是，我们可以检查该工具是静态链接还是动态链接，以及在后一种情况下它使用什么库:*

```
*$ ldd ./customhash
	linux-vdso.so.1 (0x00007ffc26bea000)
	libcrypto.so.1.1 => /lib/x86_64-linux-gnu/libcrypto.so.1.1 (0x00007f6350798000)
	libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f63505d7000)
	libdl.so.2 => /lib/x86_64-linux-gnu/libdl.so.2 (0x00007f63505d2000)
	libpthread.so.0 => /lib/x86_64-linux-gnu/libpthread.so.0 (0x00007f63505b1000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f6350a94000)*
```

*我们很幸运:它是动态链接的，并且使用了 [OpenSSL](https://www.openssl.org/) 。我们在这篇文章中关注 OpenSSL 的原因是因为 OpenSSL 是许多应用程序，甚至是专有应用程序中事实上的加密库，因为它的成熟性和许可性。*

# *用 LD_PRELOAD 挂钩加密*

*是修改动态链接应用程序行为的强大工具:通过定义一个环境变量和编写一些代码，几乎可以覆盖任何库函数。在我们的例子中，我们希望用更安全的 SHA-256 来替换我们玩具专有工具中的 SHA-1 计算。但是首先我们需要实际知道“挂钩”哪个函数(替换它):*

```
*$ nm -D ./customhash | grep 'U '
                 U __errno_location
                 U EVP_Digest
                 U EVP_sha1
                 U fclose
                 U feof
                 U fopen
                 U fread
                 U __libc_start_main
                 U perror
                 U printf
                 U puts*
```

*上面的命令从链接的动态库中输出我们的`customhash`工具使用的所有函数(“U”可能代表“uses”)。大多数函数来自于`libc`，但是`EVP_Digest`和`EVP_sha1`来自于 OpenSSL(如果我们用谷歌搜索这些，我们会被引导到 OpenSSL 在线手册页)。在这一点上，我们需要写一个小的动态库，它用相同的签名导出相同的函数，但是计算 SHA-256。事实上，我们只需要替换`EVP_Digest`，因为`EVP_sha1`只返回内部的 OpenSSL SHA-1 算法 ID。一个潜在的实现可能如下所示:*

**cryptofix.c:**

```
***#define _GNU_SOURCE** */* for RTLD_NEXT */* **#include <dlfcn.h>
#include <stdio.h>
#include <string.h>** 
**#include <openssl/evp.h>
#include <openssl/sha.h>** 
**int** **EVP_Digest**(**const** **void** *****data, **size_t** count, **unsigned** **char** *****md, **unsigned** **int** *****size, **const** EVP_MD *****type, ENGINE *****impl)
{
    **unsigned** **char** sha256_md[SHA256_DIGEST_LENGTH];
    **unsigned** **int** sha256_md_size, err;

    **static** **int** (*****real_fn)(**const** **void** *****data, **size_t** count, **unsigned** **char** *****md, **unsigned** **int** *****size, **const** EVP_MD *****type, ENGINE *****impl) **=** NULL;
    **if** (**!**real_fn)
    {
        real_fn **=** dlsym(RTLD_NEXT, "EVP_Digest");
        **if** (**!**real_fn)
        {
            fputs("cannot find EVP_Digest", stderr);
            exit(1);
        }
    }

    **if** (type **==** EVP_sha1())
    {
        err **=** real_fn(data, count, sha256_md, **&**sha256_md_size, EVP_sha256(), impl);
        fputs("replacing SHA1 with SHA256\n", stderr);
        memcpy(md, sha256_md, SHA_DIGEST_LENGTH);
        *****size **=** SHA_DIGEST_LENGTH;
        **return** err;
    }
    **else**
        **return** real_fn(data, count, md, size, type, impl);
}*
```

*从上面的实现中有一些事情需要注意:首先，我们需要得到一个指向真正的 OpenSSL `EVP_Digest`函数的指针，这样我们就可以转发对它的调用。我们使用`libdl`中的技巧获得地址。这是必要的，因为`EVP_Digest`是一个包装函数，它计算 OpenSSL 支持的任何散列算法。所以我们不能用阿沙-256 实现来代替它，因为调用应用程序可能同时依赖不同的哈希算法，并对所有算法使用相同的函数。因此，在我们的实现中，我们“过滤掉”请求 SHA-1 计算的调用，并按原样传递其余的调用。*

*其次，我们不想自己编写阿沙-256 实现。我们已经知道应用程序正在使用 OpenSSL，所以当我们的代码运行时，我们可以访问进程地址空间中的 OpenSSL 库。而且，我们已经有了从上面获得的 OpenSSL `EVP_Digest`地址，所以我们只需要调用 OpenSSL 来为我们计算 SHA-256。*

*最后，SHA-1 的输出只有 20 个字节，但是 SHA-256 产生了 32 个字节。OpenSSL 将结果返回给调用者分配的缓冲区，但此时我们不能假设调用应用程序分配了足够的内存来存储完整的 SHA-256 结果，因为它预期的是阿沙-1 哈希。为了安全起见，并且不引入缓冲区溢出，在将结果返回给调用者之前，我们将从计算的 SHA-256 中去掉额外的 12 个字节。一些安全研究可能认为截断散列结果会降低安全性，并且它们将是正确的。然而，对于这个用例，使用具有截断结果的安全散列算法比不安全散列算法更安全。*

*让我们检查一下是否一切正常:*

```
*$ gcc -shared -fPIC -o cryptofix.so ;.c
$ echo abc | LD_PRELOAD=./cryptofix.so ./customhash
replacing SHA1 with SHA256
edeaaff3f1774ad2888673770c6d64097e391bc3
$ echo abc | sha256sum
edeaaff3f1774ad2888673770c6d64097e391bc362d7d6fb34982ddf0efd18cb  -*
```

*万岁！我们成功地用更强的 SHA-256 替换了弱 SHA-1，而没有触及原始应用程序中的任何代码。*

# *供应商反击了*

*虽然供应商可能会找到理由拒绝修复我们不安全的算法，但他们有义务修复错误。如果我们检查我们的 toy `customhash.c`工具，我们可能会注意到它有一个缺陷:它不能计算大于 4096 字节的文件的散列，因为在`hash`函数中有静态缓冲区:*

```
*$ printf 'a%.0s' {1..4095} | ./customhash
10236568a284fb3733bd87c15280af95bd528839
$ printf 'a%.0s' {1..4096} | ./customhash
Input/output error*
```

*所以供应商修复了它，并交付了更新的工具(因为他们的代码很好地解耦了，他们保留了`main`功能，只是用相同的原型重写了`hash`功能的实现):*

**customhashv2.c:**

```
*...
**static** **int** **hash**(**FILE** *****f)
{
    **int** err, i;
    **unsigned** **char** md[SHA_DIGEST_LENGTH];
    **unsigned** **int** md_size;

    **unsigned** **char** buf[256];
    **size_t** bytes_read;

    EVP_MD_CTX *****ctx **=** EVP_MD_CTX_new();
    **if** (**!**ctx)
    {
        errno **=** ENOMEM;
        **return** errno;
    }

    **if** (**!**EVP_DigestInit(ctx, EVP_sha1()))
    {
        EVP_MD_CTX_free(ctx);
        errno **=** EFAULT;
        **return** errno;
    }

    bytes_read **=** fread(buf, 1, **sizeof**(buf), f);
    **while** (bytes_read)
    {
        **if** (**!**EVP_DigestUpdate(ctx, buf, bytes_read))
        {
            EVP_MD_CTX_free(ctx);
            errno **=** EFAULT;
            **return** errno;
        }
        bytes_read **=** fread(buf, 1, **sizeof**(buf), f);
    }

    **if** (**!**feof(f))
    {
        EVP_MD_CTX_free(ctx);
        errno **=** EIO;
        **return** errno;
    }

    **if** (**!**EVP_DigestFinal(ctx, md, **&**md_size))
    {
        EVP_MD_CTX_free(ctx);
        errno **=** EFAULT;
        **return** errno;
    }

    **for** (i **=** 0; i **<** md_size; i**++**)
        printf("%02x", md[i]);
    puts("");

    **return** 0;
}
...*
```

*让我们检查一下它是否有效:*

```
*$ gcc -o customhashv2 customhashv2.c -lcrypto
$ echo abc | ./customhashv2
03cfd743661f07975fa2f1220c5194cbaff48451
$ echo abc | sha1sum
03cfd743661f07975fa2f1220c5194cbaff48451  -
$ printf 'a%.0s' {1..4096} | ./customhashv2
8c51fb6a0b587ec95ca74acfa43df7539b486297
$ printf 'a%.0s' {1..4096} | sha1sum
8c51fb6a0b587ec95ca74acfa43df7539b486297  -*
```

*很好！漏洞已经修复，但我们的黑客工作:*

```
*$ echo abc | LD_PRELOAD=./cryptofix.so ./customhashv2
03cfd743661f07975fa2f1220c5194cbaff48451*
```

*我们再也看不到“用 SHA256 替换 SHA1”的消息，新工具可以清楚地计算 SHA-1。这是因为更新后的工具使用了不同于 OpenSSL 的函数来完成它的工作，我们没有挂钩这些函数:*

```
*$ nm -D ./customhashv2 | grep 'U '
                 U __errno_location
                 U EVP_DigestFinal
                 U EVP_DigestInit
                 U EVP_DigestUpdate
                 U EVP_MD_CTX_free
                 U EVP_MD_CTX_new
                 U EVP_sha1
                 U fclose
                 U feof
                 U fopen
                 U fread
                 U __libc_start_main
                 U perror
                 U printf
                 U puts*
```

*为了支持任意长度的文件，该工具现在使用一个接口，迭代地处理数据。但是我们需要更新我们的挂钩库:*

**cryptofixv2.c:**

```
***#define _GNU_SOURCE** */* for RTLD_NEXT */* **#include <dlfcn.h>
#include <stdio.h>
#include <string.h>** 
**#include <openssl/evp.h>
#include <openssl/sha.h>** 
**int** **EVP_DigestInit**(EVP_MD_CTX *****ctx, **const** EVP_MD *****type)
{
    **static** **int** (*****real_fn)(EVP_MD_CTX *****ctx, **const** EVP_MD *****type) **=** NULL;
    **if** (**!**real_fn)
    {
        real_fn **=** dlsym(RTLD_NEXT, "EVP_DigestInit");
        **if** (**!**real_fn)
        {
            fputs("cannot find EVP_DigestInit", stderr);
            exit(1);
        }
    }

    **if** (type **==** EVP_sha1())
        **return** real_fn(ctx, EVP_sha256());
    **else**
        **return** real_fn(ctx, type);
}

**int** **EVP_DigestFinal**(EVP_MD_CTX *****ctx, **unsigned** **char** *****md, **unsigned** **int** *****s)
{
    **static** **int** (*****real_fn)(EVP_MD_CTX *****ctx, **unsigned** **char** *****md, **unsigned** **int** *****s) **=** NULL;
    **if** (**!**real_fn)
    {
        real_fn **=** dlsym(RTLD_NEXT, "EVP_DigestFinal");
        **if** (**!**real_fn)
        {
            fputs("cannot find EVP_DigestFinal", stderr);
            exit(1);
        }
    }

    **if** (EVP_MD_CTX_md(ctx) **==** EVP_sha256())
    {
        **unsigned** **char** sha256_md[SHA256_DIGEST_LENGTH];
        **unsigned** **int** sha256_md_size, err;

        err **=** real_fn(ctx, sha256_md, **&**sha256_md_size);
        fputs("replacing SHA1 with SHA256\n", stderr);
        memcpy(md, sha256_md, SHA_DIGEST_LENGTH);
        *****s **=** SHA_DIGEST_LENGTH;
        **return** err;
    }
    **else**
        **return** real_fn(ctx, md, s);
}*
```

*现在我们挂钩两个函数:*

*   *和以前一样，在`EVP_DigestInit`中，我们检测调用者何时请求 SHA-1 计算，而不是从 OpenSSL 请求 SHA-256 计算*
*   *在`EVP_DigestFinal`中，我们将任何 SHA-256 计算的结果截断为 20 个字节，并将结果返回给调用者*

*为了简单起见，这个实现假设调用应用程序从不自己请求 SHA-256 散列计算。如果不是这样，挂钩库可能会变得更加复杂，因为我们必须以某种方式(例如，在集合中)跟踪我们在`EVP_DigestInit`中“修补”的 OpenSSL 上下文对象，所以我们只在`EVP_DigestFinal`中截断原始的 SHA-1 结果。*

*检查它是否工作:*

```
*$ gcc -shared -fPIC -o cryptofixv2.so cryptofixv2.c
$ echo abc | LD_PRELOAD=./cryptofixv2.so ./customhashv2
replacing SHA1 with SHA256
edeaaff3f1774ad2888673770c6d64097e391bc3
$ echo abc | sha256sum
edeaaff3f1774ad2888673770c6d64097e391bc362d7d6fb34982ddf0efd18cb  -*
```

*好了，我们没事了！我们涵盖了所有可能的情况吗？这是供应商提供的另一个潜在更新:*

**customhashv3.c:**

```
*...
**static** **int** **hash**(**FILE** *****f)
{
    **int** err, i;
    **unsigned** **char** md[SHA_DIGEST_LENGTH];
    **unsigned** **int** md_size **=** **sizeof**(md);

    **unsigned** **char** buf[256];
    **int** bytes_read;

    BIO *****filebio, *****sha1bio;

    filebio **=** BIO_new_fp(f, BIO_NOCLOSE);
    **if** (**!**filebio)
    {
        errno **=** ENOMEM;
        **return** errno;
    }

    sha1bio **=** BIO_new(BIO_f_md());
    **if** (**!**sha1bio)
    {
        BIO_free(filebio);
        errno **=** ENOMEM;
        **return** errno;
    }

    BIO_set_md(sha1bio, EVP_sha1());
    BIO_push(sha1bio, filebio);

    bytes_read **=** BIO_read(sha1bio, buf, **sizeof**(buf));
    **while** (bytes_read **>** 0)
    {
        bytes_read **=** BIO_read(sha1bio, buf, **sizeof**(buf));
    }

    **if** (bytes_read **<** 0)
    {
        BIO_free_all(sha1bio);
        errno **=** EIO;
        **return** errno;
    }

    **if** (BIO_gets(sha1bio, md, **sizeof**(md)) **<=** 0)
    {
        BIO_free_all(sha1bio);
        errno **=** EFAULT;
        **return** errno;
    }

    BIO_free_all(sha1bio);

    **for** (i **=** 0; i **<** md_size; i**++**)
        printf("%02x", md[i]);
    puts("");

    **return** 0;
}
...*
```

*它的工作原理和前面的一样:*

```
*$ gcc -o customhashv3 customhashv3.c -lcrypto
$ echo abc | ./customhashv3
03cfd743661f07975fa2f1220c5194cbaff48451*
```

*但是，我们可能已经猜到我们的挂钩库将不再工作，只需看看:*

```
*$ nm -D ./customhashv3 | grep 'U '
                 U BIO_ctrl
                 U BIO_f_md
                 U BIO_free
                 U BIO_free_all
                 U BIO_gets
                 U BIO_new
                 U BIO_new_fp
                 U BIO_push
                 U BIO_read
                 U __errno_location
                 U EVP_sha1
                 U fclose
                 U fopen
                 U __libc_start_main
                 U perror
                 U printf
                 U puts*
```

*调用应用程序使用另一组函数调用来计算 SHA-1 摘要，我们必须想出一个新的解决方案:*

**cryptofixv3.c:**

```
***#define _GNU_SOURCE** */* for RTLD_NEXT */* **#include <dlfcn.h>
#include <stdio.h>
#include <string.h>** 
**#include <openssl/evp.h>
#include <openssl/sha.h>** 
**long** **BIO_ctrl**(BIO *****bp, **int** cmd, **long** larg, **void** *****parg)
{
    **static** **long** (*****real_fn)(BIO *****bp, **int** cmd, **long** larg, **void** *****parg) **=** NULL;
    **if** (**!**real_fn)
    {
        real_fn **=** dlsym(RTLD_NEXT, "BIO_ctrl");
        **if** (**!**real_fn)
        {
            fputs("cannot find BIO_ctrl", stderr);
            exit(1);
        }
    }

    **if** (cmd **==** BIO_C_SET_MD **&&** parg **==** EVP_sha1())
        **return** real_fn(bp, cmd, larg, (**void** *****)EVP_sha256());
    **else**
        **return** real_fn(bp, cmd, larg, parg);
}

**int** **BIO_gets**(BIO *****bp, **char** *****buf, **int** size)
{
    EVP_MD *****md **=** NULL;
    **static** **int** (*****real_fn)(BIO *****bp, **char** *****buf, **int** size) **=** NULL;
    **if** (**!**real_fn)
    {
        real_fn **=** dlsym(RTLD_NEXT, "BIO_gets");
        **if** (**!**real_fn)
        {
            fputs("cannot find BIO_gets", stderr);
            exit(1);
        }
    }

    **if** (BIO_method_type(bp) **==** BIO_TYPE_MD **&&** BIO_get_md(bp, **&**md))
    {
        **if** (md **==** EVP_sha256()) {
            **char** sha256_md[SHA256_DIGEST_LENGTH];
            **int** err;

            **if** (size **<** SHA_DIGEST_LENGTH)
                **return** 0;

            err **=** real_fn(bp, sha256_md, **sizeof**(sha256_md));
            fputs("replacing SHA1 with SHA256\n", stderr);
            memcpy(buf, sha256_md, size);
            **return** err;
        }
    }

    **return** real_fn(bp, buf, size);
}*
```

*由读者来验证上面的代码是否有效，但值得注意的是，它受到与`v2`相同的限制和假设。*

*在这一点上，很明显 OpenSSL 有一个相当多样化的 API，同样的事情可以用许多不同的方式实现。这使得 OpenSSL 算法很难挂钩，因为几乎不可能考虑所有情况和组合。*

# *OpenSSL 引擎拯救世界*

*虽然我们不能为任何加密库提供可靠的算法替换，但如果应用程序使用 OpenSSL，我们可以用 [OpenSSL 引擎](https://github.com/openssl/openssl/blob/master/README.ENGINE)做得比上面更好。*

*OpenSSL 引擎是任何人都可以编写的第三方扩展，以提供任何加密算法的自定义实现。它们主要用于两种情况:*

*   *将不同的硬件加密设备集成到 OpenSSL 和基于 OpenSSL 的应用程序中*
*   *在 OpenSSL 中引入新的加密算法，并通过通用 OpenSSL `EVP_x` API 提供这些算法*

*但是我们会滥用这个框架:我们将编写一个 SHA-1 算法的“替代”实现，它将进行 SHA-256 计算(下面的代码基于 OpenSSL 博客中的[示例):](https://www.openssl.org/blog/blog/2015/11/23/engine-building-lesson-2-an-example-md5-engine/)*

**sha1-sha256.c:**

```
***#include <string.h>** 
**#include <openssl/engine.h>
#include <openssl/evp.h>
#include <openssl/sha.h>** 
**static** **const** **char** *****engine_id **=** "sha1-sha256";
**static** **const** **char** *****engine_name **=**
    "An engine, which converts SHA1 to SHA256 for better security";

**static** **int** **digest_init**(EVP_MD_CTX *****ctx) {
  **return** SHA256_Init(EVP_MD_CTX_md_data(ctx));
}

**static** **int** **digest_update**(EVP_MD_CTX *****ctx, **const** **void** *****data, **size_t** count) {
  **return** SHA256_Update(EVP_MD_CTX_md_data(ctx), data, count);
}

**static** **int** **digest_final**(EVP_MD_CTX *****ctx, **unsigned** **char** *****md) {
  **char** sha256_md[SHA256_DIGEST_LENGTH];
  **int** err;

  err **=** SHA256_Final(sha256_md, EVP_MD_CTX_md_data(ctx));
  fputs("replacing SHA1 with SHA256\n", stderr);
  memcpy(md, sha256_md, SHA_DIGEST_LENGTH);
  **return** err;
}

**static** EVP_MD *****digest_meth **=** NULL;
**static** **int** digest_nids[] **=** {NID_sha1, 0};
**static** **int** **digests**(ENGINE *****e, **const** EVP_MD ******digest, **const** **int** ******nids,
                   **int** nid) {
  **if** (**!**digest) {
    *****nids **=** digest_nids;
    **return** (**sizeof**(digest_nids) **-** 1) **/** **sizeof**(digest_nids[0]);
  }
  **switch** (nid) {
  **case** NID_sha1:
    **if** (digest_meth **==** NULL) {
      digest_meth **=** EVP_MD_meth_new(NID_sha1, NID_sha1WithRSAEncryption);
      **if** (**!**digest_meth) {
        **return** 0;
      }
      **if** (**!**EVP_MD_meth_set_result_size(digest_meth, SHA_DIGEST_LENGTH) **||**
          **!**EVP_MD_meth_set_flags(digest_meth, EVP_MD_FLAG_DIGALGID_ABSENT) **||**
          **!**EVP_MD_meth_set_init(digest_meth, digest_init) **||**
          **!**EVP_MD_meth_set_update(digest_meth, digest_update) **||**
          **!**EVP_MD_meth_set_final(digest_meth, digest_final) **||**
          **!**EVP_MD_meth_set_cleanup(digest_meth, NULL) **||**
          **!**EVP_MD_meth_set_ctrl(digest_meth, NULL) **||**
          **!**EVP_MD_meth_set_input_blocksize(digest_meth, SHA_CBLOCK) **||**
          **!**EVP_MD_meth_set_app_datasize(
              digest_meth, **sizeof**(EVP_MD *****) **+** **sizeof**(SHA256_CTX)) **||**
          **!**EVP_MD_meth_set_copy(digest_meth, NULL)) {

        **goto** err;
      }
    }
    *****digest **=** digest_meth;
    **return** 1;
  **default:**
    *****digest **=** NULL;
    **return** 0;
  }

**err:**
  **if** (digest_meth) {
    EVP_MD_meth_free(digest_meth);
    digest_meth **=** NULL;
  }
  **return** 0;
}

**static** **int** **engine_init**(ENGINE *****e) {
  **return** 1;
}

**static** **int** **engine_finish**(ENGINE *****e) {
  **if** (digest_meth) {
    EVP_MD_meth_free(digest_meth);
    digest_meth **=** NULL;
  }
  **return** 1;
}

**static** **int** **bind**(ENGINE *****e, **const** **char** *****id) {
  **if** (**!**ENGINE_set_id(e, engine_id)) {
    **goto** err;
  }
  **if** (**!**ENGINE_set_name(e, engine_name)) {
    **goto** err;
  }
  **if** (**!**ENGINE_set_init_function(e, engine_init)) {
    **goto** err;
  }
  **if** (**!**ENGINE_set_finish_function(e, engine_finish)) {
    **goto** err;
  }
  **if** (**!**ENGINE_set_digests(e, digests)) {
    **goto** err;
  }
  **return** 1;
**err:**
  **return** 0;
}

IMPLEMENT_DYNAMIC_BIND_FN(bind)
IMPLEMENT_DYNAMIC_CHECK_FN()*
```

*上面的引擎向 OpenSSL 声明自己是阿沙-1 实现，但是重用 OpenSSL 本身并计算 SHA-256。它还将输出截断为 20 个字节，以免混淆期待阿沙-1 结果的应用程序。让我们来测试一下:*

```
*$ gcc -shared -fPIC -o cryptofix_engine.so sha1-sha256.c
$ echo abc | openssl sha1
(stdin)= 03cfd743661f07975fa2f1220c5194cbaff48451
$ echo abc | openssl sha1 -engine ./cryptofix_engine.so
engine "sha1-sha256" set.
replacing SHA1 with SHA256
(stdin)= edeaaff3f1774ad2888673770c6d64097e391bc3
$ echo abc | sha256sum
edeaaff3f1774ad2888673770c6d64097e391bc362d7d6fb34982ddf0efd18cb  -*
```

*看起来像预期的那样工作。让我们用我们的专有工具来试试:*

```
*$ echo abc | LD_PRELOAD=./cryptofix_engine.so ./customhashv3
03cfd743661f07975fa2f1220c5194cbaff48451*
```

*嗯……什么都没有改变:我们没有得到我们的调试信息，结果仍然是阿沙-1。原因是:为了使引擎可用，我们还需要调用一些 OpenSSL API 来加载和配置它！因此，并不是所有基于 OpenSSL 的应用程序都是引擎感知的。显然，我们上面使用的命令行`openssl`实用程序是:当我们指定`-engine`参数时，引擎配置 API 被调用。还有其他的，比如 NGINX 和 OpenVPN——它们在配置文件中有一些指令，用户可以在其中指定所需的 OpenSSL 引擎。但大多数都不是——开发人员只是将 OpenSSL 作为一个加密库，并不期望用户替换加密算法。*

# *在进程启动时注入代码*

*如上所述，我们的定制工具不支持 OpenSSL 引擎，因此我们需要在它开始计算第一个 SHA-1 之前，让它调用 [OpenSSL 引擎配置 API](https://www.openssl.org/docs/man1.1.1/man3/ENGINE_ctrl_cmd_string.html) 。我们可能会挂钩一些其他函数，甚至是来自`libc`的函数，并希望它会在 OpenSSL 函数之前使用，但是我们会受到上述问题的影响，供应商的更新可能会破坏我们的修复程序。*

*更好的方法是在函数中实现所需的引擎配置，并将其标记为[“初始化例程”](https://gcc.gnu.org/onlinedocs/gccint/Initialization.html):*

**自动加载 c:**

```
***#define _GNU_SOURCE** */* for dladdr and Dl_info */* **#include <dlfcn.h>
#include <stdio.h>** 
**#include <openssl/engine.h>** 
**static** **void** **fatal**(**const** **char** *****msg) {
  fputs(msg, stderr);
  exit(1);
}

**static** **__attribute__**((constructor)) **void** engine_preload(**void**) {
  *// OpenSSL dynamic engine needs a filesystem path to the engine*
  *// so we determine our own filesystem path first*
  Dl_info dinfo;
  **int** res **=** dladdr((**const** **void** *****)engine_preload, **&**dinfo);
  **if** (0 **==** res) {
    fatal("failed to query engine module info");
  }
  **if** (NULL **==** dinfo.dli_fname) {
    fatal("failed to determine engine filesystem path");
  }
  ENGINE_load_dynamic();
  ENGINE *****e **=** ENGINE_by_id("dynamic");
  **if** (NULL **==** e) {
    fatal("failed to load OpenSSL dynamic engine");
  }

  res **=** ENGINE_ctrl_cmd_string(e, "SO_PATH", dinfo.dli_fname, 0);
  **if** (res **<=** 0) {
    fatal("failed to set SO_PATH parameter for dynamic engine");
  }
  res **=** ENGINE_ctrl_cmd_string(e, "ID", "sha1-sha256", 0);
  **if** (res **<=** 0) {
    fatal("failed to set ID parameter for dynamic engine");
  }
  res **=** ENGINE_ctrl_cmd_string(e, "LOAD", NULL, 0);
  **if** (res **<=** 0) {
    fatal("failed to LOAD sha1-sha256 engine");
  }
  res **=** ENGINE_set_default(e, ENGINE_METHOD_ALL);
  **if** (res **<=** 0) {
    fatal("failed to set algorithms from sha1-sha256 engine as default");
  }
}*
```

*OpenSSL 引擎配置 API 需要一个指向所需引擎的文件系统路径。我们假设上面的代码将是我们的`cryptofix_engine.so`库的一部分，所以我们只需要获取当前正在执行的模块的文件系统路径，并将其传递给 OpenSSL 引擎配置 API。但是这里的神奇之处在于函数声明:注意原型中的`__attribute__((constructor))`。它将该代码标记为“初始化例程”，因此它将在过程启动时自动执行，甚至在`main`功能之前执行。这种方法的美妙之处在于，我们不依赖于在目标应用程序中挂接任何函数。事实上，只要应用程序加载了我们的共享库，不管应用程序的逻辑是什么，这段代码都会被执行。*

*让我们重新编译包含这个函数的`cryptofix_engine.so`并测试它:*

```
*$ gcc -shared -fPIC -o cryptofix_engine.so autoload.c sha1-sha256.c
$ echo abc | LD_PRELOAD=./cryptofix_engine.so ./customhashv3
replacing SHA1 with SHA256
edeaaff3f1774ad2888673770c6d64097e391bc3
$ echo abc | sha256sum
edeaaff3f1774ad2888673770c6d64097e391bc362d7d6fb34982ddf0efd18cb  -*
```

*成功了！但是因为我们通过 OpenSSL 引擎替换了该算法，所以它也适用于该工具的每个先前版本，并且很可能适用于任何未来版本:*

```
*$ echo abc | LD_PRELOAD=./cryptofix_engine.so ./customhashv2
replacing SHA1 with SHA256
edeaaff3f1774ad2888673770c6d64097e391bc3
$ echo abc | LD_PRELOAD=./cryptofix_engine.so ./customhash
replacing SHA1 with SHA256
edeaaff3f1774ad2888673770c6d64097e391bc3*
```

*因此，我们的修补程序现在更加可靠，面向未来。*

# *正在删除 LD_PRELOAD*

*到目前为止，我们有一个可靠的修补程序，用于我们的弱专有哈希工具，但是我们需要确保我们的代码将总是通过指定`LD_PRELOAD`环境变量来加载，当工具被执行时。这不仅容易出错(当调用工具时，我们可能会忘记定义变量)，而且[并不是在所有情况下都有效](http://man7.org/linux/man-pages/man8/ld.so.8.html)(例如，当调用设置了`setuid/setgid`位的可执行文件时，环境变量被忽略)。*

*我们可以永久地修补定制工具而无需重新编译它，并添加我们的`cryptofix_engine.so`共享库作为运行时依赖项:*

```
*$ patchelf --add-needed ./cryptofix_engine.so ./customhashv3
$ ldd ./customhashv3
	linux-vdso.so.1 (0x00007ffd40977000)
	./cryptofix_engine.so (0x00007faf1d1ce000)
	libcrypto.so.1.1 => /lib/x86_64-linux-gnu/libcrypto.so.1.1 (0x00007faf1ced9000)
	libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007faf1cd18000)
	libdl.so.2 => /lib/x86_64-linux-gnu/libdl.so.2 (0x00007faf1cd13000)
	libpthread.so.0 => /lib/x86_64-linux-gnu/libpthread.so.0 (0x00007faf1ccf2000)
	/lib64/ld-linux-x86-64.so.2 (0x00007faf1d1db000)*
```

*从现在开始，我们的`cryptofix_engine.so`将成为`customhashv3`工具的一部分，并且在执行二进制文件时，即使没有任何`LD_PRELOAD`定义，也会一直被加载:*

```
*$ echo abc | ./customhashv3
replacing SHA1 with SHA256
edeaaff3f1774ad2888673770c6d64097e391bc3
$ echo abc | sha256sum
edeaaff3f1774ad2888673770c6d64097e391bc362d7d6fb34982ddf0efd18cb  -*
```

# *结论*

*这篇文章虽然基于想象的场景，但反映了一些真实世界的使用案例和经验。它还涵盖了一些强大的运行时代码修补方法，即使不需要替换私有代码中的弱加密也很有用，可以单独或一起采用。帖子中的所有代码都是在这里发布的[。](https://github.com/pqsec/cryptofix)*