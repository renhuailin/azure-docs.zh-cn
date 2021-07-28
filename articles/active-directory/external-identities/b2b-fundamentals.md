---
title: Azure Active Directory B2B 最佳做法和建议
description: 了解有关 Azure Active Directory 中企业到企业 (B2B) 来宾用户访问的最佳做法和建议。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ddb2a002969b031f25dae2511d679cc520928ff6
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2021
ms.locfileid: "108737776"
---
# <a name="azure-active-directory-b2b-best-practices"></a>Azure Active Directory B2B 最佳做法
本文包含有关 Azure Active Directory (Azure AD) 中企业到企业 (B2B) 协作的建议和最佳做法。

   > [!IMPORTANT]
   > 从 2021 年 10 月起，Microsoft 将不再支持兑换通过创建用于 B2B 协作方案的非托管（“病毒性”或“即时”）Azure AD 帐户和租户进行的邀请。 届时，将为所有现有租户启用电子邮件一次性密码功能，并在默认情况下为新租户启用此功能。 我们将启用电子邮件一次性密码功能，因为它为你的来宾用户提供了一种无缝的回退身份验证方法。 但如果你选择不使用此功能，则可以禁用此功能。 有关详细信息，请参阅[电邮件一次性密码身份验证](one-time-passcode.md)


## <a name="b2b-recommendations"></a>B2B 建议
| 建议 | 注释 |
| --- | --- |
| 为了获得最佳登录体验，请与标识提供者联合 | 请尽可能直接与标识提供者联合，以便受邀用户无需创建 Microsoft 帐户 (MSA) 或 Azure AD 帐户即可登录到你的共享应用和资源。 你可以使用 [Gogle 联合功能](google-federation.md)，使 B2B 来宾用户可以使用其 Google 帐户登录。 也可使用 [SAML/WS-Fed 标识提供者（预览版）功能](direct-federation.md)与其标识提供者 (IdP) 支持 SAML 2.0 或 WS-Fed 协议的任何组织建立联合。 |
| 对于不能通过其他方式进行身份验证的 B2B 来宾，则使用电子邮件一次性密码功能 | 在无法通过 Azure AD、Microsoft 帐户 (MSA) 或 Google 联合身份验证等其他方式对 B2B 来宾用户进行身份验证时，可以使用[电子邮件一次性密码](one-time-passcode.md)功能对其进行身份验证。 当来宾用户兑换邀请或访问共享资源时，他们可以请求临时代码，该代码会发送到他们的电子邮件地址。 他们输入此代码后，可以继续登录。 |
| 在登录页中添加公司品牌 | 你可以自定义登录页面，以便 B2B 来宾用户获得更直观的体验。 请参阅[如何向登录页和访问面板页添加公司品牌](../fundamentals/customize-branding.md)。 |
| 将隐私声明添加到 B2B 来宾用户兑换体验 | 可以将组织隐私声明的 URL 添加到首次邀请兑换过程，使受邀用户必须同意你的隐私条款才能继续。 请参阅[操作指南：在 Azure Active Directory 中添加组织的隐私信息](../fundamentals/active-directory-properties-area.md)。 |
| 使用批量邀请（预览版）功能同时邀请多个 B2B 来宾用户 | 使用 Azure 门户中的批量邀请预览版功能，同时邀请多个来宾用户加入你的组织。 使用此功能可以上传 CSV 文件来创建 B2B 来宾用户并批量发送邀请。 参阅[有关批量邀请 B2B 用户的教程](tutorial-bulk-invite.md)。 |
| 针对多重身份验证 (MFA) 强制实施条件访问策略 | 建议在要与合作伙伴 B2B 用户共享的应用上强制实施 MFA 策略。 这样，无论合作伙伴组织是否使用 MFA，都将对租户中的应用一致地强制实施 MFA。 请参阅 [B2B 协作用户的条件访问](conditional-access.md)。 |
| 如果要强制实施基于设备的条件访问策略，请使用排除列表来允许访问 B2B 用户 | 如果在组织中启用了基于设备的条件访问策略，则 B2B 来宾用户设备将被阻止，因为这些用户不受组织的管理。 你可以创建包含特定合作伙伴用户的排除列表，将其排除在基于设备的条件访问策略之外。 请参阅 [B2B 协作用户的条件访问](conditional-access.md)。 |
| 向 B2B 来宾用户提供直接链接时使用特定于租户的 URL | 可以向来宾提供应用或门户的直接链接，作为邀请电子邮件的替代方法。 此直接链接必须特定于租户。这意味着，它必须包含租户 ID 或已验证的域，以便能够在共享应用所在的租户中对来宾进行身份验证。 请参阅[来宾用户的兑换体验](redemption-experience.md)。 |
| 开发应用时，使用 UserType 来确定来宾用户体验  | 如果你正在开发某个应用程序，并想要为租户用户和来宾用户提供不同的体验，请使用 UserType 属性。 令牌中当前未包括 UserType 声明。 应用程序应使用 Microsoft Graph API 从目录中查询用户以获取其 UserType。 |
| 只有在用户与组织之间的关系发生变化的情况下，才更改 UserType 属性  | 虽然可以使用 PowerShell 将用户的 UserType 属性从 Member 转换为 Guest（反之亦然），但是，只有在用户与组织之间的关系发生变化的情况下，才应更改此属性。 请参阅 [B2B 来宾用户的属性](user-properties.md)。|

## <a name="next-steps"></a>后续步骤

[管理 B2B 共享](delegate-invitations.md)