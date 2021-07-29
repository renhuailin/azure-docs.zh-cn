---
title: 使用服务主体为 Azure 虚拟桌面（经典）部署管理工具 - Azure
description: 如何使用 PowerShell 部署适用于 Azure 虚拟桌面（经典）的管理工具。
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
ms.custom: devx-track-azurepowershell
manager: femila
ms.openlocfilehash: fbd12216cbc81df7f4f9e187c8150f69744eb139
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2021
ms.locfileid: "111744514"
---
# <a name="deploy-a-azure-virtual-desktop-classic-management-tool-with-powershell"></a>使用 PowerShell 部署 Azure 虚拟桌面（经典）管理工具

>[!IMPORTANT]
>此内容适用于 Azure 虚拟桌面（经典），后者不支持 Azure 资源管理器 Azure 虚拟桌面对象。

本文将介绍如何使用 PowerShell 来部署管理工具。

## <a name="important-considerations"></a>重要注意事项

每个 Azure Active Directory (Azure AD) 租户的订阅都需要单独有自己的管理工具部署。 此工具不支持 Azure AD 企业对企业 (B2B) 方案。

此管理工具只是一个示例。 Microsoft 将提供重要的安全更新和质量更新。 [GitHub 中提供了源代码](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy)。 无论你是客户还是合作伙伴，我们都建议你自定义该工具以满足你的业务需求。

以下浏览器与管理工具兼容：

- Google Chrome 68 或更高版本
- Microsoft Edge 40.15063 或更高版本
- Mozilla Firefox 52.0 或更高版本
- Safari 10 或更高版本（仅限 macOS）

## <a name="what-you-need-to-deploy-the-management-tool"></a>部署管理工具所需满足的条件

在部署管理工具之前，需要一个 Azure Active Directory (Azure AD) 用户创建应用注册并部署管理 UI。 此用户必须：

- 有权在 Azure 订阅中创建资源
- 有权创建 Azure AD 应用程序 按照[所需的权限](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)中的说明，执行这些步骤检查用户是否具有所需的权限。

部署和配置管理工具后，建议你要求用户启动管理 UI，以确保一切正常。 启动管理 UI 的用户必须具有使他们能够查看或编辑 Azure 虚拟桌面租户的角色分配。

## <a name="set-up-powershell"></a>设置 PowerShell

首先登录到 Az 和 Azure AD PowerShell 模块。 下面是登录方式：

1. 以管理员身份打开 PowerShell 并导航到你保存了 PowerShell 脚本的目录。
2. 通过运行以下 cmdlet，使用对你计划用来创建管理工具的 Azure 订阅具有“所有者”或“参与者”权限的帐户登录到 Azure：

    ```powershell
    Login-AzAccount
    ```

3. 运行以下 cmdlet，通过用于 Az PowerShell 模块的帐户登录到 Azure AD：

    ```powershell
    Connect-AzureAD
    ```

4. 然后，导航到你在其中保存了 RDS-Templates GitHub 存储库提供的两个 PowerShell 脚本的文件夹。

将你用来登录的 PowerShell 窗口保持打开状态，以便在登录后运行其他 PowerShell cmdlet。

## <a name="create-an-azure-active-directory-app-registration"></a>创建 Azure Active Directory 应用注册

若要成功部署和配置管理工具，首先需要从 [RDS-Templates GitHub 存储库](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy/scripts)下载以下 PowerShell 脚本

```powershell
Set-Location -Path "c:\temp"
$uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-management-ux/deploy/scripts/createWvdMgmtUxAppRegistration.ps1"
Invoke-WebRequest -Uri $uri -OutFile ".\createWvdMgmtUxAppRegistration.ps1"
$uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-management-ux/deploy/scripts/updateWvdMgmtUxApiUrl.ps1"
Invoke-WebRequest -Uri $uri -OutFile ".\updateWvdMgmtUxApiUrl.ps1"
```

运行以下命令，使用所需的 API 权限创建应用注册：

```powershell
$appName = Read-Host -Prompt "Enter a unique name for the management tool's app registration. The name can't contain spaces or special characters."
$azureSubscription = Get-AzSubscription | Out-GridView -PassThru
$subscriptionId = $azureSubscription.Id
Get-AzSubscription -SubscriptionId $subscriptionId | Select-AzSubscription

.\createWvdMgmtUxAppRegistration.ps1 -AppName $appName -SubscriptionId $subscriptionId
```

现在你已完成 Azure AD 应用注册，可以部署管理工具了。

## <a name="deploy-the-management-tool"></a>部署管理工具

运行以下 PowerShell 命令来部署管理工具，并将其与你刚才创建的服务主体相关联：

```powershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateParameters = @{}
$templateParameters.Add('isServicePrincipal', $true)
$templateParameters.Add('azureAdminUserPrincipalNameOrApplicationId', $ServicePrincipalCredentials.UserName)
$templateParameters.Add('azureAdminPassword', $servicePrincipalCredentials.Password)
$templateParameters.Add('applicationName', $appName)

Get-AzSubscription -SubscriptionId $subscriptionId | Select-AzSubscription
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-management-ux/deploy/mainTemplate.json" `
    -TemplateParameterObject $templateParameters `
    -Verbose
```

创建 Web 应用后，必须为 Azure AD 应用程序添加重定向 URI，以便使用户成功登录。

## <a name="set-the-redirect-uri"></a>设置重定向 URI

运行以下 PowerShell 命令来检索 Web 应用 URL，并将其设置为身份验证重定向 URI（也称为回复 URL）：

```powershell
$webApp = Get-AzWebApp -ResourceGroupName $resourceGroupName -Name $appName
$redirectUri = "https://" + $webApp.DefaultHostName + "/"
Get-AzureADApplication -All $true | where { $_.AppId -match $servicePrincipalCredentials.UserName } | Set-AzureADApplication -ReplyUrls $redirectUri
```

现在，你已添加了一个重定向 URI，接下来需要更新 API URL，以便管理工具可以与 API 后端服务进行交互。

## <a name="update-the-api-url-for-the-web-application"></a>更新 Web 应用程序的 API URL

运行以下脚本，更新 Web 应用程序前端中的 API URL 配置：

```powershell
.\updateWvdMgmtUxApiUrl.ps1 -AppName $appName -SubscriptionId $subscriptionId
```

现在，你已完全配置了管理工具 Web 应用，是时候验证 Azure AD 应用程序并提供同意了。

## <a name="verify-the-azure-ad-application-and-provide-consent"></a>验证 Azure AD 应用程序并提供同意

若要验证 Azure AD 应用程序配置并提供同意，请执行以下操作：

1. 打开 Internet 浏览器，然后使用管理帐户登录到 [Azure 门户](https://portal.azure.com/)。
2. 从 Azure 门户顶部的搜索栏中，搜索“应用注册”，然后在“服务”下选择该项。
3. 选择“所有应用程序”，并在[创建 Azure Active Directory 应用注册](#create-an-azure-active-directory-app-registration)中搜索为 PowerShell 脚本提供的唯一的应用名称。
4. 在浏览器左侧的面板中，选择“身份验证”，并确保重定向 URI 与管理工具的 Web 应用 URL 相同，如下图所示。

   [ ![身份验证页面，其中包含了输入的重定向 URI](../media/management-ui-redirect-uri-inline.png) ](../media/management-ui-redirect-uri-expanded.png#lightbox)

5. 在左侧面板中，选择“API 权限”以确认添加了权限。 如果你是全局管理员，请选择“授予对 `tenantname` 的管理员同意”按钮，然后按照对话框提示为你的组织提供管理员同意。

    [ ![“API 权限”页](../media/management-ui-permissions-inline.png) ](../media/management-ui-permissions-expanded.png#lightbox)

你现在可以开始使用管理工具了。

## <a name="use-the-management-tool"></a>使用管理工具

现在，你已设置管理工具，可以随时随地启动它了。 下面是该工具的启动方式：

1. 在 Web 浏览器中打开 Web 应用的 URL。 如果你不记得 URL，可以登录到 Azure，找到你为管理工具部署的应用服务，然后选择 URL。
2. 使用 Azure 虚拟桌面凭据登录。

   > [!NOTE]
   > 如果你在配置管理工具时未授予管理员同意，则登录的每个用户都需要提供自己的用户同意才能使用该工具。

3. 当系统提示你选择租户组时，请从下拉列表中选择“默认租户组”。
4. 选择“默认租户组”时，将在窗口左侧显示一个菜单。 在此菜单中，找到租户组的名称并将其选中。

   > [!NOTE]
   > 如果你有自定义的租户组，请手动输入名称，而不要从下拉列表中选择。

## <a name="report-issues"></a>报告问题

如果遇到有关管理工具或其他 Azure 虚拟桌面工具的任何问题，请按照[远程桌面服务的 Azure 资源管理器模板](https://github.com/Azure/RDS-Templates/blob/master/README.md)中的说明在 GitHub 上报告这些问题。

## <a name="next-steps"></a>后续步骤

了解如何部署和连接管理工具后，接下来可以了解如何使用 Azure 服务运行状况来监视服务问题和获得运行状况方面的建议。 若要了解详细信息，请参阅[“设置服务警报”教程](set-up-service-alerts-2019.md)。
