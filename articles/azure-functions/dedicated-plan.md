---
title: Azure Functions 专用托管
description: 了解在专用应用服务托管计划上运行 Azure Functions 的好处。
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: 0ebf83aa919d91f161b247539ae20873242a8ed8
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937563"
---
# <a name="dedicated-hosting-plans-for-azure-functions"></a>Azure Functions 的专用托管计划

本文介绍如何在应用服务计划中托管函数应用，其中包括 (ASE) 应用服务环境。 有关其他托管选项，请参阅 [宿主计划一文](functions-scale.md)。

应用服务计划为要运行的应用定义一组计算资源。 这些计算资源类似于传统托管中的 [_服务器场_](https://wikipedia.org/wiki/Server_farm) 。 可以将一个或多个函数应用配置为在相同的计算资源上运行 (应用服务计划) 为其他应用服务应用，例如 web apps。 这些计划包括基本、标准、高级和独立的 Sku。 如需详细了解如何使用应用服务计划，请参阅 [Azure 应用服务计划深入概述](../app-service/overview-hosting-plans.md)。

对于以下情况，可以考虑使用应用服务计划：

* 具有已运行其他应用服务实例的、未充分利用的现成 VM。
* 需要提供用于运行函数的自定义映像。

## <a name="billing"></a>计费

在应用服务计划中，你需要为其他应用服务资源付费。 这不同于 Azure Functions [消耗计划](consumption-plan.md) 或 [高级计划](functions-premium-plan.md) 托管，后者具有基于消耗的成本组件。 无论在计划中运行多少个 function apps 或 web apps，都只需为该计划付费。 若要了解详细信息，请参阅 [应用服务定价页](https://azure.microsoft.com/pricing/details/app-service/windows/)。 

## <a name="always-on"></a><a name="always-on"></a> Always On

如果在应用服务计划上运行，应启用 AlwaysOn 设置，使函数应用能正常运行。 在应用服务计划中，如果函数运行时处于不活动状态，几分钟后就会进入空闲状态，因此只有 HTTP 触发器才能“唤醒”函数。 **Always on** 设置仅适用于应用服务计划。 在消耗计划中，平台会自动激活函数应用。

即使启用了 AlwaysOn，各函数的执行超时也由 [host.json](functions-host-json.md#functiontimeout) 项目文件中的 `functionTimeout` 设置控制。

## <a name="scaling"></a>缩放

借助应用服务计划，可通过添加更多 VM 实例手动进行横向扩展。 你也可以启用自动缩放，但自动缩放将慢于高级计划的弹性缩放。 有关详细信息，请参阅[手动或自动缩放实例计数](../azure-monitor/platform/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json)。 还可以通过选择不同的应用服务计划来进行增加。 有关详细信息，请参阅[增加 Azure 中的应用](../app-service/manage-scale-up.md)。 

> [!NOTE] 
> 在应用服务计划上运行 JavaScript ( # A0) 函数时，应选择个 vcpu 较少的计划。 有关详细信息，请参阅[选择单核应用服务计划](functions-reference-node.md#choose-single-vcpu-app-service-plans)。 
<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 

## <a name="app-service-environments"></a>应用服务环境

在 [应用服务环境](../app-service/environment/intro.md) (ASE) ，可以完全隔离函数并利用比应用服务计划更多的实例。 若要开始，请参阅。

如果只是想要在虚拟网络中运行函数应用，则可以使用 [高级计划](functions-premium-plan.md)执行此操作。 若要了解详细信息，请参阅 [建立 Azure Functions 专用站点访问](functions-create-private-site-access.md)。 

## <a name="next-steps"></a>后续步骤

+ [Azure Functions 宿主选项](functions-scale.md)
+ [Azure 应用服务计划概述](../app-service/overview-hosting-plans.md)