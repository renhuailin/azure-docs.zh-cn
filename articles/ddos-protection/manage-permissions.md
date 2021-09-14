---
title: Azure DDoS 防护计划权限
description: 了解如何管理保护计划中的权限。
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
ms.openlocfilehash: 30a081ab25e5c7918e7253649983ddea54ddf2b4
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123439301"
---
# <a name="manage-ddos-protection-plans-permissions-and-restrictions"></a>管理 DDoS 保护计划：权限和限制

DDoS 防护计划可跨区域和订阅生效。 同一计划可跨租户从不同区域中的其他订阅链接到虚拟机。 如果受保护的公共 IP 地址超过 100，计划关联的订阅将产生针对计划以及超额费用的每月定期帐单。 有关 DDoS 定价的详细信息，请参阅[定价详细信息](https://azure.microsoft.com/pricing/details/ddos-protection/)。

## <a name="prerequisites"></a>先决条件

- 在完成本教程中的步骤之前，必须先创建 [Azure DDoS 标准防护计划](manage-ddos-protection.md)。

## <a name="permissions"></a>权限

若要使用 DDoS 保护计划，必须将你的帐户分配到[网络参与者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)角色或分配有下表所列适当操作的[自定义](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)角色：

| 操作                                            | 名称                                     |
| ---------                                         | -------------                            |
| Microsoft.Network/ddosProtectionPlans/read        | 读取 DDoS 保护计划              |
| Microsoft.Network/ddosProtectionPlans/write       | 创建或更新 DDoS 保护计划  |
| Microsoft.Network/ddosProtectionPlans/delete      | 删除 DDoS 保护计划            |
| Microsoft.Network/ddosProtectionPlans/join/action | 加入 DDoS 保护计划              |

若要为虚拟网络启用 DDoS 保护，还必须为你的帐户分配[适用于虚拟网络的适当操作](../virtual-network/manage-virtual-network.md#permissions)。

> [!IMPORTANT]
> 在虚拟网络上启用 DDoS 保护计划后，在该虚拟网络上的后续操作仍需要 `Microsoft.Network/ddosProtectionPlans/join/action` 操作权限。

## <a name="azure-policy"></a>Azure Policy

大多数组织都不需要创建多个计划。 无法直接在订阅之间移动计划。 如果要更改计划所位于的订阅，需要删除现有计划并创建新的计划。

对于具有各种订阅的客户，以及希望确保在其租户中部署一个计划以便控制成本的客户，可以使用 Azure Policy 来[限制创建 Azure DDoS 防护标准计划](https://aka.ms/ddosrestrictplan)。 此策略将会阻止创建任何 DDoS 计划，除非以前已将订阅标记为例外。 此策略还将显示已经部署了（但不应该部署）DDoS 计划的所有订阅的列表，将它们标记为“不符合”。


## <a name="next-steps"></a>后续步骤

要了解如何查看和配置 DDoS 防护计划的遥测，请继续阅读教程。

> [!div class="nextstepaction"]
> [查看和配置 DDoS 防护遥测](telemetry.md)
