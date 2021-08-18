---
title: Azure 视频分析器访问策略
description: 本文介绍 Azure 视频分析器如何在访问策略中使用 JWT 令牌来保护视频。
ms.topic: reference
ms.date: 06/01/2021
ms.openlocfilehash: 3cf450249567d07bf6855d115a0e39640074eeb0
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2021
ms.locfileid: "114604193"
---
# <a name="access-policies"></a>访问策略

访问策略定义对给定视频分析器视频资源的访问的权限和持续时间。 这些访问策略允许第三方客户端（非 AAD 客户端）JWT 令牌向启用以下访问权限的客户端 API 提供授权，从而提高控制力度和灵活性： 

- 对视频元数据的访问权限。 
- 对视频流的访问权限。 

## <a name="access-policy-definition"></a>访问策略定义

```json
"name": "accesspolicyname1", 
"properties": { 
    "role": "Reader", 
    "authentication": { 
        "@type": "#Microsoft.VideoAnalyzer.JwtAuthentication", 
        "issuers": [ 
            "issuer1", 
            "issuer2" 
        ], 
        "audiences": [ 
            "audience1" 
        ], 
        "claims": [ 
            { 
                "name":"claimname1", 
                "value":"claimvalue1" 
            }, 
            { 
                "name":"claimname2", 
                "value":"claimvalue2" 
            } 
        ], 
        "keys": [ 
            { 
                "@type": "#Microsoft.VideoAnalyzer.RsaTokenKey", 
                "alg": "RS256", 
                "kid": "123", 
                "n": "YmFzZTY0IQ==", 
                "e": "ZLFzZTY0IQ==" 
            }, 
            { 
                "@type": "#Microsoft.VideoAnalyzer.EccTokenKey", 
                "alg": "ES256", 
                "kid": "124", 
                "x": "XX==", 
                "y": "YY==" 
            } 
        ] 
    } 
} 
```

> [!NOTE] 
> 只需要一种密钥类型。 

### <a name="roles"></a>角色

目前仅支持读者角色。

### <a name="issuer-matching-rules"></a>颁发者匹配规则

可以在策略中指定多个颁发者，但在令牌中只能指定单个颁发者。  如果令牌颁发者是策略中指定的颁发者之一，则颁发者匹配。

### <a name="audience-matching-rules"></a>受众匹配规则

如果视频资源的受众值为 ${System.Runtime.BaseResourceUrlPattern}，则 JWT 令牌中提供的受众必须与资源基 URL 匹配。 否则，令牌受众必须与访问策略中的受众匹配。

### <a name="claims-matching-rules"></a>声明匹配规则

可以在访问策略和 JWT 令牌中指定多个声明。  必须在令牌中提供访问策略中的所有声明才能通过验证，但是，JWT 令牌可以具有访问策略中未列出的其他声明。

### <a name="keys"></a>键

支持的两种密钥类型是 RSA 和 ECC。

[RSA](https://wikipedia.org/wiki/RSA_(cryptosystem))

* @type- \#Microsoft.VideoAnalyzer.RsaTokenKey
* alg - 算法。  可以是 256、384 或 512 
* kid - 密钥 ID
* n - 模
* e - 公开指数 

[ECC](https://wikipedia.org/wiki/Elliptic-curve_cryptography)        

* @type- \#Microsoft.VideoAnalyzer.EccTokenKey
* alg - 算法。  可以是 256、384 或 512
* kid - 密钥 ID
* x - 坐标值。
* y - 坐标值。

### <a name="token-validation-process"></a>令牌验证过程

客户必须创建自己的 JWT 令牌，并将使用以下方法进行验证：

- 根据与密钥 ID 匹配的策略的列表，我们可以验证：
  - 令牌签名
  - 令牌过期
  - 颁发者
  - 目标受众
  - 其他声明

### <a name="policy-audience-and-token-matching-examples"></a>策略受众和令牌匹配示例：

| 策略受众                      | 请求的 URL                         | 令牌 URL                            | 结果 |
| ---------------------------------------- | ------------------------------------- | ------------------------------------ | ------ |
| （任意文本）                            | （任何）                                 | （匹配）                              | 授予  |
| （任意文本）                            | （任何）                                 | （不匹配）                          | 拒绝   |
| ${System.Runtime.BaseResourceUrlPattern} | https://fqdn/videos                   | https://fqdn/videos/*                | 授予  |
| ${System.Runtime.BaseResourceUrlPattern} | https://fqdn/videos                   | https://fqdn/videos/{videoName}      | 拒绝   |
| ${System.Runtime.BaseResourceUrlPattern} | https://fqdn/videos/{videoName}       | https://fqdn/vid*                    | 授予  |
| ${System.Runtime.BaseResourceUrlPattern} | https://fqdn/videos/{videoName}       | https://fqdn/videos/*                | 授予  |
| ${System.Runtime.BaseResourceUrlPattern} | https://fqdn/videos/{videoName}       | https://fqdn/videos/{baseVideoName}* | 授予  |
| ${System.Runtime.BaseResourceUrlPattern} | https://fqdn/videos/{videoName}       | https://fqdn/videos/{videoName}      | 授予  |
| ${System.Runtime.BaseResourceUrlPattern} | https://fqdn/videos/{videoName}Suffix | https://fqdn/videos/{videoName}      | 拒绝   |
| ${System.Runtime.BaseResourceUrlPattern} | https://fqdn/videos/{otherVideoName}  | https://fqdn/videos/{videoName}      | 拒绝   |

> [!NOTE]  
> 视频分析器最多支持 20 个策略。  通过 ${System.Runtime.BaseResourceUrlPattern}，可以使用一个访问策略和多个令牌更灵活地访问特定资源。  然后，这些令牌根据受众允许访问不同的视频分析器资源。 

## <a name="creating-an-access-policy"></a>创建访问策略

创建访问策略的方法有两种。

### <a name="in-the-azure-portal"></a>在 Azure 门户中

1. 登录到 Azure 门户，然后导航到视频分析器帐户所在的资源组。
2. 选择视频分析器资源。
3. 在“视频分析器”下，选择“访问策略”

   :::image type="content" source="./media/access-policies/access-policies-menu.png" alt-text="Azure 门户中的“访问策略”菜单":::
4. 单击“新建”并输入以下内容：

   - 访问策略名称 - 任何名称
   - 颁发者 - 必须与 JWT 令牌颁发者匹配 
   - 受众 - JWT 令牌的受众 - 默认为 ${System.Runtime.BaseResourceUrlPattern}。 
   - 密钥类型 - kty 
   - 算法 - alg
   - 密钥 ID - kid 
   - N/X 值 
   - E/Y 值 

   :::image type="content" source="./media/access-policies/access-policies-portal.png" alt-text="Azure 门户中的“访问策略”":::
5. 单击 `Save`。

### <a name="create-access-policy-via-api"></a>通过 API 创建访问策略

请参阅 Azure 资源管理器 (ARM) API 

## <a name="next-steps"></a>后续步骤

[概述](overview.md)
