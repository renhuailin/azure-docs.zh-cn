---
title: Microsoft 帐户 (MSA) 标识提供者的 Azure AD
description: 使用 Azure AD 允许外部用户 (来宾) 使用其 Microsoft 帐户 (MSA) 登录到 Azure AD 应用。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25d3380277d0e653fd5cb03069b7d91cb363dd95
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101693012"
---
# <a name="microsoft-account-msa-identity-provider-for-external-identities-preview"></a>Microsoft 帐户 (MSA) 用于外部标识 (预览版的标识提供程序) 

> [!NOTE]
> Microsoft 帐户标识提供者是 Azure Active Directory 的公共预览功能。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

你的 B2B 来宾用户可以使用自己的个人 Microsoft 帐户进行 B2B 协作，而无需进一步配置。 来宾用户可以兑换你的 B2B 协作邀请或使用其个人 Microsoft 帐户完成注册用户流。

Microsoft 帐户由用户设置，用于访问面向消费者的 Microsoft 产品和云服务（例如 Outlook、OneDrive、Xbox LIVE 或 Microsoft 365）。 此帐户是在 microsoft 运行的 Microsoft 使用者标识帐户系统中创建和存储的。

## <a name="guest-sign-in-using-microsoft-accounts"></a>使用 Microsoft 帐户的来宾登录

默认情况下，在外部标识提供者列表中提供了 Microsoft 帐户。 无需进一步的配置即可允许来宾用户使用邀请流或自助注册用户流来登录他们的 Microsoft 帐户。

!["标识提供者" 列表中的 Microsoft 帐户](media/microsoft-account/microsoft-account-identity-provider.png)

### <a name="microsoft-account-in-the-invitation-flow"></a>邀请流中的 Microsoft 帐户

当你 [邀请来宾用户](add-users-administrator.md) 使用 B2B 协作时，你可以将其 Microsoft 帐户指定为他们将用于登录的电子邮件地址。

![使用 Microsoft 帐户邀请](media/microsoft-account/microsoft-account-invite.png)

### <a name="microsoft-account-in-self-service-sign-up-user-flows"></a>自助服务注册用户流中的 Microsoft 帐户

Microsoft 帐户是自助注册用户流的标识提供者选项。 用户可以使用自己的 Microsoft 帐户注册你的应用程序。 首先，你需要为你的租户 [启用自助服务注册](self-service-sign-up-user-flow.md) 。 然后，你可以为应用程序设置用户流，并选择 Microsoft 帐户作为登录选项之一。

![自助服务注册用户流中的 Microsoft 帐户](media/microsoft-account/microsoft-account-user-flow.png)

## <a name="next-steps"></a>后续步骤

- [添加 Azure Active Directory B2B 协作用户](add-users-administrator.md)
- [向应用添加自助注册](self-service-sign-up-user-flow.md)