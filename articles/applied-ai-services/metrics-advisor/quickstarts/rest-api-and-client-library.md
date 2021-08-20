---
title: 指标顾问客户端库 REST API
titleSuffix: Azure Cognitive Services
description: 参考本快速入门，将应用程序从 Azure 认知服务连接到指标顾问 API。
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: quickstart
ms.date: 07/06/2021
ms.author: mbullwin
zone_pivot_groups: programming-languages-metrics-monitor
ms.openlocfilehash: 7f7f9c69de17779f483ce4e4155a8953f9c9da91
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "114341427"
---
# <a name="quickstart-use-the-client-libraries-or-rest-apis-to-customize-your-solution"></a>快速入门：使用客户端库或 REST API 自定义解决方案

开始使用指标顾问 REST API 或客户端库。 请按照以下步骤安装程序包并试用基本任务的示例代码。

使用指标顾问执行以下操作：

* 从数据源添加数据馈送
* 检查引入状态
* 配置检测和警报 
* 查询异常情况检测结果
* 诊断异常


::: zone pivot="programming-language-csharp"

[!INCLUDE [REST API quickstart](../includes/quickstarts/csharp.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [REST API quickstart](../includes/quickstarts/java.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

[!INCLUDE [REST API quickstart](../includes/quickstarts/javascript.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [REST API quickstart](../includes/quickstarts/python.md)]

::: zone-end

::: zone pivot="programming-language-rest-api"

[!INCLUDE [REST API quickstart](../includes/quickstarts/rest-api.md)]

::: zone-end

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除认知服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。

* [Portal](../../../cognitive-services/cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services/cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>后续步骤

- [使用 Web 门户](web-portal.md)
- [加入数据馈送](../how-tos/onboard-your-data.md)
    - [管理数据馈送](../how-tos/manage-data-feeds.md)
    - [不同数据源的配置](../data-feeds-from-different-sources.md)
- [配置指标并微调检测配置](../how-tos/configure-metrics.md)
- [使用反馈调整异常检测](../how-tos/anomaly-feedback.md)