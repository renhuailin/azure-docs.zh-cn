---
title: 将 Splunk 与 Azure Defender for IoT 集成
description: 本教程介绍如何将 Splunk 与 Azure Defender for IoT 集成。
author: ElazarK
ms.author: v-ekrieg
ms.topic: tutorial
ms.date: 09/12/2021
ms.custom: template-tutorial
ms.openlocfilehash: 2484de315508310729882e46b5e22669d01ec9f8
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124814714"
---
# <a name="tutorial-integrate-splunk-with-azure-defender-for-iot"></a>教程：将 Splunk 与 Azure Defender for IoT 集成

本教程帮助你了解如何将 Splunk 与 Azure Defender for IoT 集成，以及如何使用 Splunk。

Defender for IoT 使用获得专利的、可感知 ICS 的自我学习引擎来缓解 IIoT、ICS 和 SCADA 风险。这些引擎可在不到一小时的映像时间内提供有关 ICS 设备、漏洞和威胁的即时见解，且不需要依赖代理、规则或签名、专业技能，也不需要预先了解环境。

为了解决无法洞察 OT 网络安全性和复原能力的问题，Defender for IoT 开发了适用于 Splunk 的 Defender for IoT、IIoT 和 ICS 威胁监视应用程序，这是 Defender for IoT 与 Splunk 之间的一个原生集成，提供了用于确保 IT 和 OT 安全性的统一方法。

该应用程序为 SOC 分析人员提供对专用 OT 协议和工业环境中部署的 IIoT 设备的多维可见性，以及 ICS 感知行为分析，以便快速检测可疑或异常行为。 该应用程序还可以在一个公司 SOC 内同时进行 IT 和 OT 事件响应。 这是一项重要的改进，因为 IT 和 OT 持续聚合可支持新的 IIoT 计划，例如智能计算机和实时智能。

Splunk 应用程序可以安装在本地，也可以在云中运行。 Splunk 与 Defender for IoT 的集成支持这两种部署。

> [!Note]
> 有关 CyberX 的参考，请参阅“Azure Defender for IoT”。

在本教程中，你将了解：

> [!div class="checklist"]
> * 在 Splunk 中下载 Defender for IoT 应用程序
> * 将 Defender for IoT 警报发送到 Splunk

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

### <a name="version-requirements"></a>版本要求

运行应用程序需要以下版本。

- Defender for IoT 2.4 及更高版本。

- Splunkbase 11 及更高版本。

- Splunk Enterprise 7.2 及更高版本。

### <a name="splunk-permission-requirements"></a>Splunk 权限要求

需要以下 Splunk 权限：

- 具有管理员级别用户角色的任何用户。

## <a name="download-the-defender-for-iot-application-in-splunk"></a>在 Splunk 中下载 Defender for IoT 应用程序

若要在 Splunk 中访问 Defender for IoT 应用程序，需要从 Splunkbase 应用商店下载该应用程序。

若要在 Splunk 中访问 Defender for IoT 应用程序，请执行以下操作：

1. 导航到 [Splunkbase](https://splunkbase.splunk.com/) 应用商店。

1. 搜索 `CyberX ICS Threat Monitoring for Splunk`。

1. 选择适用于 Splunk 的 CyberX ICS 威胁监视应用程序。

1. 选择“登录以下载”按钮。

## <a name="send-defender-for-iot-alerts-to-splunk"></a>将 Defender for IoT 警报发送到 Splunk

Defender for IoT 警报提供大量安全事件的相关信息。 这些事件包括：

- 相对于已学习的基准网络活动的偏差。

- 恶意软件检测。

- 基于可疑操作更改的检测。

- 网络异常。

- 相对于协议规范的协议偏差。

    :::image type="content" source="media/tutorial-splunk/address-scan.png" alt-text="检测屏幕。":::

你还可以将 Defender for IoT 配置为向 Splunk 服务器发送警报，警报信息会显示在 Splunk Enterprise 仪表板中。

:::image type="content" source="media/tutorial-splunk/alerts-and-details.png" alt-text="查看所有警报及其详细信息。" lightbox="media/tutorial-splunk/alerts-and-details-expanded.png":::

若要将警报信息从 Defender for IoT 发送到 Splunk 服务器，需要创建转发规则。

若要创建转发规则，请执行以下操作：

1. 登录传感器，然后从左侧窗格中选择“转发”。

    :::image type="content" source="media/tutorial-splunk/forwarding.png" alt-text="选择“创建转发警报”蓝色按钮。":::

1. 选择“创建转发规则”。

1. 在“创建转发规则”窗口中，定义规则参数。

    :::image type="content" source="media/tutorial-splunk/forwarding-rule.png" alt-text="为转发规则创建规则。" lightbox="media/tutorial-splunk/forwarding-rule-expanded.png":::

    | 参数 | 说明 |
    |--|--|
    | **名称** | 转发规则名称。 |
    | 选择严重性 | 要转发的最低安全级别的事件。 例如，如果选择“轻微”，则会转发轻微警报以及高于此严重性级别的任何警报。 |
    | **协议** | 默认情况下，所有协议都会被选中。 若要选择特定协议，请选择“特定”并选择要对其应用此规则的协议。 |
    | **引擎** | 默认情况下会涉及所有安全引擎。 若要选择要对其应用此规则的特定安全引擎，请选择“特定”，然后选择引擎。 |
    | **系统通知** | 转发传感器联机/脱机状态。 只有登录到 Central Manager 后才能使用此选项。 |

1. 选择“操作”，然后选择“发送到 Splunk 服务器”。

1. 输入以下 Splunk 参数。

    :::image type="content" source="media/tutorial-splunk/parameters.png" alt-text="应在此屏幕上输入的 Splunk 参数。" lightbox="media/tutorial-splunk/parameters-expanded.png":::

    | 参数 | 说明 |
    |--|--|
    | **主机** | Splunk 服务器地址 |
    | 端口 | 8089 |
    | **用户名** | Splunk 服务器用户名 |
    | **密码** | Splunk 服务器密码 |

1. 选择“提交”。 

## <a name="clean-up-resources"></a>清理资源

没有可清理的资源。

## <a name="next-steps"></a>后续步骤

在本教程中，你已了解如何开始进行 Splunk 集成。 继续了解如何[将 ServiceNow 与 Azure Defender for IoT 集成](tutorial-servicenow.md)。

> [!div class="nextstepaction"]
> [将 ServiceNow 与 Azure Defender for IoT 集成](tutorial-servicenow.md)