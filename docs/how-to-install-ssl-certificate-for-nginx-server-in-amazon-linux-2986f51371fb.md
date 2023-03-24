# 如何在亚马逊 Linux 中为 Nginx 服务器安装 SSL 证书

> 原文：<https://levelup.gitconnected.com/how-to-install-ssl-certificate-for-nginx-server-in-amazon-linux-2986f51371fb>

![](img/00a1ce88c235c22752d671416345717d.png)

本教程将帮助你配置 HTTPS 来保护你的网站，使用一个免费的 SSL 认证中心(CA) [letsencrypt](https://letsencrypt.org/) 用于亚马逊 Linux 中的`nginx`服务器。在开始之前，您必须在 EC2 控制台中设置一个域名以指向公共 DNS。

我们将使用用户名为 *ec2-user 的 [certbot](https://certbot.eff.org/) 和 Amazon Linux AMI。*

以下是步骤:

*   **转到主页/ec2-用户**

```
- cd /home/ec2-user
```

*   **安装 certbot-auto**

```
- wget [https://dl.eff.org/certbot-auto](https://dl.eff.org/certbot-auto)
```

*   **更改 certbot-auto 的权限**

```
- chmod a+x ./certbot-auto
```

*   **生成证书**

```
- ./certbot-auto certonly --standalone --debug -d **yourdomain.com**
```

填写要求的信息，如您的电子邮件地址。如果成功，您将收到如下消息:

```
IMPORTANT NOTES:
- Congratulations! Your certificate and chain have been saved at
/etc/letsencrypt/live/**yourdomain.com**/fullchain.pem. Your cert will
expire on **yyyy-mm-dd**. To obtain a new version of the certificate in
the future, simply run Certbot again.
- If you like Certbot, please consider supporting our work by:
Donating to ISRG / Let's Encrypt:   [https://letsencrypt.org/donate](https://letsencrypt.org/donate)
Donating to EFF:                    [https://eff.org/donate-le](https://eff.org/donate-le)
```

您可以验证证书和密钥是否存在:

```
# Certificate
/etc/letsencrypt/live/**yourdomain.com**//cert.pem# Full Chain 
/etc/letsencrypt/live/**yourdomain.com**//fullchain.pem# Private Key 
/etc/letsencrypt/live/**yourdomain.com**//privkey.pem
```

*   **修改** `**nginx**` **配置**

现在您已经获得了证书，我们需要配置`nginx`让它接受 HTTPS 请求。

打开`/etc/nginx/nginx.conf`并修改:

```
...
http {
  ...

 server {
       listen 80;
       server_name yourdomain.com; location /{
         # Automatically route HTTP to HTTPS
               return 301 [https://$server_name$request_uri](/$server_name$request_uri);
       }
}server {
    listen 443 ssl;
    server_name yourdomain.com;
    ssl_certificate "/etc/letsencrypt/live/yourdomain.com/fullchain.pem";
    ssl_certificate_key "/etc/letsencrypt/live/yourdomain.com/privkey.pem";

    add_header Strict-Transport-Security "max-age=31536000";
    #other headers location / {
      autoindex on;
      root /yourdomain.com/build/; #root path of your domain's index file
      index index.html;
      try_files $uri $uri/ /index.html;
    }
  }
}
```

现在，你可以启动/重启`nginx`服务器了。

```
- sudo service nginx restart
```

请注意，证书将在 3 个月后过期，您可以设置一个 cron 作业来自动更新它。cron 作业示例:

```
Add cron job to renew certificate like:0 8 28 */3 * /home/ec2-user/certbot-auto renew10 8 28 */3 * service nginx restart# Runs at 8AM on 28th of every third month If renew fails, then stop nginx and do the renew process again
```

生成证书时可能出现的错误以及如何修复

*   如果您得到如下错误:

```
Traceback (most recent call last):
  File "/opt/eff.org/certbot/venv/bin/letsencrypt", line 7, in <module>
    from certbot.main import main
  File "/opt/eff.org/certbot/venv/local/lib/python2.7/dist-packages/certbot/main                             .py", line 10, in <module>
    import josepy as jose
  File "/opt/eff.org/certbot/venv/local/lib/python2.7/dist-packages/josepy/__ini                             t__.py", line 44, in <module>
    from josepy.interfaces import JSONDeSerializable
  File "/opt/eff.org/certbot/venv/local/lib/python2.7/dist-packages/josepy/inter                             faces.py", line 8, in <module>
    from josepy import errors, util
  File "/opt/eff.org/certbot/venv/local/lib/python2.7/dist-packages/josepy/util.                             py", line 4, in <module>
    import OpenSSL
  File "/opt/eff.org/certbot/venv/local/lib/python2.7/dist-packages/OpenSSL/__in                             it__.py", line 8, in <module>
    from OpenSSL import rand, crypto, SSL
  File "/opt/eff.org/certbot/venv/local/lib/python2.7/dist-packages/OpenSSL/rand                             .py", line 12, in <module>
    from OpenSSL._util import (
  File "/opt/eff.org/certbot/venv/local/lib/python2.7/dist-packages/OpenSSL/_uti                             l.py", line 6, in <module>
    from cryptography.hazmat.bindings.openssl.binding import Binding
ImportError: No module named cryptography.hazmat.bindings.openssl.binding
```

然后运行以下命令:

```
§  rm -rf /opt/eff.org§  pip install -U certbot
```

并再次运行命令来生成证书:

```
- ./certbot-auto certonly --standalone --debug -d **yourdomain.com**
```

*   **升级** `**pip**` **如果有警告**

```
you will see the command in console: pip install pip — upgrade
pip install virtualenv –upgrade
```

*   如果您得到这样的错误

```
Saving debug log to /var/log/letsencrypt/letsencrypt.log
Plugins selected: Authenticator standalone, Installer None
Obtaining a new certificate
Performing the following challenges:
http-01 challenge for **yourdomain.com** Cleaning up challenges
Exiting abnormally:
Traceback (most recent call last):
  File "/opt/eff.org/certbot/venv/bin/letsencrypt", line 11, in <module>
    sys.exit(main())
  File "/opt/eff.org/certbot/venv/local/lib/python2.7/site-packages/certbot/main                             .py", line 1364, in main
    return config.func(config, plugins)
  File "/opt/eff.org/certbot/venv/local/lib/python2.7/site-packages/certbot/main                             .py", line 1249, in certonly
...
```

然后停止`nginx`并再次运行命令:

```
- ./certbot-auto certonly --standalone --debug -d **yourdomain.com**
```

参考

*   [https://coder wall . com/p/e7gzbq/https-with-cert bot-for-nginx-on-Amazon-Linux](https://coderwall.com/p/e7gzbq/https-with-certbot-for-nginx-on-amazon-linux)
*   [https://certbot.eff.org/](https://certbot.eff.org/)
*   [https://crontab.guru/#0_8_28_*/3_*](https://crontab.guru/#0_8_28_*/3_*)

[](https://skilled.dev) [## 编写面试问题

### 一个完整的平台，在这里我会教你找到下一份工作所需的一切，以及…

技术开发](https://skilled.dev)