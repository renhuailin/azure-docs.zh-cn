---
title: 如何自行诊断并解决 Azure Spring Cloud 中的问题
description: 了解如何自行诊断并解决 Azure Spring Cloud 中的问题。
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 05/29/2020
ms.custom: devx-track-java
ms.openlocfilehash: b6997329b8e0be698d83aa5dddc32a09fb23eafb
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122015354"
---
# <a name="self-diagnose-and-solve-problems-in-azure-spring-cloud"></a>自行诊断并解决 Azure Spring Cloud 中的问题

本文适用于：✔️ Java ✔️ C#

Azure Spring Cloud 诊断是一种无需配置即可对应用进行故障排除的交互式体验。 Azure Spring Cloud 诊断会识别问题并指导你获取有助于排查和解决问题的信息。

## <a name="prerequisites"></a>先决条件

若要完成本练习，你需做好以下准备：

* Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 部署的 Azure Spring Cloud 服务实例。 按[有关如何通过 Azure CLI 来部署应用的快速入门](./quickstart.md)操作即可入门。
* 至少已在该服务实例中创建一个应用程序。

## <a name="navigate-to-the-diagnostics-page"></a>导航到诊断页

1. 登录到 Azure 门户。
2. 转到 Azure Spring Cloud 的“概览”页。
3. 在左侧导航窗格中，选择“诊断并解决问题”。

![“诊断并解决问题”对话框](media/spring-cloud-diagnose/diagnose-solve-dialog.png)

## <a name="search-logged-issues"></a>搜索记录的问题

若要查找问题，你可以通过键入关键字进行搜索，也可以选择解决方案组以浏览该类别中的所有内容。

![搜索问题](media/spring-cloud-diagnose/search-detectors.png)

选择“配置服务器运行状况检查”、“配置服务器运行状况”或“配置服务器更新历史记录”会显示不同结果。

![问题选项](media/spring-cloud-diagnose/detectors-options.png)

找到并选中目标检测器即可执行。 执行检测器后，会显示诊断摘要。 你可以选择“查看完整报告“以查看诊断详细信息，也可以选择“显示磁贴菜单”按钮以返回到检测器列表。 

![诊断摘要](media/spring-cloud-diagnose/summary-diagnostics.png)

在“诊断详细信息”页中，可以使用右上角的控制器更改诊断时间范围。 若要查看更多指标或日志，请切换每个诊断。 指标和日志可能会有 15 分钟的延迟。

![诊断详细信息](media/spring-cloud-diagnose/diagnostics-details.png)

某些结果包含相关文档。

![相关详细信息](media/spring-cloud-diagnose/related-details.png)

## <a name="next-steps"></a>后续步骤

* [使用警报和操作组监视 Spring Cloud 资源](./tutorial-alerts-action-groups.md)
* [Azure Spring Cloud 服务的安全控制](./concept-security-controls.md)
