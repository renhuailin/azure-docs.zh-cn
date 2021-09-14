---
title: 面向公共多租户应用服务的可用性区域支持
description: 了解如何部署应用服务，使你的应用实现区域冗余。
author: seligj95
ms.topic: article
ms.date: 09/01/2021
ms.author: jordanselig
ms.custom: references_regions
ms.openlocfilehash: b9453783ac0d3e6ecfd3785a134c31393fc15d4a
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2021
ms.locfileid: "123479465"
---
# <a name="availability-zone-support-for-public-multi-tenant-app-service"></a>面向公共多租户应用服务的可用性区域支持

Microsoft Azure 应用服务可部署到[可用性区域 (AZ)](../availability-zones/az-overview.md) 中，从而实现应用的[高可用性](https://en.wikipedia.org/wiki/High_availability)。 此体系结构也称为区域冗余。

应用驻留在应用服务计划 (ASP) 中，而应用服务计划存在于单个缩放单元中。 如果将应用服务配置为区域冗余，平台会自动将应用服务计划中的 VM 实例分散到所选地区中的所有三个区域。 如果指定的容量大于 3，并且实例数可被 3 整除，则实例将均匀分布。 否则，超过 3*N 的实例计数将分布在剩余的一两个区域中。

## <a name="requirements"></a>要求

区域冗余是应用服务计划的一个属性。 以下是目前实现区域冗余的要求/限制：

- 同时支持 Windows 和 Linux
- 需要高级 v2 或高级 v3 应用服务计划 
- 最小实例计数为 3
  - 如果指定的实例计数小于 3，平台将在后台强制执行此最小计数
- 可在以下任何地区启用：
  - 美国西部 2
  - 美国西部 3
  - 美国中部
  - 美国东部
  - 美国东部 2
  - 加拿大中部
  - 巴西南部
  - 北欧
  - 西欧
  - 德国中西部
  - 法国中部
  - 英国南部
  - Japan East
  - Southeast Asia
  - 澳大利亚东部
- 只能在创建新的应用服务计划时指定区域冗余
  - 当前，无法转换预先存在的应用服务计划。 请查看下一要点部分，详细了解如何新建支持区域冗余的应用服务计划。
- 仅在应用服务占用空间较新的部分支持区域冗余
  - 目前，如果你在 Pv3 上运行，则可能已在使用支持区域冗余的内存。 在这种情况下，你可创建新的应用服务计划，并在创建该计划时指定区域冗余。
  - 如果未使用 Pv3 或支持区域冗余的缩放单元、位于不受支持的地区或者不确定，请执行以下步骤：
    - 在受支持的地区创建新的资源组
        - 这确保了应用服务控制平面可在所选地区找到支持区域冗余的缩放单元
    - 使用新的资源组在所选地区创建新的应用服务计划（和应用）
    - 创建新的应用服务计划时，请确保 zoneRedundant 属性（如下所述）设置为 true
- 必须使用 [Azure 资源管理器 (ARM) 模板](../azure-resource-manager/templates/overview.md)进行创建

在区域关闭时，应用服务平台将检测丢失的实例并自动尝试查找新的替换实例。 如果还配置了自动缩放，则在它确定需要更多实例时，自动缩放还会向应用服务发出请求来添加更多实例（自动缩放行为与应用服务平台行为无关）。 务必要注意的是，不能保证在区域关闭的情况下需要更多实例的请求会成功，因为系统会尽力回填丢失的实例。 建议的解决方案是预配应用服务计划来解决丢失区域的问题，如本文下一部分所述。

即使同一地区的其他区域发生服务中断，在启用了区域冗余的应用服务计划中部署的应用程序也将继续运行并提供流量。 但是，非运行时行为（包括应用服务计划缩放、应用程序创建、应用程序配置和应用程序发布）可能仍然会受其他可用性区域中的中断的影响。 应用服务计划的区域冗余仅确保已部署应用程序的持续正常运行时间。

应用服务平台将实例分配给区域冗余应用服务计划时，它使用[由基础 Azure 虚拟机规模集提供的最大程度区域均衡](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md#zone-balancing)。 如果每个区域具有相同数量的 VM，或者与在应用服务计划使用的所有其他区域中的 VM 数相差 1，则应用服务计划“已实现均衡”。

## <a name="how-to-deploy-a-zone-redundant-app-service"></a>如何部署区域冗余应用服务

目前，需要使用 ARM 模板来创建区域冗余应用服务。 通过 ARM 模板创建后，可通过 Azure 门户和 CLI 工具查看应用服务计划并与之进行交互。 仅在首次创建应用服务计划时才需要 ARM 模板。

若要指定区域冗余应用服务，只需在 ARM 模板中添加新的 zoneRedundant 属性（必需），并选择性地更改 [Microsoft.Web/serverfarms](https://docs.microsoft.com/azure/templates/microsoft.web/serverfarms?tabs=json) 资源上的应用服务计划实例计数（容量）。 如果未指定容量，则平台默认为 3。 zoneRedundant 属性应设置为 true，容量应根据工作负载要求设置，但不应小于 3 。 选择容量时，一个好的做法是确保应用程序有足够的实例，这样即使失去某个实例区域，也剩有足够的容量来处理预期负载。

> [!TIP]
> 若要确定实例容量，可使用以下计算：
>
> 由于平台将 VM 分散到 3 个区域，并且你至少需要考虑 1 个区域的故障，因此将峰值工作负载实例计数乘以“区域/(区域-1)”或 3/2。 例如，如果你的典型峰值工作负载需要 4 个实例，则应预配 6 个实例：（2/3 * 6 实例）= 4 个实例。
>

下面的 ARM 模板片段显示了新的 zoneRedundant 属性和容量规范。

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "your-appserviceplan-name-here",
    "location": "West US 3",
    "sku": {
        "name": "P1v3",
        "tier": "PremiumV3",
        "size": "P1v3",
        "family": "Pv3",
        "capacity": 3
    },
    "kind": "app",
    "properties": {
        "zoneRedundant": true
    }
  }
]
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解如何创建和部署 ARM 模板](../azure-resource-manager/templates/quickstart-create-templates-use-visual-studio-code.md)

> [!div class="nextstepaction"]
> [ARM 快速入门模板](https://azure.microsoft.com/resources/templates/)
