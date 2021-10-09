---
title: 快速入门：在 Microsoft 标识平台中注册应用 | Azure
description: 本快速入门介绍如何使用 Microsoft 标识平台注册应用程序。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 06/14/2021
ms.author: marsma
ms.custom: aaddev, identityplatformtop40, contperf-fy21q1, contperf-fy21q2, contperf-fy21q4
ms.openlocfilehash: c608856e6238844638e63c3a719b3d534b98d33b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128615313"
---
# <a name="quickstart-register-an-application-with-the-microsoft-identity-platform"></a>快速入门：将应用程序注册到 Microsoft 标识平台

在本快速入门中，你将在 Azure 门户中注册一个应用，以便 Microsoft 标识平台可为该应用程序及其用户提供身份验证和授权服务。

Microsoft 标识平台仅对已注册的应用程序执行标识和访问管理 (IAM)。 无论是类似于 Web 应用或移动应用的客户端应用程序是，还是支持客户端应用的 Web API，注册它都会在应用程序与标识提供程序（Microsoft 标识平台）之间建立信任关系。

> [!TIP]
> 若要在 Azure AD B2C 中注册应用程序，请遵循[教程：在 Azure AD B2C 中注册 Web 应用程序](../../active-directory-b2c/tutorial-register-applications.md)中的步骤。

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- Azure 帐户必须有权在 Azure Active Directory (Azure AD) 中管理应用程序。 以下任何 Azure AD 角色都包括所需的权限：
  - [应用程序管理员](../roles/permissions-reference.md#application-administrator)
  - [应用程序开发人员](../roles/permissions-reference.md#application-developer)
  - [云应用程序管理员](../roles/permissions-reference.md#cloud-application-administrator)
- 完成[设置租户](quickstart-create-new-tenant.md)快速入门。

## <a name="register-an-application"></a>注册应用程序

注册应用程序会在应用与 Microsoft 标识平台之间建立信任关系。 信任是单向的：应用信任 Microsoft 标识平台，但标识平台并不信任应用。

按照以下步骤创建应用注册：

1. 登录 <a href="https://portal.azure.com/" target="_blank">Azure 门户</a>。
1. 如果有权访问多个租户，请使用顶部菜单中的“目录 + 订阅”筛选器 :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::，以切换到要在其中注册应用程序的租户。
1. 搜索并选择“Azure Active Directory”。
1. 在“管理”下，选择“应用注册” > “新建注册”  。
1. 输入应用程序的显示名称。 应用程序的用户在使用应用时（例如，在登录过程中）可能会看到显示名称。
   你可以随时更改显示名称，多个应用注册可共享同一名称。 应用注册自动生成的应用程序（客户端）ID（而不是其显示名称）在标识平台中唯一地标识应用。
1. 指定可使用该应用程序的人员，这有时称为“登录访问者”。

   | 支持的帐户类型                                                      | 说明                                                                                                                                                                                                                                                                                                                                                                                 |
   | ---------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
   | **仅此组织目录中的帐户**                           | 如果要生成仅供租户中的用户（或来宾）使用的应用程序，请选择此选项。<br><br>通常称为业务线 (LOB) 应用程序，这是 Microsoft 标识平台中的单租户应用程序 。                                                                                                                                          |
   | **任何组织目录中的帐户**                                 | 如果希望任何 Azure Active Directory (Azure AD) 租户中的用户都能够使用你的应用程序，请选择此选项。 例如，如果要构建打算向多个组织提供的软件即服务 (SaaS) 应用程序，则适合使用此选项。<br><br>这类应用在 Microsoft 标识平台中被称为多租户应用程序。 |
   | **任何组织目录中的帐户和个人 Microsoft 帐户** | 若要面向最广泛的客户，请选择此选项。<br><br>通过选择此选项，将注册一个多租户应用程序，该应用还可支持拥有 Microsoft 个人帐户的用户 。                                                                                                                                                                               |
   | **Microsoft 个人帐户**                                              | 如果要构建仅供具有 Microsoft 个人帐户的用户使用的应用程序，请选择此选项。 Microsoft 个人帐户包括 Skype、Xbox、Live 和 Hotmail 帐户。                                                                                                                                                                                                      |

1. 请勿对“重定向 URI (可选)”输入任何内容。 你将在下一部分配置重定向 URI。
1. 选择“注册”，完成初始应用注册。

   :::image type="content" source="media/quickstart-register-app/portal-02-app-reg-01.png" alt-text="Web 浏览器中 Azure 门户的屏幕截图，其中显示了“注册应用程序”窗格。":::

注册完成后，Azure 门户会显示应用注册的“概述”窗格。 你会看到应用程序（客户端）ID。 此值也被称为客户端 ID，它可唯一地标识 Microsoft 标识平台中的应用程序。

> [!IMPORTANT]
> 默认情况下，新应用注册对用户隐藏。 当你准备好让用户在其[“我的应用”页](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)上查看该应用时，可以将其启用。 若要启用该应用，请在 Azure 门户中导航到“Azure Active Directory” > “企业应用程序”并选择该应用。 然后，在“属性”页上将“对用户可见?”切换为“是”。

应用程序的代码（更多情况下通常是应用程序中使用的身份验证库）也会使用客户端 ID。 在验证它从标识平台中接收的安全令牌时，会使用此 ID。

:::image type="content" source="media/quickstart-register-app/portal-03-app-reg-02.png" alt-text="Web 浏览器中 Azure 门户的屏幕截图，其中显示了应用注册的“概述”窗格。":::

## <a name="add-a-redirect-uri"></a>添加重定向 URI

Microsoft 标识平台在重定向 URI 处重定向用户客户端并在身份验证后发送安全令牌。

例如，在生产 Web 应用程序中，重定向 URI 通常是运行应用的公共终结点，比如 `https://contoso.com/auth-response`。 在开发过程中，通常还会添加在本地运行应用的终结点，例如 `https://127.0.0.1/auth-response` 或 `http://localhost/auth-response`。

可通过配置已注册应用程序的[平台设置](#configure-platform-settings)来添加和修改它的重定向 URI。

### <a name="configure-platform-settings"></a>配置平台设置

在 Azure 门户的平台配置中配置每种应用程序类型的设置（包括重定向 URI）。 某些平台（例如 Web 和单页应用程序）要求手动指定重定向 URI 。 对于其他平台（例如移动和桌面），可选择在配置其他设置时为用户生成的重定向 URI。

若要根据面向的平台或设备配置应用程序设置：

1. 在 Azure 门户中的“应用注册”中，选择你的应用程序。
1. 在“管理”下，选择“身份验证”。 
1. 在“平台配置”下，选择“添加平台” 。
1. 在“配置平台”下，选择应用程序类型（平台）的磁贴来配置其设置。

   :::image type="content" source="media/quickstart-register-app/portal-04-app-reg-03-platform-config.png" alt-text="Azure 门户中的“平台配置”窗格的屏幕截图。" border="false":::

   | 平台                            | 配置设置                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
   | ----------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
   | **Web**                             | 输入应用的重定向 URI。 Microsoft 标识平台在此 URI 处重定向用户客户端并在身份验证后发送安全令牌。<br/><br/>为服务器上运行的标准 Web 应用程序选择此平台。                                                                                                                                                                                                                                                                   |
   | **单页应用程序**         | 输入应用的重定向 URI。 Microsoft 标识平台在此 URI 处重定向用户客户端并在身份验证后发送安全令牌。<br/><br/>如果要在 JavaScript 中或使用 Angular、Vue.js、React.js 或 Blazor WebAssembly 等框架构建客户端 Web 应用，请选择此平台。                                                                                                                                                                                    |
   | **iOS/macOS**                     | 输入应用捆绑 ID。 可在“生成设置”或 Xcode 中的 Info.plist 内找到此信息。<br/><br/>指定捆绑 ID 时，系统会为你生成重定向 URI。                                                                                                                                                                                                                                                                                                                                                              |
   | **Android**                         | 输入应用的包名称。 可在 AndroidManifest.xml 文件中找到它。 也请生成签名哈希并输入它。<br/><br/>如果指定这些设置，将生成重定向 URI。                                                                                                                                                                                                                                                                                                                            |
   | **移动和桌面应用程序** | 选择其中一个建议的重定向 URI。 或者指定自定义重定向 URI。<br/><br/>对于使用嵌入式浏览器的桌面应用程序，我们建议<br/>`https://login.microsoftonline.com/common/oauth2/nativeclient`<br/><br/>对于使用系统浏览器的桌面应用程序，我们建议<br/>`http://localhost`<br/><br/>对于不使用最新的 Microsoft 身份验证库 (MSAL) 或未在使用代理的移动应用程序，请选择此平台。 同时也为桌面应用程序选择此平台。 |

1. 选择“配置”以完成平台配置。

### <a name="redirect-uri-restrictions"></a>重定向 URI 限制

对于添加到应用注册的重定向 URI，其格式方面存在某些限制。 有关这些限制的详细信息，请参阅[重定向 URI（回复 URL）的限制和局限](reply-url.md)。

## <a name="add-credentials"></a>添加凭据

凭据供访问 Web API 的[机密客户端应用程序](msal-client-applications.md)使用。 Web 应用、其他 Web API 或服务类型和守护程序类型的应用程序都是机密客户端。 通过凭据，应用程序可以自己的身份进行身份验证，无需用户在运行时进行任何交互。

可将证书和客户端密码（字符串）作为凭据添加到机密客户端应用注册。

:::image type="content" source="media/quickstart-register-app/portal-05-app-reg-04-credentials.png" alt-text="Azure 门户的屏幕截图，其中显示了应用注册中的“证书和密码”窗格。":::

### <a name="add-a-certificate"></a>添加证书

证书有时称为公钥，是建议的凭据类型，因为我们认为证书比客户端机密更安全。 若要详细了解如何在应用程序中使用证书作为身份验证方法，请参阅 [Microsoft 标识平台应用程序身份验证证书凭据](active-directory-certificate-credentials.md)。

1. 在 Azure 门户中的“应用注册”中，选择你的应用程序。
1. 选择“证书和密码” > “上传证书” 。
1. 选择你想要上传的文件。 它必须是以下文件类型之一：.cer、.pem、.crt  。
1. 选择 **添加** 。

### <a name="add-a-client-secret"></a>添加客户端密码

客户端机密有时称为应用程序密码，它是一个字符串值，应用可以使用该值代替证书来标识自身。

我们认为客户端机密的安全性不如证书凭据。 应用程序开发人员在本地开发应用期间有时会使用客户端机密，因为它们比较容易使用。 但是，对于在生产环境中运行的任何应用程序，应该使用证书凭据。

1. 在 Azure 门户中的“应用注册”中，选择你的应用程序。
1. 选择“证书和密码” > “新建客户端密码” 。
1. 添加客户端机密的说明。
1. 选择机密的过期时间，或指定自定义的生存期。
    - 客户端机密生存期限制为两年（24 个月）或更短。 不能指定超过 24 个月的自定义生存期。
    - Microsoft 建议将过期时间值设置为小于 12 个月。
1. 选择 **添加** 。
1. 记录机密的值，以便在客户端应用程序代码中使用。 退出此页面后，此机密值永不再显示。

有关应用程序安全性的建议，请参阅 [Microsoft 标识平台最佳做法和建议](identity-platform-integration-checklist.md#security)。

## <a name="next-steps"></a>后续步骤

客户端应用程序通常需要访问 Web API 中的资源。 可使用 Microsoft 标识平台来保护客户端应用程序。 还可使用该平台授予对 Web API 的设有范围且基于权限的访问。

转到本系列的下一篇快速入门，为 Web API 再创建一个应用注册并公开其范围。

> [!div class="nextstepaction"]
> [配置应用程序以公开 Web API](quickstart-configure-app-expose-web-apis.md)