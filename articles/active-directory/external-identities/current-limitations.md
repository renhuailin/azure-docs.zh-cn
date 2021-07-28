---
title: B2B 协作的限制 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory B2B 协作的当前限制
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6049b2b8f3562689255113efa3b2454927ffe7ef
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2021
ms.locfileid: "108750745"
---
# <a name="limitations-of-azure-ad-b2b-collaboration"></a>Azure Active Directory B2B 协作的限制
Azure Active Directory (Azure AD) B2B 协作当前具有本文中描述的限制。

## <a name="possible-double-multi-factor-authentication"></a>可能需要进行两次多重身份验证
使用 Azure AD B2B，可在资源组织（邀请方组织）上进行多重身份验证。 [B2B 协作用户的条件访问](conditional-access.md)中对使用此方法的原因进行了详细说明。 如果合作伙伴已设置并实施了多重身份验证，则他们的用户可能必须在其主组织中执行一次身份验证，然后在你的组织中再次进行身份验证。

## <a name="instant-on"></a>即时启用
通过 B2B 协作流，我们将用户添加到目录，并在邀请兑换、应用分配等期间动态更新用户。 更新和写入通常发生在一个目录实例中，并且必须复制到所有实例中。 更新所有实例后完成复制。 有时，如果在一个实例中编写或更新对象，但是检索该对象的调用针对的是另一个实例，就会出现复制延迟。 如果发生这种情况，刷新或重试可有所帮助。 如果正在使用 API 编写应用，则请重试后退操作，这是解决该问题的一个很好的防御措施。

## <a name="azure-ad-directories"></a>Azure AD 目录
Azure AD B2B 受制于 Azure AD 服务目录限制。 有关用户可以创建的目录数以及用户或来宾用户可以属于的目录数的详细信息，请参阅 [Azure AD 服务限制](../enterprise-users/directory-service-limits-restrictions.md)。

## <a name="national-clouds"></a>国家云
[国家云](../develop/authentication-national-cloud.md)是物理上独立的 Azure 实例。 不支持跨国家云边界的 B2B 协作。 例如，如果 Azure 租户位于公共全球云中，则你无法邀请其帐户位于国家云中的用户。 若要与该用户协作，请让他们使用其他电子邮件地址，或者在你的目录中为他们创建成员用户帐户。

## <a name="azure-us-government-clouds"></a>Azure 美国政府云
在 Azure 美国政府云中，支持同时在 Azure 美国政府云中且同时支持 B2B 协作的租户之间进行 B2B 协作。 支持 B2B 协作的 Azure 美国政府租户还可以使用 Microsoft、Google 帐户或者电子邮件一次性密码帐户与社交用户合作。 如果你邀请这些组之外的用户（例如，如果该用户所在的租户不是 Azure 美国政府云的一部分，或者尚不支持 B2B 协作），则邀请将失败或用户无法兑换该邀请。 有关其他限制的详细信息，请参阅 [Azure Active Directory Premium P1 和 P2 变体](../../azure-government/compare-azure-government-global-azure.md#azure-active-directory-premium-p1-and-p2)。

### <a name="how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant"></a>如何判断我的 Azure 美国政府租户是否可以使用 B2B 协作？
要确认 Azure 美国政府云租户是否支持 B2B 协作，请执行以下操作：

1. 在浏览器中，转到以下 URL，并将租户名称替换为 *&lt;tenantname&gt;* ：

   `https://login.microsoftonline.com/<tenantname>/v2.0/.well-known/openid-configuration`

2. 在 JSON 响应中查找d `"tenant_region_scope"`：

   - 如果显示 `"tenant_region_scope":"USGOV”`，则支持 B2B。
   - 如果显示 `"tenant_region_scope":"USG"`，则不支持 B2B。

## <a name="next-steps"></a>后续步骤

请参阅以下有关 Azure AD B2B 协作的文章：

- [什么是 Azure AD B2B 协作？](what-is-b2b.md)
- [委托 B2B 协作邀请](delegate-invitations.md)