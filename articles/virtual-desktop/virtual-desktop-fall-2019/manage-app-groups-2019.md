---
title: 管理 Azure 虚拟桌面（经典）的应用组 - Azure
description: 了解如何在 Azure Active Directory (AD) 中设置 Azure 虚拟桌面（经典）租户。
author: Heidilohr
ms.topic: tutorial
ms.date: 08/16/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 6fe87fb7fc0cbe9e727fe1539d8424d9ee1fe1b1
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253258"
---
# <a name="tutorial-manage-app-groups-for-azure-virtual-desktop-classic"></a>教程：管理 Azure 虚拟桌面（经典）的应用组

>[!IMPORTANT]
>此内容适用于 Azure 虚拟桌面（经典），后者不支持 Azure 资源管理器 Azure 虚拟桌面对象。 若要尝试管理 Azure 资源管理器 Azure 虚拟桌面对象，请参阅[此文](../manage-app-groups.md)。

为新的 Azure 虚拟桌面主机池创建的默认应用组也会发布完整桌面。 此外，可为主机池创建一个或多个 RemoteApp 应用程序组。 请遵循本教程创建 RemoteApp 应用组并发布单个“开始”  菜单应用。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建 RemoteApp 组。
> * 授予对 RemoteApp 程序的访问权限。

在开始之前，请[下载并导入 Azure 虚拟桌面 PowerShell 模块](/powershell/windows-virtual-desktop/overview/)（如果尚未这样做），以便在 PowerShell 会话中使用。 然后，运行以下 cmdlet 登录到你的帐户：

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="create-a-remoteapp-group"></a>创建 RemoteApp 组

1. 运行以下 PowerShell cmdlet 以创建新的空 RemoteApp 应用组。

   ```powershell
   New-RdsAppGroup -TenantName <tenantname> -HostPoolName <hostpoolname> -Name <appgroupname> -ResourceType "RemoteApp"
   ```

2. （可选）若要验证是否已创建应用组，可运行以下 cmdlet，以查看主机池的所有应用组列表。

   ```powershell
   Get-RdsAppGroup -TenantName <tenantname> -HostPoolName <hostpoolname>
   ```

3. 运行以下 cmdlet，以获取主机池虚拟机映像中的“开始”  菜单应用列表。 记下要发布的应用程序的 **FilePath**、**IconPath**、**IconIndex** 值和其他重要信息。

   ```powershell
   Get-RdsStartMenuApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
   ```

4. 运行以下 cmdlet，以基于其 `AppAlias` 安装应用程序。 运行步骤 3 的输出时，可以看到 `AppAlias`。

   ```powershell
   New-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -Name <remoteappname> -AppAlias <appalias>
   ```

5. （可选）运行以下 cmdlet，将新的 RemoteApp 程序发布到在步骤 1 中创建的应用程序组。

   ```powershell
    New-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -Name <remoteappname> -Filepath <filepath>  -IconPath <iconpath> -IconIndex <iconindex>
   ```

6. 若要验证应用是否已发布，请运行以下 cmdlet。

   ```powershell
    Get-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
   ```

7. 针对要为此应用组发布的每个应用程序重复步骤 1-5。
8. 运行以下 cmdlet，授予用户对应用组中 RemoteApp 程序的访问权限。

   ```powershell
   Add-RdsAppGroupUser -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -UserPrincipalName <userupn>
   ```

## <a name="next-steps"></a>后续步骤

本教程介绍了如何创建应用组、使用 RemoteApps 程序来填充它，以及如何向应用组分配用户。 若要了解如何创建验证主机池，请参阅以下教程。 在将服务更新部署到生产环境之前，可以使用验证主机池来监视服务更新。

> [!div class="nextstepaction"]
> [创建主机池以验证服务更新](create-validation-host-pool-2019.md)
