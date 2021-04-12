---
title: 获取服务访问令牌
description: 介绍如何创建用于访问 ARR REST API 的令牌
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9721685fc3ccd2c1c80b55e9118d6d347cc97a9c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "97830694"
---
# <a name="get-service-access-tokens"></a>获取服务访问令牌

仅向授权用户授予对 ARR REST API 的访问权限。 若要证明授权，必须将访问令牌与 REST 请求一起发送。 这些令牌由安全令牌服务 (STS) 颁发，用于交换帐户密钥。 令牌的生存期为 24 小时，因此可以将其颁发给用户，而无需授予其对服务的完全访问权限。

本文介绍如何创建这类访问令牌。

## <a name="prerequisites"></a>先决条件

[创建 ARR 帐户](create-an-account.md)（如果尚无）。

## <a name="token-service-rest-api"></a>令牌服务 REST API

为创建访问令牌，安全令牌服务提供单个 REST API。 STS 服务的 URL 取决于远程渲染帐户的帐户域。 其形式为 https://sts.[账户域]，例如 `https://sts.southcentralus.mixedreality.azure.com`

### <a name="get-token-request"></a>“获取令牌”请求

| URI | 方法 |
|-----------|:-----------|
| /accounts/**accountId**/token | GET |

| 标头 | 值 |
|--------|:------|
| 授权 | "Bearer **accountId**:**accountKey**" |

将 accountId 和 accountKey 替换为相应的数据。

### <a name="get-token-response"></a>“获取令牌”响应

| 状态代码 | JSON 有效负载 | 注释 |
|-----------|:-----------|:-----------|
| 200 | AccessToken: string | 成功 |

| 标头 | 用途 |
|--------|:------|
| MS-CV | 此值可用于跟踪服务中的调用 |

## <a name="getting-a-token-using-powershell"></a>使用 PowerShell 获取令牌

下面的 PowerShell 代码演示如何向 STS 发送必要的 REST 请求。 之后，它会将令牌打印到 PowerShell 提示符。

```PowerShell
$accountId = "<account_id_from_portal>"
$accountKey = "<account_key_from_portal>"
$accountDomain = "<account_domain_from_portal>

[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;
$webResponse = Invoke-WebRequest -Uri "https://sts.$accountDomain/accounts/$accountId/token" -Method Get -Headers @{ Authorization = "Bearer ${accountId}:$accountKey" }
$response = ConvertFrom-Json -InputObject $webResponse.Content

Write-Output "Token: $($response.AccessToken)"
```

此脚本只会将令牌打印到输出中，便于从中复制和粘贴它。 对于实际项目，应自动执行此过程。

## <a name="next-steps"></a>后续步骤

* [PowerShell 脚本示例](../samples/powershell-example-scripts.md)
* [Azure 前端 API](../how-tos/frontend-apis.md)
* [会话管理 REST API](../how-tos/session-rest-api.md)
