---
title: 关于 Splunk 集成
titleSuffix: Azure Defender for IoT
description: 为了解决对网络的安全性和复原能力的不可见情况，Defender for IoT 为 Splunk 的 IoT、IIoT 和 ICS 威胁监视应用程序开发了 Defender，这是用于 IoT 和 Splunk 的 Defender 之间的本机集成，可实现统一的方法和安全性。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/4/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: 91d877d644b4b5ca7231f5f81f9163a0fd3cbe25
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/18/2021
ms.locfileid: "98557385"
---
# <a name="defender-for-iot-and-ics-threat-monitoring-application-for-splunk"></a>适用于 Splunk 的 IoT 和 ICS 威胁监视应用程序的 Defender

使用适用于 IIoT、ics 和 SCADA 的专利引擎，可通过获得专利的、可通过 ICS 识别的自助学习引擎立即获得有关 ICS 设备、漏洞和威胁的即时见解，无需依靠代理、规则或签名、专业技能，或提前了解环境。

为了解决对网络的安全性和复原能力的不可见情况，Defender for IoT 为 Splunk 的 IoT、IIoT 和 ICS 威胁监视应用程序开发了 Defender，这是用于 IoT 和 Splunk 的 Defender 之间的本机集成，可实现统一的方法和安全性。

> [!Note]
> 对 CyberX 的引用是指用于 IoT 的 Azure Defender。

## <a name="about-the-splunk-application"></a>关于 Splunk 应用程序

该应用程序为 SOC 分析人员提供了对工业环境中部署的专用的协议和 IIoT 设备的多维可见性，同时提供了 ICS 感知行为分析来快速检测可疑或异常行为。 应用程序还可以在一个公司 SOC 内实现 IT 和事件响应。 这是一项重要的改进，因为它的持续汇聚和支持新的 IIoT 计划（例如智能计算机和实时智能）。

可以在本地安装 Splunk 应用程序，也可以在云中运行该应用程序。 与 Defender for IoT 的集成支持这两种部署。

## <a name="about-the-integration"></a>关于集成

通过本机应用程序将 Defender 用于 IoT 和 Splunk，使用户能够：

- 缩短工业和关键基础结构组织检测、调查和应对网络威胁所需的时间。

- 获取有关其风险的实时智能。

- 将用于 IoT 警报的 Defender 与 Splunk Enterprise Security 威胁智能存储库相关联。

- 监视和响应单一窗格。

[:::image type="content" source="media/integration-splunk/splunk-mainpage-v2.png" alt-text="Splunk 工具的主页。":::](media/integration-splunk/splunk-mainpage-v2.png#lightbox)

:::image type="content" source="media/integration-splunk/alerts.png" alt-text="Splunk 中的 &quot;警报&quot; 页。":::

该应用程序允许 Splunk 管理员分析用于 IoT 发送的警报，并监视整个 OT 安全部署，包括以下详细信息：

- 其中五个分析引擎检测到警报。

- 生成警报的协议。

- IoT 传感器的哪个 Defender 生成了警报。

- 警报的严重级别。

- 通信的源和目标。

## <a name="requirements"></a>要求

### <a name="version-requirements"></a>版本要求

以下版本是要求。

- 用于 IoT 版本2.4 及更高版本的 Defender。

- Splunkbase 版本11及更高版本。

- Splunk Enterprise 版本7.2 及更高版本。
  
## <a name="download-the-application"></a>下载应用程序

从 [Splunkbase](https://splunkbase.splunk.com/app/4313/)下载 *适用于 Splunk 应用程序的 CyberX ICS 威胁监视*。

## <a name="splunk-permission-requirements"></a>Splunk 权限要求

以下 Splunk 权限是必需的：

- 具有 *管理员* 用户角色权限的任何用户。

## <a name="send-defender-for-iot-alerts-to-splunk"></a>将 Defender 用于 IoT 警报发送到 Splunk

Defender for IoT 警报提供了有关大量安全事件的信息，其中包括：

- 与已学习的基准网络活动的偏差。

- 恶意软件检测。

- 基于可疑操作更改的检测。

- 网络异常。

- 协议与协议规范的偏差。

:::image type="content" source="media/integration-splunk/address-scan.png" alt-text="检测屏幕。":::

你可以配置用于 IoT 的 Defender 以将警报发送到 Splunk 服务器，其中警报信息显示在 Splunk Enterprise 仪表板中。

:::image type="content" source="media/integration-splunk/alerts-and-details.png" alt-text="查看所有警报及其详细信息。":::

以下警报信息将发送到 Splunk 服务器。

- 警报的日期和时间。

- 检测到事件的用于 IoT 引擎的 Defender：协议冲突、策略冲突、恶意软件、异常或操作引擎。

- 警报标题。

- 警报消息。

- 警报的严重性： Warning、小调、主编或严重。

- 源设备名称。

- 源设备 IP 地址。

- 目标设备名称。

- 目标设备 IP 地址。

- 用于 IoT 平台 IP 地址的 Defender (主机) 。

- 用于 IoT 平台设备的 Defender 的名称 (源类型) 。

示例输出如下所示：

| 时间 | 事件 |
|--|--|
| 7/23/15<br />9：28： 31.000 PM | **Defender For IoT 平台警报**：某个设备已被 PLC 命令停止<br /><br />**类型**：操作冲突 <br /><br />**严重性**：主要 <br /><br />**源名称**： my_device1 <br /><br />**源 IP**：192.168.110.131 <br /><br />**目标名称**： my_device2<br /><br /> **目标 IP**：10.140.33.238 <br /><br />**消息**：使用 Stop PLC 命令停止了网络设备。 在发送启动命令之前，此设备将无法运行。 通过使用 PLC 停止命令，10.140.33.238 (Siemens S7 设备) 停止了192.168.110.131。<br /><br />**主机**：192.168.90.43 <br /><br />**Sourcetype**： Sensor_Agent |

## <a name="define-alert-forwarding-rules"></a>定义警报转发规则

将 Defender 用于 IoT *转发规则* ，以将警报信息发送到 Splunk 服务器。

选项可用于自定义基于以下内容的警报规则：

- 检测到特定协议。

- 事件的严重性。

- 用于检测事件的 IoT 引擎的 Defender。

若要创建转发规则：

1. 从传感器或本地管理控制台的左窗格中，选择 " **转发"。**

    :::image type="content" source="media/integration-splunk/forwarding.png" alt-text="选择蓝色按钮 &quot;创建转发警报&quot;。":::

1. 选择 " **创建转发规则**"。 在 " **创建转发规则** " 窗口中，定义规则参数。

    :::image type="content" source="media/integration-splunk/forwarding-rule.png" alt-text="为转发规则创建规则。":::

    | 参数 | 说明 |
    |--|--|
    | **名称** | 转发规则名称。 |
    | **选择严重性** | 要转发的最小安全级别事件。 例如，如果选择 "次要"，则会转发次警报和高于此严重级别的任何警报。 |
    | **协议** | 默认情况下，所有协议都处于选中状态。 若要选择特定协议，请选择 " **特定** 协议"，然后选择要应用此规则的协议。 |
    | **引擎** | 默认情况下，所有安全引擎都涉及到。 若要选择应用此规则的特定安全引擎，请选择 " **特定** "，然后选择引擎。 |
    | **系统通知** | 转发传感器联机/脱机状态。 此选项仅在已登录到中央管理器的情况下可用。 |                                            |

1. 若要指示用于 IoT 的 Defender 将资产信息发送到 Splunk，请选择 " **操作**"，然后选择 " **发送到 Splunk 服务器**"。

1. 输入以下 Splunk 参数。

    :::image type="content" source="media/integration-splunk/parameters.png" alt-text="应在此屏幕上输入的 Splunk 参数。":::

    | 参数 | 说明 |
    |--|--|
    | **主机** | Splunk 服务器地址 |
    | 端口 | 8089 |
    | **用户名** | Splunk 服务器用户名 |
    | **密码** | Splunk 服务器密码 |

1. 选择“提交”

## <a name="next-steps"></a>后续步骤

了解如何 [转发警报信息](how-to-forward-alert-information-to-partners.md)。
