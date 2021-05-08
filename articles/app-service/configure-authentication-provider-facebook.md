---
title: 配置 Facebook 身份验证
description: 了解如何将 Facebook 身份验证配置为应用服务或 Azure Functions 应用的标识提供者。
ms.assetid: b6b4f062-fcb4-47b3-b75a-ec4cb51a62fd
ms.topic: article
ms.date: 03/29/2021
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: bc639eaea76b3309d6ed047e73c726040da19639
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078003"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-facebook-login"></a>将应用服务或 Azure Functions 应用配置为使用 Facebook 登录

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

本文介绍如何将 Azure 应用服务或 Azure Functions 配置为使用 Facebook 作为身份验证提供程序。

若要完成本文中的过程，你需要一个具有已验证电子邮件地址和移动电话号码的 Facebook 帐户。 若要创建新的 Facebook 帐户，请转到 [facebook.com]。

## <a name="register-your-application-with-facebook"></a><a name="register"> </a>向 Facebook 注册应用程序

1. 转到 [Facebook 开发人员]网站，使用 Facebook 帐户凭据登录。

   如果没有 Facebook 开发者帐户，请选择“入门”，然后按照注册步骤进行操作。
1. 选择“我的应用” > “添加新应用”。
1. 在“显示名称”字段中：
   1. 键入应用的唯一名称。
   1. 提供“联系人电子邮件”。
   1. 选择“创建应用 ID”。
   1. 完成安全检查。

   新 Facebook 应用的开发者仪表板将打开。
1. 选择“仪表板” > “Facebook 登录” > “设置” > “Web”。
1. 在左侧导航的“Facebook 登录”下，选择“设置”。
1. 在“有效的 OAuth 重定向 URI”字段中输入 `https://<app-name>.azurewebsites.net/.auth/login/facebook/callback`。 切记将 `<app-name>` 替换为“Azure 应用服务”应用的名称。
1. 选择“保存更改”。
1. 在左侧窗格中，选择“设置” > “基本”。 
1. 在“应用密码”字段中，选择“显示”。 复制“应用 ID”和“应用密码”的值。 稍后可将其用于配置 Azure 中的“应用服务”应用。

   > [!IMPORTANT]
   > 应用程序密钥是一个非常重要的安全凭据。 请勿与任何人分享此密钥或在客户端应用程序中分发它。
   >

1. 用于注册应用程序的 Facebook 帐户是应用的管理员帐户。 此时，只有管理员才能登录到此应用程序。

   若要对其他 Facebook 帐户进行身份验证，请选择“应用评审”并启用“使 \<your-app-name> 公开”，以便能够使用 Facebook 身份验证对应用进行常规的公开访问。

## <a name="add-facebook-information-to-your-application"></a><a name="secrets"> </a>向应用程序添加 Facebook 信息

1. 登录 [Azure 门户]并导航到你的应用程序。
1. 在左侧菜单中选择“身份验证”。 单击“添加标识提供者”。
1. 在“标识提供者”下拉列表中选择“Facebook”。 粘贴前面获取的应用程序 ID 和应用程序机密值。

    该机密将存储为一个名为`FACEBOOK_PROVIDER_AUTHENTICATION_SECRET`的槽粘滞[应用程序设置](./configure-common.md#configure-app-settings)。 如果你想要在 Azure Key Vault 中管理机密，稍后可以将该设置更新为使用 [Key Vault 引用](./app-service-key-vault-references.md)。

1. 如果这是为应用程序配置的第一个标识提供程序，你还将看到“应用服务身份验证设置”部分的提示。 否则你可转到下一步。
    
    这些选项可确定应用程序如何响应未经身份验证的请求，默认选择会将所有请求重定向到用此新提供程序登录。 你现在可以更改自定义此行为，也可以稍后通过选择“身份验证”设置旁边的“编辑”，在主要“身份验证”屏幕上调整这些设置。   若要详细了解这些选项，请参阅[身份验证流](overview-authentication-authorization.md#authentication-flow)。

1. （可选）单击“”下一步：作用域并添加应用程序所需的任何作用域。 登录时，系统将为基于浏览器的流发出这些请求。
1. 单击“添加”。

现在，可以在应用中使用 Facebook 进行身份验证了。 该提供程序将在“身份验证”屏幕上列出。 在该屏幕上，你可以编辑或删除此提供程序配置。

## <a name="next-steps"></a><a name="related-content"> </a>后续步骤

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->
[Facebook Developers]: https://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: https://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure 门户]: https://portal.azure.com/
