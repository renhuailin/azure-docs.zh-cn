---
title: 关于 Fortinet 集成
titleSuffix: Azure Defender for IoT
description: 用于 IoT 和 Fortinet 的 Defender 已经建立了一项技术合作关系，以便检测和停止对 IoT 和 ICS 网络的攻击。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/17/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: 313db90d4c9be30ef588b00caf1d6e4ce32b113b
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/18/2021
ms.locfileid: "98557381"
---
# <a name="defender-for-iot-and-fortinet-iiot-and-ics-threat-detection--prevention"></a>用于 IoT 和 Fortinet IIoT 和 ICS 威胁检测 & 防护的 Defender

用于 IoT 的 Defender 可通过获得专利的、可通过 ICS 识别的自助学习引擎，在不到一小时的时间内提供有关 ICS 设备、漏洞和威胁的即时见解，而不依赖于代理、规则或签名、专业技能或以前的环境知识，从而减少了 IIoT 和 ICS 和 SCADA 风险。

用于 IoT 和 Fortinet 的 Defender 已经建立了一项技术合作关系，以便检测和停止对 IoT 和 ICS 网络的攻击。

同时，Fortinet 和 Defender for IoT 会阻止：

- 对可编程逻辑控制器的未经授权的更改。

- 通过其本机协议操纵 ICS 和 IoT 设备的恶意软件。
- 侦测工具收集数据。
- 因配置错误或恶意攻击者而导致的协议冲突。

## <a name="defender-for-iot-and-fortigate-joint-solution"></a>用于 IoT 和 FortiGate 联合解决方案的 Defender

Defender for IoT 检测 IoT 和 ICS 网络中的异常行为，并将该信息传递给 FortiGate 和 FortiSIEM，如下所示：

- **可见性：** Defender 为 IoT 提供的信息使 FortiSIEM 的管理员能够看到之前不可见的 IoT 和 ICS 网络。

- **阻止恶意攻击：** FortiGate 管理员可以使用用于 IoT 的信息来创建规则，以停止异常行为，无论该行为是由混乱参与者还是错误配置的设备导致，在导致生产、利润或人员损坏之前都是如此。

## <a name="the-defender-for-iot-platform"></a>用于 IoT 平台的 Defender

用于 IoT platform 的 Defender autodiscovers 和指纹任何非托管 IoT 和 ICS 设备，同时持续监视目标攻击和恶意软件。 风险和漏洞管理功能包括自动威胁建模，以及有关终结点和网络漏洞的全面报告，并提供风险优先级的缓解建议。  

用于 IoT 平台的 Defender 是无代理、无干扰且易于部署，可在连接到网络后，提供可操作的见解。

## <a name="fortinet-fortisiem"></a>Fortinet FortiSIEM

有效的安全性要求对所有设备和所有基础结构进行实时可见，同时还提供上下文。 什么是威胁，哪些设备能够管理企业面临的威胁，而不是多个安全工具创建的干扰。

终结点、IoT、基础结构、安全工具、应用程序、VM 和云是管理员需要保护的一些东西，并经常进行监视。

FortiSIEM 和 Fortinet 的多供应商安全事件和事件管理解决方案将所有这些事件结合在一起，可在可扩展的单个解决方案中实现可见性、相关、自动响应和修正。

使用 "业务服务" 视图，管理网络和安全操作的复杂性降低，从而释放资源，提高破坏性检测。 FortiSIEM 在应用机器学习和 UEBA 时提供交叉相关性，以便在事件发生之前停止违规。

## <a name="fortinet-fortigate-next-generation-firewalls"></a>Fortinet FortiGate 下一代防火墙

FortiGate 的下一代防火墙 (Ngfw) 利用 AI 支持的 FortiGuard 实验室中专门构建的安全处理器和威胁智能安全服务，提供一流的保护、清晰发送短信和加密流量的高性能检查。

下一代防火墙通过全面了解应用程序、用户和网络，降低成本和复杂性，并提供一流的安全性。 作为 Fortinet Security Fabric 下一代防火墙的一个有机组成部分，可在 Fortinet 的全面安全项目中进行通信，并在多供应商环境中进行合作伙伴安全解决方案，以共享威胁情报并改善安全状况。

## <a name="use-cases"></a>用例

### <a name="prevent-unauthorized-changes-to-programmable-logic-controllers"></a>防止对可编程逻辑控制器的未经授权的更改

组织使用可编程逻辑控制器 (Plc) 管理物理流程，例如工厂中的机器人臂、在风场中旋转涡轮机和 centrifuges。

PLC 逻辑或 PLC 的固件更新可以表示合法活动，或通过插入恶意代码来尝试破坏设备。  Defender for IoT 可以检测对 Plc 的未经授权更改，然后将该更改的相关信息传递给 FortiSIEM 和 FortiGate。 有了这些信息，FortSIEM 管理员可以决定如何最好地缓解此解决方案。 一个缓解选项是在 FortiGate 中创建一个规则，该规则停止与受影响设备的进一步通信。

### <a name="stop-ransomware-before-it-damages-iot-and-ics-networks"></a>在其损坏 IoT 和 ICS 网络之前，停止勒索软件

用于 IoT 的 Defender 持续监视 IoT 和 ICS 网络的行为，例如 LockerGoga、WannaCry 和 NotPetya。 当与 FortiSIEM 和 FortiGate 集成时，Defender for IoT 可提供有关这些类型的勒索软件的信息，以便 ForiSIEM 操作员可以看到恶意软件的位置，而 FortiGate 管理员可阻止勒索软件传播和随心所欲更多破坏。

## <a name="set-sending-defender-for-iot-alerts-to-fortisiem"></a>将用于 IoT 警报的 Defender 发送到 FortiSIEM

防守 for IoT 警报提供有关大量安全事件的信息，其中包括：

- 与已学习的基准网络活动的偏差
- 恶意软件检测
- 基于可疑操作更改的检测
- 网络异常
- 协议规定与协议的偏差

:::image type="content" source="media/integration-fortinet/address-scan.png" alt-text="&quot;检测到地址扫描&quot; 窗口的屏幕截图。":::

你可以配置用于 IoT 的 Defender 以将警报发送到 FortiSIEM 服务器，其中警报信息显示在 "分析" 窗口中：

:::image type="content" source="media/integration-fortinet/analytics.png" alt-text="分析窗口的屏幕截图。":::

在 FortiSIEM 端无需任何其他配置即可分析每个 Defender for IoT 警报，它们将作为安全事件出现在 FortiSIEM 中。 默认情况下，将显示以下事件详细信息：

:::image type="content" source="media/integration-fortinet/event-detail.png" alt-text="在 &quot;事件详细信息&quot; 窗口中查看你的事件详细信息。":::

## <a name="define-alert-forwarding-rules"></a>定义警报转发规则

使用 Defender 的 IoT 转发规则将警报信息发送到 FortiSIEM。

选项可用于自定义基于以下内容的警报规则：

- 检测到特定协议。

- 事件的严重性。

- 用于检测事件的 IoT 引擎的 Defender。

创建转发规则

1. 从传感器或本地管理控制台的左窗格中，选择 " **转发**"。

    [:::image type="content" source="media/integration-fortinet/forwarding-view.png" alt-text="在转发窗口中查看转发规则。":::](media/integration-fortinet/forwarding-view.png#lightbox)

2. 选择 " **创建转发规则**"。 在 " **创建转发规则** " 窗口中，定义规则的参数。

    :::image type="content" source="media/integration-fortinet/new-rule.png" alt-text="&quot;创建新转发规则&quot; 窗口。":::

    | 参数 | 说明 |
    |--|--|
    | **名称** | 转发规则名称。 |
    | **选择严重性** | 要转发的最小安全级别事件。 例如，如果选择 " **次要** "，则会转发次警报和高于此严重级别的任何警报。 |
    | **协议** | 默认情况下，所有协议都处于选中状态。<br><br>若要选择特定协议，请选择 " **特定** 协议"，然后选择要应用此规则的协议。 |
    | **引擎** | 默认情况下，所有安全引擎都涉及到。<br><br>若要选择应用此规则的特定安全引擎，请选择 " **特定** "，然后选择引擎。 |
    | **系统通知** | 转发传感器联机/脱机状态。 仅当你登录到本地管理控制台时，此选项才可用。 |

3. 若要指示将 IoT 发送到 FortiSIEM，请选择 " **操作** "，然后选择 " **发送到 FortiSIEM**"。

    :::image type="content" source="media/integration-fortinet/forward-rule.png" alt-text="创建转发规则并选择 &quot;发送到 Fortinet&quot;。":::

4. 输入 FortiSIEM 服务器的详细信息。

    :::image type="content" source="media/integration-fortinet/details.png" alt-text="将 FortiSIEm 详细信息添加到转发规则":::

    | 参数 | 说明 |
    | --------- | ----------- |
    | **主机** | FortiSIEM 服务器地址。 |
    | 端口 | FortiSIEM 服务器端口。 |
    | **时区** | 警报检测的时间戳。 |

5. 选择“提交”。

## <a name="set-blocking-suspected-traffic-using-fortigate-firewall"></a>使用 Fortigate 防火墙设置阻止怀疑的流量

你可以基于 Defender for IoT 警报在 FortiGate 防火墙中自动设置阻止策略。

:::image type="content" source="media/integration-fortinet/firewall.png" alt-text="FortiGate 防火墙窗口视图的视图。":::

例如，以下警报可以阻止恶意源：

:::image type="content" source="media/integration-fortinet/suspicion.png" alt-text="NotPetya 恶意软件怀疑窗口":::

若要设置阻止此恶意来源的 FortiGate 防火墙规则：

1. 在 FortiGate 中，创建用于 IoT 转发规则的 Defender 所需的 API 密钥。 有关详细信息，请参阅 [在 FortiGate 中创建 API 密钥](#create-an-api-key-in-fortigate)。

1. 在 "用于 IoT **转发** 的 Defender" 中，设置用于阻止与恶意软件相关的警报的转发规则。 有关详细信息，请参阅 [使用 FortiGate 防火墙阻止怀疑的流量](#block-suspected-traffic-using-the-fortigate-firewall)。

1. 在用于 IoT 的 Defender 中， **警报** 会阻止恶意源。 有关详细信息，请参阅 [阻止可疑源](#block-the-suspicious-source)。

    恶意源地址将显示在 "FortiGage **管理员** " 窗口中。

   :::image type="content" source="media/integration-fortinet/administrator.png" alt-text="FortiGate 管理员窗口视图。":::

   阻止策略是自动创建的，并显示在 "FortiGate **IPv4 策略** " 窗口中。

   :::image type="content" source="media/integration-fortinet/policy.png" alt-text="FortiGate IPv4 策略窗口视图。":::

   :::image type="content" source="media/integration-fortinet/edit.png" alt-text="FortiGate IPv4 策略编辑视图。":::

## <a name="create-an-api-key-in-fortigate"></a>在 FortiGate 中创建 API 密钥

1. 在 FortiGate 中，选择 "**系统**  >  **管理配置文件**"。

1. 创建具有以下权限的配置文件：

    :::image type="content" source="media/integration-fortinet/admin-profile.png" alt-text="自动生成的计算机说明":::

1. 选择 "**系统**  >  **管理员**" 并创建一个新的 **REST API 管理员**。

    :::image type="content" source="media/integration-fortinet/cellphone.png" alt-text="自动生成的手机说明":::

    | 参数 | 说明 |
    | --------- | ----------- |
    | **用户名** | 转发规则名称。 |
    | **注释** | 要转发的最小安全级别事件。 例如，如果选择 "次要"，则会转发次警报和高于此严重级别的任何警报。 |
    | **管理员配置文件** | 从下拉列表中，选择你在上一步中定义的配置文件名称。 |
    | **PKI 组** | 禁用。 |
    | **CORS 允许源** | 启用。 |
    | **限制登录到受信任的主机** | 添加将连接到 FortiGate 的传感器和 CMs 的 IP 地址。 |

1. 保存 API 密钥。

    :::image type="content" source="media/integration-fortinet/api-key.png" alt-text="手机说明自动生成新的 API 密钥":::

## <a name="block-suspected-traffic-using-the-fortigate-firewall"></a>使用 FortiGate 防火墙阻止怀疑的流量

1. 在左窗格中，选择 " **转发**"。

    [:::image type="content" source="media/integration-fortinet/forwarding-view.png" alt-text="传感器中的 &quot;转发&quot; 窗口选项。":::](media/integration-fortinet/forwarding-view.png#lightbox)

1. 选择 " **创建转发规则** " 并定义规则参数。

    :::image type="content" source="media/integration-fortinet/new-rule.png" alt-text="&quot;创建转发规则&quot; 窗口的屏幕截图":::

    | 参数 | 说明 |
    | --------- | ----------- |
    | **名称** | 转发规则名称。 |
    | **选择严重性** | 要转发的最小安全级别事件。 例如，如果选择 " **次要** "，则会转发次警报和高于此严重级别的任何警报。 |
    | **协议** | 默认情况下，所有协议都处于选中状态。<br><br>若要选择特定协议，请选择 " **特定** 协议"，然后选择要应用此规则的协议。 |
    | **引擎** | 默认情况下，所有安全引擎都涉及到。<br><br>若要选择应用此规则的特定安全引擎，请选择 " **特定** "，然后选择引擎。 |
    | **系统通知** | 转发传感器 *联机和脱机* 状态。 仅当你登录到本地管理控制台时，此选项才可用。 |

1. 若要指示用于 IoT 的 Defender 向 FortiGate 发送防火墙规则，请选择 " **操作** "，然后选择 " **发送到 FortiGate**"。

    :::image type="content" source="media/integration-fortinet/fortigate.png" alt-text="&quot;创建转发规则&quot; 窗口并选择 &quot;发送到 FortiGate&quot;":::

1. 配置 FortiGate 转发规则：

    :::image type="content" source="media/integration-fortinet/configure.png" alt-text="配置 &quot;创建转发规则&quot; 窗口":::

1. 在 " **操作** " 窗格中，设置以下参数：

    | 参数 | 描述 |
    |--|--|
    | 主机 | FortiGate 服务器 IP 地址类型。 |
    | 端口 | FortiGate 服务器端口类型。 |
    | 用户名 | FortiGate 服务器用户名类型。 |
    | API 密钥 | 输入在 FortiGate 中创建的 API 密钥。 |
    | 传入接口| 定义阻塞的执行方式：<br /><br />**按 Ip 地址**：始终根据 ip 地址在全景上创建阻止策略。<br /><br />**按 fqdn 或 Ip 地址**：根据 fqdn （如果存在）在全景上创建阻止策略，否则为 "ip 地址"。| 
    | 传出接口 |设置策略通知电子邮件的电子邮件地址。 <br /><br /> **注意**：请确保已在 XSense 中配置邮件服务器。 如果未输入电子邮件地址，则 XSense 不会发送通知电子邮件。| 
    |配置| 设置以下选项，以允许 FortiGate 防火墙阻止可疑源： <br /><br />**阻止非法函数代码**：协议冲突-违反 ICS 协议规范的非法字段值 (潜在攻击) <br /><br />**阻止未经授权的 plc 编程/固件更新**：未经授权的 plc 更改<br /><br />**阻止未授权的 PLC stop**： PLC 停止 (停机时间) <br /><br />**阻止与恶意软件相关的警报**：阻止 (TRITON、NotPetya 等 ) 的工业恶意软件尝试。 您可以选择 **自动阻止** 选项。 在这种情况下，将立即自动执行阻止。<br /><br />*阻止未授权的扫描*：未经授权的扫描 (可能的侦测) 

1. 选择“提交”。

## <a name="block-the-suspicious-source"></a>阻止可疑源

1. 在 " **警报** " 窗格中，选择与 Fortinet 集成相关的警报。

    :::image type="content" source="media/integration-fortinet/unauthorized.png" alt-text="未经授权的 PLC 编程窗口":::

1. 若要自动阻止可疑源，请选择 " **阻止源**"。

    :::image type="content" source="media/integration-fortinet/confirm.png" alt-text="确认窗口。":::

1. 在 " **确认** " 对话框中，选择 **"确定"**。

## <a name="next-steps"></a>后续步骤

了解如何 [转发警报信息](how-to-forward-alert-information-to-partners.md)。
