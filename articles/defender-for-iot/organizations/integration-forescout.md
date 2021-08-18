---
title: 关于 Forescout 集成
description: Azure Defender for IoT 与 Forescout 平台集成为 IoT 和 OT 布局提供了一个新的集中可见性、监视和控制级别。
ms.date: 1/17/2021
ms.topic: article
ms.openlocfilehash: 07e5187970d193502b95b49c5517a8e3824767be
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113015273"
---
# <a name="about-the-forescout-integration"></a>关于 Forescout 集成

Azure Defender for IoT 提供由蓝团专家构建的 ICS 和 IoT 网络安全平台，其中包含对关键国家/地区基础结构防护的跟踪记录。 Defender for IoT 是唯一一个具有获得专利的 ICS 感知威胁分析和机器学习的平台。 Defender for IoT 提供：

- 有关 ICS 设备布局的即时见解，以及有关属性的大量详细信息。
- OT 协议、设备、应用程序及其行为的 ICS 感知深层嵌入知识。
- 对漏洞和已知零日威胁的即时见解。
- 通过专有分析预测目标 ICS 攻击的最可能路径的自动化 ICS 威胁建模技术。

Defender for IoT 与 Forescout 平台集成为 IoT 和 OT 布局提供了一个新的集中可见性、监视和控制级别。

这些桥接平台实现了对以前无法访问的 ICS 设备和孤立工作流的自动设备可见性和管理。

此集成为 SOC 分析师提供了对工业环境中部署的 OT 协议的多级可见性。 可详细了解固件、设备类型、操作系统和基于专用 Azure Defender for IoT 技术的风险分析评分等项目。

> [!Note]
> 有关 CyberX 的参考，请参阅“Azure Defender for IoT”。
## <a name="devices"></a>设备

### <a name="device-visibility-and-management"></a>设备可见性和管理

设备的清单扩充了 Defender for IoT 平台检测到的关键属性。 这将确保你：

- 可通过单个平台全面并持续了解 OT 设备环境。
- 获取有关 OT 风险的实时智能。

:::image type="content" source="media/integration-forescout/forescout-device-inventory.png" alt-text="设备清单":::

:::image type="content" source="media/integration-forescout/forescout-device-details.png" alt-text="设备详细信息":::

### <a name="device-control"></a>设备控制

Forescout 集成有助于缩短工业和关键基础结构组织检测、调查和应对网络威胁所需的时间。

- 使用 Azure Defender for IoT OT 设备智能来通过触发 Forescout 策略操作关闭安全周期。 例如，可以在检测到特定协议或在固件详细信息发生更改时自动将警报电子邮件发送给 SOC 管理员。

- 将 Defender for IoT 信息与监管监视、事件管理和设备控制的其他 Forescout eyeExtended 模块相关联。

## <a name="system-requirements"></a>系统要求

- Azure Defender for IoT 版本 2.4 或更高版本
- Forescout 版本 8.0 或更高版本
- 用于 Azure Defender for IoT 平台的 Forescout eyeExtend 模块的许可证。

### <a name="getting-more-forescout-information"></a>获取更多有关 Forescout 的信息

有关 Forescout 平台的详细信息，请参阅 [Forescout 资源中心](https://www.forescout.com/company/resources/#resource_filter_group)。

## <a name="system-setup"></a>系统设置

本文介绍如何设置 Defender for IoT platform 平台和 Forescout 平台之间的通信。

### <a name="set-up-the-defender-for-iot-platform"></a>设置 Defender for IoT 平台

请在 Defender for IoT 中生成一个访问令牌，以确保 Defender for IoT 到 Forescout 的通信。

通过访问令牌，外部系统可以访问 Defender for IoT 发现的数据，并通过 SSL 连接使用外部 REST API 对该数据执行操作。 可以生成访问令牌，以便访问 Azure Defender for IoT REST API。

生成令牌：

1. 登录到 Forescout 将查询的 Defender for IoT 传感器。

1. 选择“系统设置”，然后从“常规”部分中选择“访问令牌”  。 “访问令牌”对话框将打开。
   :::image type="content" source="media/integration-forescout/generate-access-tokens-screen.png" alt-text="访问令牌":::
1. 在“访问令牌”对话框中选择“生成新令牌” 。
1. 在“新建访问令牌”对话框中输入令牌说明。
   :::image type="content" source="media/integration-forescout/new-forescout-token.png" alt-text="新建访问令牌":::
1. 选择“下一步”。 该令牌显示在对话框中。 :::image type="content" source="media/integration-forescout/forescout-access-token-display-screen.png" alt-text="查看令牌":::
   > [!NOTE]
   > 在安全的位置中记录此令牌。配置 Forescout 平台时你将需要使用它。
1. 选择“完成”。

   :::image type="content" source="media/integration-forescout/forescout-access-token-added-successfully.png" alt-text="完成添加令牌":::

### <a name="set-up-the-forescout-platform"></a>设置 Forescout 平台

可以设置 Forescout 平台，使其与 Defender for IoT 传感器通信。

若要设置：

1. 在 Forescout 平台上安装用于 CyberX 的 Forescout eyeExtend 模块。

1. 登录到 CounterACT 控制台，然后从“工具”菜单中选择“选项” 。 “选项”对话框将打开。

1. 导航到“模块”文件夹并选择该文件夹。

1. 在“模块”窗格中，选择“CyberX 平台” 。 “Defender for IoT 平台”窗格将打开。

   :::image type="content" source="media/integration-forescout/settings-for-module.png" alt-text="Azure Defender for IoT 模块设置":::

   输入以下信息：

   - 服务器地址 - 输入 Forescout 设备将查询的 Defender for IoT 传感器的 IP 地址。
   - 访问令牌 - 输入为要连接到 Forescout 设备的 Defender for IoT 传感器生成的访问令牌。 若要生成令牌，请参阅[设置 Defender for IoT 平台](#set-up-the-defender-for-iot-platform)。

1. 选择“应用”。

如果希望 Forescout 平台与其他传感器通信：

1. 请在相关的 Defender for IoT 传感器中创建新的访问令牌。

1. 在“Forescout 模块” > “CyberX 平台”对话框中：

   - 删除显示的信息。
   
   - 输入新的传感器 IP 地址和新的访问令牌信息。

### <a name="verify-communication"></a>验证通信

配置 Defender for IoT 和 Forescout 后，在 Defender for IoT 中打开传感器的“访问令牌”对话框。

如果此令牌的“已使用”字段中显示“N/A”，则表示传感器与 Forescout 设备之间的连接不起作用 。

“已使用”指示上次接收此令牌的外部调用的时间。

:::image type="content" source="media/integration-forescout/forescout-access-token-added-successfully.png" alt-text="验证是否接收了令牌":::

## <a name="view-defender-for-iot-detections-in-forescout"></a>在 Forescout 中查看 Defender for IoT 检测

查看设备的属性：

1. 登录到 Forescout 平台，然后导航到“资产清单”。

1. 导航到“CyberX 平台”。 将显示 Defender for IoT 检测到的 OT 设备的以下设备属性。

   | 项 | 说明 |
   |--|--|
   | Azure Defender for IoT 已授权 | Defender for IoT 在网络学习期间从网络中检测到的设备。 |
   | 固件 | 设备的固件的详细信息。 例如，模型和版本的详细信息。 |
   | 名称 | 设备的名称。 |
   | 操作系统 | 设备的操作系统。 |
   | 类型 | 设备类型。 例如，PLC、历史数据库或工程工作站。 |
   | Vendor | 设备的供应商。 例如，Rockwell Automation。 |
   | 风险级别 | Defender for IoT 计算出的风险级别。 |
   | 协议 | 在设备生成的流量中检测到的协议。 |

:::image type="content" source="media/integration-forescout/device-firmware-attributes-in-forescout.png" alt-text="查看固件属性。":::

:::image type="content" source="media/integration-forescout/vendor-attributes-in-forescout.png" alt-text="查看供应商属性。":::

### <a name="viewing-more-details"></a>查看更多详细信息

查看 Defender for IoT 指向的设备的其他设备信息。 例如，有关 Forescout 符合性和策略的信息。

为此，请在“设备清单主机”部分右键单击设备。 “主机详细信息”对话框将打开，其中包含更多信息。

:::image type="content" source="media/integration-forescout/details-dialog-box-in-forescout.png" alt-text="主机详细信息":::

## <a name="create-azure-defender-for-iot-policies-in-forescout"></a>在 Forescout 中创建 Azure Defender for IoT 策略

可以使用 Forescout 策略实现对 Defender for IoT 检测到的设备的自动化管理和控制。 例如，

- 在检测到特定固件版本时自动向 SOC 管理员发送电子邮件。

- 例如，将 Defender for IoT 检测到的特定设备添加到 Forescout 组，以便在事件和安全工作流中使用其他 SIEM 集成进行进一步处理。

使用 Defender for IoT 和条件属性创建 Forescout 自定义策略。

访问 Defender for IoT 属性：

1. 从“策略条件”对话框导航到“属性树” 。

1. 展开“属性树”中的“CyberX 平台” 文件夹。 其中提供了 Defender for IoT 的以下属性。

:::image type="content" source="media/integration-forescout/forescout-property-tree.png" alt-text="属性":::

## <a name="next-steps"></a>后续步骤

了解如何[转发警报信息](how-to-forward-alert-information-to-partners.md)。
