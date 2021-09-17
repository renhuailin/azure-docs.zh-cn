---
title: 迁移到 Azure 基于角色的访问控制 | Microsoft Docs
description: 了解如何从保管库访问策略迁移到 Azure 角色。
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 8/30/2020
ms.author: mbaldwin
ms.openlocfilehash: 88ac65c35b29c2113576594788f820973331d64b
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2021
ms.locfileid: "122515008"
---
# <a name="migrate-from-vault-access-policy-to-an-azure-role-based-access-control-permission-model"></a>从保管库访问策略迁移到 Azure 基于角色的访问控制权限模型

保管库访问策略模型是内置于 Key Vault 中的现有授权系统，用于提供对密钥、机密和证书的访问权限。 可以通过在 Key Vault 范围内将个人权限分配给安全主体（用户、组、服务主体、托管标识）来控制访问。 

Azure 基于角色的访问控制 (Azure RBAC) 是在 [Azure 资源管理器](../../azure-resource-manager/management/overview.md)基础上构建的授权系统，针对 Azure 资源提供精细的访问权限管理。 使用 Azure RBAC，你可以通过创建角色分配来控制对资源的访问，这些角色分配由三个元素组成：安全主体、角色定义（预定义的权限集）和范围（资源组或单个资源）。 有关详细信息，请参阅 [Azure 基于角色的访问控制 (Azure RBAC)](../../role-based-access-control/overview.md)。

在迁移到 Azure RBAC 之前，请务必了解其优点和限制。

与保管库访问策略相比，Azure RBAC 密钥的优点是：
- 通过跨 Azure 服务使用相同的 API，为 Azure 资源提供统一的访问控制模型
- 针对管理员的集中式访问管理 - 在一个视图中管理所有 Azure 资源
- 与 [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md) 集成以实现基于时间的访问控制
- 拒绝分配 - 可以排除特定范围内的安全主体。 有关信息，请参阅[了解 Azure 拒绝分配](../../role-based-access-control/deny-assignments.md)

Azure RBAC 的缺点：
- 角色分配延迟 - 可能需要几分钟才能应用角色分配。 保管库访问策略会立即分配。
- 角色分配数量有限 - 每个订阅 2000 个角色分配，每个 Key Vault 有 1024 个访问策略

## <a name="access-policies-to-azure-roles-mapping"></a>Azure 角色映射的访问策略

Azure RBAC 基于角色的访问控制有多个 Azure 内置角色，可将其分配给用户、组、服务主体和托管标识。 如果内置角色不能满足组织的特定需求，你可以创建自己的 [Azure 自定义角色](../../role-based-access-control/custom-roles.md)。

用于密钥、证书和机密访问管理的 Key Vault 内置角色：
- Key Vault 管理员
- Key Vault 读取者
- 密钥保管库证书管理人员
- Key Vault 加密管理人员
- Key Vault 加密用户
- 密钥保管库加密服务加密用户
- Key Vault 机密管理人员
- Key Vault 机密用户

有关现有内置角色的详细信息，请参阅 [Azure 内置角色](../../role-based-access-control/built-in-roles.md)

可以通过单独选择的权限或预定义的权限模板来分配保管库访问策略。

访问策略预定义的权限模板：
- 密钥、机密、证书管理
- 密钥和机密管理
- 机密和证书管理
- 密钥管理
- 机密管理
- 证书管理
- SQL Server 连接器
- Azure Data Lake Storage 或 Azure 存储
- Azure 备份
- Exchange Online 客户密钥
- SharePoint Online 客户密钥
- Azure 信息 BYOK

### <a name="access-policy-templates-to-azure-roles-mapping"></a>Azure 角色映射的访问策略模板
| 访问策略模板 | 操作 | Azure 角色 |
| --- | --- | --- |
| 密钥、机密、证书管理 | 密钥：所有操作 <br>证书：所有操作<br>机密：所有操作 | Key Vault 管理员 |
| 密钥和机密管理 | 密钥：所有操作 <br>机密：所有操作| Key Vault 加密管理人员 <br> Key Vault 机密管理人员 |
| 机密和证书管理 | 证书：所有操作 <br>机密：所有操作| Key Vault 证书管理人员 <br> Key Vault 机密管理人员|
| 密钥管理 | 密钥：所有操作| Key Vault 加密管理人员|
| 机密管理 | 机密：所有操作| Key Vault 机密管理人员|
| 证书管理 | 证书：所有操作 | Key Vault 证书管理人员|
| SQL Server 连接器 | 密钥：获取、列出、包装密钥、解包密钥 | 密钥保管库加密服务加密用户|
| Azure Data Lake Storage 或 Azure 存储 | 密钥：获取、列出、解包密钥 | 空值<br> 所需的自定义角色|
| Azure 备份 | 密钥：获取、列出、备份<br> 机密：获取、列出、备份 | 空值<br> 所需的自定义角色|
| Exchange Online 客户密钥 | 密钥：获取、列出、包装密钥、解包密钥 | 密钥保管库加密服务加密用户|
| Exchange Online 客户密钥 | 密钥：获取、列出、包装密钥、解包密钥 | 密钥保管库加密服务加密用户|
| Azure 信息 BYOK | 密钥：获取、解密、签名 | 空值<br>所需的自定义角色|


## <a name="assignment-scopes-mapping"></a>分配范围映射  

适用于 Key Vault 的 Azure RBAC 允许在以下范围内分配角色：
- 管理组
- 订阅
- 资源组
- Key Vault 资源
- 单个密钥、机密和证书

保管库访问策略权限模型限制为仅在 Key Vault 资源级别分配策略。

通常，最好每个应用程序都有一个密钥保管库，并在密钥保管库级别管理访问权限。 在某些情况下，在其他范围内管理访问权限可以简化访问管理。

- **基础结构、安全管理员和操作员：使用保管库访问策略在管理组、订阅或资源组级别管理密钥保管库组需要维护每个密钥保管库的策略。 Azure RBAC 允许在管理组、订阅或资源组范围创建一个角色分配。 该分配将应用于在同一范围创建的任何新的密钥保管库。 在这种情况下，建议将 Privileged Identity Management 与实时访问结合使用，而不是提供永久访问权限。
 
- **应用程序：在某些情况下，应用程序需要与其他应用程序共享机密。 如果使用保管库访问策略，则必须创建单独的密钥保管库，以避免提供对所有机密的访问权限。 Azure RBAC 允许针对单个机密分配角色，而不使用单个密钥保管库。

## <a name="vault-access-policy-to-azure-rbac-migration-steps"></a>将保管库访问策略迁移到 Azure RBAC 的步骤
Azure RBAC 和保管库访问策略权限模型之间存在很多差异。 为了避免迁移过程中出现中断，建议执行以下步骤。
 
1. **识别并分配角色**：根据上面的映射表标识内置角色并根据需要创建自定义角色。 根据范围映射指南，在范围内分配角色。 若要详细了解如何将角色分配到密钥保管库，请参阅[通过 Azure 基于角色的访问控制提供对 Key Vault 的访问权限](rbac-guide.md)
1. **验证角色分配**：Azure RBAC 中的角色分配可能需要几分钟才能传播。 有关如何检查角色分配的指南，请参阅[列出范围内的角色分配](../../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-for-a-user-at-a-scope)
1. **在密钥保管库上配置监视和警报**：请务必启用日志记录并针对拒绝访问异常设置警报。 有关详细信息，请参阅 [Azure Key Vault 的监视和警报](./alert.md)
1. **在 Key Vault 上设置 Azure 基于角色的访问控制权限模型**：启用 Azure RBAC 权限模型将使所有现有的访问策略失效。 如果出现错误，则可以在所有现有访问策略保持不变的情况下切换回权限模型。

> [!NOTE]
> 更改权限模型需要具有“Microsoft.Authorization/roleAssignments/write”权限，该权限是[所有者](../../role-based-access-control/built-in-roles.md#owner)和[用户访问管理员](../../role-based-access-control/built-in-roles.md#user-access-administrator)角色的一部分。 不支持经典订阅管理员角色（如“服务管理员”和“共同管理员”）。

> [!NOTE]
> 启用 Azure RBAC 权限模型后，尝试更新访问策略的所有脚本都将失败。 请务必更新这些脚本以使用 Azure RBAC。

## <a name="troubleshooting"></a>疑难解答
-  几分钟后角色分配不起作用 - 在某些情况下，角色分配可能需要更长时间。 在代码中编写重试逻辑来处理这些情况是非常重要的。
- 删除（软删除）并恢复 Key Vault 时角色分配消失 - 所有 Azure 服务中的软删除功能当前都受到此限制。 在恢复后，需要重新创建所有角色分配。    

## <a name="learn-more"></a>了解更多

- [Azure RBAC 概述](../../role-based-access-control/overview.md)
- [自定义角色教程](../../role-based-access-control/tutorial-custom-role-cli.md)
- [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)
