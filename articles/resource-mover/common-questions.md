---
title: 有关 Azure 资源转移器的常见问题？
description: 获取 Azure 资源转移器常见问题的解答
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: raynew
ms.openlocfilehash: c586832386f3bf6419d8af94ad23662c6801b1ae
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820868"
---
# <a name="common-questions"></a>常见问题

本文提供关于 [Azure 资源转移器](overview.md)的常见问题的解答。


## <a name="moving-across-regions"></a>跨区域移动

### <a name="can-i-move-resources-across-any-regions"></a>我可以跨任何区域移动资源吗？

目前，可以将资源从任何源公共区域移到任何目标公共区域，具体取决于 [该区域中可用的资源类型](https://azure.microsoft.com/global-infrastructure/services/)。 当前不支持在 Azure 政府区域中移动资源。

### <a name="what-resources-can-i-move-across-regions-using-resource-mover"></a>可以使用资源转移器跨区域移动哪些资源？

使用资源转移器，当前可以在区域之间移动以下资源：

- Azure VM 和关联的磁盘
- NIC
- 可用性集 
- Azure 虚拟网络 
- 公共 IP 地址
- 网络安全组 (NSG)：
- 内部和公共负载均衡器 
- Azure SQL 数据库和弹性池

### <a name="can-i-move-disks-across-regions"></a>能否跨区域移动磁盘？

不能选择磁盘作为跨区域移动的资源。 但是，磁盘将作为 VM 移动的一部分进行移动。

### <a name="can-i-move-resources-across-subscriptions-when-i-move-them-across-regions"></a>当我跨区域移动资源时，我能否跨订阅移动资源？

可以在将资源移动到目标区域后更改订阅。 [详细了解](../azure-resource-manager/management/move-resource-group-and-subscription.md)如何将资源移动到其他订阅。 

### <a name="does-azure-resource-move-service-store-customer-data"></a>Azure 资源移动服务是否会将客户数据存储在一起？ 
否。 资源移动服务不存储客户数据，它只存储元数据信息，这有助于客户跟踪和跟踪选定要移动的资源。


### <a name="where-is-the-metadata-for-moving-across-regions-stored"></a>跨区域移动的元数据存储在何处？

存储在 [Azure Cosmos](../cosmos-db/database-encryption-at-rest.md) 数据库、[Azure Blob 存储](../storage/common/storage-service-encryption.md)、Microsoft 订阅中。 目前元数据存储在美国东部 2 和欧洲北部。 我们会将这一覆盖范围扩大到其他地区。 这不会对你跨任何公共区域移动资源造成限制。

### <a name="is-the-collected-metadata-encrypted"></a>收集的元数据是否已加密？

是的，传输中的元数据和静态元数据都已加密。
- 在传输过程中，元数据使用 HTTPS 通过 Internet 安全地发送到资源转移器服务。
- 在存储中，元数据已加密。

### <a name="how-is-managed-identity-used-in-resource-mover"></a>在资源转移器中如何使用托管标识？

[托管标识](../active-directory/managed-identities-azure-resources/overview.md) (以前称为托管服务标识 (MSI) # A3 为 Azure 服务提供 Azure AD 中的自动托管标识。
- 资源转移器使用托管标识，以便可以访问 Azure 订阅，从而跨区域移动资源。
- 移动集合需要系统分配的标识，可以访问包含要移动资源的订阅。

- 如果在门户中跨区域移动资源，此过程将自动执行。
- 如果使用 PowerShell 移动资源，则运行 cmdlet 为集合分配系统分配的标识，然后为标识主体分配具有正确订阅权限的角色。 

### <a name="what-managed-identity-permissions-does-resource-mover-need"></a>资源转移器需要哪些托管标识权限？

Azure 资源转移器托管标识至少需要以下权限： 

- 在用户订阅中写入/创建资源的权限，参与者角色具有此权限。 
- 创建角色分配的权限。 通常，所有者、用户访问管理员分配到了 Microsoft.Authorization/role assignments/write 权限的自定义角色具有此权限  。 如果已向数据共享资源的托管标识分配对 Azure 数据存储的访问权限，则不需要此权限。 
 
当你在门户中的资源移动器中心添加资源时，只要用户具有上述权限，权限就会自动处理。 如果通过 PowerShell 添加资源，则需要手动分配权限。

> [!IMPORTANT]
> 强烈建议不要修改或删除标识角色分配。 

### <a name="what-should-i-do-if-i-dont-have-permissions-to-assign-role-identity"></a>如果我没有分配角色标识的权限，该怎么办？

可能的原因  | **建议**
--- | ---
当你首次添加资源时，你不是参与者和用户访问管理员（或所有者）  。 | 为订阅使用具有参与者和用户访问管理员（或所有者）权限的帐户  。
资源转移器托管标识没有所需的角色。 | 添加“参与者”和“用户访问管理员”角色。
资源转移器托管标识重置为“无”。 | 在“移动集合”>“标识”中重新启用系统分配的标识。 或者，在“添加资源”中再次添加资源，这将执行相同的操作。  
订阅已移动到其他租户。 | 禁用，然后为移动集合启用托管标识。

### <a name="how-can-i-do-multiple-moves-together"></a>如何一起执行多个移动？

使用门户中的更改选项根据需要更改源/目标组合。

### <a name="what-happens-when-i-remove-a-resource-from-a-list-of-move-resources"></a>从移动资源列表中删除资源时会发生什么情况？

您可以删除已添加到移动列表中的资源。 从列表中删除资源时的行为取决于资源状态。 [了解详细信息](remove-move-resources.md#vm-resource-state-after-removing)。



## <a name="next-steps"></a>后续步骤

[详细了解](about-move-process.md)资源转移器组件和移动过程。
