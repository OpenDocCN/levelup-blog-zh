# 如何在 Windows 上安装 Jupyter 笔记本家庭和远程服务器

> 原文：<https://levelup.gitconnected.com/how-to-install-the-jupyter-notebook-home-and-remote-server-on-windows-9bfcf39ee2a7>

## 编程:

## 漂亮简单的教程，一步一步的指导

![](img/ba1aab49aa7793eaf93a25d6bd72f38b.png)

凯蒂·乏色曼的图片

## 总结:

本文安装 Jupyter 笔记本家庭和公共服务器。它安装要求、配置服务器设置、安全和保护以及防火墙权限。它还安排服务器在启动时运行，设置端口转发和静态 ip 地址，并使用同一 wifi 和不同 wifi 的私有和公共 ip 地址访问服务器。

## 目录:

1.  [安装要求](#21a5)
2.  [配置服务器](#1d0c)
3.  [启动时运行服务器](#253a)
4.  [设置端口转发](#62c8)
5.  [设置静态 IP 地址](#f62c)
6.  [在家访问服务器](#d2ff)
7.  [公开访问服务器](#3a74)

## 附录:

1.  [教程:人工智能设置](#50ec)
2.  [教程:人工智能课程](#c3c2)
3.  教程:人工智能库

## 安装要求:

本节安装 jupyter notebook 和 websocket 扩展，将 git 添加到 path 环境变量中，并创建通用配置文件。

```
**# open the powershell shell** 1\. press “⊞ windows”
2\. enter “powershell” into the search bar
3\. right-click "windows powershell"
4\. click “run as administrator”**# install jupyter notebook**
python -m pip install jupyter notebook**# install the websocket extension** python -m pip install jupyter_http_over_ws**# download git**
invoke-webrequest -uri https://github.com/git-for-windows/git/releases/download/v2.33.0.windows.1/Git-2.33.0-64-bit.exe -outfile "$home/downloads/git.exe"**# install git using the default settings** invoke-item "$home/downloads/git.exe"**# add git to the path environment variables** $new_paths = "c:\program files\git\usr\bin"; $old_paths = [system.environment]::getenvironmentvariable('path','user'); [environment]::setenvironmentvariable("path", "$new_paths; $old_paths", "user")**# create the jupyter notebook configuration file** jupyter notebook --generate-config**# reload the environment variables**
$env:path = [system.environment]::getenvironmentvariable("path","machine") + ";" + [system.environment]::getenvironmentvariable("path","user")
```

## 配置服务器:

该部分创建用户配置文件，添加基于加密的安全性和密码保护，并在防火墙中授权服务器。

```
**# open the jupyter notebook directory**
cd "$home\.jupyter"**# provide arbitrary information to create the ssl certificate**
openssl req -config “c:\program files (86x)\git\usr\ssl\openssl.cnf” -x509 -nodes -days 365 -newkey rsa:2048 -keyout mykey.key -out mycert.pem**# change to the desired root directory for jupyter notebook** $root_directory = "c:\\users\\$env:username\\"**# create the user configuration file**
set-content $home\.jupyter\jupyter_notebook_config.json -encoding ascii "{`n `"NotebookApp`": {`n        `"open_browser`": false,`n        `"port`": 8887,`n        `"ip`": `"0.0.0.0`",`n        `"allow_origin`": `"*`",`n        `"notebook_dir`": `"$root_directory`",`n        `"certfile`": `"c:\\users\\$env:username\\.jupyter\\mycert.pem`",`n        `"keyfile`": `"c:\\users\\$env:username\\.jupyter\\mykey.key`",`n        `"allow_remote_access`": true,`n        `"nbserver_extensions`": {`n            `"jupyter_http_over_ws`": true`n        }`n    }`n}"**# create the password to access the jupyter notebook server**
jupyter notebook password**# create a firewall rule to authorize jupyter notebook** new-netfirewallrule -displayname "jupyter notebook server (windows)" -direction inbound -localport 8887 -protocol tcp -profile any -action allow
```

## 启动时运行服务器:

本节安排 jupyter 笔记本服务器在启动时运行。

```
**# create the script that starts jupyter notebook**
echo "set object = createobject(`"wscript.shell`")
object.run `"jupyter notebook --config $home\.jupyter\jupyter_notebook_config.json`", 0" >> "$home\.jupyter\startup_script.vbs"**# name the task that starts jupyter notebook**
$name = "jupyter notebook server (windows)"**# set the task to run the startup script**
$action = new-scheduledtaskaction -execute “$home\.jupyter\startup_script.vbs”**# set the task to run after the computer starts up**
$trigger = new-jobtrigger -atstartup -randomdelay(new-timespan -seconds 30)**# set the task to run as the user**
$principal = new-scheduledtaskprincipal -userid $env:username-runlevel highest**# set the task to run with no time limit**
$settings = new-scheduledtasksettingsset -executiontimelimit 0**# add the task to the other scheduled tasks**
register-scheduledtask -taskname $name -action $action -trigger $trigger -principal $principal -settings $settings**# restart the computer**
restart-computer
```

## 设置端口转发:

本节介绍如何访问路由器以配置端口转发，从而允许通过路由器和防火墙访问 jupyter 笔记本电脑服务器。

```
**# open the powershell shell** 1\. press “⊞ windows”
2\. enter “powershell” into the search bar
3\. right-click "windows powershell"
4\. click “run as administrator”**# find your router's ip address**
start-process iexplore https://www.techspot.com/guides/287-default-router-ip-addresses/**# change the ip address to your router's ip address**
$router_ip_address = "http://192.168.0.1"**# open the router's ip address**
start-process iexplore $router_ip_address**# bypass the unnecessary "connection" warning:** 1\. click anywhere on the website
2\. type "thisisunsafe"**# log into the router** 1\. check the physical router for administrator information
2\. enter the administrator username
3\. enter the administrator password
4\. click "log in"**# store the private ip address to the computer**
$private_ip_address = [regex]::new('\d*\.\d*\.\d*\.\d*').match($(ipconfig)).value**# write down the private ip address to the computer**
echo "`n`n
*************************************************************************************`n
    ATTENTION: Use this ip address to configure the port forwarding:`n`n    IP Address: $private_ip_address`n`n*************************************************************************************`n`n`n`n"**# configure the port forwarding** 
1\. search through the advanced settings
2\. click "port forwarding"
3\. enter the "private ip address" into "enter ip address"
4\. enter "8887" into "wan starting port"
5\. enter "8887" into "wan ending port"
6\. click "apply"
```

## 设置静态 IP 地址:

此部分为网络适配器分配一个静态 ip 地址，以防止访问 jupyter 笔记本电脑服务器的专用 ip 地址发生变化。

```
**# store network adapter name** $network_adapter_name = get-netadapter -physical | where status -eq 'up' | select-object -property name -expandproperty name**# store private ip address** $private_ip_address = get-netipaddress -addressfamily ipv4 -interfacealias $network_adapter_name | select-object -property ipaddress -expandproperty ipaddress**# store default gateway**
$default_gateway = get-netipconfiguration -interfacealias $network_adapter_name | select-object -property ipv4defaultgateway -expandproperty ipv4defaultgateway | select-object -property nexthop -expandproperty nexthop**# store dns server addresses**
$dns_server_addresses = [string]::join(", ", (get-dnsclientserveraddress -addressfamily ipv4 -interfacealias $network_adapter_name | select-object -property serveraddresses -expandproperty serveraddresses))**# remove existing network adapter configuration** remove-netipaddress -interfacealias $network_adapter_name -confirm:$false**# remove existing default gateway**
remove-netroute -interfacealias $network_adapter_name -confirm:$false**# remove existing dns server addresses** set-dnsclientserveraddress -interfacealias $network_adapter_name -resetserveraddresses**# add new network adapter configuration** 
new-netipaddress -interfacealias $network_adapter_name -addressfamily ipv4 -ipaddress $private_ip_address -prefixlength 24 -defaultgateway $default_gateway**# add dns server address to network adapter configuration** 
set-dnsclientserveraddress -interfacealias $network_adapter_name -serveraddresses $dns_server_addresses
```

## 在家访问服务器:

此部分请求、显示并使用私有 ip 地址从同一台计算机和 wifi 连接访问 jupyter 笔记本电脑服务器。

```
**# store the private ip address**
$private_ip_address = [regex]::new('\d*\.\d*\.\d*\.\d*').match($(ipconfig)).value**# store the url to the server**
$url = "https://$private_ip_address`:8887"**# write down the url to access the server**
echo "`n`n
*************************************************************************************`n
    ATTENTION: Use this url to access the server from the same wifi connection:`n`n    $url`n`n*************************************************************************************`n`n`n`n"**# access the server from the same wifi connection** start-process iexplore $url**# bypass the unnecessary "connection" warning** 1\. click anywhere on the website
2\. type "thisisunsafe"**# log into the server**
1\. enter the server password
2\. click "log in"
```

## 公开访问服务器:

此部分请求、显示并使用公共 ip 地址从不同的计算机和 wifi 连接访问 jupyter 笔记本电脑服务器。

```
**# store the public ip address to the server**
$public_ip_address = (invoke-webrequest -uri "http://ifconfig.me/ip").content# store the url to access the server
$url = "https://$public_ip_address`:8887"**# write down the url to access the server**
echo "`n`n
*************************************************************************************`n
    ATTENTION: Use this url to access the server from a different wifi connection:`n`n    $url`n`n*************************************************************************************`n`n`n`n"**# access the server from a different wifi connection** start-process iexplore $url**# bypass the unnecessary "connection" warning** 1\. click anywhere on the website
2\. type "thisisunsafe"**# log into the server**
1\. enter the server password
2\. click "log in"
```

> "最后，记得订阅并按住鼓掌按钮，以获得定期更新和帮助."

## 附录:

这个博客的存在是为了提供完整的解决方案，回答你的问题，加速你与人工智能相关的进步。它提供了设置计算机和完成 fastai 课程前半部分所需的一切。它将让你接触到人工智能子领域中最先进的知识库。它也将涵盖 fastai 课程的后半部分。

## 教程:人工智能设置

本节提供了设置电脑所需的一切。

```
**# linux**
01\. [install and manage multiple python versions](https://medium.com/p/916990dabe4b)
02\. [install the nvidia cuda driver, toolkit, cudnn, and tensorrt](https://medium.com/p/cd5b3a4f824)
03\. [install the jupyter notebook home and public server](https://medium.com/p/b2c14c47b446)
04\. [install virtual environments in jupyter notebook](https://medium.com/p/1556c8655506)
05\. [install the python environment for ai and machine learning](https://medium.com/p/765678fcb4fb)
06\. [install the fastai course requirements](https://medium.com/p/116415a9df22/)**# wsl 2**
01\. [install windows subsystem for linux 2](https://medium.com/p/8ef0e1538052/)
02\. [install and manage multiple python versions](https://medium.com/p/1131c4e50a58)
03\. [install the nvidia cuda driver, toolkit, cudnn, and tensorrt](https://medium.com/p/9800abd74409) 
04\. [install the jupyter notebook home and public server](https://medium.com/p/7c96b3705df1)
05\. [install virtual environments in jupyter notebook](https://medium.com/p/3e6bf456041b)
06\. [install the python environment for ai and machine learning](https://medium.com/p/612240cb8c0c)
07\. [install ubuntu desktop with a graphical user interface](https://medium.com/p/95911ee2997f)
08\. [install the fastai course requirements](https://medium.com/p/15a77fc7e301/)**# windows 10**
01\. [install and manage multiple python versions](https://medium.com/p/153f8258be0f/)
02\. [install the nvidia cuda driver, toolkit, cudnn, and tensorrt](https://medium.com/p/af58647b6d9a/) 03\. [install the jupyter notebook home and public server](https://medium.com/p/9bfcf39ee2a7/)
04\. [install virtual environments in jupyter notebook](https://medium.com/p/296e1d176ea9/)
05\. [install the programming environment for ai and machine learning](https://medium.com/p/8d1bc340c1c5/)**# mac** 01\. [install and manage multiple python versions](https://medium.com/p/ca01a5e398d4)
02\. [install the jupyter notebook home and public server](https://medium.com/p/2a276f679e0)
03\. [install virtual environments in jupyter notebook](https://medium.com/p/e3de97491b3a)
04\. [install the python environment for ai and machine learning](https://medium.com/p/2b2353d7bcc3)
05\. [install the fastai course requirements](https://medium.com/p/90fdd524bc82)
```

## 教程:人工智能课程

本部分包含每课结束时对问卷的回答。

```
**# fastai course** 01\. [chapter 1: your deep learning journey q&](https://medium.com/p/6f266bdb1340/)a
02\. [chapter 2: from model to production q&a](https://medium.com/p/5a0902207f5b)
03\. [chapter 3: data ethics q&a](https://medium.com/p/501bb37ca30d)
04\. [chapter 4: under the hood: training a digit classifier q&a](https://medium.com/p/89077906197e/)
05\. [chapter 5: image classification q&a](https://medium.com/p/aa7cacdeab1/)
06\. [chapter 6: other computer vision problems q&a](https://medium.com/p/aa7cacdeab1/)
07\. [chapter 7: training a state-of-the-art model q&a](https://medium.com/p/6f6dcc83dd9f/)
08\. [chapter 8: collaborative filtering deep dive q&a](https://medium.com/p/52d3583d626b/)
```

## 教程:人工智能库

这个部分包含不同子领域中的最先进的知识库。

```
**# repositories related to audio** 01\. [raise audio quality using nu-wave](https://medium.com/p/e3dd979056e0/)
02\. [change voices using maskcyclegan-vc](https://medium.com/p/8bdfeb1faecb/)
03\. [clone voices using real-time-voice-cloning toolbox](https://medium.com/p/7b8609438001/)**# repositories related to images**
01\. [achieve 90% accuracy using facedetection-dsfd](https://medium.com/p/9c9fefb3f863/)
```