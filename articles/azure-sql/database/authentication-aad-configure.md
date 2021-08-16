---
title: 配置 Azure Active Directory 身份验证
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: 了解如何在配置 Azure AD 后使用 Azure Active Directory 身份验证连接到 SQL 数据库、SQL 托管实例和 Azure Synapse Analytics。
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse, has-adal-ref, sqldbrb=2, devx-track-azurepowershell
ms.devlang: ''
ms.topic: how-to
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, sstein
ms.date: 05/11/2021
ms.openlocfilehash: 4d06ec600f71e682c9faadf3760ee76bb41c40b2
ms.sourcegitcommit: 942a1c6df387438acbeb6d8ca50a831847ecc6dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2021
ms.locfileid: "112020990"
---
# <a name="configure-and-manage-azure-ad-authentication-with-azure-sql"></a>使用 Azure SQL 配置和管理 Azure AD 身份验证

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

本文介绍如何创建和填充 Azure Active Directory (Azure AD) 实例，然后将 Azure AD 与 [Azure SQL 数据库](sql-database-paas-overview.md)、[Azure SQL 托管实例](../managed-instance/sql-managed-instance-paas-overview.md)和 [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) 配合使用。 有关概述，请参阅 [Azure Active Directory 身份验证](authentication-aad-overview.md)。

## <a name="azure-ad-authentication-methods"></a>Azure AD 身份验证方法

Azure AD 身份验证支持以下身份验证方法：

- Azure AD 仅限云的标识
- Azure AD 混合标识，此类标识支持：
  - 具有两个选项且结合无缝单一登录 (SSO) 的云身份验证
    - Azure AD 密码哈希身份验证
    - Azure AD 直通身份验证
  - 联合身份验证

若要详细了解 Azure AD 身份验证方法以及如何进行选择，请参阅[为 Azure Active Directory 混合标识解决方案选择正确的身份验证方法](../../active-directory/hybrid/choose-ad-authn.md)。

有关 Azure AD 混合标识、设置和同步的详细信息，请参阅：

- 密码哈希身份验证 - [使用 Azure AD Connect 同步实现密码哈希同步](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)
- 直通身份验证 - [Azure Active Directory 直通身份验证](../../active-directory/hybrid/how-to-connect-pta-quick-start.md)
- 联合身份验证 - [在 Azure 中部署 Active Directory 联合身份验证服务](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs)和 [Azure AD Connect 和联合身份验证](../../active-directory/hybrid/how-to-connect-fed-whatis.md)

## <a name="create-and-populate-an-azure-ad-instance"></a>创建并填充 Azure AD 实例

创建 Azure AD 实例并对其填充用户和组。 Azure AD 可以是初始 Azure AD 托管域。 Azure AD 也可以是本地 Active Directory 域服务，该服务可以与 Azure AD 联合。

有关详细信息，请参阅：
- [将本地标识与 Azure Active Directory 集成](../../active-directory/hybrid/whatis-hybrid-identity.md)
- [将自己的域名添加到 Azure AD](../../active-directory/fundamentals/add-custom-domain.md)
- [Microsoft Azure 现在支持与 Windows Server Active Directory 联合](https://azure.microsoft.com/blog/windows-azure-now-supports-federation-with-windows-server-active-directory/)
- [什么是 Azure Active Directory？](../../active-directory/fundamentals/active-directory-whatis.md)
- [使用 Windows PowerShell 管理 Azure AD](/powershell/module/azuread)
- [混合标识所需的端口和协议](../../active-directory/hybrid/reference-connect-ports.md)。

## <a name="associate-or-add-an-azure-subscription-to-azure-active-directory"></a>将 Azure 订阅关联或添加到 Azure Active Directory

1. 通过将目录设为托管数据库的 Azure 订阅的一个受信任目录，将 Azure 订阅关联到 Azure Active Directory。 有关详细信息，请参阅[将 Azure 订阅关联或添加到 Azure Active Directory 租户](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)。

2. 在 Azure 门户中使用目录切换器切换到与域关联的订阅。

   > [!IMPORTANT]
   > 每个 Azure 订阅都与某个 Azure AD 实例存在信任关系。 这意味着，此订阅信任该目录对用户、服务和设备执行身份验证。 多个订阅可以信任同一个目录，但一个订阅只能信任一个目录。 订阅与目录之间的这种信任关系不同于订阅与 Azure 中所有其他资源（网站、数据库等）之间的关系，在后一种关系中，这些资源更像是订阅的子资源。 如果某个订阅过期，则对该订阅关联的其他那些资源的访问权限也会终止。 但是，目录将保留在 Azure 中，并且用户可以将另一个订阅与该目录相关联，并继续管理目录用户。 有关资源的详细信息，请参阅[了解 Azure 中的资源访问](../../active-directory/external-identities/add-users-administrator.md)。 若要详细了解此受信任关系，请参阅[如何将 Azure 订阅关联或添加到 Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)。

## <a name="azure-ad-admin-with-a-server-in-sql-database"></a>SQL 数据库中的服务器的 Azure AD 管理员

Azure 中的每个[服务器](logical-servers.md)（托管 SQL 数据库或 Azure Synapse）开始时只使用单个服务器管理员帐户，即整个服务器的管理员。 创建另一个管理员帐户作为 Azure AD 帐户。 此主体在服务器的 master 数据库中作为包含的数据库用户创建。 管理员帐户是每个用户数据库中 db_owner 角色的成员，并且以 dbo 用户身份输入每个用户数据库 。 有关管理员帐户的详细信息，请参阅[管理数据库和登录名](logins-create-manage.md)。

将 Azure Active Directory 与异地复制结合使用时，必须为主服务器和辅助服务器配置 Azure Active Directory 管理员。 如果服务器没有 Azure Active Directory 管理员，则 Azure Active Directory 登录名和用户会收到“`Cannot connect` 到服务器”错误。

> [!NOTE]
> 如果用户使用的不是基于 Azure AD 的帐户（包括服务器管理员帐户），则无法创建基于 Azure AD 的用户，这是因为他们无权使用 Azure AD 验证建议的数据库用户。

## <a name="provision-azure-ad-admin-sql-managed-instance"></a>预配 Azure AD 管理员（SQL 托管实例）

> [!IMPORTANT]
> 若要预配 Azure SQL 托管实例，则只执行以下步骤。 在 Azure AD 中，此操作只能由全局管理员或特权角色管理员执行。
>
> 在“公共预览”中，可以在 Azure AD 中将“目录读取者”角色分配给组 。 然后，组所有者可以将托管实例标识添加为此组的成员，这将允许你为 SQL 托管实例预配 Azure AD 管理员。 有关此功能的详细信息，请参阅 [Azure SQL 的 Azure Active Directory 中的目录读取者角色](authentication-aad-directory-readers-role.md)。

SQL 托管实例需要权限来读取 Azure AD，以成功完成通过安全组成员资格验证用户身份或创建新用户等任务。 为此，需要授予 SQL 托管实例读取 Azure AD 的权限。 可以使用 Azure 门户或 PowerShell 执行此操作。

### <a name="azure-portal"></a>Azure 门户

若要通过 Azure 门户向 SQL 托管实例授予 Azure AD 读取权限，请在 Azure AD 中以全局管理员身份登录，然后执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com)右上角，从可能的 Active Directory 下拉列表中选择你的连接。

2. 选择正确的 Active Directory 作为默认的 Azure AD。

   此步骤将与 Active Directory 关联的订阅链接到 SQL 托管实例，确保为 Azure AD 实例和 SQL 托管实例使用相同的订阅。

3. 导航到要用于 Azure AD 集成的 SQL 托管实例。

   ![Azure 门户屏幕截图，显示了为所选 SQL 托管实例打开的“Active Directory 管理员”页。](./media/authentication-aad-configure/aad.png)

4. 选择“Active Directory 管理员”页顶部的横幅，并为当前用户授予权限。

    ![对话框的屏幕截图，该对话框用于向 SQL 托管实例授予访问 Active Directory 的权限。 已选择“授予权限”按钮。](./media/authentication-aad-configure/grant-permissions.png)

5. 成功完成操作后，右上角会显示以下通知：

    ![通知的屏幕截图，该通知确认已成功更新托管实例的 Active Directory 读取权限。](./media/authentication-aad-configure/success.png)

6. 现在即可为 SQL 托管实例选择 Azure AD 管理员。 为此，请在“Active Directory 管理员”页上选择“设置管理员”命令。

    ![此屏幕截图显示了在所选 SQL 托管实例的“Active Directory 管理员”页上突出显示的“设置管理员”命令。](./media/authentication-aad-configure/set-admin.png)

7. 在“Azure AD 管理员”页中，搜索某位用户，选择该用户或组作为管理员，然后选择“选择”。

   “Active Directory 管理员”页会显示 Active Directory 的所有成员和组。 若用户或组为灰显，则无法选择，因为不支持它们作为 Azure AD 管理员。 请参阅 [Azure AD 功能和限制](authentication-aad-overview.md#azure-ad-features-and-limitations)中受支持的管理员列表。 Azure 基于角色的访问控制 (Azure RBAC) 仅适用于 Azure 门户，不会传播到 SQL 数据库、SQL 托管实例或 Azure Synapse。

    ![添加 Azure Active Directory 管理员](./media/authentication-aad-configure/add-azure-active-directory-admin.png)

8. 在“Active Directory 管理员”页顶部，选择“保存”。

    ![“Active Directory 管理员”页的屏幕截图，其中的“保存”按钮位于顶行的“设置管理员”和“删除管理员”按钮的旁边。](./media/authentication-aad-configure/save.png)

    更改管理员的过程可能需要几分钟时间。 然后，新管理员将出现在“Active Directory 管理员”框中。

在为 SQL 托管实例预配 Azure AD 管理员之后，即可开始使用 [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true) 语法创建 Azure AD 服务器主体（登录名）。 有关详细信息，请参阅 [SQL 托管实例概述](../managed-instance/sql-managed-instance-paas-overview.md#azure-active-directory-integration)。

> [!TIP]
> 之后如需删除管理员，请在“Active Directory 管理员”页顶部，选择“删除管理员”，然后选择“保存”。

### <a name="powershell"></a>PowerShell

若要使用 PowerShell 向 SQL 托管实例授予 Azure AD 读取权限，请运行以下脚本：

```powershell
# Gives Azure Active Directory read permission to a Service Principal representing the SQL Managed Instance.
# Can be executed only by a "Global Administrator" or "Privileged Role Administrator" type of user.

$aadTenant = "<YourTenantId>" # Enter your tenant ID
$managedInstanceName = "MyManagedInstance"

# Get Azure AD role "Directory Users" and create if it doesn't exist
$roleName = "Directory Readers"
$role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq $roleName}
if ($role -eq $null) {
    # Instantiate an instance of the role template
    $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq $roleName}
    Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq $roleName}
}

# Get service principal for your SQL Managed Instance
$roleMember = Get-AzureADServicePrincipal -SearchString $managedInstanceName
$roleMember.Count
if ($roleMember -eq $null) {
    Write-Output "Error: No Service Principals with name '$    ($managedInstanceName)', make sure that managedInstanceName parameter was     entered correctly."
    exit
}
if (-not ($roleMember.Count -eq 1)) {
    Write-Output "Error: More than one service principal with name pattern '$    ($managedInstanceName)'"
    Write-Output "Dumping selected service principals...."
    $roleMember
    exit
}

# Check if service principal is already member of readers role
$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
$selDirReader = $allDirReaders | where{$_.ObjectId -match     $roleMember.ObjectId}

if ($selDirReader -eq $null) {
    # Add principal to readers role
    Write-Output "Adding service principal '$($managedInstanceName)' to     'Directory Readers' role'..."
    Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId     $roleMember.ObjectId
    Write-Output "'$($managedInstanceName)' service principal added to     'Directory Readers' role'..."

    #Write-Output "Dumping service principal '$($managedInstanceName)':"
    #$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
    #$allDirReaders | where{$_.ObjectId -match $roleMember.ObjectId}
}
else {
    Write-Output "Service principal '$($managedInstanceName)' is already     member of 'Directory Readers' role'."
}
```

### <a name="powershell-for-sql-managed-instance"></a>用于 SQL 托管实例的 PowerShell

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要运行 PowerShell cmdlet，需要已安装并运行 Azure PowerShell。 有关详细信息，请参阅 [如何安装和配置 Azure PowerShell](/powershell/azure/)。

> [!IMPORTANT]
> PowerShell Azure 资源管理器 (RM) 模块仍受 Azure SQL 托管实例的支持，但所有未来的开发都是针对 Az.Sql 模块的。 AzureRM 模块至少在 2020 年 12 月之前将继续接收 bug 修补程序。  Az 模块和 AzureRm 模块中的命令参数大体上是相同的。 若要详细了解其兼容性，请参阅[新 Azure PowerShell Az 模块简介](/powershell/azure/new-azureps-module-az)。

若要预配 Azure AD 管理员，请执行以下 Azure PowerShell 命令：

- Connect-AzAccount
- Select-AzSubscription

下表列出了用于预配和管理 SQL 托管实例的 Azure AD 管理员的 cmdlet：

| Cmdlet 名称 | 说明 |
| --- | --- |
| [Set-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlinstanceactivedirectoryadministrator) |预配当前订阅中 SQL 托管实例的 Azure AD 管理员。 （必须来自当前订阅）|
| [Remove-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlinstanceactivedirectoryadministrator) |删除当前订阅中 SQL 托管实例的 Azure AD 管理员。 |
| [Get-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlinstanceactivedirectoryadministrator) |返回当前订阅中 SQL 托管实例的 Azure AD 管理员的相关信息。|

以下命令获取名为 ManagedInstance01 的 SQL 托管实例的 Azure AD 管理员的相关信息，该实例与名为 ResourceGroup01 的资源组相关联。

```powershell
Get-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01"
```

以下命令为名为 ManagedInstance01 的 SQL 托管实例预配名为 DBA 的 Azure AD 管理员组。 此服务器与资源组 ResourceGroup01 相关联。

```powershell
Set-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01" -DisplayName "DBAs" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353b"
```

以下命令删除名为 ManagedInstanceName01 的 SQL 托管实例的 Azure AD 管理员，该实例与资源组 ResourceGroup01 相关联。

```powershell
Remove-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstanceName01" -Confirm -PassThru
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

也可通过调用以下 CLI 命令来预配 SQL 托管实例的 Azure AD 管理员：

| 命令 | 说明 |
| --- | --- |
|[az sql mi ad-admin create](/cli/azure/sql/mi/ad-admin#az_sql_mi_ad_admin_create) | 预配 SQL 托管实例（必须来自当前订阅）的 Azure Active Directory 管理员。 |
|[az sql mi ad-admin delete](/cli/azure/sql/mi/ad-admin#az_sql_mi_ad_admin_delete) | 删除 SQL 托管实例的 Azure Active Directory 管理员。 |
|[az sql mi ad-admin list](/cli/azure/sql/mi/ad-admin#az_sql_mi_ad_admin_list) | 返回当前为 SQL 托管实例配置的 Azure Active Directory 管理员的相关信息。 |
|[az sql mi ad-admin update](/cli/azure/sql/mi/ad-admin#az_sql_mi_ad_admin_update) | 更新 SQL 托管实例的 Active Directory 管理员。 |

有关 CLI 命令的详细信息，请参阅 [az sql mi](/cli/azure/sql/mi)。

* * *

## <a name="provision-azure-ad-admin-sql-database"></a>预配 Azure AD 管理员（SQL 数据库）

> [!IMPORTANT]
> 只有在预配 SQL 数据库或 Azure Synapse 的[服务器](logical-servers.md)的情况下，才执行以下步骤。

以下两个过程说明如何使用 PowerShell 在 Azure 门户中为服务器预配 Azure Active Directory 管理员。

### <a name="azure-portal"></a>Azure 门户

1. 在 [Azure 门户](https://portal.azure.com/)右上角，选择你的连接，以下拉可能的 Active Directory 列表。 选择正确的 Active Directory 作为默认的 Azure AD。 此步骤将与订阅关联的 Active Directory 链接到服务器，确保为 Azure AD 和服务器使用相同的订阅。

2. 搜索并选择“SQL 服务器”。

    ![搜索并选择“SQL 服务器”](./media/authentication-aad-configure/search-for-and-select-sql-servers.png)

    >[!NOTE]
    > 在此页面上，在选择“SQL 服务器”前，可选择名称旁边的星号标记以收藏该类别，并将“SQL 服务器”添加到左侧导航栏。

3. 在“SQL Server”页上，选择“Active Directory 管理员”。

4. 在“Active Directory 管理员”页中，选择“设置管理员”。

    ![SQL Server 的“设置 Active Directory 管理员”](./media/authentication-aad-configure/sql-servers-set-active-directory-admin.png)  

5. 在“添加管理员”页中，搜索某位用户，选择该用户或组作为管理员，然后选择“选择”。 （“Active Directory 管理员”页会显示 Active Directory 的所有成员和组。 若用户或组为灰显，则无法选择，因为不支持它们作为 Azure AD 管理员。 （请参阅[将 Azure Active Directory 身份验证与使用 SQL 数据库或 Azure Synapse 进行身份验证结合使用](authentication-aad-overview.md)的“Azure AD 功能和限制”部分中支持的管理员列表。）Azure 基于角色的访问控制 (Azure RBAC) 仅适用于该门户，不会传播到 SQL Server。

    ![选择 Azure Active Directory 管理员](./media/authentication-aad-configure/select-azure-active-directory-admin.png)  

6. 在“Active Directory 管理员”页顶部，选择“保存”。

    ![保存管理员](./media/authentication-aad-configure/save-admin.png)

更改管理员的过程可能需要几分钟时间。 然后，新管理员出现在“Active Directory 管理员”  框中。

   > [!NOTE]
   > 设置 Azure AD 管理员时，此新的管理员名称（用户或组）不能已作为服务器身份验证用户存在于虚拟 master 数据库中。 如果存在，Azure AD 管理员设置将失败；将回滚其创建，并指示此管理员（名称）已存在。 由于这种服务器身份验证用户不是 Azure AD 的一部分，因此使用 Azure AD 身份验证连接到服务器的任何尝试都会失败。

之后如需删除管理员，请在“Active Directory 管理员”页顶部，选择“删除管理员”，然后选择“保存”。

### <a name="powershell-for-sql-database-and-azure-synapse"></a>用于 SQL 数据库和 Azure Synapse 的 PowerShell

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要运行 PowerShell cmdlet，需要已安装并运行 Azure PowerShell。 有关详细信息，请参阅 [如何安装和配置 Azure PowerShell](/powershell/azure/)。 若要预配 Azure AD 管理员，请执行以下 Azure PowerShell 命令：

- Connect-AzAccount
- Select-AzSubscription

Cmdlet，用于预配和管理 SQL 数据库和 Azure Synapse 的 Azure AD 管理员：

| Cmdlet 名称 | 说明 |
| --- | --- |
| [Set-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlserveractivedirectoryadministrator) |为托管 SQL 数据库或 Azure Synapse 的服务器预配 Azure Active Directory 管理员。 （必须来自当前订阅） |
| [Remove-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlserveractivedirectoryadministrator) |为托管 SQL 数据库或 Azure Synapse 的服务器删除 Azure Active Directory 管理员。|
| [Get-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator) |返回有关当前为托管 SQL 数据库或 Azure Synapse 的服务器配置的 Azure Active Directory 管理员的信息。 |

使用 PowerShell 命令 get-help 查看其中每个命令的详细信息。 例如，`get-help Set-AzSqlServerActiveDirectoryAdministrator`。

以下脚本为名为 Group-23 的资源组中的 demo_server 服务器预配名为 DBA_Group 的 Azure AD 管理员组（对象 ID `40b79501-b343-44ed-9ce7-da4c8cc7353f`）  ：

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" -DisplayName "DBA_Group"
```

**DisplayName** 输入参数接受 Azure AD 显示名称或用户主体名称。 例如，``DisplayName="John Smith"`` 和 ``DisplayName="johns@contoso.com"``。 对于 Azure AD 组，只支持 Azure AD 显示名称。

> [!NOTE]
> Azure PowerShell 命令 ```Set-AzSqlServerActiveDirectoryAdministrator``` 不会阻止你为不受支持的用户预配 Azure AD 管理员。 可以预配不受支持的用户，但其无法连接到数据库。

以下示例使用可选的 **ObjectID**：

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" `
    -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [!NOTE]
> 当 **DisplayName** 不唯一时，需要使用 Azure AD **ObjectID**。 若要检索 **ObjectID** 和 **DisplayName** 值，请使用 Azure 经典门户的 Active Directory 部分，并查看用户或组的属性。

以下示例返回有关服务器的当前 Azure AD 管理员的信息：

```powershell
Get-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" | Format-List
```

以下示例删除一个 Azure AD 管理员：

```powershell
Remove-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

可以通过调用以下 CLI 命令来预配 Azure AD 管理员：

| 命令 | 说明 |
| --- | --- |
|[az sql server ad-admin create](/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_create) | 为托管 SQL 数据库或 Azure Synapse 的服务器预配 Azure Active Directory 管理员。 （必须来自当前订阅） |
|[az sql server ad-admin delete](/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_delete) | 为托管 SQL 数据库或 Azure Synapse 的服务器删除 Azure Active Directory 管理员。 |
|[az sql server ad-admin list](/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_list) | 返回有关当前为托管 SQL 数据库或 Azure Synapse 的服务器配置的 Azure Active Directory 管理员的信息。 |
|[az sql server ad-admin update](/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_update) | 为托管 SQL 数据库或 Azure Synapse 的服务器更新 Azure Active Directory 管理员。 |

有关 CLI 命令的详细信息，请参阅 [az sql server](/cli/azure/sql/server)。

* * *

> [!NOTE]
> 也可以使用 REST API 预配 Azure Active Directory 管理员。 有关详细信息，请参阅 [Azure SQL 数据库的 Azure SQL 数据库操作的 Service Management REST API 参考和操作](/rest/api/sql/)

## <a name="set-or-unset-the-azure-ad-admin-using-service-principals"></a>使用服务主体设置或取消设置 Azure AD 管理员

如果计划让服务主体为 Azure SQL 设置或取消设置 Azure AD 管理员，则需要额外的 API 权限。 需要将 [Directory.Read.All](/graph/permissions-reference#application-permissions-18) 应用程序 API 权限添加到 Azure AD 中的应用程序。

> [!NOTE]
> 本部分关于设置 Azure AD 管理员的内容仅适用于使用 PowerShell 或 CLI 命令，因为不能将 Azure 门户用作 Azure AD 服务主体。

:::image type="content" source="media/authentication-aad-service-principals-tutorial/aad-directory-reader-all-permissions.png" alt-text="Azure AD 中的 Directory.Reader.All 权限":::

服务主体还需要适用于 SQL 数据库的 [SQL Server 参与者](../../role-based-access-control/built-in-roles.md#sql-server-contributor)角色，或适用于 SQL 托管实例的 [SQL 托管实例参与者](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor)角色 。

有关详细信息，请参阅[服务主体（Azure AD 应用程序）](authentication-aad-service-principal.md)。

## <a name="configure-your-client-computers"></a>配置客户端计算机

在应用程序或用户要使用 Azure AD 标识连接到 SQL 数据库或 Azure Synapse 的所有客户端计算机上，必须安装以下软件：

- .NET Framework 4.6 或更高版本（在 [https://msdn.microsoft.com/library/5a4x27ek.aspx](/dotnet/framework/install/guide-for-developers) 上提供）。
- 适用于 SQL Server 的 Azure Active Directory 身份验证库 (*ADAL.DLL*)。 下面是下载链接，用于安装包含 *ADAL.DLL* 库的最新 SSMS、ODBC 和 OLE DB 驱动程序。
  - [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)
  - [ODBC Driver 17 for SQL Server](/sql/connect/odbc/download-odbc-driver-for-sql-server?view=sql-server-ver15&preserve-view=true)
  - [OLE DB Driver 18 for SQL Server](/sql/connect/oledb/download-oledb-driver-for-sql-server?view=sql-server-ver15&preserve-view=true)

可以通过以下操作来满足这些要求：

- 安装最新版 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) 或 [SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt) 符合 .NET Framework 4.6 要求。
  - SSMS 安装 *ADAL.DLL* 的 x86 版本。
  - SSDT 安装 *ADAL.DLL* 的 amd64 版本。
  - [Visual Studio 下载](https://www.visualstudio.com/downloads/download-visual-studio-vs)提供的最新 Visual Studio 符合 .NET Framework 4.6 要求，但并未安装必需的 amd64 版 *ADAL.DLL*。

## <a name="create-contained-users-mapped-to-azure-ad-identities"></a>创建映射到 Azure AD 标识的包含的用户

由于 SQL 托管实例支持 Azure AD 服务器主体（登录名），因此不需要使用包含的数据库用户。 通过 Azure AD 服务器主体（登录名）可以从 Azure AD 用户、组或应用程序创建登录名。 这意味着可以通过使用 Azure AD 服务器登录名（而不是包含的数据库用户）对 SQL 托管实例进行身份验证。 有关详细信息，请参阅 [SQL 托管实例概述](../managed-instance/sql-managed-instance-paas-overview.md#azure-active-directory-integration)。 有关创建 Azure AD 服务器主体（登录名）的语法，请参阅 [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true)。

但是，将 Azure Active Directory 身份验证与 SQL 数据库和 Azure Synapse 结合使用时，需要使用基于 Azure AD 标识的包含的数据库用户。 包含的数据库用户在 master 数据库中没有登录名，且映射到与数据库关联的 Azure AD 目录中的标识。 Azure AD 标识可以是单独的用户帐户，也可以是组。 有关包含的数据库用户的详细信息，请参阅[包含的数据库用户 - 使你的数据库可移植](/sql/relational-databases/security/contained-database-users-making-your-database-portable)。

> [!NOTE]
> 不能使用 Azure 门户创建数据库用户（管理员除外）。 Azure 角色不会传播到 SQL 数据库中的数据库、SQL 托管实例或 Azure Synapse。 Azure 角色用于管理 Azure 资源，不适用于数据库权限。 例如，“SQL Server 参与者”角色不会授予连接到 SQL 数据库、SQL 托管实例或 Azure Synapse 中的数据库的访问权限。 必须使用 Transact-SQL 语句直接在数据库中授予访问权限。

> [!WARNING]
> 不支持在 T-SQL 的 `CREATE LOGIN` 和 `CREATE USER` 语句中将特殊字符（例如冒号 `:` 或与号 `&`）用作用户名的一部分。

若要创建基于 Azure AD 的包含的数据库用户（而不是拥有数据库的服务器管理员），请以至少具有 **ALTER ANY USER** 权限的用户身份使用 Azure AD 标识连接到数据库。 然后，使用以下 Transact-SQL 语法：

```sql
CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
```

*Azure_AD_principal_name* 可以是 Azure AD 用户的用户主体名称，也可以是 Azure AD 组的显示名称。

**示例：** 若要创建代表 Azure AD 联合或托管域用户的包含的数据库用户：

```sql
CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;
```

若要创建代表 Azure AD 或联合域组的包含的数据库用户，请提供安全组的显示名称：

```sql
CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;
```

若要创建代表可使用 Azure AD 令牌连接的应用程序的包含的数据库用户：

```sql
CREATE USER [appName] FROM EXTERNAL PROVIDER;
```

> [!NOTE]
> 此命令要求 SQL 代表已登录用户访问 Azure AD（“外部提供程序”）。 有时候，会出现导致 Azure AD 将异常返回给 SQL 的情况。 在这些情况下，用户会看到 SQL 错误 33134，其中会包含特定于 Azure AD 的错误消息。 大多数情况下，错误会指出访问被拒绝、用户必须注册 MFA 才能访问资源，或者在第一方应用程序之间进行的访问必须通过预授权进行处理。 如果是前面的两种情况，则问题通常是由在用户的 Azure AD 租户中设置的条件访问策略引起的：这些策略阻止用户访问外部提供程序。 更新条件访问策略以允许访问应用程序“00000002-0000-0000-c000-000000000000”（Azure AD 图形 API 的应用程序 ID）应该就能解决此问题。 如果错误指出在第一方应用程序之间进行的访问必须通过预授权进行处理，则出现问题是因为用户以服务主体的身份登录。 如果改由用户执行命令，则命名应该会成功。

> [!TIP]
> 除了与 Azure 订阅关联的 Azure Active Directory 以外，无法从 Azure Active Directory 直接创建用户。 但是，可将关联的 Active Directory 中导入的用户（称为外部用户）的其他 Active Directory 成员添加到租户 Active Directory 中的 Active Directory 组。 通过创建该 AD 组的包含数据库用户，来自外部 Active Directory 的用户可以访问 SQL 数据库。

有关基于 Azure Active Directory 标识创建包含的数据库用户的详细信息，请参阅 [CREATE USER (Transact-SQL)](/sql/t-sql/statements/create-user-transact-sql)。

> [!NOTE]
> 删除服务器的 Azure Active Directory 管理员会阻止所有 Azure AD 身份验证用户连接到服务器。 必要时，SQL 数据库管理员可以手动删除无法使用的 Azure AD 用户。

> [!NOTE]
> 如果收到 **Connection Timeout Expired** 消息，则可能需要将连接字符串的 `TransparentNetworkIPResolution` 参数设置为 false。 有关详细信息，请参阅 [.NET Framework 4.6.1 的连接超时问题 - TransparentNetworkIPResolution](/archive/blogs/dataaccesstechnologies/connection-timeout-issue-with-net-framework-4-6-1-transparentnetworkipresolution)。

创建数据库用户时，该用户会收到 **CONNECT** 权限，并能够以 **PUBLIC** 角色的成员身份连接到该数据库。 最初，仅供用户使用的权限是授予 PUBLIC 角色的任何权限，或者授予其所属任何 Azure AD 组的任何权限。 预配基于 Azure AD 的包含的数据库用户后，可以授予用户其他权限，方法与向任何其他类型的用户授予权限相同。 通常，将权限授予数据库角色，并将用户添加到角色。 有关详细信息，请参阅 [数据库引擎权限基础知识](https://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx)。 有关特殊 SQL 数据库角色的详细信息，请参阅[在 Azure SQL 数据库中管理数据库和登录名](logins-create-manage.md)。
如果将联合域用户帐户作为外部用户导入到托管域，则此用户必须使用托管域标识。

> [!NOTE]
> Azure AD 用户在数据库元数据中均标记为类型 E (EXTERNAL_USER)，而组则标记为类型 X (EXTERNAL_GROUPS)。 有关详细信息，请参阅 [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql)。

## <a name="connect-to-the-database-using-ssms-or-ssdt"></a>使用 SSMS 或 SSDT 连接到数据库  

若要确认 Azure AD 管理员已正确设置，请使用 Azure AD 管理员帐户连接到 **master** 数据库。
若要预配基于 Azure AD 的包含的数据库用户（而不是拥有数据库的服务器管理员），请使用具有数据库访问权限的 Azure AD 标识连接到数据库。

> [!IMPORTANT]
> 2016 年开始的 [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) 和 2015 年开始的 [SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt) 支持 Azure Active Directory 身份验证。 2016 年 8 月版 SSMS 也包括对 Active Directory 通用身份验证的支持，这样管理员就能要求用户使用手机、短信、带 PIN 码的智能卡或移动应用通知进行多重身份验证。

## <a name="using-an-azure-ad-identity-to-connect-using-ssms-or-ssdt"></a>使用 Azure AD 标识通过 SSMS 或 SSDT 进行连接

以下过程说明如何使用 SQL Server Management Studio 或 SQL Server 数据库工具连接到具有 Azure AD 标识的 SQL 数据库。

### <a name="active-directory-integrated-authentication"></a>Active Directory 集成身份验证

如果你使用来自联合域或托管域（配置为使用无缝单一登录以实现直通和密码哈希身份验证）的 Azure Active Directory 凭据登录到 Windows，则可使用此方法。 有关详细信息，请参阅 [Azure Active Directory 无缝单一登录](../../active-directory/hybrid/how-to-connect-sso.md)。

1. 启动 Management Studio 或 Data Tools 后，在“连接到服务器”（或“连接到数据库引擎”）对话框的“身份验证”框中，选择“Azure Active Directory - 集成”。 由于会为连接提供现有凭据，因此无需密码，也无法输入密码。

   ![选择 AD 集成身份验证][11]

2. 选择“选项”按钮，在“连接属性”页上的“连接到数据库”框中，键入所要连接的用户数据库的名称。 有关详细信息，请参阅 [Azure AD 多重身份验证](authentication-mfa-ssms-overview.md#azure-ad-domain-name-or-tenant-id-parameter)一文，其中介绍了 SSMS 17.x 和 18.x 的连接属性之间的差异。

   ![选择数据库名称][13]

### <a name="active-directory-password-authentication"></a>Active Directory 密码身份验证

在使用 Azure AD 托管域与 Azure AD 主体名称连接时使用此方法。 还可以在某些情况下（例如，在进行远程工作时）将其用于联合帐户，无需访问域。

使用此方法能够以 Azure AD 仅限云的标识用户身份或者以使用 Azure AD 混合标识的用户身份，向 SQL 数据库或 SQL 托管实例中的数据库进行身份验证。 此方法支持想要使用其 Windows 凭据，但其本地计算机未加入域（例如，使用远程访问）的用户。 在这种情况下，Windows 用户可以指定其域帐户和密码，然后可以向 SQL 数据库、SQL 托管实例或 Azure Synapse 中的数据库进行身份验证。

1. 启动 Management Studio 或 Data Tools 后，在“连接到服务器”（或“连接到数据库引擎”）对话框的“身份验证”框中，选择“Azure Active Directory - 密码”。

2. 在“用户名”框中，以 **username\@domain.com** 格式键入 Azure Active Directory 用户名。 用户名必须是来自 Azure Active Directory 的帐户或是来自 Azure Active Directory 的托管域或联合域的帐户。

3. 在“密码”框中，为 Azure Active Directory 帐户或托管/联合域帐户键入用户密码。

    ![选择 AD 密码身份验证][12]

4. 选择“选项”按钮，在“连接属性”页上的“连接到数据库”框中，键入所要连接的用户数据库的名称。 （请参阅前一选项的图。）

### <a name="active-directory-interactive-authentication"></a>Active Directory 交互式身份验证

通过此方法可以在使用或不使用多重身份验证 (MFA) 的情况下，使用以交互方式请求的密码进行交互式身份验证。 使用此方法能够以 Azure AD 仅限云的标识用户身份或者以使用 Azure AD 混合标识的用户身份，向 SQL 数据库、SQL 托管实例和 Azure Synapse 中的数据库进行身份验证。

有关详细信息，请参阅[将 Azure AD 多重身份验证用于 SQL 数据库和 Azure Synapse（SSMS 对 MFA 的支持）](authentication-mfa-ssms-overview.md)。

## <a name="using-an-azure-ad-identity-to-connect-from-a-client-application"></a>使用 Azure AD 标识从客户端应用程序进行连接

以下过程说明如何使用 Azure AD 标识从客户端应用程序连接到 SQL 数据库。

### <a name="active-directory-integrated-authentication"></a>Active Directory 集成身份验证

若要使用集成的 Windows 身份验证，域的 Active Directory 必须与 Azure Active Directory 联合，或者应该是配置为使用无缝单一登录以实现直通或密码哈希身份验证的托管域。 有关详细信息，请参阅 [Azure Active Directory 无缝单一登录](../../active-directory/hybrid/how-to-connect-sso.md)。

> [!NOTE]
> 不支持将用于集成 Windows 身份验证的 [MSAL.NET (Microsoft.Identity.Client)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#roadmap) 用于无缝单一登录以实现直通或密码哈希身份验证。

连接到数据库的客户端应用程序（或服务）必须运行在已使用用户的域凭据加入域的计算机上。

若要使用集成的身份验证和 Azure AD 标识连接到数据库，必须将数据库连接字符串中的 Authentication 关键字设置为 `Active Directory Integrated`。 下面的 C# 代码示例使用 ADO.NET。

```csharp
string ConnectionString = @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

不支持使用连接字符串关键字 `Integrated Security=True` 连接到 Azure SQL 数据库。 进行 ODBC 连接时，需要删除空格，并将“Authentication”设置为“ActiveDirectoryIntegrated”。

### <a name="active-directory-password-authentication"></a>Active Directory 密码身份验证

若要使用 Azure AD 仅限云的标识用户帐户或那些使用 Azure AD 混合标识的用户帐户连接到数据库，必须将 Authentication 关键字设置为 `Active Directory Password`。 连接字符串必须包含“User ID/UID”和“Password/PWD”关键字和值。 下面的 C# 代码示例使用 ADO.NET。

```csharp
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

通过 [Azure AD 身份验证 GitHub 演示](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth)中提供的演示代码示例，了解有关 Azure AD 身份验证方法的详细信息。

## <a name="azure-ad-token"></a>Azure AD 令牌

此身份验证方法允许中间层服务通过获取 [JSON Web 令牌 (JWT)](../../active-directory/develop/id-tokens.md) 来连接到 SQL 数据库中的数据库、SQL 托管实例，或通过从 Azure AD 获取令牌来连接到 Azure Synapse。 此方法可实现各种应用程序方案，包括使用基于证书的身份验证的服务标识、服务主体和应用程序。 必须完成四个基本步骤才能使用 Azure AD 令牌身份验证：

1. 在 Azure Active Directory 中注册应用程序，并获取代码的客户端 ID。
2. 创建代表应用程序的数据库用户。 （此前已在步骤 6 中完成。）
3. 在运行应用程序的客户端计算机上创建证书。
4. 为应用程序添加用作密钥的证书。

示例连接字符串：

```csharp
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
conn.AccessToken = "Your JWT token"
conn.Open();
```

有关详细信息，请参阅 [SQL Server 安全性博客](/archive/blogs/sqlsecurity/token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth)。 有关添加证书的详细信息，请参阅 [Azure Active Directory 中基于证书的身份验证入门](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md)。

### <a name="sqlcmd"></a>sqlcmd

以下语句使用版本 13.1 的 sqlcmd 进行连接，该版本可从 [下载中心](https://www.microsoft.com/download/details.aspx?id=53591)下载。

> [!NOTE]
> 带 `-G` 的 `sqlcmd` 命令不适用于系统标识，它需要用户主体登录名。

```cmd
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="troubleshoot-azure-ad-authentication"></a>Azure AD 身份验证故障排除

有关 Azure AD 身份验证问题的故障排除指南，请参阅以下博客：<https://techcommunity.microsoft.com/t5/azure-sql-database/troubleshooting-problems-related-to-azure-ad-authentication-with/ba-p/1062991>

## <a name="next-steps"></a>后续步骤

- 有关 SQL 数据库中登录名、用户、数据库角色和权限的概述，请参见[登录名、用户、数据库角色和用户帐户](logins-create-manage.md)。
- 有关数据库主体的详细信息，请参阅[主体](/sql/relational-databases/security/authentication-access/principals-database-engine)。
- 有关数据库角色的详细信息，请参阅[数据库角色](/sql/relational-databases/security/authentication-access/database-level-roles)。
- 有关 SQL 数据库中的防火墙规则的详细信息，请参阅 [SQL 数据库防火墙规则](firewall-configure.md)。
- 有关如何将 Azure AD 来宾用户设置为 Azure AD 管理员的信息，请参阅[创建 Azure AD 来宾用户并将其设置为 Azure AD 管理员](authentication-aad-guest-users.md)。
- 有关如何使用 Azure SQL 服务主体的信息，请参阅[使用 Azure AD 应用程序创建 Azure AD 用户](authentication-aad-service-principal-tutorial.md)

<!--Image references-->

[11]: ./media/authentication-aad-configure/active-directory-integrated.png
[12]: ./media/authentication-aad-configure/12connect-using-pw-auth2.png
[13]: ./media/authentication-aad-configure/13connect-to-db2.png