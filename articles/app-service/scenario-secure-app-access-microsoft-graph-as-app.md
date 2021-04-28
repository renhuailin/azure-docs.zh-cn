---
title: 教程 - Web 应用以应用身份访问 Microsoft Graph | Azure
description: 本教程介绍如何使用托管标识访问 Microsoft Graph 中的数据。
services: microsoft-graph, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 01/28/2021
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1, devx-track-azurepowershell
ms.openlocfilehash: 5bb52799836b1975de9d936e04fb53987effb300
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832619"
---
# <a name="tutorial-access-microsoft-graph-from-a-secured-app-as-the-app"></a>教程：以应用身份从安全的应用访问 Microsoft Graph

了解如何从 Azure 应用服务上运行的 Web 应用访问 Microsoft Graph。

:::image type="content" alt-text="显示如何访问 Microsoft Graph 流的示意图。" source="./media/scenario-secure-app-access-microsoft-graph/web-app-access-graph.svg" border="false":::

你希望为 Web 应用调用 Microsoft Graph。 向 Web 应用授予数据访问权限的安全方法是使用[系统分配的托管标识](../active-directory/managed-identities-azure-resources/overview.md)。 Azure Active Directory 中的托管标识允许应用服务通过基于角色的访问控制 (RBAC) 访问资源，而不要求使用应用凭据。 向 Web 应用分配托管标识之后，Azure 会负责创建和分发证书。 你无需费心管理机密或应用凭据。

在本教程中，你将了解：

> [!div class="checklist"]
>
> * 在 Web 应用上创建系统分配的托管标识。
> * 为托管标识添加 Microsoft Graph API 权限。
> * 使用托管标识从 Web 应用调用 Microsoft Graph。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

* 在[启用了应用服务身份验证/授权模块](scenario-secure-app-authentication-app-service.md)的 Azure 应用服务上运行的 Web 应用程序。

## <a name="enable-managed-identity-on-app"></a>在应用上启用托管标识

如果通过 Visual Studio 创建和发布 Web 应用，则已在应用上启用了托管标识。 在应用服务中，在左侧窗格中选择“标识”，然后选择“系统分配” 。 验证“状态”是否设置为“打开” 。 如果不是，请依次选择“保存”和“是”以启用系统分配的托管标识 。 启用托管标识后，状态将设置为“启用”并且对象 ID 可用。

记下“对象 ID”值，下一步骤需要使用此值。

:::image type="content" alt-text="显示系统分配的标识的屏幕截图。" source="./media/scenario-secure-app-access-microsoft-graph/create-system-assigned-identity.png":::

## <a name="grant-access-to-microsoft-graph"></a>授予对 Microsoft Graph 的访问权限

在访问 Microsoft Graph 时，对于要执行的操作，托管标识需要具有适当的权限。 目前，没有通过 Azure 门户分配此类权限的选项。 下面的脚本将向托管标识服务主体对象添加请求的 Microsoft Graph API 权限。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
# Install the module. (You need admin on the machine.)
# Install-Module AzureAD.

# Your tenant ID (in the Azure portal, under Azure Active Directory > Overview).
$TenantID="<tenant-id>"
$resourceGroup = "securewebappresourcegroup"
$webAppName="SecureWebApp-20201102125811"

# Get the ID of the managed identity for the web app.
$spID = (Get-AzWebApp -ResourceGroupName $resourceGroup -Name $webAppName).identity.principalid

# Check the Microsoft Graph documentation for the permission you need for the operation.
$PermissionName = "User.Read.All"

Connect-AzureAD -TenantId $TenantID

# Get the service principal for Microsoft Graph.
# First result should be AppId 00000003-0000-0000-c000-000000000000
$GraphServicePrincipal = Get-AzureADServicePrincipal -SearchString "Microsoft Graph" | Select-Object -first 1

# Assign permissions to the managed identity service principal.
$AppRole = $GraphServicePrincipal.AppRoles | `
Where-Object {$_.Value -eq $PermissionName -and $_.AllowedMemberTypes -contains "Application"}

New-AzureAdServiceAppRoleAssignment -ObjectId $spID -PrincipalId $spID `
-ResourceId $GraphServicePrincipal.ObjectId -Id $AppRole.Id
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az login

webAppName="SecureWebApp-20201106120003"

spId=$(az resource list -n $webAppName --query [*].identity.principalId --out tsv)

graphResourceId=$(az ad sp list --display-name "Microsoft Graph" --query [0].objectId --out tsv)

appRoleId=$(az ad sp list --display-name "Microsoft Graph" --query "[0].appRoles[?value=='User.Read.All' && contains(allowedMemberTypes, 'Application')].id" --output tsv)

uri=https://graph.microsoft.com/v1.0/servicePrincipals/$spId/appRoleAssignments

body="{'principalId':'$spId','resourceId':'$graphResourceId','appRoleId':'$appRoleId'}"

az rest --method post --uri $uri --body $body --headers "Content-Type=application/json"
```

---

执行该脚本后，可在 [Azure 门户](https://portal.azure.com)中验证是否已将请求的 API 权限分配给托管标识。

转到“Azure Active Directory”，然后选择“企业应用程序” 。 此窗格显示租户中的所有服务主体。 在“所有应用程序”中，选择托管标识的服务主体。 

如果遵循本教程，则有两个具有相同显示名称（例如“SecureWebApp2020094113531”）的服务主体。 具有主页 URL 的服务主体表示租户中的 Web 应用。 没有主页 URL 的服务主体表示 Web 应用的系统分配的托管标识。 托管标识的对象 ID 值与之前创建的托管标识的对象 ID 匹配。

选择托管标识的服务主体。

:::image type="content" alt-text="显示“所有应用程序”选项的屏幕截图。" source="./media/scenario-secure-app-access-microsoft-graph/enterprise-apps-all-applications.png":::

在“概述”中选择“权限”，你将看到添加的 Microsoft Graph 权限 。

:::image type="content" alt-text="显示“权限”窗格的屏幕截图。" source="./media/scenario-secure-app-access-microsoft-graph/enterprise-apps-permissions.png":::

## <a name="call-microsoft-graph-net"></a>调用 Microsoft Graph (.NET)

[DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) 类用于获取代码的令牌凭据，以对 Microsoft Graph 的请求进行授权。 创建 [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) 类的实例，该类使用托管标识提取令牌并将其附加到服务客户端。 下面的代码示例获取经过身份验证的令牌凭据，并使用它创建服务客户端对象，该对象将获取组中的用户。

若要查看作为示例应用程序一部分的代码，请参阅 [GitHub 上的示例](https://github.com/Azure-Samples/ms-identity-easyauth-dotnet-storage-graphapi/tree/main/3-WebApp-graphapi-managed-identity)。

### <a name="install-the-microsoftidentitywebmicrosoftgraph-client-library-package"></a>安装 Microsoft.Identity.Web.MicrosoftGraph 客户端库包

使用 .NET Core 命令行接口或 Visual Studio 中的包管理器控制台，在项目中安装 [Microsoft.Identity.Web.MicrosoftGraph NuGet 包](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph)。

# <a name="command-line"></a>[命令行](#tab/command-line)

打开一个命令行，并切换到包含项目文件的目录。

运行安装命令。

```dotnetcli
dotnet add package Microsoft.Identity.Web.MicrosoftGraph
```

# <a name="package-manager"></a>[包管理器](#tab/package-manager)

在 Visual Studio 中打开项目/解决方案，并使用“工具” > “NuGet 包管理器” > “包管理器控制台”命令打开控制台  。

运行安装命令。
```powershell
Install-Package Microsoft.Identity.Web.MicrosoftGraph
```

---

### <a name="example"></a>示例

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc.RazorPages;
using Azure.Identity;
using Microsoft.Graph.Core;
using System.Net.Http.Headers;

...

public IList<MSGraphUser> Users { get; set; }

public async Task OnGetAsync()
{
    // Create the Microsoft Graph service client with a DefaultAzureCredential class, which gets an access token by using the available Managed Identity.
    var credential = new DefaultAzureCredential();
    var token = credential.GetToken(
        new Azure.Core.TokenRequestContext(
            new[] { "https://graph.microsoft.com/.default" }));

    var accessToken = token.Token;
    var graphServiceClient = new GraphServiceClient(
        new DelegateAuthenticationProvider((requestMessage) =>
        {
            requestMessage
            .Headers
            .Authorization = new AuthenticationHeaderValue("bearer", accessToken);

            return Task.CompletedTask;
        }));

    List<MSGraphUser> msGraphUsers = new List<MSGraphUser>();
    try
    {
        var users =await graphServiceClient.Users.Request().GetAsync();
        foreach(var u in users)
        {
            MSGraphUser user = new MSGraphUser();
            user.userPrincipalName = u.UserPrincipalName;
            user.displayName = u.DisplayName;
            user.mail = u.Mail;
            user.jobTitle = u.JobTitle;

            msGraphUsers.Add(user);
        }
    }
    catch(Exception ex)
    {
        string msg = ex.Message;
    }

    Users = msGraphUsers;
}
```

## <a name="clean-up-resources"></a>清理资源

如果已完成本教程，并且不再需要 Web 应用或相关资源，请[清理创建的资源](scenario-secure-app-clean-up-resources.md)。

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何执行以下操作：

> [!div class="checklist"]
>
> * 在 Web 应用上创建系统分配的托管标识。
> * 为托管标识添加 Microsoft Graph API 权限。
> * 使用托管标识从 Web 应用调用 Microsoft Graph。

了解如何将 [.NET Core 应用](tutorial-dotnetcore-sqldb-app.md)、[Python 应用](tutorial-python-postgresql-app.md)、[Java 应用](tutorial-java-spring-cosmosdb.md)或 [Node.js 应用](tutorial-nodejs-mongodb-app.md)连接到数据库。
