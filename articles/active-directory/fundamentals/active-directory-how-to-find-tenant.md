---
title: 如何查找租户 ID-Azure Active Directory
description: 有关如何查找租户 ID 并将其 Azure Active Directory 到现有 Azure 订阅的说明。
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 10/30/2020
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, devx-track-azurecli
ms.collection: M365-identity-device-management
ms.openlocfilehash: 910e68e5b8933875f252faf65fb58e514dee8254
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96326584"
---
# <a name="how-to-find-your-azure-active-directory-tenant-id"></a>如何查找 Azure Active Directory 租户 ID

Azure 订阅与 Azure Active Directory (Azure AD) 具有信任关系。 Azure AD 受信任，可对订阅的用户、服务和设备进行身份验证。 每个订阅都有一个与其关联的租户 ID，可以通过几种方式来查找订阅的租户 ID。

## <a name="find-tenant-id-through-the-azure-portal"></a>通过 Azure 门户查找租户 ID

1. 登录到 [Azure 门户](https://portal.azure.com)。
 
1. 选择“Azure Active Directory” 。

1. 选择“属性”。

1. 然后，向下滚动到 " **租户 ID** " 字段。 租户 ID 会位于框中。

:::image type="content" source="media/active-directory-how-to-find-tenant/portal-tenant-id.png" alt-text="Azure Active Directory-属性-租户 ID-租户 ID 字段":::

## <a name="find-tenant-id-with-powershell"></a>通过 PowerShell 查找租户 ID

还可以通过编程方式查找租户。 若要查找 Azure PowerShell 的租户 ID，请使用 cmdlet `Get-AzTenant` 。

```azurepowershell-interactive
Connect-AzAccount
Get-AzTenant
```
   
有关详细信息，Azure PowerShell 请参阅 [AzTenant](/powershell/module/az.accounts/get-aztenant)的 cmdlet 参考。


## <a name="find-tenant-id-with-cli"></a>通过 CLI 查找租户 ID
如果要使用命令行界面查找租户 ID，可以使用 [Azure CLI](/cli/azure/install-azure-cli) 或 [Microsoft 365 CLI](https://pnp.github.io/cli-microsoft365/)来执行此操作。 

对于 Azure CLI，请使用 **az login**、 **az account list** 或 **az account 租户列表** 之一，如以下示例中所示。 请注意每个命令的输出中每个订阅的 **tenantId** 属性。

```azurecli-interactive
az login
az account list
az account tenant list
```

有关详细信息，请参阅 [az login](/cli/azure/reference-index#az_login) command reference、 [az account](/cli/azure/ext/account/account) command reference 或 [az account 租户](/cli/azure/ext/account/account/tenant) 命令参考。


对于 Microsoft 365 CLI，请使用 cmdlet **租户 id** ，如以下示例中所示：
 
```cli
m365 tenant id get
```

有关详细信息，请参阅 Microsoft 365 [租户 id get](https://pnp.github.io/cli-microsoft365/cmd/tenant/id/id-get/) 命令参考。


## <a name="next-steps"></a>后续步骤

- 若要创建新的 Azure AD 租户，请参阅[快速入门：在 Azure Active Directory 中创建新租户](active-directory-access-create-new-tenant.md)。

- 若要了解如何将订阅关联或添加到租户，请参阅 [将 Azure 订阅关联或添加到 Azure Active Directory 租户](active-directory-how-subscriptions-associated-directory.md)。

- 若要了解如何查找对象 ID，请参阅 [查找用户对象 id](/partner-center/find-ids-and-domain-names#find-the-user-object-id)。