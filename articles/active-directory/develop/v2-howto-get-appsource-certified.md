---
title: 使 AppSource 通过 Azure Active Directory 的认证 | Microsoft Docs
description: 详细了解如何使应用程序 AppSource 通过 Azure Active Directory 的认证。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 07/23/2020
ms.author: ryanwi
ms.reviewer: jeedes
ms.custom: aaddev
ms.openlocfilehash: 7ba06e75bdbb2af8c3a825702735460404f873ce
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114464192"
---
# <a name="get-appsource-certified-for-azure-active-directory"></a>使 AppSource 通过 Azure Active Directory 的认证

[Microsoft AppSource](https://appsource.microsoft.com/) 是一个可供业务用户发现、尝试和管理业务线 SaaS 应用程序（独立 SaaS 和现有 Microsoft SaaS 产品的加载项）的目的地。

若要在 AppSource 上列出独立 SaaS 应用程序，你的应用程序必须接受来自拥有 Azure Active Directory (Azure AD) 的任何公司或组织的工作帐户的单一登录。 单一登录过程必须使用 [OpenID Connect](v2-protocols-oidc.md) 或 [OAuth 2.0](v2-oauth2-auth-code-flow.md) 协议。 AppSource 认证不接受 SAML 集成。

## <a name="multi-tenant-applications"></a>多租户应用程序

多租户应用程序是可接受来自拥有 Azure AD 的任何公司或组织的用户的登录，而无需单独实例、配置或部署的应用程序。 AppSource 建议应用程序实施多租户来启用单击免费试用体验。

若要在应用程序中启用多租户，请执行以下操作：
1. 在 [Azure 门户](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps)中，将应用程序注册信息中的 `Multi-Tenanted` 属性设置为 `Yes`。 默认情况下，在 Azure 门户中创建的应用程序将配置为[单租户](#single-tenant-applications)。
1. 将代码更新为向 `common` 终结点发送请求。 为此，请将终结点从 `https://login.microsoftonline.com/{yourtenant}` 更新为 `https://login.microsoftonline.com/common*`。
1. 对于某些平台（如 ASP.NET），还需更新代码以接受多个证书颁发者。

有关多租户的详细信息，请参阅：[如何使用多租户应用程序模式登录任意 Azure Active Directory (Azure AD) 用户](howto-convert-app-to-be-multi-tenant.md)。

### <a name="single-tenant-applications"></a>单租户应用程序

单租户应用程序是仅接受来自定义的 Azure AD 实例的用户登录的应用程序。 将每个用户作为来宾帐户添加到应用程序注册的 Azure AD 实例后，外部用户（包括来自其他组织的工作或学校帐户，或个人帐户）可以登录到单租户应用程序。 

可以通过 [Azure AD B2B 协作](../external-identities/what-is-b2b.md)将用户作为来宾帐户添加到 Azure AD，或者[以编程方式](../../active-directory-b2c/integrate-with-app-code-samples.md)执行此操作。 使用 B2B 时，用户可以创建自助服务门户，而无需接收登录邀请。 有关详细信息，请参阅 [Azure AD B2B 协作注册的自助服务门户](../external-identities/self-service-portal.md)。

单租户应用程序可启用“与我联系”体验，但若要启用 AppSource 建议的单击/免费试用体验，则请改为在应用程序上启用多租户。

## <a name="appsource-trial-experiences"></a>AppSource 试用体验

### <a name="free-trial-customer-led-trial-experience"></a>免费试用（客户导向型试用体验）

客户导向型试用是 AppSource 所推荐的体验，因为它提供对应用程序的单击访问。 下面的示例显示了这种体验：

1.  用户在 AppSource 网站上找到您的应用程序，然后选择“免费试用”选项。

    ![显示客户导向型试用体验的免费试用版](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step1.png)

2.  AppSource 将用户重定向到你网站中的 URL。 网站将自动开始 *单一登录* 过程（页面加载中）。

    ![显示如何将用户重定向到你的网站中的 URL](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step2.png)

3.  用户被重定向到 Microsoft 登录页，用户提供凭据以登录。

    ![显示 Microsoft 登录页](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step3.png)

4. 用户对你的应用程序授权。

    ![示例：应用程序的同意页](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step4.png)

5.  登录完成，且用户被重定向回你的网站。  用户开始进行免费试用。

    ![显示当用户被重定向回到站点时看到的体验](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png)

### <a name="contact-me-partner-led-trial-experience"></a>与我联系（合作伙伴导向型试用体验）

需要执行手动或长期操作来预配用户/公司（例如，应用程序需要预配虚拟机、数据库实例或耗费大量时间才可完成的操作）时，可以使用合作伙伴试用体验。 在这种情况下，用户选择“请求试用”按钮并填写表单后，AppSource 将向你发送用户的联系信息。 收到此信息后，需预配环境并就如何访问试用体验向用户发送说明：<br/><br/>

1. 用户在 AppSource 网站中找到你的应用程序，然后选择“与我联系”。

    ![显示合作伙伴导向型试用体验的“与我联系”](./media/active-directory-devhowto-appsource-certified/partner-led-trial-step1.png)

2. 用户在表单中填写联系信息。

    ![显示带有联系人信息的示例窗体](./media/active-directory-devhowto-appsource-certified/partner-led-trial-step2.png)

3. 你将收到用户的信息，设置试用实例，并发送超链接来访问用户的应用程序。

    ![显示用户信息的占位符](./media/active-directory-devhowto-appsource-certified/usercontact.png)

4. 用户访问你的应用程序并完成单一登录过程。

    ![显示应用程序登录屏幕](./media/active-directory-devhowto-appsource-certified/partner-led-trial-step3.png)

5. 用户对你的应用程序授权。

    ![显示应用程序的示例同意页](./media/active-directory-devhowto-appsource-certified/partner-led-trial-step4.png)

6. 登录完成，且用户被重定向回你的网站。 用户开始进行免费试用。

    ![显示当用户被重定向回到站点时看到的体验](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png)

### <a name="more-information"></a>详细信息

有关 AppSource 试用体验的详细信息，请参阅[此视频](https://aka.ms/trialexperienceforwebapps)。 

## <a name="get-support"></a>获取支持

对于 Azure AD 集成，我们使用 [Microsoft Q&A](/answers/topics/azure-active-directory.html) 和社区来提供支持。

强烈建议先在 [Microsoft Q&A](/answers/topics/azure-active-directory.html) 上提问并浏览现有问题，查看是否已有人提出过相同疑问。 请务必用 [`[azure-active-directory]`](/answers/topics/azure-active-directory.html) 标记你的提问和评论。

使用以下评论部分提供反馈，帮助我们改进内容。

## <a name="next-steps"></a>后续步骤

- 有关构建支持 Azure AD 登录的应用程序的详细信息，请参阅 [Azure AD 的身份验证方案](authentication-flows-app-scenarios.md)。
- 有关如何在 AppSource 中列出你的 SaaS 应用程序的信息，请参阅 [AppSource 合作伙伴信息](https://appsource.microsoft.com/partners)