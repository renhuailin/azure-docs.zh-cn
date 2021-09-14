---
title: 使用 RBAC 共享库
description: 了解如何使用基于角色的访问控制 (RBAC) 共享库。
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 08/31/2021
ms.author: cynthn
ms.custom: template-how-to
ms.openlocfilehash: 01c98ba8d619573c241e5a8fdfa2f774b4aed22d
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123452283"
---
# <a name="use-rbac-to-share-image-gallery-resources"></a>使用 RBAC 共享映像库资源

由于共享映像库、映像定义和映像版本都是资源，因此可以使用内置的本机 Azure RBAC 控制来共享它们。 使用 Azure RBAC 可与其他用户、服务主体和组共享这些资源。 甚至可以与创建这些资源的租户外部的个人共享访问权限。 一旦用户有权访问共享的映像版本，他们就可以部署 VM 或虚拟机规模集。  

建议在库级别共享以获得最佳体验。 我们建议不要共享单个映像版本。 有关 Azure RBAC 的详细信息，请参阅[分配 Azure 角色](../role-based-access-control/role-assignments-portal.md)。

如果该用户不在你的组织中，他（她）会收到一封邀请加入组织的电子邮件。 该用户需要接受邀请，才能在其资源列表中看到库以及所有映像定义和版本。

## <a name="share-a-gallery"></a>共享库

### <a name="portal"></a>[门户](#tab/portal)

如果该用户不在你的组织中，他（她）会收到一封邀请加入组织的电子邮件。 该用户需要接受邀请，才能在其资源列表中看到库以及所有映像定义和版本。

1. 在映像库页的左侧菜单中，选择“访问控制 (IAM)”。 
1. 在“添加角色分配”下，选择“添加”。  此时会打开“添加角色分配”窗格。 
1. 在“角色”下，选择“读取者”。 
1. 在“将访问权限分配给”下，保留默认设置“Azure AD 用户、组或服务主体”。 
1. 在“选择”下，键入要邀请的人员的电子邮件地址。
1. 如果该用户不在你的组织中，将会显示消息“将向此用户发送一封电子邮件，使其能够与 Microsoft 协作”。 选择具有该电子邮件地址的用户，然后单击“保存”。


### <a name="cli"></a>[CLI](#tab/cli)

若要获取库的对象 ID，请使用 [az sig show](/cli/azure/sig#az_sig_show)。

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

使用对象 ID 作为范围，并使用电子邮件地址和 [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) 为用户授予对共享映像库的访问权限。 请将 `<email-address>` 和 `<gallery iD>` 替换为自己的信息。

```azurecli-interactive
az role assignment create \
   --role "Reader" \
   --assignee <email address> \
   --scope <gallery ID>
```

有关如何使用 RBAC 共享资源的详细信息，请参阅[使用 RBAC 和 Azure CLI 管理访问权限](../role-based-access-control/role-assignments-cli.md)。

### <a name="powershell"></a>[PowerShell](#tab/powershell)

使用电子邮件地址和 [Get-AzADUser](/powershell/module/az.resources/get-azaduser) cmdlet 获取用户的对象 ID，然后使用 [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) 为用户授予对库的访问权限。 请将此示例中的示例电子邮件地址 alinne_montes@contoso.com 替换为你自己的信息。

```azurepowershell-interactive
# Get the object ID for the user
$user = Get-AzADUser -StartsWith alinne_montes@contoso.com
# Grant access to the user for our gallery
New-AzRoleAssignment `
   -ObjectId $user.Id `
   -RoleDefinitionName Reader `
   -ResourceName $gallery.Name `
   -ResourceType Microsoft.Compute/galleries `
   -ResourceGroupName $resourceGroup.ResourceGroupName

```

---


## <a name="next-steps"></a>后续步骤

创建[映像定义和映像版本](image-version.md)。

[Azure 映像生成器（预览版）](./image-builder-overview.md)可以帮助自动创建映像版本，你甚至可以使用它进行更新以及[从现有的映像版本创建新映像版本](./windows/image-builder-gallery-update-image-version.md)。 

此外可以使用模板创建共享映像库资源。 提供多个 Azure 快速入门模板： 

- [创建共享映像库](https://azure.microsoft.com/resources/templates/sig-create/)
- [在共享的映像库中创建映像定义](https://azure.microsoft.com/resources/templates/sig-image-definition-create/)
- [在共享映像库中创建映像版本](https://azure.microsoft.com/resources/templates/sig-image-version-create/)
