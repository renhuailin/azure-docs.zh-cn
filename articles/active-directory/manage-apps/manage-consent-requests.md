---
title: 管理对应用程序的同意和评估同意请求
description: 了解在 Azure Active Directory 中如何在禁用或限制用户同意时管理同意请求，以及如何评估对应用程序的租户范围管理员同意请求。
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 08/25/2021
ms.author: davidmu
ms.reviewer: phsignor
ms.openlocfilehash: 5de11c8f853d471ad616ff0a9df4d01acb896c3f
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129619797"
---
# <a name="manage-consent-to-applications-and-evaluate-consent-requests-in-azure-active-directory"></a>在 Azure Active Directory 中管理对应用程序的同意和评估同意请求

Microsoft 建议[限制用户同意](../../active-directory/manage-apps/configure-user-consent.md)，以允许用户仅同意来自经验证的出版商的应用程序，并且只同意所选择的权限。 对于不满足此策略的应用，决策过程将集中于你的组织的安全和标识管理员团队。

禁用或限制最终用户同意后，有几个重要注意事项可确保组织保持安全，同时仍允许使用业务关键型应用程序。 这些步骤对于尽量减少对组织支持团队和 IT 管理员的影响至关重要，同时可防止第三方应用程序中使用非托管帐户。

## <a name="process-changes-and-education"></a>流程更改和教育

 1. 请考虑启用[管理员同意工作流](configure-admin-consent-workflow.md)，以允许用户直接从同意屏幕请求管理员审批。

 2. 确保所有管理员都了解[权限和同意框架](../develop/consent-framework.md)、[同意提示](../develop/application-consent-experience.md)的工作方式以及[如何评估对租户范围管理员同意的请求](#evaluating-a-request-for-tenant-wide-admin-consent)。
 3. 查看组织的现有流程，以便用户请求对应用程序进行管理员审批，并在必要时进行更新。 如果流程发生更改：
    * 更新相关文档、监视、自动化等。
    * 将流程更改传达给所有受影响的用户、开发人员、支持团队和 IT 管理员。

## <a name="auditing-and-monitoring"></a>审核和监视

1. 在组织中[审核应用和授予的权限](../../security/fundamentals/steps-secure-identity.md#audit-apps-and-consented-permissions)，以确保此前没有不当或可疑的应用程序被授予对数据的访问权限。

2. 查看[检测并修正 Office 365 中的非法同意授权](/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants)以了解其他最佳做法，并防范请求 OAuth 同意的可疑应用程序。

3. 如果你的组织有适当的许可证：

    * 请[在 Microsoft Cloud App Security 中使用其他 OAuth 应用程序审核功能](/cloud-app-security/investigate-risky-oauth)。
    * 请使用 [Azure Monitor 工作簿监视权限和同意](../reports-monitoring/howto-use-azure-monitor-workbooks.md)相关活动。 Consent Insights 工作簿会按失败的同意请求数量提供应用视图。 这可能有助于确定应用程序优先级，以便管理员查看并决定是否向这些应用程序授予管理员同意。

### <a name="additional-considerations-for-reducing-friction"></a>减少摩擦的其他注意事项

若要最大程度减少对已在使用中的受信任业务关键型应用程序的影响，请考虑主动向具有大量用户同意授权的应用程序授予管理员同意：

1. 基于登录日志或同意授权活动，对已添加到组织中并且使用率较高的应用进行清点。 一个 PowerShell [脚本](https://gist.github.com/psignoret/41793f8c6211d2df5051d77ca3728c09)可用于快速轻松地发现具有大量用户同意授权的应用程序。

2. 评估尚未授予管理员同意的排名靠前的应用程序。

   > [!IMPORTANT]
   > 在授予租户范围管理员同意之前，请仔细评估应用程序，即使组织中的许多用户已自行同意。

3. 对于批准的每个应用程序，请使用下面所述的方法之一授予租户范围管理员同意。

4. 对于批准的每个应用程序，请考虑[限制用户访问权限](configure-user-consent.md)。

## <a name="evaluating-a-request-for-tenant-wide-admin-consent"></a>评估对租户范围管理员同意的请求

授予租户范围管理员同意是一种敏感操作。  权限会代表整个组织进行授予，可以包含尝试高特权操作的权限。 例如，角色管理、对所有邮箱或所有站点的完全访问权限以及完全用户模拟。

授予租户范围管理员同意之前，必须确保对于所授予的访问权限级别，你信任应用程序和应用程序发布者。 如果你不确信了解控制应用程序的人员以及应用程序请求获得权限的原因，请勿授予同意。

以下列表提供了一些在评估授予管理员同意的请求时要考虑的建议。

* **了解 Microsoft 标识平台中的 [权限和同意框架](../develop/consent-framework.md)。**

* **了解 [委托的权限与应用程序权限](../develop/v2-permissions-and-consent.md#permission-types)之间的区别。**

   应用程序权限允许应用程序访问整个组织的数据，而无需任何用户交互。 委托的权限允许应用程序代表在某个时候已登录应用程序的用户进行操作。

* **了解所请求的权限。**

   应用程序请求的权限在[同意提示](../develop/application-consent-experience.md)中列出。 展开权限标题会显示权限的说明。 应用程序权限的说明通常以“不以登录用户的身份”结尾。 委托的权限的说明通常以“代表登录用户”结尾。 Microsoft Graph API 的权限在 [Microsoft Graph 权限参考](/graph/permissions-reference)中进行介绍 - 请参阅其他 API 的文档以了解它们公开的权限。

   如果不了解所请求的权限，请勿授予同意。

* **了解请求权限的应用程序以及发布该应用程序的人员。**

   当心恶意应用程序尝试模仿其他应用程序。

   如果怀疑应用程序或其发布者的合法性，请勿授予同意。 而是寻求其他确认（例如，直接从应用程序发布者处）。

* **确保请求的权限与预期从应用程序获得的功能保持一致。**

   例如，提供 SharePoint 站点管理的应用程序可能需要委托的访问权限来读取所有网站集，但不一定需要对所有邮箱的完全访问权限或是目录中的完全模拟权限。

   如果怀疑应用程序请求的权限多于所需权限，请勿授予同意。 请与应用程序发布者联系以获取更多详细信息。

## <a name="granting-consent-as-an-administrator"></a>以管理员身份授予同意

### <a name="granting-tenant-wide-admin-consent"></a>授予租户范围管理员同意

请参阅[向应用程序授予租户范围管理员同意](grant-admin-consent.md)，以获取有关从 Azure 门户、使用 Azure AD PowerShell 或是从同意提示本身授予租户范围管理员同意的分步说明。

### <a name="granting-consent-on-behalf-of-a-specific-user"></a>代表特定用户授予同意

管理员还可以使用 [Microsoft Graph API](/graph/use-the-api) 代表单个用户向委托的权限授予同意，而不是为整个组织授予同意。 有关详细信息，请参阅[代表用户获取访问权限](/graph/auth-v2-user)。

## <a name="limiting-user-access-to-applications"></a>限制用户对应用程序的访问权限

即使授予了租户范围管理员同意，用户对应用程序的访问权限仍可受到限制。 有关如何要求向应用程序分配用户的详细信息，请参阅[分配用户和组的方法](./assign-user-or-group-access-portal.md)。

有关更广泛的概述（包括如何处理其他复杂方案），请参阅[使用 Azure AD 进行应用程序访问管理](what-is-access-management.md)。

## <a name="disable-all-future-user-consent-operations-to-any-application"></a>禁用针对任何应用程序的所有未来的用户同意操作

针对整个目录禁用用户同意操作，可防止最终用户同意任何应用程序。 管理员仍可代表用户执行同意操作。 若要深入了解应用程序同意，以及为何要或不这样操作，请参阅[了解用户和管理员同意](../develop/howto-convert-app-to-be-multi-tenant.md)。

若要禁用用户未来在整个目录中执行的所有同意操作，请执行以下步骤：

1. 打开 [**Azure 门户**](https://portal.azure.com/)，并以“全局管理员”身份登录。
2. 在左侧主导航菜单顶部单击“所有服务”，打开“Azure Active Directory 扩展”。
3. 在筛选器搜索框中键入“Azure Active Directory”，选择“Azure Active Directory”项。
4. 选择“Enterprise 应用程序”，然后选择“管理”部分中的“用户设置”。
:::image type="content" source="media/manage-consent-requests/disable-user-consent-operations.png" alt-text="禁用所有应用的用户同意操作。":::
5. 通过将“用户可以同意应用代表他们访问公司数据”开关设置为“否”来禁用将来的所有用户同意操作，并单击“保存”按钮。

## <a name="next-steps"></a>后续步骤

* [保护标识基础结构的五个步骤](../../security/fundamentals/steps-secure-identity.md#before-you-begin-protect-privileged-accounts-with-mfa)
* [配置管理员同意工作流](configure-admin-consent-workflow.md)
* [配置最终用户如何对应用程序表示同意](configure-user-consent.md)
* [Microsoft 标识平台中的权限和许可](../develop/v2-permissions-and-consent.md)
