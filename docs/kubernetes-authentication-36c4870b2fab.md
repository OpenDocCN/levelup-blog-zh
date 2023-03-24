# Kubernetes 认证

> 原文：<https://levelup.gitconnected.com/kubernetes-authentication-36c4870b2fab>

*本文最初发表于 https://www.magalix.com/blog/kubernetes-authentication*

Kubernetes 认证意味着验证发出请求的人或物的*身份*。例如，如果您想通过 SSH 访问 Linux 机器，SSH [守护进程](https://www.magalix.com/blog/kubernetes-daemonsets-101)必须验证您用于登录的用户名和密码与/etc/passwd 和/etc/shadow 文件中的帐户相匹配。为了将它们组合在一起，这些文件代表 Linux 的认证数据库。另一方面，[授权](https://www.magalix.com/blog/kubernetes-authorization)指的是一旦获得访问权限，你被允许做什么。因此，回到我们的 Linux 示例，用户可以通过提供有效的凭证来访问操作系统，但是他们不能查看或修改敏感文件(如/etc/shadow)的内容。只有根用户或具有根权限的人可以这样做。

在 Kubernetes 中，API 服务器需要验证它收到的每个请求。请求可能来自人类用户或程序(例如 Pod)。目前，人类用户帐户需要通过一种受支持的[身份验证策略](https://kubernetes.io/docs/reference/access-authn-authz/authentication/#authentication-strategies)在集群之外进行管理。请注意，用户帐户是*而不是*命名空间，这意味着它在集群范围内必须是唯一的。但通常情况下，您还需要使程序能够获得认证并向 API 服务器发送请求。对于能够向 API 服务器进行身份验证的资源，它需要拥有一个服务帐户。

# 服务帐户

服务帐户是另一个 Kubernetes 资源(如 [pods](https://www.magalix.com/blog/kubernetes-pods-101-the-cluster-sailors) 、 [deployments](https://www.magalix.com/blog/kubernetes-deployments-101) 等)。).与用户帐户不同，服务帐户是命名空间的，因此您可以创建多个同名的服务帐户，只要它们位于不同的命名空间中。

默认情况下，服务帐户凭证通过一个[密码](https://www.magalix.com/blog/kubernetes-secrets-101)安装到 pod。让我们举个简单的例子:

```
$ kubectl run -it --rm testpod --restart=Never --image=alpine -- sh
If you don't see a command prompt, try pressing enter.
/ # ls -l /var/run/secrets/kubernetes.io/serviceaccount/
total 0
lrwxrwxrwx    1 root     root            13 Nov  8 11:45 ca.crt -> ..data/ca.crt
lrwxrwxrwx    1 root     root            16 Nov  8 11:45 namespace -> ..data/namespace
lrwxrwxrwx    1 root     root            12 Nov  8 11:45 token -> ..data/token
```

这是一个一次性的豆荚，拥有阿尔卑斯山的图像。打开容器的外壳，我们可以很容易地查看从/data 挂载点下的外部秘密挂载到 pod 的文件。身份验证过程是通过令牌文件完成的。让我们看看这个文件的内容:

```
/ # cat /var/run/secrets/kubernetes.io/serviceaccount/token && echo
eyJhbGciOiJSUzI1NiIsImtpZCI6IiJ9.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJkZWZhdWx0Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZWNyZXQubmFtZSI6ImRlZmF1bHQtdG9rZW4tcHh6ZjciLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC5uYW1lIjoiZGVmYXVsdCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50LnVpZCI6ImUzMzFmNTU1LTAxOTktMTFlYS1hYTVhLTQyMDEwYTgwMDBhYSIsInN1YiI6InN5c3RlbTpzZXJ2aWNlYWNjb3VudDpkZWZhdWx0OmRlZmF1bHQifQ.I3jFqRd7ANj4BidzoW-v_YO1N_z2p33Ex1biaz_J97QK9CRlIFmK2KC1k0RbQux3zo72xsLhbPjNH40pBzqr2TzVjZKFD7T8-ihDj1Og5L_BAud-CtT4e-0zcvC5rTKcWzkUn5a64TUSwF5Q6I3KjeaE3pmDFwG4I6XLuODCVOwRkfn0V_LzjqLy3nXfUK8FpIkeBrRd9QN68PG9YFH9lKMgGZLvuG_m6K6EFxqBpVMec8SPsG77GGRezC9Mjsyxp2Wie-j8vUVb5et2o4ShKa8sp-Nqum4bpBxGQ9NBo3qlefuqYGYivGZBnL8dienBLFbO5swSHUL6vxTgaRg2kA
/ #
```

令牌的内容是 [JSON Web 令牌(JWT)](https://en.wikipedia.org/wiki/JSON_Web_Token) 格式。可以使用 [jwt](https://jwt.io/) 命令行工具或者在 jwt.io 网站上在线解码。下面是 web 上解码操作的输出，同样也是在命令行上:

![](img/6521aab0227172065993da0d40c12d26.png)

在命令行上:

```
$ jwt eyJhbGciOiJSUzI1NiIsImtpZCI6IiJ9.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJkZWZhdWx0Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZWNyZXQubmFtZSI6ImRlZmF1bHQtdG9rZW4tcHh6ZjciLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC5uYW1lIjoiZGVmYXVsdCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50LnVpZCI6ImUzMzFmNTU1LTAxOTktMTFlYS1hYTVhLTQyMDEwYTgwMDBhYSIsInN1YiI6InN5c3RlbTpzZXJ2aWNlYWNjb3VudDpkZWZhdWx0OmRlZmF1bHQifQ.I3jFqRd7ANj4BidzoW-v_YO1N_z2p33Ex1biaz_J97QK9CRlIFmK2KC1k0RbQux3zo72xsLhbPjNH40pBzqr2TzVjZKFD7T8-ihDj1Og5L_BAud-CtT4e-0zcvC5rTKcWzkUn5a64TUSwF5Q6I3KjeaE3pmDFwG4I6XLuODCVOwRkfn0V_LzjqLy3nXfUK8FpIkeBrRd9QN68PG9YFH9lKMgGZLvuG_m6K6EFxqBpVMec8SPsG77GGRezC9Mjsyxp2Wie-j8vUVb5et2o4ShKa8sp-Nqum4bpBxGQ9NBo3qlefuqYGYivGZBnL8dienBLFbO5swSHUL6vxTgaRg2kATo verify on jwt.io:[https://jwt.io/#id_token=eyJhbGciOiJSUzI1NiIsImtpZCI6IiJ9.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJkZWZhdWx0Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZWNyZXQubmFtZSI6ImRlZmF1bHQtdG9rZW4tcHh6ZjciLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC5uYW1lIjoiZGVmYXVsdCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50LnVpZCI6ImUzMzFmNTU1LTAxOTktMTFlYS1hYTVhLTQyMDEwYTgwMDBhYSIsInN1YiI6InN5c3RlbTpzZXJ2aWNlYWNjb3VudDpkZWZhdWx0OmRlZmF1bHQifQ.I3jFqRd7ANj4BidzoW-v_YO1N_z2p33Ex1biaz_J97QK9CRlIFmK2KC1k0RbQux3zo72xsLhbPjNH40pBzqr2TzVjZKFD7T8-ihDj1Og5L_BAud-CtT4e-0zcvC5rTKcWzkUn5a64TUSwF5Q6I3KjeaE3pmDFwG4I6XLuODCVOwRkfn0V_LzjqLy3nXfUK8FpIkeBrRd9QN68PG9YFH9lKMgGZLvuG_m6K6EFxqBpVMec8SPsG77GGRezC9Mjsyxp2Wie-j8vUVb5et2o4ShKa8sp-Nqum4bpBxGQ9NBo3qlefuqYGYivGZBnL8dienBLFbO5swSHUL6vxTgaRg2kA](https://jwt.io/#id_token=eyJhbGciOiJSUzI1NiIsImtpZCI6IiJ9.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJkZWZhdWx0Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZWNyZXQubmFtZSI6ImRlZmF1bHQtdG9rZW4tcHh6ZjciLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC5uYW1lIjoiZGVmYXVsdCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50LnVpZCI6ImUzMzFmNTU1LTAxOTktMTFlYS1hYTVhLTQyMDEwYTgwMDBhYSIsInN1YiI6InN5c3RlbTpzZXJ2aWNlYWNjb3VudDpkZWZhdWx0OmRlZmF1bHQifQ.I3jFqRd7ANj4BidzoW-v_YO1N_z2p33Ex1biaz_J97QK9CRlIFmK2KC1k0RbQux3zo72xsLhbPjNH40pBzqr2TzVjZKFD7T8-ihDj1Og5L_BAud-CtT4e-0zcvC5rTKcWzkUn5a64TUSwF5Q6I3KjeaE3pmDFwG4I6XLuODCVOwRkfn0V_LzjqLy3nXfUK8FpIkeBrRd9QN68PG9YFH9lKMgGZLvuG_m6K6EFxqBpVMec8SPsG77GGRezC9Mjsyxp2Wie-j8vUVb5et2o4ShKa8sp-Nqum4bpBxGQ9NBo3qlefuqYGYivGZBnL8dienBLFbO5swSHUL6vxTgaRg2kA)✻ Header
{
  "alg": "RS256",
  "kid": ""
}✻ Payload
{
  "iss": "kubernetes/serviceaccount",
  "kubernetes.io/serviceaccount/namespace": "default",
  "kubernetes.io/serviceaccount/secret.name": "default-token-pxzf7",
  "kubernetes.io/serviceaccount/service-account.name": "default",
  "kubernetes.io/serviceaccount/service-account.uid": "e331f555-0199-11ea-aa5a-42010a8000aa",
  "sub": "system:serviceaccount:default:default"
}✻ Signature I3jFqRd7ANj4BidzoW-v_YO1N_z2p33Ex1biaz_J97QK9CRlIFmK2KC1k0RbQux3zo72xsLhbPjNH40pBzqr2TzVjZKFD7T8-ihDj1Og5L_BAud-CtT4e-0zcvC5rTKcWzkUn5a64TUSwF5Q6I3KjeaE3pmDFwG4I6XLuODCVOwRkfn0V_LzjqLy3nXfUK8FpIkeBrRd9QN68PG9YFH9lKMgGZLvuG_m6K6EFxqBpVMec8SPsG77GGRezC9Mjsyxp2Wie-j8vUVb5et2o4ShKa8sp-Nqum4bpBxGQ9NBo3qlefuqYGYivGZBnL8dienBLFbO5swSHUL6vxTgaRg2kA
```

输出中有趣的部分是有效载荷。如您所见，我们将 service-account.name 设置为“default”。这是在初始化时自动创建并装载到每个 pod 的默认服务帐户。在大多数情况下，当您需要授予 pod 更多权限时，您不应该使用默认帐户。让我们创建一个服务帐户，并将其分配给一个 pod:

```
$ kubectl create serviceaccount testsa                                                                                                             
serviceaccount/testsa created
```

注意，我们在这里没有指定名称空间，所以我们的 testsa 服务帐户是在默认名称空间中创建的。如果我们看一下这个名称空间中的秘密，我们会发现两个秘密:

```
$ kubectl get secrets
NAME                  TYPE                                  DATA   AGE
default-token-pxzf7   kubernetes.io/service-account-token   3      16h
testsa-token-zwhvm    kubernetes.io/service-account-token   3      11s
```

第一个秘密是为我们前面检查过的默认服务帐户创建的。第二个包含我们刚刚创建的 testsa 帐户的凭证。

现在，我们已经准备好了服务帐户，让我们将它附加到一个 pod。以下定义创建了一个使用 testsa 作为服务帐户的 pod:

```
apiVersion: v1
kind: Pod
metadata:
  name: testpod
spec:
  serviceAccountName: testsa
  containers:
    - name: mycontainer
      image: alpine:latest
      command:
        - "sh"
        - "-c"
        - "sleep 1000"
```

也许这个定义中有趣的部分在第 6 行，我们在那里指定了 serviceAccountName 参数。容器本身运行 Alpine 映像，我们指示它休眠 1000 秒，这足以让我们在进程退出之前检查容器内容。现在，如果您将此定义应用到您的集群，您可以使用如下命令登录到 pod:

```
kubectl exec -it testpod -- sh
```

如果您检查/var/run/secrets/kubernetes . io/service account/的内容，您会看到一个令牌文件，但是(使用 jwt 工具解密时)包含不同的服务帐户名和凭证。

# Kubernetes 认证方法

在本文的开始，我们以 Linux 为例说明了认证是如何发生的。Linux 使用/etc/shadow 和/etc/passwd 来存储用户凭证。虽然您可以在 Linux 上使用其他身份验证方法，比如 LDAP，但是您没有太多其他选择。另一方面， [Kubernetes](https://www.magalix.com/blog/kubernetes-101-concepts-and-why-it-matters) 的设计者希望在选择如何认证用户时给予管理员更多的灵活性。可以通过插件启用/禁用不同的身份验证方法。让我们来看看它们:

*   **静态密码或令牌:**这是最简单(但最不灵活)的方法。您的每个用户都有一个固定的密码或令牌。API 服务器通过 HTTP 基本身份验证对用户进行身份验证，其中用户名和密码通过 HTTP 请求的授权头传递。比如[授权](https://www.magalix.com/blog/kubernetes-authorization)基本 bXl1c2VyOm15cGFzc3dvcmQK。请注意，身份验证类型(basic)后面的字符集是 myuser:mypassword 的 base64 编码版本。基本 HTTP 身份验证还支持基于令牌的凭据，用户只需提供令牌即可获得身份验证。在这种情况下，HTTP 请求的授权头如下所示:

```
Authorization Bearer: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.2Jy0uRBfQC3EIo-_iv3QE0qQMDKYLvBpkK82_7J6q0M
```

*   不用说，这两种认证都必须通过 HTTPS 发送。否则，你在锁门的同时把钥匙交给了入侵者。这种方法要求您为每个用户维护一个包含其凭据的静态文件。它还需要直接访问 API 服务器，这使得它非常不灵活。它应该只在不太关心身份验证的测试/开发环境中使用。
*   **X.509 证书:**您可以使用证书对 API 服务器进行认证。工作流程如下:您创建一个证书请求，通过证书颁发机构(CA)对其进行签名，并在身份验证阶段将其提交给 API 服务器。API 服务器咨询 CA 服务器以验证证书，并相应地批准或拒绝请求。请注意，这种机制与浏览器在需要通过 HTTPS 联系网站时使用的传统工作流有相似之处。
*   **使用 OpenID Connect 单点登录:**您可以使用 OAuth 2.0 提供商之一，如 Google、Azure Active Directory 等。向 API 服务器验证自己的身份。在这个场景中，用户需要首先登录 Google(如果我们决定使用 Google 作为 OIDC 提供者),一旦通过身份验证，他们就会发送一个包含身份信息的不记名令牌。发送这个令牌意味着用户已经被提供者成功地认证。不记名令牌是一个包含用户信息的 [JSON Web 令牌](https://jwt.io/)(前面已经讨论过了)。API 服务器需要以下标志:

**— oidc-issuer-url** :认证提供者提供的发现 url，没有路径。例如，如果发现网址是 https://accounts.google.com/.的[知名/openid-configuration，](https://accounts.google.com/.well-known/openid-configuration,)的值应该是 https://accounts.google.com[。](https://accounts.google.com.)注意，只接受使用 https 的 URL。

**— oidc-client-id:** 将为其颁发令牌的客户端 id。比如 kubernetes。

![](img/c7ca688a8b7dfcaa90a33ee1ed2c1975.png)

然而，web 证书具有用于显示网站名称(用于身份验证)的[通用名称](https://en.wikipedia.org/wiki/Common_name)和用于存储与资源位置相关的信息的[主题](https://en.wikipedia.org/wiki/X.509)，如国家、城市、部门等。Kubernetes 使用 common name 字段作为用户名，并使用 subject 来添加该用户所属的任何名称空间。在这种身份验证方法中，集群操作员/管理员负责在用户之间创建和分发证书。此外，监控证书到期和撤销也是工作流的一部分。

![](img/cf68e4629f071f512ac059cfb70a77e3.png)

这种身份验证方法需要上述标志才能工作。然而，您可以提供其他的[可选标志](https://kubernetes.io/docs/reference/access-authn-authz/authentication/#configuring-the-api-server)来根据您的喜好定制认证过程。

![](img/585ed8092366699953b6bd92dde33798.png)

以上策略都是现成的。但是，您可能需要将 API 身份验证过程与现有的 LDAP 或 Kerberos 身份验证提供程序联系起来。Kubernetes 提供了两种与外部身份验证提供者集成的方法:

假设您选择 X-Remote-User 作为包含用户名的头，X-Remote-Group 用于指定用户组(或名称空间),任何以 X-Remote-Extra- prefix 开头的头用于可能包含用户额外信息的其他头。现在，API 服务器可以使用以下标志来启用此配置:

```
--requestheader-username-headers=X-Remote-User
--requestheader-group-headers=X-Remote-Group
--requestheader-extra-headers-prefix=X-Remote-Extra-
```

保存所需信息的典型 HTTP 请求如下所示:

```
GET / HTTP/1.1
X-Remote-User: fido
X-Remote-Group: dogs
X-Remote-Group: dachshunds
X-Remote-Extra-Acme.com%2Fproject: some-project
X-Remote-Extra-Scopes: openid
X-Remote-Extra-Scopes: profile
```

然而，没有什么可以阻止攻击者通过使用合法用户的名字和名称空间来冒充合法用户。因此，API 代理使用认证机构来验证请求。API 服务器选项中添加了以下标志:
—request header-client-ca-file，这是一个 PEM 编码的证书包。任何请求都必须有一个有效的证书，API 服务器首先根据证书颁发机构对该证书进行验证。

```
# Kubernetes API version
apiVersion: v1
# kind of the API object
kind: Config
# clusters refers to the remote service.
clusters:
  - name: name-of-remote-authn-service
    cluster:
      certificate-authority: /path/to/ca.pem         # CA for verifying the remote service.
      server: [https://authn.example.com/authenticate](https://authn.example.com/authenticate) # URL of remote service to query. Must use 'https'.# users refers to the API server's webhook configuration.
users:
  - name: name-of-api-server
    user:
      client-certificate: /path/to/cert.pem # cert for the webhook plugin to use
      client-key: /path/to/key.pem          # key matching the cert# kubeconfig files require a context. Provide one for the API server.
current-context: webhook
contexts:
- context:
    cluster: name-of-remote-authn-service
    user: name-of-api-sever
  name: webhook
```

—authentic ation-token-web hook-cache-TTL:在要求用户重新提供令牌之前，API 服务器授予用户的宽限期(就像您第一次使用 sudo，然后继续使用一段时间，而不需要提供您的密码)。默认值是两分钟。

# TL；速度三角形定位法(dead reckoning)

*   身份验证是指验证试图访问资源的用户或服务的身份。
*   Kubernetes 非常灵活，允许您选择适合您和您的组织的身份验证机制。
*   最简单的身份验证方法是使用包含用户密码或令牌的静态文件。不建议在生产环境中使用这种方法，因为您需要为每个用户或服务维护一个文件，并且需要访问 API 服务器。
*   X.509 方法是一种流行的身份验证机制，在这种机制中，您为每个用户创建一个包含用户名和每个用户所属组的证书。您需要部署一个证书颁发机构，并维护证书的生成、撤销和过期。然而，它仍然比静态文件方法好。
*   使用任何支持 OIDC (OpenID Connect)的平台，比如 Google，您可以配置 API 服务器来接受用户通过成功认证 OIDC 服务而获得的不记名令牌。
*   如果您有自己的认证服务，比如 LDAP 服务器或基于 Kerberos 的机制，您仍然可以将其与 Kubernetes 集成，通过它们来认证用户。Kubernetes 在这些场景中使用身份验证代理和 Webhook 令牌身份验证。
*   身份验证代理允许您在 HTTP 请求中选择特定的头，以提取所需的身份验证信息，如用户名和名称空间。
*   Webhook 身份验证允许您的用户通过外部服务(例如 GitHub)生成和使用他们自己的令牌，并在向 API 服务器进行身份验证时使用它们。

*原载于 2020 年 1 月 21 日 https://www.magalix.com*[](https://www.magalix.com/blog/kubernetes-authentication)**。**