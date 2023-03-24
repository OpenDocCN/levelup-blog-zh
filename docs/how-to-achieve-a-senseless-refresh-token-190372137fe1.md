# 如何实现无意义刷新令牌

> 原文：<https://levelup.gitconnected.com/how-to-achieve-a-senseless-refresh-token-190372137fe1>

## 使用 JWT 的无意义令牌刷新

![](img/94ad27eeb5c14b33cc6adc5aae283a42.png)

## 什么是 JWT

JWT (JSON WEB TOKEN)，一种开放标准，用于以 JSON 格式为对象传递来自各方的数据信息，这些对象可以选择性地进行数字加密，并可以使用 RSA 或 ECDSA 使用公钥/私钥进行签名。

## 使用场景

JWT 最常见的使用场景是缓存当前用户登录信息，并在用户成功登录时获取 JWT，之后用户的每个请求都会在请求头中携带授权字段，以区分被请求的用户信息。并且不需要额外的资源开销。

JWT 由三部分组成，即报头、有效载荷和签名，由。符号来区分它们。

## 页眉

报头是一个 JSON 对象，由两部分组成，令牌是类型(JWT)和签名算法(SHA256，RSA)

```
{
  "alg": "HS256",
  "typ": "JWT"
}
```

## 有效载荷

有效负载也是一个 JSON 对象，它保存需要传递的数据，比如用户的信息。

```
 {
  "username": "_island",
  "age": 18
}
```

# 签名

这部分由前面两部分签名，防止数据被篡改。在服务器中指定一个密钥，根据下面的公式，使用报头中指定的签名算法生成该签名数据。

```
HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),
  secret)
```

获取签名数据后，数据的三个部分被连接在一起，每个部分由。分开每一部分。通过这种方式，我们生成一个 JWT 数据，然后将其返回给客户端进行存储。并且当客户端发起请求时，在请求头的授权字段中携带这个 JWT，服务器可以通过解密来识别对应的用户信息。

## JWT 的优势和劣势

**优点**
1。数据量小，传输速度快
2。存储数据没有额外的资源开销
3。支持跨域认证用法
**缺点**
1。生成的令牌不可撤销，即使重置账户密码前的令牌仍然可用(需要等待 JWT 过期)
2。无法确认用户
3 发出了多少个 jwt。不支持 refreshToken

## refreshToken

refreshToken 是 Oauth2 身份验证中的一个概念，与 accessToken 一起生成。

当用户携带的 accessToken 过期时，用户需要重新获取新的 accessToken，使用 refreshToken 重新获取新的 accessToken 的凭证。

## 为什么选择 refreshToken

当您第一次遇到它时，您是否怀疑过为什么在服务器端需要一个 refreshToken，而不是一个更长甚至永久的 accessToken？

其实 accessToken 有点像我们在酒店的生活，当我们入住酒店的时候，我们会出示我们的 ID 去注册一个房卡，这个时候房卡就相当于 accessToken，你可以进入相应的房间，当你的房卡过期了你就不能再开门了，这时你需要去前台更新房卡，才能正常进入，这个过程也相当于 refreshToken。
AccessToken 的使用比 refreshToken 频繁得多，如果像上面提到的那样给 AccessToken 更长的有效时间，会有不可控的权限泄露风险。

**使用 refreshToken 可以提高安全性**

当用户访问网站时，accessToken 被盗，此时攻击者可以使用 accessToke 来访问权限内的功能。如果 accessToken 被设置为在 2 小时的短时间内有效，攻击者最多可以使用窃取的 accessToken 2 小时，除非 access token 被 refreshToken 再次刷新以正常访问。

如果 accessToken 设置为永久有效，则即使在用户更改密码后，以前的 accessToken 也将有效。

总的来说，有了 refreshToken，accessToken 失窃的风险就降低了。

## JWT 无意识刷新令牌解决方案

在用户登录应用程序时，服务器会返回一组数据，包括 accessToken 和 refreshToken，每个 accessToken 都有一个固定的过期日期，如果携带一个过期的令牌向服务器请求，服务器会返回一个 401 状态码告诉用户这个令牌过期了，这时就需要使用登录时返回的 refreshToken 调用 refreshToken 接口(Refresh)来更新新的令牌然后发送请求。

> axios 是最流行的 http 请求库之一，在本文中，我们使用它的错误响应拦截器来实现令牌无意义刷新特性。

```
// Maximum number of retransmissions
const MAX_ERROR_COUNT = 5;
// Current number of retransmissions
let currentCount = 0;
// Cache Request Queue
const queue: ((t: string) => any)[] = [];
// Is the current state refreshed
let isRefresh = false;

export default async (error: AxiosError<ResponseDataType>) => {
  const statusCode = error.response?.status;
  const clearAuth = () => {
    console.log('Identity expired, please login again');
    window.location.replace('/login');
    // clear data
    sessionStorage.clear();
    return Promise.reject(error);
  };
  if (statusCode === 401) {
    // accessToken 失效
    // Determine if the local cache has refreshToken
    const refreshToken = sessionStorage.get('refresh') ?? null;
    if (!refreshToken) {
      clearAuth();
    }
    // Configuration of the extraction request
    const { config } = error;
    // Determine if refresh failed and status code 401, enter error interceptor again
    if (config.url?.includes('refresh')) {
    clearAuth();
    }

    if (!isRefresh) {
      isRefresh = true;
      // If the number of retransmissions exceeds, log out directly
      if (currentCount > MAX_ERROR_COUNT) {
        clearAuth();
      }

      currentCount += 1;

      try {
        const {
          data: { access },
        } = await UserAuthApi.refreshToken(refreshToken);
        // Request successful, cache new accessToken
        sessionStorage.set('token', access);
        currentCount = 0;

        queue.forEach((cb) => cb(access));
        return ApiInstance.request(error.config);
      } catch {
        //Failed to refresh token, logged out directly
        sessionStorage.clear();
        window.location.replace('/login');
        return Promise.reject(error);
      } finally {
        // Reset Status
        isRefresh = false;
      }
    } else {
      return new Promise((resolve) => {
        // Cache network requests and execute them directly after the token is refreshed
        queue.push((newToken: string) => {
          Reflect.set(config.headers!, 'authorization', newToken);
          // @ts-ignore
          resolve(ApiInstance.request<ResponseDataType<any>>(config));
        });
      });
    }
  }

  return Promise.reject(error);
};
```

将上面关于 refreshToken 调用的代码单独放入一个 refresh token 函数来单独处理这种情况，这样有助于提高代码的可读性和可维护性，让它看起来不那么臃肿。

```
// refreshToken.ts
export default async function refreshToken(error: AxiosError<ResponseDataType>) {
    /* 
    Just paste in the code from if (statusCode === 401) above
    */
}
```

经过上面的逻辑抽象，现在看拦截器中的代码非常简洁，后续如果想调整相关逻辑直接在 refreshToken.ts 文件中调整即可。

```
import refreshToken from './refreshToken.ts'
export default async (error: AxiosError<ResponseDataType>) => {
  const statusCode = error.response?.status;

  if (statusCode === 401) {
    refreshToken()
  }

  return Promise.reject(error);
};
```