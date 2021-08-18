---
title: 关于 Splunk 集成
description: 为了解决无法洞察 OT 网络安全性和复原能力的问题，Defender for IoT 开发了适用于 Splunk 的 Defender for IoT、IIoT 和 ICS 威胁监视应用程序，这是 Defender for IoT 与 Splunk 之间的一个原生集成，提供了用于确保 IT 和 OT 安全性的统一方法。
ms.date: 1/4/2021
ms.topic: article
ms.openlocfilehash: 28bdc6deaac09d795c45460bb211126a105b80c8
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113015216"
---
# <a name="defender-for-iot-and-ics-threat-monitoring-application-for-splunk"></a>适用于 Splunk 的 Defender for IoT 和 ICS 威胁监视应用程序

Defender for IoT 使用获得专利的、可感知 ICS 的自我学习引擎来缓解 IIoT、ICS 和 SCADA 风险。这些引擎可在不到一小时的映像时间内提供有关 ICS 设备、漏洞和威胁的即时见解，且不需要依赖代理、规则或签名、专业技能，也不需要预先了解环境。

为了解决无法洞察 OT 网络安全性和复原能力的问题，Defender for IoT 开发了适用于 Splunk 的 Defender for IoT、IIoT 和 ICS 威胁监视应用程序，这是 Defender for IoT 与 Splunk 之间的一个原生集成，提供了用于确保 IT 和 OT 安全性的统一方法。

> [!Note]
> 有关 CyberX 的参考，请参阅“Azure Defender for IoT”。

## <a name="about-the-splunk-application"></a>关于 Splunk 应用程序

该应用程序为 SOC 分析人员提供对专用 OT 协议和工业环境中部署的 IIoT 设备的多维可见性，以及 ICS 感知行为分析，以便快速检测可疑或异常行为。 该应用程序还可以在一个公司 SOC 内同时进行 IT 和 OT 事件响应。 这是一项重要的改进，因为 IT 和 OT 持续聚合可支持新的 IIoT 计划，例如智能计算机和实时智能。

Splunk 应用程序可以安装在本地，也可以在云中运行。 与 Defender for IoT 的集成支持这两种部署。

## <a name="about-the-integration"></a>关于集成

通过此原生应用程序将 Defender for IoT 与 Splunk 集成后，用户可以：

- 缩短工业和关键基础结构组织检测、调查和应对网络威胁所需的时间。

- 获取有关 OT 风险的实时智能。

- 将 Defender for IoT 警报与 Splunk Enterprise 安全威胁情报存储库相关联。

- 从单个管理平台进行监视和响应。

[:::image type="content" source="media/integration-splunk/splunk-mainpage-v2.png" alt-text="Splunk 工具的主页。":::](media/integration-splunk/splunk-mainpage-v2.png#lightbox)

:::image type="content" source="media/integration-splunk/alerts.png" alt-text="Splunk 中的警报页。":::

该应用程序允许 Splunk 管理员分析 Defender for IoT 发送的 OT 警报，并监视整个 OT 安全部署，包括下面这样的详细信息：

- 五个分析引擎中的哪一个检测到了警报。

- 哪种协议生成了警报。

- 哪个 Defender for IoT 传感器生成了警报。

- 警报的严重性级别。

- 通信的源和目标。

## <a name="requirements"></a>要求

### <a name="version-requirements"></a>版本要求

要求使用以下版本。

- Defender for IoT 2.4 及更高版本。

- Splunkbase 11 及更高版本。

- Splunk Enterprise 7.2 及更高版本。
  
## <a name="download-the-application"></a>下载应用程序

从 [Splunkbase](https://splunkbase.splunk.com/app/4313/) 下载适用于 Splunk 的 CyberX ICS 威胁监视应用程序。

## <a name="splunk-permission-requirements"></a>Splunk 权限要求

需要以下 Splunk 权限：

- 具有管理员用户角色权限的任何用户。

## <a name="send-defender-for-iot-alerts-to-splunk"></a>将 Defender for IoT 警报发送到 Splunk

Defender for IoT 警报提供大量安全事件的相关信息，其中包括：

- 相对于已学习的基准网络活动的偏差。

- 恶意软件检测。

- 基于可疑操作更改的检测。

- 网络异常。

- 相对于协议规范的协议偏差。

:::image type="content" source="media/integration-splunk/address-scan.png" alt-text="检测屏幕。":::

你可以将 Defender for IoT 配置为向 Splunk 服务器发送警报，警报信息会显示在 Splunk Enterprise 仪表板中。

:::image type="content" source="media/integration-splunk/alerts-and-details.png" alt-text="查看所有警报及其详细信息。":::

以下警报信息发送到 Splunk 服务器。

- 警报日期和时间。

- 检测到事件的 Defender for IoT 引擎：协议违规、策略违规、恶意软件、异常或操作引擎

- 警报标题。

- 警报消息。

- 警报严重性：警告、轻微、严重或致命。

- 源设备名称。

- 源设备 IP 地址。

- 目标设备名称。

- 目标设备 IP 地址。

- Defender for IoT 平台 IP 地址（主机）。

- Defender for IoT 平台设备的名称（源类型）。

下面显示了示例输出：

| 时间 | 事件 |
|--|--|
| 15/7/23，<br />晚上 9:28:31.000 | Defender for IoT 平台警报：某个设备已被 PLC 命令停止<br /><br />类型：操作违规 <br /><br />严重性：严重 <br /><br />源名称：my_device1 <br /><br />源 IP：192.168.110.131 <br /><br />目标名称：my_device2<br /><br /> 目标 IP：10.140.33.238 <br /><br />消息：使用 PLC 停止命令停止了网络设备。 在发送启动命令之前，此设备将无法运行。 10.140.33.238（一台 Siemens S7 设备）使用 PLC 停止命令停止了 192.168.110.131。<br /><br />主机：192.168.90.43 <br /><br />Sourcetype：Sensor_Agent |

## <a name="define-alert-forwarding-rules"></a>定义警报转发规则

使用 Defender for IoT 转发规则将警报信息发送到 Splunk 服务器。

可以使用相关选项基于以下信息来自定义警报规则：

- 检测到的特定协议。

- 事件的严重性。

- 检测事件的 Defender for IoT 引擎。

若要创建转发规则，请执行以下操作：

1. 从传感器或本地管理控制台的左窗格中选择“转发”。

    :::image type="content" source="media/integration-splunk/forwarding.png" alt-text="选择“创建转发警报”蓝色按钮。":::

1. 选择“创建转发规则”。 在“创建转发规则”窗口中，定义规则参数。

    :::image type="content" source="media/integration-splunk/forwarding-rule.png" alt-text="为转发规则创建规则。":::

    | 参数 | 说明 |
    |--|--|
    | **名称** | 转发规则名称。 |
    | 选择严重性 | 要转发的最低安全级别的事件。 例如，如果选择“轻微”，则会转发轻微警报以及高于此严重性级别的任何警报。 |
    | **协议** | 默认情况下，所有协议都会被选中。 若要选择特定协议，请选择“特定”并选择要对其应用此规则的协议。 |
    | **引擎** | 默认情况下会涉及所有安全引擎。 若要选择要对其应用此规则的特定安全引擎，请选择“特定”，然后选择引擎。 |
    | **系统通知** | 转发传感器联机/脱机状态。 只有登录到 Central Manager 后才能使用此选项。 |

1. 若要指示 Defender for IoT 将资产信息发送到 Splunk，请选择“操作”，然后选择“发送到 Splunk 服务器”。

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

了解如何[转发警报信息](how-to-forward-alert-information-to-partners.md)。
