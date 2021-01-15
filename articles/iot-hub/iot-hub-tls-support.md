---
title: Azure IoT 中心 TLS 支持
description: 了解如何对与 IoT 中心通信的设备和服务使用安全 TLS 连接
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: jlian
ms.openlocfilehash: e569cbe9030b2ac5a42bd99233b4fefc925a5662
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2021
ms.locfileid: "98220298"
---
# <a name="transport-layer-security-tls-support-in-iot-hub"></a>IoT 中心中的传输层安全性 (TLS) 支持

IoT 中心使用传输层安全性 (TLS) 保护来自 IoT 设备和服务的连接。 目前支持三个版本的 TLS 协议，即版本 1.0、1.1 和 1.2。

TLS 1.0 和 1.1 被视为旧版，我们已计划弃用这两个版本。 有关详细信息，请参阅 [IoT 中心弃用 TLS 1.0 和 1.1](iot-hub-tls-deprecating-1-0-and-1-1.md)。 为了避免将来出现问题，请在连接到 IoT 中心时，使用 TLS 1.2 作为唯一的 TLS 版本。

## <a name="iot-hubs-server-tls-certificate"></a>IoT 中心的服务器 TLS 证书

在 TLS 握手期间，IoT 中心会提供 RSA 加密的服务器证书以连接客户端。 其根是巴尔的摩 Cybertrust 根 CA。 最近，我们推出了对 TLS 服务器证书的更改，使其现在由新的中间证书颁发机构 (ICA) 颁发。 有关详细信息，请参阅 [IoT 中心 TLS 证书更新](https://azure.microsoft.com/updates/iot-hub-tls-certificate-update/)。

### <a name="elliptic-curve-cryptography-ecc-server-tls-certificate-preview"></a>椭圆曲线加密 (ECC) 服务器 TLS 证书 (预览) 

IoT 中心 ECC 服务器 TLS 证书提供公共预览版。 虽然为 RSA 证书提供了类似的安全性，但使用仅 ECC 密码套件的 ECC 证书验证 () 使用的计算、内存和带宽最多可达40%。 这些节约对于 IoT 设备很重要，因为它的配置文件和内存更小，并支持网络带宽受限环境中的用例。 

预览 IoT 中心的 ECC 服务器证书：

1. [使用上的预览模式创建新的 IoT 中心](iot-hub-preview-mode.md)。
1. [将客户端配置](#tls-configuration-for-sdk-and-iot-edge) 为 *仅* 包含 ECDSA 密码套件并 *排除* 任何 RSA。 以下是 ECC 证书公共预览版支持的密码套件：
    - `TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256`
    - `TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384`
    - `TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256`
    - `TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384`
1. 将客户端连接到预览版 IoT 中心。

## <a name="tls-12-enforcement-available-in-select-regions"></a>所选区域中可用的 TLS 1.2 强制实施

为了增加安全性，请将 IoT 中心配置为 *仅* 允许使用 TLS 版本1.2 的客户端连接，并强制使用 [密码套件](#cipher-suites)。 仅在以下区域支持此功能：

* 美国东部
* 美国中南部
* 美国西部 2
* US Gov 亚利桑那州
* US Gov 弗吉尼亚州 (TLS 1.0/1.1 支持在此区域中不可用，则必须启用 TLS 1.2 强制，否则 IoT 中心创建会失败) 

若要启用 TLS 1.2 强制，请按照 [在 Azure 门户中创建 IoT 中心](/.iot-hub-create-through-portal.md)中的步骤操作，但

- 从上述列表中选择一个 **区域** 。
- 在 " **管理-> 高级-> 传输层安全 (tls) -> 最小 tls 版本**" 下，选择 **1.2**。 此设置仅在受支持的区域中创建的 IoT 中心出现。

    :::image type="content" source="media/iot-hub-tls-12-enforcement.png" alt-text="显示如何在 IoT 中心创建期间启用 TLS 1.2 强制的屏幕截图":::

若要使用 ARM 模板进行创建，请在任何受支持的区域中预配新的 IoT 中心，并 `minTlsVersion` `1.2` 在资源规范中将属性设置为：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Devices/IotHubs",
            "apiVersion": "2020-01-01",
            "name": "<provide-a-valid-resource-name>",
            "location": "<any-of-supported-regions-below>",
            "properties": {
                "minTlsVersion": "1.2"
            },
            "sku": {
                "name": "<your-hubs-SKU-name>",
                "tier": "<your-hubs-SKU-tier>",
                "capacity": 1
            }
        }
    ]
}
```

使用此配置创建的 IoT 中心资源会拒绝那些尝试使用 TLS 版本 1.0 和 1.1 进行连接的设备和服务客户端。 同样，如果 `ClientHello` 消息未列出任何 [建议的密码](#cipher-suites)，TLS 握手将被拒绝。

> [!NOTE]
> `minTlsVersion` 属性处于只读状态，创建 IoT 中心资源后，便不能再更改该属性。 因此，必须事先正确测试并验证你的所有 IoT 设备和服务是否与 TLS 1.2 和[建议的密码](#cipher-suites)兼容。
> 
> 故障转移后，IoT 中心的 `minTlsVersion` 属性在异地配对区域中仍然有效。

## <a name="cipher-suites"></a>密码套件

配置为仅接受 TLS 1.2 的 IoT 中心还将强制使用以下建议的密码套件：

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`

对于未配置 TLS 1.2 强制的 IoT 中心，TLS 1.2 仍适用于以下密码套件：

* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_DHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_DHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_RSA_WITH_AES_256_CBC_SHA256`
* `TLS_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_AES_256_CBC_SHA`
* `TLS_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_3DES_EDE_CBC_SHA`

客户端可以建议在过程中使用的更高密码套件的列表 `ClientHello` 。 但是，IoT 中心可能不支持其中某些 (例如 `ECDHE-ECDSA-AES256-GCM-SHA384`) 。 在这种情况下，IoT 中心将尝试按照客户端的喜好进行操作，但最终会将密码套件与进行协商 `ServerHello` 。

## <a name="tls-configuration-for-sdk-and-iot-edge"></a>SDK 和 IoT Edge 的 TLS 配置

使用以下链接在 IoT 中心客户端 SDK 中配置 TLS 1.2 和允许的密码。

| 语言 | 支持 TLS 1.2 的版本 | 文档 |
|----------|------------------------------------|---------------|
| C        | 标记 2019-12-11 或更新的标记            | [链接](https://aka.ms/Tls_C_SDK_IoT)。 |
| Python   | 版本 2.0.0 或更高版本             | [链接](https://aka.ms/Tls_Python_SDK_IoT)。 |
| C#       | 版本 1.21.4 或更高版本            | [链接](https://aka.ms/Tls_CSharp_SDK_IoT)。 |
| Java     | 版本 1.19.0 或更高版本            | [链接](https://aka.ms/Tls_Java_SDK_IoT)。 |
| NodeJS   | 版本 1.12.2 或更高版本            | [链接](https://aka.ms/Tls_Node_SDK_IoT)。 |

IoT Edge 设备可以配置为在与 IoT 中心通信时使用 TLS 1.2。 为此，请参阅 [IoT Edge 文档页](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md)。

## <a name="device-authentication"></a>设备身份验证

成功进行 TLS 握手后，IoT 中心可以使用对称密钥或 x.509 证书对设备进行身份验证。 对于基于证书的身份验证，这可以是任何 x.509 证书，包括 ECC。 IoT 中心根据你提供)  (CA 的指纹或证书颁发机构验证证书。 若要了解详细信息，请参阅 [支持的 x.509 证书](iot-hub-devguide-security.md#supported-x509-certificates)。

## <a name="tls-maximum-fragment-length-negotiation-preview"></a>TLS 最大片段长度协商 (预览) 

IoT 中心还支持 TLS 最大片段长度协商，这有时称为 TLS 帧大小协商。 此功能目前以公共预览版提供。 

使用此功能可将最大纯文本片段长度指定为小于默认 2 ^ 14 字节的值。 协商后，IoT 中心和客户端开始分段消息，以确保所有段都小于协商长度。 此行为对于计算或受内存限制的设备很有用。 若要了解详细信息，请参阅 [官方 TLS 扩展规范](https://tools.ietf.org/html/rfc6066#section-4)。

尚未提供对此公共预览版功能的官方 SDK 支持。 入门指南

1. [使用上的预览模式创建新的 IoT 中心](iot-hub-preview-mode.md)。
1. 使用 OpenSSL 时，请调用 [SSL_CTX_set_tlsext_max_fragment_length](https://manpages.debian.org/testing/libssl-doc/SSL_CTX_set_max_send_fragment.3ssl.en.html) 以指定片段大小。
1. 将客户端连接到预览版 IoT 中心。

## <a name="next-steps"></a>后续步骤

- 若要了解有关 IoT 中心安全性和访问控制的详细信息，请参阅 [控制对 Iot 中心的访问](iot-hub-devguide-security.md)。
- 若要了解有关使用 X509 证书进行设备身份验证的详细信息，请参阅 [使用 X.509 CA 证书进行设备身份验证](iot-hub-x509ca-overview.md)
