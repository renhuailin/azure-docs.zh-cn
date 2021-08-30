---
title: 配置 Google 身份验证
description: 了解如何将 Google 身份验证配置为应用服务或 Azure Functions 应用的标识提供者。
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.topic: article
ms.date: 03/29/2021
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: 46ccfd04722cb9333a8f6e546505ed1e10f657af
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121752445"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-google-login"></a>将应用服务或 Azure Functions 应用配置为使用 Google 登录

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

本主题介绍如何将 Azure 应用服务或 Azure Functions 配置为使用 Google 作为身份验证提供程序。

要完成本主题中的过程，必须拥有一个包含已验证电子邮件地址的 Google 帐户。 若要新建一个 Google 帐户，请转到 [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302)。

## <a name="register-your-application-with-google"></a><a name="register"> </a>向 Google 注册应用程序

1. 遵循 Google 文档[服务器端应用的 Google 登录](https://developers.google.com/identity/sign-in/web/server-side-flow)，创建客户端 ID 和客户端密码。 无需进行任何代码更改。 只需使用以下信息：
    - 对于“授权的 JavaScript 源”，请在 *\<app-name>* 中与应用名称一起使用 `https://<app-name>.azurewebsites.net`。
    - 对于“授权的重定向 URI”，请使用 `https://<app-name>.azurewebsites.net/.auth/login/google/callback`。
1. 复制“应用 ID”和“应用密码”值。

    > [!IMPORTANT]
    > 应用密码是一个非常重要的安全凭据。 请勿与任何人分享此密钥或在客户端应用程序中分发它。

## <a name="add-google-information-to-your-application"></a><a name="secrets"> </a>向应用程序添加 Google 信息

1. 登录 [Azure 门户]并导航到你的应用程序。
1. 在左侧菜单中选择“身份验证”。 单击“添加标识提供者”。
1. 在“标识提供者”下拉列表中选择“Google”。 粘贴前面获取的应用程序 ID 和应用程序机密值。

    该机密将存储为一个名为`GOOGLE_PROVIDER_AUTHENTICATION_SECRET`的槽粘滞[应用程序设置](./configure-common.md#configure-app-settings)。 如果你想要在 Azure Key Vault 中管理机密，稍后可以将该设置更新为使用 [Key Vault 引用](./app-service-key-vault-references.md)。

1. 如果这是为应用程序配置的第一个标识提供程序，你还将看到“应用服务身份验证设置”部分的提示。 否则你可转到下一步。
    
    这些选项可确定应用程序如何响应未经身份验证的请求，默认选择会将所有请求重定向到用此新提供程序登录。 你现在可以更改自定义此行为，也可以稍后通过选择“身份验证”设置旁边的“编辑”，在主要“身份验证”屏幕上调整这些设置。   若要详细了解这些选项，请参阅[身份验证流](overview-authentication-authorization.md#authentication-flow)。

1. 单击“添加”。

> [注意] 若要添加范围：可以在提供程序的注册门户中定义应用程序拥有哪些权限。 应用可以在登录时请求利用这些权限的范围。

现在，可以在应用中使用 Google 进行身份验证了。 该提供程序将在“身份验证”屏幕上列出。 在该屏幕上，你可以编辑或删除此提供程序配置。

## <a name="next-steps"></a><a name="related-content"> </a>后续步骤

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: https://go.microsoft.com/fwlink/p/?LinkId=268303

[Azure 门户]: https://portal.azure.com/

