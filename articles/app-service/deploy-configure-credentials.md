---
title: 配置部署凭据
description: 了解 Azure 应用服务中有哪些类型的部署凭据，以及如何配置和使用这些凭据。
ms.topic: article
ms.date: 02/11/2021
ms.reviewer: byvinyal
ms.custom: seodec18
ms.openlocfilehash: 2a53ecb1b3411561da50f7dbf3be79f9d70b42bc
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100560425"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>为 Azure 应用服务配置部署凭据
若要从本地计算机保护应用部署， [Azure App Service](./overview.md) 支持两种类型的凭据用于 [本地 Git 部署](deploy-local-git.md) 和 [FTP/S 部署](deploy-ftp.md)。 这些凭据与 Azure 订阅凭据不同。

[!INCLUDE [app-service-deploy-credentials](../../includes/app-service-deploy-credentials.md)]

## <a name="configure-user-scope-credentials"></a><a name="userscope"></a>配置用户范围凭据

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

运行 [az webapp deployment user set](/cli/azure/webapp/deployment/user#az-webapp-deployment-user-set) 命令。 将 \<username> 和 \<password> 替换为部署用户的用户名和密码。 

- 用户名在 Azure 中必须唯一，并且为了本地Git推送，不能包含“@”符号。 
- 密码必须至少为 8 个字符，且具有字母、数字和符号这三种元素中的两种。 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

JSON 输出会将该密码显示为 `null`。

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

不能 Azure PowerShell 配置用户范围凭据。 使用其他方法，或考虑 [使用应用程序范围的凭据](#appscope)。 

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

可以在任何应用的 [资源页](../azure-resource-manager/management/manage-resources-portal.md#manage-resources)中配置用户范围凭据。 无论在哪个应用中配置这些凭据，它都适用于 Azure 帐户中所有订阅的所有应用。 

在 [Azure 门户](https://portal.azure.com)中，必须至少有一个应用，才能访问 "部署凭据" 页面。 配置用户范围凭据：

1. 在应用程序的左侧菜单中，选择 ">**部署中心**  >  **FTPS 凭据**" 或 "**本地 Git/FTPS 凭据**"。

    ![演示如何从 Azure 应用服务的部署中心选择 FTP 仪表板。](./media/app-service-deployment-credentials/access-no-git.png)

2. 向下滚动到 " **用户范围**"，配置 **用户名** 和 **密码**，然后选择 " **保存**"。

设置部署凭据后，可以在应用的“概述”页中找到 Git 部署用户名，

![演示如何在应用“概述”页上查找 Git 部署用户名。](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

如果配置了 Git 部署，则该页显示 **Git/部署用户名**；否则，显示 **FTP/部署用户名**。

> [!NOTE]
> Azure 不会显示用户范围的部署密码。 如果忘记密码，可以按照本部分的步骤重置凭据。
>
> 

-----

## <a name="use-user-scope-credentials-with-ftpftps"></a>通过 FTP/FTPS 使用用户范围凭据

使用用户范围凭据对 FTP/FTPS 终结点进行身份验证时，需要使用以下格式的用户名： `<app-name>\<user-name>`

由于用户范围凭据链接到用户，而不是特定资源，因此用户名必须采用以下格式才能将登录操作定向到正确的应用终结点。

## <a name="get-application-scope-credentials"></a><a name="appscope"></a>获取应用程序范围凭据

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

使用 [az webapp deployment list-发布-](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_profiles) profile 命令获取应用程序范围的凭据。 例如：

```azurecli-interactive
az webapp deployment list-publishing-profiles --resource-group <group-name> --name <app-name>
```

对于 [本地 Git 部署](deploy-local-git.md)，还可以使用 [az webapp deployment list-发布-凭据](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_credentials) 命令获取应用的 Git 远程 URI，其中包含已嵌入的应用程序范围凭据。 例如：

```azurecli-interactive
az webapp deployment list-publishing-credentials --resource-group <group-name> --name <app-name> --query scmUri
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

使用 [AzWebAppPublishingProfile](/powershell/module/az.websites/get-azwebapppublishingprofile) 命令获取应用程序范围的凭据。 例如：

```azurepowershell-interactive
Get-AzWebAppPublishingProfile -ResourceGroupName <group-name> -Name <app-name>
```

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

1. 在应用程序的左侧菜单中，选择 "**部署中心**  >  **FTPS 凭据**" 或 "**本地 Git/FTPS 凭据**"。

    ![演示如何从 Azure 应用服务的部署中心选择 FTP 仪表板。](./media/app-service-deployment-credentials/access-no-git.png)

2. 在 " **应用程序范围** " 部分中，选择 " **复制** " 链接以复制用户名或密码。

-----

## <a name="reset-application-scope-credentials"></a>重置应用程序范围凭据

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

使用 [az resource invoke](/cli/azure/resource#az_resource_invoke_action) 命令重置应用程序范围的凭据：

```azurecli-interactive
az resource invoke-action --action newpassword --resource-group <group-name> --name <app-name> --resource-type Microsoft.Web/sites
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

使用 [AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) 命令重置应用程序范围的凭据：

```azurepowershell-interactive
Invoke-AzResourceAction -ResourceGroupName <group-name> -ResourceType Microsoft.Web/sites -ResourceName <app-name> -Action newpassword
```

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

1. 在应用程序的左侧菜单中，选择 "**部署中心**  >  **FTPS 凭据**" 或 "**本地 Git/FTPS 凭据**"。

    ![演示如何从 Azure 应用服务的部署中心选择 FTP 仪表板。](./media/app-service-deployment-credentials/access-no-git.png)

2. 在 **应用程序范围** 部分，选择 " **重置**"。

-----

## <a name="disable-basic-authentication"></a>禁用基本身份验证

一些组织需要满足安全要求，因此宁愿禁用通过 FTP 或 WebDeploy 进行的访问。 这样一来，组织的成员就只能通过 Azure Active Directory (Azure AD) 控制的 API 访问其应用服务。

### <a name="ftp"></a>FTP

若要禁用对站点的 FTP 访问，请运行以下 CLI 命令。 将占位符替换为资源组和站点名称。 

```azurecli-interactive
az resource update --resource-group <resource-group> --name ftp --namespace Microsoft.Web --resource-type basicPublishingCredentialsPolicies --parent sites/<site-name> --set properties.allow=false
```

若要确认 FTP 访问被阻止，可以尝试使用 FTP 客户端（如 FileZilla）进行身份验证。 若要检索发布凭据，请转到站点的概览边栏选项卡，然后单击“下载发布配置文件”。 使用该文件的 FTP 主机名、用户名和密码进行身份验证，你会收到 401 错误响应，指示你未获得授权。

### <a name="webdeploy-and-scm"></a>WebDeploy 和 SCM

若要禁用对 WebDeploy 端口和 SCM 站点的基本身份验证访问，请运行以下 CLI 命令。 将占位符替换为资源组和站点名称。 

```azurecli-interactive
az resource update --resource-group <resource-group> --name scm --namespace Microsoft.Web --resource-type basicPublishingCredentialsPolicies --parent sites/<site-name> --set properties.allow=false
```

若要确认发布配置文件凭据在 WebDeploy 上被阻止，请尝试[使用 Visual Studio 2019 发布 Web 应用](/visualstudio/deployment/quickstart-deploy-to-azure)。

### <a name="disable-access-to-the-api"></a>禁止对 API 的访问

上一部分的 API 支持 Azure 基于角色的访问控制 (Azure RBAC)，这意味着你可以[创建一个自定义角色](../role-based-access-control/custom-roles.md#steps-to-create-a-custom-role)，并将权限较低的用户分配给该角色，这样这些用户就无法在任何站点上启用基本身份验证。 若要配置自定义角色，请[按照这些说明进行操作](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#create-a-custom-rbac-role)。

你还可以使用 [Azure Monitor](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#audit-with-azure-monitor) 审核任何成功的身份验证请求，并使用 [Azure Policy](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#enforce-compliance-with-azure-policy) 对订阅中的所有站点强制实施此配置。

## <a name="next-steps"></a>后续步骤

了解如何使用这些凭据通过[本地 Git](deploy-local-git.md) 或 [FTP/S](deploy-ftp.md) 部署应用。
