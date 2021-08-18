---
title: 关于 ServiceNow 集成
description: 用于 ServiceNow 的 Defender for IoT ICS 管理应用程序为 SOC 分析人员提供了对专用 OT 协议和工业环境中部署的 IoT 设备的多维可见性，以及 ICS 感知行为分析来快速检测可疑或异常行为。
ms.date: 1/17/2021
ms.topic: article
ms.openlocfilehash: 6e39c3d018003983f6dc5b5e16a9791de84d6005
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113015229"
---
# <a name="the-defender-for-iot-ics-management-application-for-servicenow"></a>用于 ServiceNow 的 Defender for IoT ICS 管理应用程序

用于 ServiceNow 的 Defender for IoT ICS 管理应用程序为 SOC 分析人员提供了对专用 OT 协议和工业环境中部署的 IoT 设备的多维可见性，以及 ICS 感知行为分析来快速检测可疑或异常行为。 这是一项重要的演进，因为 IT 和 OT 正在持续聚合以支持新的 IoT 计划，例如智能计算机和实时智能。

该应用程序还可以在一个公司 SOC 内同时实现 IT 和 OT 事件响应。

## <a name="about-defender-for-iot"></a>关于 Defender for IoT

Defender for IoT 提供了由蓝队专家构建的拥有保护关键国家基础结构跟踪记录的唯一 ICS 和 IoT 网络安全平台，以及具有获得专利的 ICS 感知威胁分析和机器学习的唯一平台。 Defender for IoT 提供：

- 有关 ICS 设备布局的即时见解，以及有关属性的大量详细信息。

- OT 协议、设备、应用程序及其行为的 ICS 感知深层嵌入知识。

- 对漏洞和已知零日威胁的即时见解。

- 通过专有分析预测目标 ICS 攻击的最可能路径的自动化 ICS 威胁建模技术。

> [!Note]
> 有关 CyberX 的参考，请参阅 Azure Defender for IoT。

## <a name="about-the-integration"></a>关于集成

Defender for IoT 与 ServiceNow 集成为 IoT 和 OT 布局提供了一个新的集中可见性、监视和控制级别。 这些桥接平台实现了对以前无法访问的 ICS 和 IoT 设备的自动设备可见性和威胁管理。

### <a name="threat-management"></a>威胁管理

Defender for IoT ICS 管理应用程序有助于：

- 缩短工业和关键基础结构组织检测、调查和应对网络威胁所需的时间。

- 获取有关 OT 风险的实时智能。

- 将 Defender for IoT 警报与 ServiceNow 威胁监视和事件管理工作流相关联。

- 利用 ServiceNow 平台上的其他服务和应用程序触发 ServiceNow 票证和工作流。

ICS 和 SCADA 安全威胁由 Defender for IoT 安全引擎识别，这些安全引擎为恶意软件攻击、网络和安全策略偏差以及操作和协议异常提供即时警报响应。 有关发送到 ServiceNow 的警报详情的详细信息，请参阅[警报报告](#alert-reporting)。

### <a name="device-visibility-and-management"></a>设备可见性和管理

ServiceNow 配置管理数据库 (CMDB) 使用一组由 Defender for IoT 平台推送的丰富的设备属性进行扩充和补充。 这可确保全面持续地了解设备布局，并让用户可以从单一虚拟管理平台进行监视和响应。 有关发送到 ServiceNowSee 的设备属性的详细信息，请参阅[在 ServiceNow 中查看 Defender for IoT 检测](#view-defender-for-iot-detections-in-servicenow)。

## <a name="system-requirements-and-architecture"></a>系统需求和体系结构

本文介绍：

- **软件要求**  
- **体系结构**

## <a name="software-requirements"></a>软件要求

- ServiceNow 服务管理版本 3.0.2

- Defender for IoT 补丁 2.8.11.1 或更高版本

> [!Note]
> 如果已在使用 Defender for IoT 和 ServiceNow 集成，并使用本地管理控制台进行升级，则应从 ServiceNow 中清除以前从 Defender for IoT 传感器收到的数据。

## <a name="architecture"></a>体系结构

### <a name="on-premises-management-console-architecture"></a>本地管理控制台体系结构

本地管理控制台为发送到 ServiceNow 的所有设备和警报信息提供统一源。

可以设置本地管理控制台，以便与 ServiceNow 的一个实例进行通信。 本地管理控制台使用 REST API 将传感器数据推送到 Defender for IoT 应用程序。

如果要将系统设置为使用本地管理控制台，请在传感器中禁用“ServiceNow 同步”、“转发规则”和代理配置（如果已设置）。

应当为本地管理控制台设置这些配置。 本文中介绍了配置说明。

### <a name="sensor-architecture"></a>传感器体系结构

如果要将环境设置为包括传感器与 ServiceNow 之间的直接通信，则为每个传感器定义“ServiceNow 同步”、“转发规则”和代理配置（如果需要代理）。

建议使用本地管理控制台来设置集成，以便与 ServiceNow 通信。

## <a name="create-access-tokens-in-servicenow"></a>在 ServiceNow 中创建访问令牌

本文介绍如何在 ServiceNow 中创建访问令牌。 与 Defender for IoT 通信需要令牌。

创建用于将警报信息转发到 ServiceNow 的 Defender for IoT 转发规则，并且配置用于将设备属性推送到 ServiceNow 表的 Defender for IoT 时，需要“客户端 ID”和“客户端密码”。

## <a name="set-up-defender-for-iot-to-communicate-with-servicenow"></a>设置用于与 ServiceNow 通信的 Defender for IoT

本文介绍如何设置用于与 ServiceNow 通信的 Defender for IoT。

### <a name="send-defender-for-iot-alert-information"></a>发送 Defender for IoT 警报信息

本文介绍如何配置 Defender for IoT，以便将警报信息推送到 ServiceNow 表。 有关发送的警报数据的信息，请参阅[警报报告](#alert-reporting)。

Defender for IoT 警报在 ServiceNow 中显示为安全事件。

定义 Defender for IoT 的“转发”规则以将警报信息发送到 ServiceNow。

定义规则：

1. 在 Defender for IoT 左侧窗格中，选择“转发”。  

1. 选择 :::image type="content" source="media/integration-servicenow/plus.png" alt-text=" 加号图标按钮。"::: 来折叠导航窗格。 此时将打开“创建转发规则”对话框。  

    :::image type="content" source="media/integration-servicenow/forwarding-rule.png" alt-text="创建转发规则":::

1. 添加规则名称。

1. 定义 Defender for IoT 触发转发规则的条件。 使用转发规则条件可帮助确定和管理从 Defender for IoT 发送到 ServiceNow 的信息量。 可以使用以下选项：

    - 严重性级别：这是要转发的最小安全级别事件。 例如，如果选择“次要”，将转发次要警报和此严重级别以上的任何警报。 级别由 Defender for IoT 预定义。

    - 协议：如果检测到的流量在特定协议上运行，则仅触发转发规则。 从下拉列表中选择所需的协议，或选择全部协议。

    - 引擎：选择所需的引擎或选择全部引擎。 将发送所选引擎发出的警报。

1. 验证是否选择了“报表警报通知”。

1. 在“操作”部分中，选择“添加”，然后选择“ServiceNow”。

    :::image type="content" source="media/integration-servicenow/select-servicenow.png" alt-text="从下拉选项中选择“ServiceNow”。":::

1. 输入 ServiceNow 操作参数：

    :::image type="content" source="media/integration-servicenow/parameters.png" alt-text="填充 ServiceNow 操作参数":::

1. 在“操作”窗格中，设置以下参数：

  | 参数 | 说明 |
  |--|--|
  | 域 | 输入 ServiceNow 服务器 IP 地址。 |
  | 用户名 | 输入 ServiceNow 服务器用户名。 |
  | 密码 | 输入 ServiceNow 服务器密码。 |
  | 客户端 ID | 在 ServiceNow 的“应用程序注册表”页中输入为 Defender for IoT 接收的客户端 ID。 |
  | 客户端机密 | 在 ServiceNow 的“应用程序注册表”页中输入为 Defender for IoT 创建的客户端密码字符串。 |
  | 报表类型 | 事件：转发 ServiceNow 中显示的具有事件 ID 和每个警报简短说明的警报的列表。<br /><br />Defender for IoT 应用程序：转发完整警报信息，包括传感器详细信息、引擎、源和目标地址。 此信息将转发到 ServiceNow 应用程序上的 Defender for IoT。 |

1. 选择“保存”。 Defender for IoT 警报在 ServiceNow 中显示为事件。

### <a name="send-defender-for-iot-device-attributes"></a>发送 Defender for IoT 设备属性

本文介绍如何配置 Defender for IoT，以将各种设备属性推送到 ServiceNow 表。 有关推送到 ServiceNow 的信息类型的详细信息，请参阅 ***清单信息***。

若要将属性发送到 ServiceNow，必须将本地管理控制台映射到 ServiceNow 实例。 这可以确保 Defender for IoT 平台能够与该实例进行通信和身份验证。

添加 ServiceNow 实例：

1. 登录到 Defender for IoT 本地管理控制台。

1. 从本地管理控制台的“集成”部分中选择“系统设置”，然后选择“ServiceNow”。

      :::image type="content" source="media/integration-servicenow/servicenow.png" alt-text="选择“ServiceNow”按钮。":::

1. 在“ServiceNow 同步”对话框中输入以下同步参数。

    :::image type="content" source="media/integration-servicenow/sync.png" alt-text="“ServiceNow 同步”对话框。":::

     参数 | 说明 |
    |--|--|
    | 启用同步 | 在定义参数后启用和禁用同步。 |
    | 同步频率（分钟） | 默认情况下，信息每 60 分钟推送到 ServiceNow。 最小值为 5 分钟。 |
    | ServiceNow 实例 | 输入 ServiceNow 实例 URL。 |
    | 客户端 ID | 在 ServiceNow 的“应用程序注册表”页中输入为 Defender for IoT 接收的客户端 ID。 |
    | 客户端机密 | 在 ServiceNow 的“应用程序注册表”页中输入为 Defender for IoT 创建的客户端密码字符串。 |
    | 用户名 | 输入此实例的用户名。 |
    | 密码 | 输入此实例的密码。 |

1. 选择“保存”。

## <a name="verify-communication"></a>验证通信

通过查看“上次同步”日期，验证本地管理控制台是否已连接到 ServiceNow 实例。

:::image type="content" source="media/integration-servicenow/sync-confirmation.png" alt-text="通过查看上次同步，验证是否发生通信。":::

## <a name="set-up-the-integrations-using-the-https-proxy"></a>使用 HTTPS 代理设置集成

设置 Defender for IoT 和 ServiceNow 集成时，本地管理控制台和 ServiceNow 服务器使用端口 443 进行通信。 如果 ServiceNow 服务器在代理后面，则无法使用默认端口。

Defender for IoT 通过启用对用于集成的默认端口的更改在 ServiceNow 集成中支持 HTTPS 代理。

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

## <a name="download-the-defender-for-iot-application"></a>下载 Defender for IoT 应用程序

本文介绍如何下载该应用程序。

访问 Defender for IoT 应用程序：

1. 导航到 <https://store.servicenow.com/>

2. 搜索 `Defender for IoT` 或 `CyberX IoT/ICS Management`。

   :::image type="content" source="media/integration-servicenow/search-results.png" alt-text="在搜索栏中搜索 CyberX。":::

3. 选择应用程序。

   :::image type="content" source="media/integration-servicenow/cyberx-app.png" alt-text="从列表中选择应用。":::

4. 选择“请求应用”。

   :::image type="content" source="media/integration-servicenow/sign-in.png" alt-text="使用凭据登录应用程序。":::

5. 登录并下载该应用程序。

## <a name="view-defender-for-iot-detections-in-servicenow"></a>查看 ServiceNow 中的 Defender for IoT 检测

本文介绍 ServiceNow 中提供的设备属性和警报信息。

查看设备属性：

1. 登录到 ServiceNow。

2. 导航到“CyberX 平台”。

3. 导航到“清单”或“警报”。

   [:::image type="content" source="media/integration-servicenow/alert-list.png" alt-text="清单或警报":::](media/integration-servicenow/alert-list.png#lightbox)

## <a name="inventory-information"></a>清单信息

配置管理数据库 (CMDB) 使用从 Defender for IoT 发送到 ServiceNow 的数据进行扩充和补充。 通过在 ServiceNow 的 CMDB 配置项目表中添加或更新设备属性，Defender for IoT 可触发 ServiceNow 工作流和业务规则。

该边栏选项卡中提供以下信息：

- 设备属性，例如设备 MAC、操作系统、供应商或检测到的协议。

- 固件信息，例如固件版本和序列号。

- 已连接的设备信息，例如源和目标之间的流量方向。

### <a name="devices-attributes"></a>设备属性

本文介绍推送到 ServiceNow 的设备属性。

| 项 | 说明 |
|--|--|
| 设备 | 检测到流量的传感器的名称。 |
| ID | Defender for IoT 分配的设备 ID。 |
| 名称 | 设备名称。 |
| IP 地址 | 设备的 IP 地址或地址。 |
| 类型 | 设备类型，例如交换机、PLC、历史数据或域控制器。 |
| MAC 地址 | 设备的 MAC 地址或地址。 |
| 操作系统 | 设备操作系统。 |
| Vendor | 设备供应商。 |
| 协议 | 在设备生成的流量中检测到的协议。 |
| “所有者” | 输入设备所有者的名称。 |
| 位置 | 输入设备的物理位置。 |

在此视图中查看连接到设备的设备。

查看连接的设备：

1. 选择一个设备，然后选择为该设备列出的“设备”。

    :::image type="content" source="media/integration-servicenow/appliance.png" alt-text="从列表中选择所需设备。":::

1. 在“设备详细信息”对话框中，选择“连接的设备”。

### <a name="firmware-details"></a>固件详细信息

本文介绍推送到 ServiceNow 的设备固件信息。

| 项 | 说明 |
|--|--|
| 设备 | 检测到流量的传感器的名称。 |
| 设备 | 设备名称。 |
| 地址 | 设备的 IP 地址。 |
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
| 方向 | 流量的方向。 <br /> <br /> - 单向，表示目标是服务器，源是客户端。 <br /> <br /> - 双向，表示源和目标均为服务器，或者客户端未知。 |
| 源设备 ID | 与连接的设备通信的设备的 IP 地址。 |
| 源设备名称 | 与连接的设备通信的设备的名称。 |
| 目标设备 ID | 连接的设备的 IP 地址。 |
| 目标设备名称 | 连接的设备的名称。 |

## <a name="alert-reporting"></a>警报报告

当 Defenders for IoT 引擎检测到需要关注的网络流量和行为变化时，会触发警报。 有关每个引擎生成的警报类型的详细信息，请参阅[关于警报引擎](#about-alert-engines)。

本文介绍推送到 ServiceNow 的设备警报信息。

| 项 | 说明 |
|--|--|
| 创建 | 生成警报的时间和日期。 |
| 引擎 | 检测到事件的引擎。 |
| 标题 | 警报标题。 |
| 说明 | 警报说明。 |
| 协议 | 在流量中检测到的协议。 |
| 严重性 | Defender for IoT 定义的警报严重性。 |
| 设备 | 检测到流量的传感器的名称。 |
| 源名称 | 源名称。 |
| 源 IP 地址| 源 IP 地址。 |
| 目标名称 | 目标名称。 |
| 目标 IP 地址 | 目标 IP 地址。 |
| 被分派人 | 输入分配给票证的个人的名称。 |

### <a name="updating-alert-information"></a>更新警报信息

选择已创建列中的条目以查看表单中的警报信息。 可以更新警报详细信息，并将警报分配给个人进行查看和处理。

[:::image type="content" source="media/integration-servicenow/alert.png" alt-text="查看警报的信息。":::](media/integration-servicenow/alert.png#lightbox)

### <a name="about-alert-engines"></a>关于警报引擎

本文介绍每个引擎触发的警报的类型。

| 警报类型 | 描述 |
|--|--|
| 策略违反警报 | 当策略违反引擎检测到与以前获知的流量有偏差时触发。 例如： <br /><br />- 检测到新设备。 <br /><br />- 在设备上检测到新的配置。 <br /><br />- 未定义为编程设备的设备执行编程更改。 <br /><br />- 固件版本已更改。 |
| 协议违反警报 | 当协议违反引擎检测到不符合协议规范的数据包结构或字段值时触发。 |
| 操作警报 | 当操作引擎检测到网络操作事件或设备故障时触发。 例如，使用 Stop PLC 命令停止网络设备，或者传感器上的接口停止监视流量。 |
| 恶意软件警报 | 当恶意软件引擎检测到恶意网络活动（例如，Conficker 等已知攻击）时触发。 |
| 异常警报 | 当异常引擎检测到偏差时触发。 例如，设备正在执行网络扫描，但未定义为扫描设备。 |

## <a name="next-steps"></a>后续步骤

了解如何[转发警报信息](how-to-forward-alert-information-to-partners.md)。
