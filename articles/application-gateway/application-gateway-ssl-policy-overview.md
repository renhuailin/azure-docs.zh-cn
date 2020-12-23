---
title: Azure 应用程序网关的 TLS 策略概述
description: 了解如何为 Azure 应用程序网关配置 TLS 策略，并减少后端服务器场的加密和解密开销。
services: application gateway
author: amsriva
ms.service: application-gateway
ms.topic: conceptual
ms.date: 12/17/2020
ms.author: amsriva
ms.openlocfilehash: 77239cd8586b8fb07abf6862be436979541bdb99
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/17/2020
ms.locfileid: "97631684"
---
# <a name="application-gateway-tls-policy-overview"></a>应用程序网关 TLS 策略概述

应用程序网关允许用户集中 TLS/SSL 证书管理，并减少后端服务器场的加密和解密开销。 这种集中式 TLS 处理还允许指定适合组织安全要求的中央 TLS 策略。 这有助于满足符合性要求以及安全指南和建议做法。

TLS 策略包括 TLS 协议版本控制和 TLS 握手期间会使用的密码套件和优先级顺序。 应用程序网关提供两种控制 TLS 策略的机制。 可使用预定义的策略或自定义策略。

## <a name="predefined-tls-policy"></a>预定义 TLS 策略

应用程序网关具有三种预定义的安全策略。 可以使用这些策略中的任意策略配置网关，以获得适当的安全性级别。 策略名称批注有其配置的年份和月份。 每个策略提供不同的 TLS 协议版本和密码套件。 建议使用最新的 TLS 策略来确保最佳的 TLS 安全性。

## <a name="known-issue"></a>已知问题
应用程序网关 v2 不支持以下 DHE 密码，它们不会用于与客户端建立 TLS 连接，即使这些密码已在预定义策略中提到。 建议不要使用 DHE 密码，而是建议使用安全快捷的 ECDHE 密码。

- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA

### <a name="appgwsslpolicy20150501"></a>AppGwSslPolicy20150501

|属性  |值  |
|---|---|
|名称     | AppGwSslPolicy20150501        |
|MinProtocolVersion     | TLSv1_0        |
|默认| True（如果未指定预定义策略） |
|CipherSuites     |TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_DHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_DHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_3DES_EDE_CBC_SHA<br>TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA |
  
### <a name="appgwsslpolicy20170401"></a>AppGwSslPolicy20170401
  
|属性  |值  |
|   ---      |  ---       |
|名称     | AppGwSslPolicy20170401        |
|MinProtocolVersion     | TLSv1_1        |
|默认| 错误 |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA |
  
### <a name="appgwsslpolicy20170401s"></a>AppGwSslPolicy20170401S

|属性  |值  |
|---|---|
|名称     | AppGwSslPolicy20170401S        |
|MinProtocolVersion     | TLSv1_2        |
|默认| 错误 |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 <br>    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 <br>    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br> |

## <a name="custom-tls-policy"></a>自定义 TLS 策略

如果需要根据要求配置预定义 TLS 策略，则必须定义自己的自定义 TLS 策略。 使用自定义 TLS 策略，可以完全控制要支持的最低 TLS 协议版本，以及支持的密码套件及其优先级顺序。

> [!IMPORTANT]
> 如果使用应用程序网关 v1 SKU (Standard 或 WAF) 中的自定义 SSL 策略，请确保将强制密码 "TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256" 添加到列表。 在应用程序网关 v1 SKU 中启用指标和日志记录需要此密码。
> 对于应用程序网关 v2 SKU (Standard_v2 或 WAF_v2) ，这不是必需的。
 
### <a name="tlsssl-protocol-versions"></a>TLS/SSL 协议版本

* 默认情况下，所有应用程序网关都禁用 SSL 2.0 和 3.0。 无法对这些协议版本进行配置。
* 自定义 TLS 策略允许你选择以下三种协议之一作为网关的最低 TLS 协议版本： TLSv1_0、TLSv1_1 和 TLSv1_2。
* 如果未定义 TLS 策略，则会启用 TLSv1_0、TLSv1_1 和 TLSv1_2)  (所有三个协议。

### <a name="cipher-suites"></a>密码套件

应用程序网关支持以下密码套件，你可以从中选择自定义策略。 密码套件的顺序决定了 TLS 协商期间的优先级顺序。


- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_3DES_EDE_CBC_SHA
- TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

> [!NOTE]
> 用于连接的 TLS 密码套件也基于所使用的证书类型。 在从客户端到应用程序网关的连接中，所使用的密码套件基于应用程序网关侦听器上的服务器证书的类型。 在从应用程序网关到后端池的连接中，所使用的密码套件基于后端池服务器上的服务器证书的类型。

## <a name="next-steps"></a>后续步骤

如果要了解如何配置 TLS 策略，请参阅 [在应用程序网关上配置 tls 策略版本和密码套件](application-gateway-configure-ssl-policy-powershell.md)。
