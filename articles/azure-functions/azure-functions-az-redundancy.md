---
title: Azure Functions 对弹性高级计划的可用性区域支持
description: 了解如何针对弹性高级计划的高可用性应用程序，将可用性区域冗余与 Azure Functions 一起使用。
ms.topic: conceptual
ms.author: johnguo
ms.date: 09/07/2021
ms.custom: references_regions
ms.openlocfilehash: 796f4db37429e73dd1c3d50dc71b0a599977d861
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699368"
---
# <a name="azure-functions-support-for-availability-zone-redundancy"></a>Azure Functions 对可用性区域冗余的支持

弹性高级计划和专用（应用服务）计划现在可以使用对 Azure Functions 的可用性区域 (AZ) 支持。 区域冗余 Azure Functions 应用程序将自动平衡可用性区域之间的实例，以实现更高的可用性。 本文档重点介绍区域冗余对弹性高级函数计划的支持。 有关专用计划的区域冗余，请参阅[此处](../app-service/how-to-zone-redundancy.md)。

## <a name="overview"></a>概述

[可用性区域](../availability-zones/az-overview.md#availability-zones)是一种高可用性产品/服务，在数据中心发生故障时可以保护应用程序和数据。 可用性区域是 Azure 区域中独特的物理位置。 每个区域由一个或多个数据中心组成，这些数据中心配置了独立电源、冷却和网络。 为确保能够进行复原，所有已启用的区域中必须至少有三个单独的区域。 通过将计算、存储、网络和数据资源共置在一个区域并将其复制到其他区域，可以在应用程序体系结构中内置高可用性。

区域冗余函数应用将自动在该区域的可用区域之间分配运行你的应用的实例的负载。 对于区域冗余弹性高级应用，即使应用程序进行缩减和扩展，应用运行的实例仍会在可用性区域之间均匀分布。

## <a name="requirements"></a>要求

> [!IMPORTANT]
> 为函数应用选择[存储帐户](storage-considerations.md#storage-account-requirements)时，请务必使用[区域冗余存储帐户 (ZRS)](../storage/common/storage-redundancy.md#zone-redundant-storage)。 否则，在发生区域性中断时，Functions 可能会因为其依赖于存储而表现出意外行为。 

- 同时支持 Windows 和 Linux。
- 必须承载于[弹性高级](functions-premium-plan.md)或专用托管计划中。 可在[此处](../app-service/how-to-zone-redundancy.md)找到有关含专用（应用服务）托管计划的区域冗余说明。
  - 可用性区域 (AZ) 支持目前不适用于[消耗](consumption-plan.md)计划中的函数应用。
- 区域冗余计划必须指定最小实例计数 3。
- 弹性高级计划上的函数应用还必须将[随时可用实例](functions-premium-plan.md#always-ready-instances)的最小数量设置为 3。
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
- 此时，必须通过 [ARM 模板](../azure-resource-manager/templates/index.yml)创建。

## <a name="how-to-deploy-a-function-app-on-a-zone-redundant-premium-plan"></a>如何在区域冗余高级计划中部署函数应用

首次创建区域冗余弹性高级函数应用时，需要通过 [ARM 模板](../azure-resource-manager/templates/quickstart-create-templates-use-visual-studio-code.md)来部署。 然后，在成功创建后，可以通过 Azure 门户和 CLI 工具查看函数计划并与之进行交互。 仅在首次创建函数计划时才需要 ARM 模板。 可在[此处](functions-infrastructure-as-code.md#deploy-on-premium-plan)找到有关在高级计划中承载函数的指南。 创建并部署区域冗余计划后，在新计划中托管的任何函数应用现在都具有区域冗余性。 

创建区域冗余函数计划时要注意的唯一属性是新的 zoneRedundant 属性和函数计划实例计数 (capacity) 字段。 zoneRedundant 属性必须设置为 true，并且应根据工作负载需求来设置 capacity 属性，但不能小于 3。   根据多个因素和高可用性/容错策略，选择适当的容量会有所不同。 一个好的做法是确保应用程序有足够的实例，这样即使失去某个实例区域，也剩有足够的容量来处理预期负载。

> [!IMPORTANT]
> 托管在弹性高级版上的 Azure 函数应用，区域冗余函数计划的“[随时就绪实例](functions-premium-plan.md#always-ready-instances)”最小值应设置为 3。 这是为了强制区域冗余函数应用始终具有足够的实例，以满足每个区域至少一个辅助角色。

下面是区域冗余的 ARM 模板段，高级函数计划，其中显示了新的 zoneRedundant 字段和容量规范。 

```
    "resources": [
        {
            "type": "Microsoft.Web/serverfarms",
            "apiVersion": "2021-01-15",
            "name": "your_plan_name_here",
            "location": "Central US",
            "sku": {
                "name": "EP3",
                "tier": "ElasticPremium",
                "size": "EP3",
                "family": "EP", 
                "capacity": 3
            },
            "kind": "elastic",
            "properties": {
                "perSiteScaling": false,
                "elasticScaleEnabled": true,
                "maximumElasticWorkerCount": 20,
                "isSpot": false,
                "reserved": false,
                "isXenon": false,
                "hyperV": false,
                "targetWorkerCount": 0,
                "targetWorkerSizeId": 0, 
                "zoneRedundant": true
            }
        }
    ]
```

要了解详情，请参阅[为 Azure Functions 中的函数应用自动执行资源部署](functions-infrastructure-as-code.md)。
