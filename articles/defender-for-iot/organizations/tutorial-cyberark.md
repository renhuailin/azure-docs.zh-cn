---
title: 将 CyberArk 与 Azure Defender for IoT 集成
description: 本教程介绍如何将 Azure Defender for IoT 与 CyberArk 集成。
author: ElazarK
ms.author: v-ekrieg
ms.topic: tutorial
ms.date: 09/30/2021
ms.custom: template-tutorial
ms.openlocfilehash: 3dc47696a820708f3118ff752cfabfce365a5153
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129369136"
---
# <a name="tutorial-integrate-cyberark-with-azure-defender-for-iot"></a>教程：将 CyberArk 与 Azure Defender for IoT 集成

本教程帮助你了解如何将 CyberArk 与 Azure Defender for IoT 集成，以及如何将 CyberArk 与 Azure Defender for IoT 配合使用。

Defender for IoT 提供 ICS 和 IIoT 网络安全平台（具有 ICS 感知威胁分析和机器学习功能）。

威胁行动者正在使用已泄露的远程访问凭据通过远程桌面和 VPN 连接访问关键的基础设施网络。 通过使用信任连接，这种方法可以轻松绕过任何 OT 周边安全机制。 凭据通常是从特权用户（例如需要进行远程访问来执行日常任务的控制工程师和合作伙伴维护人员）那里窃取的。

Defender for IoT 与 CyberARK 的集成使你可以：

- 降低未经授权的远程访问带来的 OT 风险

- 提供持续监视，并为 OT 提供特权访问安全性

- 增强事件响应、威胁搜寻和威胁建模

Defender for IoT 设备通过交换机等网络设备上的 SPAN 端口（镜像端口）连接到 OT 网络，并通过与 Defender for IoT 设备上的专用网络接口建立的单向（入站）连接来与路由器相连接。

Defender for IoT 设备中还提供了一个专用网络接口，用于进行集中管理和 API 访问。 此接口还用于与组织数据中心内部署的 CyberArk PSM 解决方案通信，以便管理特权用户和保护远程访问连接。

:::image type="content" source="media/tutorial-cyberark/architecture.png" alt-text="CyberArk PSM 解决方案部署":::

在本教程中，你将了解：

> [!div class="checklist"]
> - 在 CyberArk 中配置 PSM
> - 在 Defender for IoT 中启用集成
> - 查看和管理检测
> - 停止集成

## <a name="prerequisites"></a>先决条件

- CyberARK 版本 2.0。

- 验证你是否对企业中的所有 Defender for IoT 设备都有 CLI 访问权限。

- 一个 Azure 帐户。 如果还没有 Azure 帐户，可以[立即创建 Azure 免费帐户](https://azure.microsoft.com/free/)。

## <a name="configure-psm-cyberark"></a>配置 PSM CyberArk

CyberArk 必须配置为允许与 Defender for IoT 通信。 这种通信是通过配置 PSM 来实现的。

若要配置 PSM，请执行以下操作：

1. 找到并打开 `c:\Program Files\PrivateArk\Server\dbparam.xml` 文件。

1. 添加以下参数：

    `[SYSLOG]` <br>
    `UseLegacySyslogFormat=Yes` <br>
    `SyslogTranslatorFile=Syslog\CyberX.xsl` <br>
    `SyslogServerIP=<CyberX Server IP>` <br>
    `SyslogServerProtocol=UDP` <br>
    `SyslogMessageCodeFilter=319,320,295,378,380` <br>

1. 保存并关闭该文件。

1. 将 Defender for IoT syslog 配置文件 `CyberX.xsl` 放在 `c:\Program Files\PrivateArk\Server\Syslog\CyberX.xsl` 中。

1. 打开“服务器管理中心”。

1. 选择“停车交通灯”图标停止服务器。

    :::image type="content" source="media/tutorial-cyberark/server.png" alt-text="服务器管理中心停车交通灯图标的屏幕截图。":::

1. 选择“通行交通灯”图标启动服务器。

## <a name="enable-the-integration-in-defender-for-iot"></a>在 Defender for IoT 中启用集成

若要启用集成，需要在 Defender for IoT 管理控制台中启用 Syslog 服务器。 默认情况下，Syslog 服务器使用 UDP 端口 514 侦听系统的 IP 地址。

若要配置 Defender for IoT，请执行以下操作：

1. 在 Defender for IoT 管理控制台中，导航到“系统设置”。

1. 将“Syslog 服务器”切换为“打开”。

    :::image type="content" source="media/tutorial-cyberark/toggle.png" alt-text="syslog 服务器已切换为“打开”的屏幕截图。":::

1. （可选）通过 CLI 登录到系统以更改端口，然后导航到 `/var/cyberx/properties/syslog.properties` 并更改 `listener: 514/udp`。

## <a name="view-and-manage-detections"></a>查看和管理检测

Azure Defender for IoT 和 CyberArk PSM 之间的集成是通过 syslog 消息执行的。 这些消息由 PSM 解决方案发送到 Defender for IoT，通知 Defender for IoT 有关任何远程会话或验证失败的情况。

Defender for IoT 平台从 PSM 接收这些消息后，会将这些消息关联到它在网络中看到的数据，从而验证与网络建立的任何远程访问连接是否是由 PSM 解决方案（而不是由未经授权的用户）生成。

### <a name="view-alerts"></a>查看警报

每当 Defender for IoT 平台识别到尚未由 PSM 授权的远程会话时，它将发出 `Unauthorized Remote Session`。 为便于立即调查，警报还会显示源和目标设备的 IP 地址和名称。

若要查看警报，请执行以下操作：

1. 登录到管理控制台。

1. 从左侧面板中选择“警报”。

1. 从警报列表中，选择标题为“未经授权的远程会话”的警报。

    :::image type="content" source="media/tutorial-cyberark/unauthorized.png" alt-text="“未经授权的远程会话”警报。":::

### <a name="event-timeline"></a>事件时间线

每当 PSM 为某个远程连接授权后，该连接就会显示在 Defender for IoT 的“事件时间线”页中。 “事件时间线”页显示所有警报和通知的时间线。

若要查看事件时间线，请执行以下操作：

1. 登录到 Defender for IoT 传感器。

1. 从左侧面板中选择“事件时间线”。

1. 找到标题为“PSM 远程会话”的任何事件。

    :::image type="content" source="media/tutorial-cyberark/event.png" alt-text="“事件日志”屏幕的视图。":::

### <a name="auditing--forensics"></a>审核和取证

管理员可以通过 Defender for IoT 平台的内置数据挖掘接口查询该平台，以审计和调查远程访问会话。 此信息可用于识别已发生的所有远程访问连接，包括取证详细信息，例如源设备或目标设备、协议（RDP 或 SSH）、源用户和目标用户、时间戳，以及是否已使用 PSM 为会话授权。

若要进行审核和调查，请执行以下操作：

1. 登录到 Defender for IoT 传感器。

1. 从左侧面板中选择“数据挖掘”。

1. 选择“远程访问”。

    :::image type="content" source="media/tutorial-cyberark/data-mining.png" alt-text="数据挖掘接口的视图。":::

## <a name="stop-the-integration"></a>停止集成

在任意时间点，都可以阻止集成进行通信。

若要停止集成，请执行以下操作：

1. 在 Defender for IoT 管理控制台中，导航到“系统设置”屏幕。

1. 将“Syslog 服务器”选项切换为“关闭”。

    :::image type="content" source="media/tutorial-cyberark/toggle.png" alt-text="服务器状态的视图。":::

## <a name="clean-up-resources"></a>清理资源

没有可清理的资源。

## <a name="next-steps"></a>后续步骤

在本教程中，你已了解如何开始进行 CyberArk 集成。 请继续了解 Forescout 集成。

> [!div class="nextstepaction"]
> [“后续步骤”按钮](./tutorial-forescout.md)
