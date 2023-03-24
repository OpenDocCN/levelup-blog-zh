# 孔 API 安全在 Kubernetes 与 API 密钥和 JWT

> 原文：<https://levelup.gitconnected.com/kong-api-security-in-kubernetes-with-api-key-and-jwt-17985135a1a6>

![](img/849c431c5f5a8e3c4e07324bb65333cb.png)

[以前](https://medium.com/@lightphos/kong-api-gateway-with-kubernetes-a1dab930a5ba)我们在 Kubernetes 建立了 Kong 和 Konga。然后，我们将 vadal-echo 服务部署到 K8s。现在，我们来看看如何保护对该服务的访问。

# 密钥认证

Kong 提供给我们的一个机制是 Key-Auth 插件。

在下面的代码中，我们添加了插件(key-auth)，一个消费者，以及一个用于该消费者的秘密密钥“secret key”。

```
echo "
apiVersion: configuration.konghq.com/v1
kind: KongPlugin
metadata:
  name: http-auth
plugin: key-auth

---
apiVersion: configuration.konghq.com/v1
kind: KongConsumer
metadata:
  name: vadal
username: vadal

---
apiVersion: configuration.konghq.com/v1
kind: KongCredential
metadata:
  name: vadalcred
consumerRef: vadal
type: key-auth
config:
  key: secretkey
" | kubectl apply -f -
```

运行上面的。

然后，我们将 key-auth 插件添加到 vadal-echo 的 ingres 中，由元数据名称 http-auth 引用。

将您的主机 IP 添加到/etc/host，作为虚拟主机，例如。

192 . 168 . 0 . 111 vadal . cluster . local

如下所示:

```
echo "
kind: Ingress
apiVersion: extensions/v1beta1
metadata:
  name: vadal-cluster
  annotations:
    konghq.com/strip-path: "true"
    konghq.com/plugins: http-auth
spec:
  rules:
    - host: vadal.cluster.local
      http:
        paths:
          - path: /echo
            backend:
              serviceName: vadal-echo
              servicePort: 80
" | kubectl apply -f -
```

尝试访问服务

> 【http://vadal.cluster.local/echo】卷曲-I[T5](http://vadal.cluster.local/echo)
> 
> *HTTP/1.1 401 未授权
> 日期:2020 年 7 月 6 日星期一 20:51:27 GMT
> 内容类型:application/JSON；charset=utf-8
> 连接:keep-alive
> WWW-Authenticate:Key realm = " Kong "
> Content-Length:41
> X-Kong-Response-Latency:1
> 服务器:kong/2.0.4*
> 
> *{"message ":"请求中未找到 API 密钥" }*

现在访问被阻止了。向请求中添加密钥。

> *卷曲-我*http://vadal.cluster.local/echo?apikey=secretkey*T21*

返回预期的响应。很好。

# JWT 认证

另一种方法是使用 JSON web 令牌(JWT ),这种方法可以在以后使用通用的身份提供者。为此，我们使用站点 [https://jwt.io](https://jwt.io) 来帮助创建我们的令牌。

# 创建 JWT (RS256)

[https://jwt.io/#debugger-io?token = eyjhbgcioijsuzi 1 NII SINR 5 CCI 6 ikpxvcj 9 . eyjzdwiiixmjm 0 nty 3 odkwiiwibmftzsi 6 inzhzgfsiiwiaxnzijoidmfkywwtaxnzdwvyiiwywrtaw 4 ionrydwusimlhdci 6 mtuxnjizotaymn 0 . dj0n _ 25 H2 kjn 1 ozz 8 obgxhv-ymzdfl 6 etomlvvvkmc 160 egqpuv 943 xaliwekfz 3 yyy](https://jwt.io/#debugger-io?token=eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6InZhZGFsIiwiaXNzIjoidmFkYWwtaXNzdWVyIiwiYWRtaW4iOnRydWUsImlhdCI6MTUxNjIzOTAyMn0.DJ0n_25h2kJN1oZZ8ObgXhv-yMZDfL6EtOMLVVKMC160egQpuPV943MxaliWEkfZ3yyrf5kOdkcbk2_daIyaKS_gBzX7AKSiCd44OGMhP7PeoovkYncND25W7mKyRBmTCRG_5VQAwtKPS2jpF0t3IqOVfG5klB9V_oAO-aIF8TOSlxE7WW_xgYoDnW0JSzh4V-VW9uFbmVnx86SeaBlVjH_cPcEDanwlUbIz1zY4_vyP4adSrEfH-chRxW6ewpjfIXTMfVj2sdtx3uabufgvVmFQ4QSQH6NKPcUWSUK58KmYJ0g-7G5BfyKCA1Lj7qLrmAHCCm-oC8OO7qdLrqcJTg&publicKey=-----BEGIN%20PUBLIC%20KEY-----%0AMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAnzyis1ZjfNB0bBgKFMSv%0AvkTtwlvBsaJq7S5wA%2BkzeVOVpVWwkWdVha4s38XM%2Fpa%2Fyr47av7%2Bz3VTmvDRyAHc%0AaT92whREFpLv9cj5lTeJSibyr%2FMrm%2FYtjCZVWgaOYIhwrXwKLqPr%2F11inWsAkfIy%0AtvHWTxZYEcXLgAXFuUuaS3uF9gEiNQwzGTU1v0FqkqTBr4B8nW3HCN47XUu0t8Y0%0Ae%2Blf4s4OxQawWD79J9%2F5d3Ry0vbV3Am1FtGJiJvOwRsIfVChDpYStTcHTCMqtvWb%0AV6L11BWkpzGXSW4Hv43qa%2BGSYOD2QU68Mb59oSk2OB%2BBtOLpJofmbGEGgvmwyCI9%0AMwIDAQAB%0A-----END%20PUBLIC%20KEY-----)

有益的是，jwt.io 还将为我们创建密钥对。我们不需要私钥，除非我们在 Konga 使用它来创建 JWT 授权。

```
-----BEGIN PUBLIC KEY----- MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAnzyis1ZjfNB0bBgKFMSv vkTtwlvBsaJq7S5wA+kzeVOVpVWwkWdVha4s38XM/pa/yr47av7+z3VTmvDRyAHc aT92whREFpLv9cj5lTeJSibyr/Mrm/YtjCZVWgaOYIhwrXwKLqPr/11inWsAkfIy tvHWTxZYEcXLgAXFuUuaS3uF9gEiNQwzGTU1v0FqkqTBr4B8nW3HCN47XUu0t8Y0 e+lf4s4OxQawWD79J9/5d3Ry0vbV3Am1FtGJiJvOwRsIfVChDpYStTcHTCMqtvWb V6L11BWkpzGXSW4Hv43qa+GSYOD2QU68Mb59oSk2OB+BtOLpJofmbGEGgvmwyCI9 MwIDAQAB -----END PUBLIC KEY-----
```

用下面的内容创建一个文件 jwt.yml，应用到 k8s。

第一部分应用了 JWT 插件。

第二部分在 K8s 中创建一个秘密，其中包括上面的公钥。

在第三部分中，我们有引用秘密的消费者。

第四入口段在服务上设置 JWT 限制 app-jwt。

```
apiVersion: configuration.konghq.com/v1
kind: KongPlugin
metadata:
  name: app-jwt
plugin: jwt

---
apiVersion: v1
kind: Secret
metadata:
  name: vadal-jwt-k8s
type: Opaque
stringData:
  kongCredType: jwt
  key: vadal-issuer
  algorithm: RS256
  rsa_public_key: |-
    -----BEGIN PUBLIC KEY-----
    MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAnzyis1ZjfNB0bBgKFMSv
    vkTtwlvBsaJq7S5wA+kzeVOVpVWwkWdVha4s38XM/pa/yr47av7+z3VTmvDRyAHc
    aT92whREFpLv9cj5lTeJSibyr/Mrm/YtjCZVWgaOYIhwrXwKLqPr/11inWsAkfIy
    tvHWTxZYEcXLgAXFuUuaS3uF9gEiNQwzGTU1v0FqkqTBr4B8nW3HCN47XUu0t8Y0
    e+lf4s4OxQawWD79J9/5d3Ry0vbV3Am1FtGJiJvOwRsIfVChDpYStTcHTCMqtvWb
    V6L11BWkpzGXSW4Hv43qa+GSYOD2QU68Mb59oSk2OB+BtOLpJofmbGEGgvmwyCI9
    MwIDAQAB
    -----END PUBLIC KEY-----

---
apiVersion: configuration.konghq.com/v1
kind: KongConsumer
metadata:
  name: vadal-jwt-k8s
username: vadal-jwt-k8s
credentials:
  - vadal-jwt-k8s

---
kind: Ingress
apiVersion: extensions/v1beta1
metadata:
  name: vadal-cluster
  annotations:
    konghq.com/strip-path: "true"
    konghq.com/plugins: app-jwt
spec:
  rules:
    - host: vadal.cluster.local
      http:
        paths:
          - path: /echo
            backend:
              serviceName: vadal-echo
              servicePort: 80
```

> *kubectl apply -f jwt.yml*

现在，当我们调用它时，我们没有被授权(我们已经从前面的 api-key 部分删除了 http-auth，现在唯一的插件是 app-jwt)。

> *curl-I*[*http://vadal.cluster.local/echo*](http://vadal.cluster.local/echo) *HTTP/1.1 401 未授权
> 日期:2020 年 7 月 6 日星期一 21:33:58 GMT
> 内容类型:application/JSON；charset=utf-8
> 连接:keep-alive
> Content-Length:26
> X-Kong-Response-Latency:0
> 服务器:Kong/2 . 0 . 4
> { " message ":" Unauthorized " }*

将 JWT 令牌导出到变量中。

> *export VADAL _ JWT = eyjhbgcioijsuzi 1 nisinr 5 CCI 6 ikpxvcj 9 . eyjzdwiiioiixmjm 0 nty 3 odkwiiwibmftzsi 6 inzhzgfsiwiaxnzijoid mfkywwtaxnzdwvyiwiywrtaw 4 ionrydwusimlhdci 6 mtuxnjizotaymn 0 . dj0n _ 25 H2 kjn 1 ozz 8 obgxhv-ymzdfl 6 etomlvvvkmc 160 egqpuv 940*

调用服务并将其传递给授权头。

> *curl -i -H "授权:来人$ { VADAL _ JWT } "*[【http://vadal.cluster.local/echo】](http://vadal.cluster.local/echo)

*结果还显示了使用者和凭据头。*

> **{ " timestamp ":" 2020–07–07t 00:36:08.874 "，
> " headers ":{
> " host ":" vadal . cluster . local "，
> "connection":"keep-alive "，
> " kong-request-id ":" 17b 1274 c-0e9a-4c9e-bd21–42895 b9d 5702 # 26 "，【T29**

*你可以在孔加看到 JWT 的背景。注意，已经从存储在 vadal-jwt-k8s 中的 K8s secret 获得了详细信息，在这里您可以轻松地管理这个秘密并更改密钥(vadal-issuer)。一个有趣的孔到 K8s 的整合。太酷了。*

*您还可以从 GUI 创建 JWT 令牌，尽管详细信息不会存储在 k8s 中，而是存储在 Kong 中。*

*![](img/cac18d0ca87484eb47f421e5682db54f.png)*

*Kong API 还显示了 jwt，*

*[http://localhost:31492/jwts](http://localhost:31492/jwts)*

*或者消费者:*

*[http://localhost:31492/消费者](http://localhost:31492/consumers)*

# *JWT 经香港空气污染指数和高速公路 256*

*一种 JWT 对称密钥算法。像这样创建它:*

> **curl-X POST*[*http://localhost:31492/consumers/vadal-jwt-k8s/jwt*](http://localhost:31492/consumers/vadal-jwt-k8s/jwt)*
> 
> **{"rsa_public_key":null，" created_at":1594169412，" consumer ":{ " id ":" 74e 2 E3 b 6–36 F9–44cf-aa7d-f 82767 c 37672 " }，" id ":" 367 a5e 08-a904–4 ab1-a1da-7915 EC 9 BF 7 a 8 "，" tags":null，" key ":"****y1rkzx 1 oz****

*将下面的代码片段应用到 [http://jwt.io](http://jwt.io)*

*还要在验证块中添加密码(epgbtpalkw4g 3 pkpnqyay 5 MEP 1 ed 0 kkq)*

*您将得到一个类似这样的令牌:*

*eyj 0 exaioijkv 1 qilcjhbgcioijiuzi 1 nij 9 . eyjpc 3 mioijzmxjlwlgxb 1 p 4 nzq 2 cgy 4 DTG 4 tkj 3 nfjos 3 M5 wkdqzyisimlhdci 6 bnvsbcwizzhwijpdwxslcjhwqioiiilcjzdwiiiiiiifq . coqjfjbfxie h0 oxih 5kg 4 _ nqdequlrhbqiqtmetyu*

*检查一下:*

> **curl -i**

*应该也可以访问 HS256 令牌。*

> **HTTP/1.1 200
> Content-Type:application/JSON
> Transfer-Encoding:chunked
> Connection:keep-alive
> Date:Wed，08 Jul 2020 15:33:05 GMT
> X-Kong-Upstream-Latency:9
> X-Kong-Proxy-Latency:1
> Via:Kong/2 . 0 . 4**
> 
> **{ " timestamp ":" 2020–07–08t 15:33:05.483 "，" joined":["host: localhost "，" connection: keep-alive "，" kong-request-id:55d 28927–75 C5–44a 7-b656–3e 218054 ac04 # 2 "，" x-forwarded-proto: http "，" x-forwarded-host: localhost "，" x-forwarded-port: 8000 "，" x-real-ip: 1**

# *使用 ACL*

*Kong 中的访问控制列表由消费者内部的组来管理。消费者将具有带有某种授权方法的凭证。然后，ACL 插件可以附加到路由或服务，以及白名单或黑名单中定义的组。*

*![](img/eaa7d7adfb8f0b0690569160399805d5.png)*

*插件支持该特性。消费者凭证定义了要使用的授权细节。*

*这样，ACL 允许/不允许消费者访问路由或服务。只有正确的消费者凭证才能访问定义的资源。*

*让我们从同一个映像创建另一个部署，但是通过 ACL 限制对它的访问。*

> **kubectl 创建部署 vadal-echo-admin-image = vadal-echo:0 . 0 . 1-快照**
> 
> **ku bectl expose deploy vadal-echo-admin-port = 80-target-port = 8080**

*使用以下内容定义一个新的消费者并添加插件和入口。*

```
*apiVersion: configuration.konghq.com/v1
kind: KongPlugin
metadata:
  name: vadal-acl
plugin: acl
config:
  whitelist: ['vadal-admin']

---
apiVersion: v1
kind: Secret
metadata:
  name: vadal-acl-secret
type: Opaque
stringData:
  kongCredType: acl
  group: vadal-admin

---
apiVersion: configuration.konghq.com/v1
kind: KongConsumer
metadata:
  name: vadal-admin
username: vadal-admin
credentials:
  - vadal-acl-secret

---
kind: Ingress
apiVersion: extensions/v1beta1
metadata:
  name: vadal-cluster
  annotations:
    kubernetes.io/ingress.class: "kong"
    konghq.com/strip-path: "true"
    konghq.com/plugins: app-jwt, vadal-acl
spec:
  rules:
    - host: vadal.cluster.local
      http:
        paths:
          - path: /echo-admin
            backend:
              serviceName: vadal-echo-admin
              servicePort: 80
          - path: /echo
            backend:
              serviceName: vadal-echo
              servicePort: 80*
```

*上面针对路由路径/echo-admin 设置了一个 ACL (vadal-acl)。此 acl 将组 vadal-admin 列入白名单。消费者 vadal-admin 由组 vadal-admin 组成。*

*使用之前定义的 JWT 调用服务。*

> *vadal.cluster.local/echo-admin*
> 
> **HTTP/1.1 403 禁止
> 日期:2020 年 07 月 08 日 00:00:15 GMT
> 内容类型:application/JSON；charset=utf-8
> 连接:keep-alive
> Content-Length:45
> X-Kong-Response-Latency:1
> 服务器:kong/2.0.4**
> 
> **{"message ":"您不能消费该服务" }**

*ACL 不允许访问。很好。*

*现在，我们针对这个新消费者定义一个 JWT 令牌，只有这个消费者的凭证应该被接受。*

> **curl-X POST*[*http://localhost:31492/consumers/vadal-admin/jwt*](http://localhost:31492/consumers/vadal-admin/jwt)*
> 
> **{"rsa_public_key":null，" created_at":1594221350，" consumer ":{ " id ":" 45b 8094 b-5a 30–4285–9 c84–90b 106 f 4131 b " }，" id ":" 0 bffd 6 f 3-ced b-475 e-9958–9ea 005699d 17 "，" tags":null，" key ":" po 3 of 3 tmripdv 2 txv 3 gtspsp**

*您需要密钥(iss)和密码来创建令牌。*

*[https://jwt.io/#debugger-io?token = eyj 0 exaioijkv 1 qilcjhbgcioijuzi 1 nij 9 . ey jpc 3 mioiijwbznpzjnubvjjcer 2 mnr 4 vjnhvfnwd 2s wrjzowxzbmcj 9.1 hrgmwzpgrzy 0 mqcv 1 wn-enfijcdnnisom 7 lhqsegg](https://jwt.io/#debugger-io?token=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJwbzNPZjNUbVJJcER2MnR4VjNHVFNwd2swRjZOWXZBMCJ9.1HrgMwzpGRlzy0mqcv1WN-enfiJcDNnISOM7LhQSEGg)*

*我们现在有了该消费者/组的合法令牌。*

> **export VADAL _ JWT =*[*eyj 0 exaioijkv 1 qilcjhbgcioijiuzi 1 nij 9 . eyjpc 3 mioijwbznpzjnubvjjcer 2 mnr 4 vjnhvfnwd 2 swrjzowxzbmcj 9.1 hrgmwzpgrlzy 0 mqcv 1 wn-enfijcdnnxnouglhqsegg*](https://jwt.io/#debugger-io?token=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJwbzNPZjNUbVJJcER2MnR4VjNHVFNwd2swRjZOWXZBMCJ9.1HrgMwzpGRlzy0mqcv1WN-enfiJcDNnISOM7LhQSEGg)*
> 
> **curl-I-H " Authorization:Bearer $ VADAL _ JWT " vadal.cluster.local/echo-admin
> HTTP/1.1 200
> Content-Type:application/JSON
> Transfer-Encoding:chunked
> Connection:keep-alive
> Date:Wed，08 Jul 2020 15:24:33 GMT
> X-Kong-Upstream-Latency:279
> X-Kong-Proxy-Latency:3
> Via:Kong/2**
> 
> **{ " timestamp ":" 2020–07–08t 15:24:33.707 "，" joined ":[" host:vadal . cluster . local "，" connection: keep-alive "，" x-consumer-groups: vadal-admin "，" x-forwarded-proto: http "，" x-forwarded-host:vadal . cluster . local "，" x-forwarded-port: 8000 "，" x-real-ip: 192.168.65.3 "，" user-agent:curl/7.66**

*用煤气做饭。请注意持有人、消费者用户名和凭据的详细信息。服务可以对此进行进一步的解密，以获得任何有状态的信息，但不需要担心验证这一点，因为这一切都由 Kong 负责。很好。*

# *结论*

*我们已经使用 api 密钥和 jwt 为我们的服务添加了授权访问。同一服务可以有由 ACL 控制的不同访问端点。授权问题已经很好地从服务本身中分离出来，让它专注于提供商业价值。孔很好地坐在前面保护和管理访问。所有这些都在弹性可扩展的 K8s 架构中。*

*干净利落。*

**原载于 2020 年 7 月 8 日*[*https://blog . ram JEE . uk*](https://blog.ramjee.uk/kong-api-security-in-kubernetes/)*。**