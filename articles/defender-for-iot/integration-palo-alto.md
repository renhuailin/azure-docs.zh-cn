---
title: Palo Alto 集成
titleSuffix: Azure Defender for IoT
description: 用于 IoT 的 Defender 集成了其持续的 ICS 威胁监视平台和 Palo Alto 的下一代防火墙，以更快、更高效地拦截严重威胁。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/17/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: 85a7622223861f857ce75b8136b509ba279f3d96
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/18/2021
ms.locfileid: "98557404"
---
# <a name="about-the-palo-alto-integration"></a>关于 Palo Alto 集成

用于 IoT 的 Defender 集成了其持续的 ICS 威胁监视平台和 Palo Alto 的下一代防火墙，以更快、更高效地拦截严重威胁。

以下集成类型可用：

- 自动阻塞选项：用于 IoT-Palo Alto 集成的直接 Defender

- 向中央管理系统发送阻止的建议： IoT-Panorama 集成的 Defender

## <a name="configure-immediate-blocking-by-specified-palo-alto-firewall"></a>通过指定的 Palo Alto 防火墙配置立即阻止

在关键情况下（如与恶意软件相关的警报），可以启用自动阻止。 这是通过在用于将阻止命令直接发送到特定 Palo Alto 防火墙的 IoT 的 Defender 中配置转发规则来完成的。

当使用 IoT 来识别关键威胁时，它将发送警报，其中包含阻止感染的源的选项。 在警报的详细信息中选择 " **阻止源** " 将激活转发规则，该规则将阻止命令发送到指定的 Palo Alto 防火墙。

若要配置：

1. 在左窗格中，选择 " **转发**"。

   :::image type="content" source="media/integration-paloalto/forwarding.png" alt-text="转发警报屏幕。":::

1. 选择 " **创建转发规则**"。

   :::image type="content" source="media/integration-paloalto/forward-rule.png" alt-text="创建转发规则":::

1. 若要配置 Palo Alto NGFW 转发规则：  
 
   - 定义标准规则参数，并从 " **操作** " 下拉框中选择 " **发送到 Palo Alto NGFW**"。
   
   :::image type="content" source="media/integration-paloalto/edit.png" alt-text="编辑转发规则。":::

1. 在 "操作" 窗格中，设置以下参数：

   - **主机**：输入 NGFW 服务器 IP 地址。
   - **端口**：输入 NGFW 服务器端口。
   - **用户名**：输入 NGFW 服务器用户名。
   - **密码**：输入 NGFW 服务器密码。
   - **配置**：设置以下选项，允许 Palo Alto 防火墙阻止可疑源：
     - **阻止非法函数代码**：协议冲突-违反 ICS 协议规范的非法字段值 (潜在利用) 。
     - **阻止未经授权的 plc 编程/固件更新**：未经授权的 plc 更改。
     - **阻止未授权的 PLC 停止**： (停机) 的 plc 停止。
     - **阻止与恶意软件相关的警报**：阻止 (TRITON、NotPetya 等 ) 的行业恶意软件尝试。 您可以选择 **自动阻止** 选项。 在这种情况下，将立即自动执行阻止。
     - **阻止未授权的扫描**：未经授权的扫描 (潜在的侦测) 。
     
1. 选择“提交”。

阻止可疑源： 

1. 在 " **警报** " 窗格中，选择与 Palo Alto 集成相关的警报。 此时将显示 " **警报详细信息** " 对话框。
   
   :::image type="content" source="media/integration-paloalto/unauthorized.png" alt-text="警报详细信息":::

1. 若要自动阻止可疑源，请选择 " **阻止源**"。 此时将显示 " **确认** " 对话框。

   :::image type="content" source="media/integration-paloalto/please.png" alt-text="在 &quot;确认&quot; 屏幕上确认阻止。":::

1. 在 " **确认** " 对话框中，选择 **"确定"**。 Palo Alto 防火墙阻止了可疑的源。

## <a name="sending-blocking-policies-to-palo-alto-panorama"></a>向 Palo Alto 全景发送阻止策略

用于 IoT 和 Palo Alto 网络的 Defender 具有现成的集成，可在 Palo Alto 网络 NMS、全景中自动创建新策略。 此集成要求 "全景管理员" 进行确认，并且不允许自动阻止。

此集成用于以下事件：

- **未经授权的 PLC 更改：** 对设备的阶梯逻辑或固件进行更新。 这可能表示合法活动，或试图危害设备。 通过插入恶意代码（如远程访问特洛伊木马 (RAT) 或参数导致物理进程（如旋转的工作方式）以不安全的方式进行，就会发生这种损害。

- **协议冲突：** 违反协议规范的数据包结构或字段值。 这可能表示配置错误的应用程序或恶意尝试危害设备。 例如，导致目标设备中出现缓冲区溢出情况。

- **PLC 停止：** 导致设备停止运行的命令，从而使由 PLC 控制的物理进程产生风险。

- **在 ICS 网络中找到的工业恶意软件：** 使用其本机协议（如 TRITON 和 Industroyer）操纵 ICS 设备的恶意软件。 Defender for IoT 还检测到已移动到 ICS 和 SCADA 环境（例如 Conficker、WannaCry 和 NotPetya）的恶意软件。

- **扫描恶意软件：** 侦测工具，用于收集有关预攻击阶段中系统配置的数据。 例如，Havex 特洛伊木马使用 OPC 扫描适用于设备的工业网络，它是基于 Windows 的 SCADA 系统用于与 ICS 设备通信的标准协议。

## <a name="the-process"></a>此过程

当使用 IoT 检测预配置用例时，会将 " **阻止源** " 按钮添加到警报中。 然后，当 **CyberX** 用户选择 " **阻止源** " 按钮时，通过发送预定义转发规则，IoT For IoT 会在全景上创建策略。

仅当全景管理员将其推送到网络中的相关 NGFW 时，才应用该策略。

在 IT 网络中，可能有动态 IP 地址。 因此，对于这些子网，策略必须基于 FQDN (DNS 名称) 而不是 IP 地址。 Defender for IoT 执行反向查找，并将具有动态 IP 地址的设备与 FQDN (DNS 名称) 每个配置的小时数。

此外，适用于 IoT 的 Defender 向相关的全景用户发送一封电子邮件，通知 Defender 为 IoT 创建的新策略正在等待批准。 下图显示了 IoT-Panorama 集成体系结构的 Defender。

:::image type="content" source="media/integration-paloalto/structure.png" alt-text="CyberX 集成体系结构":::

## <a name="create-panorama-blocking-policies-in-defender-for-iot-configuration"></a>在 Defender for IoT 配置中创建全景阻止策略

### <a name="to-configure-dns-lookup"></a>配置 DNS 查找

1. 在左窗格中，选择 " **系统设置**"。

1. 在 " **系统设置** " 窗格中，选择 " **DNS 设置**" :::image type="icon" source="media/integration-paloalto/settings.png"::: 。

   :::image type="content" source="media/integration-paloalto/configuration.png" alt-text="配置 DNS 设置。":::

1. 在 " **编辑 DNS 设置** " 对话框中，设置以下参数：

   - **状态**： DNS 解析程序的状态。

   - **Dns 服务器地址**：输入网络 DNS 服务器的 IP 地址或 FQDN。
   - **Dns 服务器端口**：输入用于查询 DNS 服务器的端口。
   - **子网**：设置动态 IP 地址子网范围。 用于 IoT 的范围会在 DNS 服务器中反向查找其 IP 地址，以匹配其当前的 FQDN 名称。
   - **计划反向查找**：定义计划选项，如下所示：
     - 按特定时间：指定每日执行反向查找的时间。
     - 按固定间隔 (小时) ：设置执行反向查找的频率。
   - **标签数**：指示 Defender 以使 IoT 自动将网络 IP 地址解析为设备 fqdn。 <br />若要配置 DNS FQDN 解析，请添加要显示的域标签的数目。 从左到右显示最多30个字符。
1. 选择“保存”  。
1. 若要确保 DNS 设置正确无误，请选择 " **查找测试**"。 测试可确保正确设置 DNS 服务器 IP 地址和 DNS 服务器端口。

### <a name="to-configure-a-forwarding-rule-to-blocks-suspected-traffic-with-the-palo-alto-firewall"></a>配置转发规则以使用 Palo Alto 防火墙阻止可疑的流量

1. 在左窗格中，选择 " **转发**"。 此时将显示 "转发" 窗格。

   :::image type="content" source="media/integration-paloalto/forward.png" alt-text="转发屏幕。":::

1. 在 **转发** 窗格中，选择 " **创建转发规则**"。

   :::image type="content" source="media/integration-paloalto/forward-rule.png" alt-text="创建转发规则":::

1. 若要配置 Palo Alto 全景转发规则：

   定义标准规则参数，然后从 " **操作** " 下拉框中，选择 " **发送到 Palo Alto 全景**"。 此时将显示 "操作详细信息" 窗格。

   :::image type="content" source="media/integration-paloalto/details.png" alt-text="选择操作":::

1. 在 "操作" 窗格中，设置以下参数：

   - **主机**：输入全景服务器的 IP 地址。

   - **端口**：输入全景服务器端口。
   - **用户名**：输入全景 server 用户名。
   - **密码**：输入全景 server 密码。
   - **报表地址**：定义阻塞的执行方式，如下所示：
   
     - **按 Ip 地址**：始终根据 ip 地址在全景上创建阻止策略。
     
     - **按 fqdn 或 Ip 地址**：根据 fqdn （如果存在）在全景上创建阻止策略，否则通过 IP 地址创建。
     
   - **电子邮件**：设置策略通知电子邮件的电子邮件地址
     > [!NOTE]
     > 请确保已在 IoT 中为 IoT 配置了邮件服务器。 如果未输入电子邮件地址，则用于 IoT 的 Defender 不会发送通知电子邮件。
   - 在 **警报检测时执行 DNS 查找 (复选框)**：在报表地址中设置 By FQDN 或 IP 地址选项。 默认情况下，此复选框处于选中状态。 如果仅设置了 IP 地址，则此选项处于禁用状态。
   - **配置**：设置以下选项，允许 Palo Alto 全景阻止可疑源：
   
     - **阻止非法的函数代码**：协议冲突-违反 ICS 的非法字段值，协议规范 (潜在利用) 。
     
     - **阻止未经授权的 plc 编程/固件更新**：未经授权的 plc 更改。
     
     - **阻止未授权的 PLC 停止**： (停机) 的 plc 停止。
     
     - **阻止与恶意软件相关的警报**：阻止 (TRITON、NotPetya 等 ) 的行业恶意软件尝试。 您可以选择 **自动阻止** 选项。 在这种情况下，将立即自动执行阻止。
     
     - **阻止未授权的扫描**：未经授权的扫描 (潜在的侦测) 。
     
1. 选择“提交”。

### <a name="to-block-the-suspicious-source"></a>阻止可疑源

1. 在 " **警报** " 窗格中，选择与 Palo Alto 集成相关的警报。 此时将显示 **警报的 "详细信息** " 对话框。

   :::image type="content" source="media/integration-paloalto/unauthorized.png" alt-text="警报详细信息":::        

1. 若要自动阻止可疑源，请选择 " **阻止源**"。

1. 在 " **确认** " 对话框中，选择 **"确定"。**

   :::image type="content" source="media/integration-paloalto/please.png" alt-text="确认":::

## <a name="next-steps"></a>后续步骤

了解如何 [转发警报信息](how-to-forward-alert-information-to-partners.md)。
