---
title: Azure DDoS 防护标准报表和流日志
description: 了解如何配置报表和流日志。
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/28/2020
ms.author: yitoh
ms.openlocfilehash: eded4bfc37df72b9163aeac51d66c973b24838b2
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124750254"
---
# <a name="view-and-configure-ddos-diagnostic-logging"></a>查看和配置 DDoS 诊断日志记录

Azure DDoS 防护标准通过 DDoS 攻击分析提供详细的攻击见解和可视化效果。 保护其虚拟网络免受 DDoS 攻击的客户可通过攻击缓解报告和缓解流日志来详细了解攻击流量以及缓解攻击的操作。 在 DDoS 攻击期间通过 Azure Monitor 公开丰富的遥测数据（包括详细的指标）。 可以针对 DDoS 防护公开的任何 Azure Monitor 指标配置警报。 可将记录与 [Azure Sentinel](../sentinel/data-connectors-reference.md#azure-ddos-protection)、Splunk（Azure 事件中心）、OMS Log Analytics 和 Azure 存储进一步集成，以通过 Azure Monitor 诊断界面进行高级分析。

以下诊断日志适用于 Azure DDoS 防护标准： 

- **DDoSProtectionNotifications**：通知会在公共 IP 资源遭受攻击以及攻击风险缓解结束时向你发出通知。
- **DDoSMitigationFlowLogs**：通过风险攻击缓解流日志，可在活动 DDoS 攻击期间近乎实时地查看丢弃流量、转发流量和其他相关数据点。 可通过事件中心将此数据的恒定流引入 Azure Sentinel 或第三方 SIEM 系统，以实现近实时监视，还可采取可能的措施，解决防御运营需求。
- **DDoSMitigationReports**：攻击风险缓解报表使用聚合的 Netflow 协议数据来提供有关资源受攻击的详细信息。 只要公共 IP 资源受到攻击，就会在缓解措施启动时开始生成报告。 每 5 分钟生成一份增量报告，整个缓解过程结束后，生成一份缓解后报告。 这是为了确保在 DDoS 攻击持续时间较长时，能够每隔 5 分钟查看一次最新的缓解报告快照，并在攻击缓解结束时查看完整总结。 
- **AllMetrics**：提供在 DDoS 攻击期间可用的所有可能指标。 

本教程介绍以下操作：

> [!div class="checklist"]
> * 配置 DDoS 诊断日志，包括通知、风险缓解报表和缓解流日志。 
> * 在定义的范围内对所有公共 IP 启用诊断日志记录。
> * 查看工作簿中的日志数据。

## <a name="prerequisites"></a>先决条件

- 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 在完成本教程中的步骤之前，必须先创建 [Azure DDoS 标准防护计划](manage-ddos-protection.md)，并且必须在虚拟网络上启用 DDoS 防护标准。
- DDoS 监视分配给虚拟网络中的资源的公共 IP 地址。 如果虚拟网络中没有任何具有公共 IP 地址的资源，必须首先创建具有公共 IP 地址的资源。 你可以监视 [Azure 服务的虚拟网络](../virtual-network/virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network)中列出的通过资源管理器（非经典）部署的所有资源（包括后端虚拟机位于虚拟网络中的 Azure 负载均衡器）的公共 IP，但 Azure 应用服务环境除外。 可快速创建 [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 虚拟机继续本教程的内容。    

## <a name="configure-ddos-diagnostic-logs"></a>配置 DDoS 诊断日志

如果要针对环境中的所有公共 IP 自动启用诊断日志记录，请跳到[针对所有公共 IP 启用诊断日志记录](#enable-diagnostic-logging-on-all-public-ips)。

1. 在门户左上角选择“所有服务”。
2. 在“筛选器”框中输入 Monitor。 当“Monitor”出现在结果中时，将其选中。
3. 在“设置”下，选择“诊断设置”。
4. 选择包含要记录的公共 IP 地址的订阅和资源组。
5. 对于“资源类型”，请选择“公共 IP”，然后选择要为其启用日志的特定公共 IP 。
6. 选择“添加诊断设置”。 在“类别详细信息”下，选择所需的以下任意选项，然后选择“保存”。

    ![DDoS 诊断设置](./media/ddos-attack-telemetry/ddos-diagnostic-settings.png)

7. 在“目标详细信息”下，根据需要选择以下任意多个选项：

    - **存档到存储帐户**：将数据写入 Azure 存储帐户。 若要了解有关此选项的详细信息，请参阅[存档资源日志](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage)。
    - **流式传输到事件中心**：允许日志接收器使用 Azure 事件中心选取日志。 事件中心将启用与 Splunk 或其他 SIEM 系统的集成。 若要了解有关此选项的详细信息，请参阅[将资源日志流式传输到事件中心](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs)。
    - **发送到 Log Analytics**：将日志写入到 Azure Monitor 服务。 若要了解有关此选项的详细信息，请参阅 [收集日志以在 Azure Monitor 日志中使用](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-log-analytics-workspace)。

### <a name="query-ddos-protection-logs-in-log-analytics-workspace"></a>在日志分析工作区中查询 DDOS 防护日志

#### <a name="ddosprotectionnotifications-logs"></a>DDoSProtectionNotifications 日志

1. 在“日志分析工作区”边栏选项卡下，选择日志分析工作区。

4. 在“常规”下，单击“日志” 

5. 在查询资源管理器中，键入以下 Kusto 查询，将时间范围更改为“自定义”，并将时间范围更改为过去 3 个月。 然后单击“运行”。

    ```kusto
    AzureDiagnostics
    | where Category == "DDoSProtectionNotifications"
    ```

#### <a name="ddosmitigationflowlogs"></a>DDoSMitigationFlowLogs

1. 现在，将查询更改为如下所示，保留相同的时间范围，然后单击“运行”。

    ```kusto
    AzureDiagnostics
    | where Category == "DDoSMitigationFlowLogs"
    ```

#### <a name="ddosmitigationreports"></a>DDoSMitigationReports

1. 现在，将查询更改为如下所示，保留相同的时间范围，然后单击“运行”。

    ```kusto
    AzureDiagnostics
    | where Category == "DDoSMitigationReports"
    ```

### <a name="log-schemas"></a>日志架构

下表列出了字段的名称和描述：

# <a name="ddosprotectionnotifications"></a>[DDoSProtectionNotifications](#tab/DDoSProtectionNotifications)

| 字段名称 | 说明 |
| --- | --- |
| **TimeGenerated** | 创建通知时的日期和时间 (UTC)。 |
| **ResourceId** | 公共 IP 的资源 ID。 |
| **类别** | 对于通知，这将为 `DDoSProtectionNotifications`。|
| **ResourceGroup** | 包含公共 IP 和虚拟网络的资源组。 |
| **SubscriptionId** | 你的 DDoS 防护计划订阅 ID。 |
| **资源** | 公共 IP 的名称。 |
| **ResourceType** | 这将始终为 `PUBLICIPADDRESS`。 |
| **OperationName** | 对于通知，这将为 `DDoSProtectionNotifications`。  |
| **消息** | 攻击的详细信息。 |
| **类型** | 通知的类型。 可能的值包括 `MitigationStarted`。 `MitigationStopped`. |
| **PublicIpAddress** | 你的公共 IP。 |

# <a name="ddosmitigationflowlogs"></a>[DDoSMitigationFlowLogs](#tab/DDoSMitigationFlowLogs)

| 字段名称 | 说明 |
| --- | --- |
| **TimeGenerated** | 创建流日志时的日期和时间 (UTC)。 |
| **ResourceId** | 公共 IP 的资源 ID。 |
| **类别** | 对于流日志，这将为 `DDoSMitigationFlowLogs`。|
| **ResourceGroup** | 包含公共 IP 和虚拟网络的资源组。 |
| **SubscriptionId** | 你的 DDoS 防护计划订阅 ID。 |
| **资源** | 公共 IP 的名称。 |
| **ResourceType** | 这将始终为 `PUBLICIPADDRESS`。 |
| **OperationName** | 对于流日志，这将为 `DDoSMitigationFlowLogs`。 |
| **消息** | 攻击的详细信息。 |
| **SourcePublicIpAddress** | 客户端的公共 IP，该客户端生成到公共 IP 的流量。 |
| **SourcePort** | 端口号范围介于 0 到 65535 之间。 |
| **DestPublicIpAddress** | 你的公共 IP。 |
| **DestPort** | 端口号范围介于 0 到 65535 之间。 |
| 协议  | 协议的类型。 可能的值包括 `tcp`、`udp`、`other`。|

# <a name="ddosmitigationreports"></a>[DDoSMitigationReports](#tab/DDoSMitigationReports)

| 字段名称 | 说明 |
| --- | --- |
| **TimeGenerated** | 创建报表时的日期和时间 (UTC)。 |
| **ResourceId** | 公共 IP 的资源 ID。 |
| **类别** | 对于通知，这将为 `DDoSMitigationReports`。|
| **ResourceGroup** | 包含公共 IP 和虚拟网络的资源组。 |
| **SubscriptionId** | 你的 DDoS 防护计划订阅 ID。 |
| **资源** | 公共 IP 的名称。 |
| **ResourceType** | 这将始终为 `PUBLICIPADDRESS`。 |
| **OperationName** | 对于风险缓解报表，这将为 `DDoSMitigationReports`。 |
| **ReportType** | 可能的值包括 `Incremental`、`PostMitigation`。|
| **MitigationPeriodStart** | 风险缓解开始时的日期和时间 (UTC)。  |
| **MitigationPeriodEnd** | 风险缓解终止时的日期和时间 (UTC)。 |
| **IPAddress** | 你的公共 IP。 |
| **AttackVectors** |  攻击类型的明细。 键包括 `TCP SYN flood`、`TCP flood`、`UDP flood`、`UDP reflection` 和 `Other packet flood`。|
| **TrafficOverview** |  攻击流量的明细。 键包括 `Total packets`、`Total packets dropped`、`Total TCP packets`、`Total TCP packets dropped`、`Total UDP packets`、`Total UDP packets dropped`、`Total Other packets` 和 `Total Other packets dropped`。 |
| **协议** | 所包含协议的明细。 键包括 `TCP`、`UDP`、`Other`。 |
| **DropReasons** | 丢弃的数据包数的原因的明细。 键包括 `Protocol violation invalid TCP syn`、`Protocol violation invalid TCP`、`Protocol violation invalid UDP`、`UDP reflection`、`TCP rate limit exceeded`、`UDP rate limit exceeded`、`Destination limit exceeded`、`Other packet flood`、`Rate limit exceeded` 和 `Packet was forwarded to service`。 |
| **TopSourceCountries** | 传入流量的前 10 个源国家/地区的明细。 |
| **TopSourceCountriesForDroppedPackets** | 已缓解攻击流量的前 10 个源国家/地区的明细。 |
| **TopSourceASNs** | 传入流量的前 10 个源自治系统编号 (ASN) 的明细。  |
| **SourceContinents** | 传入流量的源大洲的明细。 |
***

## <a name="enable-diagnostic-logging-on-all-public-ips"></a>对所有公共 IP 启用诊断日志记录

此[内置策略](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F752154a7-1e0f-45c6-a880-ac75a7e4f648)会自动对定义范围内的所有公共 IP 日志启用诊断日志记录。 有关内置策略的完整列表，请参阅 [Azure DDoS 防护标准版的 Azure Policy 内置定义](policy-reference.md)。

## <a name="view-log-data-in-workbooks"></a>查看工作簿中的日志数据

### <a name="azure-sentinel-data-connector"></a>Azure Sentinel 数据连接器

可以将日志连接到 Azure Sentinel，查看和分析工作簿中的数据，创建自定义警报，并将其纳入调查过程。 若要连接到 Azure Sentinel，请参阅[连接到 Azure sentinel](../sentinel/data-connectors-reference.md#azure-ddos-protection)。 

![Azure Sentinel DDoS 连接器](./media/ddos-attack-telemetry/azure-sentinel-ddos.png)

### <a name="azure-ddos-protection-workbook"></a>Azure DDoS 防护工作簿

可以使用[此 Azure 资源管理器 (ARM) 模板](https://aka.ms/ddosworkbook)来部署攻击分析工作簿。 利用此工作簿，可在多个可筛选面板上可视化攻击数据，以轻松了解面临的威胁。 

[![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%20DDoS%20Protection%2FWorkbook%20-%20Azure%20DDOS%20monitor%20workbook%2FAzureDDoSWorkbook_ARM.json)

![DDoS 保护工作簿](./media/ddos-attack-telemetry/ddos-attack-analytics-workbook.png)

## <a name="validate-and-test"></a>验证并测试

若要模拟 DDoS 攻击来验证警报，请参阅[验证 DDoS 检测](test-through-simulations.md)。

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

- 配置 DDoS 诊断日志，包括通知、风险缓解报表和缓解流日志。 
- 在定义的范围内对所有公共 IP 启用诊断日志记录。
- 查看工作簿中的日志数据。

若要了解如何配置攻击警报，请继续阅读下一教程。

> [!div class="nextstepaction"]
> [查看和配置 DDoS 防护警报](alerts.md)