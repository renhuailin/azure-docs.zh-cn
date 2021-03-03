---
title: Azure API 管理诊断和解决问题
description: 了解如何通过 Azure 门户中的 "诊断和解决" 工具，在 Azure API 管理中对 API 的问题进行故障排除。
author: rzhang628
ms.service: api-management
ms.topic: article
ms.date: 02/05/2021
ms.author: rongzhang
ms.openlocfilehash: d8ec04227316088983977f5b487abfa81fb5c525
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101652396"
---
# <a name="azure-api-management-diagnostics-overview"></a>Azure API 管理诊断概述

当你在 Azure API 管理中构建和管理 API 时，你希望为任何可能出现的问题（从404未找到错误到502错误的网关错误）做好准备。 API 管理诊断是一种智能交互式体验，可帮助你解决在 APIM 中发布的 API，无需进行任何配置。 当你在发布的 Api 中遇到问题时，API 管理诊断将指出问题所在，并指导你快速完成故障排除和解决问题的相关信息。

尽管在过去24小时内使用 API 时，此体验最有用，但所有诊断图形始终都可供你进行分析。

## <a name="open-api-management-diagnostics"></a>打开 API 管理诊断

要访问 API 管理诊断，请导航到 [Azure 门户](https://portal.azure.com)中的 api 管理服务实例。 在左侧导航栏中，选择“诊断并解决问题”。

:::image type="content" source="media/diagnose-solve-problems/apim-diagnostic-home.png" alt-text="屏幕截图显示如何导航到 &quot;诊断&quot;。":::



## <a name="intelligent-search"></a>智能搜索

您可以在页面顶部的搜索栏中搜索问题或问题。 搜索还可帮助你查找可帮助排查问题或解决问题的工具。 

:::image type="content" source="media/diagnose-solve-problems/intelligent-search.png" alt-text="智能搜索的屏幕截图。":::


## <a name="troubleshooting-categories"></a>类别疑难解答

您可以对类别下的问题进行故障排除。 可在每个类别内分析与 API 性能、网关、API 策略和服务层相关的常见问题。 每个类别还提供更具体的诊断检查。 

:::image type="content" source="media/diagnose-solve-problems/troubleshooting-category.png" alt-text="类别概述的屏幕截图。":::


### <a name="availability-and-performance"></a>可用性和性能

检查此类别下的 API 可用性和性能问题。 选择此类别磁贴后，你将看到在交互式界面中建议使用几个常见的检查。 单击每个检查以深入了解每个问题的详细信息。 该检查还将为您提供一个图形，显示您的 API 性能和性能问题的摘要。 例如，你的 API 服务在后端的最后一个小时内可能有5xx 错误和超时。 

:::image type="content" source="media/diagnose-solve-problems/category-interactive-search-1.png" alt-text="交互式界面检查的屏幕截图1。":::



:::image type="content" source="media/diagnose-solve-problems/category-interactive-search-2.png" alt-text="快照2：交互式界面检查。":::

### <a name="api-policies"></a>API 策略

此类别检测错误并通知你策略问题。 

类似的交互式界面将引导你使用数据指标来帮助你排查 API 策略配置问题。

:::image type="content" source="media/diagnose-solve-problems/proxy-policies.png" alt-text="API 策略类别磁贴的屏幕截图。":::

### <a name="gateway-performance"></a>网关性能 

对于网关的网关请求或响应或任何4xx 或5xx 错误，请使用此类别进行监视和故障排除。 同样，利用交互式接口，可以深入了解要检查其 API 网关性能的特定区域。 

:::image type="content" source="media/diagnose-solve-problems/gateway-performance-tile.png" alt-text="网关性能类别磁贴的屏幕截图。":::

### <a name="service-upgrade"></a>服务升级

此类别检查当前使用的服务层 (SKU) ，并提醒你升级，以避免任何可能与该层相关的问题。 同一交互界面可帮助您深入了解更多图形和摘要检查结果。 

:::image type="content" source="media/diagnose-solve-problems/service-sku.png" alt-text="服务升级类别磁贴的屏幕截图。":::

## <a name="search-documentation"></a>搜索文档

在 "诊断和解决问题" 工具中，可以搜索与问题相关的疑难解答文档。 对你的服务运行诊断后，请选择交互式界面中的 " **搜索文档** "。 

 :::image type="content" source="media/diagnose-solve-problems/search-documentation.png" alt-text="如何使用搜索文档功能的屏幕截图1。":::


 :::image type="content" source="media/diagnose-solve-problems/search-documentation-2.png" alt-text="如何使用搜索文档的屏幕截图2。":::


## <a name="next-steps"></a>后续步骤

* 还可以使用 [api](howto-use-analytics.md) 分析来分析 api 的使用情况和性能。 
* 想要对诊断中的 Web 应用问题进行故障排除？ [在此处](../app-service/overview-diagnostics.md)阅读
* 利用诊断来检查 Azure Kubernetes 服务问题。 请参阅 [AKS 上的诊断](../aks/concepts-diagnostics.md)
* 通过在标题中添加 "[诊断]"，在 [UserVoice](https://feedback.azure.com/forums/248703-api-management) 上发布你的问题或反馈。
