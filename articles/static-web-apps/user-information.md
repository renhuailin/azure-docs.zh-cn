---
title: 访问 Azure 静态 Web 应用的用户信息
description: 了解如何读取授权提供程序返回的用户数据。
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 04/09/2021
ms.author: cshoe
ms.custom: devx-track-js
ms.openlocfilehash: 10b622a707747e69beba1e2be5f989ee2bcd4804
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124804549"
---
# <a name="accessing-user-information-in-azure-static-web-apps"></a>访问 Azure 静态 Web 应用的用户信息

Azure 静态 Web 应用通过[直接访问终结点](#direct-access-endpoint)向 [API 函数](#api-functions)提供验证相关的用户信息。

许多用户界面在很大程度上依赖于用户身份验证数据。 直接访问终结点是一个无需实现自定义函数即可公开用户信息的实用工具 API。 除了便利性之外，直接访问终结点不受无服务器架构所带来的冷启动延迟的影响。

## <a name="client-principal-data"></a>客户端主体数据

客户端主体数据对象向应用公开用户可识别的信息。 以下是客户端主体对象的特有属性：

| properties           | 说明                                                                                                                                                                                                                                                                                                                                                        |
| ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `identityProvider` | [标识提供程序](authentication-authorization.md)的名称。                                                                                                                                                                                                                                                                                              |
| `userId`           | 用户的特定于 Azure 静态 Web 应用的唯一标识符。 <ul><li>该值在每个应用中都是唯一的。 例如，同一用户在不同的静态 Web 应用资源上返回不同的 `userId` 值。<li>该值在用户的生存期内保持不变。 如果删除同一用户并将其重新添加到应用，则会生成一个新的 `userId`。</ul> |
| `userDetails`      | 用户的用户名或电子邮件地址。 一些提供程序返回[用户的电子邮件地址](authentication-authorization.md)，而其他提供程序则发送[用户句柄](authentication-authorization.md)。                                                                                                                                                                    |
| `userRoles`        | [用户的已分配角色](authentication-authorization.md)的数组。                                                                                                                                                                                                                                                                                          |

以下示例是示例客户端主体对象：

```json
{
  "identityProvider": "github",
  "userId": "d75b260a64504067bfc5b2905e3b8182",
  "userDetails": "username",
  "userRoles": ["anonymous", "authenticated"]
}
```

## <a name="direct-access-endpoint"></a>直接访问终结点

可以向 `/.auth/me` 路由发送 `GET` 请求，并接收对客户端主体数据的直接访问。 当视图状态依赖于授权数据时，使用这种方法可获得最佳性能。

对于已登录的用户，响应中包含一个客户端主体 JSON 对象。 未经身份验证的用户发送的请求会返回 `null`。

使用 [fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch)<sup>1</sup> API，可以使用以下语法访问客户端主体数据。

```javascript
async function getUserInfo() {
  const response = await fetch('/.auth/me');
  const payload = await response.json();
  const { clientPrincipal } = payload;
  return clientPrincipal;
}

console.log(getUserInfo());
```

## <a name="api-functions"></a>API 函数

静态 Web 应用中通过 Azure Functions 后端提供的 API 函数有权访问与客户端应用程序相同的用户信息。 尽管 API 接收到用户身份信息，但如果用户经过身份验证或符合所需角色，则不会执行其检查。 在 [`staticwebapp.config.json`](configuration.md#routes) 文件中定义访问控制规则。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

客户端主体数据会被传递到 `x-ms-client-principal` 请求标头中的 API 函数。 客户端主体数据以 [Base64](https://www.wikipedia.org/wiki/Base64) 编码字符串的形式发送，其中包含一个序列化的 JSON 对象。

下面的示例函数显示了如何读取和返回用户信息。

```javascript
module.exports = async function (context, req) {
  const header = req.headers['x-ms-client-principal'];
  const encoded = Buffer.from(header, 'base64');
  const decoded = encoded.toString('ascii');

  context.res = {
    body: {
      clientPrincipal: JSON.parse(decoded),
    },
  };
};
```

假设上面的函数命名为 `user`，可以使用 [fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch)<sup>1</sup> 浏览器 API 通过下面的语法访问 API 的响应。

```javascript
async function getUser() {
  const response = await fetch('/api/user');
  const payload = await response.json();
  const { clientPrincipal } = payload;
  return clientPrincipal;
}

console.log(await getUser());
```

# <a name="c"></a>[C#](#tab/csharp)

在 C# 函数中，可以从能够反序列化到 `ClaimsPrincipal` 对象或你自己的自定义类型的标头 `x-ms-client-principal` 中获取用户信息。 下面的代码演示如何将标头解压缩到中间类型 `ClientPrincipal`，然后将该类型转换为 `ClaimsPrincipal` 实例。

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Security.Claims;
using System.Text;
using System.Text.Json;
using Microsoft.AspNetCore.Http;

public static class StaticWebAppsAuth
{
  private class ClientPrincipal
  {
      public string IdentityProvider { get; set; }
      public string UserId { get; set; }
      public string UserDetails { get; set; }
      public IEnumerable<string> UserRoles { get; set; }
  }

  public static ClaimsPrincipal Parse(HttpRequest req)
  {
      var principal = new ClientPrincipal();

      if (req.Headers.TryGetValue("x-ms-client-principal", out var header))
      {
          var data = header[0];
          var decoded = Convert.FromBase64String(data);
          var json = Encoding.UTF8.GetString(decoded);
          principal = JsonSerializer.Deserialize<ClientPrincipal>(json, new JsonSerializerOptions { PropertyNameCaseInsensitive = true });
      }

      principal.UserRoles = principal.UserRoles?.Except(new string[] { "anonymous" }, StringComparer.CurrentCultureIgnoreCase);

      if (!principal.UserRoles?.Any() ?? true)
      {
          return new ClaimsPrincipal();
      }

      var identity = new ClaimsIdentity(principal.IdentityProvider);
      identity.AddClaim(new Claim(ClaimTypes.NameIdentifier, principal.UserId));
      identity.AddClaim(new Claim(ClaimTypes.Name, principal.UserDetails));
      identity.AddClaims(principal.UserRoles.Select(r => new Claim(ClaimTypes.Role, r)));

      return new ClaimsPrincipal(identity);
  }
}
```

---

当用户登录时，`x-ms-client-principal` 标头将通过 Static Web Apps 边缘节点添加到用户信息请求中。

<sup>1</sup>[fetch](https://caniuse.com/#feat=fetch) API 和 [await](https://caniuse.com/#feat=mdn-javascript_operators_await) 运算符在 Internet Explorer 中不支持。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [配置应用设置](application-settings.md)
