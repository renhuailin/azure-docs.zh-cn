---
title: 配置 Twitter 身份验证
description: 了解如何将 Twitter 身份验证配置为应用服务或 Azure Functions 应用的标识提供者。
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.topic: article
ms.date: 03/29/2021
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: 6ecce954991d9f3901c54a6f87fc803b32469862
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077969"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-twitter-login"></a>将应用服务或 Azure Functions 应用配置为使用 Twitter 登录

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

本文介绍如何将 Azure 应用服务或 Azure Functions 配置为使用 Twitter 作为身份验证提供程序。

若要完成本文中的过程，你需要一个具有已验证电子邮件地址和电话号码的 Twitter 帐户。 若要创建新的 Twitter 帐户，请转至 [twitter.com]。

## <a name="register-your-application-with-twitter"></a><a name="register"> </a>向 Twitter 注册应用程序

1. 登录到 [Azure 门户]并转到你的应用程序。 复制 **URL**。 你将使用它来配置 Twitter 应用。
1. 转到 [Twitter 开发人员]网站，使用 Twitter 帐户凭据进行登录，然后选择“创建应用”。
1. 输入新应用的应用名称和应用程序说明 。 将应用程序的 URL 粘贴到“网站 URL”字段 。 在“回叫 URL”部分中，输入应用服务应用的 HTTPS URL 并追加路径 `/.auth/login/twitter/callback`。 例如，`https://contoso.azurewebsites.net/.auth/login/twitter/callback`。
1. 在页面底部的“告诉我们如何使用此应用”中键入至少 100 个字符，然后选择“创建” 。 在弹出窗口中再次单击“创建”。 将显示应用程序详细信息。
1. 选择“密钥和访问令牌”选项卡。

   记下这些值：
   - API 密钥
   - API 密钥

   > [!IMPORTANT]
   > 该 API 密钥是重要的安全凭据。 请勿与任何人分享此密钥或将密钥随应用分发。

## <a name="add-twitter-information-to-your-application"></a><a name="secrets"> </a>向应用程序添加 Twitter 信息

1. 登录 [Azure 门户]并导航到你的应用程序。
1. 在左侧菜单中选择“身份验证”。 单击“添加标识提供者”。
1. 在“标识提供者”下拉列表中选择“Twitter”。 粘贴前面获取的 `API key` 和 `API secret key` 值。

    该机密将存储为一个名为`TWITTER_PROVIDER_AUTHENTICATION_SECRET`的槽粘滞[应用程序设置](./configure-common.md#configure-app-settings)。 如果你想要在 Azure Key Vault 中管理机密，稍后可以将该设置更新为使用 [Key Vault 引用](./app-service-key-vault-references.md)。

1. 如果这是为应用程序配置的第一个标识提供程序，你还将看到“应用服务身份验证设置”部分的提示。 否则你可转到下一步。
    
    这些选项可确定应用程序如何响应未经身份验证的请求，默认选择会将所有请求重定向到用此新提供程序登录。 你现在可以更改自定义此行为，也可以稍后通过选择“身份验证”设置旁边的“编辑”，在主要“身份验证”屏幕上调整这些设置。   若要详细了解这些选项，请参阅[身份验证流](overview-authentication-authorization.md#authentication-flow)。

1. 单击“添加”。

现在，可以在应用中使用 Twitter 进行身份验证了。 该提供程序将在“身份验证”屏幕上列出。 在该屏幕上，你可以编辑或删除此提供程序配置。

## <a name="next-steps"></a><a name="related-content"> </a>后续步骤

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[Twitter 开发人员]: https://go.microsoft.com/fwlink/p/?LinkId=268300
[twitter.com]: https://go.microsoft.com/fwlink/p/?LinkID=268287
[Azure 门户]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
