---
title: 发布的应用设置的自定义主页 - Azure Active Directory 应用程序代理
description: 介绍有关 Azure Active Directory 应用程序代理连接器的基础知识
services: active-directory
author: kenwith
manager: mtillman
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: how-to
ms.date: 04/27/2021
ms.author: kenwith
ms.reviewer: harshja
ms.openlocfilehash: fbeadc3a8c947f673ca134e0daf8a0a1c1d68b80
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124787502"
---
# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-active-directory-application-proxy"></a>使用 Azure Active Directory 应用程序代理为发布的应用设置自定义主页

本文讨论如何配置应用以将用户定向到自定义主页。 当你使用应用程序代理发布应用时，你需要设置内部 URL，但有时不是用户首先看到的页面。 请设置自定义主页，以便用户可以在访问应用时转到正确的页面。 无论用户是通过 Azure Active Directory 我的应用，还是通过 Microsoft 365 应用启动器访问应用，都会看到设置的自定义主页。

当用户启动应用时，他们默认会定向到已发布应用的根域 URL。 登陆页通常设置为主页 URL。 如果希望应用用户登录到应用中的特定页面，请使用 Azure AD PowerShell 模块来定义自定义主页 URL。

下面通过一个方案解释了公司为什么要设置自定义主页：

- 在公司网络内，用户转到 `https://ExpenseApp/login/login.aspx` 来登录并访问你的应用。
- 因为你有应用程序代理需要在文件夹结构顶层访问的其他资产（如映像），因此，你需要使用 `https://ExpenseApp` 作为内部 URL 发布应用。
- 默认的外部 URL 是 `https://ExpenseApp-contoso.msappproxy.net`，无法将外部用户转到登录页。
- 需要将 `https://ExpenseApp-contoso.msappproxy.net/login/login.aspx` 设置为主页 URL，以便外部用户首先看到登录页。

> [!NOTE]
> 向用户提供对已发布应用的访问权限时，会在[我的应用](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)和 [Office 365 应用启动器](https://www.microsoft.com/microsoft-365/blog/2016/09/27/introducing-the-new-office-365-app-launcher/)中显示这些应用。

## <a name="before-you-start"></a>开始之前

在设置主页 URL 之前，请注意以下要求：

- 指定的路径必须是根域 URL 的子域路径。

  例如，如果根域 URL 为 `https://apps.contoso.com/app1/`，则配置的主页 URL 必须以 `https://apps.contoso.com/app1/` 开头。

- 如果对发布的应用做了更改，这种更改可能会重置主页 URL 的值。 将来更新应用时，应该重新检查并根据需要更新主页 URL。

可以通过 Azure 门户或使用 PowerShell 设置主页 URL。

## <a name="change-the-home-page-in-the-azure-portal"></a>更改 Azure 门户的主页

若要通过 Azure AD 门户更改应用的主页 URL，请执行以下步骤：

1. 以管理员身份登录到 [Azure 门户](https://portal.azure.com/)。
1. 依次选择“Azure Active Directory”和“应用注册” 。 此时将显示已注册应用的列表。
1. 从列表中选择应用。 此时将出现一个显示已注册应用的详细信息的页面。
1. 在“管理”下，选择“品牌打造” 。
1. 使用新路径更新“主页 URL”。

   ![显示主页 URL 字段的已注册应用的品牌页面](media/application-proxy-configure-custom-home-page/app-proxy-app-branding.png)

1. 选择“保存”。

## <a name="change-the-home-page-with-powershell"></a>使用 PowerShell 更改主页

若要使用 PowerShell 配置应用的主页，需执行以下操作：

1. 安装 Azure AD PowerShell 模块。
1. 查找应用的 ObjectId 值。
1. 使用 PowerShell 命令来更新应用的主页 URL。

### <a name="install-the-azure-ad-powershell-module"></a>安装 Azure AD PowerShell 模块

在使用 PowerShell 定义自定义主页 URL 之前，请安装 Azure AD PowerShell 模块。 可以从 [PowerShell 库](https://www.powershellgallery.com/packages/AzureAD/2.0.2.16)下载使用图形 API 终结点的包。

若要安装包，请执行以下步骤：

1. 打开标准 PowerShell 窗口，并运行以下命令：

   ```powershell
   Install-Module -Name AzureAD
   ```

    如果以非管理员身份运行该命令，请使用 `-scope currentuser` 选项。

1. 在安装期间，请选择“Y”，安装来自 Nuget.org 的两个包。这两个包是必需的。

### <a name="find-the-objectid-of-the-app"></a>查找应用的 ObjectId

通过使用应用的显示名称或主页搜索应用来获取应用的 ObjectId。

1. 在同一 PowerShell 窗口中，导入 Azure AD 模块。

   ```powershell
   Import-Module AzureAD
   ```

1. 以租户管理员身份登录到 Azure AD 模块。

   ```powershell
   Connect-AzureAD
   ```

1. 查找应用。 此示例使用 PowerShell 通过搜索显示名称为 `SharePoint` 的应用来查找 ObjectId。

   ```powershell
   Get-AzureADApplication | Where-Object { $_.DisplayName -eq "SharePoint" } | Format-List DisplayName, Homepage, ObjectId
   ```

   随后应会返回如下所示的结果。 复制 ObjectId GUID，以便在下一部分中使用。

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

   或者，可以只拉取所有应用的列表，在列表中搜索具有特定显示名称或主页的应用，并在找到应用后复制应用的 ObjectId。

   ```powershell
   Get-AzureADApplication | Format-List DisplayName, Homepage, ObjectId
   ```

### <a name="update-the-home-page-url"></a>更新主页 URL

创建主页 URL，并在应用中更新此值。 继续使用同一 PowerShell 窗口，或者若要使用新 PowerShell 窗口，请运行 `Connect-AzureAD` 再次登录 Azure AD 模块。 然后，执行以下步骤：

1. 创建一个变量，用于保存在上一部分中复制的 ObjectId 值。 （使用应用的 ObjectId 值替换此 SharePoint 示例中使用的 ObjectId 值。）

   ```powershell
   $objguid = "8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4"
   ```

1. 通过运行以下命令来确认有正确的应用。 输出应与上一部分中看到的输出相同([查找应用的 ObjectId](#find-the-objectid-of-the-app))。

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

1. 创建一个空白应用程序对象用于保存想要做出的更改。

   ```powershell
   $appnew = New-Object "Microsoft.Open.AzureAD.Model.Application"
   ```

1. 将主页 URL 设置为所需的值。 该值必须是已发布应用的子域路径。 例如，如果将主页 URL 从 `https://sharepoint-iddemo.msappproxy.net/` 更改为 `https://sharepoint-iddemo.msappproxy.net/hybrid/`，则应用用户将直接转到自定义主页。

   ```powershell
   $homepage = "https://sharepoint-iddemo.msappproxy.net/hybrid/"
   ```

1. 对主页进行更新。

   ```powershell
   Set-AzureADApplication -ObjectId $objguid -Homepage $homepage
   ```

1. 若要确认更改是否成功，请再次运行以下步骤 2 中的命令。

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

   对于我们的示例，输出现在应如下所示：

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/hybrid/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

1. 重新启动应用以确认主页是否显示为第一个屏幕（如预期）。

> [!NOTE]
> 对应用进行任何更改都可能会重置主页 URL。 如果主页网址重置，请重复执行此部分中的步骤，重置主页网址。

## <a name="next-steps"></a>后续步骤

- [通过 Azure AD 应用程序代理启用对 SharePoint 的远程访问](./application-proxy-integrate-with-sharepoint-server.md)
- [教程：在 Azure Active Directory 中添加一个本地应用程序以通过应用程序代理进行远程访问](application-proxy-add-on-premises-application.md)