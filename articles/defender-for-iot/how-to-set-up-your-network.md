---
title: 设置网络
description: 了解需要确保成功将网络设置为使用适用于 IoT 设备的 Azure Defender 的解决方案体系结构、网络准备、先决条件和其他信息。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 01/03/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: b5cb7d0e421657b84c365d8dca536191cafd4e72
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "97861964"
---
# <a name="about-azure-defender-for-iot-network-setup"></a>关于用于 IoT 的 Azure Defender 网络设置

Azure Defender for IoT 提供持续的 ICS 威胁监视和设备发现。 此平台包括以下组件：

**用于 IoT 传感器的 Defender：** 传感器使用被动 (无代理) 监视收集 ICS 网络流量。 被动和产生干扰，传感器对 OT 网络和设备的性能没有影响。 传感器连接到 SPAN 端口或网络分流，并立即开始监视网络。 检测在传感器控制台中显示。 在这里，你可以在网络映射、设备清单和范围广泛的报表中查看、调查和分析它们。 示例包括风险评估报表、数据挖掘查询和攻击向量。 

**用于 IoT 本地管理控制台的 Defender**：本地管理控制台提供所有网络设备的合并视图。 它在所有设备上实时查看关键的和 IoT 风险指标和警报。 它与 SOC 工作流和行动手册紧密集成，可轻松地确定缓解活动的优先级，以及威胁的跨站点关联。 

适用于 **iot 的 iot 门户：** 用于 IoT 应用程序的 Defender 可帮助你购买解决方案设备、安装和更新软件以及更新 TI 包。 

本文提供了有关解决方案体系结构、网络准备、先决条件等的信息，可帮助你成功将网络设置为使用适用于 IoT 设备的 Defender。 使用本文中的信息的读者应在操作和管理 OT 和 IoT 网络方面有经验。 示例包括自动化工程师、植物经理、网络基础结构服务提供商、网络安全团队、Ciso 或 Cio。

若要获得帮助或支持，请联系 [Microsoft 支持部门](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099)。

## <a name="on-site-deployment-tasks"></a>现场部署任务

站点部署任务包括：

- [收集站点信息](#collect-site-information)

- [准备配置工作站](#prepare-a-configuration-workstation)

- [规划机架安装](#planning-rack-installation)

### <a name="collect-site-information"></a>收集站点信息

记录站点信息，例如：

- 传感器管理网络信息。

- 站点网络体系结构。

- 物理环境。

- 系统集成。

- 计划的用户凭据。

- 配置工作站。

- SSL 证书 (可选的，但建议) 。

- SMTP 身份验证 (可选) 。 若要将 SMTP 服务器用于身份验证，请准备服务器所需的凭据。

- DNS 服务器 (可选的) 。 准备 DNS 服务器的 IP 和主机名。

有关重要站点信息的详细列表和说明，请参阅 [示例网站书籍](#example-site-book)。

#### <a name="successful-monitoring-guidelines"></a>成功的监视指导原则

若要在每个生产网络中查找连接设备的最佳位置，建议执行以下步骤：

:::image type="content" source="media/how-to-set-up-your-network/production-network-diagram.png" alt-text="生产网络设置示意图示例。":::

### <a name="prepare-a-configuration-workstation"></a>准备配置工作站

准备 Windows 工作站，包括以下各项：

- 与传感器管理接口的连接。

- 受支持的浏览器

- 终端软件，如 PuTTY。

请确保在工作站上打开了所需的防火墙规则。 有关详细信息，请参阅 [网络访问要求](#network-access-requirements) 。

#### <a name="supported-browsers"></a>支持的浏览器

传感器和本地管理控制台 web 应用程序支持以下浏览器：

- Chrome 32 +

- Microsoft Edge 86 +

- Internet Explorer 10+

### <a name="network-access-requirements"></a>网络访问要求

验证你的组织安全策略是否允许访问以下内容：

| **用途** | 协议  | **Transport** | **入或出** | 端口  | **类别** |
| ----------- | ----------- | ------------ | ---------- | -------- | ------------ |
| **访问 web 控制台** | HTTPS | TCP | 入或出 | 443 | 用于 IoT 平台的本地管理控制台 |
| **访问 CLI** | SSH | TCP | 入或出 | 22 | CLI |
| **用于 IoT 平台和本地管理控制台的 Defender 之间的连接** | SSL | TCP | 入或出 | 443 | 传感器和本地管理控制台|
| **用于传感器的本地管理控制台** | NTP | UDP| In 到 CM | 123 | 时间同步 | 
| **如果相关) ，则传感器连接到外部 NTP 服务器 (** | NTP | UDP | 入或出| 123 | 时间同步 |
| **用于 IoT 平台和管理平台的 Defender 与邮件服务器之间的连接 (如果相关)** | SMTP | TCP | 传感器管理 | 25 | 电子邮件 |
| **从本地管理控制台发送到 Syslog 服务器的日志 (如果相关)** | Syslog | UDP | 传感器管理| 514 | LEEF |
| **(相关的 DNS 服务器端口)** | DNS | 空值 | 入或出| 53 | DNS |
| **用于 IoT 平台和本地管理控制台的 Defender 之间的连接，用于 Active Directory (相关)** | LDAPS | TCP | 入或出 | 636 <br />389 | Active Directory |
| **远程 SNMP 收集器 (相关)** | SNMP | UDP | 传感器管理| 161 | 监视 |
| **Windows 终结点监视 (相关)** | WMI | UDP | 传感器管理| 135 | 监视 |
| **Windows 终结点监视 (相关)** | WMI | TCP | 传感器管理| 1024及更高版本 | 监视 |
| **如果相关) ，则为隧道 (** | 隧道 | TCP | IN 到 CM | 9000<br />除了端口443<br />从最终用户到本地管理控制台 <br />从传感器到本地管理控制台的端口22 | 监视 |
| **用于 IoT 中心的 Defender 的出站** | HTTPS | TCP | 传感器管理| **URL**<br />*. azure-devices.net:443<br />或者，如果不支持通配符<br />{你的 IoT 中心名称}。 azure-devices.net:443 |

### <a name="planning-rack-installation"></a>规划机架安装

规划机架安装：

1. 为设备网络设置准备监视器和键盘。
2. 为设备分配货架空间。
3. 设备是否有可用的 AC 电源。
4. 准备用于将管理连接到网络交换机的 LAN 电缆。
5. 准备好用于连接交换机范围 (镜像的 LAN 电缆) 端口和或网络点击到 IoT 设备的 Defender。 
6. 如体系结构审查会话中所述，配置、连接和验证镜像交换机中的 SPAN 端口。
7. 将配置的 SPAN 端口连接到运行 Wireshark 的计算机，并验证是否正确配置了端口。
8. 打开所有相关的防火墙端口。

## <a name="about-passive-network-monitoring"></a>关于被动网络监视

设备通过交换机镜像端口 (跨端口) 或网络点击来接收来自多个源的流量。 管理端口连接到业务、公司或传感器管理网络，连接到本地管理控制台或 Defender for IoT 门户。

:::image type="content" source="media/how-to-set-up-your-network/switch-with-port-mirroring.png" alt-text="具有端口镜像的托管交换机的关系图。":::

### <a name="purdue-model"></a>Purdue 模型

以下各节介绍了 Purdue 级别。

:::image type="content" source="media/how-to-set-up-your-network/purdue-model.png" alt-text="Purdue 模型的关系图。":::

####  <a name="level-0-cell-and-area"></a>级别0：单元格和区域  

级别0包含基本制造过程中涉及的各种传感器、传动装置和设备。 这些设备执行工业自动化和控制系统的基本功能，例如：

- 驱动汽车。
- 度量变量。
- 设置输出。
- 执行键功能，如绘制、焊接和蛇形。

#### <a name="level-1-process-control"></a>级别1：进程控制

级别1由嵌入式控制器组成，它们控制和操作其键函数与级别0设备通信的生产进程。 在离散制造中，这些设备是 (Plc) 或远程遥测单位 (RTUs) 的可编程逻辑控制器。 在处理制造中，基本控制器称为分布式控制系统 (DC) 。

#### <a name="level-2-supervisory"></a>级别2：监管

级别2表示与生产设施的某个区域的运行时监管和操作相关联的系统和功能。 这通常包括以下内容： 

- Operator 接口或 HMIs

- 告警或警报系统

- 处理 historian 和 batch 管理系统

- 控制室工作站

这些系统与第1级中的 Plc 和 RTUs 通信。 在某些情况下，它们与站点或企业 (级别4和级别 5) 系统和应用程序通信或共享数据。 这些系统主要基于标准计算设备和操作系统 (Unix 或 Microsoft Windows) 。

#### <a name="levels-3-and-35-site-level-and-industrial-perimeter-network"></a>级别3和3.5：站点级别和工业外围网络

站点级别表示工业自动化和控制系统的最高级别。 位于此级别的系统和应用程序将管理站点范围的工业自动化和控制功能。 级别0到3被视为对站点操作至关重要。 位于此级别的系统和功能可能包括：

- 生产报表 (例如，周期时间、质量索引、预测维护) 

- 植物 historian

- 详细生产计划

- 站点级操作管理

- 设备和材料管理

- 修补程序启动服务器

- 文件服务器

- 工业域，Active Directory，终端服务器

这些系统与生产区域通信，并与企业 (级别4和级别 5) 系统和应用程序共享数据。  

#### <a name="levels-4-and-5-business-and-enterprise-networks"></a>级别4和5：企业网络和企业网络

级别4和级别5代表集中式 IT 系统和功能所在的站点或企业网络。 IT 组织会直接管理这些级别的服务、系统和应用程序。

## <a name="planning-for-network-monitoring"></a>规划网络监视

以下示例演示了适用于工业控制网络的不同类型的拓扑，以及传感器的最佳监视和放置的注意事项。

### <a name="what-should-be-monitored"></a>应监视哪些内容？

应监视遍历第1层和第2层的流量。

### <a name="what-should-the-defender-for-iot-appliance-connect-to"></a>IoT 设备的 Defender 应连接到什么？

用于 IoT 设备的 Defender 应连接到托管交换机，这些交换机在某些情况下还会在第3层) 中看到第1层和第2层之间的工业通信 (。

下图是多层的多租户网络的总体抽象概念，其中包含的大规模网络安全生态系统通常由 SOC 和 MSSP 操作。

通常，NTA 传感器部署在 OSI 模型的第0层到第3层。

:::image type="content" source="media/how-to-set-up-your-network/osi-model.png" alt-text="OSI 模型的关系图。":::

#### <a name="example-ring-topology"></a>示例：环形拓扑

环形网络是一种拓扑，其中每个交换机或节点正好连接到两个其他交换机，形成流量的单个连续路径。

:::image type="content" source="media/how-to-set-up-your-network/ring-topology.PNG" alt-text="环形拓扑图。":::

#### <a name="example-linear-bus-and-star-topology"></a>示例：线性总线和星型拓扑

在星形网络中，每个主机都连接到中心集线器。 在最简单的形式中，一个中心中心充当用于传输消息的管道。 在以下示例中，不会监视较低版本的交换机，也不会显示仍为本地交换机的流量。 可能会根据 ARP 消息标识设备，但会缺少连接信息。

:::image type="content" source="media/how-to-set-up-your-network/linear-bus-star-topology.PNG" alt-text="线性总线和星型拓扑的关系图。":::

#### <a name="multisensor-deployment"></a>Multisensor 部署

下面是部署多个传感器的一些建议：

| * * 数字 * *| **计量** | **依赖项** | **传感器数量** |
|--|--|--|--|
| 交换机之间的最大距离 | 80米 | 准备好的以太网电缆 | 超过1个 |
| 网络的数目 | 超过1个 | 无物理连接 | 超过1个 |
| 开关数 | 可以使用 RSPAN 配置 | 最多8个交换机，其中本地范围接近传感器，通过布线距离 | 超过1个 |

#### <a name="traffic-mirroring"></a>流量镜像  

若要仅查看流量分析的相关信息，需要将 Defender for IoT 平台连接到交换机上的镜像端口，或只包含工业 ICS 和 SCADA 流量的网络分流。 

:::image type="content" source="media/how-to-set-up-your-network/switch.jpg" alt-text="使用此开关进行设置。":::

你可以使用以下方法监视交换机流量：

- [交换机范围端口](#switch-span-port)

- [远程 SPAN (RSPAN) ](#remote-span-rspan)

- [主动和被动聚合分流点](#active-and-passive-aggregation-tap)

SPAN 和 RSPAN 是 Cisco 术语。 其他品牌的交换机具有类似的功能，但可能使用不同的术语。

#### <a name="switch-span-port"></a>交换机范围端口

交换机端口分析器将交换机上的接口的本地流量镜像到相同交换机上的接口。 下面是一些注意事项：

- 验证相关交换机是否支持端口镜像功能。  

- 镜像选项默认情况下处于禁用状态。

- 建议配置交换机的所有端口，即使没有数据连接到交换机。 否则，恶意设备可能连接到不受监视的端口，并且不会在传感器上收到警报。

- 在使用广播或多播消息的网络上，将开关配置为仅限镜像 RX (接收) 传输。 否则，将为多个活动端口重复多播消息，并将带宽相乘。

以下配置示例仅供参考，并且基于 Cisco 2960 交换机 (24 个端口) 运行 IOS。 它们只是典型示例，因此请勿将其用作说明。 其他 Cisco 操作系统和其他品牌交换机的镜像端口配置不同。

:::image type="content" source="media/how-to-set-up-your-network/span-port-configuration-terminal-v2.png" alt-text="SPAN 端口配置终端的关系图。":::
:::image type="content" source="media/how-to-set-up-your-network/span-port-configuration-mode-v2.png" alt-text="跨端口配置模式的关系图。":::

##### <a name="monitoring-multiple-vlans"></a>监视多个 Vlan

Defender for IoT 允许监视网络中配置的 Vlan。 无需为 IoT 系统配置 Defender。 用户需要确保网络中的交换机配置为将 VLAN 标记发送到用于 IoT 的 Defender。

以下示例显示了必须在 Cisco 交换机上配置的必需命令，以便在用于 IoT 的 Defender 中监视 Vlan：

**监视会话**：此命令负责将 vlan 发送到 SPAN 端口。

- 监视会话1源接口 Gi1/2

- 监视会话1筛选器数据包类型良好 Rx

- 监视会话1目标接口 fastEthernet1/1 封装 dot1q

**监视干线端口 F.E. Gi1/1**：在中继端口上配置 vlan。

- interface GigabitEthernet1/1

- 交换机间中继封装 dot1q

- 交换机间模式干线

#### <a name="remote-span-rspan"></a>远程 SPAN (RSPAN) 

远程 SPAN 会话反映了从多个分布式源端口到专用远程 VLAN 的流量。  

:::image type="content" source="media/how-to-set-up-your-network/remote-span.png" alt-text="远程范围关系图。":::

然后，通过中继端口跨多个交换机将 VLAN 中的数据传递到包含物理目标端口的特定交换机。 此端口连接到 IoT 平台的 Defender。  

##### <a name="more-about-rspan"></a>有关 RSPAN 的详细信息

- RSPAN 是一项高级功能，需要特殊的 VLAN 来携带跨交换机的流量。 所有交换机都不支持 RSPAN。 验证开关是否支持 RSPAN 函数。

- 镜像选项默认情况下处于禁用状态。

- 在源交换机和目标交换机之间的中继端口上必须允许使用远程 VLAN。

- 连接同一个 RSPAN 会话的所有交换机都必须来自同一个供应商。

> [!NOTE]
> 请确保共享交换机之间的远程 VLAN 的中继端口未定义为镜像会话源端口。
>
> 远程 VLAN 按镜像会话带宽的大小增加中继端口上的带宽。 验证交换机的干线端口是否支持该端口。  

:::image type="content" source="media/how-to-set-up-your-network/remote-vlan.jpg" alt-text="远程 VLAN 关系图。":::

#### <a name="rspan-configuration-examples"></a>RSPAN 配置示例

RSPAN：基于 Cisco catalyst 2960 (24 端口) 。

**源交换机配置示例：**

:::image type="content" source="media/how-to-set-up-your-network/rspan-configuration.png" alt-text="RSPAN 配置的屏幕截图。":::

1. 进入全局配置模式。

1. 创建专用 VLAN。

1. 将 VLAN 标识为 RSPAN VLAN。

1. 返回 "配置终端" 模式。

1. 将所有24个端口配置为会话源。

1. 将 RSPAN VLAN 配置为会话目标。

1. 返回到特权执行模式。

1. 验证端口镜像配置。

**目标交换机配置示例：**

1. 进入全局配置模式。

1. 将 RSPAN VLAN 配置为会话源。

1. 将物理端口24配置为会话目标。

1. 返回到特权执行模式。

1. 验证端口镜像配置。

1. 保存配置。

#### <a name="active-and-passive-aggregation-tap"></a>主动和被动聚合分流点

将主动或被动聚合分流方式安装到网络电缆。 它将 RX 和 TX 都复制到监视传感器。

 (，终端接入点) 是一种硬件设备，允许网络流量从端口 A 流向端口 B，从端口 B 流向端口 A，而不会中断。 它会连续创建流量流两侧的完全副本，而不会影响网络完整性。 如果需要，某些点击会使用交换机设置来聚合传输和接收流量。 如果不支持聚合，则每个分流器都使用两个传感器端口来监视发送和接收流量。

点击由于各种原因非常有利。 它们是基于硬件的，不会受到损害。 它们会传递经常丢弃的所有流量，甚至是损坏的消息。 它们不是处理器敏感的，因此，数据包计时是指交换机处理镜像函数的确切位置，这是一项可以影响镜像数据包的时间的低优先级任务。 出于取证考虑，点击是最佳设备。

点击聚合器还可用于端口监视。 这些设备是基于处理器的，并不像硬件点击那样本质上是安全的。 它们可能不会反映确切的数据包计时。

:::image type="content" source="media/how-to-set-up-your-network/active-passive-tap-v2.PNG" alt-text="主动和被动分流关系图。":::

##### <a name="common-tap-models"></a>常用分流模型

这些模型已测试兼容性。 其他供应商和型号也可能兼容。

| 图像 | 型号 |
| -- | -- |
| :::image type="content" source="media/how-to-set-up-your-network/garland-p1gccas-v2.png" alt-text="Garland P1GCCAS 的屏幕截图。":::  | Garland P1GCCAS  |
| :::image type="content" source="media/how-to-set-up-your-network/ixia-tpa2-cu3-v2.png" alt-text="IXIA TPA2-CU3 的屏幕截图。":::  | IXIA TPA2-CU3  |
| :::image type="content" source="media/how-to-set-up-your-network/us-robotics-usr-4503-v2.png" alt-text="美国机器人 USR 4503 的屏幕截图。":::  | 美国机器人 USR 4503  |

##### <a name="special-tap-configuration"></a>特殊点击配置

| Garland | US 机器人分流 |
| ----------- | --------------- |
| 请确保跳线设置如下：<br />:::image type="content" source="media/how-to-set-up-your-network/jumper-setup-v2.jpg" alt-text="美国机器人开关屏幕截图。":::| 请确保聚合模式处于活动状态。 |

## <a name="deployment-validation"></a>部署验证

### <a name="engineering-self-review"></a>工程自我审查  

查看你的 OT 和 ICS 网络图是定义连接到的最佳位置的最有效方法，你可以在其中获取最相关的流量进行监视。

站点工程师知道其网络外观。 与本地网络和运营团队开展审查会话通常会阐明预期并定义连接设备的最佳位置。

相关信息：

-  (电子表格) 的已知设备列表  

- 估计的设备数  

- 供应商和工业协议

- 交换机型号，验证端口镜像选项是否可用

- 有关谁管理交换机的信息 (例如，它) 以及它们是否是外部资源

- 站点上的网络网络列表

#### <a name="common-questions"></a>常见问题

- 实现的总体目标是什么？ 完整清单和准确的网络映射是否重要？

- ICS 中是否有多个或冗余的网络？ 是否正在监视所有网络？

- ICS 与企业 (business) 网络之间是否存在通信？ 是否正在监视这些通信？

- 网络设计中是否配置了 Vlan？

- 如何维护带有固定或暂时性设备的 ICS？

- 哪些防火墙安装在受监视的网络中？

- 监视的网络中是否有任何路由？

- 监视的网络上哪些协议处于活动状态？

- 如果我们连接到此交换机，我们是否会看到 HMI 和 Plc 之间的通信？

- ICS 交换机与企业防火墙之间的物理距离是多少？ 

- 不能将非托管交换机替换为托管交换机，还是使用网络点击选项？

- 网络中是否有任何串行通信？ 如果是，则在网络图中显示它。

- 如果 IoT 设备的 Defender 应连接到该交换机，该机柜中是否有可用的物理机架空间？

#### <a name="additional-considerations"></a>其他注意事项

用于 IoT 设备的 Defender 的用途是监视来自第1层和第2层的流量。

对于某些体系结构，如果此层上存在通信，则用于 IoT 设备的 Defender 还会监视第3层。 查看站点体系结构并决定是否监视交换机时，请考虑以下变量：

- 监视此交换机的重要性是多少？

- 如果交换机处于非托管状态，是否可以监视来自更高级别交换机的流量？

  如果 ICS 体系结构是环拓扑，则只需要监视此环中的一个交换机。

- 此网络中的安全或操作风险是什么？

- 是否可以监视交换机的 VLAN？ VLAN 在可监视的其他交换机中是否可见？

#### <a name="technical-validation"></a>技术验证

从交换机跨度 (PCAP 文件) 接收记录的流量示例 (或镜像) 端口可帮助：

- 验证是否正确配置了开关。

- 确认通过交换机的流量是否适用于监视)  (的流量。

- 确定此交换机中的带宽和设备数估算。

通过 Wireshark 应用程序将便携式计算机连接到已配置的 SPAN 端口，可以将示例 PCAP 文件录制 (几分钟) 。

:::image type="content" source="media/how-to-set-up-your-network/laptop-connected-to-span.jpg" alt-text="连接到 SPAN 端口的便携式计算机的屏幕截图。":::
:::image type="content" source="media/how-to-set-up-your-network/sample-pcap-file.PNG" alt-text="示例 PCAP 文件的录制屏幕截图。":::

#### <a name="wireshark-validation"></a>Wireshark 验证

- 检查 *单播数据包* 是否在记录通信中出现。 单播从一个地址到另一个地址。 如果大部分流量为 ARP 消息，则交换机设置不正确。

- 请参阅 **Statistics**  >  **协议层次结构**。 验证是否存在工业 OT 协议。

:::image type="content" source="media/how-to-set-up-your-network/wireshark-validation.png" alt-text="Wireshark 验证的屏幕截图。":::

## <a name="troubleshooting"></a>疑难解答

使用以下部分来解决问题：

- [无法使用 web 界面进行连接](#cant-connect-by-using-a-web-interface)

- [设备未响应](#appliance-is-not-responding)

### <a name="cant-connect-by-using-a-web-interface"></a>无法使用 web 界面进行连接

1. 验证你尝试连接的计算机是否与设备位于同一网络上。

2. 验证 GUI 网络是否已连接到传感器上的管理端口。

3. 对设备 IP 地址执行 Ping 操作。 如果没有对 ping 的响应：

    1. 将监视器和键盘连接到设备。

    1. 使用 **支持** 用户和密码进行登录。

    1. 使用 "命令 **网络" 列表** 查看当前 IP 地址。

    :::image type="content" source="media/how-to-set-up-your-network/list-of-network-commands.png" alt-text="Network list 命令的屏幕截图。":::

4. 如果网络参数配置错误，请使用以下过程对其进行更改：

    1. 使用命令 " **网络编辑设置**"。

    1. 若要更改管理网络 IP 地址，请选择 " **Y**"。

    1. 若要更改子网掩码，请选择 **Y**。

    1. 若要更改 DNS，请选择 **Y**。

    1. 若要更改默认网关 IP 地址，请选择 " **Y**"。

    1. 对于 "仅传感器) 的输入接口更改 ("，请选择 " **Y**"。

    1. 对于桥接口，选择 " **N**"。

    1. 若要应用设置，请选择 " **Y**"。

5. 重新启动后，请与用户支持联系，并使用 **network list** 命令验证参数是否已更改。

6. 尝试 ping 并再次从 GUI 连接。

### <a name="appliance-is-not-responding"></a>设备未响应

1. 使用监视器和键盘连接到设备，或使用 PuTTY 远程连接到 CLI。

2. 使用支持凭据进行登录。

3. 使用 **系统健全** 命令并检查所有进程是否正在运行。

    :::image type="content" source="media/how-to-set-up-your-network/system-sanity-command.png" alt-text="系统健全命令的屏幕截图。":::

对于任何其他问题，请联系 [Microsoft 支持部门](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099)。

## <a name="example-site-book"></a>示例网站书籍

使用示例网站书籍检索和查看网络设置所需的重要信息。

### <a name="site-checklist"></a>站点清单

在站点部署之前查看此列表：

| **#** | **任务或活动** | **状态** | **注释** |
|--|--|--|--|
| 1 | 提供 global。 | ☐ |  |
| 3 | 订购设备。 | ☐ |  |
| 4 | 准备网络中子网的列表。 | ☐ |  |
| 5 | 提供生产网络的 VLAN 列表。 | ☐ |  |
| 6 | 提供网络中的交换机型号列表。 | ☐ |  |
| 7 | 提供工业设备的供应商和协议列表。 | ☐ |  |
| 8 | 为传感器 (IP 地址、子网、D-GW、DNS) 提供网络详细信息。 | ☐ |  |
| 9 | 创建必要的防火墙规则和访问列表。 | ☐ |  |
| 10 | 为端口监视在交换机上创建跨越端口，或根据需要配置网络分流。 | ☐ |  |
| 11 | 准备传感器设备的机架空间。 | ☐ |  |
| 12 | 为人员准备工作站。 | ☐ |  |
| 13 | 为 IoT 货架设备的 Defender 提供键盘、监视器和鼠标。 | ☐ |  |
| 14 | 将设备放在机架上并连接电缆。 | ☐ |  |
| 15 | 分配站点资源以支持部署。 | ☐ |  |
| 16 | 创建 Active Directory 组或本地用户。 | ☐ |  |
| 17 | 设置培训 (自学) 。 | ☐ |  |
| 18 | 开始或不执行。 | ☐ |  |
| 19 | 计划部署日期。 | ☐ |  |


| **日期** | **注意** | **部署日期** | **注意** |
|--|--|--|--|
| Defender for IoT |  | 站点名称 * |  |
| 名称 |  | 名称 |  |
| 位置 |  | 位置 |  |

#### <a name="architecture-review"></a>体系结构评审

"工业网络" 图示的概述允许为 IoT 设备定义正确的位置。

1.  查看工业环境的全球网络示意图。 例如：

    :::image type="content" source="media/how-to-set-up-your-network/ot-global-network-diagram.png" alt-text="适用于全球网络的工业 OT 环境示意图。":::

    > [!NOTE]
    > IoT 设备的 Defender 应连接到一个较低级别的交换机，该交换机查看交换机上端口间的流量。  

2. 提供要监视的网络设备的近似数目。 在将订阅加入 Azure Defender for IoT 门户时，你将需要此信息。 在载入过程中，系统会提示输入设备数量，以1000为增量。

3. 为生产网络提供子网列表，并提供 (可选) 的说明。 

    |  **#**  | **子网名称** | **说明** |
    |--| --------------- | --------------- |
    | 1  | |
    | 2  | |
    | 3  | |
    | 4  | |

4. 提供生产网络的 VLAN 列表。

    | **#** | **VLAN 名称** | **说明** |
    |--|--|--|
    | 1 |  |  |
    | 2 |  |  |
    | 3 |  |  |
    | 4 |  |  |

5. 若要验证交换机是否具有端口镜像功能，请提供 IoT 平台的 Defender 应连接到的交换机型号：

    | **#** | **开关** | **Model** | **流量镜像支持 (SPAN、RSPAN 或 none)** |
    |--|--|--|--|
    | 1 |  |  |
    | 2 |  |  |
    | 3 |  |  |
    | 4 |  |  |

    第三方是否会管理交换机？ Y 或 N 

    如果是，谁？ __________________________________ 

    其策略是什么？ __________________________________ 

    例如：

    - Siemens

    - 幼圆-以太网或 IP

    - Emerson – DeltaV、Ovation
    
6. 是否有设备通过网络中的串行连接进行通信？ 是或否 

    如果是，请指定哪种串行通信协议： ________________ 

    如果是，则在网络图上标记哪些设备与串行协议通信以及这些设备的位置： 
 
    <Add your network diagram with marked serial connection> 

7. 对于 QoS，传感器的默认设置为 1.5 Mbps。 指定是否要更改： ________________ 

   业务部门 (BU) ： ________________ 

### <a name="specifications-for-site-equipment"></a>站点设备规范

#### <a name="network"></a>网络  

传感器设备通过网络适配器连接到交换机范围端口。 它与客户的公司网络连接，通过另一个专用网络适配器进行管理。

提供将在企业网络中连接的传感器 NIC 的地址详细信息： 

|  Item               | 设备1 | 设备2 | 设备3 |
| --------------- | ------------- | ------------- | ------------- |
| 设备 IP 地址    |               |               |               |
| 子网          |               |               |               |
| 默认网关 |               |               |               |
| DNS             |               |               |               |
| 主机名       |               |               |               |

#### <a name="idraciloserver-management"></a>iDRAC/iLO/服务器管理

|       Item          | 设备1 | 设备2 | 设备3 |
| --------------- | ------------- | ------------- | ------------- |
| 设备 IP 地址     |               |               |               |
| 子网          |               |               |               |
| 默认网关 |               |               |               |
| DNS             |               |               |               |

#### <a name="on-premises-management-console"></a>本地管理控制台  

|       Item          | 可用 | 使用 HA) 时的被动 ( |
| --------------- | ------ | ----------------------- |
| IP 地址             |        |                         |
| 子网          |        |                         |
| 默认网关 |        |                         |
| DNS             |        |                         |

#### <a name="snmp"></a>SNMP  

|   Item              | 详细信息 |
| --------------- | ------ |
| IP              |        |
| IP 地址 | |
| 用户名 | |
| 密码 | |
| 身份验证类型 | MD5 或 SHA |
| 加密 | DES 或 AES |
| 密钥 | |
| SNMP v2 团体字符串 |

### <a name="cm-ssl-certificate"></a>CM SSL 证书

你是否打算使用 SSL 证书？ 是或否

如果是，将使用哪种服务生成它？ 你将在证书 (中包含哪些属性，例如，域或 IP 地址) ？

### <a name="smtp-authentication"></a>SMTP 身份验证

你是否打算使用 SMTP 将警报转发到电子邮件服务器？ 是或否

如果是，将使用哪种身份验证方法？  

### <a name="active-directory-or-local-users"></a>Active Directory 或本地用户

请与 Active Directory 管理员联系，以创建 Active Directory 的站点用户组或创建本地用户。 请确保用户已准备好部署日期。 

### <a name="iot-device-types-in-the-network"></a>网络中的 IoT 设备类型

| 设备类型 | 网络中的设备数 | 平均带宽 |
| --------------- | ------ | ----------------------- |
| 照相机 | |
| X 射线计算机 | |

## <a name="see-also"></a>另请参阅

[关于用于 IoT 安装的 Defender](how-to-install-software.md)
