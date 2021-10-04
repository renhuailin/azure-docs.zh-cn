---
title: 使用 Azure 凭据将合作伙伴 ID 链接到 Power Apps 帐户
description: 本文可帮助 Microsoft 合作伙伴使用其 Azure 凭据来协助客户使用 Microsoft Power Apps。
author: bandersmsft
ms.reviewer: akangaw
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: banders
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c7bc61691f0035c9f1aa0361ae3439cfb1e1485e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128604391"
---
# <a name="link-a-partner-id-to-your-power-apps-accounts"></a>将合作伙伴 ID 链接到 Power Apps 帐户

本文将帮助 Microsoft 合作伙伴（即 Power Apps 服务提供商）将其服务关联到 Microsoft Power Apps 上的客户。 当你（Microsoft 合作伙伴）为客户管理、配置和支持 Power Apps 服务时，你可以访问客户的环境。 你可以使用 Azure 凭据和合作伙伴管理链接 (PAL) 将合作伙伴网络 ID 与用于服务交付的帐户凭据相关联。

通过 PAL，Microsoft 可以识别拥有 Power Apps 客户的合作伙伴。 Microsoft 根据帐户的权限（Power Apps 角色）和范围（租户、资源组、资源）将使用情况归结于合作伙伴组织。

## <a name="get-access-from-your-customer"></a>从客户获取访问权限

在你链接合作伙伴 ID 之前，客户必须使用以下选项之一，授权你访问其 Power Apps 资源：

- **来宾用户** - 客户可将你添加为来宾用户并分配任何 Power Apps 角色。 有关详细信息，请参阅[添加另一个目录中的来宾用户](../../active-directory/external-identities/what-is-b2b.md)。
- **目录帐户** - 客户可以为你在其自己的目录中创建用户帐户，并分配任何 Power Apps 角色。
- **服务主体** - 客户可在其目录中为你的组织添加一个应用或脚本，并分配任何 Power Apps 角色。 该应用或脚本的标识称为服务主体。
- **委派的管理员** - 客户可以委派一个资源组，以便用户可以通过你的租户在其上工作。 有关详细信息，请参阅[合作伙伴：委派的管理员](/power-platform/admin/for-partners-delegated-administrator)。

## <a name="link-customer-to-a-partner-id"></a>将客户链接到合作伙伴 ID

获取客户资源的访问权限后，请使用 Azure 门户、PowerShell 或 Azure CLI 将 Microsoft 合作伙伴网络 ID (MPN ID) 链接到用户 ID 或服务主体。 将合作伙伴 ID 链接到每个客户租户。

### <a name="use-the-azure-portal-to-link-to-a-new-partner-id"></a>使用 Azure 门户链接新合作伙伴 ID

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 可以在 Azure 门户中[链接到合作伙伴 ID](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade)。
1. 输入组织的 [Microsoft 合作伙伴网络](https://partner.microsoft.com/) ID。 请务必使用合作伙伴中心配置文件中显示的“关联 MPN ID”。 这通常称为[合作伙伴位置帐户 MPN ID](/partner-center/account-structure)。  
    :::image type="content" source="./media/link-partner-id-power-apps-accounts/link-partner-id.png" alt-text="显示指向合作伙伴 ID 窗口的链接的屏幕截图。" lightbox="./media/link-partner-id-power-apps-accounts/link-partner-id.png" :::
1. 要将合作伙伴 ID 链接到另一个客户，请切换目录。 在“切换目录”下，选择适当的目录。  
    :::image type="content" source="./media/link-partner-id-power-apps-accounts/switch-directory.png" alt-text="显示可在其中切换目录的“目录 + 订阅”窗口的屏幕截图。" lightbox="./media/link-partner-id-power-apps-accounts/switch-directory.png" :::

### <a name="use-powershell-to-link-to-a-new-partner-id"></a>使用 PowerShell 链接到新合作伙伴 ID

安装 [Az.ManagementPartner](https://www.powershellgallery.com/packages/Az.ManagementPartner/) Azure PowerShell 模块。

使用用户帐户或服务主体登录到客户的租户。 有关详细信息，请参阅[使用 PowerShell 登录](/powershell/azure/authenticate-azureps)。

```azurepowershell-interactive
Update-AzManagementPartner -PartnerId 12345
```

链接到新合作伙伴 ID。 合作伙伴 ID 是组织的 [Microsoft 合作伙伴网络](https://partner.microsoft.com/) ID。 请务必使用合作伙伴配置文件中显示的“关联 MPN ID”。

```azurepowershell-interactive
new-AzManagementPartner -PartnerId 12345
```

获取链接的合作伙伴 ID

```azurepowershell-interactive
get-AzManagementPartner
```

更新链接的合作伙伴 ID

```azurepowershell-interactive
Update-AzManagementPartner -PartnerId 12345
```

删除链接的合作伙伴 ID

```azurepowershell-interactive
remove-AzManagementPartner -PartnerId 12345
```

### <a name="use-the-azure-cli-to-link-to-a-new-partner-id"></a>使用 Azure CLI 链接到新合作伙伴 ID

首先，安装 Azure CLI 扩展。

```azurecli-interactive
az extension add --name managementpartner
```

使用用户帐户或服务主体登录到客户的租户。 有关详细信息，请参阅[使用 Azure CLI 登录](/cli/azure/authenticate-azure-cli)。

```azurecli-interactive
az login --tenant TenantName
```

链接到新合作伙伴 ID。 合作伙伴 ID 是组织的 [Microsoft 合作伙伴网络](https://partner.microsoft.com/) ID。

```azurecli-interactive
az managementpartner create --partner-id 12345
```

获取链接的合作伙伴 ID

```azurecli-interactive
az managementpartner show
```

更新链接的合作伙伴 ID

```azurecli-interactive
az managementpartner update --partner-id 12345
```

删除链接的合作伙伴 ID

```azurecli-interactive
az managementpartner delete --partner-id 12345
```

### <a name="next-steps"></a>后续步骤

- 阅读[成本管理 + 计费常见问题解答](../cost-management-billing-faq.yml)，了解关于将合作伙伴 ID 链接到 Power Apps 账户的问题及解答。
- 加入 [Microsoft 合作伙伴社区](https://aka.ms/PALdiscussion)中的讨论，以接收更新或发送反馈。
- 阅读[低代码应用程序开发高级专业化常见问题解答](https://assetsprod.microsoft.com/mpn/faq-low-code-app-development-advanced-specialization.pdf)，了解适用于低代码应用程序开发高级专业化的基于 PAL 的 Power Apps 关联。
