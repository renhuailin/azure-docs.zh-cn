---
title: 使用 API 管理中的客户端证书身份验证确保 API 安全
titleSuffix: Azure API Management
description: 了解如何使用客户端证书保护对 API 的访问。 你可以使用策略表达式来验证传入证书。
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/01/2021
ms.author: apimpm
ms.openlocfilehash: 6faa78dc75b67afd4c310a94ce4a81129e09cb26
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121742751"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>如何使用 API 管理中的客户端证书身份验证确保 API 安全

API 管理提供的功能可确保使用客户端证书安全地访问 API（即，客户端到 API 管理）。 你可以验证连接客户端提供的证书，并使用策略表达式，对照所需的值检查证书属性。

若要了解如何使用客户端证书保护对 API 后端服务的访问（即，从 API 管理到后端），请参阅[如何使用客户端证书身份验证保护后端服务](./api-management-howto-mutual-certificates.md)

> [!IMPORTANT]
> 若要在开发人员层、基本层、标准层或高级层中通过 HTTP/2 接收和验证客户端证书，必须在“自定义域”边栏选项卡上启用“协商客户端证书”设置，如下所示。

![协商客户端证书](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

> [!IMPORTANT]
> 若要在“消耗”层中接收并验证客户端证书，必须在“自定义域”边栏选项卡上启用“请求客户端证书”设置，如下所示。

![请求客户端证书](./media/api-management-howto-mutual-certificates-for-clients/request-client-certificate.png)

## <a name="policy-to-validate-client-certificates"></a>用于客户端证书的策略

使用 [validate-client-certificate](api-management-access-restriction-policies.md#validate-client-certificate) 策略验证客户端证书的一个或多个属性，该客户端证书用于访问托管在 API 管理 API 实例中的 API。

配置策略以验证一个或多个属性，包括证书颁发者、主题、指纹、是否根据联机吊销列表验证证书等。

有关详细信息，请参阅访问 [API 管理访问限制策略](api-management-access-restriction-policies.md)。

## <a name="certificate-validation-with-context-variables"></a>使用上下文变量的证书验证

还可使用 [`context` 变量](api-management-policy-expressions.md#ContextVariables)创建策略表达式以检查客户端证书。 以下各节中的示例显示了使用 `context.Request.Certificate` 属性和其他 `context` 属性的表达式。

> [!IMPORTANT]
> 从 2021 年 5 月开始，仅当 API 管理实例的 [`hostnameConfiguration`](/rest/api/apimanagement/2020-12-01/api-management-service/create-or-update#hostnameconfiguration) 将 `negotiateClientCertificate` 属性设置为 True 时，`context.Request.Certificate` 属性才会请求证书。 `negotiateClientCertificate` 默认设置为 false。

### <a name="checking-the-issuer-and-subject"></a>检查颁发者和使用者

可以将以下策略配置为检查客户端证书的颁发者和使用者：

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Issuer != "trusted-issuer" || context.Request.Certificate.SubjectName.Name != "expected-subject-name")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> 若要禁止检查证书吊销列表，请使用 `context.Request.Certificate.VerifyNoRevocation()` 而不是 `context.Request.Certificate.Verify()`。
> 如果客户端证书是自签名证书，则必须将根（或中间）CA 证书[上传](api-management-howto-ca-certificates.md)到 API 管理，`context.Request.Certificate.Verify()` 和 `context.Request.Certificate.VerifyNoRevocation()` 才能正常工作。

### <a name="checking-the-thumbprint"></a>检查指纹

可以将以下策略配置为检查客户端证书的指纹：

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Thumbprint != "DESIRED-THUMBPRINT-IN-UPPER-CASE")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> 若要禁止检查证书吊销列表，请使用 `context.Request.Certificate.VerifyNoRevocation()` 而不是 `context.Request.Certificate.Verify()`。
> 如果客户端证书是自签名证书，则必须将根（或中间）CA 证书[上传](api-management-howto-ca-certificates.md)到 API 管理，`context.Request.Certificate.Verify()` 和 `context.Request.Certificate.VerifyNoRevocation()` 才能正常工作。

### <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>针对已上传到 API 管理的证书检查指纹

以下示例演示如何针对已上传到 API 管理的证书，检查客户端证书的指纹：

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify()  || !context.Deployment.Certificates.Any(c => c.Value.Thumbprint == context.Request.Certificate.Thumbprint))" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>

```

> [!NOTE]
> 若要禁止检查证书吊销列表，请使用 `context.Request.Certificate.VerifyNoRevocation()` 而不是 `context.Request.Certificate.Verify()`。
> 如果客户端证书是自签名证书，则必须将根（或中间）CA 证书[上传](api-management-howto-ca-certificates.md)到 API 管理，`context.Request.Certificate.Verify()` 和 `context.Request.Certificate.VerifyNoRevocation()` 才能正常工作。

> [!TIP]
> 本[文](https://techcommunity.microsoft.com/t5/Networking-Blog/HTTPS-Client-Certificate-Request-freezes-when-the-Server-is/ba-p/339672)中所述的客户端证书死锁问题可以通过多种方式表现出来，例如：请求冻结、请求在超时后生成 `403 Forbidden` 状态代码、`context.Request.Certificate` 为 `null`。 此问题通常会影响内容长度约为 60KB 或更大的 `POST` 和 `PUT` 请求。
> 若要防止出现此问题，请在“自定义域”边栏选项卡上为所需主机名启用“协商客户端证书”设置，如本文档的第一个图像所示。 在“消耗”层中，此功能不可用。

## <a name="next-steps"></a>后续步骤

-   [如何使用客户端证书身份验证确保后端服务安全](./api-management-howto-mutual-certificates.md)
-   [如何上传证书](./api-management-howto-mutual-certificates.md)
