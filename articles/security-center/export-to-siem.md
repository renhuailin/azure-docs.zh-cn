---
title: 将警报从 Azure 安全中心流式传输到安全信息和事件管理 (SIEM) 系统和其他监视解决方案
description: 了解如何将安全警报流式传输到 Azure Sentinel、第三方 SIEM、SOAR 或 ITSM 解决方案
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 10/06/2020
ms.author: memildin
ms.openlocfilehash: ad4d401072bc96784b45c0f5fd43d08f3de8ade0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124823831"
---
# <a name="stream-alerts-to-a-siem-soar-or-it-service-management-solution"></a>将警报流式传输到 SIEM、SOAR 或 IT 服务管理解决方案

Azure 安全中心可以将安全警报流式传输到最常用的安全信息和事件管理 (SIEM)、安全业务流程自动响应 (SOAR) 和 IT 服务管理 (ITSM) 解决方案中。

Azure 本机工具可确保你可以查看当前使用的所有最常用解决方案中的警报数据，其中包括：

- **Azure Sentinel**
- **Splunk Enterprise and Splunk Cloud**
- **IBM 的 QRadar**
- **ServiceNow**
- **ArcSight**
- **Power BI**
- **Palo Alto Networks**

## <a name="stream-alerts-to-azure-sentinel"></a>将警报流式传输到 Azure Sentinel 

安全中心以本机方式集成到 Azure Sentinel、Azure 的云原生 SIEM 和 SOAR 解决方案。 

[了解有关 Azure Sentinel 的详细信息](../sentinel/overview.md)。

### <a name="azure-sentinels-connectors-for-security-center"></a>适用于安全中心的 Azure Sentinel 连接器

Azure Sentinel 包括 Azure 安全中心在订阅和租户级别的内置连接器：

- [在订阅级别将警报流式传输到 Azure Sentinel](../sentinel/connect-azure-security-center.md)
- [将租户中的所有订阅连接到 Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-security-center-auto-connect-to-sentinel/ba-p/1387539) 

将 Azure Defender 连接到 Azure Sentinel 时，系统会在这两项服务之间同步已引入到 Azure Sentinel 中的 Azure Defender 警报的状态。 例如，当某个警报在 Azure Defender 中处于已关闭状态时，该警报在 Azure Sentinel 中也会显示为已关闭。 在 Azure Defender 中更改警报的状态不会影响包含同步 Azure Sentinel 警报的任何 Azure Sentinel 事件的状态，只会影响同步警报自身的状态。

启用此预览功能（即“双向警报同步”）后，系统会自动将原始 Azure Defender 警报的状态与包含这些 Azure Defender 警报副本的 Azure Sentinel 事件的状态同步。 例如，当包含 Azure Defender 警报的 Azure Sentinel 事件被关闭时，Azure Defender 将自动关闭相应的原始警报。

若要了解详细信息，请参阅[从 Azure 安全中心连接 Azure Defender 警报](../sentinel/connect-azure-security-center.md)。

> [!NOTE]
> 双向警报同步功能在云中 Azure 政府不可用。 

### <a name="configure-ingestion-of-all-audit-logs-into-azure-sentinel"></a>配置将所有审核日志引入 Azure Sentinel 

调查 Azure Sentinel 中的安全中心警报的另一种方法是将审核日志流式传输到 Azure Sentinel：
    - [连接 Windows 安全事件](../sentinel/connect-windows-security-events.md)
    - [使用 Syslog 从基于 Linux 的源收集数据](../sentinel/connect-syslog.md)
    - [连接 Azure 活动日志中的数据](../sentinel/data-connectors-reference.md#azure-activity)

> [!TIP]
> 我们将根据 Azure Sentinel 中为分析引入的数据量和 Azure Monitor Log Analytics 工作区中存储的数据量对 Azure Sentinel 进行计费。 Azure Sentinel 提供灵活、可预测的定价模型。 [有关详细信息，请参阅 Azure Sentinel 定价页面](https://azure.microsoft.com/pricing/details/azure-sentinel/)。


## <a name="stream-alerts-with-microsoft-graph-security-api"></a>使用 Microsoft Graph 安全性 API 流式传输警报

安全中心具有与 Microsoft Graph 安全性 API 的现成集成。 无需进行配置，也不需要额外的费用。 

可以使用此 API 将警报从整个租户（以及许多其他 Microsoft 安全产品的数据）流式传输到第三方 SIEM 和其他常用平台：

- **Splunk Enterprise and Splunk Cloud** - [使用适用于 Splunk 的 Microsoft Graph 安全性 API 附加产品](https://splunkbase.splunk.com/app/4564/) 
- **Power BI** - [连接到 Power BI Desktop 中的 Microsoft Graph 安全性 API](/power-bi/connect-data/desktop-connect-graph-security)
- **ServiceNow** - [按照说明从 ServiceNow Store 中安装和配置 Microsoft Graph 安全性 API 应用程序](https://docs.servicenow.com/bundle/orlando-security-management/page/product/secops-integration-sir/secops-integration-ms-graph/task/ms-graph-install.html)
- **QRadar** - [IBM 通过 Microsoft Graph API 为 Azure 安全中心提供的设备支持模块](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/com.ibm.dsm.doc/c_dsm_guide_ms_azure_security_center_overview.html) 
- **Palo Alto Networks**、**Anomali**、**Lookout**、**InSpark** 等 - [Microsoft Graph 安全性 API](https://www.microsoft.com/security/business/graph-security-api#office-MultiFeatureCarousel-09jr2ji)

[详细了解 Microsoft Graph 安全性 API](https://www.microsoft.com/security/business/graph-security-api)。


## <a name="stream-alerts-with-azure-monitor"></a>使用 Azure Monitor 流式传输警报 

若要将警报流式传输到 ArcSight、Splunk、SumoLogic、Syslog 服务器、LogRhythm、Logz.io Cloud Observability Platform 和其他监视解决方案    。 请通过 Azure 事件中心连接安全中心与 Azure monitor：

1. 启用[连续导出](continuous-export.md)，以在订阅级别将安全中心警报流式传输到专用 Azure 事件中心。 
    > [!TIP]
    > 若要在管理组级别使用 Azure Policy 执行此操作，请参阅[大规模创建连续导出自动化配置](continuous-export.md?tabs=azure-policy#configure-continuous-export-at-scale-using-the-supplied-policies)

1. [使用 Azure Monitor 的内置连接器将 Azure 事件中心连接到首选解决方案](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration)。

1. （可选）将原始日志流式传输到 Azure 事件中心并连接到首选解决方案。 有关详细信息，请参阅[提供的监视数据](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md#monitoring-data-available)。

> [!TIP]
> 若要查看导出的数据类型的事件架构，请访问[事件中心事件架构](https://aka.ms/ASCAutomationSchemas)。


## <a name="next-steps"></a>后续步骤

本页介绍了如何确保你的 Azure 安全中心警报数据在所选的 SIEM、SOAR 或 ITSM 工具中可用。 如需查看相关材料，请参阅：

- [什么是 Azure Sentinel？](../sentinel/overview.md)
- [Azure 安全中心中的警报验证](security-center-alert-validation.md) - 验证是否已正确配置警报
- [连续导出安全中心数据](continuous-export.md)