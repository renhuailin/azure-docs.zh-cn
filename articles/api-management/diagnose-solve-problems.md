---
title: Azure API 管理诊断和解决问题
description: 了解如何使用 Azure 门户中的“诊断和解决”工具在 Azure API 管理中排查 API 的问题。
author: rzhang628
ms.service: api-management
ms.topic: article
ms.date: 02/05/2021
ms.author: rongzhang
ms.openlocfilehash: 9dcbd1bf789d199b0561587949a5381027518ca3
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123436403"
---
# <a name="azure-api-management-diagnostics-overview"></a>Azure API 管理诊断概述

在 Azure API 管理中构建和管理 API 时，你需要为任何可能出现的问题（从 404“未找到”错误到 502“错误的网关”错误）做好准备。 API 管理诊断是智能的交互式体验，无需进行配置即可帮助你排查 APIM 中发布的 API 的问题。 在发布的 API 出现问题时，API 管理诊断会指出问题所在，并指导你获取正确信息以快速排查并解决问题。

尽管此体验对于 API 在过去 24 小时内出现的问题最有帮助，但是，所有的诊断图都随时可用于进行分析。

[!NOTE] 消耗层目前不支持诊断和解决问题。

## <a name="open-api-management-diagnostics"></a>打开 API 管理诊断

若要访问 API 管理诊断，请导航到 [Azure 门户](https://portal.azure.com)中的 API 管理服务实例。 在左侧导航栏中，选择“诊断并解决问题”。

:::image type="content" source="media/diagnose-solve-problems/apim-diagnostic-home.png" alt-text="展示如何导航到诊断的屏幕截图。":::



## <a name="intelligent-search"></a>智能搜索

可在页面顶部的搜索栏中搜索故障或疑问。 该搜索还有助于查找可帮助排查或解决问题的工具。 

:::image type="content" source="media/diagnose-solve-problems/intelligent-search.png" alt-text="智能搜索的屏幕截图。":::


## <a name="troubleshooting-categories"></a>疑难解答类别

可按类别来排查问题。 可在各个类别内分析与 API 性能、网关、API 策略和服务层级相关的常见问题。 每个类别还会提供更具体的诊断检查。 

:::image type="content" source="media/diagnose-solve-problems/troubleshooting-category.png" alt-text="类别概述的屏幕截图。":::


### <a name="availability-and-performance"></a>可用性和性能

在此类别下检查 API 可用性和性能问题。 在选择此类别磁贴后，你将在交互式界面中看到几个常见检查的建议。 单击每个检查可深入了解每个问题的细节。 该检查还将提供图形，显示 API 性能和性能问题摘要。 例如，你的 API 服务在后端上一个小时内可能遇到了 5xx 错误和超时。 

:::image type="content" source="media/diagnose-solve-problems/category-interactive-search-1.png" alt-text="交互式界面检查的屏幕截图 1。":::



:::image type="content" source="media/diagnose-solve-problems/category-interactive-search-2.png" alt-text="交互式界面检查的屏幕截图 2。":::

### <a name="api-policies"></a>API 策略

此类别检测错误并通知你策略问题。 

一个相似的交互式界面会引导你使用数据指标来帮助排查 API 策略配置问题。

:::image type="content" source="media/diagnose-solve-problems/proxy-policies.png" alt-text="API 策略类别磁贴的屏幕截图。":::

### <a name="gateway-performance"></a>网关性能 

对于网关上的网关请求或响应或任何 4xx 或 5xx 错误，请使用此类别来进行监视和故障排除。 同样，利用该交互式界面，可以深入了解要针对 API 网关性能进行检查的特定区域。 

:::image type="content" source="media/diagnose-solve-problems/gateway-performance-tile.png" alt-text="网关性能类别磁贴的屏幕截图。":::

### <a name="service-upgrade"></a>服务升级

此类别检查你目前使用哪个服务层级 (SKU)，并提醒你升级，以避免任何可能与该层级相关的问题。 相同的交互式界面会帮助你深入了解更多图形和摘要检查结果。 

:::image type="content" source="media/diagnose-solve-problems/service-sku.png" alt-text="服务升级类别磁贴的屏幕截图。":::

## <a name="search-documentation"></a>搜索文档

除了诊断和解决问题工具之外，还可以搜索与问题相关的故障排除文档。 在对服务运行诊断之后，请选择交互式界面中的“搜索文档”。 

 :::image type="content" source="media/diagnose-solve-problems/search-documentation.png" alt-text="如何使用“搜索文档”功能的屏幕截图 1。":::


 :::image type="content" source="media/diagnose-solve-problems/search-documentation-2.png" alt-text="如何使用“搜索文档”的屏幕截图 2。":::


## <a name="next-steps"></a>后续步骤

* 另外还可以使用 [API 分析](howto-use-analytics.md)来分析 API 的使用情况和性能。 
* 是否要使用诊断来排查 Web 应用问题？ 请在[此处](../app-service/overview-diagnostics.md)阅读相关内容
* 利用诊断来检查 Azure Kubernetes 服务问题。 请参阅 [AKS 上的诊断](../aks/concepts-diagnostics.md)
* 通过在标题中添加“[Diag]”，在 [UserVoice](https://feedback.azure.com/forums/248703-api-management) 上发布问题或反馈。
