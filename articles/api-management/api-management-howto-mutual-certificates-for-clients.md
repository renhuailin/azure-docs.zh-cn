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
ms.date: 01/13/2020
ms.author: apimpm
ms.openlocfilehash: 553b4527796db3e5d0f430afd6c5e614626187e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "99988893"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>如何使用 API 管理中的客户端证书身份验证确保 API 安全

API 管理提供的功能可确保使用客户端证书安全地访问 API（即，客户端到 API 管理）。 可以使用策略表达式验证传入证书并根据所需值检查证书属性。

若要了解如何使用客户端证书保护对 API 后端服务的访问（即，从 API 管理到后端），请参阅[如何使用客户端证书身份验证保护后端服务](./api-management-howto-mutual-certificates.md)

> [!IMPORTANT]
> 若要在开发人员层、基本层、标准层或高级层中通过 HTTP/2 接收和验证客户端证书，必须在“自定义域”边栏选项卡上启用“协商客户端证书”设置，如下所示。

![协商客户端证书](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

> [!IMPORTANT]
> 若要在“消耗”层中接收并验证客户端证书，必须在“自定义域”边栏选项卡上启用“请求客户端证书”设置，如下所示。

![请求客户端证书](./media/api-management-howto-mutual-certificates-for-clients/request-client-certificate.png)

## <a name="checking-the-issuer-and-subject"></a>检查颁发者和使用者

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

## <a name="checking-the-thumbprint"></a>检查指纹

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

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>针对已上传到 API 管理的证书检查指纹

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

## <a name="certificate-validation-in-self-hosted-gateway"></a>自承载网关上的证书验证

默认 API 管理[自承载网关](self-hosted-gateway-overview.md)映像不支持使用上传到 API 管理实例的 [CA 根证书](api-management-howto-ca-certificates.md)验证服务器和客户端证书。 向自承载网关提供自定义证书的客户端可能会遇到响应慢的问题，因为证书吊销列表 (CRL) 验证可能需要较长时间才会在网关上超时。 

一种解决方法是，在运行网关时，可以将 PKI IP 地址配置为指向 localhost 地址 (127.0.0.1) 而不是 API 管理实例。 当网关尝试验证客户端证书时，这会导致 CRL 验证快速失败。 要配置网关，请添加 API 管理实例的 DNS 条目以解析到容器中 `/etc/hosts` 文件中的 localhost。 可以在网关部署过程中添加此条目：
 
* 对于 Docker 部署 - 请将 `--add-host <hostname>:127.0.0.1` 参数添加到 `docker run` 命令中。 有关详细信息，请参阅[将条目添加到容器主机文件](https://docs.docker.com/engine/reference/commandline/run/#add-entries-to-container-hosts-file---add-host)
 
* 对于 Kubernetes 部署 - 将 `hostAliases` 规范添加到 `myGateway.yaml` 配置文件。 有关详细信息，请参阅[使用主机别名向 Pod/etc/hosts 添加条目](https://kubernetes.io/docs/concepts/services-networking/add-entries-to-pod-etc-hosts-with-host-aliases/)。




## <a name="next-steps"></a>后续步骤

-   [如何使用客户端证书身份验证确保后端服务安全](./api-management-howto-mutual-certificates.md)
-   [如何上传证书](./api-management-howto-mutual-certificates.md)
