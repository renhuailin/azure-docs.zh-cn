---
title: 生成调用 Web API 的单页应用程序
titleSuffix: Microsoft identity platform
description: 了解如何生成调用 Web API 的单页应用程序
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/27/2021
ms.author: ryanwi
ms.custom: aaddev
ms.openlocfilehash: ac71d20088a3697315ee24bce7be544e3c0763de
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2021
ms.locfileid: "129091875"
---
# <a name="single-page-application-call-a-web-api"></a>单页应用程序：调用 Web API

我们建议你在调用 Web API 之前调用 `acquireTokenSilent` 方法获取或续订访问令牌。 有了令牌后，就可以调用受保护的 Web API 了。

## <a name="call-a-web-api"></a>调用 Web API

# <a name="javascript"></a>[JavaScript](#tab/javascript)

在 HTTP 请求中使用获得的访问令牌作为持有者令牌来调用任何 Web API，例如 Microsoft Graph API。 例如：

```javascript
    var headers = new Headers();
    var bearer = "Bearer " + access_token;
    headers.append("Authorization", bearer);
    var options = {
         method: "GET",
         headers: headers
    };
    var graphEndpoint = "https://graph.microsoft.com/v1.0/me";

    fetch(graphEndpoint, options)
        .then(function (response) {
             //do something with response
        }
```

# <a name="angular"></a>[Angular](#tab/angular)

MSAL Angular 包装器器利用 HTTP 拦截器自动以无提示方式获取访问令牌并将其附加到对 API 的 HTTP 请求中。 有关详细信息，请参阅[获取令牌以调用 API](scenario-spa-acquire-token.md)。

---

## <a name="next-steps"></a>后续步骤

转到此方案中的下一篇文章：[移到生产环境](scenario-spa-production.md)。
