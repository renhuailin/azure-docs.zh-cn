---
title: 教程 - 如何将 Azure Key Vault 与 .NET Azure Web 应用配合使用
description: 本教程介绍如何将 ASP.NET Core 应用程序中的 Azure Web 应用配置为从密钥保管库读取机密。
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: mbaldwin
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: fdefca482dfa1afeb3eebb284b08eaadf4f1af60
ms.sourcegitcommit: 19dcad80aa7df4d288d40dc28cb0a5157b401ac4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2021
ms.locfileid: "107897047"
---
# <a name="tutorial-use-a-managed-identity-to-connect-key-vault-to-an-azure-web-app-in-net"></a>教程：使用托管标识将 Key Vault 连接到 .NET 中的 Azure Web 应用

[Azure Key Vault](./overview.md) 提供了一种更安全的存储凭据和其他机密的方法。 但代码需要对 Key Vault 进行身份验证才能检索它们。 [Azure 资源的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)通过为 Azure 服务提供 Azure Active Directory (Azure AD) 中的自动托管标识来帮助解决此问题。 此标识可用于通过支持 Azure AD 身份验证的任何服务（包括 Key Vault）的身份验证，这样就无需在代码中插入任何凭据了。

在本教程中，你将创建 Azure Web 应用程序并将其部署到 [Azure 应用服务](../../app-service/overview.md)。 你将使用托管标识对 Azure Web 应用进行身份验证，该应用具有使用[用于 .NET 的 Azure Key Vault 机密客户端库](/dotnet/api/overview/azure/key-vault)和 [Azure CLI](/cli/azure/get-started-with-azure-cli) 的 Azure 密钥保管库。 当你使用所选的开发语言 Azure PowerShell 和/或 Azure 门户时，同样的基本原则也适用。

有关本教程中介绍的 Azure 应用服务 Web 应用程序和部署的详细信息，请参阅：
- [应用服务概述](../../app-service/overview.md)
- [在 Azure 应用服务中创建 ASP.NET Core Web 应用](../../app-service/quickstart-dotnetcore.md)
- [从本地 Git 部署到 Azure 应用服务](../../app-service/deploy-local-git.md)

## <a name="prerequisites"></a>先决条件

要完成本教程，需要：

* Azure 订阅。 [免费创建一个。](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [.NET Core 3.1 SDK（或更高版本）](https://dotnet.microsoft.com/download/dotnet-core/3.1)。
* 版本 2.28.0 或更高版本的 [Git](https://www.git-scm.com/downloads) 安装。
* [Azure CLI](/cli/azure/install-azure-cli) 或 [Azure PowerShell](/powershell/azure/)。
* [Azure Key Vault](./overview.md)。 可以使用 [Azure 门户](quick-create-portal.md)、[Azure CLI](quick-create-cli.md) 或 [Azure PowerShell](quick-create-powershell.md) 来创建密钥保管库。
* Key Vault [机密](../secrets/about-secrets.md)。 可以使用 [Azure 门户](../secrets/quick-create-portal.md)、[PowerShell](../secrets/quick-create-powershell.md) 或 [Azure CLI](../secrets/quick-create-cli.md) 来创建机密。

如果已在 Azure 应用服务中部署了 Web 应用程序，则可以跳到[配置对密钥保管库的 Web 应用访问](#create-and-assign-a-managed-identity)和[修改 Web 应用程序代码](#modify-the-app-to-access-your-key-vault)部分。

## <a name="create-a-net-core-app"></a>创建 .NET Core 应用
在此步骤中，你将设置本地 .NET Core 项目。

在计算机的终端窗口中，创建一个名为 `akvwebapp` 的目录，并将其设为当前目录：

```bash
mkdir akvwebapp
cd akvwebapp
```

使用 [dotnet new web](/dotnet/core/tools/dotnet-new) 命令创建 .NET Core 应用：

```bash
dotnet new web
```

在本地运行应用程序，以便了解将它部署到 Azure 时的情形：

```bash
dotnet run
```

在 Web 浏览器中，转到 `http://localhost:5000` 处的应用。

随即会看到应用发出的“Hello World!” 消息显示在页面中。

有关为 Azure 创建 Web 应用程序的详细信息，请参阅[在 Azure 应用服务中创建 ASP.NET Core Web 应用](../../app-service/quickstart-dotnetcore.md)

## <a name="deploy-the-app-to-azure"></a>将应用部署到 Azure

在此步骤中，你将使用本地 Git 将 .NET Core 应用程序部署到 Azure 应用服务。 有关如何创建和部署应用程序的详细信息，请参阅[在 Azure 中创建 ASP.NET Core Web 应用](../../app-service/quickstart-dotnetcore.md)。

### <a name="configure-the-local-git-deployment"></a>配置本地 Git 部署

在终端窗口中，选择 Ctrl+C 关闭 Web 服务器。  为 .NET Core 项目初始化 Git 存储库：

```bash
git init --initial-branch=main
git add .
git commit -m "first commit"
```

可以通过 FTP 和本地 Git 使用 deployment user 来部署 Azure Web 应用。 配置部署用户之后，可对所有 Azure 部署使用此用户。 帐户级部署用户名和密码不同于 Azure 订阅凭据。 

若要配置部署用户，请运行 [az webapp deployment user set](/cli/azure/webapp/deployment/user?#az_webapp_deployment_user_set) 命令。 选择符合以下准则的用户名和密码： 

- 用户名在 Azure 中必须唯一。 对于本地 Git 推送，它不能包含 at 符号 (@)。 
- 密码必须至少为 8 个字符，且包含字母、数字和符号这三种元素中的两种。 

```azurecli-interactive
az webapp deployment user set --user-name "<username>" --password "<password>"
```

JSON 输出会将该密码显示为 `null`。 如果收到 `'Conflict'. Details: 409` 错误，请更改用户名。 如果收到 `'Bad Request'. Details: 400` 错误，请使用更强的密码。 

记录用户名和密码，以便可以使用它来部署 Web 应用。

### <a name="create-a-resource-group"></a>创建资源组

资源组是一个逻辑容器，可以在其中部署和管理 Azure 资源。 使用 [az group Create](/cli/azure/group?#az_group_create) 命令创建一个资源组用于包含密钥保管库和 Web 应用：

```azurecli-interactive
az group create --name "myResourceGroup" -l "EastUS"
```

### <a name="create-an-app-service-plan"></a>创建应用服务计划

使用 Azure CLI [az appservice plan create](/cli/azure/appservice/plan) 命令创建[应用服务计划](../../app-service/overview-hosting-plans.md)。 以下示例在 `FREE` 定价层中创建名为 `myAppServicePlan` 的应用服务计划：

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

创建应用服务计划时，Azure CLI 将显示与在此处看到的类似信息：

<pre>
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "West Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "West Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  &lt; JSON data removed for brevity. &gt;
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
} 
</pre>

有关详细信息，请参阅[在 Azure 中管理应用服务计划](../../app-service/app-service-plan-manage.md)。

### <a name="create-a-web-app"></a>创建 Web 应用

在 `myAppServicePlan` 应用服务计划中创建 [Azure Web 应用](../../app-service/overview.md)。 

> [!Important]
> 与密钥保管库一样，Azure Web 应用必须具有独一无二的名称。 在以下示例中，将 `<your-webapp-name>` 替换为 Web 应用的名称。


```azurecli-interactive
az webapp create --resource-group "myResourceGroup" --plan "myAppServicePlan" --name "<your-webapp-name>" --deployment-local-git
```

创建 Web 应用时，Azure CLI 将显示与你在此处看到的类似的输出：

<pre>
Local git is configured with url of 'https://&lt;username&gt;@&lt;your-webapp-name&gt;.scm.azurewebsites.net/&lt;ayour-webapp-name&gt;.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "clientCertExclusionPaths": null,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;your-webapp-name&gt;.azurewebsites.net",
  "deploymentLocalGitUrl": "https://&lt;username&gt;@&lt;your-webapp-name&gt;.scm.azurewebsites.net/&lt;your-webapp-name&gt;.git",
  "enabled": true,
  &lt; JSON data removed for brevity. &gt;
}
</pre>

Git 远程的 URL 将显示在 `deploymentLocalGitUrl` 属性中，其格式为 `https://<username>@<your-webapp-name>.scm.azurewebsites.net/<your-webapp-name>.git`。 保存此 URL。 稍后需要用到此信息。

现在，将 Web 应用配置为从 `main` 分支部署：

```azurecli-interactive
 az webapp config appsettings set -g MyResourceGroup --name "<your-webapp-name>" --settings deployment_branch=main
```

使用以下命令转到新应用。 将 `<your-webapp-name>` 替换为你的应用名称。

```bash
https://<your-webapp-name>.azurewebsites.net
```

你将看到新 Azure Web 应用的默认网页。

### <a name="deploy-your-local-app"></a>部署本地应用

回到本地终端窗口，将 Azure 远程功能添加到本地 Git 存储库。 在以下命令中，将 `<deploymentLocalGitUrl-from-create-step>` 替换为保存在[创建 Web 应用](#create-a-web-app)部分中的 Git 远程的 URL。

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

使用以下命令推送到 Azure 远程以部署应用。 当 Git 凭据管理器提示你输入凭据时，请使用你在[配置本地 Git 部署](#configure-the-local-git-deployment)部分中创建的凭据。

```bash
git push azure main
```

该命令可能需要几分钟时间才能运行。 运行时，它会显示与此处所示类似的信息：
<pre>
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 285 bytes | 95.00 KiB/s, done.
Total 3 (delta 2), reused 0 (delta 0), pack-reused 0
remote: Deploy Async
remote: Updating branch 'main'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'd6b54472f7'.
remote: Repository path is /home/site/repository
remote: Running oryx build...
remote: Build orchestrated by Microsoft Oryx, https://github.com/Microsoft/Oryx
remote: You can report issues at https://github.com/Microsoft/Oryx/issues
remote:
remote: Oryx Version      : 0.2.20200114.13, Commit: 204922f30f8e8d41f5241b8c218425ef89106d1d, ReleaseTagName: 20200114.13
remote: Build Operation ID: |imoMY2y77/s=.40ca2a87_
remote: Repository Commit : d6b54472f7e8e9fd885ffafaa64522e74cf370e1
.
.
.
remote: Deployment successful.
remote: Deployment Logs : 'https://&lt;your-webapp-name&gt;.scm.azurewebsites.net/newui/jsonviewer?view_url=/api/deployments/d6b54472f7e8e9fd885ffafaa64522e74cf370e1/log'
To https://&lt;your-webapp-name&gt;.scm.azurewebsites.net:443/&lt;your-webapp-name&gt;.git
   d87e6ca..d6b5447  main -> main
</pre>

使用 Web 浏览器转到（或刷新）已部署的应用程序：

```bash
http://<your-webapp-name>.azurewebsites.net
```

随即会看到应用发出的“Hello World!” 你先前访问 `http://localhost:5000` 时看到的消息。

有关使用 Git 部署 Web 应用程序的详细信息，请参阅[从本地 Git 部署到 Azure 应用服务](../../app-service/deploy-local-git.md)
 
## <a name="configure-the-web-app-to-connect-to-key-vault"></a>配置 Web 应用以连接到 Key Vault

在本部分中，你将配置对 Key Vault 的 Web 访问，并更新应用程序代码以从 Key Vault 中检索机密。

### <a name="create-and-assign-a-managed-identity"></a>创建并分配托管标识

在本教程中，我们将使用[托管标识](../../active-directory/managed-identities-azure-resources/overview.md)对 Key Vault 进行身份验证。 托管标识会自动管理应用程序凭据。

在 Azure CLI 中，若要为此应用程序创建标识，请运行 [az webapp-identity assign](/cli/azure/webapp/identity?#az_webapp_identity_assign) 命令：

```azurecli-interactive
az webapp identity assign --name "<your-webapp-name>" --resource-group "myResourceGroup"
```

该命令将返回此 JSON 代码片段：

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```

若要为 Web 应用授予在密钥保管库上执行 get 和 list 的权限，请将 `principalId` 传递到 Azure CLI [az keyvault set-policy](/cli/azure/keyvault?#az_keyvault_set_policy) 命令 ：

```azurecli-interactive
az keyvault set-policy --name "<your-keyvault-name>" --object-id "<principalId>" --secret-permissions get list
```

你还可以使用 [Azure 门户](./assign-access-policy-portal.md)或 [PowerShell](./assign-access-policy-powershell.md) 分配访问策略。

### <a name="modify-the-app-to-access-your-key-vault"></a>修改应用以访问密钥保管库

在本教程中，你将使用 [Azure Key Vault 机密客户端库](/dotnet/api/overview/azure/security.keyvault.secrets-readme)进行演示。 你还可以使用 [Azure Key Vault 证书客户端库](/dotnet/api/overview/azure/security.keyvault.certificates-readme)或 [Azure Key Vault 密钥客户端库](/dotnet/api/overview/azure/security.keyvault.keys-readme)。

#### <a name="install-the-packages"></a>安装包

在终端窗口中，安装适用于 .NET 的 Azure Key Vault 机密客户端库和 Azure 标识客户端库包：

```console
dotnet add package Azure.Identity
dotnet add package Azure.Security.KeyVault.Secrets
```

#### <a name="update-the-code"></a>更新代码

在 akvwebapp 项目中找到 Startup.cs 文件并将其打开。 

将以下行添加到标头：

```csharp
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;
using Azure.Core;
```

将以下行添加到 `app.UseEndpoints` 调用之前，更新 URI 以反映密钥保管库的 `vaultUri`。 此代码使用 [DefaultAzureCredential()](/dotnet/api/azure.identity.defaultazurecredential) 对 Key Vault 进行身份验证，后者使用托管标识中的令牌进行身份验证。 有关向 Key Vault 进行身份验证的详细信息，请参阅[开发人员指南](./developers-guide.md#authenticate-to-key-vault-in-code)。 该代码还会使用指数退避，以便在 Key Vault 受到限制的情况下进行重试。 有关 Key Vault 事务限制的详细信息，请参阅 [Azure Key Vault 限制指南](./overview-throttling.md)。

```csharp
SecretClientOptions options = new SecretClientOptions()
    {
        Retry =
        {
            Delay= TimeSpan.FromSeconds(2),
            MaxDelay = TimeSpan.FromSeconds(16),
            MaxRetries = 5,
            Mode = RetryMode.Exponential
         }
    };
var client = new SecretClient(new Uri("https://<your-unique-key-vault-name>.vault.azure.net/"), new DefaultAzureCredential(),options);

KeyVaultSecret secret = client.GetSecret("<mySecret>");

string secretValue = secret.Value;
```

将行 `await context.Response.WriteAsync("Hello World!");` 更新为如下行：

```csharp
await context.Response.WriteAsync(secretValue);
```

在继续下一步之前，请务必保存所做的更改。

#### <a name="redeploy-your-web-app"></a>重新部署 Web 应用

现已更新了代码，可以使用以下 Git 命令将其重新部署到 Azure：

```bash
git add .
git commit -m "Updated web app to access my key vault"
git push azure main
```

## <a name="go-to-your-completed-web-app"></a>转到已完成的 Web 应用

```bash
http://<your-webapp-name>.azurewebsites.net
```

在以前显示“Hello World!”的地方，现在应显示机密值。

## <a name="next-steps"></a>后续步骤

- [将 Azure Key Vault 与部署到 .NET 虚拟机的应用程序结合使用](./tutorial-net-virtual-machine.md)
- 详细了解 [Azure 资源的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)
- 查看[开发人员指南](./developers-guide.md)
- [保护对密钥保管库的访问](./security-features.md)
