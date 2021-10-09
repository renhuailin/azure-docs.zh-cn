---
title: 将 Forescout 与 Azure Defender for IoT 集成
description: 本教程介绍如何将 Azure Defender for IoT 与 Forescout 集成。
author: ElazarK
ms.author: v-ekrieg
ms.topic: tutorial
ms.date: 09/23/2021
ms.custom: template-tutorial
ms.openlocfilehash: 6a930671e501940ce7f6d0d22036225e7539eb7f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128701518"
---
# <a name="tutorial-integrate-forescout-with-azure-defender-for-iot"></a>教程：将 Forescout 与 Azure Defender for IoT 集成

> [!Note]
> 有关 CyberX 的参考，请参阅“Azure Defender for IoT”。

本教程介绍如何将 Forescout 与 Azure Defender for IoT 集成，以及如何使用 Forescout。

Azure Defender for IoT 提供 ICS 和 IoT 网络安全平台。 Defender for IoT 是唯一一个具有 ICS 感知威胁分析和机器学习功能的平台。 Defender for IoT 提供：

- 有关 ICS 设备布局的即时见解，以及有关属性的大量详细信息。

- OT 协议、设备、应用程序及其行为的 ICS 感知深层嵌入知识。

- 对漏洞和已知零日威胁的即时见解。

- 通过专有分析预测目标 ICS 攻击的最可能路径的自动化 ICS 威胁建模技术。

Forescout 集成有助于缩短工业和关键基础结构组织检测、调查和应对网络威胁所需的时间。

- 使用 Azure Defender for IoT OT 设备智能来通过触发 Forescout 策略操作关闭安全周期。 例如，可以在检测到特定协议或在固件详细信息发生更改时自动将警报电子邮件发送给 SOC 管理员。

- 将 Defender for IoT 信息与监管监视、事件管理和设备控制的其他 Forescout eyeExtended 模块相关联。

Defender for IoT 与 Forescout 平台集成为 IoT 和 OT 布局提供了集中的可见性、监视和控制。 这些桥接平台实现了对 ICS 设备和孤立工作流的自动设备可见性和管理。 此集成为 SOC 分析师提供了对工业环境中部署的 OT 协议的多级可见性。 可以详细了解固件、设备类型、操作系统和基于专用 Azure Defender for IoT 技术的风险分析评分等信息。

在本教程中，你将了解：

> [!div class="checklist"]
> - 生成访问令牌
> - 设置 Forescout 平台
> - 验证通信
> - 查看 Forescout 中的设备属性
> - 在 Forescout 中创建 Azure Defender for IoT 策略

如果还没有 Azure 帐户，可以[立即创建 Azure 免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

- Azure Defender for IoT 版本 2.4 或更高版本

- Forescout 版本 8.0 或更高版本

- 用于 Azure Defender for IoT 平台的 Forescout eyeExtend 模块的许可证。

## <a name="generate-an-access-token"></a>生成访问令牌

访问令牌使外部系统能够访问 Defender for IoT 发现的数据。 访问令牌允许将这些数据用于外部 REST API 并通过 SSL 进行连接。 可以生成访问令牌，以便访问 Azure Defender for IoT REST API。

必须在 Defender for IoT 中生成一个访问令牌，以确保 Defender for IoT 与 Forescout 之间的通信。

生成访问令牌：

1. 登录 Forescout 将查询的 Defender for IoT 传感器。

1. 从“常规”部分中选择“系统设置” > “访问令牌”  。

1. 选择“生成新令牌”。

    :::image type="content" source="media/tutorial-forescout/generate-access-tokens-screen.png" alt-text="访问令牌生成屏幕的屏幕截图。":::

1. 在“新建访问令牌”对话框中输入令牌说明。

   :::image type="content" source="media/tutorial-forescout/new-forescout-token.png" alt-text="新建访问令牌":::

1. 选择“下一步”。 该令牌随即显示在对话框中。

   > [!NOTE]
   > 在安全的位置中记录此令牌。 配置 Forescout 平台时需要使用它。

1. 选择“完成”。

   :::image type="content" source="media/tutorial-forescout/forescout-access-token-added-successfully.png" alt-text="完成添加令牌":::

## <a name="configure-the-forescout-platform"></a>设置 Forescout 平台

现可配置 Forescout 平台，使其与 Defender for IoT 传感器通信。

配置 Forescout 平台：

1. 在 Forescout 平台上，搜索并安装用于 CyberX 的 Forescout eyeExtend 模块。

1. 登录 CounterACT 控制台。

1. 从 “工具” 菜单中，选择 **“选项”** 。

1. 导航到“模块” > “CyberX 平台” 。

   :::image type="content" source="media/tutorial-forescout/settings-for-module.png" alt-text="Azure Defender for IoT 模块设置":::

1. 在“服务器地址”字段中，输入 Forescout 设备将查询的 Defender for IoT 传感器的 IP 地址。

1. 在“访问令牌”字段中，输入之前生成的访问令牌。

1. 选择“应用”。 

### <a name="change-sensors-in-forescout"></a>更改 Forescout 中的传感器

若要使 Forescout 平台与不同的传感器通信，必须更改 Forescout 中的配置。

更改 Forescout 中的传感器：

1. 请在相关的 Defender for IoT 传感器中创建新的访问令牌。

1. 导航到“Forescout 模块” > “CyberX 平台” 。

1. 删除这两个字段中显示的信息。

1. 登录新的 Defender for IoT 传感器，并[生成新的访问令牌](#generate-an-access-token)。

1. 在“服务器地址”字段中，输入 Forescout 设备将查询的 Defender for IoT 传感器的新 IP 地址。

1. 在“访问令牌”字段中，输入新的访问令牌。

1. 选择“应用”。 

## <a name="verify-communication"></a>验证通信

配置连接后，需要确认两个平台是否可以进行通信。

确认两个平台正在通信：

1. 登录 Defender for IoT 传感器。

1. 导航到“系统设置” > “访问令牌” 。

如果传感器与 Forescout 设备之间的连接无法正常工作，则“已使用”字段将发出警报。 如果显示“N/A”，则该连接无法正常工作。 如果现实“已使用”，则它指示上次接收此令牌的外部调用的时间。

:::image type="content" source="media/tutorial-forescout/forescout-access-token-added-successfully.png" alt-text="验证是否接收了令牌":::

## <a name="view-device-attributes-in-forescout"></a>查看 Forescout 中的设备属性

通过将 Defender for IoT 与 Forescout 集成，可以在 Forescout 应用程序中查看 Defender for IoT 检测到的不同设备的属性。

下表列出了通过 Forescout 应用程序可见的所有属性：

| 项 | 说明 |
|--|--|
| **Azure Defender for IoT 已授权** | Defender for IoT 在网络学习期间从网络中检测到的设备。 |
| **固件** | 设备的固件的详细信息。 例如，模型和版本的详细信息。 |
| **名称** | 设备的名称。 |
| **操作系统** | 设备的操作系统。 |
| **类型** | 设备类型。 例如，PLC、历史数据库或工程工作站。 |
| **供应商** | 设备的供应商。 例如，Rockwell Automation。 |
| **风险级别** | Defender for IoT 计算出的风险级别。 |
| **协议** | 在设备生成的流量中检测到的协议。 |

查看设备的属性：

1. 登录到 Forescout 平台，然后导航到“资产清单”。

   :::image type="content" source="media/tutorial-forescout/device-firmware-attributes-in-forescout.png" alt-text="查看固件属性。":::

1. 选择“CyberX 平台”。

   :::image type="content" source="media/tutorial-forescout/vendor-attributes-in-forescout.png" alt-text="查看供应商属性。":::

### <a name="view-more-details"></a>查看更多详细信息

查看设备属性后，可以查看每个设备的更多详细信息，例如 Forescout 合规性和策略信息。

查看其它详细信息：

1. 登录到 Forescout 平台，然后导航到“资产清单”。

1. 选择“CyberX 平台”。

1. 在“设备清单主机”部分中，右键单击设备。 “主机详细信息”对话框将打开，其中包含更多信息。

## <a name="create-azure-defender-for-iot-policies-in-forescout"></a>在 Forescout 中创建 Azure Defender for IoT 策略

可以使用 Forescout 策略实现对 Defender for IoT 检测到的设备的自动化管理和控制。 例如，

- 在检测到特定固件版本时自动向 SOC 管理员发送电子邮件。

- 例如，将 Defender for IoT 检测到的特定设备添加到 Forescout 组，以便在事件和安全工作流中使用其他 SIEM 集成进行进一步处理。

可以使用 Defender for IoT 条件属性在 Forescout 中创建自定义策略。

访问 Defender for IoT 属性：

1. 导航到“策略条件” > “属性树” 。

1. 在“属性树”中，展开“CyberX 平台”文件夹。 其中提供了 Defender for IoT 的以下属性。

:::image type="content" source="media/tutorial-forescout/forescout-property-tree.png" alt-text="属性":::

## <a name="clean-up-resources"></a>清理资源

没有可清理的资源。

## <a name="next-steps"></a>后续步骤

在本教程中，你已了解如何开始进行 Forescout 集成。 请继续了解 [Palo Alto 集成](./tutorial-palo-alto.md)。

> [!div class="nextstepaction"]
> [“后续步骤”按钮](./tutorial-palo-alto.md)