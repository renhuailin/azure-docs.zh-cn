---
title: 排查 Azure 应用程序网关上的相互身份验证问题
description: 了解如何排查应用程序网关上的相互身份验证问题
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 04/02/2021
ms.author: caya
ms.openlocfilehash: 623addd253b3eb28bdf70db02ddfbe4b320cbae7
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2021
ms.locfileid: "106230819"
---
# <a name="troubleshooting-mutual-authentication-errors-in-application-gateway-preview"></a>排查 Azure 应用程序网关中的相互身份验证错误（预览版）

了解如何排查使用应用程序网关时出现的相互身份验证问题。 

## <a name="overview"></a>概述 

在应用程序网关上配置相互身份验证后，尝试使用相互身份验证时，可能会出现一些错误。 出现错误的一些常见原因包括：

* 上传了没有根 CA 证书的证书或证书链
* 上传了具有多个根 CA 证书的证书链
* 上传了只包含叶证书而没有 CA 证书的证书链 
* 因颁发者 DN 不匹配而发生验证错误  

我们将会讲解你可能遇到的各种不同情况，以及如何解决这些情况。 接下来，我们将会列举使用相互身份验证时可能出现的错误代码，并解释出现特定错误代码的可能原因。 

## <a name="scenario-troubleshooting---configuration-problems"></a>情况排查 - 配置问题
尝试配置相互身份验证时，你可能会遇到几种情况。 我们将逐步说明如何排查一些最常见的问题。 

### <a name="self-signed-certificate"></a>自签名证书

#### <a name="problem"></a>问题 

上传的客户端证书是自签名证书，导致出现错误代码 ApplicationGatewayTrustedClientCertificateDoesNotContainAnyCACertificate。

#### <a name="solution"></a>解决方案 

仔细检查所用的自签名证书是否具有扩展 BasicConstraintsOid = "2.5.29.19"（指示使用者可充当 CA）。 这可以确保使用的证书是 CA 证书。 有关如何生成自签名客户端证书的详细信息，请查看[受信任的客户端证书](./mutual-authentication-certificate-management.md)。

## <a name="scenario-troubleshooting---connectivity-problems"></a>情况排查 - 连接问题

你可能已顺利地配置相互身份验证而未出现任何问题，但在向应用程序网关发送请求时却遇到了问题。 我们将在下一部分列举一些常见问题并提供其解决方法。 可以在应用程序网关的访问日志中找到 sslClientVerify 属性。 

### <a name="sslclientverify-is-none"></a>SslClientVerify 为 NONE

#### <a name="problem"></a>问题 

在访问日志中，属性 sslClientVerify 显示为“NONE”。 

#### <a name="solution"></a>解决方案 

如果客户端在向应用程序网关发送请求时未发送客户端证书，则会出现此问题。 如果向应用程序网关发送请求的客户端未正确配置为使用客户端证书，则可能会发生此情况。 验证应用程序网关上的客户端身份验证设置是否按预期方式工作的方法之一是运行以下 OpenSSL 命令：

```
openssl s_client -connect <hostname:port> -cert <path-to-certificate> -key <client-private-key-file> 
```

`-cert` 标志为叶证书，`-key` 标志为客户端私钥文件。 

有关如何使用 OpenSSL `s_client` 命令的详细信息，请查看其[手册页](https://www.openssl.org/docs/man1.0.2/man1/openssl-s_client.html)。

### <a name="sslclientverify-is-failed"></a>SslClientVerify 为 FAILED

#### <a name="problem"></a>问题

在访问日志中，属性 sslClientVerify 显示为“FAILED”。 

#### <a name="solution"></a>解决方案

有多个潜在原因会导致在访问日志中记录失败。 下面是常见的失败原因列表：
* 无法获取颁发者证书：找不到客户端证书的颁发者证书。 这通常意味着，应用程序网关中的受信任客户端 CA 证书链不完整。 验证上传到应用程序网关中的受信任客户端 CA 证书链是否完整。  
* 无法获取本地颁发者证书：类似于无法获取颁发者证书，客户端证书的颁发者证书无法找到。 这通常意味着，应用程序网关中的受信任客户端 CA 证书链不完整。 验证上传到应用程序网关中的受信任客户端 CA 证书链是否完整。
* 无法验证第一个证书：无法验证客户端证书。 具体而言，当客户端仅提供颁发者不受信任的叶证书时，就会发生此错误。 验证上传到应用程序网关中的受信任客户端 CA 证书链是否完整。
* 无法验证客户端证书颁发者：当 VerifyClientCertIssuerDN 配置设置为 true 时，将发生此错误。 当客户端证书的颁发者 DN 与从客户上传的受信任客户端 CA 证书链中提取的 ClientCertificateIssuerDN 不匹配时，通常会发生此错误。 有关应用程序网关如何提取 ClientCertificateIssuerDN 的详细信息，请查看[应用程序网关提取颁发者 DN](./mutual-authentication-overview.md#verify-client-certificate-dn)。 最佳做法是，确保以每个文件一个证书链的方式将证书链上传到应用程序网关。 

有关如何提取要上传到应用程序网关的整个受信任客户端 CA 证书链的详细信息，请参阅[如何提取受信任客户端 CA 证书链](./mutual-authentication-certificate-management.md)。

## <a name="error-code-troubleshooting"></a>错误代码排查
如果你看到以下任何错误代码，可以参考我们建议的解决方法来帮助解决可能遇到的问题。 

### <a name="error-code-applicationgatewaytrustedclientcertificatemustspecifydata"></a>错误代码：ApplicationGatewayTrustedClientCertificateMustSpecifyData

#### <a name="cause"></a>原因

缺少证书数据。 上传的证书可能是不包含任何证书数据的空文件。 

#### <a name="solution"></a>解决方案

验证上传的证书文件是否不缺失任何数据。 

### <a name="error-code-applicationgatewaytrustedclientcertificatemustnothaveprivatekey"></a>错误代码：ApplicationGatewayTrustedClientCertificateMustNotHavePrivateKey

#### <a name="cause"></a>原因

证书链中包含私钥。 证书链中不应包含私钥。 

#### <a name="solution"></a>解决方案

仔细检查上传的证书链，并删除该链中包含的私钥。 重新上传不包含私钥的链。 

### <a name="error-code-applicationgatewaytrustedclientcertificateinvaliddata"></a>错误代码：ApplicationGatewayTrustedClientCertificateInvalidData

#### <a name="cause"></a>原因

出现此错误代码的潜在原因有两个。
1. 由于未以正确的格式提供链，因此分析已失败。 应用程序网关要求以 PEM 格式提供证书链，并要求分隔各项证书数据。 
2. 分析程序找不到任何可分析的内容。 上传的文件可能仅包含分隔符，而不包含证书数据。 

#### <a name="solution"></a>解决方案

根据此错误的原因，有两种可能的解决方法。 
* 验证上传的证书链是否采用正确的格式 (PEM)，并验证是否已正确分隔证书数据。 
* 检查上传的证书文件是否除了包含分隔符以外，还包含证书数据。 

### <a name="error-code-applicationgatewaytrustedclientcertificatedoesnotcontainanycacertificate"></a>错误代码：ApplicationGatewayTrustedClientCertificateDoesNotContainAnyCACertificate

#### <a name="cause"></a>原因

上传的证书只包含叶证书，而没有 CA 证书。 上传包含 CA 证书和叶证书的证书链是可接受的，因为叶证书会直接被忽略，但证书必须具有 CA。 

#### <a name="solution"></a>解决方案 

仔细检查上传的证书链是否不只是包含叶证书。 BasicConstraintsOid = "2.5.29.19" 扩展应存在，并指示使用者可充当 CA。

### <a name="error-code-applicationgatewayonlyonerootcaallowedintrustedclientcertificate"></a>错误代码：ApplicationGatewayOnlyOneRootCAAllowedInTrustedClientCertificate

#### <a name="cause"></a>原因

证书链包含多个根 CA 证书，或包含零个根 CA 证书。 

#### <a name="solution"></a>解决方案

上传的证书只能包含一个根 CA 证书（但可根据需要包含多个中间 CA 证书）。


