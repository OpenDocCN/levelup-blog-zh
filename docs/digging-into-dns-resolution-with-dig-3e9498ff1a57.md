# 用 dig 挖掘 DNS 解析

> 原文：<https://levelup.gitconnected.com/digging-into-dns-resolution-with-dig-3e9498ff1a57>

![](img/dad185faa8b4ed03b352113274c2906c.png)

照片由[马库斯·温克勒](https://unsplash.com/@markuswinkler?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

电脑和数字打交道。电脑使用称为 IP 地址的数字地址与另一台电脑通信。虽然它是结构化的，因此对计算机来说很棒，但对人类来说很难记住。

DNS 充当互联网的电话簿🌐。它将诸如“example.com”之类的网址转换成计算机用来连接的 IP 地址。因此，我们不必记住复杂的 IP 地址🤩。

![](img/5fc06ea7937282b69f91da07f8fbb512.png)

*惊艳小品/漫画作者* [朱莉娅·埃文斯！](https://twitter.com/b0rk?ref_src=twsrc%5Egoogle%7Ctwcamp%5Eserp%7Ctwgr%5Eauthor)

我们正试图在浏览器上打开 example.com。典型的 DNS 查找是这样的:

1.  浏览器首先在其 DNS 缓存中查找“example.com”。如果存在，浏览器将使用缓存的 IP 地址并连接到“example.com”。如果没有，则浏览器进入下一步。
2.  浏览器发出一个`gethostbyname (3)`并将名称解析的责任传递给操作系统(OS)。操作系统现在成为解析器。
3.  操作系统在系统 DNS 缓存中查找域名。如果找到了，它就把 IP 地址返回给浏览器，否则操作系统进入下一步。
4.  操作系统查看被称为主机文件的`\etc\hosts`。在 ARPANET 时代，hosts 文件是维护主机名到 IP 地址映射的一种方法。如果条目存在，操作系统返回 IP 地址，否则进入下一步。
5.  操作系统尝试连接到您配置的 DNS 服务器，并发送针对“example.com”的 DNS 查询。您可以手动设置您的 DNS 服务器，或者您连接的网络可以为您进行配置。DNS 服务器现在成为解析器，必须向发送 DNS 查询的机器的操作系统返回响应。
6.  DNS 服务器(解析器)在其 DNS 缓存中查找主机名。如果它找到一个条目，它就把这个条目返回给调用机器。否则进入下一步。
7.  DNS 服务器尝试连接到根名称服务器(。)你可以做`dig .`来找到你的 DNS 服务器试图连接的根域名服务器。目前，以字母“a”到“m”——`a.root-servers.net.`命名的根域名服务器有 13 个

```
➜ dig -t NS .; <<>> DiG 9.10.6 <<>> -t NS .
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 45206
;; flags: qr rd ra; QUERY: 1, ANSWER: 13, AUTHORITY: 0, ADDITIONAL: 1;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;.              IN  NS;; ANSWER SECTION:
.           48  IN  NS  a.root-servers.net.
.           48  IN  NS  d.root-servers.net.
.           48  IN  NS  k.root-servers.net.
.           48  IN  NS  g.root-servers.net.
.           48  IN  NS  j.root-servers.net.
.           48  IN  NS  c.root-servers.net.
.           48  IN  NS  b.root-servers.net.
.           48  IN  NS  m.root-servers.net.
.           48  IN  NS  f.root-servers.net.
.           48  IN  NS  h.root-servers.net.
.           48  IN  NS  l.root-servers.net.
.           48  IN  NS  e.root-servers.net.
.           48  IN  NS  i.root-servers.net.;; Query time: 80 msec
;; SERVER: 10.254.254.210#53(10.254.254.210)
;; WHEN: Wed May 06 22:51:43 IST 2020
;; MSG SIZE  rcvd: 239
```

8.现在，DNS 服务器请求上述根域名服务器中的一个作为 TLD 的 TLD 级根域名服务器。com”。

```
➜ dig [@d](http://twitter.com/d).root-servers.net. -t NS com.; <<>> DiG 9.10.6 <<>> [@d](http://twitter.com/d).root-servers.net. -t NS com.
; (1 server found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 106
;; flags: qr rd; QUERY: 1, ANSWER: 0, AUTHORITY: 13, ADDITIONAL: 27
;; WARNING: recursion requested but not available;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1450
;; QUESTION SECTION:
;com.               IN  NS;; AUTHORITY SECTION:
com.            172800  IN  NS  a.gtld-servers.net.
com.            172800  IN  NS  b.gtld-servers.net.
com.            172800  IN  NS  c.gtld-servers.net.
com.            172800  IN  NS  d.gtld-servers.net.
com.            172800  IN  NS  e.gtld-servers.net.
com.            172800  IN  NS  f.gtld-servers.net.
com.            172800  IN  NS  g.gtld-servers.net.
com.            172800  IN  NS  h.gtld-servers.net.
com.            172800  IN  NS  i.gtld-servers.net.
com.            172800  IN  NS  j.gtld-servers.net.
com.            172800  IN  NS  k.gtld-servers.net.
com.            172800  IN  NS  l.gtld-servers.net.
com.            172800  IN  NS  m.gtld-servers.net.;; ADDITIONAL SECTION:
a.gtld-servers.net. 172800  IN  A   192.5.6.30
b.gtld-servers.net. 172800  IN  A   192.33.14.30
c.gtld-servers.net. 172800  IN  A   192.26.92.30
d.gtld-servers.net. 172800  IN  A   192.31.80.30
e.gtld-servers.net. 172800  IN  A   192.12.94.30
f.gtld-servers.net. 172800  IN  A   192.35.51.30
g.gtld-servers.net. 172800  IN  A   192.42.93.30
h.gtld-servers.net. 172800  IN  A   192.54.112.30
i.gtld-servers.net. 172800  IN  A   192.43.172.30
j.gtld-servers.net. 172800  IN  A   192.48.79.30
k.gtld-servers.net. 172800  IN  A   192.52.178.30
l.gtld-servers.net. 172800  IN  A   192.41.162.30
m.gtld-servers.net. 172800  IN  A   192.55.83.30
a.gtld-servers.net. 172800  IN  AAAA    2001:503:a83e::2:30
b.gtld-servers.net. 172800  IN  AAAA    2001:503:231d::2:30
c.gtld-servers.net. 172800  IN  AAAA    2001:503:83eb::30
d.gtld-servers.net. 172800  IN  AAAA    2001:500:856e::30
e.gtld-servers.net. 172800  IN  AAAA    2001:502:1ca1::30
f.gtld-servers.net. 172800  IN  AAAA    2001:503:d414::30
g.gtld-servers.net. 172800  IN  AAAA    2001:503:eea3::30
h.gtld-servers.net. 172800  IN  AAAA    2001:502:8cc::30
i.gtld-servers.net. 172800  IN  AAAA    2001:503:39c1::30
j.gtld-servers.net. 172800  IN  AAAA    2001:502:7094::30
k.gtld-servers.net. 172800  IN  AAAA    2001:503:d2d::30
l.gtld-servers.net. 172800  IN  AAAA    2001:500:d937::30
m.gtld-servers.net. 172800  IN  AAAA    2001:501:b1f9::30;; Query time: 259 msec
;; SERVER: 199.7.91.13#53(199.7.91.13)
;; WHEN: Wed May 06 22:54:16 IST 2020
;; MSG SIZE  rcvd: 828
```

9.然后，DNS 服务器请求上述根域名服务器之一作为域`example.com`的权威域名服务器。这组域名服务器托管该域的地址以及它可能拥有的任何子域。

```
➜ dig [@a](http://twitter.com/a).gtld-servers.net. -t NS example.com; <<>> DiG 9.10.6 <<>> [@a](http://twitter.com/a).gtld-servers.net. -t NS example.com
; (1 server found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 1127
;; flags: qr rd; QUERY: 1, ANSWER: 0, AUTHORITY: 2, ADDITIONAL: 1
;; WARNING: recursion requested but not available;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;example.com.           IN  NS;; AUTHORITY SECTION:
example.com.        172800  IN  NS  a.iana-servers.net.
example.com.        172800  IN  NS  b.iana-servers.net.;; Query time: 66 msec
;; SERVER: 192.5.6.30#53(192.5.6.30)
;; WHEN: Wed May 06 22:55:10 IST 2020
;; MSG SIZE  rcvd: 88
```

10.DNS 服务器向权威名称服务器请求该域的 IP 地址，并将结果返回给向其发送 DNS 查询的系统。

```
➜ dig [@a](http://twitter.com/a).iana-servers.net. -t A example.com; <<>> DiG 9.10.6 <<>> [@a](http://twitter.com/a).iana-servers.net. -t A example.com
; (1 server found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 5682
;; flags: qr aa rd; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1
;; WARNING: recursion requested but not available;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;example.com.           IN  A;; ANSWER SECTION:
example.com.        86400   IN  A   93.184.216.34;; Query time: 281 msec
;; SERVER: 199.43.135.53#53(199.43.135.53)
;; WHEN: Wed May 06 22:58:40 IST 2020
;; MSG SIZE  rcvd: 56
```

使用 IP 地址`93.184.216.34`，网络浏览器连接到主机。

根据每个查询返回的`TTL`,每个阶段都会在几秒钟内维护一个缓存。在下面的 DNS 查询结果中，TTL 是`86400`秒

```
example.com.        86400   IN  A   93.184.216.34
```

因此，解析器可以将查询内容缓存 86400 秒。这种缓存有助于加快进程并减少 DNS 服务器的负载。