---
title: 管理 Azure 自动化运行方式帐户
description: 本文介绍如何使用 PowerShell 或 Azure 门户管理 Azure 自动化运行方式帐户。
services: automation
ms.subservice: ''
ms.date: 05/17/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d2d615df07e89e1fc2d4e63066d320002718d200
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110059673"
---
# <a name="manage-an-azure-automation-run-as-account"></a>管理 Azure 自动化运行方式帐户

Azure 自动化中的运行方式帐户提供身份验证，以使用自动化 runbook 和其他自动化功能管理 Azure 资源管理器或 Azure 经典部署模型上的资源。 

本文介绍如何管理运行方式或经典运行方式帐户，包括：

   * 如何续订自签名证书
   * 如何从企业或第三方证书颁发机构 (CA) 续订证书
   * 管理运行方式帐户的权限

若要详细了解 Azure 自动化帐户身份验证以及有关流程自动化方案的指导，请参阅[自动化帐户身份验证概述](automation-security-overview.md)。

## <a name="renew-a-self-signed-certificate"></a><a name="cert-renewal"></a>续订自签名证书

为运行方式帐户创建的自签名证书自创建日期算起的一年后过期。 在运行方式帐户过期之前的某个时间点，必须续订证书。 可以在该证书过期之前的任何时间续订。

续订自签名证书时，将保留当前的有效证书，以确保已排队等候或正在主动运行且使用运行方式帐户进行身份验证的任何 Runbook 不会受到负面影响。 该证书在过期之前将保持有效。

>[!NOTE]
>如果认为运行方式帐户已遭到入侵，可以删除该自签名证书然后重新创建。

>[!NOTE]
>如果已将运行方式帐户配置为使用企业或第三方 CA 颁发的证书，并且使用此选项来续订自签名证书选项，该企业证书会被自签名证书替换。 若要在这种情况下续订证书，请参阅[续订企业或第三方证书](#renew-an-enterprise-or-third-party-certificate)。

使用以下步骤来续订自签名证书。

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 转到自动化帐户，然后在帐户设置部分中选择“运行方式帐户”。

    :::image type="content" source="media/manage-runas-account/automation-account-properties-pane.png" alt-text="自动化帐户属性窗格。":::

1. 在“运行方式帐户”属性页上选择“运行方式帐户”或“经典运行方式帐户”，具体取决于你需要为其续订证书的帐户  。

1. 在所选帐户的属性页中，选择“续订证书” 。

    :::image type="content" source="media/manage-runas-account/automation-account-renew-runas-certificate.png" alt-text="续订运行方式帐户的证书。":::

1. 证书续订过程中，可以在菜单的“通知”下面跟踪进度。

## <a name="renew-an-enterprise-or-third-party-certificate"></a>续订企业或第三方证书

每个证书都有一个内置的到期日期。 如果分配给运行方式帐户的证书是由证书颁发机构 (CA) 颁发的，则需要执行一组不同的步骤，在运行方式帐户证书过期之前为其配置新证书。 可以在该证书过期之前的任何时间续订。

1. 按照[创建新证书](./shared-resources/certificates.md#create-a-new-certificate)的步骤导入续订的证书。 自动化要求证书具有以下配置：

   * 指定提供程序：Microsoft 增强型 RSA 和 AES 加密提供程序
   * 标记为可导出
   * 配置为使用 SHA256 算法
   * 以 `*.pfx` 或 `*.cer` 格式保存。 

   导入证书后，请记下或复制证书 Thumbprint 值。 此值用于使用新证书更新运行方式连接属性。 

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 搜索并选择“自动化帐户”。

1. 在“自动化帐户”页上，从列表中选择你的自动化帐户。

1. 在左窗格中，选择“连接”。

1. 在“连接”页上，选择“AzureRunAsConnection”，然后使用新的证书指纹更新证书指纹。

1. 选择“保存”，以提交更改。

## <a name="grant-run-as-account-permissions-in-other-subscriptions"></a>授予运行方式帐户在其他订阅中的权限

Azure 自动化支持在一个订阅中使用单个自动化帐户，并针对多个订阅中的 Azure 资源管理器资源执行 runbook。 此配置不支持 Azure 经典部署模型。

请为运行方式帐户服务主体分配另一个订阅中的[参与者](../role-based-access-control/built-in-roles.md#contributor)角色或限制性更强的权限。 有关详细信息，请参阅 Azure 自动化中[基于角色的访问控制](automation-role-based-access-control.md)。 若要将运行方式帐户分配给另一个订阅中的角色，执行此任务的用户帐户必须是该订阅中“所有者”角色的成员。

> [!NOTE]
> 此配置仅支持某个使用常见 Azure AD 租户的组织的多个订阅。

在授予运行方式帐户权限之前，需要先记下要分配的服务主体的显示名称。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在“自动化帐户”的“帐户设置”下，选择“运行方式帐户” 。
1. 选择“Azure 运行方式帐户”。
1. 复制或记下“Azure 运行方式帐户”页上的“显示名称”的值。

有关如何添加角色分配的详细步骤，请查阅以下文章（具体取决于你想要使用的方法）。

* [使用 Azure 门户分配 Azure 角色](../role-based-access-control/role-assignments-portal.md)
* [使用 Azure PowerShell 分配 Azure 角色](../role-based-access-control/role-assignments-powershell.md)
* [使用 Azure CLI 分配 Azure 角色](../role-based-access-control/role-assignments-cli.md)
* [使用 REST API 分配 Azure 角色](..//role-based-access-control/role-assignments-rest.md)

将运行方式帐户分配给角色后，在 runbook 中指定 `Set-AzContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"` 以设置要使用的订阅上下文。 有关详细信息，请参阅 [Set-AzContext](/powershell/module/az.accounts/set-azcontext)。

## <a name="limit-run-as-account-permissions"></a>限制运行方式帐户权限

若要针对 Azure 中的资源控制自动化的目标，可以运行 [Update-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug8) 脚本。 此脚本将更改现有运行方式帐户服务主体，以创建和使用自定义角色定义。 该角色具有除了 [Key Vault](../key-vault/index.yml) 之外的所有资源的权限。

>[!IMPORTANT]
>运行 Update-AutomationRunAsAccountRoleAssignments.ps1 脚本后，通过使用运行方式帐户访问 Key Vault 的 Runbook 将不再工作。 在运行该脚本之前，应查看帐户中的 Runbook，以便调用 Azure Key Vault。 若要实现从 Azure 自动化 Runbook 对 Key Vault 的访问，必须[将运行方式帐户添加到 Key Vault 的权限](#add-permissions-to-key-vault)。

如需进一步限制运行方式服务主体可执行的操作，可将其他资源类型添加到自定义角色定义的 `NotActions` 元素。 下面的示例限制对 `Microsoft.Compute/*` 的访问。 如果将此资源类型添加到角色定义的 `NotActions`，则该角色将不能访问任何计算资源。 若要详细了解角色定义，请参阅[了解 Azure 资源的角色定义](../role-based-access-control/role-definitions.md)。

```powershell
$roleDefinition = Get-AzRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzRoleDefinition
```

可确定向运行方式帐户使用的服务主体分配了“参与者”角色还是自定义角色。

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 转到自动化帐户，然后在帐户设置部分中选择“运行方式帐户”。
1. 选择“Azure 运行方式帐户”。
1. 选择“角色”以查找正在使用的角色定义。

:::image type="content" source="media/manage-runas-account/verify-role.png" alt-text="验证运行方式帐户角色。" lightbox="media/manage-runas-account/verify-role-expanded.png":::

还可以为多个订阅或自动化帐户确定运行方式帐户使用的角色定义。 为此，请使用 PowerShell 库中的 [Check-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug5) 脚本。

### <a name="add-permissions-to-key-vault"></a>添加 Key Vault 权限

可以允许 Azure 自动化验证 Key Vault 和运行方式帐户服务主体是否正在使用自定义角色定义。 必须具备以下条件：

* 授予 Key Vault 权限。
* 设置访问策略。

可使用 PowerShell 库中的 [Extend-AutomationRunAsAccountRoleAssignmentToKeyVault.ps1](https://aka.ms/AA5hugb) 脚本向运行方式帐户授予对 Key Vault 的权限。 有关设置对 Key Vault 的权限的详细信息，请参阅[分配 Key Vault 访问策略](../key-vault/general/assign-access-policy-powershell.md)。

## <a name="resolve-misconfiguration-issues-for-run-as-accounts"></a>解决运行方式帐户的错误配置问题

在初始设置期间，运行方式帐户或经典运行方式帐户所需的某些配置项可能已被删除或未正确创建。 错误配置的实例可能包括：

* 证书资产
* 连接资产
* 已从参与者角色中删除的运行方式帐户
* Azure AD 中的服务主体或应用程序

对于此类错误配置实例，自动化帐户将检测更改，并在该帐户的“运行方式帐户”属性窗格中显示“不完整”状态。

:::image type="content" source="media/manage-runas-account/automation-account-runas-config-incomplete.png" alt-text="运行方式帐户的配置不完整。":::

选择该运行方式帐户时，该帐户的“属性”窗格中会显示以下错误消息：

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

可先[删除](delete-run-as-account.md)再[重新创建](create-run-as-account.md)运行方式帐户，来快速解决这些运行方式帐户问题。

## <a name="next-steps"></a>后续步骤

* [应用程序对象和服务主体对象](../active-directory/develop/app-objects-and-service-principals.md)。
* [Azure 云服务证书概述](../cloud-services/cloud-services-certs-create.md)。
* 若要创建或重新创建运行方式帐户，请参阅[创建运行方式帐户](create-run-as-account.md)。
* 如果无需再使用运行方式帐户，请参阅[删除运行方式帐户](delete-run-as-account.md)。
