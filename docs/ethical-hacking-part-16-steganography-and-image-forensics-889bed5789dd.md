# 隐写术和图像取证

> 原文：<https://levelup.gitconnected.com/ethical-hacking-part-16-steganography-and-image-forensics-889bed5789dd>

## 数字取证和隐藏图像信息的迷人世界！

![](img/2563ed94a1295e30b2e62715b9a242fd.png)

隐写术是将消息或信息隐藏在其他非机密文本或数据中的做法。

实际上，您会惊讶于一个图像或视频文件可以存储多少元数据。在取证调查期间，媒体文件的元数据通常可以揭示很多信息。例如，您可能会在社交媒体上分享一张在家中拍摄的照片，但您的相机在元数据中包含关于您和您的位置的个人信息，这是最好的情况。

在更险恶的情况下，有害的有效载荷可能被包括在内。虽然图像查看器或 web 服务器不可能自动运行恶意代码，但这是黑客或僵尸网络在系统上预加载有效负载而不被检测到的一种方式。然后，他们需要做的就是访问系统并执行来自无缝无害媒体文件的有效载荷。

如果您的企业处理上传的媒体文件，那么在上传时清除文件的元数据是值得的，除非有任何字段是绝对必要的。我将在后面介绍如何做到这一点。

## 如何查看媒体文件的元数据？

大多数操作系统允许您检查媒体文件的属性。然而，它们可能只显示几个选定的字段，但文件中可能包含更多的字段。Linux 和 macOS 系统有一个非常方便的工具，叫做“ **file** ”，用来查看最常见的元数据字段。

```
% **file someimage.jpg** 
someimage.jpg: JPEG image data, JFIF standard 1.01, aspect ratio, density 1x1, segment length 16, progressive, precision 8, 1920x1280, components 3
```

有一个非常强大的工具叫做 [ExifTool](https://exiftool.org/) ，适用于 Windows、macOS 和 Linux。对于 Linux 和 macOS，您可以使用 Brew、Aptitude 或 Yum 等常用的软件包安装程序来安装它。如果你去 [ExifTool](https://exiftool.org/) 官方网站，你可以看到支持读写的媒体格式的广泛列表。

```
% **exiftool someimage.jpg** 
ExifTool Version Number         : 12.00
File Name                       : someimage.jpg
Directory                       : .
File Size                       : 66 kB
File Modification Date/Time     : 2020:11:09 13:05:58+00:00
File Access Date/Time           : 2020:11:09 13:14:53+00:00
File Inode Change Date/Time     : 2020:11:09 13:14:52+00:00
File Permissions                : rw-r--r--
File Type                       : JPEG
File Type Extension             : jpg
MIME Type                       : image/jpeg
JFIF Version                    : 1.01
Resolution Unit                 : None
X Resolution                    : 1
Y Resolution                    : 1
Image Width                     : 1920
Image Height                    : 1280
Encoding Process                : Progressive DCT, Huffman coding
Bits Per Sample                 : 8
Color Components                : 3
Y Cb Cr Sub Sampling            : YCbCr4:2:0 (2 2)
Image Size                      : 1920x1280
Megapixels                      : 2.5
```

## 如何将有效负载包含到媒体文件中？

第一步是去 [ExifTool](https://exiftool.org/) 官方网站，检查你想要使用的媒体格式是否支持书写。为了这个教程，我去了 [Pixabay](https://pixabay.com/) 下载了一个免版税的 JPEG 图片来玩。

```
% **exiftool -Comment='<?php system("touch newfile1"); ?>' someimage.jpg 
    1 image files updated**
```

您会注意到 ExifTool 会为您创建一个原始文件的副本。

```
% **ls someimage.jpg***
someimage.jpg  **someimage.jpg_original**
```

你可以看到有效载荷是这样的…

```
% **file someimage.jpg**
someimage.jpg: JPEG image data, JFIF standard 1.01, aspect ratio, density 1x1, segment length 16, comment: "**<?php system("touch newfile1"); ?>**", progressive, precision 8, 1920x1280, components 3% **exiftool someimage.jpg**
ExifTool Version Number         : 12.00
File Name                       : someimage.jpg
Directory                       : .
File Size                       : 66 kB
File Modification Date/Time     : 2020:11:09 13:39:55+00:00
File Access Date/Time           : 2020:11:09 13:39:58+00:00
File Inode Change Date/Time     : 2020:11:09 13:39:57+00:00
File Permissions                : rw-r--r--
File Type                       : JPEG
File Type Extension             : jpg
MIME Type                       : image/jpeg
JFIF Version                    : 1.01
Resolution Unit                 : None
X Resolution                    : 1
Y Resolution                    : 1
Comment                         : **<?php system("touch newfile1"); ?>**
Image Width                     : 1920
Image Height                    : 1280
Encoding Process                : Progressive DCT, Huffman coding
Bits Per Sample                 : 8
Color Components                : 3
Y Cb Cr Sub Sampling            : YCbCr4:2:0 (2 2)
Image Size                      : 1920x1280
Megapixels                      : 2.5
```

也许我们也可以这样做…

```
% **mv someimage.jpg_original someimage.jpg    ** % **exiftool -Comment="touch newfile2" someimage.jpg** 
    1 image files updated
```

并这样看待它…

```
% **file someimage.jpg**
someimage.jpg: JPEG image data, JFIF standard 1.01, aspect ratio, density 1x1, segment length 16, comment: "**touch newfile2**", progressive, precision 8, 1920x1280, components 3% **exiftool someimage.jpg**
ExifTool Version Number         : 12.00
File Name                       : someimage.jpg
Directory                       : .
File Size                       : 66 kB
File Modification Date/Time     : 2020:11:09 13:47:18+00:00
File Access Date/Time           : 2020:11:09 13:47:21+00:00
File Inode Change Date/Time     : 2020:11:09 13:47:20+00:00
File Permissions                : rw-r--r--
File Type                       : JPEG
File Type Extension             : jpg
MIME Type                       : image/jpeg
JFIF Version                    : 1.01
Resolution Unit                 : None
X Resolution                    : 1
Y Resolution                    : 1
Comment                         : **touch newfile2**
Image Width                     : 1920
Image Height                    : 1280
Encoding Process                : Progressive DCT, Huffman coding
Bits Per Sample                 : 8
Color Components                : 3
Y Cb Cr Sub Sampling            : YCbCr4:2:0 (2 2)
Image Size                      : 1920x1280
Megapixels                      : 2.5
```

我们可以这样运行它…

```
% **PAYLOAD="$(exiftool -Comment someimage.jpg | sed 's/Comment.*:.//g')"**% **echo $PAYLOAD**                                   
touch newfile2% **eval $PAYLOAD**% **ls newfile2** 
newfile2**% unset PAYLOAD**
```

正如你所看到的，我已经预加载了一些 bash 代码，当执行这些代码时，将创建一个名为“ **newfile2** ”的新文件。然而，这需要在目标系统上安装“ **exiftool** ”，但实际情况可能并非如此。

在我上面的例子中，我在 JPEG 文件中使用了“ **Comment** ”元数据字段，如果有人检查该文件以查看是否嵌入了可疑代码，这将是非常明显的。

我们可以更有创意，也许 base64 编码的有效载荷，并将其隐藏在另一个领域，如“**证书**”。

让我们试试另一种方式…

```
% **mv someimage.jpg_original someimage.jpg**% **exiftool -Certificate="$(echo 'touch newfile3' | base64)" someimage.jpg**                   
    1 image files updated
```

我在这里做了什么？我对字符串“**触摸新文件 3** ”进行了 base64 编码，并将其嵌入到“**证书**”元数据字段中。

```
% **file someimage.jpg**
someimage.jpg: JPEG image data, JFIF standard 1.01, aspect ratio, density 1x1, segment length 16, progressive, precision 8, 1920x1280, components 3% **exiftool someimage.jpg**
ExifTool Version Number         : 12.00
File Name                       : someimage.jpg
Directory                       : .
File Size                       : 68 kB
File Modification Date/Time     : 2020:11:09 14:19:56+00:00
File Access Date/Time           : 2020:11:09 14:19:59+00:00
File Inode Change Date/Time     : 2020:11:09 14:19:58+00:00
File Permissions                : rw-r--r--
File Type                       : JPEG
File Type Extension             : jpg
MIME Type                       : image/jpeg
JFIF Version                    : 1.01
Resolution Unit                 : None
X Resolution                    : 1
Y Resolution                    : 1
XMP Toolkit                     : Image::ExifTool 12.00
Certificate                     : **dG91Y2ggbmV3ZmlsZTMK**
Image Width                     : 1920
Image Height                    : 1280
Encoding Process                : Progressive DCT, Huffman coding
Bits Per Sample                 : 8
Color Components                : 3
Y Cb Cr Sub Sampling            : YCbCr4:2:0 (2 2)
Image Size                      : 1920x1280
Megapixels                      : 2.5
```

您现在会注意到，“**文件**”没有显示“**证书**”字段。您还会注意到，我们的 base64 编码的有效载荷现在位于“ **Certificate** ”字段中，实际上看起来是无害的。

让我们运行与上面相同的过程…

```
% **PAYLOAD="$(exiftool -Certificate someimage.jpg | sed 's/Certificate.*:.//g' | base64 -d)"**% **echo $PAYLOAD**                                   
touch newfile3% **eval $PAYLOAD**% **ls newfile2** 
newfile3**% unset PAYLOAD**
```

如果不使用 ExifTool，这怎么能执行呢？

让我们在一个新的终端窗口中使用 Python **快速启动一个 HTTP 服务器。**

```
# If you are using Python 2% **python2 -m SimpleHTTPServer 8000** 
Serving HTTP on 0.0.0.0 port 8000 ...# If you are using Python 3% **python3 -m http.server 8000**
Serving HTTP on :: port 8000 ([http://[::]:8000/](http://[::]:8000/)) ...
```

回到我们最初的终端窗口，运行这个…

```
% **curl** [**http://localhost:8000/someimage.jpg**](http://localhost:8000/someimage.jpg) 
Warning: Binary output can mess up your terminal. Use "--output -" to tell 
Warning: curl to output it to your terminal anyway, or consider "--output 
Warning: <FILE>" to save to a file.
```

我们可以这样做来保存文件。

```
% **curl** [**http://localhost:8000/someimage.jpg**](http://localhost:8000/someimage.jpg) **--output newimage.jpg**
```

并检查它是否仍然是正确的图像，它是。

```
% **exiftool newimage.jpg**                    
ExifTool Version Number         : 12.00
File Name                       : newimage.jpg
Directory                       : .
File Size                       : 68 kB
File Modification Date/Time     : 2020:11:09 14:29:48+00:00
File Access Date/Time           : 2020:11:09 14:29:50+00:00
File Inode Change Date/Time     : 2020:11:09 14:29:48+00:00
File Permissions                : rw-r--r--
File Type                       : JPEG
File Type Extension             : jpg
MIME Type                       : image/jpeg
JFIF Version                    : 1.01
Resolution Unit                 : None
X Resolution                    : 1
Y Resolution                    : 1
XMP Toolkit                     : Image::ExifTool 12.00
Certificate                     : **dG91Y2ggbmV3ZmlsZTMK**
Image Width                     : 1920
Image Height                    : 1280
Encoding Process                : Progressive DCT, Huffman coding
Bits Per Sample                 : 8
Color Components                : 3
Y Cb Cr Sub Sampling            : YCbCr4:2:0 (2 2)
Image Size                      : 1920x1280
Megapixels                      : 2.5
```

让我们看看是否可以从二进制文件中提取“**证书**”有效负载…

```
% **PAYLOAD=$(curl -s** [**http://localhost:8000/someimage.jpg**](http://localhost:8000/someimage.jpg) **| grep Certificate -a | sed 's/<[^>]*>//g' | base64 -d)**% **echo $PAYLOAD** 
touch newfile3% **eval $PAYLOAD**% **ls newfile3** 
newfile3**% unset PAYLOAD**
```

黑客可以进一步隐藏其攻击负载的另一种方式是在不同的映像中包含部分代码，并在目标系统上重新构建它们。

这可能看起来很可怕，但幸运的是，作为一名安全架构师，通过适当地清除媒体文件上的元数据，这是相当容易防止的。

## 如何从媒体文件中删除元数据？

ExifTool 允许您通过一个简单的命令来实现这一点…

```
% **exiftool -all= someimage.jpg**
```

“**元数据匿名化工具包 2** ”或“ **MAT2** ”在这方面也是一个非常有用的工具。它可以使用 Aptitude 或 Yum 安装在 Linux 系统上。

```
kali@kali:~$ **sudo apt-get install mat -y**
Reading package lists... Done
Building dependency tree       
Reading state information... Done
mat is already the newest version (0.11.0-1).kali@kali:~$ **mat2 --help**
usage: mat2 [-h] [-V] [--unknown-members policy] [--inplace]
            [--no-sandbox] [-v] [-l] [--check-dependencies] [-L | -s]
            [files [files ...]]Metadata anonymisation toolkit 2positional arguments:
  files                 the files to processoptional arguments:
  -h, --help            show this help message and exit
  -V, --verbose         show more verbose status information
  --unknown-members policy
                        how to handle unknown members of archive-style
                        files (policy should be one of: abort, omit,
                        keep) [Default: abort]
  --inplace             clean in place, without backup
  --no-sandbox          Disable bubblewrap's sandboxing
  -v, --version         show program's version number and exit
  -l, --list            list all supported fileformats
  --check-dependencies  check if mat2 has all the dependencies it needs
  -L, --lightweight     remove SOME metadata
  -s, --show            list harmful metadata detectable by mat2 without
                        removing them
```

我希望你觉得这篇文章有趣并且有用。如果您想随时了解情况，请不要忘记关注我，注册我的[电子邮件通知](https://whittle.medium.com/subscribe)。

# 迈克尔·惠特尔

*   ***如果你喜欢这个，请*** [***跟我上媒***](https://whittle.medium.com/)
*   ***更多有趣的文章，请*** [***关注我的刊物***](https://medium.com/trading-data-analysis)
*   ***有兴趣合作吗？*** [***我们来连线 LinkedIn 上的***](https://www.linkedin.com/in/miwhittle/)
*   ***支持我和其他媒体作者*** [***在此报名***](https://whittle.medium.com/membership)
*   ***请别忘了为文章鼓掌:)←谢谢！***