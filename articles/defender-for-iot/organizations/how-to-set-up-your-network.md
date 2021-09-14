---
title: 设置网络
description: 了解需要提供哪些解决方案体系结构、网络准备、先决条件和其他信息，才能确保成功设置好网络来使用 Azure Defender for IoT 设备。
ms.date: 07/25/2021
ms.topic: how-to
ms.openlocfilehash: 7dc9e41b3bfdcbeab86aaabbdf0c97b0339b3df3
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123434081"
---
# <a name="about-azure-defender-for-iot-network-setup"></a>关于 Azure Defender for IoT 网络设置

Azure Defender for IoT 提供连续的 ICS 威胁监视和设备发现。 该平台包含以下组件：

Defender for IoT 传感器：传感器使用被动（无代理）监视收集 ICS 网络流量。 这些传感器属被动非侵入式传感器，对 OT 与 IoT 网络和设备的性能不会造成任何影响。 传感器在连接到 SPAN 端口或网络 TAP 后，会立即开始监视你的网络。 检测结果会在传感器控制台中显示。 在那里，可以在网络映射、设备库存和各种报告中查看、调查和分析这些结果。 例如，风险评估报告、数据挖掘查询、攻击途径。 

Defender for IoT 本地管理控制台：本地管理控制台提供所有网络设备的合并视图。 它提供所有设施中关键 OT 与 IoT 风险指标和警报的实时视图。 它紧密集成了 SOC 工作流和 playbook，可轻松确定缓解活动的优先级和跨站点的威胁关联。 

Defender for IoT 门户：Defender for IoT 应用程序可帮助你购买解决方案设备、安装和更新软件，以及更新 TI 包。 

本文提供有关解决方案体系结构、网络准备、先决条件等信息，以帮助你成功设置网络来使用 Defender for IoT 设备。 阅读本文中的信息的读者应具备操作和管理 OT 与 IoT 网络方面的经验。 这些读者包括自动化工程师、工厂经理、OT 网络基础结构服务提供商、网络安全团队、CISO 或 CIO。

如需帮助或支持，请联系 [Microsoft 支持部门](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099)。

## <a name="on-site-deployment-tasks"></a>站点部署任务

站点部署任务包括：

- [收集站点信息](#collect-site-information)

- [准备配置工作站](#prepare-a-configuration-workstation)

- [设置证书](#set-up-certificates)

- [准备配置工作站](#prepare-a-configuration-workstation)

- [规划机架安装](#plan-rack-installation)

### <a name="collect-site-information"></a>收集站点信息

记录如下所述的站点信息：

- 传感器管理网络信息。

- 站点网络体系结构。

- 物理环境。

- 系统集成。

- 规划的用户凭据。

- 配置工作站。

- SSL 证书（可选，但建议记录）。

- SMTP 身份验证（可选）。 若要将 SMTP 服务器用于身份验证，请准备服务器所需的凭据。

- DNS 服务器（可选）。 准备 DNS 服务器的 IP 和主机名。

有关重要站点信息的详细列表和说明，请参阅[预部署清单](#predeployment-checklist)。

#### <a name="successful-monitoring-guidelines"></a>有关成功监视的指导原则

若要在每个生产网络中找到最适合连接到设备的位置，我们建议遵循以下过程：

:::image type="content" source="media/how-to-set-up-your-network/production-network-diagram.png" alt-text="生产网络设置示意图示例。":::

### <a name="prepare-a-configuration-workstation"></a>准备配置工作站

准备 Windows 工作站，包括以下各项：

- 与传感器管理接口的连接。

- 支持的浏览器

- 终端软件，例如 PuTTY。

确保所需的防火墙规则已在工作站上打开。 有关详细信息，请参阅[网络访问要求](#network-access-requirements)。

#### <a name="supported-browsers"></a>支持的浏览器

传感器和本地管理控制台 Web 应用程序支持以下浏览器：

- Microsoft Edge（最新版本）

- Safari（最新版本，仅限 Mac）

- Chrome（最新版本）

- Firefox（最新版本）

有关支持的浏览器的详细信息，请参阅[推荐的浏览器](../../azure-portal/azure-portal-supported-browsers-devices.md#recommended-browsers)。

### <a name="set-up-certificates"></a>设置证书

安装传感器和本地管理控制台后，将生成一个本地自签名证书，它用于访问传感器 Web 应用程序。 首次登录到 Defender for IoT 时，系统会提示管理员用户提供 SSL/TLS 证书。 此外，还会自动启用验证此证书及其他系统证书的选项。 有关详细信息，请参阅[关于证书](how-to-deploy-certificates.md)。

### <a name="network-access-requirements"></a>网络访问要求

验证你的组织安全策略是否允许访问以下各项：

| 协议 | Transport | 输入/输出 | 端口 | 已使用 | 用途 | 源 | 目标 |
|--|--|--|--|--|--|--|--|
| HTTPS | TCP | 输入/输出 | 443 | 传感器和本地管理 Web 控制台 | 访问 Web 控制台 | 客户端 | 传感器和本地管理控制台 |
| SSH | TCP | 输入/输出 | 22 | CLI | 访问 CLI | 客户端 | 传感器和本地管理控制台 |
| SSL | TCP | 输入/输出 | 443 | 传感器和本地管理控制台 | CyberX 平台与集中管理平台之间的连接 | 传感器 | 本地管理控制台 |
| NTP | UDP | IN | 123 | 时间同步 | 本地管理控制台使用 NTP 来连接传感器 | 传感器 | 本地管理控制台 |
| NTP | UDP | 输入/输出 | 123 | 时间同步 | 未安装本地管理控制台时，传感器将连接到外部 NTP 服务器 | 传感器 | NTP |
| SMTP | TCP | OUT | 25 | 电子邮件 | CyberX 平台和管理平台与邮件服务器之间的连接 | 传感器和本地管理控制台 | 电子邮件服务器 |
| Syslog | UDP | OUT | 514 | LEEF | 从本地管理控制台发送到 Syslog 服务器的日志 | 本地管理控制台和传感器 | Syslog 服务器 |
| DNS |  | 输入/输出 | 53 | DNS | DNS 服务器端口 | 本地管理控制台和传感器 | DNS 服务器 |
| LDAP | TCP | 输入/输出 | 389 | Active Directory | CyberX 平台和管理平台与 Active Directory 之间的连接 | 本地管理控制台和传感器 | LDAP 服务器 |
| LDAPS | TCP | 输入/输出 | 636 | Active Directory | CyberX 平台和管理平台与 Active Directory 之间的连接 | 本地管理控制台和传感器 | LDAPS 服务器 |
| SNMP | UDP | OUT | 161 | 监视 | 远程 SNMP 收集器。 | 本地管理控制台和传感器 | SNMP 服务器 |
| WMI | UDP | OUT | 135 | 监视 | Windows 终结点监视 | 传感器 | 相关网络元素 |
| 隧道 | TCP | IN | 9000 <br /><br />- 在端口 443 上 <br /><br />从最终用户到本地管理控制台。 <br /><br />- 从传感器的端口 22 到本地管理控制台  | 监视 | 隧道 | 传感器 | 本地管理控制台 |
| HTTP| TCP | OUT | 80 | 证书验证  | 下载 CRL 文件 | 传感器 | CRL 服务器 |

### <a name="plan-rack-installation"></a>规划机架安装

规划机架安装：

1. 为设备网络设置准备监视器和键盘。

1. 为设备分配机架空间。

1. 为设备提供交流电源。
1. 准备好用于将管理控制台连接到网络交换机的 LAN 线缆。
1. 准备好用于将交换机 SPAN（镜像）端口和/或网络 TAP 连接到 Defender for IoT 设备的 LAN 线缆。
1. 根据体系结构复习课中所述，配置、连接并验证镜像交换机中的 SPAN 端口。
1. 将已配置的 SPAN 端口连接到运行 Wireshark 的计算机，并验证是否正确配置了该端口。
1. 打开所有相关的防火墙端口。

## <a name="about-passive-network-monitoring"></a>关于被动网络监视

设备通过交换机镜像端口（SPAN 端口）或网络 TAP 接收来自多个源的流量。 管理端口通过与本地管理控制台或 Defender for IoT 门户建立的连接，连接到业务、企业或传感器管理网络。

:::image type="content" source="media/how-to-set-up-your-network/switch-with-port-mirroring.png" alt-text="使用端口镜像的受管理交换机的示意图。":::

### <a name="purdue-model"></a>Purdue 模型

以下部分将介绍 Purdue 级别。

:::image type="content" source="media/how-to-set-up-your-network/purdue-model.png" alt-text="Purdue 模型示意图。":::

#### <a name="level-0-cell-and-area"></a>级别 0：单元和区域  

级别 0 由涉及到基本制造过程的各种传感器、传动器和设备组成。 这些设备执行工业自动化和控制系统的基本功能，例如：

- 驱动马达。

- 测量变量。
- 设置输出。
- 执行关键功能，例如喷漆、焊接和弯折。

#### <a name="level-1-process-control"></a>级别 1：过程控制

级别 1 由嵌入式控制器组成，这些控制器操控其关键功能是与级别 0 设备通信的制造过程。 在离散式制造中，这些设备是可编程逻辑控制器 (PLC) 或远程遥测单元 (RTU)。 在流程式制造中，基本控制器称为分布式控制系统 (DCS)。

#### <a name="level-2-supervisory"></a>级别 2：监督

级别 2 代表与某个生产设施区域的运行时监督和操作关联的系统与功能。 这通常包括以下各项： 

- 操作员接口或 HMI

- 告警或警报系统

- 过程历史数据库和批管理系统

- 控制室工作站

这些系统与级别 1 中的 PLC 和 RTU 通信。 在某些情况下，它们与站点或企业（级别 4 和级别 5）系统和应用程序通信或共享数据。 这些系统主要基于标准计算设备和操作系统（Unix 或 Microsoft Windows）。

#### <a name="levels-3-and-35-site-level-and-industrial-perimeter-network"></a>级别 3 和 3.5：站点级别和工业外围网络

站点级别代表工业自动化和控制系统的最高级别。 位于此级别的系统和应用程序将管理站点范围的工业自动化和控制功能。 级别 0 至 3 被认为对站点操作至关重要。 位于此级别的系统和功能可能包括：

- 生产报告（例如周期时间、质量索引、预测性维护）

- 工厂历史数据库

- 详细生产排程

- 站点级操作管理

- 设备和材料管理

- 补丁启动服务器

- 文件服务器

- 工业域、Active Directory、终端服务器

这些系统与生产区域通信，并与企业（级别 4 和级别 5）系统和应用程序共享数据。  

#### <a name="levels-4-and-5-business-and-enterprise-networks"></a>级别 4 和 5：业务和企业网络

级别 4 和级别 5 代表集中式 IT 系统和功能所在的站点或企业网络。 IT 组织直接管理位于这些级别的服务、系统和应用程序。

## <a name="planning-for-network-monitoring"></a>规划网络监视

以下示例演示了工业控制网络的不同类型的拓扑，以及以最佳方式监视和定位传感器时的注意事项。

### <a name="what-should-be-monitored"></a>应监视哪些内容？

应监视流经第 1 和第 2 层的流量。

### <a name="what-should-the-defender-for-iot-appliance-connect-to"></a>Defender for IoT 设备应连接到哪些组件？

Defender for IoT 设备应连接到可看到第 1 层与第 2 层（在某些情况下还包括第 3 层）之间的工业通信的受管理交换机。

下图是多层多租户网络的总体抽象图，其中的广阔网络安全生态系统通常由 SOC 和 MSSP 运营。

通常，NTA 传感器部署在 OSI 模型的第 0 层到第 3 层。

:::image type="content" source="media/how-to-set-up-your-network/osi-model.png" alt-text="OSI 模型示意图。":::

#### <a name="example-ring-topology"></a>示例：环形拓扑

环形网络是其中每个交换机或节点连接到其他两个（且只能是两个）交换机，从而构成单个连续路径用于传送流量的一种拓扑。

:::image type="content" source="media/how-to-set-up-your-network/ring-topology.PNG" alt-text="环形拓扑示意图。":::

#### <a name="example-linear-bus-and-star-topology"></a>示例：线性总线和星形拓扑

在星形网络中，每个主机连接到中心集线器。 最简单的形式是，由一个中心集线器充当管道来传输消息。 在以下示例中，不会监视下方的交换机，并且看不到保留在这些交换机本地的流量。 可根据 ARP 消息识别设备，但缺少连接信息。

:::image type="content" source="media/how-to-set-up-your-network/linear-bus-star-topology.PNG" alt-text="线性总线和星形拓扑示意图。":::

#### <a name="multisensor-deployment"></a>多传感器部署

下面是有关部署多个传感器的一些建议：

| **数字** | **米** | **依赖项** | **传感器数量** |
|--|--|--|--|
| 交换机之间的最大距离 | 80 米 | 已准备好的以太网线缆 | > 1 |
| OT 网络数量 | > 1 | 无物理连接 | > 1 |
| 交换机数量 | 可以使用 RSPAN 配置 | 最多可部署 8 个本地 SPAN 与传感器靠近的交换机，具体取决于布线距离 | > 1 |

#### <a name="traffic-mirroring"></a>流量镜像  

若要仅查看流量分析的相关信息，需要将 Defender for IoT 平台连接到交换机上的镜像端口，或连接到只包含工业 ICS 和 SCADA 流量的 TAP。

:::image type="content" source="media/how-to-set-up-your-network/switch.jpg" alt-text="将此交换机用于设置。":::

可使用以下方法监视交换机流量：

- [交换机 SPAN 端口](#switch-span-port)

- [远程 SPAN (RSPAN)](#remote-span-rspan)

- [主动和被动聚合 TAP](#active-and-passive-aggregation-tap)

SPAN 和 RSPAN 是 Cisco 的术语。 其他品牌的交换机具有类似的功能，但可能使用不同的术语。

#### <a name="switch-span-port"></a>交换机 SPAN 端口

交换机端口分析器将交换机上的接口发出的本地流量镜像到同一交换机上的接口。 下面是一些注意事项：

- 验证相关交换机是否支持端口镜像功能。  

- 默认会禁用镜像选项。

- 我们建议配置交换机的所有端口，即使这些端口不连接任何数据。 否则，恶意设备可能会连接到不受监视的端口，而且在传感器上不会收到警报。

- 在利用广播或多播消息的 OT 网络上，将交换机配置为仅镜像 RX（接收）传输。 否则，多播消息将会根据主动端口数目反复传输，消耗的带宽会倍增。

以下配置示例仅供参考，基于运行 IOS 的 Cisco 2960 交换机（24 个端口）。 这只是一些典型示例，因此请勿将其当作说明书使用。 其他 Cisco 操作系统和其他品牌的交换机上的镜像端口配置方式不同。

:::image type="content" source="media/how-to-set-up-your-network/span-port-configuration-terminal-v2.png" alt-text="SPAN 端口配置终端示意图。":::
:::image type="content" source="media/how-to-set-up-your-network/span-port-configuration-mode-v2.png" alt-text="SPAN 端口配置模式示意图。":::

##### <a name="monitoring-multiple-vlans"></a>监视多个 VLAN

Defender for IoT 允许监视网络中配置的 VLAN。 无需对 Defender for IoT 系统进行任何配置。 用户需确保网络中的交换机已配置为将 VLAN 标记发送到 Defender for IoT。

以下示例演示了要在 Defender for IoT 启用 VLAN 监视而必须在 Cisco 交换机上配置的命令：

Monitor session：此命令负责将 VLAN 发送到 SPAN 端口的过程。

- monitor session 1 source interface Gi1/2

- monitor session 1 filter packet type good Rx

- monitor session 1 destination interface fastEthernet1/1 encapsulation dot1q

Monitor Trunk Port F.E.Gi1/1：在汇聚端口上配置 VLAN。

- interface GigabitEthernet1/1

- switchport trunk encapsulation dot1q

- switchport mode trunk

#### <a name="remote-span-rspan"></a>远程 SPAN (RSPAN)

远程 SPAN 会话将多个分布式源端口发出的流量镜像到专用远程 VLAN 中。  

:::image type="content" source="media/how-to-set-up-your-network/remote-span.png" alt-text="远程 SPAN 示意图。":::

然后，通过汇聚端口跨多个交换机将 VLAN 中的数据传递到包含物理目标端口的特定交换机。 此端口连接到 Defender for IoT 平台。  

##### <a name="more-about-rspan"></a>有关 RSPAN 的详细信息

- RSPAN 是一项高级功能，它需要使用特殊的 VLAN 在交换机之间传输 SPAN 监视的流量。 并非所有交换机都支持 RSPAN。 请验证你的交换机是否支持 RSPAN 功能。

- 默认会禁用镜像选项。

- 源交换机与目标交换机之间的汇聚端口上必须允许远程 VLAN。

- 连接同一个 RSPAN 会话的所有交换机必须来自同一家供应商。

> [!NOTE]
> 确保在交换机之间共享远程 VLAN 的汇聚端口未定义为镜像会话源端口。
>
> 远程 VLAN 会增大汇聚端口上的带宽用量，增量与镜像会话的带宽大小相当。 验证交换机的汇聚端口是否支持这种情况。  

:::image type="content" source="media/how-to-set-up-your-network/remote-vlan.jpg" alt-text="远程 VLAN 示意图。":::

#### <a name="rspan-configuration-examples"></a>RSPAN 配置示例

RSPAN：基于 Cisco catalyst 2960（24 个端口）。

**源交换机配置示例：**

:::image type="content" source="media/how-to-set-up-your-network/rspan-configuration.png" alt-text="RSPAN 配置的屏幕截图。":::

1. 进入全局配置模式。

1. 创建专用 VLAN。

1. 将该 VLAN 标识为 RSPAN VLAN。

1. 返回“配置终端”模式。

1. 将所有 24 个端口配置为会话源。

1. 将 RSPAN VLAN 配置为会话目标。

1. 返回特权 EXEC 模式。

1. 验证端口镜像配置。

**目标交换机配置示例：**

1. 进入全局配置模式。

1. 将 RSPAN VLAN 配置为会话源。

1. 将物理端口 24 配置为会话目标。

1. 返回特权 EXEC 模式。

1. 验证端口镜像配置。

1. 保存配置。

#### <a name="active-and-passive-aggregation-tap"></a>主动和被动聚合 TAP

主动或被动聚合 TAP 以内联方式安装在网线中。 它将 RX 和 TX 复制到监视传感器。

终端接入点 (TAP) 是一个硬件设备，用于将网络流量从端口 A 传送到端口 B，或者从端口 B 传送到端口 A，而不会出现中断。 它会连续创建两端的流量流的精确副本，且不会影响网络完整性。 某些 TAP 会根据需要使用交换机设置来聚合传输和接收流量。 如果不支持聚合，则每个 TAP 将使用两个传感器端口来监视发送和接收流量。

出于各种原因，TAP 具备诸多优势。 TAP 基于硬件，因此不会遭到入侵。 它们传递所有流量，甚至包括往往由交换机丢弃的已损坏消息。 它们不是处理器敏感型设备，因此数据包计时非常精确，交换机将镜像功能当作可影响镜像数据包计时的低优先级任务进行处理。 对于取证目的，TAP 是最佳设备。

TAP 聚合器也可用于端口监视。 但这些设备基于处理器，因此其内在安全性不如硬件 TAP。 它们可能不会反映精确的数据包计时。

:::image type="content" source="media/how-to-set-up-your-network/active-passive-tap-v2.PNG" alt-text="主动和被动 TAP 示意图。":::

##### <a name="common-tap-models"></a>常见 TAP 型号

这些型号已经过兼容性测试。 其他供应商和型号也可能兼容。

| 映像 | 建模 |
|--|--|
| :::image type="content" source="media/how-to-set-up-your-network/garland-p1gccas-v2.png" alt-text="Garland P1GCCAS 的屏幕截图。"::: | Garland P1GCCAS |
| :::image type="content" source="media/how-to-set-up-your-network/ixia-tpa2-cu3-v2.png" alt-text="IXIA TPA2-CU3 的屏幕截图。"::: | IXIA TPA2-CU3 |
| :::image type="content" source="media/how-to-set-up-your-network/us-robotics-usr-4503-v2.png" alt-text="US Robotics USR 4503 的屏幕截图。"::: | US Robotics USR 4503 |

##### <a name="special-tap-configuration"></a>特殊 TAP 配置

| Garland TAP | US Robotics TAP |
| ----------- | --------------- |
| 确保按如下所示设置跳线：<br />:::image type="content" source="media/how-to-set-up-your-network/jumper-setup-v2.jpg" alt-text="US Robotics 交换机的屏幕截图。":::| 确保聚合模式为“主动”。 |

## <a name="deployment-validation"></a>部署验证

### <a name="engineering-self-review"></a>工程自我评审  

评审 OT 和 ICS 网络图是定义要连接到的最佳位置（从中可以获取最相关的待监视流量）的最有效方式。

站点工程师知道其网络的外观。 与本地网络和运营团队开展评审会议通常可以明确预期，并定义最佳设备连接位置。

相关信息：

- 已知设备列表（电子表格）  

- 估计的设备数  

- 供应商和工业协议

- 交换机型号，用于验证端口镜像选项是否可用

- 有关交换机管理者（例如 IT）以及这些管理者是否是外部资源的信息

- 站点上的 OT 网络列表

#### <a name="common-questions"></a>常见问题

- 实施方案的总体目标是什么？ 完整库存和准确的网络映射是否重要？

- ICS 中是否有多个网络或冗余网络？ 是否监视所有网络？

- ICS 与企业（业务）网络之间是否有通信？ 是否监视这些通信？

- 网络设计中是否配置了 VLAN？

- 如何使用固定设备或暂用设备执行 ICS 的维护？

- 防火墙安装在受监视网络中的哪个位置？

- 受监视网络中是否有任何路由？

- 受监视网络上有哪些 OT 协议处于活动状态？

- 如果我们连接到此交换机，是否会看到 HMI 与 PLC 之间的通信？

- ICS 交换机与企业防火墙之间的实际距离是多少？ 

- 是否可将非受管理交换机更换为受管理交换机，或者，是否可以选择使用网络 TAP？

- 网络中是否有任何串行通信？ 如果有，则在网络图中显示这种通信。

- 如果 Defender for IoT 设备应连接到该交换机，机柜中是否有可用的物理机架空间？

#### <a name="other-considerations"></a>其他注意事项

Defender for IoT 设备的用途是监视来自第 1 层和第 2 层的流量。

对于某些体系结构，如果此层上存在 OT 流量，则 Defender for IoT 设备还会监视第 3 层。 评审站点体系结构和决定是否监视某个交换机时，请考虑以下变数：

- 监视此交换机产生的成本/收益与这种监视的重要性各有多大？

- 如果某个交换机不受管理，是否可以监视来自更高级别交换机的流量？

  如果 ICS 体系结构是环形拓扑，则只需监视此环中的一个交换机。

- 此网络中的安全或操作风险是什么？

- 是否可以监视交换机的 VLAN？ 该 VLAN 是否在我们可以监视的另一个交换机中可见？

#### <a name="technical-validation"></a>技术验证

从交换机 SPAN（或镜像）端口接收记录的流量示例（PCAP 文件）可能有助于：

- 验证是否正确配置了交换机。

- 确认通过交换机的流量（OT 流量）是否适合进行监视。

- 确定此交换机中的带宽和估计设备数。

通过 Wireshark 应用程序将笔记本电脑连接到已配置的 SPAN 端口，即可记录示例 PCAP 文件（只需几分钟时间）。

:::image type="content" source="media/how-to-set-up-your-network/laptop-connected-to-span.jpg" alt-text="已连接到 SPAN 端口的笔记本电脑的屏幕截图。":::
:::image type="content" source="media/how-to-set-up-your-network/sample-pcap-file.PNG" alt-text="记录示例 PCAP 文件的屏幕截图。":::

#### <a name="wireshark-validation"></a>Wireshark 验证

- 检查记录流量中是否存在单播数据包。 单播是指从一个地址传播到另一个地址。 如果大部分流量是 ARP 消息，则表示交换机设置不正确。

- 转到“统计信息” > “协议层次结构” 。 验证是否存在工业 OT 协议。

:::image type="content" source="media/how-to-set-up-your-network/wireshark-validation.png" alt-text="Wireshark 验证的屏幕截图。":::

## <a name="troubleshooting"></a>疑难解答

参考以下部分排查问题：

- [无法使用 Web 界面进行连接](#cant-connect-by-using-a-web-interface)

- [设备无响应](#appliance-is-not-responding)

### <a name="cant-connect-by-using-a-web-interface"></a>无法使用 Web 界面进行连接

1. 验证你尝试连接的计算机是否位于设备所在的同一网络上。

2. 验证 GUI 网络是否已连接到传感器上的管理端口。

3. 对设备 IP 地址运行 ping。 如果 ping 命令没有响应：

    1. 将监视器和键盘连接到设备。

    1. 使用 support 用户和密码登录。

    1. 使用 network list 命令查看当前 IP 地址。

    :::image type="content" source="media/how-to-set-up-your-network/list-of-network-commands.png" alt-text="network list 命令的屏幕截图。":::

4. 如果网络参数配置错误，请使用以下过程更改参数：

    1. 使用 network edit-settings 命令。

    1. 若要更改管理网络 IP 地址，请选择“Y”。

    1. 若要更改子网掩码，请选择“Y”。

    1. 若要更改 DNS，请选择“Y”。

    1. 若要更改默认网关 IP 地址，请选择“Y”。

    1. 若要更改输入接口（仅限传感器），请选择“Y”。

    1. 对于网桥接口，请选择“N”。

    1. 若要应用设置，请选择“Y”。

5. 重启后，使用 support 用户建立连接，然后使用 network list 命令验证参数是否已更改。

6. 再次尝试从 GUI 运行 ping 并连接。

### <a name="appliance-is-not-responding"></a>设备无响应

1. 使用监视器和键盘连接到设备，或使用 PuTTY 远程连接到 CLI。

2. 使用 support 用户凭据登录。

3. 使用 system sanity 命令，检查所有进程是否正在运行。

    :::image type="content" source="media/how-to-set-up-your-network/system-sanity-command.png" alt-text="system sanity 命令的屏幕截图。":::

如有任何其他问题，请联系 [Microsoft 支持部门](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099)。

## <a name="predeployment-checklist"></a>预部署清单

使用预部署清单检索和查看网络设置所需的重要信息。

### <a name="site-checklist"></a>站点检查列表

在进行站点部署之前查看此列表：

| **#** | **任务或活动** | **状态** | **注释** |
|--|--|--|--|
| 1 | 订购设备。 | ☐ |  |
| 2 | 准备网络中的子网列表。 | ☐ |  |
| 3 | 提供生产网络的 VLAN 列表。 | ☐ |  |
| 4 | 提供网络中的交换机型号列表。 | ☐ |  |
| 5 | 提供工业设备的供应商和协议列表。 | ☐ |  |
| 6 | 提供传感器的网络详细信息（IP 地址、子网、D-GW、DNS）。 | ☐ |  |
| 7 | 第三方交换机管理 | ☐ |  |
| 8 | 创建所需的防火墙规则和访问列表。 | ☐ |  |
| 9 | 创建交换机上的跨越端口用于端口监视，或根据需要配置网络 TAP。 | ☐ |  |
| 10 | 准备机架空间用于安装传感器设备。 | ☐ |  |
| 11 | 为人员准备工作站。 | ☐ |  |
| 12 | 为 Defender for IoT 机架设备提供键盘、监视器和鼠标。 | ☐ |  |
| 13 | 在机架上安装设备并布线。 | ☐ |  |
| 14 | 分配站点资源用于支持部署。 | ☐ |  |
| 15 | 创建 Active Directory 组或本地用户。 | ☐ |  |
| 16 | 设置培训（自学）。 | ☐ |  |
| 17 | 做出是否部署的决策。 | ☐ |  |
| 18 | 安排部署日期。 | ☐ |  |


| **日期** | **注意** | **部署日期** | **注意** |
|--|--|--|--|
| Defender for IoT |  | 站点名称* |  |
| 名称 |  | 名称 |  |
| 位置 |  | 位置 |  |

#### <a name="architecture-review"></a>体系结构评审

提供工业网络概览图可为 Defender for IoT 设备定义正确的位置。

1.  全局网络图 - 查看工业 OT 环境的全局网络图。 例如：

    :::image type="content" source="media/how-to-set-up-your-network/backbone-switch.png" alt-text="工业 OT 环境的全局网络图。":::

    > [!NOTE]
    > Defender for IoT 设备应连接到较低级别的交换机，该交换机可以看到其端口之间的流量。  

1. 已提交设备 - 提供要监视的网络设备的大致数量。 将订阅加入 Azure Defender for IoT 门户时需要此信息。 在加入过程中，系统会提示你输入设备数量，以 1000 为增量。

1. （可选）子网列表 - 提供生产网络的子网列表，并提供说明（可选）。 

    |  **#**  | **子网名称** | **说明** |
    |--| --------------- | --------------- |
    | 1  | |
    | 2  | |
    | 3  | |
    | 4  | |

1. VLAN - 提供生产网络的 VLAN 列表。

    | **#** | **VLAN 名称** | **说明** |
    |--|--|--|
    | 1 |  |  |
    | 2 |  |  |
    | 3 |  |  |
    | 4 |  |  |

1. 交换模型和镜像支持 - 若要验证交换机是否具有端口镜像功能，请提供 Defender for IoT 平台应连接到的交换机型号：

    | **#** | **Switch** | **Model** | **流量镜像支持（SPAN、RSPAN 或无）** |
    |--|--|--|--|
    | 1 |  |  |
    | 2 |  |  |
    | 3 |  |  |
    | 4 |  |  |

1. 第三方交换机管理 - 第三方是否管理交换机？ 是或否 

    如果是，由谁管理？ __________________________________ 

    他们的策略是什么？ __________________________________ 

    例如：

    - Siemens

    - Rockwell Automation – 以太网或 IP

    - Emerson – DeltaV、Ovation
    
1.  串行连接 - 是否有设备通过网络中的串行连接进行通信？ 是或否 

    如果是，请指定串行通信协议：________________ 

    如果是，请在网络图上标记哪些设备使用串行协议通信，以及这些设备的位置： 
 
    使用标记的串行连接添加网络关系图 

1. 服务质量 - 对于服务质量 (QoS)，传感器的默认设置为 1.5 Mbps。 指定是否要更改此设置：________________ 

   业务单位 (BU)：________________ 

1.  传感器 - 站点设备规范

    传感器设备通过网络适配器连接到交换机 SPAN 端口。 它连接到客户的企业网络，以通过另一个专用网络适配器进行管理。
    
    提供要在企业网络中连接的传感器 NIC 的地址详细信息： 
    
    | 项 | 设备 1 | 设备 2 | 设备 3 |
    |--|--|--|--|
    | 设备 IP 地址 |  |  |  |
    | 子网 |  |  |  |
    | 默认网关 |  |  |  |
    | DNS |  |  |  |
    | 主机名 |  |  |  |

1.  **iDRAC/iLO/服务器管理**

    | 项 | 设备 1 | 设备 2 | 设备 3 |
    |--|--|--|--|
    | 设备 IP 地址 |  |  |  |
    | 子网 |  |  |  |
    | 默认网关 |  |  |  |
    | DNS |  |  |  |

1. **本地管理控制台** 

    | 项 | 可用 | 被动（使用 HA 时） |
    |--|--|--|
    | IP 地址 |  |  |
    | 子网 |  |  |
    | 默认网关 |  |  |
    | DNS |  |  |

1. **SNMP**  

    | 项 | 详细信息 |
    |--|--|
    | IP |  |
    | IP 地址 |  |
    | 用户名 |  |
    | 密码 |  |
    | 身份验证类型 | MD5 或 SHA |
    | 加密 | DES 或 AES |
    | 密钥 |  |
    | SNMP v2 社区字符串 |

1. **本地管理控制台 SSL 证书**

    你是否计划使用 SSL 证书？ 是或否
    
    如果是，你要使用哪个服务生成证书？ 你将在证书中包含哪些特性（例如域或 IP 地址）？

1. **SMTP 身份验证**

    你是否计划使用 SMTP 将警报转发到电子邮件服务器？ 是或否
    
    如果是，你要使用哪种身份验证方法？  
    
1. **Active Directory 或本地用户**

    请与 Active Directory 管理员联系，以创建 Active Directory 站点用户组或创建本地用户。 确保用户为部署日的到来做好准备。 

1. 网络中的 IoT 设备类型

    | 设备类型 | 网络中的设备数 | 平均带宽 |
    | --------------- | ------ | ----------------------- |
    | 照相机 | |
    | X 光机 | |
    |  |  |
    |  |  |
    |  |  |
    |  |  |
    |  |  |
    |  |  |
    |  |  |
    |  |  |

## <a name="next-steps"></a>后续步骤

[关于 Defender for IoT 安装](how-to-install-software.md)
