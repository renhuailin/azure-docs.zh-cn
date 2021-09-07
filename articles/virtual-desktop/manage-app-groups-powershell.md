---
title: 管理 Azure 虚拟桌面的应用组 - Azure
description: 如何使用 PowerShell 或 Azure CLI 管理 Azure 虚拟桌面应用组。
author: Heidilohr
ms.topic: how-to
ms.date: 07/23/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: f323819492fe89f7742c6b218afa4d2e1bf1b6c0
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123103924"
---
# <a name="manage-app-groups-using-powershell-or-the-azure-cli"></a>使用 PowerShell 或 Azure CLI 管理应用组

>[!IMPORTANT]
>本教程的内容适用于包含 Azure 资源管理器 Azure 虚拟桌面对象的 Azure 虚拟桌面。 如果你使用的是不包含 Azure 资源管理器对象的 Azure 虚拟桌面（经典），请参阅[此文](./virtual-desktop-fall-2019/manage-app-groups-2019.md)。

为新的 Azure 虚拟桌面主机池创建的默认应用组也会发布完整桌面。 此外，可为主机池创建一个或多个 RemoteApp 应用程序组。 请遵循本教程创建 RemoteApp 应用组并发布单个“开始”  菜单应用。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建 RemoteApp 组。
> * 授予对 RemoteApp 程序的访问权限。

## <a name="prerequisites"></a>先决条件

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

本文假定你已按照[设置 PowerShell 模块](powershell-module.md)中的说明设置 PowerShell 模块并登录到 Azure 帐户。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

本文假设你已经为 Azure CLI 设置了环境，并且已经登录到 Azure 帐户。

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

---

## <a name="create-a-remoteapp-group"></a>创建 RemoteApp 组

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

若要使用 PowerShell 创建 RemoteApp 组，请执行以下操作：

1. 运行以下 PowerShell cmdlet 以创建新的空 RemoteApp 应用组。

   ```powershell
   New-AzWvdApplicationGroup -Name <appgroupname> -ResourceGroupName <resourcegroupname> -ApplicationGroupType "RemoteApp" -HostPoolArmPath '/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName/providers/Microsoft.DesktopVirtualization/hostPools/HostPoolName'-Location <azureregion>
   ```

2. （可选）若要验证是否已创建应用组，可运行以下 cmdlet，以查看主机池的所有应用组列表。

   ```powershell
   Get-AzWvdApplicationGroup -Name <appgroupname> -ResourceGroupName <resourcegroupname>
   ```

3. 运行以下 cmdlet，以获取主机池虚拟机映像中的“开始”  菜单应用列表。 记下要发布的应用程序的 **FilePath**、**IconPath**、**IconIndex** 值和其他重要信息。

   ```powershell
   Get-AzWvdStartMenuItem -ApplicationGroupName <appgroupname> -ResourceGroupName <resourcegroupname> | Format-List | more
   ```

   输出应以如下格式显示所有“开始”菜单项：

   ```powershell
   AppAlias            : access
   CommandLineArgument :
   FilePath            : C:\Program Files\Microsoft Office\root\Office16\MSACCESS.EXE
   FriendlyName        :
   IconIndex           : 0
   IconPath            : C:\Program Files\Microsoft Office\Root\VFS\Windows\Installer\{90160000-000F-0000-1000-0000000FF1CE}\accicons.exe
   Id                  : /subscriptions/resourcegroups/providers/Microsoft.DesktopVirtualization/applicationgroups/startmenuitems/Access
   Name                : 0301RAG/Access
   Type                : Microsoft.DesktopVirtualization/applicationgroups/startmenuitems

   AppAlias            : charactermap
   CommandLineArgument :
   FilePath            : C:\windows\system32\charmap.exe
   FriendlyName        :
   IconIndex           : 0
   IconPath            : C:\windows\system32\charmap.exe
   Id                  : /subscriptions/resourcegroups/providers/Microsoft.DesktopVirtualization/applicationgroups/startmenuitems/Character Map
   Name                : 0301RAG/Character Map
   Type                : Microsoft.DesktopVirtualization/applicationgroups/startmenuitems
   ```

4. 运行以下 cmdlet，以基于其 `AppAlias` 安装应用程序。 运行步骤 3 的输出时，可以看到 `AppAlias`。

   ```powershell
   New-AzWvdApplication -AppAlias <appalias> -GroupName <appgroupname> -Name <remoteappname> -ResourceGroupName <resourcegroupname> -CommandLineSetting <DoNotAllow|Allow|Require>
   ```

5. （可选）运行以下 cmdlet，将新的 RemoteApp 程序发布到在步骤 1 中创建的应用程序组。

   ```powershell
   New-AzWvdApplication -GroupName <appgroupname> -Name <remoteappname> -ResourceGroupName <resourcegroupname> -Filepath <filepath> -IconPath <iconpath> -IconIndex <iconindex> -CommandLineSetting <DoNotAllow|Allow|Require>
   ```

6. 若要验证应用是否已发布，请运行以下 cmdlet。

   ```powershell
   Get-AzWvdApplication -GroupName <appgroupname> -ResourceGroupName <resourcegroupname>
   ```

7. 针对要为此应用组发布的每个应用程序重复步骤 1-5。
8. 运行以下 cmdlet，授予用户对应用组中 RemoteApp 程序的访问权限。

   ```powershell
   New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
   ```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!NOTE]
> Azure CLI 目前不提供用于获取开始菜单应用的命令，也不提供用于创建新的 RemoteApp 程序或将其发布到应用程序组的命令。 使用 Azure PowerShell。

若要使用 Azure CLI 创建 RemoteApp 组，请执行以下操作：

1. 使用 [az desktopvirtualization applicationgroup create](/cli/azure/desktopvirtualization##az_desktopvirtualization_applicationgroup_create) 命令创建新的远程应用程序组：

   ```azurecli
   az desktopvirtualization applicationgroup create --name "MyApplicationGroup" \
      --resource-group "MyResourceGroup" \
      --location "MyLocation" \
      --application-group-type "RemoteApp" \
      --host-pool-arm-path "/subscriptions/MySubscriptionGUID/resourceGroups/MyResourceGroup/providers/Microsoft.DesktopVirtualization/hostpools/MyHostPool"
      --tags tag1="value1" tag2="value2" \
      --friendly-name "Friendly name of this application group" \
      --description "Description of this application group" 
   ```
    
2. （可选）若要验证是否已创建应用组，可运行以下命令，以查看主机池的所有应用组的列表。

   ```azurecli
   az desktopvirtualization applicationgroup list \
      --resource-group "MyResourceGroup"
   ```
---

## <a name="next-steps"></a>后续步骤

如果你通过我们的教程阅读了此操作指南，请查看[创建主机池以验证服务更新](create-validation-host-pool.md)。 在将服务更新部署到生产环境之前，可以使用验证主机池来监视服务更新。