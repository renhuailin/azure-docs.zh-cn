---
title: Azure Fluid Relay 令牌协定
description: 更好地了解 Azure Fluid Relay 中使用的 JSON Web 令牌
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: reference
ms.service: azure-fluid
ms.openlocfilehash: 3fe063a6d5b3c76d300a088372e9dee08d9d79c0
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661560"
---
# <a name="azure-fluid-relay-token-contract"></a>Azure Fluid Relay 令牌协定

> [!NOTE]
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。

发送到 Azure Fluid Relay 的请求应在授权标头中包含 JWT 令牌。 此令牌应[由租户密钥签名](../concepts/authentication-authorization.md)。

## <a name="claims"></a>声明

JWT（JSON Web 令牌）分为三部分： 

- **标头** - 提供有关如何验证令牌的信息，包括有关令牌的类型及其签名方式的信息。 
- **有效负载** - 包含有关正在尝试调用你的服务的用户或应用的所有重要数据。 
- **签名** - 用于验证令牌的原始材料。 

每个部分由句点 (.) 分隔，并分别采用 Base64 编码。 

## <a name="header-claims"></a>标头声明

| 声明 | 格式 | 说明  |
|-------|--------|--------------|
| alg   | string | 用于签署令牌的算法。 例如，“HS256” |
| typ   | string | 此值应始终为“JWT”。 |

## <a name="payload-claims"></a>有效负载声明

| 声明      | 格式                   | 说明 |
|------------|--------------------------|-------------|
| documentId | string                   | 由 FRS 生成，用于标识要生成令牌的文档。 |
| scope      | string[]                 | 标识客户端对文档或摘要所需的权限。 对于每个范围，可以定义要授予客户端的权限。  |
| tenantId   | string                   | 标识租户。 |
| user       | JSON                     | 可选 `{ displayName: <display_name>, id: <user_id>, name: <user_name>, }` 标识应用程序的用户。 此声明由排序服务 Alfred 发送回应用程序。  应用程序可以使用它通过从 Alfred 获得的响应标识用户。 Azure Fluid Relay 不会验证此信息。 |
| iat        | 数字，UNIX 时间戳 | “Issued At”表示针对此令牌进行身份验证的时间。 |
| exp        | 数字，UNIX 时间戳 | “exp”（过期时间）声明指定只能在哪个时间（含）之前接受 JWT 的处理。 令牌生存期不能超过 1 小时。 |
| ver        | string                   | 指示访问令牌的版本。 必须为 `1.0`。 |
| jti        | string                   | *可选。*  “jti”(JWT ID) 声明为 JWT 提供唯一标识符。 标识符值的赋值方式必须确保几乎不会将相同的值意外分配给不同的数据对象。 建议你使用此声明，以避免创建文档时因使用相同的令牌而失败。  |

## <a name="a-sample-azure-fluid-relay-token"></a>Azure Fluid Relay 令牌示例

```typescript
{ 
  "alg": "HS256",  
  "typ": "JWT" 
}.{ 
  "documentId": "746c4a6f-f778-4970-83cd-9e21bf88326c", 
  "scopes": [ "doc:read", "doc:write", "summary:write" ],   
  "iat": 1599098963,  
  "exp": 1599098963,  
  "tenantId": "AzureFluidTenantId",  
  "ver": "1.0",
  "jti": "d7cd6602-2179-11ec-9621-0242ac130002"
}.[Signature] 
```

## <a name="how-can-you-generate-an-azure-fluid-relay-token"></a>如何生成 Azure Fluid Relay 令牌？ 

你可以使用 [jsonwebtoken](https://www.npmjs.com/package/jsonwebtoken) npm 包并使用此方法签署令牌。

```typescript
export function getSignedToken(
    tenantId: string,
    documentId: string,
    tokenLifetime: number = 60 * 60,
    ver: string = "1.0") {
        jwt.sign(
            {
                documentId, 
                user: {
                    displayName: "displayName", 
                    id: "userId", 
                    name: "userName" 
                }, 
                scopes: ["doc:read", "doc:write", "summary:write"], 
                iat: Math.round((new Date()).getTime() / 1000), 
                exp: Math.round((new Date()).getTime() / 1000) + tokenLifetime, //set the expiry date based on your needs but max-limit is one hour.
                tenantId, 
                ver,
                jti: uuid(), 
            },
            "<tenant_key>");
    }
```
