---
title: 适用于 Azure IoT 的安全性建议 | Microsoft Docs
description: 本文总结了一些其他步骤，以确保 Azure IoT 中心解决方案的安全性。
author: dsk-2015
ms.service: iot-hub
services: iot-hub
ms.topic: article
ms.date: 11/13/2019
ms.author: dkshir
ms.custom:
- security-recommendations
- amqp
- mqtt
ms.openlocfilehash: c3592770769492bdf90351c59a1077a56e47baa1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121742219"
---
# <a name="security-recommendations-for-azure-internet-of-things-iot-deployment"></a>Azure 物联网 (IoT) 部署的安全性建议

本文包含适用于 IoT 的安全性建议。 实施执行建议将有助于你履行我们的共享职责模型中描述的安全职责。 若要详细了解 Microsoft 采取哪些措施来履行服务提供商责任，请阅读[云计算的责任分担](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91)。

包含在本文中的某些建议可能受 Azure 安全中心的自动监视。 在保护你在 Azure 中的资源方面，Azure 安全中心是第一道防线。 它定期分析 Azure 资源的安全状态，以识别潜在的安全漏洞。 然后向你提供有关如何解决这些安全漏洞的建议。

- 有关 Azure 安全中心建议的详细信息，请参阅 [Azure 安全中心的安全性建议](../security-center/security-center-recommendations.md)。
- 有关 Azure 安全中心的信息，请参阅[什么是 Azure 安全中心？](../security-center/security-center-introduction.md)

## <a name="general"></a>常规

| 建议 | 注释 | 由 ASC 支持 |
|-|----|--|
| 保持最新状态 | 使用最新版的受支持平台、编程语言、协议和框架。 | - |
| 确保身份验证密钥的安全 | 部署后，请确保以物理方式保障设备 ID 及其身份验证密钥的安全。 这将避免设备恶意伪装为注册设备。 | - |
| 尽可能使用设备 SDK | 设备 SDK 实现多种安全功能（如加密、身份验证等），以此帮助你开发既强大又安全的设备应用程序。 有关详细信息，请参阅[了解和使用 Azure IoT 中心 SDK](../iot-hub/iot-hub-devguide-sdks.md)。 | - |

## <a name="identity-and-access-management"></a>标识和访问管理 

| 建议 | 注释 | 由 ASC 支持 |
|-|----|--|
| 定义中心的访问控制 | [了解并定义](iot-security-deployment.md#securing-the-cloud)每个组件在 IoT 中心解决方案中将具有的访问类型（基于功能）。 允许的权限包括 Registry Read、RegistryReadWrite、ServiceConnect 和 DeviceConnect。 默认的 [IoT 中心的共享访问策略](../iot-hub/iot-hub-dev-guide-sas.md#access-control-and-permissions)还有助于根据组件的角色定义每个组件的权限。 | - |
| 定义后端服务的访问控制 | IoT 中心解决方案引入的数据可供其他 Azure 服务使用，例如：[Cosmos DB](../cosmos-db/index.yml)、[流分析](../stream-analytics/index.yml)、[应用服务](../app-service/index.yml)、[逻辑应用](../logic-apps/index.yml)和 [Blob 存储](../storage/blobs/storage-blobs-introduction.md)。 对于这些服务，请务必进行了解并允许所记录的适当访问权限。 | - |

## <a name="data-protection"></a>数据保护

| 建议 | 注释 | 由 ASC 支持 |
|-|----|--|
| 保护设备身份验证 | 通过使用[唯一的标识密钥或安全令牌](iot-security-deployment.md#iot-hub-security-tokens)，或每个设备的[设备上 X.509 证书](iot-security-deployment.md#x509-certificate-based-device-authentication)，确保设备和 IoT 中心之间的通信安全。 [根据所选协议（MQTT、AMQP 或 HTTPS），采取使用安全令牌的](../iot-hub/iot-hub-dev-guide-sas.md)适当方法。 | - |
| 保护设备通信 | IoT 中心使用传输层安全性 (TLS) 标准（支持版本 1.2 和 1.0）来保护与设备的连接。 使用 [TLS 1.2](https://tools.ietf.org/html/rfc5246) 来确保最大安全性。 | - |
| 保护服务通信 | IoT 中心提供终结点，以便仅使用 TLS 协议连接到后端服务（如 [Azure 存储](../storage/index.yml)或[事件中心](../event-hubs/index.yml)），并且不会在未加密的通道上公开任何终结点。 在将此数据传输到这些后端服务进行存储或分析后，请确保为该服务使用适当的安全和加密方法，同时保护后端的敏感信息。 | - |

## <a name="networking"></a>网络

| 建议 | 注释 | 由 ASC 支持 |
|-|----|--|
| 保护对设备的访问 | 将设备中的硬件端口数保持在最低限度，以避免不必要的访问。 此外，建立相应机制来阻止或检测对设备的物理篡改。 有关详细信息，请参阅 [IoT 安全性最佳做法](iot-security-best-practices.md)。 | - |
| 构建安全的硬件 | 合并加密存储或受信任的平台模块 (TPM) 等安全功能，以使设备和基础结构更加安全。 将设备操作系统和驱动程序升级到最新版本，如果空间允许，请安装防病毒和反恶意软件功能。 阅读 [IoT 安全性体系结构](iot-security-architecture.md)，了解这可以如何帮助缓解若干安全威胁。 | - |

## <a name="monitoring"></a>监视

| 建议 | 注释 | 由 ASC 支持 |
|-|----|--|
| 监视对设备的未授权访问 |  使用设备操作系统的日志记录功能来监视设备或其端口的任何安全漏洞或物理篡改。 | - |
| 监视云中的 IoT 解决方案 | 使用 [Azure Monitor 中的指标](../iot-hub/monitor-iot-hub.md)监视 IoT 中心解决方案的总体运行状况。 | - |
| 设置诊断 | 通过在解决方案中记录事件，然后将诊断日志发送到 Azure Monitor 来密切监视操作，从而了解性能。 有关详细信息，请阅读[监视 IoT 中心并诊断其中的问题](../iot-hub/monitor-iot-hub.md)。 | - |

## <a name="next-steps"></a>后续步骤

对于涉及 Azure IoT 的高级方案，你可能需要考虑其他安全要求。 有关更多指导，请参阅 [IoT 安全性体系结构](iot-security-architecture.md)。