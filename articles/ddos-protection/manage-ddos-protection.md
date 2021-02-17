---
title: 使用 Azure 门户管理 Azure DDoS 保护标准
description: 了解如何使用 Azure DDoS 保护标准来缓解攻击。
services: ddos-protection
documentationcenter: na
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: ddos-protection
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/17/2019
ms.author: kumud
ms.openlocfilehash: 336eeee7d522647d11f55a65dc9dc4e65e8b7d47
ms.sourcegitcommit: b513b0becf878eb9a1554c26da53aa48d580bb22
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/16/2021
ms.locfileid: "100535224"
---
# <a name="quickstart-create-and-configure-azure-ddos-protection-standard"></a>快速入门：创建和配置 Azure DDoS 保护标准

使用 Azure 门户开始使用 Azure DDoS 保护标准版。 

DDoS 防护计划在订阅中定义一组已启用 DDoS 防护标准的虚拟网络。 可以为组织配置一个 DDoS 防护计划，然后从多个订阅将虚拟网络链接到相同计划。 

在本快速入门中，你将创建一个 DDoS 保护计划并将其链接到虚拟网络。 

## <a name="prerequisites"></a>先决条件

- 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 通过 https://portal.azure.com 登录到 Azure 门户。 请确保将你的帐户分配到[网络参与者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)角色或分配给分配了操作方法[指南中所](manage-permissions.md)列相应操作的[自定义角色](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

## <a name="create-a-ddos-protection-plan"></a>创建 DDoS 防护计划

1. 在 Azure 门户的左上角选择 " **创建资源** "。
2. 搜索术语 " *DDoS*"。 当 " **DDoS 防护计划** " 出现在搜索结果中时，请选择它。
3. 选择“创建”。
4. 输入或选择以下值，然后选择 " **创建**"：

    |设置        |值                                              |
    |---------      |---------                                          |
    |名称           | 输入 _MyDdosProtectionPlan_。                     |
    |订阅   | 选择订阅。                         |
    |资源组 | 选择 " **新建** "，然后输入 _MyResourceGroup_。|
    |位置       | 输入 " _美国东部_"。                                  |

## <a name="enable-ddos-protection-for-a-virtual-network"></a>为虚拟网络启用 DDoS 保护

### <a name="enable-ddos-protection-for-a-new-virtual-network"></a>为新的虚拟网络启用 DDoS 保护

1. 在 Azure 门户的左上角选择 " **创建资源** "。
2. 选择“网络”，然后选择“虚拟网络” 。
3. 输入或选择以下值，接受其余默认值，然后选择 " **创建**"：

    | 设置         | 值                                           |
    | ---------       | ---------                                       |
    | 名称            | 输入 _MyVnet_。                                 |
    | 订阅    | 选择订阅。                                    |
    | 资源组  | 选择 " **使用现有**"，然后选择 **MyResourceGroup** |
    | 位置        | 输入 _美国东部_                                                    |
    | 标准 DDoS 防护 | 选择“启用”。 所选计划可位于与虚拟网络相同或不同的订阅中，但这两个订阅必须与同一 Azure Active Directory 租户相关联。|

如果已为虚拟网络启用 DDoS 防护，无法将虚拟网络移到其他资源组或订阅。 如果需要移动已启用 DDoS 标准的虚拟网络，请先禁用该标准并移动虚拟网络，然后启用 DDoS 标准。 移动后，适用于虚拟网络所有受保护的公共 IP 地址的自动优化策略阈值都将重置。

### <a name="enable-ddos-protection-for-an-existing-virtual-network"></a>为现有虚拟网络启用 DDoS 保护

1. 如果没有现有的 DDoS 防护计划，通过完成[创建 DDoS 防护计划](#create-a-ddos-protection-plan)中的步骤创建一个 DDoS 防护计划。
2. 在 Azure 门户的左上角选择 " **创建资源** "。
3. 输入想要在门户顶部的“搜索资源、服务和文档”框中为其启用 DDoS 防护标准的虚拟网络的名称。 当虚拟网络名称出现在搜索结果中时，将其选中。
4. 选择“设置”下的“DDoS 防护”。
5. 选择“标准”。 选择“DDoS 防护计划”下的现有 DDoS 防护计划，或在步骤 1 中创建的计划，然后选择“保存”。 所选计划可位于与虚拟网络相同或不同的订阅中，但这两个订阅必须与同一 Azure Active Directory 租户相关联。

### <a name="enable-ddos-protection-for-all-virtual-networks"></a>为所有虚拟网络启用 DDoS 保护

此 [策略](https://aka.ms/ddosvnetpolicy) 将检测定义的作用域中未启用 DDoS 保护标准的任何虚拟网络，然后可以选择创建一个将创建关联的修正任务来保护 VNet。 有关如何部署此策略的详细分步说明，请参阅 https://aka.ms/ddosvnetpolicy-techcommunity 。

## <a name="validate-and-test"></a>验证并测试

首先，请查看 DDoS 保护计划的详细信息：

1. 在门户左上角选择“所有服务”。
2. 在“筛选器”框中输入 DDoS。 当“DDoS 防护计划”出现在结果中时，将其选中。
3. 从列表中选择 DDoS 保护计划。

应列出 _MyVnet_ 虚拟网络。 

### <a name="view-protected-resources"></a>查看受保护的资源
在 " **受保护的资源**" 下，可以查看受保护的虚拟网络和公共 IP 地址，或将更多虚拟网络添加到 DDoS 保护计划：

![查看受保护的资源](./media/manage-ddos-protection/ddos-protected-resources.png)

## <a name="clean-up-resources"></a>清理资源

可在下一教程中保留资源。 如果不再需要，请删除 _MyResourceGroup_ 资源组。 删除资源组时，还会删除 DDoS 保护计划及其所有相关资源。 如果你不打算使用这一 DDoS 防护计划，则应删除资源，以免产生不必要的费用。

   >[!WARNING]
   >此操作不可逆。

1. 在 Azure 门户中，搜索并选择“资源组”，或者从 Azure 门户菜单中选择“资源组” 。

2. 筛选或向下滚动以查找 _MyResourceGroup_ 资源组。

3. 选择该资源组，然后选择“删除资源组”。

4. 键入资源组名称以确认，然后选择“删除”。

为虚拟网络禁用 DDoS 保护： 

1. 输入想要在门户顶部的“搜索资源、服务和文档”框中为其禁用 DDoS 防护标准的虚拟网络的名称。 当虚拟网络名称出现在搜索结果中时，将其选中。
2. **在 "DDoS 保护标准" 下** 选择 "**禁用**"。

如果要删除 DDoS 保护计划，必须先取消关联所有虚拟网络的关联。 

## <a name="next-steps"></a>后续步骤

要了解如何查看和配置 DDoS 防护计划的遥测，请继续阅读教程。

> [!div class="nextstepaction"]
> [查看和配置 DDoS 防护遥测](telemetry.md)
