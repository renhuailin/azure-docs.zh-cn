---
title: 从 Azure 虚拟桌面（经典）（预览版）自动迁移 - Azure
description: 如何使用迁移模块从 Azure 虚拟桌面（经典）自动迁移到 Azure 虚拟桌面。
author: Heidilohr
ms.topic: how-to
ms.date: 08/31/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 856b5c08176573640876cab1fa34fe6f33cc8a18
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123439786"
---
# <a name="migrate-automatically-from-azure-virtual-desktop-classic-preview"></a>从 Azure 虚拟桌面（经典）（预览版）自动迁移

> [!IMPORTANT]
> Azure 虚拟桌面的迁移模块工具目前为公共预览版。
> 有关 beta 版本、预览版或尚未正式发布的版本的 Azure 功能所适用的法律条款，请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

使用迁移模块工具（预览版）可将组织从 Azure 虚拟桌面（经典）自动迁移到 Azure 虚拟桌面。 本文将介绍如何使用该工具。 

## <a name="requirements"></a>要求

在使用迁移模块之前，请确保已准备好以下各项：

- 一个 Azure 订阅，你将在其中创建新的 Azure 服务对象。

- 你必须分配有参与者角色，可在订阅上创建 Azure 对象；还必须分配有用户访问管理员角色，可将用户分配到应用程序组。

- 至少对某个 RDS 租户或要迁移的特定主机池拥有远程桌面服务 (RDS) 参与者权限。

- 最新版本的 Microsoft.RdInfra.RDPowershell PowerShell 模块 

- 最新版本的 Az.DesktopVirtualization PowerShell 模块 

- 最新版本的 Az.Resources PowerShell 模块 

- 在计算机中安装迁移模块

- 用于运行本文中所述脚本的 PowerShell 或 PowerShell ISE。 Microsoft.RdInfra.RDPowershell 模块在 PowerShell Core 中不能正常工作。

>[!IMPORTANT]
>迁移过程只会在美国地理区域中创建服务对象。 如果你尝试将服务对象迁移到另一个地理区域，则迁移将无法正常进行。 此外，如果 Azure 虚拟桌面（经典）部署中有 200 个以上的应用组，则无法迁移。 只有在重建环境以减少 Azure Active Directory (Azure AD) 租户中的应用组数量之后，才能进行迁移。

## <a name="prepare-your-powershell-environment"></a>准备 PowerShell 环境

首先，需要为迁移过程准备 PowerShell 环境。

若要准备 PowerShell 环境，请执行以下操作：

1. 在开始之前，请运行以下 cmdlet，以确保使用最新版本的 Az.Desktop Virtualization 和 Az.Resources 模块：

    ```powershell
    Get-Module Az.Resources
    Get-Module Az.DesktopVirtualization
    https://www.powershellgallery.com/packages/Az.DesktopVirtualization/
    https://www.powershellgallery.com/packages/Az.Resources/
    ```

    如果没有，请运行以下 cmdlet 安装和导入这些模块：

    ```powershell
    Install-module Az.Resources
    Import-module Az.Resources
    Install-module Az.DesktopVirtualization
    Import-module Az.DesktopVirtualization
    ```

2. 接下来，运行以下 cmdlet 卸载当前 RDInfra PowerShell 模块：

    ```powershell
    Uninstall-Module -Name Microsoft.RDInfra.RDPowershell -AllVersions
    ```

3. 然后，使用以下 cmdlet 安装 RDPowershell 模块：

    ```powershell
    Install-Module -Name Microsoft.RDInfra.RDPowershell -RequiredVersion 1.0.3414.0 -force
    Import-module Microsoft.RDInfra.RDPowershell
    ```

4. 安装完所有组件后，运行以下 cmdlet 以确保你拥有正确的模块版本：

    ```powershell
    Get-Module Microsoft.RDInfra.RDPowershell
    ```

5. 现在，运行以下 cmdlet 来安装和导入迁移模块：

    ```powershell
    Install-Module -Name PackageManagement -Repository PSGallery -Force
    Install-Module -Name PowerShellGet -Repository PSGallery -Force
    # Then restart shell
    Install-Module -Name Microsoft.RdInfra.RDPowershell.Migration -RequiredVersion 1.0.3595-Prerelease -AllowPrerelease
    Import-Module <Full path to the location of the migration module>\Microsoft.RdInfra.RDPowershell.Migration.psd1
    ```

6. 完成后，在 PowerShell 窗口中登录到 Windows 虚拟桌面（经典）：

    ```powershell
    Add-RdsAccount -DeploymentUrl https://rdbroker.wvd.microsoft.com
    ```

7. 登录到 Azure 资源管理器：

    ```powershell
    Login-AzAccount
    ```

8. 如果你有多个订阅，请使用以下 cmdlet 选择要将资源迁移到的订阅：

    ```powershell
    Select-AzSubscription -Subscriptionid <subID>
    ```

9. 在 Azure 门户中为所选订阅注册资源提供程序。

10. 最后，需要注册该提供程序。 可以采用两种方法执行此操作：
    - 若要使用 PowerShell，请运行以下 cmdlet：
       
       ```powershell
       Register-AzResourceProvider -ProviderNamespace Microsoft.DesktopVirtualization
       ```
    
    - 如果你偏好使用 Azure 门户，请打开 Azure 门户并登录，然后转到“订阅”并选择要使用的订阅的名称。 然后，转到“资源提供程序” > “Microsoft.DesktopVirtualization”并选择“重新注册”  。 UI 中暂时不会有任何变化，但 PowerShell 环境现在应已准备好运行该模块。

## <a name="migrate-azure-virtual-desktop-classic-resources-to-azure-resource-manager"></a>将 Azure 虚拟桌面（经典）资源迁移到 Azure 资源管理器

PowerShell 环境现已准备就绪，你可以开始执行迁移过程了。

若要将 Azure 虚拟桌面（经典）资源迁移到 Azure 资源管理器，请执行以下操作：

1. 在迁移之前，若要了解现有经典资源将如何映射到新的 Azure 资源管理器资源，请运行以下 cmdlet：
    
    ```powershell
    Get-RdsHostPoolMigrationMapping
    ```

    使用 Get-RdsHostPoolMigrationMapping 可以创建一个 CSV 文件来映射资源将要去往的位置。 例如，如果租户名称是“Contoso”，而你想要将映射文件存储在“contosouser”文件中，则可以运行如下所示的 cmdlet：

    ```powershell
    Get-RdsHostPoolMigrationMapping -Tenant Contoso -HostPool Office -Location EastUS -OutputFile 'C:\\Users\contosouser\OneDrive - Microsoft\Desktop\mapping.csv'
    ```

2. 接下来，运行 Start-RdsHostPoolMigration cmdlet 以选择是要迁移租户中的单个主机池还是所有主机池。

    例如：

    ```powershell
    Start-RdsHostPoolMigration -Tenant Contoso -Location WestUS
    ```

    若要将资源迁移到特定的主机池，请包含该主机池的名称。 例如，若要移动名为“Office”的主机池，请运行如下所示的命令：

    ```powershell
    Start-RdsHostPoolMigration -Tenant Contoso -HostPool Office -CopyUserAssignments $false -Location EastUS
    ```

    如果未指定工作区名称，则模块将根据租户名称自动为你创建一个工作区。 但是，如果你想要使用特定的工作区，可按下面所示输入该工作区的资源 ID：

    ```powershell
    Start-RdsHostPoolMigration -Tenant Contoso -HostPool Office -CopyUserAssignments -Location EastUS -Workspace <Resource ID of workspacename>
    ```
    
    如果你要使用特定的工作区但不知道其资源 ID，请运行以下 cmdlet：

    ```powershell
    Get-AzWvdWorkspace -WorkspaceName <workspace> -ResourceGroupName <resource group> |fl
    ```

  此外，还需要为现有用户分配指定用户分配模式：

  - 使用 Copy 将旧应用组中的所有用户分配复制到 Azure 资源管理器应用程序组。 用户将可以看到其客户端的两个版本的源。
  - 如果你不想要更改用户分配，请使用 None。 以后，可以使用 Azure 门户、PowerShell 或 API 将用户或用户组分配到应用组。 用户将只能使用 Azure 虚拟桌面（经典）客户端查看源。

  只能为每个订阅复制 2,000 个用户分配，因此，具体的限制取决于订阅中已有的分配数量。 模块将根据已有的分配数量计算限制。 如果没有足够的分配可供复制，你将收到一条错误消息，指出“角色分配配额不足，无法复制用户分配。 请在不使用 -CopyUserAssignments 开关的情况下重新运行命令进行迁移。”

3. 运行命令后，模块最多需要花费 15 分钟来创建服务对象。 如果你复制或移动了任何用户分配，则模块完成所有设置所需的时间将会增加。

   Start-RdsHostPoolMigration cmdlet 执行完毕后，应会看到以下内容：

      - 指定的租户或主机池的 Azure 服务对象

      - 两个新资源组：

         - 名为“Tenantname”的资源组，其中包含你的工作区。

         - 名为“Tenantname_originalHostPoolName”的资源组，其中包含主机池和桌面应用组。

      - 已发布到新建应用组的任何用户。

      - 虚拟机将在现有主机池和新主机池中提供，以避免用户在该迁移过程中遇到停机。 这样，用户就可以连接到同一个用户会话。

   由于这些新的 Azure 服务对象是 Azure 资源管理器对象，因此模块无法对其设置基于角色的访问控制 (RBAC) 权限或诊断设置。 因此，你需要手动更新这些对象的 RBAC 权限和设置。

   在模块验证初始用户连接后，你还可以根据需要将应用组发布到更多用户或用户组。

   >[!NOTE]
   >迁移后，如果在为用户分配权限后将应用组移到其他资源组，则会删除所有 RBAC 角色。 需要从头开始为用户重新分配 RBAC 权限。

4. 若要删除所有 Azure 虚拟桌面（经典）服务对象，请运行 Complete-RdsHostPoolMigration 以完成迁移过程。 此 cmdlet 会删除所有 Azure 虚拟桌面（经典）对象，只保留新的 Azure 对象。 用户将只能在其客户端上看到新建应用组的源。 此命令执行完毕后，可以安全地删除 Azure 虚拟桌面（经典）租户以完成迁移过程。

   例如：

   ```powershell
   Complete-RdsHostPoolMigration -Tenant Contoso -Location EastUS
   ```

   若要完成特定的主机池，可以在 cmdlet 中包含该主机池的名称。 例如，若要完成名为“Office”的主机池，请使用如下所示的命令：

    ```powershell
    Complete-RdsHostPoolMigration -Tenant Contoso -HostPool Office -Location EastUS
    ```

    这会删除 Azure 虚拟桌面（经典）创建的所有服务对象。 只会留下新的 Azure 对象，用户将只能在其客户端上看到新建应用组的源。 完成迁移后，需要在 Azure 虚拟桌面（经典）中显式删除租户。

5. 如果你对迁移改变了想法，并且想要还原，请运行 Revert-RdsHostPoolMigration cmdlet。
    
   例如：

   ```powershell
   Revert-RdsHostPoolMigration -Tenant Contoso -Location EastUS
   ```

   如果你要还原特定的主机池，可以在命令中包含该主机池的名称。 例如，若要还原名为“Office”的主机池，请输入如下所示的命令：

   ```powershell
   Revert-RdsHostPoolMigration -Tenant Contoso -HostPool Office -Location EastUS
   ```

   此 cmdlet 会删除所有新建的 Azure 服务对象。 用户将只能在其客户端中看到 Azure 虚拟桌面（经典）对象的源。

   但是，该 cmdlet 不会删除模块创建的工作区或其关联的资源组。 你需要手动删除这些项才能将其去除。

6. 如果你还不想删除 Azure 虚拟桌面（经典）服务对象，但想要测试迁移，可以运行 Set-RdsHostPoolHidden。

    例如：

    ```powershell
    Set-RdsHostPoolHidden -Tenant Contoso -Hostpool Office -Hidden $true -Location WestUS
    ```

    将状态设置为“true”会隐藏 Azure 虚拟桌面（经典）资源。 将状态设置为“false”会向用户显示资源。

    -Hostpool 参数是可选参数。 如果你要隐藏特定的 Azure 虚拟桌面（经典）主机池，可以使用此参数。

    此 cmdlet 会隐藏 Azure 虚拟桌面（经典）用户源和服务对象，而不会删除它们。 但是，此 cmdlet 通常只用于测试，而不适合用于完成迁移。 若要完成迁移，需要运行 Complete-RdsHostPoolMigration 命令。 否则，请运行 Revert-RdsHostPoolMigration 来还原部署。

## <a name="troubleshoot-automatic-migration"></a>排查自动迁移问题

本部分介绍如何解决迁移模块中的常见问题。

### <a name="i-cant-access-the-tenant"></a>无法访问租户

首先，请尝试做到以下两点：

- 确保管理员帐户拥有访问租户所需的权限。
- 尝试对租户运行 Get-RdsTenant。

如果这两点都没有问题，请尝试运行 Set-RdsMigrationContext cmdlet 以便为迁移设置 RDS 上下文和 ADAL 上下文：

1. 运行 Add-RdsAccount cmdlet 来创建 RDS 上下文。       

2. 在全局变量 $rdMgmtContext 中找到 RDS 上下文。         

3. 在全局变量 $AdalContext 中找到 ADAL 上下文。

4. 运行带有你找到的变量的 Set-RdsMigrationContext，格式如下：

   ```powershell
   Set-RdsMigrationContext -RdsContext <rdscontext> -AdalContext <adalcontext>
   ```

## <a name="next-steps"></a>后续步骤

如果你想要了解如何手动迁移部署，请参阅[从 Azure 虚拟桌面（经典）手动迁移](manual-migration.md)。

在迁移后，请通过查看[我们的教程](create-host-pools-azure-marketplace.md)来了解 Azure 虚拟桌面的工作方式。 从[扩展现有主机池](expand-existing-host-pool.md)和[自定义 RDP 属性](customize-rdp-properties.md)了解高级管理功能。

若要详细了解服务对象，请参阅 [Azure 虚拟桌面环境](environment-setup.md)。