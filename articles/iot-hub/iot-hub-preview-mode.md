---
title: 为 Azure IoT 中心启用预览模式
description: 了解为 IoT 中心启用预览模式的方式、原因，以及相关的一些警告
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: jlian
ms.openlocfilehash: 864870c4392b12477c321c86afd9da848120490c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96621699"
---
# <a name="turn-on-preview-mode-for-iot-hub-to-try-select-new-features"></a>为 Azure IoT 中心启用预览模式以试用特选的新功能

<!-- 
- We are working hard to bring you new features
- Some of these features require a brand new iot hub with preview mode on
- some features may not work at all or have unexpected behavior
- "Normal preview features" do NOT require preview mode 
- Support opt-in at creation time only
- Customer cannot opt back out post creation
- If customer wants to evaluate, they must use new hub dedicated for the preview
- Banners, documentations and all materials indicate preview quality: no GA guarantee at all
-->

IoT 中心的公共预览版提供了一些新功能，其中包括：

- MQTT 5
- ECC 服务器证书
- TLS 片段长度协商
- X509 CA 链对 HTTPS/WebSocket 的支持

这些功能是在 IoT 中心协议和身份验证层上进行的改进，因此目前仅适用于新的 IoT 中心。 它们尚不适用于现有 IoT 中心。 若要预览这些功能，必须在启用预览模式的情况下创建 IoT 中心。

## <a name="turn-on-preview-mode-for-a-new-iot-hub"></a>为新的 IoT 中心启用预览模式

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 从 Azure 主页中选择“+ 创建资源”按钮，然后在“搜索市场”字段中输入“IoT 中心”。

1. 在搜索结果中选择“IoT 中心”，然后选择“创建” 。

1. 在“基本信息”选项卡上[照常](iot-hub-create-through-portal.md)填写字段，“区域”字段除外。 选择以下区域之一：
    
    - Central US
    - 西欧
    - 东南亚

1. 选择“管理”选项卡，然后展开“高级设置”部分。

1. 在“预览模式”旁边，选择“开启”。 仔细查看警告文本。

    :::image type="content" source="media/iot-hub-preview-mode/turn-on-preview-mode-at-create.png" alt-text="图中显示了创建新的 IoT 中心时在何处选择预览模式选项":::

1. 选择“下一步:查看 + 创建”，然后选择“创建”。

创建后，处于预览模式的 IoT 中心始终会显示此横幅，让你知道仅将此 IoT 中心用于预览目的： 

:::image type="content" source="media/iot-hub-preview-mode/banner.png" alt-text="此图显示预览模式 IoT 中心的横幅":::

## <a name="using-an-iot-hub-in-preview-mode"></a>在预览模式下使用 IoT 中心

不要将预览模式的 IoT 中心用于生产。 预览模式仅用于预览此页面顶部列出的特选功能。 IoT 中心预览模式有一些其他限制：

- 某些现有的 IoT 中心功能（例如 IP 筛选器、专用链接、托管标识、设备流和故障转移）可能会工作异常或根本不工作。
- 处于预览模式的 IoT 中心无法更改或升级为正常 IoT 中心。
- 我们无法保证正常的 [IoT 中心 SLA](https://azure.microsoft.com/support/legal/sla/iot-hub/v1_2/) - 不要将其用于生产。

> [!TIP]
> 对于[设备流](iot-hub-device-streams-overview.md)和[分布式跟踪](iot-hub-distributed-tracing.md)，预览模式不是必需的。 若要使用这些较早的预览功能，请按照其文档正常操作。 

## <a name="next-steps"></a>后续步骤

- 若要预览 MQTT 5 支持，请参阅 [IoT 中心 MQTT 5 支持概述（预览）](iot-hub-mqtt-5.md)
- 若要预览 ECC 服务器证书，请参阅[椭圆曲线加密 (ECC) 服务器 TLS 证书（预览）](iot-hub-tls-support.md#elliptic-curve-cryptography-ecc-server-tls-certificate-preview)
- 若要预览 TLS 片段大小协商，请参阅 [TLS 最大片段长度协商（预览）](iot-hub-tls-support.md#tls-maximum-fragment-length-negotiation-preview)