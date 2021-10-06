---
title: Azure Functions 的缩放和托管
description: 了解如何在 Azure Functions 消耗计划和高级计划之间进行选择。
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.topic: conceptual
ms.date: 08/17/2020
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a2c760e4b1ca923496d11002f2e30b72672f32df
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124778393"
---
# <a name="azure-functions-hosting-options"></a>Azure Functions 托管选项

在 Azure 中创建函数应用时，必须为应用选择托管计划。 有三个基本托管计划可用于 Azure Functions：[消耗计划](consumption-plan.md)、[高级计划](functions-premium-plan.md)和[专用（应用服务）计划](dedicated-plan.md)。 Linux 和 Windows 虚拟机上的所有托管计划均已正式发布 (GA)。

选择的托管计划决定了以下行为：

* 函数应用的缩放方式。
* 每个函数应用实例可用的资源。
* 对 Azure 虚拟网络连接等高级功能的支持。

本文详细比较了各种托管计划和基于 [Kubernetes 的托管](functions-kubernetes-keda.md)。

> [!NOTE]
> 如果选择在 Kubernetes 群集中托管函数，请考虑使用[已启用 Azure Arc 的 Kubernetes 群集](../azure-arc/kubernetes/overview.md)。 预览版中现已包括在已启用 Azure Arc 的 Kubernetes 群集上的托管。 若要了解详细信息，请参阅 [Azure Arc 上的应用服务、函数和逻辑应用](../app-service/overview-arc-integration.md)。  

## <a name="overview-of-plans"></a>计划概述

下面汇总了适用于 Functions 的三个主要托管计划的优点：

| 计划 | 优点 |
| --- | --- |  
|**[消耗计划](consumption-plan.md)**| 在函数运行时自动缩放，你只需为计算资源付费。<br/><br/>在消耗计划中，会根据传入事件数动态添加和删除 Functions 主机的实例。<br/><br/> ✔ 默认托管计划。<br/>✔ 仅当函数运行时才产生费用。<br/>✔ 即使是在负载较高期间也可自动缩放。|  
|**[高级计划](functions-premium-plan.md)**|使用预热的工作器根据需要自动缩放，这些工作器在空闲后会毫不延迟地运行应用程序，在更强大的实例上运行，并连接到虚拟网络。 <br/><br/>请考虑下列情况中的 Azure Functions 高级计划： <br/><br/>✔ 你的函数应用持续地运行，或者近乎持续地运行。<br/>✔ 你的消耗计划具有大量的小型执行操作，具有较高的执行费用但较低的 GB 秒数。<br/>✔ 你需要的 CPU 或内存选项超出消耗计划提供的选项。<br/>✔ 你的代码所需的运行时间超过消耗计划允许的最长执行时间。<br/>✔ 你需要使用消耗计划中未提供的功能，例如虚拟网络连接。<br/>✔ 需要提供用于运行函数的自定义 Linux 映像。 |  
|**[专用计划](dedicated-plan.md)** |在应用服务计划中以定期[应用服务计划费率](https://azure.microsoft.com/pricing/details/app-service/windows/)运行你的函数。<br/><br/>最适用于不能使用 [Durable Functions](durable/durable-functions-overview.md) 的长时间运行的方案。 对于以下情况，可以考虑使用应用服务计划：<br/><br/>✔ 具有已运行其他应用服务实例的、未充分利用的现成 VM。<br/>✔ 需要可预测的缩放和成本。|  

本文中的比较表还包括以下托管选项，这些选项提供了运行函数应用所需的最大限度的控制和隔离。  

| 托管选项 | 详细信息 |
| --- | --- |  
|**[ASE](dedicated-plan.md)** | 应用服务环境 (ASE) 是一项应用服务功能，可提供完全隔离和专用的环境，以便高度安全地运行应用服务应用。<br/><br/>ASE 适用于有以下要求的应用程序工作负荷： <br/><br/>✔ 极高的缩放性。<br/>✔完全计算隔离和安全的网络访问。<br/>✔ 内存使用率较高。|  
| **Kubernetes**<br/>（[直接](functions-kubernetes-keda.md)或<br/>[Azure Arc](../app-service/overview-arc-integration.md)） | Kubernetes 提供了一个在 Kubernetes 平台之上运行的完全隔离的专用环境。<br/><br/> Kubernetes 适用于有以下要求的应用程序工作负荷： <br/>✔ 自定义硬件要求。<br/>✔ 隔离和安全的网络访问。<br/>✔ 能够在混合或多云环境中运行。<br/>✔ 与现有的 Kubernetes 应用程序和服务一起运行。|  

本文中的其余表对有关各种功能和行为的计划进行了比较。 有关动态托管计划（“消耗”和“高级”）之间的成本比较，请参阅 [Azure Functions 定价页](https://azure.microsoft.com/pricing/details/functions/)。 有关各种专用计划选项的定价，请参阅[应用服务定价页](https://azure.microsoft.com/pricing/details/app-service/windows/)。 

## <a name="operating-systemruntime"></a>操作系统/运行时

下表显示了各个托管计划支持的操作系统及其语言运行时支持。

| | Linux<sup>1</sup><br/>仅限代码 | Windows<sup>2</sup><br/>仅限代码 | Linux<sup>1、3</sup><br/>Docker 容器 |
| --- | --- | --- | --- |
| **[消耗计划](consumption-plan.md)** | .NET Core<br/>Node.js<br/>Java<br/>Python | .NET Core<br/>Node.js<br/>Java<br/>PowerShell Core | 不支持  |
| **[高级计划](functions-premium-plan.md)** | .NET Core<br/>Node.js<br/>Java<br/>Python|.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python  | 
| **[专用计划](dedicated-plan.md)** | .NET Core<br/>Node.js<br/>Java<br/>Python|.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python |
| **[ASE](dedicated-plan.md)** | .NET Core<br/>Node.js<br/>Java<br/>Python |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core  |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python | 
| [Kubernetes（直接）](functions-kubernetes-keda.md) | 不适用 | 不适用 |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python |
| **[Azure Arc（预览版）](../app-service/overview-arc-integration.md)** | .NET Core<br/>Node.js<br/>Java<br/>Python | 不适用 |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python |

<sup>1</sup> 对于 Python 运行时堆栈，Linux 是唯一受支持的操作系统。 <br/>
<sup>2</sup> 对于 PowerShell 运行时堆栈，Windows 是唯一受支持的操作系统。<br/>
<sup>3</sup> 对于 Docker 容器，Linux 是唯一受支持的操作系统。<br/>

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]

## <a name="scale"></a>缩放

下表对各种托管计划的缩放行为进行了比较。

| 计划 | 向外扩展 | 最大实例数 |
| --- | --- | --- |
| **[消耗计划](consumption-plan.md)** | [事件驱动型](event-driven-scaling.md)。 即使是在负载较高期间也可自动扩展。 Azure Functions 基础结构可根据传入的触发器事件数添加额外的 Functions 主机实例，因此可以缩放 CPU 和内存资源。 | 200 |
| **[高级计划](functions-premium-plan.md)** | [事件驱动型](event-driven-scaling.md)。 即使是在负载较高期间也可自动扩展。 Azure Functions 基础结构可根据触发函数的事件数添加额外的 Functions 主机实例，因此可以缩放 CPU 和内存资源。 |100|
| **[专用计划](dedicated-plan.md)** <sup>1</sup> | 手动/自动缩放 |10-20|
| **[ASE](dedicated-plan.md)** <sup>1</sup> | 手动/自动缩放 |100 |
| **[Kubernetes](functions-kubernetes-keda.md)**  | 对使用 [KEDA](https://keda.sh) 的 Kubernetes 群集进行事件驱动型自动缩放。 | 因群集而异。&nbsp;&nbsp;&nbsp;&nbsp;|

<sup>1</sup> 有关各种应用服务计划选项的特定限制，请参阅[应用服务计划限制](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)。

## <a name="cold-start-behavior"></a>冷启动行为

| 计划 | 详细信息 | 
| -- | -- |
| **[消耗&nbsp;计划](consumption-plan.md)** | 当空闲时，应用数可能会缩减为零，这意味着某些请求在启动时可能会产生额外的延迟。  消耗计划有一些可缩短冷启动时间的优化措施，包括从已经运行函数主机和语言进程的预热占位符函数中进行拉取。 |
| **[高级计划](functions-premium-plan.md)** | 永久预热实例以避免任何冷启动。 |
| **[专用计划](dedicated-plan.md)** | 在专用计划中运行时，Functions 主机可以连续运行，这意味着冷启动实际上不是问题。 |
| **[ASE](dedicated-plan.md)** | 在专用计划中运行时，Functions 主机可以连续运行，这意味着冷启动实际上不是问题。 |
| **[Kubernetes](functions-kubernetes-keda.md)**  | 可以根据 KEDA 配置对应用进行配置以避免冷启动。 如果配置为缩放到零，则新事件会遇到冷启动。 

## <a name="service-limits"></a>服务限制

[!INCLUDE [functions-limits](../../includes/functions-limits.md)]

## <a name="networking-features"></a>网络功能

[!INCLUDE [functions-networking-features](../../includes/functions-networking-features.md)]

## <a name="billing"></a>计费

| 计划 | 详细信息 |
| --- | --- |
| **[消耗计划](consumption-plan.md)** | 只需为函数运行时间付费。 账单将基于执行数量、执行时间和所用内存。 |
| **[高级计划](functions-premium-plan.md)** | 高级计划基于在必需实例和预热实例中使用的核心秒数和内存。 每个计划必须至少有一个实例始终处于预热状态。 此计划提供了最可预测的定价。 |
| **[专用计划](dedicated-plan.md)** | 应用服务计划中函数应用的费用与其他应用服务资源（例如 Web 应用）的费用相同。|
| **[应用服务环境 (ASE)](dedicated-plan.md)** | ASE 每月会产生统一的基础结构使用费，该费率不会随 ASE 大小的变化而改变。 每个应用服务计划 vCPU 也会产生费用。 ASE 中托管的所有应用都在“隔离”定价 SKU 中。 |
| **[Kubernetes](functions-kubernetes-keda.md)**| 只需支付 Kubernetes 群集的费用；Functions 无额外费用。 函数应用作为应用程序工作负荷在群集之上运行，就像普通应用一样。 |

## <a name="next-steps"></a>后续步骤

+ [Azure Functions 中的部署技术](functions-deployment-technologies.md) 
+ [Azure Functions 开发人员指南](functions-reference.md)
