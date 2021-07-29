---
title: Azure AD 帐户标识提供者
description: 使用 Azure Active Directory 可使外部用户（来宾）能够使用其 Azure AD 工作帐户登录到你的 Azure AD 应用。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 06/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d9d7a8e74767982d5089a1308ca36108eb5f106
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111972521"
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

## <a name="next-steps"></a>后续步骤

- [添加 Azure Active Directory B2B 协作用户](add-users-administrator.md)
- [向应用添加自助注册](self-service-sign-up-user-flow.md)