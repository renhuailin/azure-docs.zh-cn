---
title: 跨资源组、订阅或区域移动 Azure 资源。
description: 可以跨资源组、订阅或区域移动的 Azure 资源类型的概述。
ms.topic: conceptual
ms.date: 04/16/2021
ms.openlocfilehash: 646c0892ac3609fc531e91f4bfb9c1de6a61bc38
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730278"
---
# <a name="move-azure-resources-across-resource-groups-subscriptions-or-regions"></a>跨资源组、订阅或区域移动 Azure 资源

Azure 资源可以移动到新的资源组或订阅，或是跨区域移动。

## <a name="move-resources-across-resource-groups-or-subscriptions"></a>跨资源组或订阅移动资源

可以将 Azure 资源移动到另一个 Azure 订阅或同一订阅下的另一个资源组。 可以使用 Azure 门户、Azure PowerShell、Azure CLI 或 REST API 移动资源。 要了解操作详细，请参阅[将资源移动到新资源组或订阅](move-resource-group-and-subscription.md)。

### <a name="upgrade-a-subscription"></a>升级订阅

如果确实想要升级 Azure 订阅（例如从免费切换到即用即付），则需要转换订阅。

- 若要升级免费试用版，请参阅[将免费试用版或 Microsoft Imagine Azure 订阅升级到即用即付](../../cost-management-billing/manage/upgrade-azure-subscription.md)。
- 若要更改即用即付帐户，请参阅[将 Azure 即用即付订阅更改为其他套餐](../../cost-management-billing/manage/switch-azure-offer.md)。

如果无法转换订阅，请[创建 Azure 支持请求](../../azure-portal/supportability/how-to-create-azure-support-request.md)。 选择“订阅管理”  作为问题类型。

## <a name="move-resources-across-regions"></a>跨区域移动资源

Azure 地理位置、区域和可用性区域构成了 Azure 全球基础结构的基础。 Azure [地理位置](https://azure.microsoft.com/global-infrastructure/geographies/)通常包含两个或更多个 [Azure 区域](https://azure.microsoft.com/global-infrastructure/regions/)。 区域是一个地理位置内的地区，包含可用性区域和多个数据中心。

在特定 Azure 区域中部署资源后，有很多原因可能要将资源移动到不同的区域。

- **与区域的推出相适应**：将资源移到新推出的 Azure 区域（以前不存在此区域）。
- **与服务/功能相适应**：移动资源以利用特定区域中提供的服务或功能。
- **应对业务发展**：将资源移动到某个区域，以应对业务变化，例如合并或收购。
- **针对临近进行协调**：将资源移动到你的业务所在的本地区域。
- **满足数据要求**：移动资源，使之符合数据驻留要求或数据分类需求。 [了解详细信息](https://azure.microsoft.com/mediahandler/files/resourcefiles/achieving-compliant-data-residency-and-security-with-azure/Achieving_Compliant_Data_Residency_and_Security_with_Azure.pdf)。
- **应对部署要求**：移动错误部署的资源，或者移动资源来应对容量需求。
- 应对停止使用：因为区域已停止使用而移动资源。

### <a name="move-resources-with-resource-mover"></a>使用资源转移器移动资源

可以使用 [Azure 资源转移器](../../resource-mover/overview.md)将资源移动到不同区域。 资源转移器提供以下功能：

- 跨区域移动资源的单一中心。
- 缩短了移动时间并降低了复杂性。 你所需要的一切都在同一个位置。
- 移动不同类型的 Azure 资源的简单、一致的体验。
- 标识要移动的资源之间的依赖项的一种简单方法。 此标识有助于将相关资源移动到一起，以便在移动后，所有资源在目标区域中都按预期方式工作。
- 如果要在移动后删除源区域中的资源，请在源区域中自动清除它们。
- 测试。 如果不想进行完整移动，可以尝试移动，然后将其丢弃。

可以使用几种不同的方法将资源移动到另一个区域：

- 从资源组开始移动资源：使用此方法时，会从资源组中启动区域移动。 选择要移动的资源后，该过程会在资源转移器中心继续进行，以检查资源依赖项并协调移动过程。 [了解详细信息](../../resource-mover/move-region-within-resource-group.md)。
- 直接从资源转移器中心开始移动资源：使用此方法时，可以直接在中心内启动区域移动过程。 [了解详细信息](../../resource-mover/tutorial-move-region-virtual-machines.md)。

## <a name="next-steps"></a>后续步骤

- 若要检查资源类型是否支持移动，请参阅[支持移动操作的资源](move-support-resources.md)。
- 若要了解有关区域移动过程的详细信息，请参阅[关于移动过程](../../resource-mover/about-move-process.md)。
