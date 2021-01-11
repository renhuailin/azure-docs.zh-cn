---
title: " (预览版 Azure Monitor 疑难解答日志) "
description: 使用 Azure Monitor 可以快速或定期调查问题、排查代码或配置问题或解决问题，这通常依赖于在大量数据中搜索特定的见解。
author: osalzberg
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 12/29/2020
ms.openlocfilehash: 816cdddc1f3d0a9bc9ebc3f277bc223a688cba31
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2021
ms.locfileid: "98067357"
---
# <a name="azure-monitor-troubleshooting-logs-preview"></a> (预览版 Azure Monitor 疑难解答日志) 
使用 Azure Monitor 可以快速、/或定期调查问题、对代码或配置问题进行故障排除或解决问题，这些情况通常依赖于在大量数据中搜索特定的见解。

>[!NOTE]
> * 日志疑难解答处于预览模式。
>* 请与 [Log Analytics 团队](mailto:orens@microsoft.com) 联系，以解决任何问题或应用功能。
## <a name="troubleshoot-and-query-your-code-or-configuration-issues"></a>对你的代码或配置问题进行故障排除和查询
使用 KQL，使用 Azure Monitor 疑难解答日志获取记录，并以更便宜的方式调查问题和问题。
故障排除日志通过为你提供基本的故障排除功能来 decrees 你的费用。

> [!NOTE]
>* 故障排除模式的决策是可配置的。
>* 日志故障排除可以应用于特定的表，当前位于 "容器日志" 和 "应用跟踪" 表中。
>* 可用的保留期为4天，可以增加额外的费用。
>* 默认情况下，这些表继承工作区保持期。 若要避免额外的费用，建议更改这些表的保持期。 [单击此处了解如何更改表保留](https://docs.microsoft.com//azure/azure-monitor/platform/manage-cost-storage)。

## <a name="turn-on-troubleshooting-logs-on-your-tables"></a>打开表的故障排除日志

若要启用工作区中的疑难解答日志，需要使用以下 API 调用。
```http
PUT https://PortalURL/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables/{tableName}

(With body in the form of a GET single table request response)

Response:

{
        "properties": {
          "retentionInDays": 40,
          "isTroubleshootingAllowed": true,
          "isTroubleshootEnabled": true
        },
        "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables/{tableName}",
        "name": "{tableName}"
      }
```
## <a name="check-if-the-troubleshooting-logs-feature-is-enabled-for-a-given-table"></a>检查是否为给定表启用了疑难解答日志功能
若要检查是否为给定表启用了疑难解答日志，可以使用以下 API 调用。

```http
GET https://PortalURL/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables/{tableName}

Response: 
"properties": {
          "retentionInDays": 30,
          "isTroubleshootingAllowed": true,
          "isTroubleshootEnabled": true,
          "lastTroubleshootDate": "Thu, 19 Nov 2020 07:40:51 GMT"
        },
        "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/microsoft.operationalinsights/workspaces/{workspaceName}/tables/{tableName}",
        "name": " {tableName}"

```
## <a name="check-if-the-troubleshooting-logs-feature-is-enabled-for-all-of-the-tables-in-a-workspace"></a>检查是否为工作区中的所有表启用了疑难解答日志功能
若要检查哪些表启用了疑难解答日志，可以使用以下 API 调用。

```http
GET "https://PortalURL/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables"

Response: 
{
          "properties": {
            "retentionInDays": 30,
            "isTroubleshootingAllowed": true,
            "isTroubleshootEnabled": true,
            "lastTroubleshootDate": "Thu, 19 Nov 2020 07:40:51 GMT"
          },
          "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/microsoft.operationalinsights/workspaces/{workspaceName}/tables/table1",
          "name": "table1"
        },
        {
          "properties": {
            "retentionInDays": 7,
            "isTroubleshootingAllowed": true
          },
          "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/microsoft.operationalinsights/workspaces/{workspaceName}/tables/table2",
          "name": "table2"
        },
        {
          "properties": {
            "retentionInDays": 7,
            "isTroubleshootingAllowed": false
          },
          "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/microsoft.operationalinsights/workspaces/{workspaceName}/tables/table3",
          "name": "table3"
        }
```
## <a name="turn-off-troubleshooting-logs-on-your-tables"></a>关闭表的故障排除日志

若要在工作区中关闭疑难解答日志，需要使用以下 API 调用。
```http
PUT https://PortalURL/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables/{tableName}

(With body in the form of a GET single table request response)

Response:

{
        "properties": {
          "retentionInDays": 40,
          "isTroubleshootingAllowed": true,
          "isTroubleshootEnabled": false
        },
        "id": "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/tables/{tableName}",
        "name": "{tableName}"
      }
```
>[!TIP]
>* 您可以使用任何 REST API 工具来运行这些命令。 [阅读详细信息](https://docs.microsoft.com/rest/api/azure/)
>* 需要使用持有者令牌进行身份验证。 [阅读详细信息](https://social.technet.microsoft.com/wiki/contents/articles/51140.azure-rest-management-api-the-quickest-way-to-get-your-bearer-token.aspx)

>[!NOTE]
>* "IsTroubleshootingAllowed" 标志–描述是否允许在服务中使用表
>* "IsTroubleshootEnabled" 指示是否为表启用了功能-可以打开或关闭 (true 或 false) 
>* 禁用特定表的 "isTroubleshootEnabled" 标志后，只可能在上一次启用日期之后的一周内重新启用它。
>* 目前仅支持此项 (某些其他 Sku 也将在将来的) 提供支持- [详细了解定价](https://docs.microsoft.com/services-hub/health/azure_pricing)。

## <a name="query-limitations-for-troubleshooting"></a>故障排除查询限制
标记为 "疑难解答日志" 的表有一些限制：
*   将获取较少的处理资源，因此不适合大型仪表板、复杂分析或许多并发 API 调用。
*   查询限制为两天的时间范围。
* 清除将不起作用-请 [阅读更多有关清除的信息](https://docs.microsoft.com/rest/api/loganalytics/workspacepurge/purge)。
* 此服务不支持警报。
## <a name="next-steps"></a>后续步骤
* [编写查询](https://docs.microsoft.com/azure/data-explorer/write-queries)


