---
title: Azure AD 帐户标识提供者
description: 使用 Azure Active Directory 可使外部用户（来宾）能够使用其 Azure AD 工作帐户登录到你的 Azure AD 应用。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 08/09/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00edf144e06710204bf5d6eb477187668b3e5237
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122182963"
---
# <a name="azure-active-directory-azure-ad-identity-provider-for-external-identities"></a>外部标识的 Azure Active Directory (Azure AD) 标识提供者

在默认情况下，Azure Active Directory 可用作 B2B 协作的标识提供者选项。 如果外部来宾用户通过工作单位或学校获得 Azure AD 帐户，他们可以使用其 Azure AD 帐户来兑换你的 B2B 协作邀请，或者完成你的注册用户流程。

## <a name="guest-sign-in-using-azure-active-directory-accounts"></a>使用 Azure Active Directory 帐户的来宾登录

在默认情况下，外部标识标识提供者列表中提供了 Azure Active Directory。 不需要进行进一步的配置，就可以允许来宾用户通过邀请流程或自助注册用户流程使用其 Azure AD 帐户完成登录。

![标识提供者列表中的 Azure AD 帐户](media/azure-ad-account/azure-ad-account-identity-provider.png)

### <a name="azure-ad-account-in-the-invitation-flow"></a>邀请流程中的 Azure AD 帐户

在[邀请来宾用户](add-users-administrator.md)参与 B2B 协作时，可将其 Azure AD 帐户指定为他们用于登录的电子邮件地址。

![使用 Azure AD 帐户进行邀请](media/azure-ad-account/azure-ad-account-invite.png)

### <a name="azure-ad-account-in-self-service-sign-up-user-flows"></a>自助注册用户流程中的 Azure AD 帐户

Azure AD 帐户是适用于自助注册用户流程的一个标识提供者选项。 用户可以使用他们自己的 Azure AD 帐户来注册你的应用程序。 首先需要为租户[启用自助注册](self-service-sign-up-user-flow.md)。 然后，你可以设置应用程序的用户流程，并选择 Azure Active Directory 作为登录选项之一。

![自助注册用户流程中的 Azure AD 帐户](media/azure-ad-account/azure-ad-account-user-flow.png)

## <a name="verifying-the-applications-publisher-domain"></a>验证应用程序的发布者域
从 2020 年 11 月开始，新的应用程序注册在用户同意提示中会显示为未验证，除非[应用程序的发布者域已经过验证](../develop/howto-configure-publisher-domain.md)，并且公司标识已使用 Microsoft 合作伙伴网络进行验证并且与应用程序相关联。 （[详细了解](../develop/publisher-verification-overview.md)此更改。）请注意，对于 Azure AD 用户流，发布者的域仅在使用 [Microsoft 帐户](microsoft-account.md)或其他 Azure AD 租户作为标识提供者}时才会显示。 为了满足这些新要求，请执行下列操作：

1. [使用 Microsoft 合作伙伴网络 (MPN) 帐户验证公司标识](/partner-center/verification-responses)。 此过程会验证有关贵公司和公司主要联系人的信息。
1. 使用下列选项之一，完成发布者验证过程，以将 MPN 帐户与应用注册关联：
   - 如果 Microsoft 帐户标识提供者的应用注册位于 Azure AD 租户中，请[验证应用注册门户中的应用](../develop/mark-app-as-publisher-verified.md)。
   - 如果 Microsoft 帐户标识提供者的应用注册在 Azure AD B2C 租户中，请[使用 Microsoft Graph API（例如 Graph 浏览器）将应用标记为已验证的发布者](../develop/troubleshoot-publisher-verification.md#making-microsoft-graph-api-calls)。

## <a name="next-steps"></a>后续步骤

- [添加 Azure Active Directory B2B 协作用户](add-users-administrator.md)
- [向应用添加自助注册](self-service-sign-up-user-flow.md)