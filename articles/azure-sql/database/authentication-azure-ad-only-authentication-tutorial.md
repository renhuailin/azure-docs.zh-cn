---
title: 启用仅 Azure Active Directory 身份验证
description: 本文将引导你在 Azure SQL 数据库和 Azure SQL 托管实例中启用仅 Azure Active Directory (Azure AD) 身份验证功能
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
ms.service: sql-db-mi
ms.subservice: security
ms.topic: tutorial
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/31/2021
ms.openlocfilehash: 76425e37c83ea5ac44653e20753e35606b89d370
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128643225"
---
# <a name="tutorial-enable-azure-active-directory-only-authentication-with-azure-sql"></a>教程：启用 Azure SQL 中的仅限 Azure Active Directory 身份验证

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

> [!NOTE]
> 本文所述的“仅限 Azure AD 身份验证”功能以公共预览版提供 。 

本文将引导你启用 Azure SQL 数据库和 Azure SQL 托管实例中的[仅限 Azure AD 身份验证](authentication-azure-ad-only-authentication.md)功能。 如果希望预配启用了仅限 Azure AD 的身份验证的 SQL 数据库或托管实例，请参阅[在 Azure SQL 中创建启用了仅限 Azure AD 的身份验证的服务器](authentication-azure-ad-only-authentication-create-server.md)。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> - 分配角色以启用“仅限 Azure AD 身份验证”
> - 使用 Azure 门户、Azure CLI 或 PowerShell 启用“仅限 Azure AD 身份验证”
> - 检查是否已启用“仅限 Azure AD 身份验证”
> - 测试与 Azure SQL 的连接
> - 使用 Azure 门户、Azure CLI 或 PowerShell 禁用“仅限 Azure AD 身份验证”


## <a name="prerequisites"></a>先决条件

- Azure AD 实例。 有关详细信息，请参阅[使用 Azure SQL 配置和管理 Azure AD 身份验证](authentication-aad-configure.md)。
- 一个包含数据库的 SQL 数据库或 SQL 托管实例以及相应的登录名或用户。 如果尚未创建 Azure SQL 数据库，请参阅[快速入门：创建 Azure SQL 数据库单一数据库](single-database-create-quickstart.md)；或参阅[快速入门：创建 Azure SQL 托管实例](../managed-instance/instance-create-quickstart.md)。

## <a name="assign-role-to-enable-azure-ad-only-authentication"></a>分配角色以启用“仅限 Azure AD 身份验证”

若要启用或禁用“仅限 Azure AD 身份验证”，在本教程中执行这些操作的 Azure AD 用户需要具有所选的内置角色。 在本教程中，我们将为用户分配 [SQL 安全管理者](../../role-based-access-control/built-in-roles.md#sql-security-manager)角色。

有关如何为 Azure AD 帐户分配角色的详细信息，请参阅[为使用 Azure Active Directory 的用户分配管理员和非管理员角色](../../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)

有关启用或禁用“仅限 Azure AD 身份验证”时所需的权限的详细信息，请参阅[“仅限 Azure AD 身份验证”一文的“权限”部分](authentication-azure-ad-only-authentication.md#permissions)。

1. 在本示例中，我们将为用户 `UserSqlSecurityManager@contoso.onmicrosoft.com` 分配“SQL 安全管理者”角色。 使用可分配 Azure AD 角色的特权用户身份登录到 [Azure 门户](https://portal.azure.com/)。
1. 转到你的 SQL 服务器资源，然后在菜单中选择“访问控制(IAM)”。 选择“添加”按钮，然后在下拉菜单中选择“添加角色分配” 。

   :::image type="content" source="media/authentication-azure-ad-only-authentication/azure-ad-only-authentication-access-control.png" alt-text="Azure 门户中的“访问控制”窗格":::

1. 在“添加角色分配”窗格中选择“SQL 安全管理者”角色，然后选择要为其授予权限、使其能够启用或禁用“仅限 Azure AD 身份验证”的用户 。

   :::image type="content" source="media/authentication-azure-ad-only-authentication/azure-ad-only-authentication-access-control-add-role.png" alt-text="Azure 门户中的“添加角色分配”窗格":::

1. 单击 **“保存”** 。

## <a name="enable-azure-ad-only-authentication"></a>启用仅 Azure AD 身份验证

# <a name="portal"></a>[Portal](#tab/azure-portal)

## <a name="enable-in-sql-database-using-azure-portal"></a>使用 Azure 门户在 SQL 数据库中启用

若要在 Azure 门户中启用“仅限 Azure AD 身份验证”，请参阅以下步骤。

1. 使用具有 [SQL 安全管理者](../../role-based-access-control/built-in-roles.md#sql-security-manager)角色的用户身份转到 [Azure 门户](https://portal.azure.com/)。
1. 转到你的 SQL 服务器资源，然后在“设置”菜单下选择“Azure Active Directory” 。

   :::image type="content" source="media/authentication-azure-ad-only-authentication/azure-ad-only-authentication-portal.png" alt-text="“启用仅限 Azure AD 身份验证”菜单":::

1. 如果尚未添加 Azure Active Directory 管理员，需要先设置此角色才能启用“仅限 Azure AD 身份验证”。
1. 选中“对此服务器支持仅限 Azure Active Directory 身份验证”复选框。
1. 此时会显示“启用仅限 Azure AD 身份验证”弹出窗口。 单击“是”以启用该功能，然后保存设置 。

## <a name="azure-sql-managed-instance"></a>Azure SQL 托管实例

目前不支持在门户中管理 SQL 托管实例的“仅限 Azure AD 身份验证”。

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

## <a name="enable-in-sql-database-using-azure-cli"></a>使用 Azure CLI 在 SQL 数据库中启用

若要使用 Azure CLI 在 Azure SQL 数据库中启用“仅限 Azure AD 身份验证”，请参阅以下命令。 [安装最新版本的 Azure CLI](/cli/azure/install-azure-cli-windows)。 必须使用 Azure CLI 2.14.2 或更高版本。 有关这些命令的详细信息，请参阅 [az sql server ad-only-auth](/cli/azure/sql/server/ad-only-auth)。

有关使用 API 管理“仅限 Azure AD 身份验证”的详细信息，请参阅[使用 API 管理“仅限 Azure AD 身份验证”](authentication-azure-ad-only-authentication.md#managing-azure-ad-only-authentication-using-apis)。

> [!NOTE]
> 在启用“仅限 Azure AD 身份验证”之前必须为服务器设置 Azure AD 管理员。 否则 Azure CLI 命令将会失败。
>
> 有关用户需要哪些权限和操作才能执行这些命令来启用“仅限 Azure AD 身份验证”的信息，请参阅[仅限 Azure AD 身份验证](authentication-azure-ad-only-authentication.md#permissions)一文。

1. 使用具有 [SQL 安全管理者](../../role-based-access-control/built-in-roles.md#sql-security-manager)角色的帐户[登录到 Azure](/cli/azure/authenticate-azure-cli)。

   ```azurecli
   az login
   ```

1. 运行以下命令（请将其中的 `<myserver>` 替换为你的 SQL 服务器名称，将 `<myresource>` 替换为承载该 SQL 服务器的 Azure 资源）。

   ```azurecli
   az sql server ad-only-auth enable --resource-group <myresource> --name <myserver>
   ```

## <a name="enable-in-sql-managed-instance-using-azure-cli"></a>使用 Azure CLI 在 SQL 托管实例中启用

若要使用 Azure CLI 在 Azure SQL 托管实例中启用“仅限 Azure AD 身份验证”，请参阅以下命令。 [安装最新版本的 Azure CLI](/cli/azure/install-azure-cli-windows)。 

1. 使用具有 [SQL 安全管理者](../../role-based-access-control/built-in-roles.md#sql-security-manager)角色的帐户[登录到 Azure](/cli/azure/authenticate-azure-cli)。

   ```azurecli
   az login
   ```

1. 运行以下命令（请将其中的 `<myserver>` 替换为你的 SQL 服务器名称，将 `<myresource>` 替换为承载该 SQL 服务器的 Azure 资源）。

   ```azurecli
   az sql mi ad-only-auth enable --resource-group <myresource> --name <myserver>
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

## <a name="enable-in-sql-database-using-powershell"></a>使用 PowerShell 在 SQL 数据库中启用

若要使用 PowerShell 在 Azure SQL 数据库中启用“仅限 Azure AD 身份验证”，请参阅以下命令。 需使用 [Az.Sql 2.10.0](https://www.powershellgallery.com/packages/Az.Sql/2.10.0) 模块或更高版本来执行这些命令。 有关这些命令的详细信息，请参阅 [Enable-AzSqlInstanceActiveDirectoryOnlyAuthentication](/powershell/module/az.sql/enable-azsqlinstanceactivedirectoryonlyauthentication)。

有关使用 API 管理“仅限 Azure AD 身份验证”的详细信息，请参阅[使用 API 管理“仅限 Azure AD 身份验证”](authentication-azure-ad-only-authentication.md#managing-azure-ad-only-authentication-using-apis)

> [!NOTE]
> 在启用“仅限 Azure AD 身份验证”之前必须为服务器设置 Azure AD 管理员。 否则 PowerShell 命令将会失败。
>
> 有关用户需要哪些权限和操作才能执行这些命令来启用“仅限 Azure AD 身份验证”的信息，请参阅[仅限 Azure AD 身份验证](authentication-azure-ad-only-authentication.md#permissions)一文。 如果用户的权限不足，将出现以下错误：
>
> ```output
> Enable-AzSqlServerActiveDirectoryOnlyAuthentication : The client
> 'UserSqlServerContributor@contoso.onmicrosoft.com' with object id
> '<guid>' does not have authorization to perform
> action 'Microsoft.Sql/servers/azureADOnlyAuthentications/write' over scope
> '/subscriptions/<guid>...'
> ```

1. 使用具有 [SQL 安全管理者](../../role-based-access-control/built-in-roles.md#sql-security-manager)角色的帐户[登录到 Azure](/powershell/azure/authenticate-azureps)。

   ```powershell
   Connect-AzAccount
   ```

1. 运行以下命令（请将其中的 `<myserver>` 替换为你的 SQL 服务器名称，将 `<myresource>` 替换为承载该 SQL 服务器的 Azure 资源）。

   ```powershell
   Enable-AzSqlServerActiveDirectoryOnlyAuthentication -ServerName <myserver>  -ResourceGroupName <myresource>
   ```

## <a name="enable-in-sql-managed-instance-using-powershell"></a>使用 PowerShell 在 SQL 托管实例中启用

若要使用 PowerShell 在 Azure SQL 托管实例中启用“仅限 Azure AD 身份验证”，请参阅以下命令。 需使用 [Az.Sql 2.10.0](https://www.powershellgallery.com/packages/Az.Sql/2.10.0) 模块或更高版本来执行这些命令。 

有关使用 API 管理“仅限 Azure AD 身份验证”的详细信息，请参阅[使用 API 管理“仅限 Azure AD 身份验证”](authentication-azure-ad-only-authentication.md#managing-azure-ad-only-authentication-using-apis)。


1. 使用具有 [SQL 安全管理者](../../role-based-access-control/built-in-roles.md#sql-security-manager)角色的帐户[登录到 Azure](/powershell/azure/authenticate-azureps)。

   ```powershell
   Connect-AzAccount
   ```

1. 运行以下命令（请将其中的 `<myinstance>` 替换为你的 SQL 托管实例名称，将 `<myresource>` 替换为承载该 SQL 托管实例的 Azure 资源）。

   ```powershell
   Enable-AzSqlInstanceActiveDirectoryOnlyAuthentication -InstanceName <myinstance> -ResourceGroupName <myresource>
   ```

---

## <a name="check-the-azure-ad-only-authentication-status"></a>检查“仅限 Azure AD 身份验证”状态

检查是否为服务器或实例启用了“仅限 Azure AD 身份验证”。

# <a name="portal"></a>[Portal](#tab/azure-portal)

在 [Azure 门户](https://portal.azure.com/)中转到你的 SQL 服务器资源。 在“设置”菜单下选择“Azure Active Directory” 。 只为 Azure SQL 数据库提供了对“仅限 Azure AD 身份验证”的门户支持。

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

这些命令可用于检查是否为 Azure SQL 数据库或 SQL 托管实例的[逻辑服务器](logical-servers.md)启用了仅 Azure AD 身份验证。 [SQL Server 参与者](../../role-based-access-control/built-in-roles.md#sql-server-contributor)和 [SQL 托管实例参与者](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor)角色的成员可以使用这些命令检查“仅限 Azure AD 身份验证”的状态，但不能启用或禁用该功能。

## <a name="check-status-in-sql-database"></a>检查 SQL 数据库中的状态

1. 使用具有 [SQL 安全管理者](../../role-based-access-control/built-in-roles.md#sql-security-manager)角色的帐户[登录到 Azure](/cli/azure/authenticate-azure-cli)。 有关使用 API 管理“仅限 Azure AD 身份验证”的详细信息，请参阅[使用 API 管理“仅限 Azure AD 身份验证”](authentication-azure-ad-only-authentication.md#managing-azure-ad-only-authentication-using-apis)

   ```azurecli
   az login
   ```

1. 运行以下命令（请将其中的 `<myserver>` 替换为你的 SQL 服务器名称，将 `<myresource>` 替换为承载该 SQL 服务器的 Azure 资源）。

   ```azurecli
   az sql server ad-only-auth get --resource-group <myresource> --name <myserver>
   ```

1. 应会看到以下输出：

   ```json
   {
    "azureAdOnlyAuthentication": true,
    "/subscriptions/<guid>/resourceGroups/mygroup/providers/Microsoft.Sql/servers/myserver/azureADOnlyAuthentications/Default",
    "name": "Default",
    "resourceGroup": "myresource",
    "type": "Microsoft.Sql/servers"
   }
   ```

## <a name="check-status-in-sql-managed-instance"></a>检查 SQL 托管实例中的状态

1. 使用具有 [SQL 安全管理者](../../role-based-access-control/built-in-roles.md#sql-security-manager)角色的帐户[登录到 Azure](/cli/azure/authenticate-azure-cli)。

   ```azurecli
   az login
   ```

1. 运行以下命令（请将其中的 `<myserver>` 替换为你的 SQL 服务器名称，将 `<myresource>` 替换为承载该 SQL 服务器的 Azure 资源）。

   ```azurecli
   az sql mi ad-only-auth get --resource-group <myresource> --name <myserver>
   ```

1. 应会看到以下输出：

   ```json
   {
    "azureAdOnlyAuthentication": true,
    "id": "/subscriptions/<guid>/resourceGroups/myresource/providers/Microsoft.Sql/managedInstances/myinstance/azureADOnlyAuthentications/Default",
    "name": "Default",
    "resourceGroup": "myresource",
    "type": "Microsoft.Sql/managedInstances"
   }
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

这些命令可用于检查是否为 Azure SQL 数据库或 SQL 托管实例的[逻辑服务器](logical-servers.md)启用了仅 Azure AD 身份验证。 [SQL Server 参与者](../../role-based-access-control/built-in-roles.md#sql-server-contributor)和 [SQL 托管实例参与者](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor)角色的成员可以使用这些命令检查“仅限 Azure AD 身份验证”的状态，但不能启用或禁用该功能。

如果已启用该功能，则状态将返回 True；如果已禁用，则返回 False 。

## <a name="check-status-in-sql-database"></a>检查 SQL 数据库中的状态

1. 使用具有 [SQL 安全管理者](../../role-based-access-control/built-in-roles.md#sql-security-manager)角色的帐户[登录到 Azure](/powershell/azure/authenticate-azureps)。 有关使用 API 管理“仅限 Azure AD 身份验证”的详细信息，请参阅[使用 API 管理“仅限 Azure AD 身份验证”](authentication-azure-ad-only-authentication.md#managing-azure-ad-only-authentication-using-apis)

   ```powershell
   Connect-AzAccount
   ```

1. 运行以下命令（请将其中的 `<myserver>` 替换为你的 SQL 服务器名称，将 `<myresource>` 替换为承载该 SQL 服务器的 Azure 资源）。

   ```powershell
   Get-AzSqlServerActiveDirectoryOnlyAuthentication  -ServerName <myserver> -ResourceGroupName <myresource>
   ```

## <a name="check-status-in-sql-managed-instance"></a>检查 SQL 托管实例中的状态

1. 使用具有 [SQL 安全管理者](../../role-based-access-control/built-in-roles.md#sql-security-manager)角色的帐户[登录到 Azure](/powershell/azure/authenticate-azureps)。

   ```powershell
   Connect-AzAccount
   ```

1. 运行以下命令（请将其中的 `<myinstance>` 替换为你的 SQL 托管实例名称，将 `<myresource>` 替换为承载该 SQL 托管实例的 Azure 资源）。

   ```powershell
   Get-AzSqlInstanceActiveDirectoryOnlyAuthentication -InstanceName <myinstance> -ResourceGroupName <myresource>
   ```

---

## <a name="test-sql-authentication-with-connection-failure"></a>测试 SQL 身份验证时连接失败

启用“仅限 Azure AD 身份验证”后，使用 [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) 测试[连接到 SQL 数据库或托管实例](connect-query-ssms.md)。 使用 SQL 身份验证进行连接。

应会看到类似于以下输出的登录失败消息：

```output
Cannot connect to <myserver>.database.windows.net.
Additional information:
  Login failed for user 'username'. Reason: Azure Active Directory only authentication is enabled.
  Please contact your system administrator. (Microsoft SQL Server, Error: 18456)
```

## <a name="disable-azure-ad-only-authentication"></a>禁用“仅限 Azure AD 身份验证”

可以通过禁用“仅限 Azure AD 身份验证”功能来允许执行 Azure SQL 的 SQL 身份验证和 Azure AD 身份验证。

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. 使用具有 [SQL 安全管理者](../../role-based-access-control/built-in-roles.md#sql-security-manager)角色的用户身份转到 [Azure 门户](https://portal.azure.com/)。
1. 转到你的 SQL 服务器资源，然后在“设置”菜单下选择“Azure Active Directory” 。
1. 若要禁用“仅限 Azure AD 身份验证”功能，请取消选中“对此服务器支持仅限 Azure Active Directory 身份验证”复选框，然后保存设置 。

目前不支持在门户中管理 SQL 托管实例的“仅限 Azure AD 身份验证”。

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

## <a name="disable-in-sql-database-using-azure-cli"></a>使用 Azure CLI 在 SQL 数据库中禁用

若要使用 Azure CLI 在 Azure SQL 数据库中禁用“仅限 Azure AD 身份验证”，请参阅以下命令。 

1. 使用具有 [SQL 安全管理者](../../role-based-access-control/built-in-roles.md#sql-security-manager)角色的帐户[登录到 Azure](/cli/azure/authenticate-azure-cli)。

   ```azurecli
   az login
   ```

1. 运行以下命令（请将其中的 `<myserver>` 替换为你的 SQL 服务器名称，将 `<myresource>` 替换为承载该 SQL 服务器的 Azure 资源）。

   ```azurecli
   az sql server ad-only-auth disable --resource-group <myresource> --name <myserver>
   ```

1. 禁用“仅限 Azure AD 身份验证”后，在检查状态时应会看到以下输出：

   ```json
   {
    "azureAdOnlyAuthentication": false,
    "/subscriptions/<guid>/resourceGroups/mygroup/providers/Microsoft.Sql/servers/myserver/azureADOnlyAuthentications/Default",
    "name": "Default",
    "resourceGroup": "myresource",
    "type": "Microsoft.Sql/servers"
   }
   ```

## <a name="disable-in-sql-managed-instance-using-azure-cli"></a>使用 Azure CLI 在 SQL 托管实例中禁用

若要使用 Azure CLI 在 Azure SQL 托管实例中禁用“仅限 Azure AD 身份验证”，请参阅以下命令。 

1. 使用具有 [SQL 安全管理者](../../role-based-access-control/built-in-roles.md#sql-security-manager)角色的帐户[登录到 Azure](/cli/azure/authenticate-azure-cli)。

   ```azurecli
   az login
   ```

1. 运行以下命令（请将其中的 `<myserver>` 替换为你的 SQL 服务器名称，将 `<myresource>` 替换为承载该 SQL 服务器的 Azure 资源）。

   ```azurecli
   az sql mi ad-only-auth disable --resource-group <myresource> --name <myserver>
   ```

1. 禁用“仅限 Azure AD 身份验证”后，在检查状态时应会看到以下输出：

   ```json
   {
    "azureAdOnlyAuthentication": false,
    "id": "/subscriptions/<guid>/resourceGroups/myresource/providers/Microsoft.Sql/managedInstances/myinstance/azureADOnlyAuthentications/Default",
    "name": "Default",
    "resourceGroup": "myresource",
    "type": "Microsoft.Sql/managedInstances"
   }
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

## <a name="disable-in-sql-database-using-powershell"></a>使用 PowerShell 在 SQL 数据库中禁用

若要使用 PowerShell 在 Azure SQL 数据库中禁用“仅限 Azure AD 身份验证”，请参阅以下命令。

1. 使用具有 [SQL 安全管理者](../../role-based-access-control/built-in-roles.md#sql-security-manager)角色的帐户[登录到 Azure](/powershell/azure/authenticate-azureps)。

   ```powershell
   Connect-AzAccount
   ```

1. 运行以下命令（请将其中的 `<myserver>` 替换为你的 SQL 服务器名称，将 `<myresource>` 替换为承载该 SQL 服务器的 Azure 资源）。

   ```powershell
   Disable-AzSqlServerActiveDirectoryOnlyAuthentication -ServerName <myserver>  -ResourceGroupName <myresource>
   ```

## <a name="disable-in-sql-managed-instance-using-powershell"></a>使用 PowerShell 在 SQL 托管实例中禁用

若要使用 PowerShell 在 Azure SQL 托管实例中禁用“仅限 Azure AD 身份验证”，请参阅以下命令。

1. 使用具有 [SQL 安全管理者](../../role-based-access-control/built-in-roles.md#sql-security-manager)角色的帐户[登录到 Azure](/powershell/azure/authenticate-azureps)。

   ```powershell
   Connect-AzAccount
   ```

1. 运行以下命令（请将其中的 `<myinstance>` 替换为你的 SQL 托管实例名称，将 `<myresource>` 替换为承载该 SQL 托管实例的 Azure 资源）。

   ```powershell
   Disable-AzSqlInstanceActiveDirectoryOnlyAuthentication -InstanceName <myinstance> -ResourceGroupName <myresource>
   ```

---

## <a name="test-connecting-to-azure-sql-again"></a>再次测试连接到 Azure SQL

禁用“仅限 Azure AD 身份验证”后，使用 SQL 身份验证登录名测试连接。 现在应该可以连接到你的服务器或实例。

## <a name="next-steps"></a>后续步骤

- [Azure SQL 中的“仅限 Azure AD 身份验证”](authentication-azure-ad-only-authentication.md)
- [在 Azure SQL 中创建启用了仅限 Azure AD 的身份验证的服务器](authentication-azure-ad-only-authentication-create-server.md)
- [使用 Azure Policy 强制 Azure SQL 实施仅 Azure Active Directory 身份验证](authentication-azure-ad-only-authentication-policy-how-to.md)
