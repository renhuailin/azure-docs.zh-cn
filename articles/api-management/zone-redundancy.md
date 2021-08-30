---
title: 适用于 Azure API 管理的可用性区域支持
description: 了解如何通过启用区域冗余来改善区域中 Azure API 管理服务实例的复原能力。
author: dlepow
ms.service: api-management
ms.topic: how-to
ms.date: 08/11/2021
ms.author: apimpm
ms.custom: references_regions
ms.openlocfilehash: 2415a07a215c99a1775593aae403af6f84b308fa
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121752474"
---
# <a name="availability-zone-support-for-azure-api-management"></a>适用于 Azure API 管理的可用性区域支持 

本文介绍如何使用 Azure 门户为 API 管理实例启用区域冗余。 [区域冗余](../availability-zones/az-overview.md#availability-zones)可为特定 Azure 区域（位置）中的服务实例提供复原能力和高可用性。 借助区域冗余，API 管理实例（管理 API、开发人员门户、Git 配置）的网关和控制平面可在物理上分隔的区域中的数据中心间进行复制，使其能够在发生区域故障时复原。 

API 管理还支持[多区域部署](api-management-howto-deploy-multi-region.md)，这可帮助减少地理上分散的 API 使用者感知到的请求延迟，并提高网关组件在一个区域脱机时的可用性。 区域内实现冗余的可用性区域和多区域部署的组合可提高网关在发生区域中断时的可用性，有助于提高 API 管理实例的可靠性和性能。

[!INCLUDE [premium.md](../../includes/api-management-availability-premium.md)]

## <a name="supported-regions"></a>支持的区域

以下 Azure 区域当前支持为实现区域冗余配置 API 管理。

* 澳大利亚东部
* 巴西南部
* 加拿大中部
* 印度中部 (*)
* 美国中部
* 美国东部
* 美国东部 2
* 法国中部
* 德国中西部
* Japan East
* 韩国中部 (*)
* 欧洲北部
* 挪威东部 (*)
* 南非北部 (*)
* 美国中南部
* 东南亚
* 英国南部
* 西欧
* 美国西部 2
* 美国西部 3

> [!IMPORTANT]
> 带有 * 的区域在 Azure 订阅中具有限制性访问权限，以启用可用性区域支持。 请与你的 Microsoft 销售或客户代表合作

## <a name="prerequisites"></a>必备条件

* 如果尚未创建 API 管理服务实例，请参阅[创建 API 管理服务实例](get-started-create-service-instance.md)。 选择高级服务层级。
* 如果 API 管理实例部署在[虚拟网络](api-management-using-with-vnet.md)中，请确保在计划启用区域冗余的任何新位置设置虚拟网络、子网和公共 IP 地址。

## <a name="enable-zone-redundancy---portal"></a>启用区域冗余 - 门户

在门户中，在将位置添加到 API 管理服务，或更新现有位置的配置时可以选择启用区域冗余。

1. 在 Azure 门户中导航到 API 管理服务，然后在菜单中选择“位置”。
1. 选择现有位置，或在顶部栏中选择“+ 添加”。 该位置必须[支持可用性区域](#supported-regions)。
1. 选择该位置中的缩放[单元](upgrade-and-scale.md)数。
1. 在“可用性区域”中，选择一个或多个区域。 选择的单元数必须在可用性区域间均匀分布。 例如，如果选择了 3 个单元，则选择 3 个区域，以便每个区域托管一个单元。
1. 如果 API 管理实例部署在[虚拟网络](api-management-using-with-vnet.md)中，则在位置中配置虚拟网络设置。 选择位置中可用的现有虚拟网络、子网和公共 IP 地址。
1. 选择“应用”，然后选择“保存” 。

:::image type="content" source="media/zone-redundancy/add-location-zones.png" alt-text="启用区域冗余":::

> [!IMPORTANT]
> 启用、添加或删除可用性区域时，位置中的公共 IP 地址会发生更改。 使用网络设置更新区域中的可用性区域时，必须配置与以前设置的公共 IP 地址资源不同的地址资源。

> [!NOTE]
> 将更改应用于 API 管理实例可能需要 15 到 45 分钟。

## <a name="next-steps"></a>后续步骤

* 详细了解[将 Azure API 管理服务实例部署到多个 Azure 区域](api-management-howto-deploy-multi-region.md)。
* 还可以使用 [Azure 资源管理器模板](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.apimanagement/api-management-simple-zones)启用区域冗余。
* 详细了解[支持可用性区域的 Azure 服务](../availability-zones/az-region.md)。
* 详细了解在 Azure 中生成以提高[可靠性](/azure/architecture/framework/resiliency/app-design)。