---
title: 结合使用 Azure Sentinel 与 Azure Web 应用程序防火墙
description: 本文介绍如何结合使用 Azure Sentinel 与 Azure Web 应用程序防火墙 (WAF)
services: web-application-firewall
author: TreMansdoerfer
ms.service: web-application-firewall
ms.date: 10/12/2020
ms.author: victorh
ms.topic: how-to
ms.openlocfilehash: 3d905dd1e6acab8f9f6d3885c882dd9c32133cb4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "100596441"
---
# <a name="using-azure-sentinel-with-azure-web-application-firewall"></a>结合使用 Azure Sentinel 与 Azure Web 应用程序防火墙

将 Azure Web 应用程序防火墙 (WAF) 与 Azure Sentinel 结合，可以为 WAF 资源提供安全信息事件管理。 Azure Sentinel 使用 Log Analytics 提供安全分析，使你可以轻松地细分和查看 WAF 数据。 使用 Azure Sentinel，可以访问预生成的工作簿并对其进行修改，以最大程度地满足组织的需求。 该工作簿可以显示有关跨多个订阅和工作区的 Azure 内容分发网络 (CDN) 上的 WAF、Azure Front Door 上的 WAF 以及应用程序网关上的 WAF 的分析。

## <a name="waf-log-analytics-categories"></a>WAF 日志分析类别

WAF 日志分析分为以下几类：  

- 执行的所有 WAF 操作 
- 前 40 个被阻止的请求 URI 地址 
- 前 50 个事件触发器  
- 一段时间内的消息 
- 完整的消息详细信息 
- 攻击事件（按消息）  
- 一段时间内的攻击事件 
- 跟踪 ID 筛选器 
- 跟踪 ID 消息 
- 前 10 个攻击 IP 地址 
- IP 地址的攻击消息 

## <a name="waf-workbook-examples"></a>WAF 工作簿示例

以下 WAF 工作簿示例显示了示例数据：

:::image type="content" source="media//waf-sentinel/waf-actions-filter.png" alt-text="WAF 操作筛选器":::

:::image type="content" source="media//waf-sentinel/top-50-event-trigger.png" alt-text="前 50 个事件":::

:::image type="content" source="media//waf-sentinel/attack-events.png" alt-text="攻击事件":::

:::image type="content" source="media//waf-sentinel/top-10-attacking-ip-address.png" alt-text="前 10 个攻击 IP 地址":::

## <a name="launch-a-waf-workbook"></a>启动 WAF 工作簿

WAF 工作簿适用于所有 Azure Front Door、应用程序网关和 CDN WAF。 在连接这些资源中的数据之前，必须为资源启用日志分析。 

若要为每种资源启用日志分析，请分别转到 Azure Front Door、应用程序网关或 CDN 资源：

1. 选择“诊断设置”。
2. 选择“+ 添加诊断设置”。  
3. 在“诊断设置”页中：
   1. 键入名称。 
   1. 选择“发送到 Log Analytics”。 
   1. 选择日志目标工作区。 
   1. 选择要分析的日志类型：
      1. 应用程序网关：“ApplicationGatewayAccessLog”和“ApplicationGatewayFirewallLog”
      1. Azure Front Door：“FrontDoorAccessLog”和“FrontDoorFirewallLog”
      1. CDN：“AzureCdnAccessLog”
   1. 选择“保存”。

   :::image type="content" source="media//waf-sentinel/diagnostics-setting.png" alt-text="诊断设置":::

4. 在 Azure 主页的搜索栏中键入“Azure Sentinel”，然后选择“Azure Sentinel”资源 。 
2. 选择一个已经处于活动状态的工作区或创建一个新的工作区。 
3. 在左侧面板的“配置”下，选择“数据连接器” 。
4. 搜索“Microsoft Web 应用程序防火墙”，然后选择“Microsoft Web 应用程序防火墙(WAF)” 。 选择右下角的“打开连接器”页。

   :::image type="content" source="media//waf-sentinel/data-connectors.png" alt-text="数据连接器":::

8. 如果以前未执行过此操作，请按照“配置”下的说明，为要具有其日志分析数据的每个 WAF 资源执行操作。
6. 完成配置单个 WAF 资源后，请选择“后续步骤”选项卡。选择推荐的其中一个工作簿。 该工作簿将使用以前启用的所有日志分析数据。 WAF 资源现在应存在一个有效的 WAF 工作簿。

   :::image type="content" source="media//waf-sentinel/waf-workbooks.png" alt-text="WAF 工作簿":::


## <a name="next-steps"></a>后续步骤

- [详细了解 Azure Sentinel](../sentinel/overview.md)
- [详细了解 Azure Monitor 工作簿](../azure-monitor/visualize/workbooks-overview.md)
