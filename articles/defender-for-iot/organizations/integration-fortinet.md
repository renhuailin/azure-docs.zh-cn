---
title: 关于 Fortinet 集成
description: Defender for IoT 和 Fortinet 建立了技术合作，以便检测和停止对 IoT 和 ICS 网络的攻击。
ms.date: 1/17/2021
ms.topic: article
ms.openlocfilehash: dedd144d1f09d84fc44e0ddeaa9298b0ebd8704b
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113015244"
---
# <a name="defender-for-iot-and-fortinet-iiot-and-ics-threat-detection--prevention"></a>Defender for IoT 及 Fortinet IIoT 和 ICS 威胁检测和防护

Defender for IoT 使用获得专利的 ICS 感知的自学习引擎来缓解 IIoT、ICS 和 SCADA 风险，这些引擎可在不到一小时的时间内提供有关 ICS 设备、漏洞和威胁的即时见解，且不需要依赖于代理、规则或签名、专业技能，也不需要预先了解环境。

Defender for IoT 和 Fortinet 建立了技术合作，以便检测和停止对 IoT 和 ICS 网络的攻击。

Fortinet 和 Defender for IoT 一起可阻止：

- 对可编程逻辑控制器的未经授权的更改。

- 通过本机协议操纵 ICS 和 IoT 设备的恶意软件。
- 侦查工具收集数据。
- 因配置错误或恶意攻击者而导致的协议冲突。

## <a name="defender-for-iot-and-fortigate-joint-solution"></a>Defender for IoT 和 FortiGate 联合解决方案

Defender for IoT 检测 IoT 和 ICS 网络中的异常行为，并将该信息传递给 FortiGate 和 FortiSIEM，如下所示：

- **可见性：** 提供的信息让 FortiSIEM 管理员能够了解之前所不能了解的 IoT 和 ICS 网络相关情况。

- **阻止恶意攻击：** FortiGate 管理员可以使用 Defender for IoT 发现的信息来创建规则，以在异常行为对生产、利润和人员造成损失/损害之前停止该行为，无论该行为是由混乱参与者还是错误配置的设备所导致的。

## <a name="the-defender-for-iot-platform"></a>Defender for IoT 平台

Defender for IoT 平台自动发现任何非托管的 IoT 和 ICS 设备并识别其指纹，同时持续监视目标攻击和恶意软件。 风险和漏洞管理功能包括自动威胁建模，以及有关终结点和网络漏洞的全面报告，并提供按风险划分优先级的缓解建议。  

Defender for IoT 平台是无代理的非侵入式平台，它易于部署，可在连接到网络后不到一小时内提供可操作的见解。

## <a name="fortinet-fortisiem"></a>Fortinet FortiSIEM

有效的安全性要求实时了解所有设备和所有基础结构，同时还要看到上下文。 还要了解什么设备表示存在威胁，以及设备有什么功能可供你管理企业所面临的威胁，而不是多个安全工具所制造的干扰。

终结点、IoT、基础结构、安全工具、应用程序、VM 和云是管理员需要保护并持续监视的一些内容。

FortiSIEM 和 Fortinet 的多供应商安全事件和事件管理解决方案将所有这些结合在一起，让你可在可缩放的单个解决方案中实现可见性、关联性、自动响应和修正。

使用“业务服务”视图，管理网络和安全操作的复杂性将降低，从而释放资源并改善入侵检测。 FortiSIEM 在应用机器学习时提供交叉关联，并提供 UEBA 来改善响应，以便在入侵发生之前停止它们。

## <a name="fortinet-fortigate-next-generation-firewalls"></a>Fortinet FortiGate 下一代防火墙

FortiGate 的下一代防火墙 (NGFW) 利用出自由 AI 提供支持的 FortiGuard 实验室中的专门安全处理器和威胁情报安全服务，为明文和加密流量提供一流的保护和高性能的检查。

下一代防火墙通过全面了解应用程序、用户和网络来降低成本和复杂性，并提供一流的安全性。 作为 Fortinet Security Fabric 的一个有机组成部分，下一代防火墙可在多供应商环境中与 Fortinet 的全面安全组合和合作伙伴安全解决方案进行通信，以共享威胁情报并改善安全状况。

## <a name="use-cases"></a>用例

### <a name="prevent-unauthorized-changes-to-programmable-logic-controllers"></a>防止对可编程逻辑控制器的未经授权的更改

组织使用可编程逻辑控制器 (PLC) 来管理物理流程，例如工厂中的机器人臂、风力发电场中的旋转涡轮机和核电站中的离心机。

PLC 的梯形逻辑或固件的更新可以表示合法的活动，也可以表示通过插入恶意代码尝试破坏设备。  Defender for IoT 可以检测对 PLC 未经授权的更改，然后将该更改的相关信息传递给 FortiSIEM 和 FortiGate。 有了这些信息，FortSIEM 管理员可以决定如何最好地缓解这种情况。 一个缓解选项是在 FortiGate 中创建一个停止与受影响设备进一步通信的规则。

### <a name="stop-ransomware-before-it-damages-iot-and-ics-networks"></a>在勒索软件损坏 IoT 和 ICS 网络之前停止该勒索软件

Defender for IoT 持续监视 IoT 和 ICS 网络以发现由勒索软件（例如 LockerGoga、WannaCry 和 NotPetya）导致的行为。 当与 FortiSIEM 和 FortiGate 集成时，Defender for IoT 可提供这些类型的勒索软件的相关情况，以便 ForiSIEM 操作员可以看到恶意软件的位置，并且 FortiGate 管理员可阻止勒索软件的传播和造成更多破坏。

## <a name="set-sending-defender-for-iot-alerts-to-fortisiem"></a>就将 Defender for IoT 警报发送到 FortiSIEM 进行设置

Defender for IoT 警报提供有关大量安全事件的信息，其中包括：

- 相对于已学习的基准网络活动的偏差
- 恶意软件检测
- 基于可疑操作性更改的检测
- 网络异常
- 相对于协议规范的协议偏差

:::image type="content" source="media/integration-fortinet/address-scan.png" alt-text="“检测到地址扫描”窗口的屏幕截图。":::

可以将 Defender for IoT 配置为向 FortiSIEM 服务器发送警报，警报信息将显示在“分析”窗口中：

:::image type="content" source="media/integration-fortinet/analytics.png" alt-text="“分析”窗口的屏幕截图。":::

无需在 FortiSIEM 端进行任何其他配置即可分析每个 Defender for IoT 警报，它们将作为安全事件呈现在 FortiSIEM 中。 默认情况下，将显示以下事件详细信息：

:::image type="content" source="media/integration-fortinet/event-detail.png" alt-text="在“事件详细信息”窗口中查看事件详细信息。":::

## <a name="define-alert-forwarding-rules"></a>定义转发警报规则

使用 Defender for IoT 转发规则将警报信息发送到 FortiSIEM。

可以使用相关选项基于以下信息来自定义警报规则：

- 检测到的特定协议。

- 事件的严重性。

- 检测到事件的 Defender for IoT 引擎。

创建转发规则

1. 从传感器或本地管理控制台的左窗格中，选择“转发”。

    [:::image type="content" source="media/integration-fortinet/forwarding-view.png" alt-text="在“转发”窗口中查看转发规则。":::](media/integration-fortinet/forwarding-view.png#lightbox)

2. 选择“创建转发规则”。 在“创建转发规则”窗口中，定义规则的参数。

    :::image type="content" source="media/integration-fortinet/new-rule.png" alt-text="新建转发规则窗口。":::

    | 参数 | 说明 |
    |--|--|
    | **名称** | 转发规则名称。 |
    | **选择严重性** | 要转发的最低安全级别的事件。 例如，如果选择“轻微”，则将转发轻微和高于此严重性级别的任何警报。 |
    | **协议** | 默认情况下，所有协议都会被选中。<br><br>若要选择特定协议，请选择“特定”并选择要对其应用此规则的协议。 |
    | **引擎** | 默认情况下会涉及所有安全引擎。<br><br>若要选择要对其应用此规则的特定安全引擎，请选择“特定”，然后选择引擎。 |
    | **系统通知** | 转发传感器联机/脱机状态。 仅当登录到本地管理控制台时，此选项才可用。 |

3. 若要指示 Defender for IoT 将警报信息发送到 FortiSIEM，请选择“操作”，然后选择“发送到 FortiSIEM” 。

    :::image type="content" source="media/integration-fortinet/forward-rule.png" alt-text="创建转发规则并选择“发送到 Fortinet”。":::

4. 输入 FortiSIEM 服务器详细信息。

    :::image type="content" source="media/integration-fortinet/details.png" alt-text="将 FortiSIEm 详细信息添加到转发规则":::

    | 参数 | 说明 |
    | --------- | ----------- |
    | **主机** | FortiSIEM 服务器地址。 |
    | 端口 | FortiSIEM 服务器端口。 |
    | **时区** | 用于警报检测的时间戳。 |

5. 选择“提交”。

## <a name="set-blocking-suspected-traffic-using-fortigate-firewall"></a>使用 Fortigate 防火墙就阻止可疑流量进行设置

可以基于 Defender for IoT 警报在 FortiGate 防火墙中设置自动阻止策略。

:::image type="content" source="media/integration-fortinet/firewall.png" alt-text="FortiGate 防火墙窗口的视图。":::

例如，以下警报可以阻止恶意源：

:::image type="content" source="media/integration-fortinet/suspicion.png" alt-text="可疑的 NotPetya 恶意软件窗口":::

设置阻止此恶意源的 FortiGate 防火墙规则：

1. 在 FortiGate 中，创建 Defender for IoT 转发规则所需的 API 密钥。 有关详细信息，请参阅[在 FortiGate 中创建 API 密钥](#create-an-api-key-in-fortigate)。

1. 在 Defender for IoT“转发”中，设置用于阻止与恶意软件相关的警报的转发规则。 有关详细信息，请参阅[使用 FortiGate 防火墙阻止可疑流量](#block-suspected-traffic-using-the-fortigate-firewall)。

1. 在 Defender for IoT 中，“警报”将阻止恶意源。 有关详细信息，请参阅[阻止可疑源](#block-the-suspicious-source)。

    恶意源地址显示在 FortiGate“管理员”窗口中。

   :::image type="content" source="media/integration-fortinet/administrator.png" alt-text="FortiGate“管理员”窗口视图。":::

   阻止策略是自动创建的，并显示在 FortiGate“IPv4 策略”窗口中。

   :::image type="content" source="media/integration-fortinet/policy.png" alt-text="FortiGate“IPv4 策略”窗口视图。":::

   :::image type="content" source="media/integration-fortinet/edit.png" alt-text="FortiGate IPv4 策略编辑视图。":::

## <a name="create-an-api-key-in-fortigate"></a>在 FortiGate 中创建 API 密钥

1. 在 FortiGate 中选择“系统” > “管理配置文件” 。

1. 创建具有以下权限的配置文件：

    :::image type="content" source="media/integration-fortinet/admin-profile.png" alt-text="自动生成的计算机说明":::

1. 选择“系统” > “管理员”，并创建新的“REST API 管理员”  。

    :::image type="content" source="media/integration-fortinet/cellphone.png" alt-text="自动生成的手机说明":::

    | 参数 | 说明 |
    | --------- | ----------- |
    | **用户名** | 转发规则名称。 |
    | **注释** | 要转发的最低安全级别的事件。 例如，如果选择“轻微”，则将转发轻微和高于此严重性级别的任何警报。 |
    | **管理员配置文件** | 从下拉列表中，选择在上一步中定义的配置文件名称。 |
    | **PKI 组** | 禁用。 |
    | **CORS 允许源** | 启用。 |
    | **将登录限制为受信任的主机** | 添加将连接到 FortiGate 的传感器和 CM 的 IP 地址。 |

1. 保存 API 密钥。

    :::image type="content" source="media/integration-fortinet/api-key.png" alt-text="手机说明自动生成新 API 密钥":::

## <a name="block-suspected-traffic-using-the-fortigate-firewall"></a>使用 FortiGate 防火墙阻止可疑流量

1. 在左窗格中选择“转发”。

    [:::image type="content" source="media/integration-fortinet/forwarding-view.png" alt-text="传感器中的“转发”窗口选项。":::](media/integration-fortinet/forwarding-view.png#lightbox)

1. 选择“创建转发规则”并定义规则参数。

    :::image type="content" source="media/integration-fortinet/new-rule.png" alt-text="“创建转发规则”窗口的屏幕截图":::

    | 参数 | 说明 |
    | --------- | ----------- |
    | **名称** | 转发规则名称。 |
    | 选择严重性 | 要转发的最低安全级别的事件。 例如，如果选择“轻微”，则将转发轻微和高于此严重性级别的任何警报。 |
    | **协议** | 默认情况下，所有协议都会被选中。<br><br>若要选择特定协议，请选择“特定”并选择要对其应用此规则的协议。 |
    | **引擎** | 默认情况下会涉及所有安全引擎。<br><br>若要选择要对其应用此规则的特定安全引擎，请选择“特定”，然后选择引擎。 |
    | **系统通知** | 转发传感器联机和脱机状态。 仅当登录到本地管理控制台时，此选项才可用。 |

1. 若要指示 Defender for IoT 将防火墙规则转发到 FortiGate，请选择“操作”，然后选择“发送到 FortiGate” 。

    :::image type="content" source="media/integration-fortinet/fortigate.png" alt-text="“创建转发规则”窗口，并选择了“发送到 FortiGate”":::

1. 配置 FortiGate 转发规则：

    :::image type="content" source="media/integration-fortinet/configure.png" alt-text="配置“创建转发规则”窗口":::

1. 在“操作”窗格中，设置以下参数：

    | 参数 | 说明 |
    |--|--|
    | 主机 | FortiGate 服务器 IP 地址类型。 |
    | 端口 | FortiGate 服务器端口类型。 |
    | 用户名 | FortiGate 服务器用户名类型。 |
    | API 密钥 | 输入在 FortiGate 中创建的 API 密钥。 |
    | 传入接口| 定义执行阻止的方式：<br /><br />**通过 IP 地址**：始终根据 IP 地址在 Panorama 上创建阻止策略。<br /><br />**通过 FQDN 或 IP 地址**：根据 FQDN（如果存在）在 Panorama 上创建阻止策略，否则则根据 IP 地址进行创建。| 
    | 传出接口 |设置用于策略通知电子邮件的电子邮件地址。 <br /><br /> **注意**：请确保已在 XSense 中配置邮件服务器。 如果未输入任何电子邮件地址，XSense 不会发送通知电子邮件。| 
    |配置| 设置以下选项，以允许 FortiGate 防火墙阻止可疑源： <br /><br />**阻止非法函数代码**：违反 ICS 协议规范（潜在攻击）的协议冲突 - 非法字段值<br /><br />**阻止未经授权的 PLC 编程/固件更新**：未经授权的 PLC 更改<br /><br />**阻止未经授权的 PLC 停止**：PLC 停止（故障时间）<br /><br />**阻止与恶意软件相关的警报**：阻止行业恶意软件尝试（TRITON、NotPetya 等）。 可以选择“自动阻止”选项。 在这种情况下，将立即自动执行阻止。<br /><br />*阻止未经授权的扫描*：未经授权的扫描（潜在侦查）

1. 选择“提交”。

## <a name="block-the-suspicious-source"></a>阻止可疑源

1. 在“警报”窗格中，选择与 Fortinet 集成相关的警报。

    :::image type="content" source="media/integration-fortinet/unauthorized.png" alt-text="未经授权的 PLC 编程窗口":::

1. 若要自动阻止可疑源，请选择“阻止源”。

    :::image type="content" source="media/integration-fortinet/confirm.png" alt-text="确认窗口。":::

1. 在“请确认”对话框中，选择“确定” 。

## <a name="next-steps"></a>后续步骤

了解如何[转发警报信息](how-to-forward-alert-information-to-partners.md)。
