---
title: Azure DDoS 保护计划权限
description: 了解如何在保护计划中管理权限。
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: df53062c7c897493a47d88ea2873f9710b9825bf
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2021
ms.locfileid: "99806250"
---
# <a name="manage-ddos-protection-plans-permissions-and-restrictions"></a>管理 DDoS 保护计划：权限和限制

DDoS 防护计划跨区域和订阅工作。 同一计划可从不同区域中的其他订阅链接到你的租户。 如果受保护的公共 IP 地址超过 100，计划关联的订阅将产生针对计划以及超额费用的每月定期帐单。 有关 DDoS 定价的详细信息，请参阅[定价详细信息](https://azure.microsoft.com/pricing/details/ddos-protection/)。

## <a name="prerequisites"></a>先决条件

- 在完成本教程中的步骤之前，必须先创建 [Azure DDoS 标准保护计划](manage-ddos-protection.md)。

## <a name="permissions"></a>权限

若要使用 DDoS 保护计划，必须将你的帐户分配到[网络参与者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)角色或分配有下表所列适当操作的[自定义](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)角色：

| 操作                                            | 名称                                     |
| ---------                                         | -------------                            |
| Microsoft.Network/ddosProtectionPlans/read        | 读取 DDoS 保护计划              |
| Microsoft.Network/ddosProtectionPlans/write       | 创建或更新 DDoS 保护计划  |
| Microsoft.Network/ddosProtectionPlans/delete      | 删除 DDoS 保护计划            |
| Microsoft.Network/ddosProtectionPlans/join/action | 加入 DDoS 保护计划              |

若要为虚拟网络启用 DDoS 保护，还必须为你的帐户分配[适用于虚拟网络的适当操作](../virtual-network/manage-virtual-network.md#permissions)。

## <a name="azure-policy"></a>Azure Policy

大多数组织都不需要创建多个计划。 无法直接在订阅之间移动计划。 如果要更改计划所位于的订阅，需要删除现有计划并创建新的计划。

对于拥有各种订阅的客户，以及希望确保在其租户中部署单个计划以实现成本控制的客户，可以使用 Azure 策略 [限制创建 Azure DDoS 保护标准计划](https://aka.ms/ddosrestrictplan)。 此策略将阻止创建任何 DDoS 计划，除非订阅之前标记为异常。 此策略还将显示已部署 DDoS 计划的所有订阅的列表，但不应将其标记为 "不符合"。


## <a name="next-steps"></a>后续步骤

要了解如何查看和配置 DDoS 防护计划的遥测，请继续阅读教程。

> [!div class="nextstepaction"]
> [查看和配置 DDoS 防护遥测](telemetry.md)
