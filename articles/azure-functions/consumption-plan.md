---
title: Azure Functions 消耗计划托管
description: 了解 Azure Functions 消耗计划托管如何支持在动态缩放的环境中运行代码，但你只需为执行期间使用的资源付费。
ms.date: 8/31/2020
ms.topic: conceptual
ms.openlocfilehash: d292a70a8dfaa4cebdb99f2bcb5420c8b8ab9cd8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "98760548"
---
# <a name="azure-functions-consumption-plan-hosting"></a>Azure Functions 消耗计划托管

使用消耗计划时，会根据传入事件数自动添加和删除 Azure Functions 主机实例。 消耗计划是 Azure Functions 的完全<em>无服务器</em>托管选项。

## <a name="benefits"></a>优点

消耗计划会自动缩放，即使在高负载时段也是如此。 如果在消耗计划中运行函数，仅当函数运行时，计算资源才会产生费用。 在消耗计划中，函数执行在可配置的时间段后超时。

有关消耗计划与其他计划和托管类型的比较，请参阅[函数缩放和托管选项](functions-scale.md)。

## <a name="billing"></a>计费

账单将基于执行数量、执行时间和所用内存。 使用情况将基于函数应用内的所有函数聚合生成。 有关详细信息，请参阅 [Azure Functions 定价页](https://azure.microsoft.com/pricing/details/functions/)。

若要详细了解在消耗计划中运行时如何估算成本，请参阅[了解消耗计划成本](functions-consumption-costs.md)。

## <a name="create-a-consumption-plan-function-app"></a>创建消耗计划函数应用

在 Azure 门户中创建函数应用时，消耗计划为默认设置。 使用 API 创建函数应用时，不必像使用高级计划和专用计划那样首先创建应用服务计划。

使用以下链接了解如何在消耗计划中以编程方式或通过 Azure 门户创建无服务器函数应用：

+ [Azure CLI](./scripts/functions-cli-create-serverless.md)
+ [Azure 门户](./functions-get-started.md)
+ [Azure Resource Manager 模板](functions-create-first-function-resource-manager.md)

从 [Visual Studio Code](./create-first-function-vs-code-csharp.md#publish-the-project-to-azure) 或 [Visual Studio](functions-create-your-first-function-visual-studio.md#publish-the-project-to-azure) 发布 Functions 项目时，还可以在消耗计划中创建函数应用。

## <a name="multiple-apps-in-the-same-plan"></a>同一计划中的多个应用

可将同一区域中的函数应用分配到同一个消耗计划。 在同一个消耗计划中运行多个应用不会产生负面影响。 将多个应用分配到同一个消耗计划不会影响每个应用的复原能力、可伸缩性或可靠性。

## <a name="next-steps"></a>后续步骤

+ [Azure Functions 主机选项](functions-scale.md)
+ [Azure Functions 中的事件驱动缩放](event-driven-scaling.md)
