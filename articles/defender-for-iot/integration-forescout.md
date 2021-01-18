---
title: 关于 Forescout 集成
titleSuffix: Azure Defender for IoT
description: Azure Defender for IoT 与 Forescout 平台的集成为 IoT 和 OT 环境提供了一种新的集中可见性、监视和控制。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/17/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: faa53c770d0d6caac471e770c80b4dfd5c5ff603
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/18/2021
ms.locfileid: "98557395"
---
# <a name="about-the-forescout-integration"></a>关于 Forescout 集成

Azure Defender for IoT 提供由蓝色团队专家构建的 ICS 和 IoT 网络安全平台，其中包含用于保护关键国家基础结构的跟踪记录。 Defender for IoT 是具有专利的 ICS 感知威胁分析和机器学习的唯一平台。 用于 IoT 的 Defender 提供：

- 有关 ICS 设备布局的即时见解，其中包含有关属性的大量详细信息。
- ICS 感知深层嵌入的有关协议、设备、应用程序及其行为的知识。
- 立即深入了解漏洞以及已知和零天的威胁。
- 一种自动化 ICS 威胁建模技术，可通过专有分析预测目标 ICS 攻击的最可能路径。

与 Forescout 平台的 IoT 集成的 Defender 为 IoT 和 OT 提供了一个新的集中可见性、监视和控制级别。

这些桥接平台可实现对以前无法访问的 ICS 设备和孤立工作流的自动化设备可见性和管理。

集成为 SOC 分析师提供对在工业环境中部署的协议的多级可见性。 详细信息可用于基于专用 Azure Defender IoT 技术的固件、设备类型、操作系统和风险分析评分等项。

> [!Note]
> 对 CyberX 的引用是指用于 IoT 的 Azure Defender。
## <a name="devices"></a>设备

### <a name="device-visibility-and-management"></a>设备可见性和管理

设备的清单使用 IoT 平台的 Defender 检测到的关键属性进行了丰富。 这将确保：

- 通过单一窗格，充分利用设备环境中的设备。
- 获取有关其风险的实时智能。

:::image type="content" source="media/integration-forescout/forescout-device-inventory.png" alt-text="设备清单":::

:::image type="content" source="media/integration-forescout/forescout-device-details.png" alt-text="设备详细信息":::

### <a name="device-control"></a>设备控制

Forescout 集成有助于缩短工业和关键基础结构组织检测、调查和应对网络威胁所需的时间。

- 使用用于 IoT 的 Azure Defender 设备智能通过触发 Forescout 策略操作来关闭安全周期。 例如，可以在检测到特定协议时，或在固件详细信息更改时自动将警报电子邮件发送给 SOC 管理员。

- 将 Defender 用于 IoT 信息与监视监视、事件管理和设备控制的其他 *Forescout eyeExtended* 模块。

## <a name="system-requirements"></a>系统要求

- 用于 IoT 版本2.4 或更高版本的 Azure Defender
- Forescout 版本8.0 或更高版本
- 用于 IoT 平台的 Azure Defender *Forescout eyeExtend* 模块的许可证。

### <a name="getting-more-forescout-information"></a>获取更多 Forescout 信息

有关 Forescout 平台的详细信息，请参阅 [Forescout 资源中心](https://www.forescout.com/company/resources/#resource_filter_group)。

## <a name="system-setup"></a>系统设置

本文介绍如何设置用于 IoT 平台和 Forescout 平台的 Defender 之间的通信。

### <a name="set-up-the-defender-for-iot-platform"></a>设置用于 IoT 平台的 Defender

若要确保从 Defender 到 Forescout 的通信，请在用于 IoT 的 Defender 中生成一个访问令牌。

访问令牌允许外部系统访问 Defender 为 IoT 发现的数据，并通过 SSL 连接使用外部 REST API 对该数据执行操作。 可以生成访问令牌，以便访问 Azure Defender for IoT REST API。

生成令牌：

1. 登录到将由 Forescout 查询的 IoT 传感器的 Defender。

1. 选择 "**系统设置**"，然后从 "**常规**" 部分中选择 "**访问令牌**"。 " **访问令牌** " 对话框将打开。
   :::image type="content" source="media/integration-forescout/generate-access-tokens-screen.png" alt-text="访问令牌":::
1. 在 "**访问令牌**" 对话框中选择 "**生成新令牌**"。
1. 在 " **新建访问令牌** " 对话框中输入标记说明。
   :::image type="content" source="media/integration-forescout/new-forescout-token.png" alt-text="新的访问令牌":::
1. 选择“**下一页**”。 该标记将显示在对话框中。 :::image type="content" source="media/integration-forescout/forescout-access-token-display-screen.png" alt-text="查看令牌":::
   > [!NOTE]
   > 将 *令牌记录到安全位置。配置 Forescout 平台时需要用到它*。
1. 选择“完成”。

   :::image type="content" source="media/integration-forescout/forescout-access-token-added-successfully.png" alt-text="完成添加标记":::

### <a name="set-up-the-forescout-platform"></a>设置 Forescout 平台

可以设置 Forescout 平台，使其与用于 IoT 传感器的 Defender 通信。

若要设置：

1. 在 Forescout 平台上安装 *适用于 CyberX 的 Forescout eyeExtend 模块* 。

1. 登录到 "应对" 控制台，然后从 "**工具**" 菜单中选择 "**选项**"。 此时将打开 " **选项** " 对话框。

1. 导航到 " **模块** " 文件夹并将其选中。

1. 在 " **模块** " 窗格中，选择 " **CyberX Platform**"。 此时将打开 "用于 IoT 平台的 Defender" 窗格。

   :::image type="content" source="media/integration-forescout/settings-for-module.png" alt-text="用于 IoT 的 Azure Defender 模块设置":::

   输入以下信息：

   - **服务器地址** -输入将由 Forescout 设备查询的用于 IoT 传感器的 DEFENDER 的 IP 地址。
   - **访问令牌** -输入为将连接到 Forescout 设备的 IoT 传感器生成的访问令牌。 若要生成令牌，请参阅 [设置用于 IoT 的 Defender 平台](#set-up-the-defender-for-iot-platform)。

1. 选择“应用”。

如果希望 Forescout 平台与其他传感器通信：

1. 在适用于 IoT 传感器的相关 Defender 中创建新的访问令牌。

1. 在 " **Forescout 模块**  >  **CyberX 平台**" 对话框中：

   - 删除显示的信息。
   
   - 输入新的传感器 IP 地址和新的访问令牌信息。

### <a name="verify-communication"></a>验证通信

为 IoT 和 Forescout 配置 Defender 后，打开用于 IoT 的 defender 中的传感器的 "访问令牌" 对话框。

如果此令牌的 "已 **使用**" 字段中显示了 " **N/A** "，则传感器与 Forescout 设备之间的连接将不起作用。

**用于** 指示上次接收此令牌的外部调用的时间。

:::image type="content" source="media/integration-forescout/forescout-access-token-added-successfully.png" alt-text="验证是否已收到令牌":::

## <a name="view-defender-for-iot-detections-in-forescout"></a>查看 Forescout 中的 IoT 检测的 Defender

查看设备的属性：

1. 登录到 Forescout 平台，然后导航到 " **资产清单**"。

1. 导航到 **CyberX 平台**。 对于用于 IoT 的 Defender 检测到的设备，将显示下列设备属性。

   | 项 | 说明 |
   |--|--|
   | Azure Defender for IoT 授权 | 网络学习期间，在网络上通过 Defender 为 IoT 检测到设备。 |
   | 固件 | 设备的固件详细信息。 例如，模型和版本详细信息。 |
   | 名称 | 设备的名称。 |
   | 操作系统 | 设备的操作系统。 |
   | 类型 | 设备类型。 例如，PLC、Historian 或工程工作站。 |
   | Vendor | 设备的供应商。 例如，"宋体"。 |
   | 风险级别 | Defender 为 IoT 计算的风险级别。 |
   | 协议 | 设备生成的流量中检测到的协议。 |

:::image type="content" source="media/integration-forescout/device-firmware-attributes-in-forescout.png" alt-text="查看固件属性。":::

:::image type="content" source="media/integration-forescout/vendor-attributes-in-forescout.png" alt-text="查看供应商属性。":::

### <a name="viewing-more-details"></a>查看更多详细信息

查看由 Defender for IoT 定向的设备的额外设备信息。 例如，Forescout 合规性和策略信息。

若要实现此目的，请在 **设备清单主机** 部分右键单击设备。 此时将打开 "主机详细信息" 对话框，其中包含附加信息。

:::image type="content" source="media/integration-forescout/details-dialog-box-in-forescout.png" alt-text="主机详细信息":::

## <a name="create-azure-defender-for-iot-policies-in-forescout"></a>在 Forescout 中创建用于 IoT 策略的 Azure Defender

可以使用 Forescout 策略来自动控制和管理 Defender for IoT 检测到的设备。 例如，

- 当检测到特定固件版本时，自动向 SOC 管理员发送电子邮件。

- 将用于 IoT 检测到的特定 Defender 设备添加到 Forescout 组，以便在事件和安全工作流中进行进一步处理，例如使用其他 SIEM 集成。

使用 "条件属性" 创建 Forescout 自定义策略，并使用具有 Defender for IoT 的 IoT。

访问 Defender for IoT 属性：

1. 从 "**策略条件**" 对话框导航到 "**属性" 树**。

1. 展开 **属性树** 中的 **CyberX 平台** 文件夹。 可用的 IoT 的 Defender 属性如下。

:::image type="content" source="media/integration-forescout/forescout-property-tree.png" alt-text="属性":::

## <a name="next-steps"></a>后续步骤

了解如何 [转发警报信息](how-to-forward-alert-information-to-partners.md)。
