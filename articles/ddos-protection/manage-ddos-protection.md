---
title: 使用 Azure 门户管理 Azure DDoS 保护标准
description: 了解如何使用 Azure DDoS 防护标准来缓解攻击。
services: ddos-protection
documentationcenter: na
author: aletheatoh
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: ddos-protection
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/17/2019
ms.author: yitoh
ms.openlocfilehash: 803d89dedc59c53001f078492d956e5e50d5411b
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123305115"
---
# <a name="quickstart-create-and-configure-azure-ddos-protection-standard"></a>快速入门：创建和配置 Azure DDoS 防护标准

借助于 Azure 门户开始使用 Azure DDoS 防护标准。 

DDoS 防护计划在订阅中定义一组已启用 DDoS 防护标准的虚拟网络。 可以为组织配置一个 DDoS 防护计划，然后从多个订阅将虚拟网络链接到相同计划。 

在本快速入门中，你将创建一个 DDoS 防护计划，并将其链接到虚拟网络。 

## <a name="prerequisites"></a>先决条件

- 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 通过 https://portal.azure.com 登录到 Azure 门户。 确保将帐户分配给[网络参与者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)角色或[自定义角色](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)，后者被分配了关于[权限](manage-permissions.md)的操作指南中列出的适当操作。

## <a name="create-a-ddos-protection-plan"></a>创建 DDoS 防护计划

1. 在 Azure 门户的左上角，选择“创建资源”。
2. 搜索术语“DDoS”。 当“DDoS 防护计划”出现在搜索结果中时，将其选中。
3. 选择“创建”  。
4. 输入或选择以下值，然后选择“创建”：

    |设置        |值                                              |
    |---------      |---------                                          |
    |名称           | 输入“MyDdosProtectionPlan”。                     |
    |订阅   | 选择订阅。                         |
    |资源组 | 选择“新建”，并输入“MyResourceGroup”。|
    |位置       | 输入“美国东部”。                                  |

## <a name="enable-ddos-protection-for-a-virtual-network"></a>为虚拟网络启用 DDoS 防护

### <a name="enable-ddos-protection-for-a-new-virtual-network"></a>为新的虚拟网络启用 DDoS 防护

1. 在 Azure 门户的左上角，选择“创建资源”。
2. 选择“网络”，然后选择“虚拟网络” 。
3. 输入或选择以下值，接受剩下的默认设置，然后选择“创建”：

    | 设置         | 值                                           |
    | ---------       | ---------                                       |
    | 名称            | 输入“MyVnet”。                                 |
    | 订阅    | 选择订阅。                                    |
    | 资源组  | 选择“使用现有”，然后选择“MyResourceGroup”  |
    | 位置        | 输入“美国东部”                                                    |
    | 标准 DDoS 防护 | 选择“启用”。 所选计划可位于与虚拟网络相同或不同的订阅中，但这两个订阅必须与同一 Azure Active Directory 租户相关联。|

如果已为虚拟网络启用 DDoS 防护，无法将虚拟网络移到其他资源组或订阅。 如果需要移动已启用 DDoS 标准的虚拟网络，请先禁用该标准并移动虚拟网络，然后启用 DDoS 标准。 移动后，适用于虚拟网络所有受保护的公共 IP 地址的自动优化策略阈值都将重置。

### <a name="enable-ddos-protection-for-an-existing-virtual-network"></a>为现有虚拟网络启用 DDoS 防护

1. 如果没有现有的 DDoS 防护计划，通过完成[创建 DDoS 防护计划](#create-a-ddos-protection-plan)中的步骤创建一个 DDoS 防护计划。
2. 输入想要在 Azure 门户顶部的“搜索资源、服务和文档”框中为其启用 DDoS 防护标准版的虚拟网络的名称。 当虚拟网络名称出现在搜索结果中时，将其选中。
3. 选择“设置”下的“DDoS 防护”。
4. 选择“标准”。 选择“DDoS 防护计划”下的现有 DDoS 防护计划，或在步骤 1 中创建的计划，然后选择“保存”。 所选计划可位于与虚拟网络相同或不同的订阅中，但这两个订阅必须与同一 Azure Active Directory 租户相关联。

### <a name="enable-ddos-protection-for-all-virtual-networks"></a>为所有虚拟网络启用 DDoS 防护

[内置策略](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F94de2ad3-e0c1-4caf-ad78-5d47bbc83d3d)会在定义的范围内检测未启用 DDoS 防护标准版的任何虚拟网络，然后创建一个修正任务（可选），该任务会创建关联来保护 VNet。 有关内置策略的完整列表，请参阅 [Azure DDoS 防护标准版的 Azure Policy 内置定义](policy-reference.md)。 

## <a name="validate-and-test"></a>验证并测试

首先检查 DDoS 防护计划的详细信息：

1. 在门户左上角选择“所有服务”。
2. 在“筛选器”框中输入 DDoS。 当“DDoS 防护计划”出现在结果中时，将其选中。
3. 从列表中选择你的 DDoS 防护计划。

应会列出 MyVnet 虚拟网络。 

### <a name="view-protected-resources"></a>查看受保护的资源
在“受保护的资源”下，可以查看受保护的虚拟网络和公共 IP 地址，或者将更多虚拟网络添加到 DDoS 防护计划中：

![查看受保护的资源](./media/manage-ddos-protection/ddos-protected-resources.png)

## <a name="clean-up-resources"></a>清理资源

可保留资源以供下一教程使用。 如果不再需要，请删除“MyResourceGroup”资源组。 删除资源组时，DDoS 防护计划及其所有相关资源也会一起删除。 如果你不打算使用此 DDoS 防护计划，则应删除资源，以免产生不必要的费用。

   >[!WARNING]
   >此操作不可逆。

1. 在 Azure 门户中，搜索并选择“资源组”，或者从 Azure 门户菜单中选择“资源组” 。

2. 筛选或向下滚动以找到 MyResourceGroup 资源组。

3. 选择该资源组，然后选择“删除资源组”。

4. 键入资源组名称以确认，然后选择“删除”。

若要为虚拟网络禁用 DDoS 防护，请执行以下操作： 

1. 输入想要在门户顶部的“搜索资源、服务和文档”框中为其禁用 DDoS 防护标准的虚拟网络的名称。 当虚拟网络名称出现在搜索结果中时，将其选中。
2. 在“DDoS 防护标准版”下，选择“禁用”。

如果要删除 DDoS 防护计划，必须首先取消与之关联的所有虚拟网络。 

## <a name="next-steps"></a>后续步骤

要了解如何查看和配置 DDoS 防护计划的遥测，请继续阅读教程。

> [!div class="nextstepaction"]
> [查看和配置 DDoS 防护遥测](telemetry.md)
