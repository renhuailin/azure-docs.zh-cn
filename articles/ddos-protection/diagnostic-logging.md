---
title: Azure DDoS 保护标准报表和流日志
description: 了解如何配置报表和流日志。
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/28/2020
ms.author: yitoh
ms.openlocfilehash: 22c49502883cb444027bd59a24bfb5bb3c32da4c
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/06/2021
ms.locfileid: "97915158"
---
# <a name="view-and-configure-ddos-diagnostic-logging"></a>查看和配置 DDoS 诊断日志记录

Azure DDoS 防护标准通过 DDoS 攻击分析提供详细的攻击见解和可视化效果。 保护其虚拟网络免受 DDoS 攻击的客户可通过攻击缓解报告和缓解流日志来详细了解攻击流量以及缓解攻击的操作。 丰富的遥测通过 Azure Monitor 公开，包括在 DDoS 攻击期间的详细指标。 可以针对 DDoS 防护公开的任何 Azure Monitor 指标配置警报。 可以通过 Azure Monitor 诊断接口，将日志记录与 [Azure Sentinel](../sentinel/connect-azure-ddos-protection.md)、Splunk (Azure 事件中心) 、OMS Log Analytics 和 azure 存储进行进一步集成，以实现高级分析。

以下诊断日志适用于 Azure DDoS 保护标准： 

- **DDoSProtectionNotifications**：通知会在公共 IP 资源遭受攻击时，以及当遭受攻击缓解时向你发出通知。
- **DDoSMitigationFlowLogs**：攻击缓解流日志使你能够以近乎实时的状态在主动 DDoS 攻击期间查看丢弃的流量、转发的流量和其他有趣的数据点。 可以通过事件中心将此数据的常量流引入 Azure Sentinel 或第三方 SIEM 系统，以进行近乎实时的监视，采取可能的措施，并满足防御操作的需求。
- **DDoSMitigationReports**：攻击缓解报告使用聚合的 Netflow 协议数据来提供有关资源攻击的详细信息。 只要公共 IP 资源受到攻击，就会在缓解措施启动时开始生成报告。 每 5 分钟生成一份增量报告，整个缓解过程结束后，生成一份缓解后报告。 这是为了确保在 DDoS 攻击持续时间较长时，能够每隔 5 分钟查看一次最新的缓解报告快照，并在攻击缓解结束时查看完整总结。 
- **AllMetrics**：提供在 DDoS 攻击期间可用的所有可能指标。 

本教程介绍以下操作：

> [!div class="checklist"]
> * 配置 DDoS 诊断日志，包括通知、缓解报告和缓解流日志。 
> * 对定义的作用域中的所有公共 Ip 启用诊断日志记录。
> * 查看工作簿中的日志数据。

## <a name="prerequisites"></a>先决条件

- 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 在完成本教程中的步骤之前，必须先创建 [Azure DDoS 标准保护计划](manage-ddos-protection.md) ，并且必须在虚拟网络上启用 DDoS 保护标准。
- DDoS 监视分配给虚拟网络中的资源的公共 IP 地址。 如果虚拟网络中没有任何具有公共 IP 地址的资源，必须首先创建具有公共 IP 地址的资源。 你可以通过 [azure 服务的虚拟网络](../virtual-network/virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) 中列出的资源管理器 (非经典) 来监视部署的所有资源的公共 IP 地址 (包括) 环境除外的虚拟网络中的后端虚拟机所在的 Azure 负载平衡器。 可快速创建 [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 虚拟机继续本教程的内容。    

## <a name="configure-ddos-diagnostic-logs"></a>配置 DDoS 诊断日志

如果要在环境中的所有公共 Ip 上自动启用诊断日志记录，请跳到对 [所有公共 Ip 启用诊断日志记录](#enable-diagnostic-logging-on-all-public-ips)。

1. 在门户左上角选择“所有服务”。
2. 在“筛选器”框中输入 Monitor。 当“Monitor”出现在结果中时，将其选中。
3. 在 " **设置**" 下，选择 " **诊断设置**"。
4. 选择包含要记录的公共 IP 地址的订阅和资源组。
5. 为 "**资源类型**" 选择 "**公共 ip 地址**"，然后选择要为其启用日志的特定公共 ip 地址。
6. 选择“添加诊断设置”。  在 " **类别详细信息**" 下，选择所需的以下任意选项，然后选择 " **保存**"。

    ![DDoS 诊断设置](./media/ddos-attack-telemetry/ddos-diagnostic-settings.png)

7. 在 " **目标详细信息**" 下，根据需要选择以下任意选项：

    - **存档到存储帐户**：将数据写入 Azure 存储帐户。 若要了解有关此选项的详细信息，请参阅 [存档资源日志](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage)。
    - **流式传输到事件中心**：允许日志接收器使用 Azure 事件中心选取日志。 事件中心将启用与 Splunk 或其他 SIEM 系统的集成。 若要了解有关此选项的详细信息，请参阅 [将资源日志流式传输到事件中心](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs)。
    - **发送到 Log Analytics**：将日志写入 Azure Monitor 服务。 若要了解有关此选项的详细信息，请参阅 [收集用于 Azure Monitor 日志的日志](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-log-analytics-workspace)。

### <a name="log-schemas"></a>日志架构

下表列出了字段的名称和描述：

# <a name="ddosprotectionnotifications"></a>[DDoSProtectionNotifications](#tab/DDoSProtectionNotifications)

| 字段名称 | 说明 |
| --- | --- |
| **TimeGenerated** | 创建通知时的 UTC 日期和时间。 |
| **ResourceId** | 公共 IP 地址的资源 ID。 |
| **类别** | 对于通知，这将为 `DDoSProtectionNotifications` 。|
| **ResourceGroup** | 包含公共 IP 地址和虚拟网络的资源组。 |
| **订阅** | DDoS 保护计划订阅 ID。 |
| **资源** | 公共 IP 地址的名称。 |
| **ResourceType** | 这将始终为 `PUBLICIPADDRESS` 。 |
| **OperationName** | 对于通知，这将为 `DDoSProtectionNotifications` 。  |
| **消息** | 攻击的详细信息。 |
| 类型 | 通知的类型。 可能的值包括 `MitigationStarted` 。 `MitigationStopped`. |
| **PublicIpAddress** | 公共 IP 地址。 |

# <a name="ddosmitigationflowlogs"></a>[DDoSMitigationFlowLogs](#tab/DDoSMitigationFlowLogs)

| 字段名称 | 说明 |
| --- | --- |
| **TimeGenerated** | 创建流日志时的 UTC 日期和时间。 |
| **ResourceId** | 公共 IP 地址的资源 ID。 |
| **类别** | 对于流日志，这将为 `DDoSMitigationFlowLogs` 。|
| **ResourceGroup** | 包含公共 IP 地址和虚拟网络的资源组。 |
| **订阅** | DDoS 保护计划订阅 ID。 |
| **资源** | 公共 IP 地址的名称。 |
| **ResourceType** | 这将始终为 `PUBLICIPADDRESS` 。 |
| **OperationName** | 对于流日志，这将为 `DDoSMitigationFlowLogs` 。 |
| **消息** | 攻击的详细信息。 |
| **SourcePublicIpAddress** | 生成发送到公共 IP 地址的流量的客户端的公共 IP 地址。 |
| **SourcePort** | 端口号范围介于0到65535之间。 |
| **DestPublicIpAddress** | 公共 IP 地址。 |
| **DestPort** | 端口号范围介于0到65535之间。 |
| 协议  | 协议类型。 可能的值包括 `tcp` 、 `udp` 、 `other` 。|

# <a name="ddosmitigationreports"></a>[DDoSMitigationReports](#tab/DDoSMitigationReports)

| 字段名称 | 说明 |
| --- | --- |
| **TimeGenerated** | 创建报表时的 UTC 日期和时间。 |
| **ResourceId** | 公共 IP 地址的资源 ID。 |
| **类别** | 对于通知，这将为 `DDoSProtectionNotifications` 。|
| **ResourceGroup** | 包含公共 IP 地址和虚拟网络的资源组。 |
| **订阅** | DDoS 保护计划订阅 ID。 |
| **资源** | 公共 IP 地址的名称。 |
| **ResourceType** | 这将始终为 `PUBLICIPADDRESS` 。 |
| **OperationName** | 对于缓解报告，这将为 `DDoSMitigationReports` 。 |
| **ReportType** | 可能的值包括 `Incremental` ：和 `PostMitigation` 。|
| **MitigationPeriodStart** | 缓解开始时的 UTC 日期和时间。  |
| **MitigationPeriodEnd** | 缓解终止时的 UTC 日期和时间。 |
| **IPAddress** | 公共 IP 地址。 |
| **AttackVectors** |  攻击类型细目。 键包括 `TCP SYN flood` 、、、 `TCP flood` `UDP flood` `UDP reflection` 和 `Other packet flood` 。|
| **TrafficOverview** |  攻击流量的细分。 键包括 `Total packets` 、 `Total packets dropped` 、 `Total TCP packets` 、 `Total TCP packets dropped` 、 `Total UDP packets` 、 `Total UDP packets dropped` 、 `Total Other packets` 和 `Total Other packets dropped` 。 |
| **协议** | 涉及的协议细目。 键包括 `TCP` 、 `UDP` 、 `Other` 。 |
| **DropReasons** | 丢弃数据包的原因的细分。 键包括 `Protocol violation invalid TCP syn` 、 `Protocol violation invalid TCP` 、 `Protocol violation invalid UDP` 、 `UDP reflection` 、 `TCP rate limit exceeded` 、 `UDP rate limit exceeded` 、 `Destination limit exceeded` `Other packet flood` `Rate limit exceeded` `Packet was forwarded to service` 、、和。 |
| **TopSourceCountries** | 传入流量的前10个源国家/地区的细分。 |
| **TopSourceCountriesForDroppedPackets** | 已缓解的攻击流量的前10个源国家/地区的细分。 |
| **TopSourceASNs** | 对传入流量 (ASN) 的前10个源自治系统编号的细分。  |
| **SourceContinents** | 传入流量的源大洲的细分。 |
***

## <a name="enable-diagnostic-logging-on-all-public-ips"></a>对所有公共 Ip 启用诊断日志记录

此 [模板](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/Enable%20Diagnostic%20Logging/Azure%20Policy) 创建一个 Azure 策略定义，用于在定义的作用域内的所有公共 IP 日志上自动启用诊断日志记录。

[![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%2520DDoS%2520Protection%2FEnable%2520Diagnostic%2520Logging%2FAzure%2520Policy%2FDDoSLogs.json)

## <a name="view-log-data-in-workbooks"></a>查看工作簿中的日志数据

### <a name="azure-sentinel-data-connector"></a>Azure Sentinel 数据连接器

可以将日志连接到 Azure Sentinel，查看和分析工作簿中的数据，创建自定义警报，并将其纳入调查过程。 若要连接到 Azure Sentinel，请参阅 [连接到 Azure sentinel](../sentinel/connect-azure-ddos-protection.md)。 

![Azure Sentinel DDoS 连接器](./media/ddos-attack-telemetry/azure-sentinel-ddos.png)

### <a name="azure-ddos-protection-workbook"></a>Azure DDoS 保护工作簿

可以使用此 Azure 资源管理器 (ARM) 模板来部署攻击分析工作簿。 利用此工作簿，可以可视化多个可筛选面板中的攻击数据，轻松了解面临的威胁。 

[![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%2520DDoS%2520Protection%2FAzure%2520DDoS%2520Protection%2520Workbook%2FAzureDDoSWorkbook_ARM.json)

![DDoS 保护工作簿](./media/ddos-attack-telemetry/ddos-attack-analytics-workbook.png)

## <a name="validate-and-test"></a>验证和测试

若要模拟 DDoS 攻击来验证日志，请参阅 [验证 ddos 检测](test-through-simulations.md)。

## <a name="next-steps"></a>后续步骤

在本教程中，你将了解：

- 配置 DDoS 诊断日志，包括通知、缓解报告和缓解流日志。 
- 对定义的作用域中的所有公共 Ip 启用诊断日志记录。
- 查看工作簿中的日志数据。

若要了解如何配置攻击警报，请继续学习下一教程。

> [!div class="nextstepaction"]
> [查看和配置 DDoS 防护警报](alerts.md)
