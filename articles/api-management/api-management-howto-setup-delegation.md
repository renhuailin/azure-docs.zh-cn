---
title: 如何委派用户注册和产品订阅
description: 了解如何在 Azure API 管理中将用户注册和产品订阅委派给第三方。
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.assetid: 8b7ad5ee-a873-4966-a400-7e508bbbe158
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/13/2021
ms.author: apimpm
ms.openlocfilehash: dcfa41d939ce578872a3b0650500829f5f4fd303
ms.sourcegitcommit: da9335cf42321b180757521e62c28f917f1b9a07
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2021
ms.locfileid: "122228765"
---
# <a name="how-to-delegate-user-registration-and-product-subscription"></a>如何委派用户注册和产品订阅

委派让网站可以拥有用户数据，并执行自定义验证。 通过委派，可以使用现有网站（而不是开发人员门户的内置功能）处理开发人员登录/注册和产品订阅。 

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="delegating-developer-sign-in-and-sign-up"></a><a name="delegate-signin-up"> </a>委派开发人员登录和注册

若要将开发人员登录和注册委派给现有网站，需要在站点上创建一个特殊的委派终结点。 此特殊委派充当从 API 管理开发人员门户发起的任何登录/注册请求的入口点。

最终工作流将是：

1. 开发人员单击 API 管理开发人员门户中的登录或注册链接。
2. 浏览器重定向到委派终结点。
3. 委派终结点再将用户重定向到登录/注册或为用户提供登录/注册选项。 
4. 成功登录/注册后，会将用户重定向回 API 管理开发人员门户中用户离开的位置。

### <a name="set-up-api-management-to-route-requests-via-delegation-endpoint"></a>设置 API 管理通过委派终结点路由请求

1. 在 Azure 门户中，搜索 API 管理资源中的“开发人员门户”。
2. 单击“委派”项。 
3. 单击复选框以启用“委派登录和注册”。

:::image type="content" source="media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png" alt-text="委派概述":::

4. 确定特殊委派终结点的 URL，将其输入“委派终结点 URL”字段中。 
5. 在“委派验证密钥”字段中，执行以下任一项：
    * 输入一个用于计算所提供签名的机密，以验证请求是否来自 API 管理。 
    * 单击“生成”按钮让 API 管理为你生成一个随机密钥。
6. 单击“保存”。

### <a name="create-your-delegation-endpoint"></a>创建委派终结点

>[!NOTE]
> 以下过程提供了 SignIn 操作的示例，也可以通过以下步骤使用任何可用的操作来执行帐户管理。 

创建新的委派终结点以在站点上实现的推荐步骤：

1. 接收以下形式的请求：
   
   > *http:\//www.yourwebsite.com/apimdelegation?operation=SignIn&returnUrl={源页的 URL}&salt={字符串}&sig={字符串}*
   
    登录/注册示例的查询参数：

   | 参数 | 说明 |
   | --------- | ----------- |
   | **operation** | 标识委派请求类型。 可用操作：SignIn、ChangePassword、ChangeProfile、CloseAccount 和 SignOut。     |
   | returnUrl | 用户在其中单击了登录或注册链接的 URL。 |
   | **加密盐** | 用于计算安全哈希的特殊加密盐字符串。 |
   | 信号 | 计算的安全哈希，用于与用户自行计算的哈希进行比较。 |
   
3. 验证请求是否来自 Azure API 管理（可选，但强烈推荐执行以确保安全）。
   
   * 根据 returnUrl 和加密盐查询参数计算字符串的 HMAC-SHA512 哈希。  如需更多详细信息，请查看[示例代码]。
     
     ```
         HMAC(salt + '\n' + returnUrl)
     ```
   * 将上面计算的哈希与 **sig** 查询参数的值进行比较。 如果两个哈希匹配，则继续进行下一步。 否则，拒绝请求。
4. 验证是否收到登录/注册请求。
    * 操作查询参数将设置为“SignIn”。 
5. 向用户提供登录/注册 UI。
    * 如果用户注册，则在 API 管理中为用户创建相应的帐户。 
      * 请使用 API 管理 REST API [创建用户]。 
      * 将用户 ID 设置为用户存储中的相同值或一个新的易于跟踪的 ID。
6. 在成功对用户进行身份验证后：
   
   * 通过 API 管理 REST API [请求共享访问令牌]。
   * 将 returnUrl 查询参数追加到从上述 API 调用接收的 SSO URL。 例如：
     
     > `https://contoso.developer.azure-api.net/signin-sso?token=<URL-encoded token>&returnUrl=%2Freturn%2Furl` 
     
   * 将用户重定向到上述生成的 URL。

>[!NOTE]
> 对于帐户管理操作（ChangePassword、ChangeProfile 和 CloseAccount），传递以下查询参数：  
> 
>    | 参数 | 说明 |
>    | --------- | ----------- |
>    | **operation** | 标识委派请求类型。 |
>    | **userId** | 要管理的帐户的用户 ID。 |
>    | **加密盐** | 用于计算安全哈希的特殊加密盐字符串。 |
>    | 信号 | 计算的安全哈希，用于与用户自行计算的哈希进行比较。 |

## <a name="delegating-product-subscription"></a><a name="delegate-product-subscription"> </a>委派产品订阅

委派产品订阅与委派用户登录/注册类似。 最终工作流将如下所示：

1. 开发人员在 API 管理开发人员门户中选择一个产品，并单击“订阅”按钮。
2. 浏览器重定向到委派终结点。
3. 委派终结点执行所需的产品订阅步骤，你可以设计这些步骤。 它们可能包括： 
   * 重定向到另一页以请求账单信息。
   * 询问其他问题。
   * 存储信息，并且不需要任何用户操作。

### <a name="enable-the-api-management-functionality"></a>启用 API 管理功能

在“委派”页上单击“委派产品订阅”。 

### <a name="create-your-delegation-endpoint"></a>创建委派终结点

创建新的委派终结点以在站点上实现的推荐步骤：

1. 接收以下形式的请求。
   
   > *http:\//www.yourwebsite.com/apimdelegation?operation={操作}&productId={要订阅的产品}&userId={提出请求的用户}&salt={字符串}&sig={字符串}*
   >
   
    产品订阅示例的查询参数：

   | 参数 | 说明 |
   | --------- | ----------- |
   | **operation** | 标识委派请求类型。 有效的产品订阅请求选项包括： <ul><li>Subscribe：请求为用户订阅具有所提供的 ID 的给定产品（见下）。</li><li>Unsubscribe：请求为用户取消某个产品的订阅。</li><li>Renew：请求续订某个订阅（例如即将到期的订阅）</li></ul> |
   | productId | 在“订阅”中，用户请求订阅的产品 ID。 |
   | **subscriptionId** | Unsubscribe 和 Renew 上的产品订阅。  |
   | **userId** | Subscribe 上发出请求的用户的 ID。 |
   | **加密盐** | 用于计算安全哈希的特殊加密盐字符串。 |
   | 信号 | 计算的安全哈希，用于与用户自行计算的哈希进行比较。 |

2. 验证请求是否来自 Azure API 管理（可选，但强烈推荐执行以确保安全）
   
   * 根据 **productId**、**userId** 和 **salt** 查询参数计算字符串的 HMAC-SHA512：
     ```
     HMAC(**salt** + '\n' + **productId** + '\n' + **userId**)
     ```
   * 将上面计算的哈希与 **sig** 查询参数的值进行比较。 如果两个哈希匹配，则继续进行下一步。 否则，拒绝请求。
3. 根据在 operation 中请求的操作类型（例如账单、更多问题等等）处理产品订阅。
4. 在这一端成功完成用户订阅后，即可通过[为订阅调用 REST API] 为用户订阅 API 管理产品。

## <a name="example-code"></a><a name="delegate-example-code"> </a> 示例代码

这些代码示例演示如何：

* 获取委派验证密钥，该密钥在发布服务器门户的“委派”屏幕中设置。
* 创建一个 HMAC 用于验证签名，以证明传递的 returnUrl 的有效性。

经过轻微修改后，可以将同样的代码用于 productId 和 userId。 

### <a name="c-code-to-generate-hash-of-returnurl"></a>用于生成 returnUrl 哈希的 C# 代码

```csharp
using System.Security.Cryptography;

string key = "delegation validation key";
string returnUrl = "returnUrl query parameter";
string salt = "salt query parameter";
string signature;
using (var encoder = new HMACSHA512(Convert.FromBase64String(key)))
{
    signature = Convert.ToBase64String(encoder.ComputeHash(Encoding.UTF8.GetBytes(salt + "\n" + returnUrl)));
    // change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
    // compare signature to sig query parameter
}
```

### <a name="nodejs-code-to-generate-hash-of-returnurl"></a>用于生成 returnUrl 哈希的 NodeJS 代码

```
var crypto = require('crypto');

var key = 'delegation validation key'; 
var returnUrl = 'returnUrl query parameter';
var salt = 'salt query parameter';

var hmac = crypto.createHmac('sha512', new Buffer(key, 'base64'));
var digest = hmac.update(salt + '\n' + returnUrl).digest();
// change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
// compare signature to sig query parameter

var signature = digest.toString('base64');
```

> [!IMPORTANT]
> 需要[重新发布开发人员门户](api-management-howto-developer-portal-customize.md#publish)才能使委派更改生效。

## <a name="next-steps"></a>后续步骤
- [详细了解开发人员门户。](api-management-howto-developer-portal.md)
- [使用 Azure AD](api-management-howto-aad.md) 或 [Azure AD B2C](api-management-howto-aad-b2c.md) 进行身份验证。
- 有更多关于开发人员门户的问题？ [在常见问题解答中寻找答案](developer-portal-faq.md)。

[Delegating developer sign-in and sign-up]: #delegate-signin-up
[Delegating product subscription]: #delegate-product-subscription
[请求共享访问令牌]: /rest/api/apimanagement/2020-12-01/user/get-shared-access-token
[创建用户]: /rest/api/apimanagement/2020-12-01/user/create-or-update
[调用订阅 REST API]: /rest/api/apimanagement/2020-12-01/subscription/create-or-update
[Next steps]: #next-steps
[示例代码]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png
