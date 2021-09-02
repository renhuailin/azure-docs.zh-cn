---
title: Azure 流量分析架构 | Microsoft Docs
description: 了解用于分析 Azure 网络安全组流日志的流量分析的架构。
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: vinigam
ms.openlocfilehash: fb32ff13df7329e6e78095b8ee28639312cc62b5
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2021
ms.locfileid: "113216236"
---
# <a name="schema-and-data-aggregation-in-traffic-analytics"></a>流量分析中的架构和数据聚合

流量分析是一种基于云的解决方案，可用于洞察云网络中的用户和应用程序活动。 流量分析可以分析网络观察程序网络安全组 (NSG) 流日志，帮助洞察 Azure 云中的流量流。 使用流量分析可以：

- 直观查看各个 Azure 订阅中的网络活动，以及识别热点。
- 参考有关开放的端口、尝试访问 Internet 的应用程序以及连接到恶意网络的虚拟机 (VM) 的信息，来识别网络安全威胁和保护网络。
- 了解 Azure 区域与 Internet 之间的流量流模式，优化网络部署以提高性能和容量。
- 查明导致网络连接失败的不当网络配置。
- 了解网络用量（字节、数据包或流）。

### <a name="data-aggregation"></a>数据聚合

1. 位于“FlowIntervalStartTime_t”与“FlowIntervalEndTime_t”之间的 NSG 中的所有流日志将按一分钟间隔捕获为存储帐户中的 Blob，然后由流量分析处理。
2. 流量分析的默认处理间隔为 60 分钟。 即，流量分析每隔 60 分钟从存储中选取要聚合的 Blob。 如果所选的处理间隔为 10 分钟，则流量分析将每隔 10 分钟从存储帐户中选取 Blob。
3. 具有相同源 IP、目标 IP、目标端口、NSG 名称、NSG 规则、流方向和传输层协议（TCP 或 UDP）的流（注意：将排除源端口的聚合）将由流量分析聚集成单个流。
4. 此单条记录在经过修饰后（以下部分将提供详细信息）由流量分析引入到 Log Analytics 中。此过程最长可能需要花费 1 小时。
5. FlowStartTime_t 字段指示流日志处理间隔中出现在“FlowIntervalStartTime_t”与“FlowIntervalEndTime_t”之间的第一个此类聚合流（相同的四元组）。
6. 对于 TA 中的任何资源，UI 中指示的流是 NSG 看到的总流数，但在 Log Analytics 中，用户只会看到一条简化的记录。 若要查看所有流，请使用可从存储引用的 blob_id 字段。 该记录的总流数将与 Blob 中出现的各个流相匹配。

下面的查询有助于查看过去 30 天内与非 Azure 公共 IP 交互的所有子网。
```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FlowStartTime_t >= ago(30d) and FlowType_s == "ExternalPublic"
| project Subnet1_s, Subnet2_s  
```
若要查看上述查询中的流的 Blob 路径，请使用以下查询：

```
let TableWithBlobId =
(AzureNetworkAnalytics_CL
   | where SubType_s == "Topology" and ResourceType == "NetworkSecurityGroup" and DiscoveryRegion_s == Region_s and IsFlowEnabled_b
   | extend binTime = bin(TimeProcessed_t, 6h),
            nsgId = strcat(Subscription_g, "/", Name_s),
            saNameSplit = split(FlowLogStorageAccount_s, "/")
   | extend saName = iif(arraylength(saNameSplit) == 3, saNameSplit[2], '')
   | distinct nsgId, saName, binTime)
| join kind = rightouter (
   AzureNetworkAnalytics_CL
   | where SubType_s == "FlowLog"  
   | extend binTime = bin(FlowEndTime_t, 6h)
) on binTime, $left.nsgId == $right.NSGList_s  
| extend blobTime = format_datetime(todatetime(FlowIntervalStartTime_t), "yyyy MM dd hh")
| extend nsgComponents = split(toupper(NSGList_s), "/"), dateTimeComponents = split(blobTime, " ")
| extend BlobPath = strcat("https://", saName,
                        "@insights-logs-networksecuritygroupflowevent/resoureId=/SUBSCRIPTIONS/", nsgComponents[0],
                        "/RESOURCEGROUPS/", nsgComponents[1],
                        "/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/", nsgComponents[2],
                        "/y=", dateTimeComponents[0], "/m=", dateTimeComponents[1], "/d=", dateTimeComponents[2], "/h=", dateTimeComponents[3],
                        "/m=00/macAddress=", replace(@"-", "", MACAddress_s),
                        "/PT1H.json")
| project-away nsgId, saName, binTime, blobTime, nsgComponents, dateTimeComponents;

TableWithBlobId
| where SubType_s == "FlowLog" and FlowStartTime_t >= ago(30d) and FlowType_s == "ExternalPublic"
| project Subnet_s , BlobPath
```

以上查询构造一个用于直接访问 Blob 的 URL。 下面是包含占位符的 URL：

```
https://{saName}@insights-logs-networksecuritygroupflowevent/resoureId=/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroup}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json

```

### <a name="fields-used-in-traffic-analytics-schema"></a>在流量分析架构中使用的字段
  > [!IMPORTANT]
  > 流量分析架构已在 2019 年 8 月 22 日更新。 新架构单独提供源和目标 IP，无需用户分析 FlowDirection 字段，因此可以简化查询。 </br>
  > FASchemaVersion_s 已从 1 更新为 2。 </br>
  > 已弃用的字段：VMIP_s、Subscription_s、Region_s、NSGRules_s、Subnet_s、VM_s、NIC_s、PublicIPs_s、FlowCount_d </br>
  > 新字段：SrcPublicIPs_s、DestPublicIPs_s、NSGRule_s </br>
  > 已弃用的字段在 2019 年 11 月 22 日之前仍然可用。

流量分析构建在 Log Analytics 的基础之上，因此你可以针对流量分析修饰的数据运行自定义查询，并针对这些数据设置警报。

下面列出了架构中的字段及其含义

| 字段 | 格式 | 注释 |
|:---   |:---    |:---  |
| TableName | AzureNetworkAnalytics_CL | 流量分析数据表
| SubType_s | FlowLog | 流日志的子类型。 仅使用“FlowLog”，SubType_s 的其他值用于产品的内部工作 |
| FASchemaVersion_s |   2   | 架构版本。 不反映 NSG 流日志版本 |
| TimeProcessed_t   | UTC 日期和时间  | 流量分析处理存储帐户中的原始流日志的时间 |
| FlowIntervalStartTime_t | UTC 日期和时间 |  流日志处理间隔的开始时间。 这是开始计量流间隔的时间 |
| FlowIntervalEndTime_t | UTC 日期和时间 | 流日志处理间隔的结束时间 |
| FlowStartTime_t | UTC 日期和时间 |  流日志处理间隔中出现在“FlowIntervalStartTime_t”与“FlowIntervalEndTime_t”之间的第一个流（将会聚合）。 此流将会基于聚合逻辑进行聚合 |
| FlowEndTime_t | UTC 日期和时间 | 流日志处理间隔中出现在“FlowIntervalStartTime_t”与“FlowIntervalEndTime_t”之间的最后一个流（将会聚合）。 在流日志 v2 中，此字段包含启动具有相同四元组的最后一个流（在原始流记录中标记为“B”）的时间。 |
| FlowType_s |  * IntraVNet <br> * InterVNet <br> * S2S <br> * P2S <br> * AzurePublic <br> * ExternalPublic <br> * MaliciousFlow <br> * Unknown Private <br> * Unknown | 表格下方的注释中提供了定义 |
| SrcIP_s | 源 IP 地址 | 使用 AzurePublic 和 ExternalPublic 流时是空白的 |
| DestIP_s | 目标 IP 地址 | 使用 AzurePublic 和 ExternalPublic 流时是空白的 |
| VMIP_s | VM 的 IP | 用于 AzurePublic 和 ExternalPublic 流 |
| PublicIP_s | 公共 IP 地址 | 用于 AzurePublic 和 ExternalPublic 流 |
| DestPort_d | Destination Port | 传入流量的端口 |
| L4Protocol_s  | * T <br> * U  | 传输协议。 T = TCP <br> U = UDP |
| L7Protocol_s  | 协议名称 | 派生自目标端口 |
| FlowDirection_s | * I = 出站<br> * O = 出站 | 根据流日志流入/流出 NSG 的方向 |
| FlowStatus_s  | * A = 由 NSG 规则允许 <br> * D = 由 NSG 规则拒绝  | 根据流日志由 NSG 允许/阻止的流的状态 |
| NSGList_s | \<SUBSCRIPTIONID>\/<RESOURCEGROUP_NAME>\/<NSG_NAME> | 与流关联的网络安全组 (NSG) |
| NSGRules_s | \<Index value 0)>\|\<NSG_RULENAME>\|\<Flow Direction>\|\<Flow Status>\|\<FlowCount ProcessedByRule> |  允许或拒绝此流的 NSG 规则 |
| NSGRule_s | NSG_RULENAME |  允许或拒绝此流的 NSG 规则 |
| NSGRuleType_s | * 用户定义 * 默认值 |   流使用的 NSG 规则类型 |
| MACAddress_s | MAC 地址 | 捕获流的 NIC 的 MAC 地址 |
| Subscription_s | 此字段中填充了 Azure 虚拟网络/网络接口/虚拟机的订阅 | 仅适用于以下流类型：S2S、P2S、AzurePublic、ExternalPublic、MaliciousFlow 和 UnknownPrivate（只有一端的流类型是 Azure） |
| Subscription1_s | 订阅 ID | 流中的源 IP 所属的虚拟网络/网络接口/虚拟机的订阅 ID |
| Subscription2_s | 订阅 ID | 流中的目标 IP 所属的虚拟网络/网络接口/虚拟机的订阅 ID |
| Region_s | 流中的 IP 所属的虚拟网络/网络接口/虚拟机的 Azure 区域 | 仅适用于以下流类型：S2S、P2S、AzurePublic、ExternalPublic、MaliciousFlow 和 UnknownPrivate（只有一端的流类型是 Azure） |
| Region1_s | Azure 区域 | 流中的源 IP 所属的虚拟网络/网络接口/虚拟机的 Azure 区域 |
| Region2_s | Azure 区域 | 流中的目标 IP 所属的虚拟网络的 Azure 区域 |
| NIC_s | \<resourcegroup_Name>\/\<NetworkInterfaceName> |  与发送或接收流量的 VM 关联的 NIC |
| NIC1_s | <resourcegroup_Name>/\<NetworkInterfaceName> | 与流中的源 IP 关联的 NIC |
| NIC2_s | <resourcegroup_Name>/\<NetworkInterfaceName> | 与流中的目标 IP 关联的 NIC |
| VM_s | <resourcegroup_Name>\/\<NetworkInterfaceName> | 与网络接口 NIC_s 关联的虚拟机 |
| VM1_s | <resourcegroup_Name>/\<VirtualMachineName> | 与流中的源 IP 关联的虚拟机 |
| VM2_s | <resourcegroup_Name>/\<VirtualMachineName> | 与流中的目标 IP 关联的虚拟机 |
| Subnet_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName> | 与 NIC_s 关联的子网 |
| Subnet1_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName> | 与流中的源 IP 关联的子网 |
| Subnet2_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName>    | 与流中的目标 IP 关联的子网 |
| ApplicationGateway1_s | \<SubscriptionID>/\<ResourceGroupName>/\<ApplicationGatewayName> | 与流中的源 IP 关联的应用程序网关 |
| ApplicationGateway2_s | \<SubscriptionID>/\<ResourceGroupName>/\<ApplicationGatewayName> | 与流中的目标 IP 关联的应用程序网关 |
| LoadBalancer1_s | \<SubscriptionID>/\<ResourceGroupName>/\<LoadBalancerName> | 与流中的源 IP 关联的负载均衡器 |
| LoadBalancer2_s | \<SubscriptionID>/\<ResourceGroupName>/\<LoadBalancerName> | 与流中的目标 IP 关联的负载均衡器 |
| LocalNetworkGateway1_s | \<SubscriptionID>/\<ResourceGroupName>/\<LocalNetworkGatewayName> | 与流中的源 IP 关联的本地网络网关 |
| LocalNetworkGateway2_s | \<SubscriptionID>/\<ResourceGroupName>/\<LocalNetworkGatewayName> | 与流中的目标 IP 关联的本地网络网关 |
| ConnectionType_s | 可能的值为 VNetPeering、VpnGateway 和 ExpressRoute |    连接类型 |
| ConnectionName_s | \<SubscriptionID>/\<ResourceGroupName>/\<ConnectionName> | 连接名称。 对于流类型 P2S，此项的格式将设为 <gateway name>_<VPN Client IP> |
| ConnectingVNets_s | 虚拟网络名称的空格分隔列表 | 对于中心辐射型拓扑，此处将会填充中心虚拟网络 |
| Country_s | 双字母国家/地区代码 (ISO 3166-1 alpha-2) | 为流类型 ExternalPublic 填充此字段。 PublicIPs_s 字段中的所有 IP 地址将共享同一个国家/地区代码 |
| AzureRegion_s | Azure 区域位置 | 为流类型 AzurePublic 填充此字段。 PublicIPs_s 字段中的所有 IP 地址将共享该 Azure 区域 |
| AllowedInFlows_d | | 允许的入站流数。 这表示共享入站到捕获流的网络接口的同一个四元组的流数 |
| DeniedInFlows_d |  | 拒绝的入站流数。 （入站到捕获流的网络接口） |
| AllowedOutFlows_d | | 允许的出站流数（出站到捕获流的网络接口） |
| DeniedOutFlows_d  | | 拒绝的出站流数（出站到捕获流的网络接口） |
| FlowCount_d | 已弃用。 匹配同一个四元组的总流数。 如果流类型为 ExternalPublic 和 AzurePublic，则计数还包括来自各个 PublicIP 地址的流。
| InboundPackets_d | 表示已从目标向流源发送的数据包 | 仅为 NSG 流日志架构版本 2 填充此字段 |
| OutboundPackets_d  | 表示已从源向流目标发送的数据包 | 仅为 NSG 流日志架构版本 2 填充此字段 |
| InboundBytes_d |  表示已从目标向流源发送的字节 | 仅为 NSG 流日志架构版本 2 填充此字段 |
| OutboundBytes_d |表示已从源向流目标发送的字节 | 仅为 NSG 流日志架构版本 2 填充此字段 |
| CompletedFlows_d  |  | 仅为 NSG 流日志架构版本 2 在此字段中填充非零值 |
| PublicIPs_s | <PUBLIC_IP>\|\<FLOW_STARTED_COUNT>\|\<FLOW_ENDED_COUNT>\|\<OUTBOUND_PACKETS>\|\<INBOUND_PACKETS>\|\<OUTBOUND_BYTES>\|\<INBOUND_BYTES> | 条形分隔的条目 |
| SrcPublicIPs_s | <SOURCE_PUBLIC_IP>\|\<FLOW_STARTED_COUNT>\|\<FLOW_ENDED_COUNT>\|\<OUTBOUND_PACKETS>\|\<INBOUND_PACKETS>\|\<OUTBOUND_BYTES>\|\<INBOUND_BYTES> | 条形分隔的条目 |
| DestPublicIPs_s | <DESTINATION_PUBLIC_IP>\|\<FLOW_STARTED_COUNT>\|\<FLOW_ENDED_COUNT>\|\<OUTBOUND_PACKETS>\|\<INBOUND_PACKETS>\|\<OUTBOUND_BYTES>\|\<INBOUND_BYTES> | 条形分隔的条目 |

### <a name="public-ip-details-schema"></a>公共 IP 详细信息架构

流量分析可为客户环境中的所有公共 IP 提供 WHOIS 数据和地理位置。 针对恶意 IP，其可提供由 Microsoft 安全智能解决方案识别的 DNS 域、威胁类型和线程说明。 我们已将 IP 详细信息发布到 Log Analytics 工作区，以便你可以创建自定义查询并在其中放置警报。 你还可以通过流量分析仪表板访问已预填充的查询。

以下为公共 IP 详细信息架构：

| 字段 | 格式 | 注释 |
|:---   |:---    |:---  |
| TableName | AzureNetworkAnalyticsIPDetails_CL | 包含流量分析 IP 详细信息数据的表格 |
| SubType_s | FlowLog | 流日志的子类型。 仅可使用“FlowLog”，SubType_s 的其他值需在产品内部使用 |
| FASchemaVersion_s | 2 | 架构版本。 不反映 NSG 流日志版本 |
| FlowIntervalStartTime_t | UTC 日期和时间 | 流日志处理时间间隔的开始时间。 这是开始计量流间隔的时间 |
| FlowIntervalEndTime_t | UTC 日期和时间 | 流日志处理时间间隔的结束时间 |
| FlowType_s | * AzurePublic <br> * ExternalPublic <br> * MaliciousFlow | 表格下方的注释中提供了定义 |
| IP | 公共 IP | 记录中提供信息的公共 IP |
| 位置 | IP 位置 | - 针对 Azure 公共 IP：IP 所属的虚拟网络/网络接口/虚拟机的 Azure 区域 <br> - 针对外部公共 IP 和恶意 IP：IP 位置的二位字母国家/地区代码（ISO 3166-1 二位字母代码） |
| PublicIPDetails | IP 信息 | - 针对 AzurePublic IP：IP 的 Azure 服务 <br> - 针对外部公共/恶意 IP：IP 的 WhoIS 信息 |
| ThreatType | 恶意 IP 带来的威胁 | 仅针对恶意 IP：如下所述的当前允许值列表中的威胁之一 |
| ThreatDescription | 威胁说明 | 仅针对恶意 IP：恶意 IP 所带来威胁的说明 |
| DNSDomain | DNS 域 | 仅针对恶意 IP：与此 IP 关联的域名 |

威胁类型列表：

| 值 | 说明 |
|:---   |:---    |
| 僵尸网络 | 指示器详细介绍了僵尸网络节点/成员。 |
| C2 | 指示器详细介绍了僵尸网络的命令与控制节点。 |
| CryptoMining | 涉及此网络地址/URL 的流量表示加密挖矿/资源滥用。 |
| DarkNet | Darknet 节点/网络的指示器。 |
| DDos | 与有效或即将到来的 DDoS 活动相关的指示器。 |
| MaliciousUrl | 服务于恶意软件的 URL。 |
| 恶意软件 | 描述一个或多个恶意文件的指示器。 |
| 钓鱼 | 与网络钓鱼活动相关的指示器。 |
| 代理 | 代理服务的指示器。 |
| PUA | 可能多余的应用程序。 |
| WatchList | 此为一般存储桶，当不能准确确定威胁的具体内容或需要手动解释时，将在其中放置指示器。 合作伙伴在将数据提交到系统时通常不应使用此方法。 |


### <a name="notes"></a>注释

1. 对于 AzurePublic 和 ExternalPublic 流，客户拥有的 Azure VM IP 将填充在 VMIP_s 字段中，而公共 IP 地址将填充在 PublicIPs_s 字段中。 对于这两种流类型，我们应使用 VMIP_s 和 PublicIPs_s 字段，而不是 SrcIP_s 和 DestIP_s 字段。 对于 AzurePublic 和 ExternalPublicIP 地址，我们将进一步聚合，以尽量减少引入到客户 Log Analytics 工作区的记录数。（此字段即将弃用，我们应该会根据 Azure VM 是流中的源还是目标，使用 SrcIP_ 或 DestIP_s）
1. 流类型的详细信息：根据流中涉及的 IP 地址，我们将流分类为以下流类型：
1. IntraVNet - 流中的两个 IP 地址位于同一个 Azure 虚拟网络中。
1. InterVNet - 流中的 IP 地址位于两个不同的 Azure 虚拟网络中。
1. S2S（站点到站点）- 一个 IP 地址属于 Azure 虚拟网络，而另一个 IP 地址属于通过 VPN 网关或 Express Route 连接到 Azure 虚拟网络的客户网络（站点）。
1. P2S（点到站点）- 一个 IP 地址属于 Azure 虚拟网络，而另一个 IP 地址属于通过 VPN 网关连接到 Azure 虚拟网络的客户网络（站点）。
1. AzurePublic - 一个 IP 地址属于 Azure 虚拟网络，而另一个 IP 地址属于 Microsoft 拥有的 Azure 内部公共 IP 地址。 客户拥有的公共 IP 地址不属于此流类型。 例如，客户拥有的、将流量发送到 Azure 服务（存储终结点）的任何 VM 将划分到此流类型。
1. ExternalPublic - 一个 IP 地址属于 Azure 虚拟网络，而另一个 IP 地址是不在 Azure 中的公共 IP 地址，流量分析在处理间隔期间在“FlowIntervalStartTime_t”与“FlowIntervalEndTime_t”之间使用的 ASC 源中不会将此地址报告为恶意地址。
1. MaliciousFlow - 其中一个 IP 地址属于 Azure 虚拟网络，而另一个 IP 地址则是并不位于 Azure 的公共 IP 地址。如果流量分析使用 ASC 源处理“FlowIntervalStartTime_t”与“FlowIntervalEndTime_t”之间的时间间隔，则系统会在 ASC 源内将此地址报告为恶意地址。
1. UnknownPrivate - 一个 IP 地址属于 Azure 虚拟网络，而另一个 IP 地址属于 RFC 1918 中定义的专用 IP 范围，无法由流量分析映射到客户拥有的站点或 Azure 虚拟网络。
1. Unknown - 无法将流中的任一 IP 地址映射到 Azure 中的客户拓扑以及本地（站点）。
1. 某些字段名称的后面追加了 \_s 或 \_d。 这些后缀并不表示源 (source) 和目标 (destination)，而是表示数据类型字符串 (string) 和十进制 (decimal)。

### <a name="next-steps"></a>后续步骤
若要获取常见问题的解答，请参阅[流量分析常见问题解答](traffic-analytics-faq.yml)。若要查看有关功能的详细信息，请参阅[流量分析文档](traffic-analytics.md)
