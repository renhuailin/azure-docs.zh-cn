---
title: 教程：使用托管标识访问数据
description: 了解如何使用托管标识让数据库连接更安全，以及如何将此方法应用到其他 Azure 服务。
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/27/2020
ms.custom: devx-track-csharp, mvc, cli-validate, devx-track-azurecli
ms.openlocfilehash: 690991f637c33ec4b66fd2bc591deb5474900d57
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128657341"
---
# <a name="tutorial-secure-azure-sql-database-connection-from-app-service-using-a-managed-identity"></a>教程：使用托管标识确保从应用服务进行的 Azure SQL 数据库连接安全

[应用服务](overview.md)在 Azure 中提供高度可缩放、自修补的 Web 托管服务。 它还为应用提供[托管标识](overview-managed-identity.md)，这是一项统包解决方案，可以确保安全地访问 [Azure SQL 数据库](/azure/sql-database/)和其他 Azure 服务。 应用服务中的托管标识可以让应用更安全，因为不需在应用中存储机密，例如连接字符串中的凭据。 在本教程中，你要将托管标识添加到在以下教程之一中生成的示例 Web 应用： 

- [教程：使用 Azure SQL 数据库在 Azure 中生成 ASP.NET 应用](app-service-web-tutorial-dotnet-sqldatabase.md)
- [教程：在 Azure 应用服务中生成 ASP.NET Core 和 Azure SQL 数据库应用](tutorial-dotnetcore-sqldb-app.md)

完成后，示例应用就可以安全地连接到 SQL 数据库，不需用户名和密码。

> [!NOTE]
> 本教程所述的步骤支持以下版本：
> 
> - .NET Framework 4.7.2 和更高版本
> - .NET Core 2.2 和更高版本
>

要学习的知识：

> [!div class="checklist"]
> * 启用托管标识
> * 授予 SQL 数据库访问托管标识的权限
> * 配置实体框架，将 Azure AD 身份验证用于 SQL 数据库
> * 使用 Azure AD 身份验证从 Visual Studio 连接到 SQL 数据库

> [!NOTE]
>在本地 Active Directory 域服务 (AD DS) 中，Azure AD 身份验证不同于[集成式 Windows 身份验证](/previous-versions/windows/it-pro/windows-server-2003/cc758557(v=ws.10))。 AD DS 和 Azure AD 使用的身份验证协议完全不相同。 有关详细信息，请参阅 [Azure AD 域服务文档](../active-directory-domain-services/index.yml)。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

本文是[教程：使用 SQL 数据库在 Azure 中生成 ASP.NET 应用](app-service-web-tutorial-dotnet-sqldatabase.md)或[教程：在 Azure 应用服务中生成 ASP.NET Core 和 SQL 数据库应用](tutorial-dotnetcore-sqldb-app.md)。 请先完成这两篇教程之一（如果尚未完成）。 也可调整这些步骤，使用 SQL 数据库来生成自己的 .NET 应用。

若要使用 SQL 数据库作为后端调试应用程序，请确保已经允许从计算机连接客户端。 否则，请遵循[使用 Azure 门户管理服务器级 IP 防火墙规则](../azure-sql/database/firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules)中的步骤添加客户端 IP。

为 Azure CLI 准备环境。

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="grant-database-access-to-azure-ad-user"></a>向 Azure AD 用户授予数据库访问权限

首先，通过将 Azure AD 用户指定为服务器的 Active Directory 管理员，对 SQL 数据库启用 Azure AD 身份验证。 此用户与用于注册 Azure 订阅的 Microsoft 帐户不同。 它必须是你在 Azure AD 中创建、导入、同步或邀请到其中的用户。 有关允许的 Azure AD 用户的详细信息，请参阅 [SQL 数据库中的 Azure AD 功能和限制](../azure-sql/database/authentication-aad-overview.md#azure-ad-features-and-limitations)。

1. 如果 Azure AD 租户还没有用户，请按照[使用 Azure Active Directory 添加或删除用户](../active-directory/fundamentals/add-users-azure-active-directory.md)中的步骤创建一个用户。

1. 使用 [`az ad user list`](/cli/azure/ad/user#az_ad_user_list) 查找 Azure AD 用户的对象 ID，并替换 \<user-principal-name>。 结果会保存到变量中。

    ```azurecli-interactive
    azureaduser=$(az ad user list --filter "userPrincipalName eq '<user-principal-name>'" --query [].objectId --output tsv)
    ```

    > [!TIP]
    > 若要查看 Azure AD 中所有用户主体名称的列表，请运行 `az ad user list --query [].userPrincipalName`。
    >

1. 使用 Cloud Shell 中的 [`az sql server ad-admin create`](/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_create) 命令，将此 Azure AD 用户添加为 Active Directory 管理员。 在以下命令中，将 \<server-name> 替换为服务器名称（不带 `.database.windows.net` 后缀）。

    ```azurecli-interactive
    az sql server ad-admin create --resource-group myResourceGroup --server-name <server-name> --display-name ADMIN --object-id $azureaduser
    ```

有关添加 Active Directory 管理员的详细信息，请参阅[为服务器预配 Azure Active Directory 管理员](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance)

## <a name="set-up-visual-studio"></a>设置 Visual Studio

# <a name="windows-client"></a>[Windows 客户端](#tab/windowsclient)

1. Visual Studio for Windows 集成了 Azure AD 身份验证。 若要在 Visual Studio 中启用开发和调试，请在 Visual Studio 中添加 Azure AD 用户，方法是从菜单中依次选择“文件” > “帐户设置”，然后单击“添加帐户”  。

1. 若要设置进行 Azure 服务身份验证的 Azure AD 用户，请从菜单中依次选择“工具” > “选项”，然后依次选择“Azure 服务身份验证” > “帐户选择”   。 选择已添加的 Azure AD 用户，然后单击“确定”。

# <a name="macos-client"></a>[macOS 客户端](#tab/macosclient)

1. Visual Studio for Mac 未集成 Azure AD 身份验证。 不过，稍后将使用的 [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) 库可以使用 Azure CLI 中的令牌。 若要在 Visual Studio 中启用开发和调试，请在本地计算机上[安装 Azure CLI](/cli/azure/install-azure-cli)。

1. 使用 Azure AD 用户通过以下命令登录到 Azure CLI：

    ```azurecli
    az login --allow-no-subscriptions
    ```

-----

现已准备好将 SQL 数据库作为后端，使用 Azure AD 身份验证来开发和调试应用程序。

## <a name="modify-your-project"></a>修改项目

要对项目执行的步骤取决于它是 ASP.NET 项目还是 ASP.NET Core 项目。

# <a name="aspnet"></a>[ASP.NET](#tab/dotnet)

1. 在 Visual Studio 中，打开包管理器控制台，并添加 NuGet 包 [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)：

    ```powershell
    Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.4.0
    ```

1. 在 Web.config 中，从文件顶部开始工作并进行以下更改：

    - 在 `<configSections>` 中，添加以下部分声明：
    
        ```xml
        <section name="SqlAuthenticationProviders" type="System.Data.SqlClient.SqlAuthenticationProviderConfigurationSection, System.Data, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" />
        ```
    
    - 在 `</configSections>` 结束标记下方，为 `<SqlAuthenticationProviders>` 添加以下 XML 代码。
    
        ```xml
        <SqlAuthenticationProviders>
          <providers>
            <add name="Active Directory Interactive" type="Microsoft.Azure.Services.AppAuthentication.SqlAppAuthenticationProvider, Microsoft.Azure.Services.AppAuthentication" />
          </providers>
        </SqlAuthenticationProviders>
        ```    
    
    - 找到名为 `MyDbConnection` 的连接字符串，并将其 `connectionString` 值替换为 `"server=tcp:<server-name>.database.windows.net;database=<db-name>;UID=AnyString;Authentication=Active Directory Interactive"`。 将 \<server-name> 和 \<db-name> 替换为你的服务器名称和数据库名称 。
    
    > [!NOTE]
    > 刚注册的 SqlAuthenticationProvider 基于之前安装的 AppAuthentication 库。 默认情况下，它使用系统分配的标识。 若要利用用户分配的标识，需提供其他配置。 请参阅针对 AppAuthentication 库的[连接字符串支持](/dotnet/api/overview/azure/service-to-service-authentication#connection-string-support)。

    这就是连接到 SQL 数据库所要完成的所有准备工作。 在 Visual Studio 中调试时，代码将使用[设置 Visual Studio](#set-up-visual-studio) 中配置的 Azure AD 用户。 稍后你将设置 SQL 数据库，以允许“应用服务”应用的托管标识建立连接。

1. 键入 `Ctrl+F5`，再次运行该应用。 浏览器中相同的 CRUD 应用程序现使用 Azure AD 身份验证直接连接到 Azure SQL 数据库。 此设置使你能够从 Visual Studio 运行数据库迁移。

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/dotnetcore)

> [!NOTE]
> Microsoft.Azure.Services.AppAuthentication 不再被推荐用于新的 Azure SDK。 已将其替换为新的 Azure 标识客户端库（可用于 .NET、Java、TypeScript 和 Python），应将此库用于所有新的开发。 有关如何迁移到 `Azure Identity` 的信息，请参阅：[AppAuthentication 到 Azure.Identity 的迁移指南](/dotnet/api/overview/azure/app-auth-migration)。

1. 在 Visual Studio 中，打开包管理器控制台，并添加 NuGet 包 [Azure.Identity](https://www.nuget.org/packages/Azure.Identity)：

    ```powershell
    Install-Package Microsoft.Data.SqlClient -Version 2.1.2
    Install-Package Azure.Identity -Version 1.4.0
    ```

1. [ASP.NET Core 和 SQL 数据库教程](tutorial-dotnetcore-sqldb-app.md)中完全未使用 `MyDbConnection` 连接字符串，因为本地开发环境使用 Sqlite 数据库文件，而 Azure 生产环境使用应用服务中的连接字符串。 使用 Active Directory 身份验证时，最好是让这两种环境使用相同的连接字符串。 在 *appsettings.json* 中，请将 `MyDbConnection` 连接字符串的值替换为：

    ```json
    "Server=tcp:<server-name>.database.windows.net;Authentication=Active Directory Device Code Flow; Database=<database-name>;"
    ```

    > [!NOTE]
    > 使用 `Active Directory Device Code Flow` 身份验证类型，因为这是能够选择的最接近自定义选项的类型。 理想情况下，会提供 `Custom Authentication` 类型。 当前没有更好的术语可以使用，因此将使用 `Device Code Flow`。
    >

1. 接下来，需要创建自定义身份验证提供程序类，以便使用 SQL 数据库的访问令牌获取和提供实体框架数据库上下文。 在 Data\\ 目录中，添加新类 `CustomAzureSQLAuthProvider.cs`，其中包含以下代码：

    ```csharp
    public class CustomAzureSQLAuthProvider : SqlAuthenticationProvider
    {
        private static readonly string[] _azureSqlScopes = new[]
        {
            "https://database.windows.net//.default"
        };
    
        private static readonly TokenCredential _credential = new DefaultAzureCredential();
    
        public override async Task<SqlAuthenticationToken> AcquireTokenAsync(SqlAuthenticationParameters parameters)
        {
            var tokenRequestContext = new TokenRequestContext(_azureSqlScopes);
            var tokenResult = await _credential.GetTokenAsync(tokenRequestContext, default);
            return new SqlAuthenticationToken(tokenResult.Token, tokenResult.ExpiresOn);
        }
    
        public override bool IsSupported(SqlAuthenticationMethod authenticationMethod) => authenticationMethod.Equals(SqlAuthenticationMethod.ActiveDirectoryDeviceCodeFlow);
    }
    ```

1. 在 Startup.cs 中，使用以下代码更新 `ConfigureServices()` 方法：

    ```csharp
    services.AddControllersWithViews();
    services.AddDbContext<MyDatabaseContext>(options =>
    {
        SqlAuthenticationProvider.SetProvider(
            SqlAuthenticationMethod.ActiveDirectoryDeviceCodeFlow, 
            new CustomAzureSQLAuthProvider());
        var sqlConnection = new SqlConnection(Configuration.GetConnectionString("MyDbConnection"));
        options.UseSqlServer(sqlConnection);
    });
    ```

    > [!NOTE]
    > 为清楚和简单起见，此演示代码是同步的。
    
    前面的代码使用 `Azure.Identity` 库，因此无论代码在何处运行，都可以对数据库进行身份验证和检索访问令牌。 如果在本地计算机上运行，则 `DefaultAzureCredential()` 会循环访问多个选项，以查找登录的有效帐户。 可以详细了解 [DefaultAzureCredential 类](/dotnet/api/azure.identity.defaultazurecredential)。

    这就是连接到 SQL 数据库所要完成的所有准备工作。 在 Visual Studio 中调试时，代码将使用[设置 Visual Studio](#set-up-visual-studio) 中配置的 Azure AD 用户。 稍后你将设置 SQL 数据库，以允许“应用服务”应用的托管标识建立连接。 `DefaultAzureCredential` 类将令牌缓存在内存中，在过期前才将其从 Azure AD 检索出来。 不需要任何自定义代码就可以刷新该令牌。

1. 键入 `Ctrl+F5`，再次运行该应用。 浏览器中相同的 CRUD 应用程序现使用 Azure AD 身份验证直接连接到 Azure SQL 数据库。 此设置使你能够从 Visual Studio 运行数据库迁移。

-----

## <a name="use-managed-identity-connectivity"></a>使用托管标识连接性

接下来，配置应用服务应用，使其使用系统分配的托管标识连接到 SQL 数据库。

> [!NOTE]
> 虽然本部分的说明适用于系统分配的标识，但用户分配的标识同样可以很容易地使用。 为此， 需更改 `az webapp identity assign command`，以便分配所需的用户分配标识。 然后，在创建 SQL 用户时，请确保使用用户分配的标识资源的名称，而不是站点名称。

### <a name="enable-managed-identity-on-app"></a>在应用上启用托管标识

若要为 Azure 应用启用托管标识，请在 Cloud Shell 中使用 [az webapp identity assign](/cli/azure/webapp/identity#az_webapp_identity_assign) 命令。 在以下命令中，替换 \<app-name>。

```azurecli-interactive
az webapp identity assign --resource-group myResourceGroup --name <app-name>
```

> [!NOTE]
> 若要为[部署槽位](deploy-staging-slots.md)启用托管标识，请添加 `--slot <slot-name>`，并在 \<slot-name> 中使用槽名称。

这是一个输出示例：

<pre>
{
  "additionalProperties": {},
  "principalId": "21dfa71c-9e6f-4d17-9e90-1d28801c9735",
  "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47",
  "type": "SystemAssigned"
}
</pre>

### <a name="grant-permissions-to-managed-identity"></a>向托管标识授予权限

> [!NOTE]
> 如果需要，可将该标识添加到 [Azure AD 组](../active-directory/fundamentals/active-directory-manage-groups.md)，然后将 SQL 数据库访问权限授予 Azure AD 组，而不是授予该标识。 例如，以下命令将上一步中的托管标识添加到名为 _myAzureSQLDBAccessGroup_ 的新组：
> 
> ```azurecli-interactive
> groupid=$(az ad group create --display-name myAzureSQLDBAccessGroup --mail-nickname myAzureSQLDBAccessGroup --query objectId --output tsv)
> msiobjectid=$(az webapp identity show --resource-group myResourceGroup --name <app-name> --query principalId --output tsv)
> az ad group member add --group $groupid --member-id $msiobjectid
> az ad group member list -g $groupid
> ```
>

1. 在 Cloud Shell 中，使用 SQLCMD 命令登录到 SQL 数据库。 将 \<server-name> 替换为你的服务器名称，将 \<db-name> 替换为应用使用的数据库的名称，并将 \<aad-user-name> 和 \<aad-password> 替换为 Azure AD 用户的凭据   。

    ```bash
    sqlcmd -S <server-name>.database.windows.net -d <db-name> -U <aad-user-name> -P "<aad-password>" -G -l 30
    ```

1. 在所需数据库的 SQL 提示符下运行以下命令，以授予应用所需的权限。 例如， 

    ```sql
    CREATE USER [<identity-name>] FROM EXTERNAL PROVIDER;
    ALTER ROLE db_datareader ADD MEMBER [<identity-name>];
    ALTER ROLE db_datawriter ADD MEMBER [<identity-name>];
    ALTER ROLE db_ddladmin ADD MEMBER [<identity-name>];
    GO
    ```

    \<identity-name> 是 Azure AD 中托管标识的名称。 如果标识是系统分配的，则此名称始终与应用服务应用的名称相同。 对于[部署槽位](deploy-staging-slots.md)，其系统分配的标识的名称为 \<app-name>/slots/\<slot-name>。 若要为 Azure AD 组授予权限，请改为使用组的显示名称（例如，*myAzureSQLDBAccessGroup*）。

1. 键入 `EXIT`，返回到 Cloud Shell 提示符窗口。

    > [!NOTE]
    > 托管标识的后端服务还[保留一个令牌缓存](overview-managed-identity.md#obtain-tokens-for-azure-resources)，只有在过期的情况下才更新目标资源的令牌。 如果在配置 SQL 数据库权限时出错，并尝试在使用应用获取令牌后修改权限，则在缓存的令牌过期之前，实际上不会获得具有更新权限的新令牌。

    > [!NOTE]
    > 本地 SQL Server 不支持 Azure Active Directory 和托管标识。 

### <a name="modify-connection-string"></a>修改连接字符串

请记住，在 *Web.config* 或 *appsettings.json* 中所做的相同更改适用于托管标识，因此只需删除应用服务中的现有连接字符串，该字符串由 Visual Studio 在首次部署应用时创建。 使用以下命令，但将 \<app-name> 替换为应用的名称。

```azurecli-interactive
az webapp config connection-string delete --resource-group myResourceGroup --name <app-name> --setting-names MyDbConnection
```

## <a name="publish-your-changes"></a>发布更改

现在，剩下的操作是将更改发布到 Azure。

# <a name="aspnet"></a>[ASP.NET](#tab/dotnet)

1. **如果你是在学完 [教程：使用 SQL 数据库在 Azure 中生成 ASP.NET 应用](app-service-web-tutorial-dotnet-sqldatabase.md)** 后转到本教程，请在 Visual Studio 中发布更改。 在“解决方案资源管理器”中，右键单击 “DotNetAppSqlDb”项目，然后选择“发布”。

    ![从解决方案资源管理器发布](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

1. 在发布页中单击“发布”。 

    > [!IMPORTANT]
    > 确保应用服务的名称与任何现有的[应用注册](../active-directory/manage-apps/add-application-portal.md)都不匹配。 这会导致主体 ID 冲突。

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/dotnetcore)

**如果你是在学完 [教程：在 Azure 应用服务中生成 ASP.NET Core 和 SQL 数据库应用](tutorial-dotnetcore-sqldb-app.md)** 后转到本教程，请运行以下命令使用 Git 发布更改：

```bash
git commit -am "configure managed identity"
git push azure main
```

-----

当新网页显示待办事项列表时，表明应用使用了托管标识连接到数据库。

![Code First 迁移后的 Azure 应用](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

现在应该可以像以前一样编辑待办事项列表了。

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>后续步骤

你已了解：

> [!div class="checklist"]
> * 启用托管标识
> * 授予 SQL 数据库访问托管标识的权限
> * 配置实体框架，将 Azure AD 身份验证用于 SQL 数据库
> * 使用 Azure AD 身份验证从 Visual Studio 连接到 SQL 数据库

转到下一教程，了解如何向 Web 应用映射自定义 DNS 名称。

> [!div class="nextstepaction"]
> [将现有的自定义 DNS 名称映射到 Azure 应用服务](app-service-web-tutorial-custom-domain.md)
