---
title: Azure 虚拟桌面（经典）服务主体角色分配 - Azure
description: 了解如何使用 PowerShell 在 Azure 虚拟桌面（经典）中创建服务主体并分配角色。
author: Heidilohr
ms.topic: tutorial
ms.date: 05/27/2020
ms.author: helohr
ms.custom: devx-track-azurepowershell
manager: femila
ms.openlocfilehash: eacb82408c2981375e599cf29c67b016ba0f3b41
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2021
ms.locfileid: "111749842"
---
# <a name="tutorial-create-service-principals-and-role-assignments-with-powershell-in-azure-virtual-desktop-classic"></a>教程：使用 PowerShell 在 Azure 虚拟桌面（经典）中创建服务主体和角色分配

>[!IMPORTANT]
>此内容适用于 Azure 虚拟桌面（经典），后者不支持 Azure 资源管理器 Azure 虚拟桌面对象。

服务主体是可以在 Azure Active Directory 中创建的、用于根据特定目的分配角色和权限的标识。 在 Azure 虚拟桌面中，可以创建服务主体来实现以下目的：

- 自动执行特定的 Azure 虚拟桌面管理任务。
- 运行任何 Azure 虚拟桌面的 Azure 资源管理器模板时，将服务主体用作凭据，而无需让用户执行 MFA。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 在 Azure Active Directory 中创建服务主体。
> * 在 Azure 虚拟桌面中创建角色分配。
> * 使用服务主体登录到 Azure 虚拟桌面。

## <a name="prerequisites"></a>先决条件

在创建服务主体和角色分配之前，需要先完成三项操作：

1. 安装 AzureAD 模块。 若要安装该模块，请以管理员身份运行 PowerShell，然后运行以下 cmdlet：

    ```powershell
    Install-Module AzureAD
    ```

2. [下载并导入 Azure 虚拟桌面 PowerShell 模块](/powershell/windows-virtual-desktop/overview/)。

3. 在同一 PowerShell 会话中，遵照本文中的所有说明操作。 如果通过关闭窗口并稍后重新打开该窗口来中断 PowerShell 会话，则该过程可能无法运行。

## <a name="create-a-service-principal-in-azure-active-directory"></a>在 Azure Active Directory 中创建服务主体

在满足 PowerShell 会话中的先决条件之后，运行以下 PowerShell cmdlet，以在 Azure 中创建多租户服务主体。

```powershell
Import-Module AzureAD
$aadContext = Connect-AzureAD
$svcPrincipal = New-AzureADApplication -AvailableToOtherTenants $true -DisplayName "Windows Virtual Desktop Svc Principal"
$svcPrincipalCreds = New-AzureADApplicationPasswordCredential -ObjectId $svcPrincipal.ObjectId
```
## <a name="view-your-credentials-in-powershell"></a>在 PowerShell 中查看凭据

在为服务主体创建角色分配之前，请查看凭据并将其记下以供将来参考。 密码特别重要，因为关闭此 PowerShell 会话后将无法检索密码。

下面是应该记下的三个凭据，以及用于获取这些凭据的 cmdlet：

- 密码：

    ```powershell
    $svcPrincipalCreds.Value
    ```

- 租户 ID：

    ```powershell
    $aadContext.TenantId.Guid
    ```

- 应用程序 ID：

    ```powershell
    $svcPrincipal.AppId
    ```

## <a name="create-a-role-assignment-in-azure-virtual-desktop"></a>在 Azure 虚拟桌面中创建角色分配

接下来，需要创建角色分配，使服务主体可以登录到 Azure 虚拟桌面。 请确保使用有权创建角色分配的帐户登录。

首先[下载并导入 Azure 虚拟桌面 PowerShell 模块](/powershell/windows-virtual-desktop/overview/)（如果尚未这样做），方便在 PowerShell 会话中使用。

运行以下 PowerShell cmdlet 以连接到 Azure 虚拟桌面，并显示你的租户。

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Get-RdsTenant
```

找到要为其创建角色分配的租户的租户名称时，请在以下 cmdlet 中使用该名称：

```powershell
$myTenantName = "<Azure Virtual Desktop Tenant Name>"
New-RdsRoleAssignment -RoleDefinitionName "RDS Owner" -ApplicationId $svcPrincipal.AppId -TenantName $myTenantName
```

## <a name="sign-in-with-the-service-principal"></a>使用服务主体登录

为服务主体创建角色分配后，请通过运行以下 cmdlet，确保该服务主体可以登录到 Azure 虚拟桌面：

```powershell
$creds = New-Object System.Management.Automation.PSCredential($svcPrincipal.AppId, (ConvertTo-SecureString $svcPrincipalCreds.Value -AsPlainText -Force))
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com" -Credential $creds -ServicePrincipal -AadTenantId $aadContext.TenantId.Guid
```

登录后，请使用该服务主体测试几个 Azure 虚拟桌面 PowerShell cmdlet，确保一切正常工作。

## <a name="next-steps"></a>后续步骤

创建了服务主体并在 Azure 虚拟桌面租户中为其分配角色后，可以用它来创建主机池。 若要详细了解主机池，请继续学习有关在 Azure 虚拟桌面中创建主机池的教程。

 > [!div class="nextstepaction"]
 > [通过 Azure 市场创建主机池](create-host-pools-azure-marketplace-2019.md)
