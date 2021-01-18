---
title: 关于 ServiceNow 集成
titleSuffix: Azure Defender for IoT
description: 用于 ServiceNow 的 IoT ICS 管理应用程序的 Defender 为 SOC 分析人员提供了对工业环境中部署的专用的 "协议" 和 "IoT" 设备的多维可见性，同时提供了 ICS 感知的行为分析来快速检测可疑或异常行为。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/17/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: f2a4c3e79a762de19c6e8c029256cd70dedfe3dc
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/18/2021
ms.locfileid: "98557417"
---
# <a name="the-defender-for-iot-ics-management-application-for-servicenow"></a>用于 ServiceNow 的 IoT ICS 管理应用程序的 Defender

用于 ServiceNow 的 IoT ICS 管理应用程序的 Defender 为 SOC 分析人员提供了对工业环境中部署的专用的 "协议" 和 "IoT" 设备的多维可见性，同时提供了 ICS 感知的行为分析来快速检测可疑或异常行为。 这是一项重要的改进，因为它的持续汇聚和支持新的 IoT 计划（例如智能计算机和实时智能）。

应用程序还可以在一个公司 SOC 内实现 IT 和事件响应。

## <a name="about-defender-for-iot"></a>关于用于 IoT 的 Defender

Defender for IoT 提供了由蓝色团队专家构建的唯一 ICS 和 IoT 网络安全平台，其中包含用于保护关键国家基础结构的跟踪记录，以及具有获专利的可通过 ICS 识别的威胁分析和机器学习的唯一平台。 用于 IoT 的 Defender 提供：

- 有关 ICS 设备布局的即时见解，其中包含有关属性的大量详细信息。

- ICS 感知深层嵌入的有关协议、设备、应用程序及其行为的知识。

- 立即深入了解漏洞和已知的零天威胁。

- 一种自动化 ICS 威胁建模技术，可通过专有分析预测目标 ICS 攻击的最可能路径。

> [!Note]
> 对 CyberX 的引用是指用于 IoT 的 Azure Defender。

## <a name="about-the-integration"></a>关于集成

用于 IoT 与 ServiceNow 集成的 Defender 为 IoT 和 OT 提供了一个新的集中可见性、监视和控制级别。 这些桥接平台实现了对以前无法访问的 ICS & IoT 设备的自动设备可见性和威胁管理。

### <a name="threat-management"></a>威胁管理

用于 IoT ICS 管理应用程序的 Defender 可帮助：

- 缩短工业和关键基础结构组织检测、调查和应对网络威胁所需的时间。

- 获取有关其风险的实时智能。

- 将用于 IoT 警报的 Defender 与 ServiceNow 威胁监视和事件管理工作流相关联。

- 利用 ServiceNow 平台上的其他服务和应用程序触发 ServiceNow 票证和工作流。

ICS 和 SCADA 安全威胁由 Defender 用于 IoT 安全引擎，它们为恶意软件攻击、网络和安全策略的偏差以及操作和协议异常提供即时警报响应。 有关发送到 ServiceNow 的警报详细信息，请参阅 [警报报告](#alert-reporting)。

### <a name="device-visibility-and-management"></a>设备可见性和管理

ServiceNow 配置管理数据库 (CMDB) 使用一组由 IoT 平台推送的丰富设备属性进行扩充和补充。 这可确保对设备布局的全面且连续的可见性，并使你可以监视和响应单一窗格。 有关发送到 ServiceNowSee 的设备属性的详细信息，请参阅 [在 ServiceNow 中查看用于 IoT 检测的 Defender](#view-defender-for-iot-detections-in-servicenow)。

## <a name="system-requirements-and-architecture"></a>系统要求和体系结构

本文介绍：

- **软件要求**  
- **体系结构**

## <a name="software-requirements"></a>软件要求

- ServiceNow 服务管理版本3.0。2

- 用于 IoT 的 Defender 修补2.8.11.1 或更高版本

> [!Note]
> 如果你已在使用用于 IoT 和 ServiceNow 集成的 Defender，并使用本地管理控制台进行升级，则应从 ServiceNow 中清除从早期接收到 IoT 传感器的数据。

## <a name="architecture"></a>体系结构

### <a name="on-premises-management-console-architecture"></a>本地管理控制台体系结构

本地管理控制台为发送到 ServiceNow 的所有设备和警报信息提供统一的源。

可以设置本地管理控制台，以便与 ServiceNow 的一个实例进行通信。 本地管理控制台使用 REST API 将传感器数据推送到 IoT 应用程序的 Defender。

如果要将系统设置为使用本地管理控制台，请在传感器中禁用 ServiceNow 同步、转发规则和代理配置（如果已设置）。

应为本地管理控制台设置这些配置。 本文介绍了配置说明。

### <a name="sensor-architecture"></a>传感器体系结构

如果要将环境设置为包括传感器与 ServiceNow 之间的直接通信，则对于每个传感器，定义 ServiceNow 同步、转发规则和代理配置 (如果需要代理) 。

建议使用本地管理控制台来设置与 ServiceNow 通信的集成。

## <a name="create-access-tokens-in-servicenow"></a>在 ServiceNow 中创建访问令牌

本文介绍如何在 ServiceNow 中创建访问令牌。 需要令牌才能与用于 IoT 的 Defender 通信。

为 IoT 转发规则创建 Defender 时，需要提供 **客户端 ID** 和 **客户端机密** ，以便将警报信息转发给 Servicenow，并为 iot 配置 defender 以将设备属性推送到 servicenow 表。

## <a name="set-up-defender-for-iot-to-communicate-with-servicenow"></a>设置用于 IoT 与 ServiceNow 通信的 Defender

本文介绍如何设置用于 IoT 的 Defender 以与 ServiceNow 通信。

### <a name="send-defender-for-iot-alert-information"></a>发送用于 IoT 警报信息的 Defender

本文介绍如何为 IoT 配置 Defender，以将警报信息推送到 ServiceNow 表。 有关发送的警报数据的信息，请参阅 [警报报告](#alert-reporting)。

防守 for IoT 警报在 ServiceNow 中显示为安全事件。

定义用于将警报信息发送到 ServiceNow 的 IoT *转发* 规则 Defender。

定义规则：

1. 在 "用于 IoT 的 Defender" 左侧窗格中，选择 " **转发**"。  

1. 选择 :::image type="content" source="media/integration-servicenow/plus.png" alt-text="加号图标按钮。"::: 图标。 此时将打开 "创建转发规则" 对话框。  

    :::image type="content" source="media/integration-servicenow/forwarding-rule.png" alt-text="创建转发规则":::

1. 添加规则名称。

1. 定义用于 IoT 的 Defender 将触发转发规则的条件。 使用转发规则条件可帮助确定和管理从用于 IoT 的到 ServiceNow 的 Defender 发送的信息量。 可以使用以下选项：

    - **严重性级别：** 这是要转发的最小安全级别事件。 例如，如果选择 " **次要** "，则会转发次警报，以及此严重级别以上的任何警报。 级别由 Defender 为 IoT 预定义。

    - **协议：** 仅当检测到的流量在特定协议上运行时，才触发转发规则。 从下拉列表中选择所需的协议，或选择 "全部"。

    - **引擎：** 选择所需的引擎或选择 "全部"。 将发送所选引擎发出的警报。

1. 验证是否选择了 " **报告警报通知** "。

1. 在 "操作" 部分中，选择 " **添加** "，然后选择 " **ServiceNow**"。

    :::image type="content" source="media/integration-servicenow/select-servicenow.png" alt-text="从下拉选项中选择 &quot;ServiceNow&quot;。":::

1. 输入 ServiceNow 操作参数：

    :::image type="content" source="media/integration-servicenow/parameters.png" alt-text="填写 ServiceNow 操作参数":::

1. 在 " **操作** " 窗格中，设置以下参数：

  | 参数 | 说明 |
  |--|--|
  | 域 | 输入 ServiceNow 服务器的 IP 地址。 |
  | 用户名 | 输入 ServiceNow 服务器用户名。 |
  | 密码 | 输入 ServiceNow 服务器密码。 |
  | 客户端 ID | 在 ServiceNow 的 " **应用程序注册表** " 页中输入你为 Defender for IoT 接收的客户端 ID。 |
  | 客户端机密 | 在 ServiceNow 的 " **应用程序注册表** " 页中输入为 Defender for IoT 创建的客户端密码字符串。 |
  | 报表类型 | **事件**：转发在 ServiceNow 中显示的警报的列表以及每个警报的事件 ID 和简短说明。<br /><br />**Defender For IoT 应用程序**：转发完整警报信息，包括传感器详细信息、引擎、源和目标地址。 信息将转发到 ServiceNow 应用程序上的 IoT。 |

1. 选择“保存”  。 用于 IoT 警报的防守在 ServiceNow 中显示为事件。

### <a name="send-defender-for-iot-device-attributes"></a>为 IoT 设备属性发送 Defender

本文介绍如何为 IoT 配置 Defender，以将范围较广的设备属性范围推送到 ServiceNow 表。 有关推送到 ServiceNow 的信息类型的详细信息，请参阅 **_清单信息_* _。

若要将属性发送到 ServiceNow，你必须将本地管理控制台映射到 ServiceNow 实例。 这可以确保 IoT 平台的 Defender 能够与实例进行通信和身份验证。

添加 ServiceNow 实例：

1. 登录到用于 IoT 本地管理控制台的 Defender。

1. 依次选择 "*系统设置*" 和 "本地管理控制台集成" 部分中的 " **ServiceNow** "。

      :::image type="content" source="media/integration-servicenow/servicenow.png" alt-text="选择 &quot;ServiceNow&quot; 按钮。":::

1. 在 "ServiceNow 同步" 对话框中输入以下同步参数。

    :::image type="content" source="media/integration-servicenow/sync.png" alt-text="&quot;ServiceNow 同步&quot; 对话框。":::

     参数 | 说明 |
    |--|--|
    | 启用同步 | 在定义参数后启用和禁用同步。 |
    | 同步频率 (分钟)  | 默认情况下，每60分钟会将信息推送到 ServiceNow。 最小值为5分钟。 |
    | ServiceNow 实例 | 输入 ServiceNow 实例 URL。 |
    | 客户端 ID | 在 ServiceNow 的 " **应用程序注册表** " 页中输入你为 Defender for IoT 接收的客户端 ID。 |
    | 客户端机密 | 在 ServiceNow 的 " **应用程序注册表** " 页中输入为 Defender for IoT 创建的客户端密码字符串。 |
    | 用户名 | 输入此实例的用户名。 |
    | 密码 | 输入此实例的密码。 |

1. 选择“保存”  。

## <a name="verify-communication"></a>验证通信

通过查看 *上次同步* 日期来验证本地管理控制台是否已连接到 ServiceNow 实例。

:::image type="content" source="media/integration-servicenow/sync-confirmation.png" alt-text="查看上次同步，验证是否发生了通信。":::

## <a name="set-up-the-integrations-using-the-https-proxy"></a>使用 HTTPS 代理设置集成

设置用于 IoT 和 ServiceNow 集成的 Defender 时，本地管理控制台和 ServiceNow 服务器使用端口443进行通信。 如果 ServiceNow 服务器位于代理后面，则无法使用默认端口。

用于 IoT 的 Defender 通过启用用于集成的默认端口的更改在 ServiceNow 集成中支持 HTTPS 代理。

配置代理：

1. 在本地管理控制台中编辑全局属性：  
    `sudo vim /var/cyberx/properties/global.properties`

2. 添加以下参数：

    - `servicenow.http_proxy.enabled=1`

    - `servicenow.http_proxy.ip=1.179.148.9`

    - `servicenow.http_proxy.port=59125`

3. 保存并退出。

4. 运行以下命令：`sudo monit restart all`

配置后，使用配置的代理转发所有 ServiceNow 数据。

## <a name="download-the-defender-for-iot-application"></a>下载适用于 IoT 应用程序的 Defender

本文介绍如何下载该应用程序。

访问用于 IoT 应用程序的 Defender：

1. 导航到 <https://store.servicenow.com/>

2. 搜索 `Defender for IoT` 或 `CyberX IoT/ICS Management` 。

   :::image type="content" source="media/integration-servicenow/search-results.png" alt-text="在搜索栏中搜索 &quot;CyberX&quot;。":::

3. 选择应用程序。

   :::image type="content" source="media/integration-servicenow/cyberx-app.png" alt-text="从列表中选择应用。":::

4. 选择 " **请求应用"。**

   :::image type="content" source="media/integration-servicenow/sign-in.png" alt-text="用凭据登录到应用程序。":::

5. 登录并下载该应用程序。

## <a name="view-defender-for-iot-detections-in-servicenow"></a>查看 ServiceNow 中的 IoT 检测的 Defender

本文介绍 ServiceNow 中提供的设备属性和警报信息。

查看设备属性：

1. 登录到 ServiceNow。

2. 导航到 " **CyberX Platform**"。

3. 导航到 " **清单** " 或 " **警报**"。

   [:::image type="content" source="media/integration-servicenow/alert-list.png" alt-text="清单或警报":::](media/integration-servicenow/alert-list.png#lightbox)

## <a name="inventory-information"></a>清单信息

配置管理数据库 (CMDB) 由 Defender 为 IoT 发送到 ServiceNow 的数据进行充实和补充。 通过在 ServiceNow 的 CMDB 配置项表中添加或更新设备属性，适用于 IoT 的 Defender 可以触发 ServiceNow 工作流和业务规则。

该边栏选项卡中提供以下信息：

- 设备属性，例如检测到的设备 MAC、OS、供应商或协议。

- 固件信息，例如固件版本和序列号。

- 已连接的设备信息，例如源和目标之间的流量方向。

### <a name="devices-attributes"></a>设备属性

本文介绍推送到 ServiceNow 的设备特性。

| 项 | 说明 |
|--|--|
| 设备 | 检测到流量的传感器的名称。 |
| ID | Defender 为 IoT 分配的设备 ID。 |
| 名称 | 设备名称。 |
| IP 地址 | 设备 IP 地址。 |
| 类型 | 设备类型，例如交换机、PLC、historian 或域控制器。 |
| MAC 地址 | 设备 MAC 地址。 |
| 操作系统 | 设备操作系统。 |
| Vendor | 设备供应商。 |
| 协议 | 设备生成的流量中检测到的协议。 |
| 所有者 | 输入设备所有者的名称。 |
| 位置 | 输入设备的物理位置。 |

在此视图中查看连接到设备的设备。

查看连接设备：

1. 选择一个设备，然后选择 " **设备" 中列出的设备** 。

    :::image type="content" source="media/integration-servicenow/appliance.png" alt-text="从列表中选择所需的设备。":::

1. 在 " **设备详细信息** " 对话框中，选择 " **连接的设备**"。

### <a name="firmware-details"></a>固件详细信息

本文介绍推送到 ServiceNow 的设备固件信息。

| 项 | 说明 |
|--|--|
| 设备 | 检测到流量的传感器的名称。 |
| 设备 | 设备名称。 |
| 地址 | 设备 IP 地址。 |
| 模块地址 | 设备型号和槽号或 ID。 |
| 串行 | 设备序列号。 |
| 建模 | 设备型号。 |
| 版本 | 固件版本号。 |
| 其他数据 | 供应商定义的有关固件的更多数据，例如设备类型。 |

### <a name="connection-details"></a>连接详细信息

本文介绍推送到 ServiceNow 的设备连接信息。

:::image type="content" source="media/integration-servicenow/connections.png" alt-text="设备的连接信息":::

| 项 | 说明 |
|--|--|
| 设备 | 检测到流量的传感器的名称。 |
| 方向 | 流量的方向。 <br /> <br /> - **一种方法** 表明目标是服务器，源是客户端。 <br /> <br /> - **两种方式** 表示源和目标均为服务器，或者客户端是未知的。 |
| 源设备 ID | 与连接的设备通信的设备的 IP 地址。 |
| 源设备名称 | 与连接的设备通信的设备的名称。 |
| 目标设备 ID | 已连接设备的 IP 地址。 |
| 目标设备名称 | 连接的设备的名称。 |

## <a name="alert-reporting"></a>警报报告

当 IoT 引擎的防守检测到需要关注的网络流量和行为变化时，会触发警报。 有关每个引擎生成的警报类型的详细信息，请参阅 [关于警报引擎](#about-alert-engines)。

本文介绍推送到 ServiceNow 的设备警报信息。

| 项 | 说明 |
|--|--|
| 创建 | 生成警报的时间和日期。 |
| 引擎 | 检测到事件的引擎。 |
| 标题 | 警报标题。 |
| 说明 | 警报说明。 |
| 协议 | 在流量中检测到的协议。 |
| 严重性 | Defender 为 IoT 定义的警报严重性。 |
| 设备 | 检测到流量的传感器的名称。 |
| 源名称 | 源名称。 |
| 源 IP 地址| 源 IP 地址。 |
| 目标名称 | 目标名称。 |
| 目标 IP 地址 | 目标 IP 地址。 |
| 代理人 | 输入分配给票证的个体的名称。 |

### <a name="updating-alert-information"></a>正在更新警报信息

选择 "创建" 列中的条目以查看表单中的警报信息。 你可以更新警报详细信息，并将警报分配给个人以查看和处理。

[:::image type="content" source="media/integration-servicenow/alert.png" alt-text="查看警报的信息。":::](media/integration-servicenow/alert.png#lightbox)

### <a name="about-alert-engines"></a>关于警报引擎

本文介绍每个引擎触发器的警报类型。

| 警报类型 | 说明 |
|--|--|
| 策略冲突警报 | 当策略冲突引擎检测到以前获知的流量的偏差时触发。 例如： <br /><br />-检测到新设备。 <br /><br />-在设备上检测到新的配置。 <br /><br />-未定义为编程设备的设备将执行编程更改。 <br /><br />-固件版本已更改。 |
| 协议冲突警报 | 在协议冲突引擎检测到不符合协议规范的数据包结构或字段值时触发。 |
| 操作警报 | 当操作引擎检测到网络操作事件或设备故障时触发。 例如，使用 Stop PLC 命令停止网络设备，或者传感器上的接口停止监视流量。 |
| 恶意软件警报 | 当恶意软件引擎检测到恶意网络活动（例如，Conficker）时触发。 |
| 异常警报 | 异常引擎检测到偏差时触发。 例如，设备正在执行网络扫描，但未定义为扫描设备。 |

## <a name="next-steps"></a>后续步骤

了解如何 [转发警报信息](how-to-forward-alert-information-to-partners.md)。
