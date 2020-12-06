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
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 5c3bfbdf133777f0bc219d1306f80bd4d38b56ea
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2020
ms.locfileid: "96746261"
---
# <a name="configure-ddos-attack-mitigation-reports-and-flow-logs"></a>配置 DDoS 攻击缓解报表和流日志 

Azure DDoS 防护标准通过 DDoS 攻击分析提供详细的攻击见解和可视化效果。 保护其虚拟网络免受 DDoS 攻击的客户可通过攻击缓解报告和缓解流日志来详细了解攻击流量以及缓解攻击的操作。 丰富的遥测通过 Azure Monitor 公开，包括在 DDoS 攻击期间的详细指标。 可以针对 DDoS 防护公开的任何 Azure Monitor 指标配置警报。 可以通过 Azure Monitor 诊断接口，将日志记录与 [Azure Sentinel](../sentinel/connect-azure-ddos-protection.md)、Splunk (Azure 事件中心) 、OMS Log Analytics 和 azure 存储进行进一步集成，以实现高级分析。

本教程介绍以下操作：

> [!div class="checklist"]
> * 查看和配置 DDoS 攻击缓解报告
> * 查看和配置 DDoS 攻击缓解流日志

## <a name="prerequisites"></a>先决条件

- 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 在完成本教程中的步骤之前，必须先创建 [Azure DDoS 标准保护计划](manage-ddos-protection.md)。

## <a name="view-and-configure-ddos-attack-mitigation-reports"></a>查看和配置 DDoS 攻击缓解报告

攻击缓解报告使用 Netflow 协议数据，这些数据聚合在一起，提供有关资源所受攻击的详细信息。 只要公共 IP 资源受到攻击，就会在缓解措施启动时开始生成报告。 每 5 分钟生成一份增量报告，整个缓解过程结束后，生成一份缓解后报告。 这是为了确保在 DDoS 攻击持续时间较长时，能够每隔 5 分钟查看一次最新的缓解报告快照，并在攻击缓解结束时查看完整总结。 

1. 在门户左上角选择“所有服务”。
2. 在“筛选器”框中输入 Monitor。 当“Monitor”出现在结果中时，将其选中。
3. 选择“设置”下的“诊断设置”。
4. 选择包含要记录的公共 IP 地址的订阅和资源组。
5. 为“资源类型”选择“公共 IP 地址”，然后选择要为其记录指标的特定公共 IP 地址。
6. 选择“启用诊断以收集 DDoSMitigationReports 日志”，然后根据需要选择任意多个以下选项：

    - **存档到存储帐户**：将数据写入 Azure 存储帐户。 若要了解有关此选项的详细信息，请参阅 [存档资源日志](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage)。
    - **流式传输到事件中心**：允许日志接收器使用 Azure 事件中心选取日志。 事件中心将启用与 Splunk 或其他 SIEM 系统的集成。 若要了解有关此选项的详细信息，请参阅 [将资源日志流式传输到事件中心](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs)。
    - **发送到 Log Analytics**：将日志写入 Azure Monitor 服务。 若要了解有关此选项的详细信息，请参阅 [收集用于 Azure Monitor 日志的日志](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-log-analytics-workspace)。

增量缓解报告和攻击后缓解报告都包括以下字段
- 攻击途径
- 流量统计信息
- 丢弃数据包的原因
- 所涉及的协议
- 前 10 个源国家或地区
- 前 10 个源 ASN

## <a name="view-and-configure-ddos-attack-mitigation-flow-logs"></a>查看和配置 DDoS 攻击缓解流日志
攻击缓解流日志使你能够以近乎实时的状态在主动 DDoS 攻击期间查看丢弃的流量、转发的流量和其他有趣的数据点。 可以通过事件中心将此数据的常量流引入 Azure Sentinel 或第三方 SIEM 系统，以进行近乎实时的监视，采取可能的措施，并满足防御操作的需求。

1. 在门户左上角选择“所有服务”。
2. 在“筛选器”框中输入 Monitor。 当“Monitor”出现在结果中时，将其选中。
3. 选择“设置”下的“诊断设置”。
4. 选择包含要记录的公共 IP 地址的订阅和资源组。
5. 为“资源类型”选择“公共 IP 地址”，然后选择要为其记录指标的特定公共 IP 地址。
6. 选择“启用诊断以收集 DDoSMitigationFlowLogs 日志”，然后根据需要选择任意多个以下选项：

    - **存档到存储帐户**：将数据写入 Azure 存储帐户。 若要了解有关此选项的详细信息，请参阅 [存档资源日志](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage)。
    - **流式传输到事件中心**：允许日志接收器使用 Azure 事件中心选取日志。 事件中心将启用与 Splunk 或其他 SIEM 系统的集成。 若要了解有关此选项的详细信息，请参阅 [将资源日志流式传输到事件中心](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs)。
    - **发送到 Log Analytics**：将日志写入 Azure Monitor 服务。 若要了解有关此选项的详细信息，请参阅 [收集用于 Azure Monitor 日志的日志](../azure-monitor/platform/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-log-analytics-workspace)。

此 [模板](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/Enable%20Diagnostic%20Logging/Azure%20Policy) 创建 Azure 策略定义以启用诊断日志记录。

### <a name="azure-sentinel-data-connector"></a>Azure Sentinel 数据连接器

可以将攻击缓解流日志连接到 Azure Sentinel，查看和分析工作簿中的数据，创建自定义警报，并将其纳入调查过程。 若要连接到 Azure Sentinel，请参阅 [连接到 Azure sentinel](../sentinel/connect-azure-ddos-protection.md)。 

![Azure Sentinel DDoS 连接器](./media/ddos-attack-telemetry/azure-sentinel-ddos.png)

### <a name="azure-ddos-protection-workbook"></a>Azure DDoS 保护工作簿

若要在 Azure 分析仪表板中查看流日志数据，可从 https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/Azure%20DDoS%20Protection%20Workbook 导入示例仪表板

流日志将具有以下字段： 
- 源 IP
- 目标 IP
- Source Port 
- 目标端口 
- 协议类型 
- 在缓解期间执行的操作

![DDoS 保护工作簿](./media/ddos-attack-telemetry/ddos-attack-analytics-workbook.png)

只有在公共 IP 地址的虚拟网络上启用了 DDoS 保护标准后，攻击分析才会生效。 

## <a name="sample-log-outputs"></a>示例日志输出

以下屏幕截图是示例日志输出：

### <a name="ddosmitigationflowlogs"></a>DDoSMitigationFlowLogs

![DDoS 保护 DDoSMitigationFlowLogs](./media/ddos-attack-telemetry/ddos-mitigation-flow-logs.png)

### <a name="ddosprotectionnotifications"></a>DDoSProtectionNotifications

![DDoS 保护 DDoSProtectionNotifications](./media/ddos-attack-telemetry/ddos-protection-notifications.png)

### <a name="ddosmitigationreports"></a>DDoSMitigationReports

![DDoS 保护 DDoSMitigationReports](./media/ddos-attack-telemetry/ddos-mitigation-reports.png)

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

- 查看和配置 DDoS 攻击缓解报告
- 查看和配置 DDoS 攻击缓解流日志

若要了解如何测试和模拟 DDoS 攻击，请参阅模拟测试指南：

> [!div class="nextstepaction"]
> [通过模拟进行测试](test-through-simulations.md)