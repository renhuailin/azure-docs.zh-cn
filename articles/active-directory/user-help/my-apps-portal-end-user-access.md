---
title: 在“我的应用”门户上查找和使用应用 - Azure AD
description: 了解如何查找“我的应用”门户以及如何访问组织基于云的应用。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 03/26/2021
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: 54a609cb73dcbf70afa5e20c41fa734080b89cc1
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122770687"
---
# <a name="sign-in-and-start-apps-from-the-my-apps-portal"></a>从“我的应用”门户登录并启动应用

可以结合使用工作和学校帐户与基于 Web 的“我的应用”门户，以便：

- 查看并启用组织的多个基于云的应用
- 转到[“我的组”门户](https://account.activedirectory.windowsazure.com/r#/groups)
- 转到[“我的帐户”门户](https://myaccount.microsoft.com/)

如果无权访问“我的应用”门户，请联系组织的支持人员以获取相关权限。

> [!IMPORTANT]
> 此内容适用于“我的应用”用户。 如果你是管理员，可以在[应用程序管理文档](../manage-apps/index.yml)中查找有关如何设置和管理基于云的应用的详细信息。
>
> 如果在使用个人 Microsoft 帐户登录时遇到错误，用户仍可以使用组织的域名（例如 contoso.com）登录，也可以以管理员身份，使用组织的“租户 ID”通过以下其中一个 URL 来登录：
>
>   - https://myapplications.microsoft.com?tenantId= your_domain_name
>   - https://myapplications.microsoft.com?tenant= your_tenant_ID

## <a name="supported-browsers"></a>支持的浏览器

可以从以下任意 Web 浏览器访问“我的应用”门户。 Microsoft 建议使用与操作系统兼容的最新浏览器。

- Microsoft Edge（最新版本，适用于桌面和移动设备）
- Safari（最新版本，适用于 Mac 和 iOS）
- Chrome（最新版本，适用于桌面和移动设备）
- Firefox（最新版本）

可以在计算机上或从 iOS 或 Android 移动设备上的 Edge 浏览器的移动版访问并使用“我的应用”门户。

![“我的应用”门户中的“应用”页](media/my-apps-portal/my-apps-home.png)

## <a name="access-and-use-the-my-apps-portal-on-your-computer"></a>在计算机上访问和使用“我的应用”门户

如果有权访问和使用组织基于云的应用，则可以通过“我的应用”门户进入它们。

1. 在计算机上登录到工作或学校帐户。

1. 在受支持的 Web 浏览器中，转到 https://myapps.microsoft.com ，或者使用组织提供的链接（如果其将你定向到自定义页，如 `https://myapps.microsoft.com/contoso.com` ）。

   将显示“应用”页，其中显示组织拥有并可供你使用的所有基于云的应用。

1. 从“应用”页中，选择要开始使用的应用。

   将为该应用打开一个新页面，你可以在其中登录（如有必要）或者开始使用应用。

### <a name="download-and-install-the-my-apps-secure-sign-in-extension"></a>下载并安装我的应用安全登录扩展

如果出现提示，请下载并安装“我的应用安全登录扩展”。 此扩展有助于使用桌面浏览器将单一登录用于组织的云应用。 在“我的应用”门户中将鼠标悬停在应用上，选择“...”，然后选择“管理应用程序”  。

如果组织已将你设置为单一登录，则会自动安装该扩展，可以跳过此部分。

“我的应用安全登录扩展”有助于：

- 从登录页面直接登录到应用。
- 使用“快速搜索”功能启动任何应用。
- 请在“最近使用的项目”部分中查看你最后使用的应用。
- 远程使用[应用程序代理](../app-proxy/application-proxy.md)时使用公司内部 URL。

### <a name="to-download-and-install-the-extension"></a>下载并安装扩展

根据所使用的浏览器下载并安装该扩展。

- Microsoft Edge - 在 Microsoft Store 中，转到[我的应用安全登录扩展](https://microsoftedge.microsoft.com/addons/detail/my-apps-secure-signin-ex/gaaceiggkkiffbfdpmfapegoiohkiipl)功能，然后选择“获取”以获取 Microsoft Edge 旧版浏览器的扩展 。

- Google Chrome - 在 Chrome Web Store 中，转到[我的应用安全登录扩展](https://chrome.google.com/webstore/detail/my-apps-secure-sign-in-ex/ggjhpefgjjfobnfoldnjipclpcfbgbhl)功能，然后选择“添加到 Chrome” 。

- Mozilla Firefox - 在“Firefox 加载项”页中，转到我的应用安全登录扩展功能，然后选择“添加到 Firefox”。  

“地址”栏右侧会添加一个图标，可用于登录并自定义扩展。

### <a name="to-change-your-my-apps-portal-using-the-extension"></a>使用扩展更改“我的应用”门户

可以在“最近使用过的项目”部分中选择要查看的应用数量，并决定是否允许组织的内部 URL 进行重定向。

1. 选择地址栏右侧的新“我的应用安全登录扩展”图标![扩展图标](media/my-apps-portal/my-apps-portal-extension-icon.png)，然后选择“登录”以开始使用  。

1. 右键单击“设置”图标![设置图标](media/my-apps-portal/my-apps-portal-extension-settings-icon.png)，然后选择“设置” 。

1. 在“设置”框中，选择要在门户上查看的最新应用的数量，以及是否允许组织的内部 URL 进行重定向，以便可以远程使用它们。

   ![扩展的“设置”页，其中显示了可用的自定义](media/my-apps-portal/my-apps-portal-extension-settings-page.png)

## <a name="access-and-use-the-my-apps-portal-on-mobile-edge"></a>在 Edge 移动版上访问和使用“我的应用”门户

在设备上从 Edge 浏览器的移动版中查看和使用组织的应用。

1. 在移动设备上，从 Apple App Store 和 Google Play 商店下载并安装移动版 Edge 浏览器应用。

1. 打开移动版 Edge 浏览器应用，转到 https://myapps.microsoft.com ，或使用组织提供的链接（如果其将你定向到自定义页，如 https://myapps.microsoft.com/contoso.com ）。

   将显示“应用”页，其中显示组织拥有并可供你使用的所有基于云的应用。

1. 从“应用”页中，选择要开始使用的应用。

   将为该应用打开一个新页面，你可以在其中登录（如有必要）或者开始使用应用。

## <a name="add-a-new-app-to-the-my-apps-portal"></a>将新应用添加到“我的应用”门户

如果管理员已授予你权限，则可以将新应用添加到“应用”页。

1. 从“应用”页中，选择“添加自助服务应用” 。

   ![myapplications.microsoft.com 上“我的应用”门户中的“添加应用”页](media/my-apps-portal/my-apps-portal-add-app-link.png)

1. 从提供的列表中选择要添加的应用，然后选择“添加”。

1. 该应用将添加到“应用”页上的列表中。

   某些应用可能需要获得管理员批准才能进行添加。 如果出现这种情况，则在管理员批准之前，应用不会添加到“应用”页。

## <a name="start-a-cloud-based-app"></a>启动基于云的应用

可以从“我的应用”门户中启动任何可用的基于云的应用。 只能看到有权使用的应用。

- 从“应用”页中，选择要开始使用的应用。

   将为该应用打开一个新页面，你可以在其中登录（如有必要）或者开始使用应用。

## <a name="activities-in-the-my-apps-portal"></a>“我的应用”门户中的活动

进入“我的应用”门户后，可以进行以下操作：

- 将应用整理到组织创建和提供的各种类别中。 有关详细信息，请参阅[在“我的应用”门户中访问和使用集合](my-applications-portal-workspaces.md)。
- 查看、更新或撤销授予应用程序的权限。 有关详细信息，请参阅[在“我的应用”门户中编辑或撤销应用程序权限](my-applications-portal-permissions-saved-accounts.md)。
- 创建、更新和删除应用帐户。 有关详细信息，请参阅“[编辑或撤销应用程序权限](my-applications-portal-permissions-saved-accounts.md)”。
- 从“我的应用”门户访问[我的帐户](my-account-portal-overview.md)进行帐户管理 ：
  
  - 检查是否有异常登录活动
  - 管理密码
  - 管理连接的设备、订阅、组织和 Office 应用

>[!NOTE]
>如果看不到任何可用的集合或类别，则表示管理员尚未设置或共享任何集合。 若要查看共享的集合，请联系组织的支持人员以获得其他帮助或权限。

## <a name="next-steps"></a>后续步骤

进入“应用”页后，可以进行以下操作：

- [更改个人资料信息](./my-account-portal-settings.md)

- [查看和更新与组相关的信息](my-apps-portal-end-user-groups.md)

- [执行自己的访问评审](my-apps-portal-end-user-access-reviews.md)
