---
title: 打开 Azure IoT 中心的预览模式
description: 了解如何打开 IoT 中心的预览模式、需要的原因以及一些警告
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: jlian
ms.openlocfilehash: 864870c4392b12477c321c86afd9da848120490c
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2020
ms.locfileid: "96621699"
---
# <a name="turn-on-preview-mode-for-iot-hub-to-try-select-new-features"></a>打开 IoT 中心的预览模式，尝试选择新功能

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

新功能处于 IoT 中心公共预览版中，其中包括：

- MQTT 5
- ECC 服务器证书
- TLS 片段长度协商
- X509 CA 链对 HTTPS/WebSocket 的支持

这些功能在 IoT 中心协议和身份验证层上进行了改进，因此它们目前仅适用于 **新** 的 IoT 中心。 目前尚 *不* 支持现有的 IoT 中心。 若要预览这些功能，必须在启用了预览模式的情况下创建 IoT 中心。

## <a name="turn-on-preview-mode-for-a-new-iot-hub"></a>为新的 IoT 中心打开预览模式

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 从 Azure 主页中选择“+ 创建资源”按钮，然后在“搜索市场”字段中输入“IoT 中心”。

1. 在搜索结果中选择“IoT 中心”，然后选择“创建” 。

1. 在 "**基本** 信息" 选项卡上，[像往常一样](iot-hub-create-through-portal.md)完成字段 **。** 选择以下区域之一：
    
    - Central US
    - 西欧
    - 东南亚

1. 选择 " **管理** " 选项卡，然后展开 " **高级设置** " 部分。

1. 在 **预览模式** 旁边，选择 **"打开"**。 仔细查看警告文本。

    :::image type="content" source="media/iot-hub-preview-mode/turn-on-preview-mode-at-create.png" alt-text="显示创建新 IoT 中心时在何处选择预览模式选项的图像":::

1. 选择 " **下一步"：查看 + 创建**，然后 **创建**。

创建后，处于预览模式的 IoT 中心始终会显示此横幅，让你知道仅出于预览目的使用此 IoT 中心： 

:::image type="content" source="media/iot-hub-preview-mode/banner.png" alt-text="显示预览模式 IoT 中心横幅的图像":::

## <a name="using-an-iot-hub-in-preview-mode"></a>在预览模式下使用 IoT 中心

不要 *将 IoT* 中心用于生产环境。 预览模式 *仅* 用于预览此页面顶部列出的选择功能。 IoT 中心预览模式有一些其他限制

- 某些现有的 IoT 中心功能（如 IP 筛选器、专用链接、托管标识、设备流和故障转移）可能会意外或根本不工作。
- 处于预览模式的 IoT 中心无法更改或升级到普通 IoT 中心。
- 我们无法保证普通 [IoT 中心 SLA](https://azure.microsoft.com/support/legal/sla/iot-hub/v1_2/) -不能用于生产。

> [!TIP]
> 对于 [设备流](iot-hub-device-streams-overview.md) 和 [分布式跟踪](iot-hub-distributed-tracing.md)，预览模式不是必需的。 若要使用这些旧预览版功能，请在文档中按正常方式操作。 

## <a name="next-steps"></a>后续步骤

- 若要预览 MQTT 5 支持，请参阅 [IoT 中心 MQTT 5 支持概述 (预览) ](iot-hub-mqtt-5.md)
- 若要预览 ECC 服务器证书，请参阅 [椭圆曲线加密 (ECC) 服务器 TLS 证书 (预览) ](iot-hub-tls-support.md#elliptic-curve-cryptography-ecc-server-tls-certificate-preview)
- 若要预览 TLS 片段大小协商，请参阅 [tls 最大片段长度协商 (预览) ](iot-hub-tls-support.md#tls-maximum-fragment-length-negotiation-preview)