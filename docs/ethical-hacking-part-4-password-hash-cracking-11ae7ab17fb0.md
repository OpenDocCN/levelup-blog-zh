# 密码和哈希破解

> 原文：<https://levelup.gitconnected.com/ethical-hacking-part-4-password-hash-cracking-11ae7ab17fb0>

## 道德黑客，了解风险，防止攻击

![](img/018a413aa5d0b6d1bf779edfe38ba551.png)

因此，从这篇文章和我的其他道德黑客文章中，最大的收获是认真对待你的网络安全。

所有这些应用程序都有严格的密码策略，这看起来可能很乏味，但它们存在的原因你很快就会明白。

给用户一些关于密码的建议:

*   您的密码应该至少有 8 个字符，最好不要正好是 8 个字符。
*   您的密码应该由大小写字母、数字和特殊字符组成。
*   你真的应该在敏感网站上使用唯一的密码，比如网上银行、政府、社交媒体、系统等。
*   您应该尽可能频繁地尝试轮换您的密码。每 6 个月输入一个合理的密码。
*   如果站点使用多因素身份验证(MFA ),请使用它！
*   如果一个网站允许你登录谷歌，脸书，Github 等。使用 OAuth2，使用它！它可能看起来不安全，但它们比你登录的网站更有可能保护你的个人数据安全。
*   报名参加“[我被典当了吗](https://haveibeenpwned.com/)”。它会通知你，如果任何网站已被黑客攻击，你的凭据被盗。

关于用户数据，特别是密码，给开发人员的一些建议:

*   不要在服务器上本地存储密码和用户数据
*   不要将密码和敏感数据存储在代码库中
*   用 MD5 [散列密码并不能保护用户密码！](https://haveibeenpwned.com/)
*   虽然 SHA 的哈希稍微好一点，但是你仍然不能保证用户密码的安全[！](https://haveibeenpwned.com/)
*   对密码进行哈希处理比加密更好。加密是双向函数，而哈希是单向函数。加密要求您存储一个密钥，如果有人获取了该密钥，这将是一个问题。OWASP 的“ [**密码存储备忘单**](https://cheatsheetseries.owasp.org/cheatsheets/Password_Storage_Cheat_Sheet.html) ”是一个非常好的资源，可以确保您以正确的方式处理密码。
*   如果可能，使用 OpenID Connect 或 OAuth2，例如从 Active Directory 进行单点登录(SSO)或谷歌/脸书登录。至少这样你可以提供多因素认证(MFA ),他们更有可能保证用户数据的安全。
*   不允许无限制次数的错误密码尝试。理想情况下，扼制企图，甚至更好地暂时禁止源 IP。如果发生这种情况，您肯定会触发安全事件。

只是给你一些关于密码的序言…

*   破解一个已知的普通密码序列不到一毫秒。这甚至不一定是字典里的词。它可以是用户常用的任何序列。例如，即使“zxcvbnm”估计也需要 0.29 毫秒。“rockyou.txt”黑客密码列表有 14344392 个常用密码，所以要有创意！
*   少于 8 个字符的密码是没有意义的，所以不要费心了。假设密码由 8 个完全随机的小写字符组成，黑客需要 5 个多小时才能破解。
*   一个由 8 个完全随机的大小写字母组成的密码需要黑客花 1 个多月零 3 个星期才能破解。
*   由 8 个完全随机的大写、小写和数字字符组成的密码需要 7 个月零 1 周才能破解。
*   由 8 个完全随机的大写、小写和特殊字符组成的密码需要 14 年才能破解。
*   一个由 8 个完全随机的大写、小写、数字和特殊字符组成的密码需要 14 年才能破解。
*   超过 8 个字符后，每增加一个字符，它就会呈指数增长。

这是基于今天的计算能力，也很大程度上取决于你所拥有的硬件。如果你有一个服务器农场在处理密码，或者在未来量子计算中，密码真的会变得无关紧要。

这里的底线是…

*   不要使用可预测的密码！
*   **拥有相当强的 8 个字符的密码，每 6 个月更换一次，这意味着任何试图破解它的人都必须重新开始。**
*   **不要在多个地方使用同一个密码，好像有人被破解了一样。黑客会从你珍贵的社交媒体开始，在多个地方自动尝试同一个密码。**

我将讨论 Kali Linux 中的密码和哈希破解工具。如果你对此不熟悉，我推荐你阅读我的另一篇文章，“道德黑客(第 2 部分):Kali Linux 简介”。你总是可以在你的 Linux 系统或者 Mac 上安装单独的工具，但是这是没有意义的，因为 Kali 已经捆绑了所有的工具。

**CeWL**

尝试破解密码是好事，但用什么用户名呢？WordPress 黑客工具(“ **wpscan** ”)有一个很好的功能来列举用户名，所以你会有一个列表来立即工作，但是如果你没有这个呢？这就是“ **cewl** ”的用武之地。你把它指向一个网站，它就会扫描网页，寻找潜在的用户名，并把它们转储到一个文件中，然后传给一个黑客。

```
kali@kali:~$ **cewl -w usernames.txt -d1 -m4** [**http://192.168.1.2**](http://192.168.1.2)
CeWL 5.4.8 (Inclusion) Robin Wood (robin@digi.ninja) ([https://digi.ninja/](https://digi.ninja/))kali@kali:~$ **wc -l usernames.txt** 
198 usernames.txt
```

*   CeWL 将对网站进行爬网的深度。1 表示它将停留在这个确切的站点上，并且不打开任何链接。
*   m4 —将放入列表中的单词的最小长度。

例如，如果您想要将这个新用户名列表传递到" **wpscan** "中，您可以这样做…

```
kali@kali:~$ **wpscan --usernames usernames.txt**
```

**嘎吱声**

" **crunch** "是一个为暴力破解生成"**单词表**的便捷工具。

```
kali@kali:~$ **crunch 6 6 0123456789 -o numbers.txt**
Crunch will now generate the following amount of data: 7000000 bytes
6 MB
0 GB
0 TB
0 PB
Crunch will now generate the following number of lines: 1000000crunch: 100% completed generating output
```

在上面的例子中，我说我想要最少和最多的 **6** 个字符，并且我只想要它们在范围“ **0123456789** ”内。这里的输出文件将被称为“ **numbers.txt** ”，并将包含一个 100 万个数字的列表。

创建自己的"**单词表**"可能有点多余，因为 Kali 捆绑了许多单词表，包括著名的" **rockyou.txt** "和 **14344392** 常用密码。

```
kali@kali:/usr/share/wordlists$ tree
.
├── dirb -> /usr/share/dirb/wordlists
├── dirbuster -> /usr/share/dirbuster/wordlists
├── dnsmap.txt -> /usr/share/dnsmap/wordlist_TLAs.txt
├── fasttrack.txt -> /usr/share/set/src/fasttrack/wordlist.txt
├── fern-wifi -> /usr/share/fern-wifi-cracker/extras/wordlists
├── metasploit -> /usr/share/metasploit-framework/data/wordlists
├── nmap.lst -> /usr/share/nmap/nselib/data/passwords.lst
├── **rockyou.txt**
├── seclists -> /usr/share/seclists
└── wfuzz -> /usr/share/wfuzz/wordlist6 directories, 4 files
```

请注意，在你的卡利系统中，你可能会看到“**rockyou.txt.gz**”。这意味着文件被压缩。如果你想提取它，运行“**gunzip rockyou.txt.gz**”。

**九头蛇**

“**九头蛇**”是一个极其强大的密码破解工具。我不打算详细介绍这个工具，但是我会给你一些有用的提示和使用它的例子。

示例 1:

我的测试 Wordpress 站点运行在 192.168.1.2 上。我在这里做的是让“ **hydra** ”运行“ **usernames.txt** ”列表，查看系统是否包含任何用户名。如你所见，我提供了“ **na** ”作为密码，因为它实际上是不相关的。如果请求的响应是“**无效用户名**”，它会告诉 hydra 这是一次失败的尝试。

```
kali@kali:~$ **hydra -V -L usernames.txt -p na 192.168.1.2 http-post-form '/wp-login.php:log=^USER^&pwd=^PASS^&wp-submit=Log:F=Invalid username'**
```

示例 2:

一旦在“ **usernames.txt** ”中确定了用户名列表，下一步就是尝试暴力破解密码。

```
kali@kali:~$ **hydra -V -L usernames.txt -x 1:3:A1 192.168.1.2 http-post-form '/wp-login.php:log=^USER^&pwd=^PASS^&wp-submit=Log:F=Invalid username'**
```

“ **-x** ”和可选的“ **-y** ”参数决定了密码标准。上面我说的是一个由大写字母和数字组成的 1 到 3 个字符的密码。

```
Hydra bruteforce password generation option usage:-x MIN:MAX:CHARSETMIN     is the minimum number of characters in the password
     MAX     is the maximum number of characters in the password
     CHARSET is a specification of the characters to use in the generation
             valid CHARSET values are: 'a' for lowercase letters,
             'A' for uppercase letters, '1' for numbers, and for all others,
             just add their real representation.
  -y         disable the use of the above letters as placeholdersExamples:
   -x 3:5:a  generate passwords from length 3 to 5 with all lowercase letters
   -x 5:8:A1 generate passwords from length 5 to 8 with uppercase and numbers
   -x 1:3:/  generate passwords from length 1 to 3 containing only slashes
   -x 5:5:/%,.-  generate passwords with length 5 which consists only of /%,.-
   -x 3:5:aA1 -y generate passwords from length 3 to 5 with a, A and 1 only
```

出于兴趣，如果你想锁定一个特定的用户名，你可以使用“ **-l <用户名>** ”而不是“ **-L 用户名. txt** ”。

开发人员似乎很有幽默感。我试图生成一个由大写字母、数字、星号和胡萝卜组成的 18 个字符的密码，我得到了这个…

```
kali@kali:~$ **hydra -V -l admin -x 18:18:A1*^ 192.168.1.2 http-post-form '/wp-login.php:log=^USER^&pwd=^PASS^&wp-submit=Log:F=Invalid username'****[ERROR] definition for password bruteforce (-x) generates more than 4 billion passwords - this is not a bug in the program, it is just not feasible to try so many attempts. Try a calculator how long that would take. duh.**
```

示例 3:

**九头蛇**可以攻击**很多**服务。

Hydra 目前支持:

**adam6500，afp，asterisk，cisco，cisco-enable，cvs，firebird，ftp，ftps，http[s]-{ head | get | post } http[s]-{ get | post }-form，http-proxy，http-
proxy-urlenum，icq，imap[s]，irc，ldap2[s]，ldap3[-{cram|digest}md5][s]，mssql，mysql(v4)，mysql**

```
kali@kali:~$ **hydra -l <username> -P <password_file> telnet://targetname**kali@kali:~$ **hydra -t 4 -V -f -l administrator -P RockYou.txt rdp://targetname**kali@kali:~$ **hydra -t 5 -V -f -L userlist -P passwordlist ftp://targetname**
```

如果您试图破解的系统没有 Cisco devices 那样的用户名，并且没有启用 AAA，您可以使用“ **ikettle** ”。这是一个特殊的关键字，仅用于发送密码。

```
kali@kali:~$ **hydra -P <password_file> cisco://ikettle**
```

**哈希卡特**

正如我前面提到的，MD5 不适合存储用户密码。它只比使用纯文本存储密码好一点点。我见过许多 web 服务和开发人员将用户密码散列并存储在数据库中。然后，当用户登录时，他们散列密码并比较散列。如果哈希匹配，密码匹配，用户就登录了。这在理论上听起来很好，但当你仔细研究 MD5 时就不是这样了。虽然你不能“解开”一个密码，但你仍然可以通过尝试组合和比较散列直到它们匹配来找出散列值！

```
kali@kali:~$ **echo -n "hash" | md5sum | tr -d " -"**
**0800fc577294c34e0b28ad2839435945**kali@kali:~$ **echo -n "hash" | md5sum | tr -d " -"**
**0800fc577294c34e0b28ad2839435945**kali@kali:~$ **echo -n "hash" | md5sum | tr -d " -"**
**0800fc577294c34e0b28ad2839435945**
```

对于“**哈希**，哈希总是相同的。这意味着，如果我使用一个密码破解程序，它将在几毫秒内找到“**哈希**”，所有需要做的就是对其进行哈希处理，并比较哈希以确认密码确实是“**哈希**”。

一种选择是遍历一个大的"**单词表**"，比如" **rockyou.txt** "，并生成它的散列版本。

```
kali@kali:~$ **for i in $(cat rockyoutop10.txt); do echo -n "$i"| md5sum | tr -d " -" >> hashes; done**
```

我从“ **rockyou.txt** ”中取了前 10 个密码，创建了“ **rockyoutop10.txt** ”。

之前:

```
kali@kali:/usr/share/wordlists# **cat rockyoutop10.txt** 
123456
12345
123456789
password
iloveyou
princess
1234567
rockyou
12345678
abc123
```

之后:

```
root@kali:/usr/share/wordlists# **cat rockyouhashes.txt** 
e10adc3949ba59abbe56e057f20f883e
827ccb0eea8a706c4c34a16891f84e7b
25f9e794323b453885f5181f1b624d0b
5f4dcc3b5aa765d61d8327deb882cf99
f25a2fc72690b780b2a14e140ef6a9e0
8afa847f50a716e64932d995c8e7435a
fcea920f7412b5da7be0cf42b8c93759
f806fc5a2a0d5ba2471600758452799c
25d55ad283aa400af464c76d713c07ad
e99a18c428cb38d5f260853678922e03
```

现在，如果我的密码位于“ **rockyou.txt** 的前 10 位，我存储的哈希将与“**rock you ashes . txt**相同。

```
kali@kali:/usr/share/wordlists# **hashcat -m 0 rockyouhashes.txt rockyoutop10.txt**
```

输出和处理太大，无法在此包含，但这将从"**rockyoutp 10 . txt**"的普通密码列表中破解"**rockyou ashes . txt**"中的所有散列。

这个工具非常适合破解散列密码，但是稍后讨论的“**开膛手约翰**”可能更好。

我只是想简要说明为什么开发人员应该使用 AES-256 位加密。

```
kali@kali:~$ **echo "plaintext" > plaintext.txt**kali@kali:~$ **cat plaintext.txt**
plaintextkali@kali:~$ **openssl aes-256-cbc -a -salt -pbkdf2 -in plaintext.txt -out ciphertext.txt**
enter aes-256-cbc encryption password:
Verifying - enter aes-256-cbc encryption password:kali@kali:~$ **cat ciphertext.txt** 
U2FsdGVkX1+zve57VHOFPJByuoGiBW34zsaMs2t69NY=kali@kali:~$ **openssl aes-256-cbc -d -a -pbkdf2 -in ciphertext.txt -out plaintextnew.txt**
enter aes-256-cbc decryption password:kali@kali:~$ **cat plaintextnew.txt** 
plaintext
```

我们再次加密“明文”。

```
kali@kali:~$ **openssl aes-256-cbc -a -salt -pbkdf2 -in plaintext.txt -out ciphertext.txt**
enter aes-256-cbc encryption password:
Verifying - enter aes-256-cbc encryption password:kali@kali:~$ **cat ciphertext.txt** 
U2FsdGVkX19l7vWZ1Hq9shl1lRll3py9ZQfrq/23BbI=kali@kali:~$ **openssl aes-256-cbc -d -a -pbkdf2 -in ciphertext.txt -out plaintextnew.txt**
enter aes-256-cbc decryption password:kali@kali:~$ **cat plaintextnew.txt** 
plaintext
```

请注意文本“**明文**”的密码是不一样的…**u 2 fsdgvkx 1+zve 57 vhofpjbuogibw 34 zsams 2t 69 ny =**”和“**u 2 fsdgvkx 19 l 7 vwz 1 HQ 9 sh 11 RLL 3 py 9 zqfrq/23 bbi =**”。

你不能暴力解决这个问题。

**开膛手约翰**

开膛手约翰是一个免费的密码破解软件工具。最初是为 Unix 操作系统开发的，现在可以在 15 种不同的平台上运行。

它可以用来破解 Linux 密码。Linux 用户密码保存在“ **/etc/shadow** 文件中。

如果你有超级用户权限并且能够访问“ **/etc/shadow** ”文件，你可以运行这个…

```
root@kali:~# **john /etc/shadow**
Using default input encoding: UTF-8
Loaded 1 password hash (sha512crypt, crypt(3) $6$ [SHA512 256/256 AVX2 4x])
Cost 1 (iteration count) is 5000 for all loaded hashes
Proceeding with single, rules:Single
Press 'q' or Ctrl-C to abort, almost any other key for status
Warning: Only 7 candidates buffered for the current salt, minimum 8 needed for performance.
Warning: Only 4 candidates buffered for the current salt, minimum 8 needed for performance.
Warning: Only 2 candidates buffered for the current salt, minimum 8 needed for performance.
Warning: Only 7 candidates buffered for the current salt, minimum 8 needed for performance.
Warning: Only 4 candidates buffered for the current salt, minimum 8 needed for performance.
Almost done: Processing the remaining buffered candidate passwords, if any.
Warning: Only 1 candidate buffered for the current salt, minimum 8 needed for performance.
Proceeding with wordlist:/usr/share/john/password.lst, rules:Wordlist
0g 0:00:00:04 4.47% 2/3 (ETA: 10:02:32) 0g/s 1886p/s 1886c/s 1886C/s Ruthless..Unique
Session aborted
```

这可能需要一段时间，具体取决于您的硬件。

约翰可以在几毫秒内破解散列…

```
kali@kali:~$ **echo -n "Password1" | md5sum | tr -d " -" > md5hash.txt**kali@kali:~$ **sudo john --format=Raw-MD5 md5hash.txt** 
Using default input encoding: UTF-8
Loaded 1 password hash (Raw-MD5 [MD5 256/256 AVX2 8x3])
Proceeding with single, rules:Single
Press 'q' or Ctrl-C to abort, almost any other key for status
Almost done: Processing the remaining buffered candidate passwords, if any.
Proceeding with wordlist:/usr/share/john/password.lst, rules:Wordlist
**Password1**        (?)
1g **0:00:00:00** DONE 2/3 (2020-10-03 10:49) 100.0g/s 384000p/s 384000c/s 384000C/s !@#$%..Skippy
Use the "--show --format=Raw-MD5" options to display all of the cracked passwords reliably
Session completedkali@kali:~$ **sudo john --format=Raw-MD5 md5hash.txt --show**
?:**Password1**1 password hash cracked, 0 left
```

如果你想覆盖一个 ZIP 或 RAR 档案的密码，你可以这样做…

```
root@kali:/home/kali# **echo "example" > zip2john.txt**root@kali:/home/kali# **zip -P pass zip2john.zip zip2john.txt** 
  adding: zip2john.txt (stored 0%)root@kali:/home/kali# **unzip zip2john.zip** 
Archive:  zip2john.zip
[zip2john.zip] zip2john.txt password: 
replace zip2john.txt? [y]es, [n]o, [A]ll, [N]one, [r]ename: y
 extracting: zip2john.txtroot@kali:/home/kali# **zip2john zip2john.zip > zip.hashes**
ver 1.0 efh 5455 efh 7875 zip2john.zip/zip2john.txt PKZIP Encr: 2b chk, TS_chk, cmplen=20, decmplen=8, crc=520964DDroot@kali:/home/kali# **cat zip.hashes** 
zip2john.zip/zip2john.txt:$pkzip2$1*2*2*0*14*8*520964dd*0*46*0*14*5209*51b1*ca8bdb7527a441fd8253e355f715b5b2bd09bd8e*$/pkzip2$:zip2john.txt:zip2john.zip::zip2john.ziproot@kali:/home/kali# **john zip.hashes** 
Using default input encoding: UTF-8
Loaded 1 password hash (PKZIP [32/64])
Proceeding with single, rules:Single
Press 'q' or Ctrl-C to abort, almost any other key for status
Warning: Only 2 candidates buffered for the current salt, minimum 8 needed for performance.
Warning: Only 3 candidates buffered for the current salt, minimum 8 needed for performance.
Warning: Only 4 candidates buffered for the current salt, minimum 8 needed for performance.
Almost done: Processing the remaining buffered candidate passwords, if any.
Warning: Only 1 candidate buffered for the current salt, minimum 8 needed for performance.
Proceeding with wordlist:/usr/share/john/password.lst, rules:Wordlist
**pass**             (zip2john.zip/zip2john.txt)
1g 0:00:00:00 DONE 2/3 (2020-10-03 10:16) 50.00g/s 1105Kp/s 1105Kc/s 1105KC/s modem..robocop
Use the "--show" option to display all of the cracked passwords reliably
Session completedroot@kali:/home/kali# **john zip.hashes --show**
zip2john.zip/zip2john.txt:pass:zip2john.txt:zip2john.zip::zip2john.zip
1 password hash cracked, 0 left
```

我创建了一个包含单词“**example”**的纯文本文件。然后，我用密码“ **pass** ”创建了一个 ZIP 存档。然后，我运行“ **zip2john** ”来提取 zip 哈希。然后，我在散列文件上运行“ **john** ”。密码“ **pass** ”在毫秒内被破解。

您可以使用“ **rar2john** ”对 RAR 档案做完全相同的事情，它也包含在 Kali Linux 中。

Kali Linux 还包含许多其他变体…

```
root@kali:~# **ls -la /usr/sbin | grep 2john**
-rwxr-xr-x  1 root root      55192 Sep 13  2019 bitlocker2john
-rwxr-xr-x  1 root root      22392 Sep 13  2019 dmg2john
lrwxrwxrwx  1 root root          4 Sep 13  2019 gpg2john -> john
-rwxr-xr-x  1 root root      18296 Sep 13  2019 hccap2john
-rwxr-xr-x  1 root root      55208 Sep 13  2019 keepass2john
-rwxr-xr-x  1 root root      22392 Sep 13  2019 putty2john
-rwxr-xr-x  1 root root      18296 Sep 13  2019 racf2john
lrwxrwxrwx  1 root root          4 Sep 13  2019 rar2john -> john
-rwxr-xr-x  1 root root      22472 Sep 13  2019 uaf2john
-rwxr-xr-x  1 root root      14200 Sep 13  2019 vncpcap2john
-rwxr-xr-x  1 root root      59408 Sep 13  2019 wpapcap2john
lrwxrwxrwx  1 root root          4 Sep 13  2019 zip2john -> johnroot@kali:~# **ls -la /usr/share/john | grep 2john**
-rwxr-xr-x   1 root root   11530 May 14  2019 1password2john.py
-rwxr-xr-x   1 root root   83932 Sep 13  2019 7z2john.pl
-rwxr-xr-x   1 root root    3897 May 14  2019 adxcsouf2john.py
-rwxr-xr-x   1 root root    2403 May 14  2019 aem2john.py
-rwxr-xr-x   1 root root     890 Sep 13  2019 aix2john.pl
-rwxr-xr-x   1 root root    2085 May 14  2019 aix2john.py
-rwxr-xr-x   1 root root    1369 May 14  2019 andotp2john.py
-rwxr-xr-x   1 root root    3456 May 14  2019 androidbackup2john.py
-rwxr-xr-x   1 root root    7449 May 14  2019 androidfde2john.py
-rwxr-xr-x   1 root root    1710 May 14  2019 ansible2john.py
-rwxr-xr-x   1 root root     732 May 14  2019 apex2john.py
-rwxr-xr-x   1 root root    2028 May 14  2019 applenotes2john.py
-rwxr-xr-x   1 root root    1448 May 14  2019 aruba2john.py
-rwxr-xr-x   1 root root    6294 May 14  2019 axcrypt2john.py
-rwxr-xr-x   1 root root   10174 May 14  2019 bestcrypt2john.py
-rwxr-xr-x   1 root root   33770 May 14  2019 bitcoin2john.py
-rwxr-xr-x   1 root root    2720 May 14  2019 bitshares2john.py
-rwxr-xr-x   1 root root    4318 May 14  2019 bitwarden2john.py
-rwxr-xr-x   1 root root    7755 May 14  2019 bks2john.py
-rwxr-xr-x   1 root root    2686 May 14  2019 blockchain2john.py
-rwxr-xr-x   1 root root   26741 May 14  2019 ccache2john.py
-rwxr-xr-x   1 root root    6487 Sep 13  2019 cisco2john.pl
-rwxr-xr-x   1 root root     881 May 14  2019 cracf2john.py
-rwxr-xr-x   1 root root    2538 May 14  2019 dashlane2john.py
-rwxr-xr-x   1 root root    3771 May 14  2019 deepsound2john.py
-rw-r--r--   1 root root    7365 May 14  2019 diskcryptor2john.py
-rwxr-xr-x   1 root root    5250 May 14  2019 dmg2john.py
-rwxr-xr-x   1 root root   26225 May 14  2019 DPAPImk2john.py
-rwxr-xr-x   1 root root    2054 May 14  2019 ecryptfs2john.py
-rwxr-xr-x   1 root root    5085 May 14  2019 ejabberd2john.py
-rwxr-xr-x   1 root root    9574 May 14  2019 electrum2john.py
-rwxr-xr-x   1 root root    2718 May 14  2019 encfs2john.py
-rwxr-xr-x   1 root root    1093 May 14  2019 enpass2john.py
-rwxr-xr-x   1 root root    3775 May 14  2019 ethereum2john.py
-rwxr-xr-x   1 root root    1726 May 14  2019 filezilla2john.py
-rwxr-xr-x   1 root root    3440 May 14  2019 geli2john.py
-rwxr-xr-x   1 root root    7096 May 14  2019 hccapx2john.py
-rwxr-xr-x   1 root root    1060 May 14  2019 htdigest2john.py
-rwxr-xr-x   1 root root    1304 May 14  2019 ibmiscanner2john.py
-rwxr-xr-x   1 root root     711 May 14  2019 ikescan2john.py
-rwxr-xr-x   1 root root    5711 Sep 13  2019 itunes_backup2john.pl
-rwxr-xr-x   1 root root    4811 May 14  2019 iwork2john.py
-rwxr-xr-x   1 root root    1110 May 14  2019 kdcdump2john.py
-rwxr-xr-x   1 root root    2956 May 14  2019 keychain2john.py
-rwxr-xr-x   1 root root    3408 May 14  2019 keyring2john.py
-rwxr-xr-x   1 root root    5417 May 14  2019 keystore2john.py
-rwxr-xr-x   1 root root    2325 May 14  2019 kirbi2john.py
-rwxr-xr-x   1 root root     837 May 14  2019 known_hosts2john.py
-rwxr-xr-x   1 root root    9682 May 14  2019 krb2john.py
-rwxr-xr-x   1 root root    4432 May 14  2019 kwallet2john.py
-rwxr-xr-x   1 root root    4299 May 14  2019 lastpass2john.py
-rwxr-xr-x   1 root root     468 Sep 13  2019 ldif2john.pl
-rwxr-xr-x   1 root root    5645 May 14  2019 libreoffice2john.py
-rwxr-xr-x   1 root root     874 Sep 13  2019 lion2john-alt.pl
-rwxr-xr-x   1 root root     990 Sep 13  2019 lion2john.pl
-rwxr-xr-x   1 root root    1527 May 14  2019 lotus2john.py
-rwxr-xr-x   1 root root    4426 May 14  2019 luks2john.py
-rwxr-xr-x   1 root root    2603 May 14  2019 mac2john-alt.py
-rwxr-xr-x   1 root root   24612 May 14  2019 mac2john.py
-rwxr-xr-x   1 root root    2297 May 14  2019 mcafee_epo2john.py
-rwxr-xr-x   1 root root    1373 May 14  2019 monero2john.py
-rwxr-xr-x   1 root root    2495 May 14  2019 money2john.py
-rwxr-xr-x   1 root root    2576 May 14  2019 mozilla2john.py
-rwxr-xr-x   1 root root   56632 May 14  2019 multibit2john.py
-rwxr-xr-x   1 root root     943 May 14  2019 neo2john.py
-rwxr-xr-x   1 root root  131690 May 14  2019 office2john.py
-rwxr-xr-x   1 root root    3073 May 14  2019 openbsd_softraid2john.py
-rwxr-xr-x   1 root root    3792 May 14  2019 openssl2john.py
-rwxr-xr-x   1 root root    2846 May 14  2019 padlock2john.py
-rwxr-xr-x   1 root root   56777 May 14  2019 pcap2john.py
-rwxr-xr-x   1 root root   59772 Sep 13  2019 pdf2john.pl
-rwxr-xr-x   1 root root    4735 May 14  2019 pem2john.py
-rwxr-xr-x   1 root root    3303 May 14  2019 pfx2john.py
-rwxr-xr-x   1 root root    9861 May 14  2019 pgpdisk2john.py
-rwxr-xr-x   1 root root    2751 May 14  2019 pgpsda2john.py
-rwxr-xr-x   1 root root    9381 May 14  2019 pgpwde2john.py
-rwxr-xr-x   1 root root    1463 May 14  2019 prosody2john.py
-rwxr-xr-x   1 root root    2481 May 14  2019 pse2john.py
-rwxr-xr-x   1 root root    1337 May 14  2019 ps_token2john.py
-rwxr-xr-x   1 root root    1684 May 14  2019 pwsafe2john.py
-rwxr-xr-x   1 root root    7162 Sep 13  2019 radius2john.pl
-rwxr-xr-x   1 root root    2274 May 14  2019 radius2john.py
-rwxr-xr-x   1 root root    9537 Sep 13  2019 sap2john.pl
-rwxr-xr-x   1 root root   20434 May 14  2019 signal2john.py
-rwxr-xr-x   1 root root     856 May 14  2019 sipdump2john.py
-rwxr-xr-x   1 root root    7781 May 14  2019 ssh2john.py
-rwxr-xr-x   1 root root   25118 May 14  2019 sspr2john.py
-rwxr-xr-x   1 root root    3782 May 14  2019 staroffice2john.py
-rwxr-xr-x   1 root root     686 May 14  2019 strip2john.py
-rwxr-xr-x   1 root root    5203 May 14  2019 telegram2john.py
-rwxr-xr-x   1 root root    4931 May 14  2019 tezos2john.py
-rwxr-xr-x   1 root root    3206 May 14  2019 truecrypt2john.py
-rwxr-xr-x   1 root root    2246 Sep 13  2019 vdi2john.pl
-rwxr-xr-x   1 root root    2185 May 14  2019 vmx2john.py
```

这就是破解 SSH 私钥的方法。我用“ **pass123** ”这个短语创建了一个。

```
kali@kali:~$ **ssh-keygen**
Generating public/private rsa key pair.
Enter file in which to save the key (/home/kali/.ssh/id_rsa): 
Created directory '/home/kali/.ssh'.
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/kali/.ssh/id_rsa
Your public key has been saved in /home/kali/.ssh/id_rsa.pub
The key fingerprint is:
SHA256:5LasngROUjrU5Y7iVNt2367kOcbqoOHUGA7EVgBMNw8 kali@kali
The key's randomart image is:
+---[RSA 3072]----+
|+o.E. .          |
| o.o+o           |
|  = +.. .        |
| + + = o         |
|  B * + S        |
| o O * + o .     |
|  . * + o.o .    |
|   o + + o+o     |
|    o.+.oo+o.    |
+----[SHA256]-----+kali@kali:~$ **sudo /usr/share/john/ssh2john.py /home/kali/.ssh/id_rsa > id_rsa.txt**kali@kali:~$ **cat id_rsa.txt** 
/home/kali/.ssh/id_rsa:$sshng$2$16$57cf61c7d0a6804a6daf016a886def31$1894$6f70656e7373682d6b65792d7631000000000a6165733235362d63747200000006626372797074000000180000001057cf61c7d0a6804a6daf016a886def31000000100000000100000197000000077373682d727361000000030100010000018100b368edba67ec85a792e39921192019c28ddd6e78c954332a93d85082c941ad229a39e9eaa18960234ae2fdac4a8580e69440918e8fa05aeba001f258abd7c7414bfac983aca8aa8098c980f3cafda99fd2d1a09ec4bb23ce7835430ca4d93ca9f61931c091c2e9a15a3f3847eb61802d7eb560c85ff3b934e1246f5692c610646e3221dd7e102842f68ae28bf38d0bb1fb47a4f7174bfe2b81929ffb7c1e65fc796137f9550425095fc62cc5fb882b4a6b48add1d57bdc42515c57926a3baaa2f6fbca8d96686bb0ecf498637be1def0e6ecd5e0adb2f637be1bbdf141a02b76c1e8a8595b0cea02c11d46adc4f8a596a1880625747c44f5ef097a74d69d3acefac72afc49a704b98413ffbe9376b7280af6df7109e0e7f75dfab1f314d8729580080b7e9ebbef9a327112e609a25b1f374d3c0204d63c5cb692eb7fb735f0537c786d0c4c6e513d6544b5fe71cb1792273176a944f532ac42a1a588b275cb48bb55c1cd1f455c9fad11aee39127c40373b484d07a28891701d31c1ee5c927ff00000580b869ed4b8a85a459400ee5f53a9ae3a0c5ccb2ff2550341b164e25d3c2f9c6368b434780d1cf04811100749e5ac987908a33de42834b8b745231d766d1a9b2b3c21106391696faf9eab4a5ef791069c7f7436c482af53c2ac8fb5f80d76891001570b6d00fd158c28580a7042e17f5003a0cdfd6d012b1bc5e628df787229677119dfa8f3fbe697e2eea954079f11cd93cbb5f022f0f77aa779fb667fac012f1a48a67656ddfeb258d68783275a240afbc7cb8d609674ba9e3061503a61cf3d608e4dd885787af762a749e951cea1dff6cd37b0d0df17cf3082582224ee17ec613d1b27e1c27ab8a5fb446e8502197bf8f1a842a4b92305b9285150aa27d45c255fd5a482648e7bf761fc7bb57d3e1cf086b2a43bb00757d27f53ace8576716c58812457edc2530065707514fc63efbbf7633fcc2620218476fc6cebc2729cbf68eaaf4f585371ed26352c77671ac69d229f1c041ed435076e894f8480ef3fc9771d038338a114eeee00a5f3b6616d79a0b7836890bd2e74f2907eee10480bd3c8755d50c53eb77abc76b6892104d42dd09b971f118d85686c16994df5d24c90a145e148723e5cd3f1cfac93426b61813bc1c528d796c5d2496646f3d18257c205b13555fc1247caaf23f6316a888ddcfd1fbe2ec6fd0a05a3655ba42a9a475c32ac97b9ff21ced5fa333bb8b9ee19b3c50d022019d1de3deb20cad9709be49607bf4d08e22c6ec945497c800ab09481e10b83eb22bdde6df1e2a5ad1cad0fd0fff2030001c9e41b2a4cef5472df9fd2d2421c3daa59e2f8c2939ef31c97c9d4b7895e474089f60f6c7b760c5d5e0c339d3e0ef1a061d725ed485bd62b596c63d4471fb52a7c449c969d1f76606517483bb00046494b93d377d9c4eb75dd4fcae7568250a39ffa2d3d8eb1440e352863a9a71b81288a1eae66d0d3520a772bb2ea2fc42050c0369d2aeaa4bc9a45c6f9668ba4d90965910cfb82072050332567daa209f98fe747ce3f9b092d94c491b9efdacc118b80eade99d44d8c3af2753f0c3d6578ec462f96b863abb247506a7ddafc6f64d609fced4fe511fe273b1da1286f4904834e49e4b5ea5f329d7d4755daabb58e04315213878db1657c5dca91bfa39f56cb75d1a0a02ffcd38bf8fa70f5e578057623f5b7f8a1480cb1948db7b0957e73cff4bf5731ca1e981d09ede55d44fd7c1067d2d786bd33e0ae2015348712f27abb07c761b80d6ecded67e76092a73b93bf79efbd71a6cf7ef134996d60e4c4bf7981e131690c133dd971724d55c297ccf0bcfe927e0c3e6d3eb1d9f84300cfb1a06acc6cc64b22cf2887f1dd66a9a410c7102402f6e4ce252b2c1d92f20fdcc676d4d63c62b1d893b1c0b30a107ccdf876c33358ccbc0f667c9e324a87a9a0b6abbce8140dab4379c2c8e646f5865de0842fed2339382e6fc47a8b37f508d8182340a7b65a9405fc1b253e263d4e28b9bca6edfe11268c140d97ee1ebf87c09b8d49e6ba1267a79512a187d912190a5b214f3ba623d96a9f4a273d738b680291d2754eaff5102a348378e96867304e64b3ce3446b04db5abf43b17c2a0221cfb25a6deae8555cc238d3b44a3d92133cec43692fa61124e252df04492e86fb6206083c968bd03b41c1e786cbe70be1bce6b5fd3ff1e679f4225c2bdb8f751ca001c806529b564c2cf9c38eca2dd7d1e050ce4018695b8d6818f17caba8a2eb340b7dd69dbd22e11bc3c114c8ae962527e692fb870b33c8103f9973bbb134418a90a470efa1653131e8470ed1cf2e3b732dc3c4f064fa6c157b6a9e1ec3bbb8b097d214d53147d75900e29f42a93896b7740f6a4d7baa5fadfd5d2861fa2c2a7ee3be663bcd956276587b5c5303682d0e4e027b74b808b84e9f2a9d092254bd5ed8b0bb50ae1667a82b999d01b4f36795c60c6d640eab758a8047abf68c245421c9f003823d27da1da2471f81edf7581102c35657c387341f7ba9547d2$16$486kali@kali:~$ **sudo john --wordlist=/usr/share/wordlists/rockyou.txt id_rsa.txt**
Using default input encoding: UTF-8
Loaded 1 password hash (SSH [RSA/DSA/EC/OPENSSH (SSH private keys) 32/64])
Cost 1 (KDF/cipher [0=MD5/AES 1=MD5/3DES 2=Bcrypt/AES]) is 2 for all loaded hashes
Cost 2 (iteration count) is 16 for all loaded hashes
Note: This format may emit false positives, so it will keep trying even after
finding a possible candidate.
Press 'q' or Ctrl-C to abort, almost any other key for status
0g 0:00:05:18 0.02% (ETA: 2020-10-23 06:36) 0g/s 10.34p/s 10.34c/s 10.34C/s larisa
Session aborted
```

我不打算一一介绍" **2john** "工具，但基本上每一个工具都有一个提取散列的过程，然后您将使用" **john** "来破解它。

关于如何使用“john”的更多例子，你可以在这里找到。

Kali Linux 中包含的其他密码破解工具值得研究，这取决于您的需求。如果这是您感兴趣的领域，我建议您也研究一下这些工具。

*   **美杜莎**(“美杜莎”)——非常快速地强行远程服务，如 SMB、HTTP、POP3、MSSQL、SSH v2 等等。
*   **NC rack**(“NC rack”)——非常快速的网络认证破解工具，支持多种协议，包括 SSH、RDP、FTP、Telnet、HTTP(S)、Wordpress、POP3(S)、IMAP、CVS、SMB、VNC、SIP、Redis、PostgreSQL、MQTT、MySQL、MSSQL、MongoDB、Cassandra、WinRM、OWA、DICOM。
*   **oph crack**(“oph crack”)—用彩虹表破解 Windows 密码。
*   **Mimikatz**(“minikatz”)—使用 Windows 上的管理员权限以明文形式显示密码。
*   **chnt pw**(" sudo chnt pw ")—在 Windows SAM 文件中更改用户密码，或调用注册表编辑器。应该可以处理 32 位和 64 位 windows 以及从 NT3.x 到 Win8.1 的所有版本
*   **THC-PPT-bruter**(“THC-PPTP-bruter”)—这个强力工具针对 PPTP VPN 端点。它支持 MSChapV2 身份验证。Windows-Hack 使用相同的呼叫者 id 重新使用 LCP 连接。这绕过了微软的反暴力保护。默认情况下是启用的。它已经过思科和微软终端的测试。
*   **RS mangler**(“RS mangler”)—获取一个单词列表并对其执行各种操作，类似于开膛手约翰所做的操作，主要区别在于，它将首先获取输入单词并生成所有单词的排列和首字母缩略词(按照它们在文件中出现的顺序)，然后应用其余的 mangler..

**为了进一步阅读，请查看我写的关于这个话题的 19 个故事。**

![Michael Whittle](img/f4d39d0c62b7f2e165491c1e2a8c6e26.png)

迈克尔·惠特尔

## 道德黑客培训课程

[View list](https://whittle.medium.com/list/ethical-hacking-training-course-710769700b83?source=post_page-----11ae7ab17fb0--------------------------------)19 stories![](img/57c6829d9a7a0ff6d78e5d828845bb8c.png)![](img/e2d3aa85ee76ceb2b889dd753c231b67.png)![](img/6240cd235d84e9cd8cb158d7147a71f5.png)

# 迈克尔·惠特尔

*   ***如果你喜欢这个，请*** [***跟我上媒***](https://whittle.medium.com/)
*   ***更多有趣的文章，请*** [***关注我的刊物***](https://medium.com/trading-data-analysis)
*   ***有兴趣合作吗？*** [***我们来连线 LinkedIn 上的***](https://www.linkedin.com/in/miwhittle/)
*   ***支持我和其他媒体作者*** [***在此报名***](https://whittle.medium.com/membership)
*   ***请别忘了为文章鼓掌:)←谢谢！***