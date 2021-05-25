---
title: Azure AD 中的 Microsoft 帐户 (MSA) 标识提供者
description: 通过使用 Azure AD 让外部用户（来宾）能使用其 Microsoft 帐户 (MSA) 登录至你的 Azure AD 应用。
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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "101693012"
---
# <a name="microsoft-account-msa-identity-provider-for-external-identities-preview"></a>Microsoft 帐户 (MSA) 标识提供者（用于外部标识）

> [!NOTE]
> Microsoft 帐户标识提供者是 Azure Active Directory 的一项公共预览功能。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

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

## <a name="next-steps"></a>后续步骤

- [添加 Azure Active Directory B2B 协作用户](add-users-administrator.md)
- [向应用添加自助注册](self-service-sign-up-user-flow.md)