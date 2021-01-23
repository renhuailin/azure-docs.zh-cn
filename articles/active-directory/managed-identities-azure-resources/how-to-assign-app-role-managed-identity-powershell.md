---
title: 使用 PowerShell 向应用程序角色分配托管标识 - Azure AD
description: 分步说明如何使用 PowerShell 将托管标识访问权限分配给另一个应用程序的角色。
services: active-directory
documentationcenter: ''
author: johndowns
manager: ''
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2020
ms.author: jodowns
ms.collection: M365-identity-device-management
ms.openlocfilehash: f2d844054e3744aafa01f43ded354a5b5d9f8b4b
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2021
ms.locfileid: "98732041"
---
# <a name="assign-a-managed-identity-access-to-an-application-role-using-powershell"></a>使用 PowerShell 向应用程序角色分配托管标识访问权限

Azure 资源的托管标识在 Azure Active Directory 中为 Azure 服务提供了一个标识。 它们无需在代码中使用凭据即可工作。 Azure 服务使用此标识向支持 Azure AD 身份验证的服务证明身份。 应用程序角色提供了一种基于角色的访问控制形式，并允许服务实现授权规则。

本文介绍如何使用 Azure AD PowerShell 向另一应用程序公开的应用程序角色分配托管标识。

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>先决条件

- 如果不熟悉 Azure 资源的托管标识，请查阅[概述部分](overview.md)。 请务必了解[系统分配的托管标识与用户分配的托管标识之间的差异](overview.md#managed-identity-types)。
- 如果没有 Azure 帐户，请在继续前[注册免费帐户](https://azure.microsoft.com/free/)。
- 若要运行示例脚本，有两个选项：
    - 使用 " [Azure Cloud Shell](../../cloud-shell/overview.md)"，可以使用代码块右上角的 " **试用** " 按钮打开。
    - 通过安装最新版的 [Azure AD PowerShell](/powershell/azure/active-directory/install-adv2) 在本地运行脚本。

## <a name="assign-a-managed-identity-access-to-another-applications-app-role"></a>将托管标识访问权限分配给另一应用程序的应用角色

1. 在 Azure 资源上启用托管标识，[如 Azure VM](qs-configure-powershell-windows-vm.md)。

1. 查找托管标识的服务主体的对象 ID。

   对于系统分配的托管标识，可在 Azure 门户中资源的“标识”页上找到对象 ID 。 还可以使用以下 PowerShell 脚本来查找对象 ID。 你将需要步骤 1 中所创建资源的资源 ID，可在 Azure 门户中资源的“属性”页上找到该 ID。

    ```powershell
    $resourceIdWithManagedIdentity = '/subscriptions/{my subscription ID}/resourceGroups/{my resource group name}/providers/Microsoft.Compute/virtualMachines/{my virtual machine name}'
    (Get-AzResource -ResourceId $resourceIdWithManagedIdentity).Identity.PrincipalId
    ```

    对于用户分配的托管标识，可在 Azure 门户中资源的“概述”页上找到托管标识的对象 ID 。 还可以使用以下 PowerShell 脚本来查找对象 ID。 你将需要用户分配的托管标识的资源 ID。

    ```powershell
    $userManagedIdentityResourceId = '/subscriptions/{my subscription ID}/resourceGroups/{my resource group name}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{my managed identity name}'
    (Get-AzResource -ResourceId $userManagedIdentityResourceId).Properties.PrincipalId
    ```

1. 创建新的应用程序注册，以表示托管标识要向其发送请求的服务。 如果公开向托管标识授予的应用角色的 API 或服务在 Azure AD 租户中已有服务主体，请跳过此步骤。 例如，如果要授予托管标识对 Microsoft Graph API 的访问权限，则可以跳过此步骤。

1. 查找服务应用程序的服务主体的对象 ID。 可以使用 Azure 门户来查找此 ID。 转到 Azure Active Directory 并打开“企业应用程序”页，然后找到应用程序并查找“对象 ID” 。 还可以使用以下 PowerShell 脚本，按照服务主体的对象 ID 的显示名称找到此 ID：

    ```powershell
    $serverServicePrincipalObjectId = (Get-AzureADServicePrincipal -Filter "DisplayName eq '$applicationName'").ObjectId
    ```

    > [!NOTE]
    > 应用程序的显示名称不是唯一的，因此，应验证是否获取了正确的应用程序服务主体。

1. 将[应用角色](../develop/howto-add-app-roles-in-azure-ad-apps.md)添加到步骤 3 中创建的应用程序。 可以使用 Azure 门户或使用 Microsoft Graph 来创建角色。 例如，可以添加如下应用角色：

    ```json
    {
        "allowedMemberTypes": [
            "Application"
        ],
        "displayName": "Read data from MyApi",
        "id": "0566419e-bb95-4d9d-a4f8-ed9a0f147fa6",
        "isEnabled": true,
        "description": "Allow the application to read data as itself.",
        "value": "MyApi.Read.All"
    }
    ```

1. 将应用角色分配给托管标识。 你将需要以下信息来分配应用角色：
    * `managedIdentityObjectId`：在步骤 2 中找到的托管标识服务主体的对象 ID。
    * `serverServicePrincipalObjectId`：在步骤 4 中找到的服务器应用程序服务主体的对象 ID。
    * `appRoleId`：在步骤 5 中生成的服务器应用公开的应用角色 ID，此示例中的应用角色 ID 为 `0566419e-bb95-4d9d-a4f8-ed9a0f147fa6`。
   
   执行以下 PowerShell 脚本以添加角色分配：

    ```powershell
    New-AzureADServiceAppRoleAssignment -ObjectId $managedIdentityObjectId -Id $appRoleId -PrincipalId $managedIdentityObjectId -ResourceId $serverServicePrincipalObjectId
    ```

## <a name="complete-script"></a>完整脚本

此示例脚本演示如何将 Azure Web 应用的托管标识分配给应用角色。

```powershell
# Install the module. (You need admin on the machine.)
# Install-Module AzureAD

# Your tenant ID (in the Azure portal, under Azure Active Directory > Overview).
$tenantID = '<tenant-id>'

# The name of your web app, which has a managed identity that should be assigned to the server app's app role.
$webAppName = '<web-app-name>'
$resourceGroupName = '<resource-group-name-containing-web-app>'

# The name of the server app that exposes the app role.
$serverApplicationName = '<server-application-name>' # For example, MyApi

# The name of the app role that the managed identity should be assigned to.
$appRoleName = '<app-role-name>' # For example, MyApi.Read.All

# Look up the web app's managed identity's object ID.
$managedIdentityObjectId = (Get-AzWebApp -ResourceGroupName $resourceGroupName -Name $webAppName).identity.principalid

Connect-AzureAD -TenantId $tenantID

# Look up the details about the server app's service principal and app role.
$serverServicePrincipal = (Get-AzureADServicePrincipal -Filter "DisplayName eq '$serverApplicationName'")
$serverServicePrincipalObjectId = $serverServicePrincipal.ObjectId
$appRoleId = ($serverServicePrincipal.AppRoles | Where-Object {$_.Value -eq $appRoleName }).Id

# Assign the managed identity access to the app role.
New-AzureADServiceAppRoleAssignment `
    -ObjectId $managedIdentityObjectId `
    -Id $appRoleId `
    -PrincipalId $managedIdentityObjectId `
    -ResourceId $serverServicePrincipalObjectId
```

## <a name="next-steps"></a>后续步骤

- [Azure 资源托管标识概述](overview.md)
- 若要启用 Azure VM 上的托管标识，请参阅[使用 PowerShell 在 VM 上配置 Azure 资源的托管标识](qs-configure-powershell-windows-vm.md)。