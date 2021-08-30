---
title: Azure AD 中的 Microsoft 帐户 (MSA) 标识提供者
description: 通过使用 Azure AD 让外部用户（来宾）能使用其 Microsoft 帐户 (MSA) 登录至你的 Azure AD 应用。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 08/09/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2333ba6aec10d124d960dfc746bc9df2c449f3bb
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122178843"
---
# <a name="microsoft-account-msa-identity-provider-for-external-identities"></a>Microsoft 帐户 (MSA) 标识提供者（用于外部标识）

B2B 来宾用户可以使用自己的 Microsoft 帐户进行 B2B 协作，且无需进一步的配置。 来宾用户可以兑换 B2B 协作邀请，或者使用其个人 Microsoft 帐户完成注册用户的流程。

Microsoft 帐户由用户设置，用于访问面向使用者的 Microsoft 产品和云服务，例如 Outlook、OneDrive、Xbox LIVE 或 Microsoft 365。 该帐户在由 Microsoft 运行的 Microsoft 使用者标识帐户系统中创建和存储。

## <a name="guest-sign-in-using-microsoft-accounts"></a>使用 Microsoft 帐户的来宾登录

默认情况下在外部标识标识提供者列表中提供 Microsoft 帐户。 不需要进行进一步的配置，就可以允许来宾用户通过邀请流程或自助注册用户流程使用其 Microsoft 帐户完成登录。

![标识提供者列表中的 Microsoft 帐户](media/microsoft-account/microsoft-account-identity-provider.png)

### <a name="microsoft-account-in-the-invitation-flow"></a>邀请流程中的 Microsoft 帐户

邀请[来宾用户](add-users-administrator.md)参与 B2B 协作时，可将其 Microsoft 帐户指定为用于登录的电子邮件地址。

![使用 Microsoft 帐户邀请](media/microsoft-account/microsoft-account-invite.png)

### <a name="microsoft-account-in-self-service-sign-up-user-flows"></a>自助注册用户流程中的 Microsoft 帐户

Microsoft 帐户是自助注册用户流程中的一个标识提供者选项。 用户可以使用自己的 Microsoft 帐户注册应用程序。 首先需要为租户[启用自助注册](self-service-sign-up-user-flow.md)。 然后可以设置应用程序的用户流程，并选择“Microsoft 帐户”作为登录方式之一。

![自助注册用户流程中的 Microsoft 帐户](media/microsoft-account/microsoft-account-user-flow.png)

## <a name="verifying-the-applications-publisher-domain"></a>验证应用程序的发布者域
从 2020 年 11 月开始，新的应用程序注册在用户同意提示中会显示为未验证，除非[应用程序的发布者域已经过验证](../develop/howto-configure-publisher-domain.md)，并且公司标识已使用 Microsoft 合作伙伴网络进行验证并且与应用程序相关联。 （[详细了解](../develop/publisher-verification-overview.md)此更改。）请注意，对于 Azure AD 用户流，发布者的域仅在使用 Microsoft 帐户或其他 [Azure AD 租户](azure-ad-account.md)作为标识提供者时才会显示。 为了满足这些新要求，请执行下列操作：

1. [使用 Microsoft 合作伙伴网络 (MPN) 帐户验证公司标识](/partner-center/verification-responses)。 此过程会验证有关贵公司和贵公司主要联系人的信息。
1. 使用以下选项之一完成发布者验证过程，以便将 MPN 帐户与应用注册相关联：
   - 如果 Microsoft 帐户标识提供者的应用注册在 Azure AD 租户中，请[在应用注册门户中验证应用](../develop/mark-app-as-publisher-verified.md)。
   - 如果 Microsoft 帐户标识提供者的应用注册在 Azure AD B2C 租户中，请[使用 Microsoft Graph API（例如 Graph 浏览器）将应用标记为已验证的发布者](../develop/troubleshoot-publisher-verification.md#making-microsoft-graph-api-calls)。

## <a name="next-steps"></a>后续步骤

- [添加 Azure Active Directory B2B 协作用户](add-users-administrator.md)
- [向应用添加自助注册](self-service-sign-up-user-flow.md)