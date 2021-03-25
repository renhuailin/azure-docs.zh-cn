---
title: Azure Active Directory REST API - 使用 Fiddler 进行测试
description: 使用 Fiddler 测试 Azure 应用配置 REST API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 1142aa25212d87c5484963cda4e172df3d1fbafc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "96932602"
---
# <a name="test-the-azure-app-configuration-rest-api-using-fiddler"></a>使用 Fiddler 测试 Azure 应用配置 REST API

若要使用 [Fiddler](https://www.telerik.com/fiddler) 测试 REST API，需要在请求中包含[身份验证](./rest-api-authentication-hmac.md)所需的 HTTP 标头。 下面介绍了如何配置 Fiddler 以测试 REST API，自动生成身份验证标头：

1. 确保 TLS 1.2 是允许的协议：
    1. 访问“工具” > “选项” > “HTTPS”。
    1. 确保选中“解密 HTTPS 流量”。
    1. 在协议列表中，添加“tls1.2”（如果不存在）。
1. 在 Fiddler 中打开“Fiddler 脚本编辑器”或按“Ctrl-R”
1. 将以下代码添加到 `Handlers` 类中 `OnBeforeRequest` 函数之前

    ```js
    static function SignRequest(oSession: Session, credential: String, secret: String) {
        var utcNow = DateTimeOffset.UtcNow.ToString("r", System.Globalization.DateTimeFormatInfo.InvariantInfo);
        var contentHash = ComputeSHA256Hash(oSession.RequestBody);
        var stringToSign = oSession.RequestMethod.ToUpperInvariant() + "\n" + oSession.PathAndQuery + "\n" + utcNow +";" + oSession.hostname + ";" + contentHash;
        var signature = ComputeHMACHash(secret, stringToSign);

        oSession.oRequest.headers["x-ms-date"] = utcNow;
        oSession.oRequest.headers["x-ms-content-sha256"] = contentHash;
        oSession.oRequest.headers["Authorization"] = "HMAC-SHA256 Credential=" + credential + "&SignedHeaders=x-ms-date;host;x-ms-content-sha256&Signature=" + signature;
    }

    static function ComputeSHA256Hash(content: Byte[]) {
        var sha256 = System.Security.Cryptography.SHA256.Create();
        try {
            return Convert.ToBase64String(sha256.ComputeHash(content));
        }
        finally {
            sha256.Dispose();
        }
    }

    static function ComputeHMACHash(secret: String, content: String) {
        var hmac = new System.Security.Cryptography.HMACSHA256(Convert.FromBase64String(secret));
        try {
            return Convert.ToBase64String(hmac.ComputeHash(System.Text.Encoding.ASCII.GetBytes(content)));
        }
        finally {
            hmac.Dispose();
        }
    }
    ```

1. 在 `OnBeforeRequest` 函数的末尾添加以下代码。 按 TODO 注释的指示更新访问密钥。

    ```js
    if (oSession.isFlagSet(SessionFlags.RequestGeneratedByFiddler) &&
        oSession.hostname.EndsWith(".azconfig.io", StringComparison.OrdinalIgnoreCase)) {

        // TODO: Replace the following placeholders with your access key
        var credential = "<Credential>"; // Id
        var secret = "<Secret>"; // Value

        SignRequest(oSession, credential, secret);
    }
    ```
1. 使用 [Fiddler 的编辑器](https://docs.telerik.com/fiddler/Generate-Traffic/Tasks/CreateNewRequest)生成并发送请求
