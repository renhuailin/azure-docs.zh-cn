---
title: Azure IoT 中心 TLS 支持
description: 了解如何对与 IoT 中心通信的设备和服务使用安全 TLS 连接
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 06/29/2021
ms.author: jlian
ms.openlocfilehash: 0a9a3bc11bac0dd389c346ccecab7f95d5ffd5d2
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2021
ms.locfileid: "113128023"
---
# <a name="transport-layer-security-tls-support-in-iot-hub"></a>IoT 中心的传输层安全性 (TLS) 支持

IoT 中心使用传输层安全性 (TLS) 保护来自 IoT 设备和服务的连接。 目前支持三个版本的 TLS 协议，即版本 1.0、1.1 和 1.2。

TLS 1.0 和 1.1 被视为旧版，我们已计划弃用这两个版本。 有关详细信息，请参阅 [IoT 中心弃用 TLS 1.0 和 1.1](iot-hub-tls-deprecating-1-0-and-1-1.md)。 为避免将来出现错误，请在连接到 IoT 中心时使用 TLS 1.2 作为唯一的 TLS 版本。

## <a name="iot-hubs-server-tls-certificate"></a>IoT 中心的服务器 TLS 证书

在 TLS 握手期间，IoT 中心会提供 RSA 加密的服务器证书以连接客户端。 它的根是 Baltimore Cybertrust 根 CA。 最近，我们推出了对 TLS 服务器证书的更改，它现在由新的中间证书颁发机构 (ICA) 颁发。 有关详细信息，请参阅 [IoT 中心 TLS 证书更新](https://azure.microsoft.com/updates/iot-hub-tls-certificate-update/)。

### <a name="elliptic-curve-cryptography-ecc-server-tls-certificate-preview"></a>椭圆曲线加密 (ECC) 服务器 TLS 证书（预览版）

IoT 中心 ECC 服务器 TLS 证书提供公共预览版。 在为 RSA 证书提供相似安全性的同时，ECC 证书验证（具有仅限 ECC 的加密套件）最多使用 40% 的计算、内存和带宽。 由于 IoT 设备的配置文件和内存较少，因此这些节省的资源对于 IoT 设备而言至关重要，并且对于支持网络带宽受限的环境中的用例也至关重要。 ECC 服务器证书的根是 DigiCert 全局根 G3。

预览 IoT 中心的 ECC 服务器证书：

1. [在启用预览模式的情况下创建新的 IoT 中心](iot-hub-preview-mode.md)。
1. [对客户端进行配置](#tls-configuration-for-sdk-and-iot-edge)，使其仅包含 ECDSA 密码套件并排除任何 RSA 密码套件 。 以下是 ECC 证书公共预览版支持的密码套件：
    - `TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256`
    - `TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384`
    - `TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256`
    - `TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384`
1. 将客户端连接到预览版 IoT 中心。

## <a name="tls-12-enforcement-available-in-select-regions"></a>选定区域支持的 TLS 1.2 强制执行

为了提高安全性，请将 IoT 中心配置为仅允许那些使用 TLS 版本 1.2 的客户端连接，并强制使用[密码套件](#cipher-suites)。 仅以下地区支持此功能：

* 美国东部
* 美国中南部
* 美国西部 2
* US Gov 亚利桑那州
* US Gov 弗吉尼亚州（此区域不支持 TLS 1.0/1.1 - 必须启用 TLS 1.2 强制执行，否则 IoT 中心创建会失败）

若要启用 TLS 1.2 强制执行，请按照[在 Azure 门户创建 IoT 中心](iot-hub-create-through-portal.md)中的步骤操作，以下步骤除外：

- 从上面的列表中选择一个“区域”。
- 在“管理”->“高级”->“传输层安全性 (TLS)”->“最低 TLS 版本”下，选择“1.2” 。 仅在受支持的区域中创建 IoT 中心时，才会显示此设置。

    :::image type="content" source="media/iot-hub-tls-12-enforcement.png" alt-text="显示如何在创建 IoT 中心期间启用 TLS 1.2 强制执行的屏幕截图":::

若要使用 ARM 模板进行创建，请在任何受支持的区域中预配新的 IoT 中心，并将资源规范中的 `minTlsVersion` 属性设置为 `1.2`：

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

使用此配置创建的 IoT 中心资源会拒绝那些尝试使用 TLS 版本 1.0 和 1.1 进行连接的设备和服务客户端。 同样，如果 `ClientHello` 消息未列出任何[建议的密码](#cipher-suites)，TLS 握手会被拒绝。

> [!NOTE]
> `minTlsVersion` 属性处于只读状态，创建 IoT 中心资源后，便不能再更改该属性。 因此，必须事先正确测试并验证你的所有 IoT 设备和服务是否与 TLS 1.2 和[建议的密码](#cipher-suites)兼容。
> 
> 故障转移后，IoT 中心的 `minTlsVersion` 属性在异地配对区域中仍然有效。

## <a name="cipher-suites"></a>密码套件

配置为仅接受 TLS 1.2 的 IoT 中心还会强制使用以下建议的密码套件：

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`

对于未配置为强制接受 TLS 1.2 的 IoT 中心，TLS 1.2 仍可使用以下密码套件：

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

客户端可以建议在 `ClientHello` 期间使用的高级密码套件的列表。 但是，IoT 中心可能不支持其中某些密码套件（例如 `ECDHE-ECDSA-AES256-GCM-SHA384`）。 在这种情况下，IoT 中心将尝试遵循客户端的偏好，但最终会通过 `ServerHello` 协商密码套件。

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

成功进行 TLS 握手后，IoT 中心可以使用对称密钥或 X.509 证书对设备进行身份验证。 对于基于证书的身份验证，可以使用任何 X.509 证书，包括 ECC。 IoT 中心根据所提供的指纹或证书颁发机构 (CA) 对证书进行验证。 若要了解详细信息，请参阅[支持的 X.509 证书](iot-hub-dev-guide-sas.md#supported-x509-certificates)。

## <a name="tls-maximum-fragment-length-negotiation-preview"></a>TLS 最大片段长度协商（预览）

IoT 中心还支持 TLS 最大片段长度协商，有时也称为 TLS 片段大小协商。 此功能目前以公共预览版提供。 

使用此功能可将最大纯文本片段长度指定为小于默认 2^14 字节的值。 协商后，IoT 中心和客户端开始对消息进行分段，以确保所有片段都小于协商的长度。 此行为对计算或内存受限的设备很有用。 若要了解详细信息，请参阅[官方 TLS 扩展规范](https://tools.ietf.org/html/rfc6066#section-4)。

尚未提供对此公共预览版功能的官方 SDK 支持。 入门指南

1. [在启用预览模式的情况下创建新的 IoT 中心](iot-hub-preview-mode.md)。
1. 使用 OpenSSL 时，请调用 [SSL_CTX_set_tlsext_max_fragment_length](https://manpages.debian.org/testing/libssl-doc/SSL_CTX_set_max_send_fragment.3ssl.en.html) 以指定片段大小。
1. 将客户端连接到预览版 IoT 中心。

## <a name="next-steps"></a>后续步骤

- 若要了解有关 IoT 中心安全性和访问控制的详细信息，请参阅[控制对 IoT 中心的访问](iot-hub-devguide-security.md)。
- 若要了解有关使用 X509 证书进行设备身份验证的详细信息，请参阅[使用 X.509 CA 证书进行设备身份验证](iot-hub-x509ca-overview.md)
