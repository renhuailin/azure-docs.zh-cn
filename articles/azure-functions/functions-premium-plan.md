---
title: Azure Functions 高级计划
description: Azure Functions 高级计划的详细信息和配置选项（VNet、无冷启动、无限制执行持续时间）。
author: cachai2
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: cachai
ms.custom:
- references_regions
- fasttrack-edit
- devx-track-azurecli
ms.openlocfilehash: f2ac7aa7d5078359f747e141971eabeba21eab3e
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2021
ms.locfileid: "122968726"
---
# <a name="azure-functions-premium-plan"></a>Azure Functions 高级计划

Azure Functions 弹性高级计划是函数应用的一个动态缩放托管选项。 有关其他托管计划选项，请参阅[托管计划文章](functions-scale.md)。

>[!IMPORTANT]
>Azure Functions 在 Azure 应用服务平台上运行。 在应用服务平台中，托管高级计划函数应用的计划称为弹性高级计划，其 SKU 名称类似于 `EP1`。 如果选择在高级计划上运行函数应用，请确保创建一个 SKU 名称以“E”开头的计划，例如 `EP1`。 以“P”开头的应用服务计划 SKU 名称，例如 `P1V2`（高级 V2 小型计划），实际上是[专用托管计划](dedicated-plan.md)。 由于它们是专用计划而不是弹性高级计划，因此 SKU 名称以“P”开头的计划不会动态缩放，可能会增加你的成本。 

高级计划托管为函数提供了以下好处：

* 使用永久温实例避免冷启动
* 虚拟网络连接。
* 无限制的执行持续时间（保证 60 分钟）。
* 高级实例大小：单核心、双核心和四核心实例。
* 与消耗计划相比，定价可预测性更高。
* 对于具有多个函数应用的计划，提供了高密度应用分配。

就像[消耗计划](consumption-plan.md)一样，使用高级计划时，会根据传入事件数添加和删除 Azure Functions 主机实例。 可以将多个函数应用部署到同一高级计划，该计划允许配置计算实例大小、基本计划大小和最大计划大小。 

## <a name="billing"></a>计费

高级计划的计费基于跨实例分配的核心秒数和内存。 此计费不同于消耗计划，后者按执行和消耗的内存计费。 高级计划不收取执行费用。 每个计划必须始终至少分配一个实例。 使用此计费方式，无论函数是活动的还是空闲的，每个活动计划的月成本都是最低的。 请记住，高级计划中的所有函数应用会共享已分配的实例。 若要了解详细信息，请参阅 [Azure Functions 定价页](https://azure.microsoft.com/pricing/details/functions/)。

## <a name="create-a-premium-plan"></a>创建高级计划

在 Azure 门户中创建函数应用时，消耗计划为默认值。 若要创建在高级计划中运行的函数应用，必须使用弹性高级 SKU 之一显式创建应用服务计划。 然后，在此计划中托管你创建的函数应用。 可以使用 Azure 门户轻松地同时创建高级计划和函数应用。 可以在同一高级计划中运行多个函数应用，但它们必须在相同的操作系统（Windows 或 Linux）上运行。 

以下文章说明了如何通过编程方式或 Azure 门户创建采用高级计划的函数应用：

+ [Azure 门户](create-premium-plan-function-app-portal.md)
+ [Azure CLI](scripts/functions-cli-create-premium-plan.md)
+ [Azure Resource Manager 模板](functions-infrastructure-as-code.md#deploy-on-premium-plan)

## <a name="eliminate-cold-starts"></a>消除冷启动

当消耗计划中没有发生任何事件或执行任何操作时，你的应用可以缩减到零个实例。 当出现新事件时，必须使运行应用的新实例专用化。 将新实例专用化可能需要一些时间，具体取决于应用。 第一次调用时的这种额外延迟通常称为应用冷启动。

高级计划提供了两个功能，这些功能协同工作以有效地消除函数中的冷启动：“始终就绪实例”和“预热实例”。 

### <a name="always-ready-instances"></a>始终就绪实例

在高级计划中，你可以让应用在指定数量的实例上始终处于就绪状态。 始终就绪实例的最大数目为 20。 当事件开始触发应用时，它们首先路由到始终就绪的实例。 当函数变为活动状态时，其他实例将作为缓冲区进行预热。 此缓冲区可防止缩放过程中所需的新实例的冷启动。 这些缓冲的实例称为[预热实例](#pre-warmed-instances)。 组合使用始终就绪实例和预热缓冲区，你的应用可以有效地消除冷启动。

> [!NOTE]
> 每个高级计划在任何时候都至少有一个活动（计费）的实例。

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

预热实例是在缩放和激活事件期间作为缓冲区预热的实例。 预热实例将持续缓冲，直至达到最大横向扩展限制。 默认的预热实例数为 1，在大多数情况下此值应保留为 1。

如果应用长时间预热（如自定义容器映像），则可能需要增大此缓冲区。 只有充分利用了所有活动实例后，预热实例才会变为活动状态。

请考虑此示例，了解始终就绪实例和预热实例如何协同工作。 高级函数应用配置了五个始终就绪实例和一个默认的预热实例。 当应用处于空闲状态且未触发任何事件时，应用将通过五个实例进行预配并运行。 此时，无需为预热实例付费，因为未使用始终就绪实例，并且没有分配预热实例。

一旦第一个触发器进入，五个始终就绪实例将变为活动状态，并分配预热实例。 应用现在运行时有六个预配的实例：五个现在处于活动状态的始终就绪实例，以及第六个预热的处于非活动状态的缓冲区。 如果执行速率持续增大，则最终会使用五个活动实例。 当平台决定扩展到五个以上实例时，它将扩展到预热实例。 如果发生这种情况，则现在会有六个活动实例，系统会立即预配第七个实例并填充预热缓冲区。 此缩放和预热序列将继续，直至达到应用的最大实例计数。 不会预热或激活超出最大值的实例。

可以使用 Azure CLI 为应用修改预热实例数。

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.preWarmedInstanceCount=<desired_prewarmed_count> --resource-type Microsoft.Web/sites
```

### <a name="maximum-function-app-instances"></a>最大函数应用实例数

除了[计划最大实例计数](#plan-and-sku-settings)之外，还可以配置每个应用的最大值。 可以使用[应用规模限制](./event-driven-scaling.md#limit-scale-out)配置应用最大值。

## <a name="private-network-connectivity"></a>专用网络连接

部署到高级计划的函数应用可以利用[针对 Web 应用的 VNet 集成](../app-service/web-sites-integrate-with-vnet.md)。 配置后，你的应用可以与 VNet 中的资源通信，或通过服务终结点获得保护。 应用上也提供 IP 限制，以限制传入流量。

在高级计划中向函数应用分配子网时，你需要有一个具有足够 IP 地址的子网，用于每个潜在的实例。 我们需要一个至少包含 100 个可用地址的 IP 块。

有关详细信息，请参阅[将函数应用与 VNet 集成](functions-create-vnet.md)。

## <a name="rapid-elastic-scale"></a>快速弹性缩放

使用与消耗计划相同的快速缩放逻辑，为你的应用自动添加其他计算实例。 同一应用服务计划中的应用会基于单个应用的需求彼此独立地进行缩放。 但是，如果可能，同一应用服务计划中的 Functions 应用会共享 VM 资源，以便降低成本。 与某个 VM 关联的应用数取决于每个应用的资源占用情况和 VM 大小。

若要详细了解缩放工作原理，请参阅 [Azure Functions 中事件驱动的缩放](event-driven-scaling.md)。

## <a name="longer-run-duration"></a>更长的运行持续时间

消耗计划中的 Azure Functions 限制为单次执行 10 分钟。 在高级计划中，运行持续时间默认为 30 分钟，以防止执行失控。 不过，你可以[修改 host.json 配置](./functions-host-json.md#functiontimeout)，使高级计划应用的持续时间无限长。 如果设置为一个无限的持续时间，则可以保证函数应用至少运行 60 分钟。 

## <a name="plan-and-sku-settings"></a>计划和 SKU 设置

创建计划时，有两个计划大小设置：实例的最小数目（或计划大小）和最大突发限制。

如果应用需要的实例数超出了始终就绪实例数，则它可以继续横向扩展，直到实例数达到最大突发限制。 仅当实例正在运行并分配给你时，你才需要为超出计划大小的实例付费（按秒计）。 平台会尽最大努力将你的应用横向扩展到所定义的最大限制。

在 Azure 门户中，可以通过在计划中或在部署到该计划的函数应用中选择“横向扩展”选项（在“平台功能”下），配置计划大小和最大值。

你还可以通过 Azure CLI 提高最大突发限制：

```azurecli-interactive
az functionapp plan update -g <resource_group> -n <premium_plan_name> --max-burst <desired_max_burst>
```

每个计划的最小值至少为一个实例。 系统会根据计划中的应用请求的始终就绪实例自动为你配置实际的最小实例数。 例如，如果应用 A 请求 5 个始终就绪实例，应用 B 在同一计划中请求 2 个始终就绪实例，则最小计划大小将计算为 5。 应用 A 将在所有 5 个实例上运行，应用 B 将仅在 2 个实例上运行。

> [!IMPORTANT]
> 对于在最小实例计数中分配的每个实例，无论函数是否正在执行，系统都会向你收费。

在大多数情况下，此自动计算的最小值可以满足需要。 但是，系统会尽力进行超出最小值的缩放。 如果其他实例不可用，则在特定的时间，横向扩展可能会延迟，虽然这种情况不太可能发生。 通过将最小值设置为高于自动算出的最小值，可以在横向扩展之前预留实例。

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

在具有更多内存的计算机上运行并不一定意味着函数应用会使用所有可用内存。

例如，JavaScript 函数应用受 Node.js 中的默认内存限制约束。 若要增大此固定内存限制，请添加值为 `--max-old-space-size=<max memory in MB>` 的应用设置 `languageWorkers:node:arguments`。

对于内存超过 4GB 的计划，请确保“位数”平台设置在[常规设置](../app-service/configure-common.md#configure-general-settings)下设置为 `64 Bit`。

## <a name="region-max-scale-out"></a>区域最大横向扩展

下面是每个区域和 OS 配置中单个计划当前支持的最大横向扩展值。

请在 [Azure 网站](https://azure.microsoft.com/global-infrastructure/services/?products=functions)上查看 Functions 的完整的区域可用性。

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
