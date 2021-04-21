---
title: 如何查找租户 ID - Azure Active Directory
description: 有关如何查找现有 Azure 订阅的 Azure Active Directory 租户 ID 的说明。
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
ms.custom: it-pro, devx-track-azurepowershell
ms.collection: M365-identity-device-management
ms.openlocfilehash: cba823775849fdad8407c7bb697a53761e8ccbcd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764344"
---
# <a name="how-to-find-your-azure-active-directory-tenant-id"></a>如何查找 Azure Active Directory 租户 ID

Azure 订阅与 Azure Active Directory (Azure AD) 之间存在信任关系。 订阅信任 Azure AD 对用户、服务和设备执行身份验证。 每个订阅都有一个与之关联的租户 ID，可以通过几种方法查找订阅的租户 ID。

## <a name="find-tenant-id-through-the-azure-portal"></a>通过 Azure 门户查找租户 ID

1. 登录到 [Azure 门户](https://portal.azure.com)。
 
1. 选择“Azure Active Directory” 。

1. 选择“属性”。

1. 然后向下滚动到“租户 ID”字段。 你的租户 ID 将显示在框内。

:::image type="content" source="media/active-directory-how-to-find-tenant/portal-tenant-id.png" alt-text="Azure Active Directory - 属性 - 租户 ID - 租户 ID 字段":::

## <a name="find-tenant-id-with-powershell"></a>通过 PowerShell 查找租户 ID

你还可以以编程方式查找租户。 若要使用 Azure PowerShell 查找租户 ID，请使用 cmdlet `Get-AzTenant`。

```azurepowershell-interactive
Connect-AzAccount
Get-AzTenant
```
   
有关详细信息，请参阅 [Get-AzTenant](/powershell/module/az.accounts/get-aztenant) 的 Azure PowerShell cmdlet 参考。


## <a name="find-tenant-id-with-cli"></a>通过 CLI 查找租户 ID
如果要使用命令行接口查找租户 ID，可以通过 [Azure CLI](/cli/azure/install-azure-cli) 或 [Microsoft 365 CLI](https://pnp.github.io/cli-microsoft365/) 实现。 

对于 Azure CLI，请使用 az login、az account list 或 az account tenant list 命令，如以下示例所示  。 注意每个命令的输出中每个订阅的 tenantId 属性。

```azurecli-interactive
az login
az account list
az account tenant list
```

有关详细信息，请参阅 [az login](/cli/azure/reference-index#az_login) 命令参考、[az account](/cli/azure/ext/account/account) 命令参考或 [az account tenant](/cli/azure/ext/account/account/tenant) 命令参考。


对于 Microsoft 365 CLI，请使用 tenant id 这一 cmdlet，如以下示例所示：
 
```cli
m365 tenant id get
```

有关详细信息，请参阅 Microsoft 365 [tenant id get](https://pnp.github.io/cli-microsoft365/cmd/tenant/id/id-get/) 命令参考。


## <a name="next-steps"></a>后续步骤

- 若要创建新的 Azure AD 租户，请参阅[快速入门：在 Azure Active Directory 中创建新租户](active-directory-access-create-new-tenant.md)。

- 若要了解如何将订阅关联或添加到租户，请参阅[将 Azure 订阅关联或添加到 Azure Active Directory 租户](active-directory-how-subscriptions-associated-directory.md)。

- 若要了解如何查找对象 ID，请参阅[查找用户对象 ID](/partner-center/find-ids-and-domain-names#find-the-user-object-id)。
