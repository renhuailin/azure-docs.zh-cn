---
title: 配置来电的客户端身份验证 - Azure 事件网格 IoT Edge | Microsoft Docs
description: 了解事件网格模块的可能的客户端身份验证配置。
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 9525da4204e270d033f0c2354318bd71874eaf54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "91290766"
---
# <a name="configure-client-authentication-of-incoming-calls"></a>配置来电的客户端身份验证

本指南举例说明事件网格模块的可能的客户端身份验证配置。 事件网格模块支持两种类型的客户端身份验证：

* 基于共享访问签名 (SAS) 密钥
* 基于证书

有关所有可能的配置，请参阅[安全性和身份验证](security-authentication.md)指南。

## <a name="enable-certificate-based-client-authentication-no-self-signed-certificates"></a>启用基于证书的客户端身份验证，不使用自签名证书

```json
 {
  "Env": [
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=false"
  ]
}
 ```

## <a name="enable-certificate-based-client-authentication-allow-self-signed-certificates"></a>启用基于证书的客户端身份验证，允许使用自签名证书

```json
 {
  "Env": [
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true"
  ]
}
```

>[!NOTE]
>仅在测试环境中将 inbound__clientAuth__clientCert__allowUnknownCA 属性设置为 true，因为通常可能会使用自签名证书 。 对于生产工作负载，我们建议将此属性设置为 false，并使用证书颁发机构 (CA) 颁发的证书。

## <a name="enable-certificate-based-and-sas-key-based-client-authentication"></a>启用基于证书和基于 SAS 密钥的客户端身份验证

```json
 {
  "Env": [
    "inbound__clientAuth__sasKeys__enabled=true",
    "inbound__clientAuth__sasKeys__key1=<some-secret1-here>",
    "inbound__clientAuth__sasKeys__key2=<some-secret2-here>",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>基于 SAS 密钥的客户端身份验证允许非 IoT Edge 模块执行管理和运行时操作，当然前提是可以在 IoT Edge 网络外部访问 API 端口。
