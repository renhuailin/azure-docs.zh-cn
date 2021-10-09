---
title: 将 Fortinet 与 Azure Defender for IoT 集成
description: 本教程介绍如何将 Azure Defender for IoT 与 Fortinet 集成。
author: ElazarK
ms.author: v-ekrieg
ms.topic: tutorial
ms.date: 09/23/2021
ms.custom: template-tutorial
ms.openlocfilehash: 30c60a71c15b8b597735c69f2f1f76178016b749
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128701381"
---
# <a name="tutorial-integrate-fortinet-with-azure-defender-for-iot"></a>教程：将 Fortinet 与 Azure Defender for IoT 集成

本教程介绍如何将 Fortinet 与 Azure Defender for IoT 集成，以及如何使用 Fortinet。

Azure Defender for IoT 通过 ICS 感知的自学引擎缓解 IIoT、ICS 和 SCADA 风险，提供有关 ICS 设备、漏洞和威胁的即时见解。  Defender for IoT 实现了这一点，而无需依赖于代理、规则、签名、专业技能或事先对环境有所了解。

Defenders for IoT 与 Fortinet 建立了技术合作伙伴关系，可以检测并阻止对 IoT 和 ICS 网络的攻击。

Fortinet 和 Azure Defender for IoT 可以阻止以下情况：

- 对可编程逻辑控制器 (PLC) 未经授权的更改。

- 通过本机协议操纵 ICS 和 IoT 设备的恶意软件。

- 侦查工具收集数据。

- 因配置错误或恶意攻击者而导致的协议冲突。

Defender for IoT 检测 IoT 和 ICS 网络中的异常行为，并将该信息传递给 FortiGate 和 FortiSIEM，如下所示：

- **可见性：** 提供的信息让 FortiSIEM 管理员能够了解之前所不能了解的 IoT 和 ICS 网络相关情况。

- 阻止恶意攻击：在异常行为对生产、利润或人员造成损坏之前，FortiGate 管理员可以使用 Defender for IoT 发现的信息来创建阻止异常行为的规则，无论该行为是由混乱的行为体还是配置错误的设备造成的。

FortiSIEM 和 Fortinet 的多供应商安全事件以及事件管理解决方案为单个可扩展的解决方案带来了可视性、相关性、自动响应和补救。

使用“业务服务”视图，管理网络和安全操作的复杂性将降低，从而释放资源并改善入侵检测。 FortiSIEM 在应用机器学习时提供交叉关联，并提供 UEBA 来改善响应，以便在入侵发生之前停止它们。

在本教程中，你将了解：

> [!div class="checklist"]
> - 在 Fortinet 中创建 API 密钥
> - 设置转发规则以阻止恶意软件相关的警报
> - 阻止可疑警报的来源
> - 向 FortiSIEM 发送 Defender for IoT 警报
> - 使用 Fortigate 防火墙阻止恶意源

如果还没有 Azure 帐户，可以[立即创建 Azure 免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

本教程需要许多先决条件。

## <a name="create-an-api-key-in-fortinet"></a>在 Fortinet 中创建 API 密钥

应用程序编程接口 (API) 密钥是唯一生成的代码，它使 API 能够识别请求访问它的应用程序或用户。 Azure Defender for Iot 和 Fortinet 需要 API 密钥才能正常通信。

在 Fortinet 中创建 API 密钥：

1. 在 FortiGate 中，导航到“系统” > “管理配置文件” 。

1. 创建具有以下权限的配置文件：

    | 参数 | 选择 |
    |--|--|
    | Security Fabric | 无 |
    | Fortiview | 无 |
    | 用户和设备 | 无 |
    | **防火墙** | 自定义 |
    | **策略** | 读取/写入 |
    | **Address** | 读取/写入  |
    | **服务** | 无 |
    | **计划** | 无 |
    | 日志和报告 | 无 |
    | **Network** | 无 |
    | **系统** | 无 |
    | 安全配置文件 | 无 |
    | **VPN** | 无 |
    | WAN 选项和缓存 | 无 |
    | WiFi 和开关 | 无 |

1. 导航到“系统” > “管理员”，然后使用以下字段创建新的 REST API 管理员  ：

    | 参数 | 说明 |
    | --------- | ----------- |
    | **用户名** | 输入转发规则名称。 |
    | **注释** | 输入要转发的最低安全级别事件。 例如，如果选择“轻微”，则将转发轻微和高于此严重性级别的任何警报。 |
    | **管理员配置文件** | 从下拉列表中，选择在上一步中定义的配置文件名称。 |
    | **PKI 组** | 将开关切换为“禁用”。 |
    | **CORS 允许源** | 将开关切换为“启用”。 |
    | **将登录限制为受信任的主机** | 添加将连接到 FortiGate 的传感器和管理控制台的 IP 地址。 |

生成 API 密钥后，请保存它，因为系统不会再次提供它。

:::image type="content" source="media/tutorial-fortinet/api-key.png" alt-text="自动生成新 API 密钥的描述的屏幕截图。":::

## <a name="set-a-forwarding-rule-to-block-malware-related-alerts"></a>设置转发规则以阻止恶意软件相关的警报

可将 FortiGate 防火墙用于阻止可疑流量。

设置转发规则以阻止恶意软件相关警报：

1. 登录 Azure Defender for IoT 管理控制台。

1. 在左窗格中选择“转发”。

    [:::image type="content" source="media/tutorial-fortinet/forwarding-view.png" alt-text="传感器中“转发”窗口选项的屏幕截图。":::](media/tutorial-fortinet/forwarding-view.png#lightbox)

1. 选择“创建转发规则”并定义以下规则参数。

    | 参数 | 说明 |
    | --------- | ----------- |
    | **名称** | 为转发规则输入一个有意义的名称。 |
    | 选择严重性 | 从下拉菜单中，选择要转发的最低安全级别事件。 例如，如果选择“轻微”，则将转发轻微和高于此严重性级别的任何警报。 |
    | **协议** | 若要选择特定协议，选择“特定”，然后选择将应用此规则的协议。 默认情况下，所有协议都会被选中。 |
    | **引擎** | 若要选择要应用此规则的特定安全引擎，请选择“特定”，然后选择引擎。 默认情况下会涉及所有安全引擎。 |
    | **系统通知** | 转发传感器的“联机”和“脱机”状态 。 仅当登录到本地管理控制台时，此选项才可用。 |

1. 在“操作”部分中，选择“添加”，然后从下拉菜单中选择“发送到 FortiGate” 。

    :::image type="content" source="media/tutorial-fortinet/fortigate.png" alt-text="“创建转发规则”窗口的“添加操作”部分的屏幕截图。":::

1. 若要配置 FortiGate 转发规则，请设置以下参数：

    :::image type="content" source="media/tutorial-fortinet/configure.png" alt-text="配置“创建转发规则”窗口的屏幕截图。":::

    | 参数 | 说明 |
    |--|--|
    | **主机** | 输入 FortiGate 服务器 IP 地址。 |
    | **API 密钥** | 输入在 FortiGate 中创建的 [API 密钥](#create-an-api-key-in-fortinet)。 |
    | **传入接口** | 输入传入接口端口。 |
    | **传出接口** | 输入传出接口端口。 |
    | **配置**| 确保以下选项中显示“√”以通过 FortiGate 防火墙阻止可疑的源： <br> - 阻止非法函数代码：协议冲突 - 非法字段值违反 ICS 协议规范（潜在漏洞） <br /> - 阻止未经授权的 PLC 编程/固件更新：未经授权的 PLC 更改 <br /> - 阻止未经授权的 PLC 停止：PLC 停止（故障时间） <br /> - 阻止与恶意软件相关的警报：阻止行业恶意软件尝试（TRITON、NotPetya 等）。 <br /> - （可选）可以选择“自动阻止”选项 。 如果选择了“自动阻止”，则会自动并立即执行阻止。 <br /> - 阻止未经授权的扫描：未经授权的扫描（潜在侦查） |

1. 选择“提交”。 

## <a name="block-the-source-of-suspicious-alerts"></a>阻止可疑警报的来源

为了防止发生进一步的事件，可能会阻止可疑警报源。

阻止可疑警报的源：

1. 登录到管理控制台，并从左侧菜单中选择“警报”。

1. 选择与 Fortinet 集成相关的警报。

1. 若要自动阻止可疑源，请选择“阻止源”。

    :::image type="content" source="media/tutorial-fortinet/block-source.png" alt-text="“警报”窗口的屏幕截图。":::

1. 在“请确认”对话框中，选择“确定”。

## <a name="send-defender-for-iot-alerts-to-fortisiem"></a>向 FortiSIEM 发送 Defender for IoT 警报

Defender for IoT 警报提供有关大量安全事件的信息，其中包括：

- 相对于已学习的基准网络活动的偏差

- 恶意软件检测

- 基于可疑操作性更改的检测

- 网络异常

- 相对于协议规范的协议偏差

可以将 Defender for IoT 配置为向 FortiSIEM 服务器发送警报，警报信息将显示在“分析”窗口中：

:::image type="content" source="media/tutorial-fortinet/analytics.png" alt-text="“分析”窗口的屏幕截图。":::

无需在 FortiSIEM 端进行任何其他配置即可分析每个 Defender for IoT 警报，它们将作为安全事件呈现在 FortiSIEM 中。 默认情况下，将显示以下事件详细信息：

:::image type="content" source="media/tutorial-fortinet/event-detail.png" alt-text="在“事件详细信息”窗口中查看事件详细信息的屏幕截图。":::

然后，可以使用 Defender for IoT 转发规则将警报信息发送到 FortiSIEM。

使用 Defender for IoT 转发规则将警报信息发送到 FortiSIEM：

1. 从传感器或管理控制台的左侧窗格中选择“转发”。

    [:::image type="content" source="media/tutorial-fortinet/forwarding-view.png" alt-text="“转发”窗口中转发规则视图的屏幕截图。":::](media/tutorial-fortinet/forwarding-view.png#lightbox)

2. 选择“创建转发规则”并定义规则的参数。

    | 参数 | 说明 |
    |--|--|
    | **名称** | 为转发规则输入一个有意义的名称。 |
    | 选择严重性 | 选择要转发的最低安全级别事件。 例如，如果选择“轻微”，则将转发轻微和高于此严重性级别的任何警报。 |
    | **协议** | 若要选择特定协议，选择“特定”，然后选择将应用此规则的协议。 默认情况下，所有协议都会被选中。 |
    | **引擎** | 若要选择要对其应用此规则的特定安全引擎，请选择“特定”，然后选择引擎。 默认情况下会涉及所有安全引擎。 |
    | **系统通知** | 转发传感器的“联机”或“脱机”状态 。 仅当登录到本地管理控制台时，此选项才可用。 |

3. 在“操作”部分，选择“发送到 FortiSIEM”。

    :::image type="content" source="media/tutorial-fortinet/forward-rule.png" alt-text="创建转发规则并选择“发送到 Fortinet”的屏幕截图。":::

4. 输入 FortiSIEM 服务器详细信息。

    :::image type="content" source="media/tutorial-fortinet/details.png" alt-text="将 FortiSIEm 详细信息添加到转发规则的屏幕截图。":::

    | 参数 | 说明 |
    | --------- | ----------- |
    | **主机** | 输入 FortiSIEM 服务器 IP 地址。 |
    | **端口** | 输入 FortiSIEM 服务器端口。 |
    | **时区** | 用于警报检测的时间戳。 |

5. 选择“提交”。

## <a name="block-a-malicious-source-using-the-fortigate-firewall"></a>使用 Fortigate 防火墙阻止恶意源

可以设置策略，以使用 Defender for IoT 中的警报在 FortiGate 防火墙中自动阻止恶意源。

:::image type="content" source="media/tutorial-fortinet/firewall.png" alt-text="FortiGate 防火墙窗口视图的屏幕截图。":::

例如，以下警报可以阻止恶意源：

:::image type="content" source="media/tutorial-fortinet/suspicion.png" alt-text="NotPetya 恶意软件可疑窗口的屏幕截图。":::

设置阻止此恶意源的 FortiGate 防火墙规则：

1. 在 FortiGate 中[创建 API 密钥](#create-an-api-key-in-fortinet)。

1. 登录 Defender for IoT 传感器或管理控制台，然后选择“转发”，[设置用于阻止与恶意软件相关警报的转发规则](#set-a-forwarding-rule-to-block-malware-related-alerts)。

1. 在 Defender for IoT 传感器或管理控制台中，选择“警报”，然后选择“[阻止恶意源](#block-a-malicious-source-using-the-fortigate-firewall)”。

1. 导航到 FortiGage“管理员”窗口，然后找到阻止的恶意源地址。

   :::image type="content" source="media/tutorial-fortinet/administrator.png" alt-text="FortiGate“管理员”窗口视图的屏幕截图。":::

   阻止策略是自动创建的，并会显示在 FortiGate“IPv4 策略”窗口中。

   :::image type="content" source="media/tutorial-fortinet/policy.png" alt-text="FortiGate IPv4 策略窗口视图的屏幕截图。":::

1. 选择策略，并确保将“启用此策略”切换到“开”位置。

   :::image type="content" source="media/tutorial-fortinet/edit.png" alt-text="FortiGate IPv4 策略编辑视图的屏幕截图。":::

## <a name="clean-up-resources"></a>清理资源

没有可清理的资源。

## <a name="next-steps"></a>后续步骤

在本教程中，你已了解如何开始进行 Fortinet 集成。 请继续了解 [Palo Alto 集成](./tutorial-palo-alto.md)。

> [!div class="nextstepaction"]
> [“后续步骤”按钮](./tutorial-palo-alto.md)
