---
title: Azure 视频分析器访问策略
description: 本文介绍 Azure 视频分析器如何在访问策略中使用 JWT 令牌来保护视频。
ms.topic: reference
ms.date: 06/01/2021
ms.openlocfilehash: fe421e429357000bf6380cdf18f3a029fea4f7c9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128670474"
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

## <a name="creating-a-token"></a>创建令牌

在此部分，我们将创建稍后要在本文中使用的 JWT 令牌。  我们将使用一个会生成 JWT 令牌的示例应用程序，它会提供创建访问策略所需的所有字段。

> [!NOTE] 
> 如果熟悉如何基于 RSA 或 ECC 证书生成 JWT 令牌，可跳过此部分。

1. 克隆 [AVA C# 示例存储库](https://github.com/Azure-Samples/video-analyzer-iot-edge-csharp)。 然后，转到 JWTTokenIssuer 应用程序文件夹 src/jwt-token-issuer，找到 JWTTokenIssuer 应用程序。
2. 打开 Visual Studio Code，然后转到你下载了 JWTTokenIssuer 应用程序的文件夹。 此文件夹应包含 \*.csproj 文件。
3. 在“资源管理器”窗格中，转到 program.cs 文件。
4. 在第 77 行，将受众更改为视频分析器终结点，后跟 /videos/\*，使之看起来如下所示：

   ```
   https://{Azure Video Analyzer Account ID}.api.{Azure Long Region Code}.videoanalyzer.azure.net/videos/*
   ```

   > [!NOTE] 
   > 可以在 Azure 门户中的视频分析器资源的概述部分找到视频分析器终结点。

   :::image type="content" source="media/player-widget/client-api-url.png" alt-text="显示播放器小组件终结点的屏幕截图。":::
    
5. 在第 78 行将颁发者更改为证书的颁发者值。 示例：`https://contoso.com`
6. 保存文件。    

   > [!NOTE]
   > 系统可能会向你发出提示消息“`Required assets to build and debug are missing from 'jwt token issuer'. Add them?`”，请选择“`Yes`”。
   
   :::image type="content" source="media/player-widget/visual-studio-code-required-assets.png" alt-text="显示 Visual Studio Code 中所需资产提示的屏幕截图。":::
   
7. 打开命令提示符窗口，转到包含 JWTTokenIssuer 文件的文件夹。 运行以下两个命令：`dotnet build`，接着是 `dotnet run`。 如果你的 Visual Studio Code 上有 C# 扩展，还可以选择 F5 以运行 JWTTokenIssuer 应用程序。

然后会生成并执行应用程序。 生成应用程序后，它会创建一个自签名证书，并从该证书生成 JWT 令牌信息。 你还可以运行 JWTTokenIssuer.exe 文件，此文件位于生成了 JWTTokenIssuer 的目录的调试文件夹中。 运行应用程序的好处是可以指定输入选项，如下所示：

- `JwtTokenIssuer [--audience=<audience>] [--issuer=<issuer>] [--expiration=<expiration>] [--certificatePath=<filepath> --certificatePassword=<password>]`

JWTTokenIssuer 会创建 JWT 令牌和以下所需组件：

- `Issuer`, `Audience`, `Key Type`, `Algorithm`, `Key Id`, `RSA Key Modulus`, `RSA Key Exponent`, `Token`

请务必复制这些值供以后使用。


## <a name="creating-an-access-policy"></a>创建访问策略

创建访问策略的方法有两种。

### <a name="in-the-azure-portal"></a>在 Azure 门户中

1. 登录到 Azure 门户，然后转到视频分析器帐户所在的资源组。
1. 选择视频分析器资源。
1. 在“视频分析器”下，选择“访问策略” 。

   :::image type="content" source="./media/player-widget/portal-access-policies.png" alt-text="播放器小组件 - 门户访问策略。":::
   
1. 选择“新建”，然后输入以下信息：

   > [!NOTE] 
   > 这些值来自在上一步创建的 JWTTokenIssuer 应用程序。

   - 访问策略名称 - 任何名称

   - 颁发者 - 必须与 JWT 令牌颁发者匹配 

   - 受众 - JWT 令牌的受众 -- `${System.Runtime.BaseResourceUrlPattern}` 为默认值。

   - 密钥类型 - RSA 

   - 算法 - 支持的值为 RS256、RS384、RS512

   - 密钥 ID - 从证书生成。 有关详细信息，请参阅[创建令牌](#creating-a-token)。

   - RSA 密钥模数 - 从证书生成。 有关详细信息，请参阅[创建令牌](#creating-a-token)。

   - RSA 密钥指数 - 从证书生成。 有关详细信息，请参阅[创建令牌](#creating-a-token)。

   :::image type="content" source="./media/player-widget/access-policies-portal.png" alt-text="播放器小组件 - 访问策略门户"::: 
   
1. 选择“保存”。
### <a name="create-access-policy-via-api"></a>通过 API 创建访问策略

请参阅 Azure 资源管理器 (ARM) API 

## <a name="next-steps"></a>后续步骤

[概述](overview.md)
