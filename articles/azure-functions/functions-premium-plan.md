---
title: Azure Functions 高级计划
description: Azure Functions 高级计划的详细信息和配置选项（VNet、无冷启动、无限制执行持续时间）。
author: jeffhollan
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: jehollan
ms.custom:
- references_regions
- fasttrack-edit
- devx-track-azurecli
ms.openlocfilehash: d944512e5f6126920ab4fba99fb70513b93177ba
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/06/2021
ms.locfileid: "97936815"
---
# <a name="azure-functions-premium-plan"></a>Azure Functions 高级计划

Azure Functions 高级计划 (有时称为弹性高级计划) 是函数应用的托管选项。 有关其他宿主计划选项，请参阅 [宿主计划一文](functions-scale.md)。

高级计划宿主为函数提供以下好处：

* 避免冷启动永久温实例
* 虚拟网络连接。
* 无限制的执行持续时间，保证为60分钟。
* 高级实例大小：一个核心、两核和四个核心实例。
* 与消耗计划相比，可预测性更高。
* 针对具有多个 function app 的计划的高密度应用分配。

使用高级计划时，会根据传入事件的数量添加并删除 Azure Functions 主机的实例，就像 [消耗计划](consumption-plan.md)一样。 可以将多个函数应用部署到同一高级计划，该计划允许配置计算实例大小、基本计划大小和最大计划大小。 

## <a name="billing"></a>计费

高级计划的计费取决于核心秒数和实例间分配的内存量。 此计费不同于消耗计划，每次执行和内存都按计费。 高级计划不收取执行费用。 每个计划必须始终至少分配一个实例。 此计费每个活动计划的每月成本最低，而不考虑函数是处于活动状态还是处于空闲状态。 请记住，高级计划中的所有函数应用会共享已分配的实例。 若要了解详细信息，请参阅 [Azure Functions 定价页](https://azure.microsoft.com/pricing/details/functions/)。

## <a name="create-a-premium-plan"></a>创建高级计划

在 Azure 门户中创建函数应用时，消耗计划为默认值。 若要创建在高级计划中运行的函数应用，必须使用一个 _弹性高级_ sku 显式创建应用服务计划。 然后，在此计划中托管创建的函数应用。 Azure 门户可以轻松地同时创建高级计划和函数应用。 您可以在同一高级计划中运行多个 function app，但它们在同一操作系统 (Windows 或 Linux) 上运行。 

以下文章说明了如何以编程方式或在 Azure 门户中创建具有高级计划的函数应用：

+ [Azure 门户](create-premium-plan-function-app-portal.md)
+ [Azure CLI](scripts/functions-cli-create-premium-plan.md)
+ [Azure Resource Manager 模板](functions-infrastructure-as-code.md#deploy-on-premium-plan)

## <a name="eliminate-cold-starts"></a>消除冷启动

当消耗计划中不发生事件或执行时，应用可能会扩展到零实例。 当出现新事件时，在其上运行应用的新实例必须是专用的。 将新实例专用化可能需要一些时间，具体取决于应用。 第一次调用的额外延迟通常称为应用 _冷启动_。

高级计划提供了两个功能，这些功能协同工作以有效地消除函数中的冷启动： _始终可用实例_ 和 _预准备好实例_。 

### <a name="always-ready-instances"></a>始终就绪实例

在高级计划中，你可以让应用在指定数量的实例上始终处于就绪状态。 始终就绪实例的最大数目为 20。 事件开始触发应用时，将首先路由到始终可用的实例。 当函数变为活动状态时，其他实例将作为缓冲区进行预热。 此缓冲区可防止缩放过程中所需的新实例的冷启动。 这些缓冲的实例称为[预热实例](#pre-warmed-instances)。 组合使用始终就绪实例和预热缓冲区，你的应用可以有效地消除冷启动。

> [!NOTE]
> 每个高级计划至少具有一个活动 (计费) 实例。

# <a name="portal"></a>[Portal](#tab/portal)

你可以通过以下方式在 Azure 门户中配置始终就绪实例的数目：选择你的 **函数应用**，转到“平台功能”选项卡，然后选择“横向扩展”选项。 在函数应用编辑窗口中，始终就绪实例是该应用特有的。

![弹性缩放设置](./media/functions-premium-plan/scale-out.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

你还可以使用 Azure CLI 为应用配置始终就绪实例。

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.minimumElasticInstanceCount=<desired_always_ready_count> --resource-type Microsoft.Web/sites
```
---

### <a name="pre-warmed-instances"></a>预热实例

准备好实例是在缩放和激活事件期间作为缓冲区准备好的实例。 预热实例将持续缓冲，直至达到最大横向扩展限制。 默认的预准备好实例计数为1，在大多数情况下，此值应保留为1。

如果某个应用具有较长的预热 (（如) 自定义容器映像），则可能需要增加此缓冲区。 只有在使用完所有活动的实例后，准备好实例才会变为活动状态。

请考虑以下示例，该示例演示了始终可用实例和预准备好实例如何协同工作。 高级函数应用配置了五个始终就绪实例和一个默认的预热实例。 当应用处于空闲状态且未触发任何事件时，应用将预配并运行五个实例。 目前，你不会对预准备好实例计费，因为未使用始终可用的实例，并且未分配准备好实例。

第一次触发器进入后，五个随时可用的实例会变为活动状态，并且会分配准备好实例。 应用现在运行时有六个预配的实例：五个现在处于活动状态的始终就绪实例，以及第六个预热的处于非活动状态的缓冲区。 如果执行速率持续增加，最终将使用五个活动实例。 当平台决定扩展到超过五个实例时，它将扩展到准备好实例。 发生这种情况时，现在有六个活动实例，并会立即预配第七个实例并填充预准备好缓冲区。 此缩放顺序和预先预热将继续执行，直到达到应用的最大实例计数。 任何实例都不会提前准备好或激活，超过最大值。

可以使用 Azure CLI 为应用修改预热实例数。

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.preWarmedInstanceCount=<desired_prewarmed_count> --resource-type Microsoft.Web/sites
```

### <a name="maximum-function-app-instances"></a>最大函数应用实例

除了[计划最大实例计数](#plan-and-sku-settings)之外，还可以配置每个应用的最大值。 可以使用[应用规模限制](./event-driven-scaling.md#limit-scale-out)配置应用最大值。

## <a name="private-network-connectivity"></a>专用网络连接

部署到高级计划的函数应用可以利用 [web 应用的 VNet 集成](../app-service/web-sites-integrate-with-vnet.md)。 配置后，你的应用可以与 VNet 中的资源通信，或通过服务终结点获得保护。 应用上也提供 IP 限制，以限制传入流量。

在高级计划中向函数应用分配子网时，你需要有一个具有足够 IP 地址的子网，用于每个潜在的实例。 我们需要一个至少包含 100 个可用地址的 IP 块。

有关详细信息，请参阅 [将 function app 与 VNet 集成](functions-create-vnet.md)。

## <a name="rapid-elastic-scale"></a>快速弹性缩放

使用与消耗计划相同的快速缩放逻辑，为你的应用自动添加其他计算实例。 同一应用服务计划中的应用会基于单个应用的需求彼此独立地进行缩放。 但是，如果可能，同一应用服务计划中的 Functions 应用会共享 VM 资源，以便降低成本。 与某个 VM 关联的应用数取决于每个应用的资源占用情况和 VM 大小。

若要详细了解缩放的工作方式，请参阅 [Azure Functions 中事件驱动的缩放](event-driven-scaling.md)。

## <a name="longer-run-duration"></a>更长的运行持续时间

消耗计划中的 Azure Functions 限制为单次执行 10 分钟。 在高级计划中，运行持续时间默认为 30 分钟，以防止执行失控。 但是，可以 [修改配置的 host.js](./functions-host-json.md#functiontimeout) ，使高级计划应用的持续时间无限制。 如果设置为 "未限定的持续时间"，则可以保证函数应用运行至少60分钟。 

## <a name="plan-and-sku-settings"></a>计划和 SKU 设置

创建计划时，有两个计划大小设置：实例的最小数目（或计划大小）和最大突发限制。

如果你的应用程序需要的实例超出了始终可用的实例，它可以继续横向扩展，直到实例数达到最大猝发限制。 仅在每秒按计划大小运行并分配给你时，才会向你收费。 在将应用程序扩展到已定义的最大限制时，该平台可实现最大努力。

在 Azure 门户中，可以通过在计划中或在部署到该计划的函数应用中选择“横向扩展”选项（在“平台功能”下），配置计划大小和最大值。

你还可以通过 Azure CLI 提高最大突发限制：

```azurecli-interactive
az functionapp plan update -g <resource_group> -n <premium_plan_name> --max-burst <desired_max_burst>
```

每个计划的最小值至少为一个实例。 系统会根据计划中的应用请求的始终就绪实例自动为你配置实际的最小实例数。 例如，如果应用 A 请求 5 个始终就绪实例，应用 B 在同一计划中请求 2 个始终就绪实例，则最小计划大小将计算为 5。 应用 A 将在所有 5 个实例上运行，应用 B 将仅在 2 个实例上运行。

> [!IMPORTANT]
> 对于在最小实例计数中分配的每个实例，无论函数是否正在执行，系统都会向你收费。

在大多数情况下，此 autocalculated 的最小值就够了。 但是，系统会尽力进行超出最小值的缩放。 虽然不太可能，但如果其他实例不可用，则可能会延迟特定的时间扩展。 通过将最小值设置为高于自动算出的最小值，可以在横向扩展之前预留实例。

可以使用 Azure CLI 为计划增大算出的最小值。

```azurecli-interactive
az functionapp plan update -g <resource_group> -n <premium_plan_name> --min-instances <desired_min_instances>
```

### <a name="available-instance-skus"></a>可用的实例 SKU

创建或缩放计划时，可以在三个实例大小之间进行选择。 你将按分配给你的每个实例的内核总数以及每秒的预配内存付费。 你的应用可以根据需要自动横向扩展到多个实例。

|SKU|核心数|内存|存储|
|--|--|--|--|
|EP1|1|3.5GB|250GB|
|EP2|2|7GB|250GB|
|EP3|4|14GB|250GB|

### <a name="memory-usage-considerations"></a>内存使用情况注意事项

在具有更多内存的计算机上运行并不一定意味着函数应用使用所有可用内存。

例如，JavaScript 函数应用受 Node.js 中的默认内存限制约束。 若要增大此固定内存限制，请添加值为 `--max-old-space-size=<max memory in MB>` 的应用设置 `languageWorkers:node:arguments`。

## <a name="region-max-scale-out"></a>区域最大横向扩展

下面是每个区域和 OS 配置中单个计划当前支持的最大横向扩展值。 若要请求增加，可以打开支持票证。

请参阅 [Azure](https://azure.microsoft.com/global-infrastructure/services/?products=functions)网站上的功能的完整区域可用性。

|区域| Windows | Linux |
|--| -- | -- |
|澳大利亚中部| 100 | 不可用 |
|澳大利亚中部 2| 100 | 不可用 |
|澳大利亚东部| 100 | 20 |
|Australia Southeast | 100 | 20 |
|Brazil South| 100 | 20 |
|加拿大中部| 100 | 20 |
|美国中部| 100 | 20 |
|中国东部 2| 100 | 20 |
|中国北部 2| 100 | 20 |
|东亚| 100 | 20 |
|美国东部 | 100 | 20 |
|美国东部 2| 100 | 20 |
|法国中部| 100 | 20 |
|德国中西部| 100 | 不可用 |
|Japan East| 100 | 20 |
|日本西部| 100 | 20 |
|韩国中部| 100 | 20 |
|韩国南部| 不可用 | 20 |
|美国中北部| 100 | 20 |
|北欧| 100 | 20 |
|挪威东部| 100 | 20 |
|美国中南部| 100 | 20 |
|印度南部 | 100 | 不可用 |
|Southeast Asia| 100 | 20 |
|瑞士北部| 100 | 不可用 |
|瑞士西部| 100 | 不可用 |
|英国南部| 100 | 20 |
|英国西部| 100 | 20 |
|USGov Arizona| 100 | 20 |
|USGov Virginia| 100 | 20 |
|USNat 东部| 100 | 不可用 |
|USNat 西部| 100 | 不可用 |
|西欧| 100 | 20 |
|印度西部| 100 | 20 |
|美国中西部| 100 | 20 |
|美国西部| 100 | 20 |
|美国西部 2| 100 | 20 |

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解 Azure Functions 托管选项](functions-scale.md)
