---
title: 将 Palo Alto 与 Azure Defender for IoT 集成
description: Defender for IoT 已经将其持续 ICS 威胁监视平台与 Palo Alto 的下一代防火墙集成，以更快且更有效地阻止严重威胁。
ms.date: 09/29/2021
ms.topic: tutorial
ms.openlocfilehash: ca6160920a19fc653132d6fdc8cccd878da7cee5
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129236780"
---
# <a name="tutorial-integrate-palo-alto-with-azure-defender-for-iot"></a>教程：将 Palo Alto 与 Azure Defender for IoT 集成

本教程介绍如何将 Palo Alto 与 Azure Defender for IoT 集成，以及如何使用 Palo Alto。

Defender for IoT 已经将其持续 ICS 威胁监视平台与 Palo Alto 的下一代防火墙集成，以更快且更有效地阻止严重威胁。

下列集成类型可供使用：

- 自动阻止选项：Defender for IoT 与 Palo Alto 的直接集成。

- 向中央管理系统发送阻止建议：Defender for IoT 与 Panorama 的集成。

在本教程中，你将了解：

> [!div class="checklist"]
> - 通过指定的 Palo Alto 防火墙配置即时阻止
> - 在 Defender for IoT 中创建 Panorama 阻止策略

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

### <a name="panorama-permissions"></a>Panorama 权限

- 由 Panorama 管理员确认是否允许自动阻止。

## <a name="configure-immediate-blocking-by-a-specified-palo-alto-firewall"></a>通过指定的 Palo Alto 防火墙配置即时阻止

在某些情况（例如与恶意软件相关的警报）下，可以启用自动阻止。 Defenders for IoT 转发规则用于向特定的 Palo Alto 防火墙直接发送阻止命令。

当 Defender for IoT 识别到关键威胁时，它会发送警报，其中包含“阻止受感染的源”选项。 通过选择警报详细信息中的“阻止源”，激活转发规则，这会将阻止命令发送到特定的 Palo Alto 防火墙。

配置即时阻止：

1. 在左窗格中选择“转发”。

1. 选择“创建转发规则”。

    :::image type="content" source="media/tutorial-palo-alto/forwarding.png" alt-text="转发警报屏幕的屏幕截图。":::

1. 从“操作”下拉菜单中，选择“发送到 Palo Alto NGFW”。

   :::image type="content" source="media/tutorial-palo-alto/forward-rule.png" alt-text="“创建转发规则”屏幕的屏幕截图。":::

1. 在“操作”窗格中，设置以下参数：

   - **主机**：输入 NGFW 服务器 IP 地址。
   - **端口**：输入 NGFW 服务器端口。
   - **用户名**：输入 NGFW 服务器用户名。
   - **密码**：输入 NGFW 服务器密码。
   - **配置**：设置以下选项，以允许 Palo Alto 防火墙阻止可疑源：
     - **阻止非法函数代码**：违反 ICS 协议规范（潜在攻击）的协议冲突 - 非法字段值。
     - **阻止未经授权的 PLC 编程/固件更新**：未经授权的 PLC 更改。
     - **阻止未经授权的 PLC 停止**：PLC 停止（故障时间）。
     - **阻止与恶意软件相关的警报**：阻止行业恶意软件尝试（TRITON、NotPetya 等）。 可以选择“自动阻止”选项。 在这种情况下，将立即自动执行阻止。
     - **阻止未经授权的扫描**：未经授权的扫描（潜在侦查）。

    :::image type="content" source="media/tutorial-palo-alto/edit.png" alt-text="“编辑转发规则”屏幕的屏幕截图。":::

1. 选择“提交”。

然后，需要阻止任何可疑的源。

阻止可疑的源：

1. 导航到“警报”窗格，选择与 Palo Alto 集成相关的警报。

1. 若要自动阻止可疑源，请选择“阻止源”。 “请确认”对话框随即出现。

    :::image type="content" source="media/tutorial-palo-alto/unauthorized.png" alt-text="用于阻止未授权源的“阻止源”按钮的屏幕截图。":::

1. 选择“确定”。

可疑源现已由 Palo Alto 防火墙阻止。

## <a name="create-panorama-blocking-policies-in-defender-for-iot"></a>在 Defender for IoT 中创建 Panorama 阻止策略

Defender for IoT 和 Palo Alto Networks 的集成，会在 Palo Alto Networks 的 NMS 和 Panorama 中自动创建新的策略。

下表显示了此集成可以用于哪些事件：

| 事件类型 | 说明 |
|--|--|
|未经授权的 PLC 更改 | 对设备的阶梯逻辑或固件的更新。 这可能表示合法活动或危害设备的尝试。 例如，恶意代码（如远程访问特洛伊木马 (RAT)）或导致物理进程（如旋转的涡轮机）以不安全方式运行的参数。 |
|协议冲突 | 违反协议规范的数据包结构或字段值。 这可能表示配置错误的应用程序或危害设备的恶意尝试。 例如，导致目标设备中出现缓冲区溢出情况。 |
|PLC 停止 | 导致设备停止运行的命令，从而使由 PLC 控制的物理进程处于风险之中。 |
|在 ICS 网络中发现工业恶意软件 | 使用其本机协议操纵 ICS 设备的恶意软件，例如 TRITON 和 Industroyer。 Defender for IoT 还会检测横向移动到 ICS 和 SCADA 环境的 IT 恶意软件。 例如 Conficker、WannaCry 和 NotPetya。 |
|扫描恶意软件 | 侦查工具，用于收集关于预攻击阶段中系统配置的数据。 例如，Havex 特洛伊木马使用 OPC 扫描设备的工业网络，OPC 是基于 Windows 的 SCADA 系统用于与 ICS 设备通信的标准协议。 |

Defender for IoT 检测到预配置的用例时，“阻止源”按钮会被添加到警报。 然后在 CyberX 用户选择“阻止源”按钮时，Defender for IoT 通过发送预定义的转发规则在 Panorama 上创建策略。

只有在 Panorama 管理员将其推送到网络中的相关 NGFW 时，才会应用该策略。

IT 网络中可能存在动态 IP 地址。 因此，对于这些子网，策略必须基于 FQDN（DNS 名称）而不是 IP 地址。 Defender for IoT 执行反向查找，并在每个配置的小时数内将具有动态 IP 地址的设备与其 FQDN（DNS 名称）相匹配。

此外，Defender for IoT 发送电子邮件到相关 Panorama 用户，通知由 Defender for IoT 创建的新策略正在等待批准。 下图显示 Defender for IoT 与 Panorama 的集成体系结构。

:::image type="content" source="media/tutorial-palo-alto/structure.png" alt-text="CyberX-Panorama 集成体系结构的屏幕截图。":::

在 Defender for IoT 中创建 Panorama 阻止策略的第一步是配置 DNS 查找。

配置 DNS 查找：

1. 在左窗格中，选择“系统设置”。

1. 选择“DNS设置”:::image type="icon" source="media/tutorial-palo-alto/settings.png"::: 按钮。

1. 在“编辑 DNS 设置”对话框中，设置以下参数：

   - **状态**：DNS 解析程序的状态。

   - **DNS 服务器地址**：输入网络 DNS 服务器的 IP 地址或 FQDN。
   - **DNS 服务器端口**：输入用于查询 DNS 服务器的端口。
   - **子网**：设置动态 IP 地址子网范围。 Defender for IoT 在 DNS 服务器中反向查找其 IP 地址以匹配其当前 FQDN 名称的范围。
   - **计划反向查找**：定义计划选项，如下所示：
     - 按特定时间：指定每日执行反向查找的时间。
     - 按固定间隔（小时）：设置执行反向查找频率。
   - **标签数**：指示 Defender for IoT 自动将网络 IP 地址解析为设备 FQDN。 <br />若要配置 DNS FQDN 解析，请添加要显示的域标签数。 从左到右最多显示 30 个字符。

    :::image type="content" source="media/tutorial-palo-alto/configuration.png" alt-text="“配置 DNS 设置”屏幕的屏幕截图。":::

1. 选择“保存”。

若要确保 DNS 的设置正确，请选择“查找测试”。 测试可确保正确设置 DNS 服务器 IP 地址和 DNS 服务器端口。

## <a name="block-suspicious-traffic-with-the-palo-alto-firewall"></a>使用 Palo Alto 防火墙阻止可疑流量

需使用 Palo Alto 防火墙阻止可疑流量。 可以通过使用 Defender for IoT 中的转发规则阻止可疑流量。

通过 Palo Alto 防火墙使用 Defender for IoT 转发规则阻止可疑流量：

1. 在左窗格中选择“转发”。

1. 选择“创建转发规则”。

1. 从“操作”下拉菜单中，选择“发送到 Palo Alto Panorama” 。

1. 在“操作”窗格中，设置以下参数：

   - **主机**：输入 Panorama 服务器的 IP 地址。

   - **端口**：输入 Panorama 服务器的端口。

   - **用户名**：输入 Panorama 服务器的用户名。

   - **密码**：输入 Panorama 服务器的密码。

   - **报表地址**：定义执行阻止的方式，如下所示：

     - **通过 IP 地址**：始终根据 IP 地址在 Panorama 上创建阻止策略。

     - **通过 FQDN 或 IP 地址**：根据 FQDN（如果存在）在 Panorama 上创建阻止策略，否则根据 IP 地址进行创建。

   - **电子邮件**：设置用于策略通知电子邮件的电子邮件地址

    > [!NOTE]
    > 请确保已在 Defender for IoT 中配置邮件服务器。 如果未输入任何电子邮件地址，Defender for IoT 不会发送通知电子邮件。

   - 检测到警报时执行 DNS 查找（复选框）：在报表地址中设置“FQDN 或 IP 地址”选项后。 默认情况下，此复选框为选中状态。 如果仅设置 IP 地址，则会禁用此选项。

   - 配置：设置以下选项，以允许 Palo Alto Panorama 阻止可疑源：

     - **阻止非法函数代码**：违反 ICS 协议规范（潜在攻击）的协议冲突 - 非法字段值。

     - **阻止未经授权的 PLC 编程/固件更新**：未经授权的 PLC 更改。

     - **阻止未经授权的 PLC 停止**：PLC 停止（故障时间）。

     - **阻止与恶意软件相关的警报**：阻止行业恶意软件尝试（TRITON、NotPetya 等）。 可以选择“自动阻止”选项。 在这种情况下，将立即自动执行阻止。

     - **阻止未经授权的扫描**：未经授权的扫描（潜在侦查）。

    :::image type="content" source="media/tutorial-palo-alto/details.png" alt-text="“选择操作”屏幕的屏幕截图。":::

1. 选择“提交”。

然后，需要阻止可疑的源。

阻止可疑的源：

1. 在“警报”窗格中，选择与 Palo Alto 集成相关的警报。 “警报详细信息”对话框随即出现。

  :::image type="content" source="media/tutorial-palo-alto/unauthorized.png" alt-text="警报屏幕的屏幕截图，选择与 Palo Alto 相关的警报，然后选择“阻止源”。":::

1. 若要自动阻止可疑源，请选择“阻止源”。

1. 选择“确定”。

## <a name="clean-up-resources"></a>清理资源

没有可清理的资源。

## <a name="next-step"></a>后续步骤

在本教程中，你已了解如何开始进行 Palo Alto 集成。

> [!div class="nextstepaction"]
> [“后续步骤”按钮](tutorial-splunk.md)
