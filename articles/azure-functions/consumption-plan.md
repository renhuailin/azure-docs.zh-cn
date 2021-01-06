---
title: Azure Functions 消耗计划托管
description: 了解 Azure 函数消耗计划托管如何使你能够在动态缩放的环境中运行代码，但只需为执行期间使用的资源付费。
ms.date: 8/31/2020
ms.topic: conceptual
ms.openlocfilehash: c0619def4687935cd9e403563966b35b84f13c7c
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937604"
---
# <a name="azure-functions-consumption-plan-hosting"></a>Azure Functions 消耗计划托管

使用消耗计划时，会根据传入事件数自动添加和删除 Azure Functions 主机实例。 消耗计划是 Azure Functions 的完全 <em>无服务器</em> 宿主选项。

## <a name="benefits"></a>优点

即使在高负载期间，消耗计划也会自动缩放。 在消耗计划中运行函数时，仅当函数正在运行时才为计算资源付费。 在消耗计划中，函数执行在可配置的时间段后超时。

有关消耗计划与其他计划和托管类型的比较，请参阅 [函数缩放和宿主选项](functions-scale.md)。

## <a name="billing"></a>计费

账单将基于执行数量、执行时间和所用内存。 使用情况将基于函数应用内的所有函数聚合生成。 有关详细信息，请参阅 [Azure Functions 定价页](https://azure.microsoft.com/pricing/details/functions/)。

若要详细了解在消耗计划中运行时如何估算成本，请参阅[了解消耗计划成本](functions-consumption-costs.md)。

## <a name="create-a-consumption-plan-function-app"></a>创建消耗计划函数应用

在 Azure 门户中创建函数应用时，消耗计划为默认值。 使用 Api 创建 function app 时，无需先创建应用服务计划，就像高级和专用计划一样。

使用以下链接来了解如何在消耗计划中创建无服务器函数应用，无论是通过编程方式还是在 Azure 门户中：

+ [Azure CLI](./scripts/functions-cli-create-serverless.md)
+ [Azure 门户](functions-create-first-azure-function.md)
+ [Azure Resource Manager 模板](functions-create-first-function-resource-manager.md)

你还可以在从 [Visual Studio Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure) 或 [Visual Studio](functions-create-your-first-function-visual-studio.md#publish-the-project-to-azure)发布函数项目时，在消耗计划中创建 function app。

## <a name="multiple-apps-in-the-same-plan"></a>同一计划中的多个应用

可将同一区域中的函数应用分配到同一个消耗计划。 在同一个消耗计划中运行多个应用不会产生负面影响。 将多个应用分配到同一个消耗计划不会影响每个应用的复原能力、可伸缩性或可靠性。

## <a name="next-steps"></a>后续步骤

+ [Azure Functions 宿主选项](functions-scale.md)
+ [Azure Functions 中的事件驱动的缩放](event-driven-scaling.md)
