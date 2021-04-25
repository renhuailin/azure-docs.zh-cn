---
title: Azure AD 应用同意体验
titleSuffix: Microsoft identity platform
description: 详细了解 Azure AD 许可体验，以便了解如何在 Azure AD 上管理和开发应用程序时使用它
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 04/06/2021
ms.author: ryanwi
ms.reviewer: jesakowi, asteen
ms.openlocfilehash: c570fc9f30d69f13546353cf6edab4122ae35142
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105390"
---
# <a name="understanding-azure-ad-application-consent-experiences"></a>了解 Azure AD 应用程序许可体验

详细了解 Azure Active Directory (Azure AD) 应用程序许可用户体验， 以便通过更加无缝的许可体验智能地管理组织的应用程序和/或开发应用程序。

## <a name="consent-and-permissions"></a>许可和权限

许可是指用户授权应用程序代表他们访问受保护资源的过程。 可以要求管理员或用户同意访问其组织/个人数据。

授予许可的实际用户体验将根据在用户租户上设置的策略、用户的授权范围（或角色）以及客户端应用程序请求的[权限](v2-permissions-and-consent.md)类型而有所不同。 这意味着应用程序开发人员和租户管理员可以对许可体验进行一些控制。 管理员可以在租户或应用上灵活地设置和禁用策略，以控制其租户中的许可体验。 应用程序开发人员可以指示正在请求的权限类型以及是否希望引导用户完成用户许可流或管理员许可流。

- **用户许可流** 是指应用程序开发人员将用户定向到授权终结点，意图仅记录当前用户的许可。
- **管理员许可流** 是指应用程序开发人员将用户定向到管理员许可终结点，意图记录整个租户的许可。 若要确保管理员许可流正常工作，应用程序开发人员必须列出应用程序清单中 `RequiredResourceAccess` 属性中的所有权限。 有关详细信息，请参阅[应用程序清单](./reference-app-manifest.md)。

## <a name="building-blocks-of-the-consent-prompt"></a>许可提示的构建基块

许可提示旨在确保用户有足够的信息来确定他们是否信任客户端应用程序代表他们访问受保护的资源。 了解构建基块将帮助授予许可的用户做出更明智的决策，并帮助开发人员构建更好的用户体验。

下面的图和表提供了有关许可提示构建基块的信息。

:::image type="content" source="./media/application-consent-experience/consent_prompt.png" alt-text="许可提示的构建基块":::

| # | 组件 | 目的 |
| ----- | ----- | ----- |
| 1 | 用户标识符 | 此标识符表示客户端应用程序正在请求代表其访问受保护资源的用户。 |
| 2 | 标题 | 标题根据用户是完成用户许可流还是管理员许可流而变化。 在用户许可流中，标题将为“请求的权限”，而在管理员许可流中，标题还有额外的一行“为组织接受”。 |
| 3 | 应用徽标 | 此图像应帮助用户直观地了解此应用是否是他们打算访问的应用。 此图像由应用程序开发人员提供，并且未验证此图像的所有权。 |
| 4 | 应用程序名称 | 此值应告知用户哪个应用程序正在请求访问其数据。 请注意，此名称由开发人员提供，并且未验证此应用名称的所有权。 |
| 5 | 发布者域 | 此值应该为用户提供他们可以评估可信度的域。 此域由开发人员提供，并且已验证此发布者域的所有权。 |
| 6 | 已验证的发布者 | 蓝色的“已验证”标志表示应用发布者已使用 Microsoft 合作伙伴网络帐户验证了其身份，并完成了验证过程。|
| 7 | 发布者信息  | 显示应用程序是由 Microsoft 还是由你的组织发布的。 |
| 8 | 权限 | 此列表包含客户端应用程序请求的权限。 用户应始终评估所请求的权限类型，以了解客户端应用程序获权代表他们（如果他们接受）访问哪些数据。 作为应用程序开发人员，最好使用最小特权请求访问权限。 |
| 9 | 权限说明 | 此值由公开权限的服务提供。 若要查看权限说明，必须切换权限旁边的 V 形图标。 |
| 10| 应用条款 | 这些条款包含指向应用程序服务条款和隐私声明的链接。 发布者有责任在其服务条款中概述其规则。 此外，发布者有责任在其隐私声明中公开他们使用和共享用户数据的方式。 如果发布者没有为多租户应用程序提供指向这些值的链接，则会在许可提示上显示加粗警告。 |
| 11 | https://myapps.microsoft.com | 在此链接中，用户可以查看和删除当前有权访问其数据的任何非 Microsoft 应用程序。 |
| 12 | 在此处报告 | 如果你不信任该应用，如果你认为该应用正在模拟其他应用，如果你认为该应用会滥用数据，或出于某些其他原因，则此链接可用于报告可疑应用。 |

## <a name="app-requires-a-permission-within-the-users-scope-of-authority"></a>应用需要用户授权范围内的权限

常见的许可方案是，用户访问的应用需要用户授权范围内的权限集。 用户将定向到用户许可流。

管理员将在传统许可提示上看到一个附加控件，该控件允许他们代表整个租户进行许可。 该控件默认处于关闭状态，因此只有当管理员显式选中该框时，才会代表整个租户授予许可。 到目前为止，此复选框仅对全局管理员角色显示，因此云管理员和应用管理员不会看到此复选框。

:::image type="content" source="./media/application-consent-experience/consent_prompt_1a.png" alt-text="方案 1a 的许可提示":::

用户将看到传统许可提示。

:::image type="content" source="./media/application-consent-experience/consent_prompt_1b.png" alt-text="显示传统同意提示的屏幕截图。":::

## <a name="app-requires-a-permission-outside-of-the-users-scope-of-authority"></a>应用需要用户授权范围外的权限

另一种常见的许可方案是，用户访问的应用至少需要一个用户授权范围外的权限。

管理员将在传统许可提示上看到一个附加控件，该控件允许他们代表整个租户进行许可。

:::image type="content" source="./media/application-consent-experience/consent_prompt_1a.png" alt-text="方案 1a 的许可提示":::

将阻止非管理员用户向该应用程序授予许可，并将告知用户向其管理员索要对该应用的访问权限。

:::image type="content" source="./media/application-consent-experience/consent_prompt_2b.png" alt-text="同意提示的屏幕截图，告知用户向管理员请求对该应用的访问权限":::

## <a name="user-is-directed-to-the-admin-consent-flow"></a>用户将定向到管理员许可流

另一种常见的方案是用户导航到或定向到管理员许可流。

管理员用户将看到管理员许可提示。 此提示上的标题和权限说明已更改，这些更改强调了一个事实：接受此提示即表示授予应用代表整个租户访问所请求数据的权限。

:::image type="content" source="./media/application-consent-experience/consent_prompt_3a.png" alt-text="方案 3a 的许可提示":::

将阻止非管理员用户向该应用程序授予许可，并将告知用户向其管理员索要对该应用的访问权限。

:::image type="content" source="./media/application-consent-experience/consent_prompt_2b.png" alt-text="同意提示的屏幕截图，告知用户向管理员请求对该应用的访问权限":::

## <a name="next-steps"></a>后续步骤

- 获取有关 [Azure AD 同意框架如何实现同意](./quickstart-register-app.md)的分步概述。
- 有关详细信息，请参阅[多租户应用程序如何使用同意框架](./howto-convert-app-to-be-multi-tenant.md)实现“用户”和“管理员”同意（支持更多高级多层应用程序模式）。
- 了解[如何配置应用的发布者域](howto-configure-publisher-domain.md)。