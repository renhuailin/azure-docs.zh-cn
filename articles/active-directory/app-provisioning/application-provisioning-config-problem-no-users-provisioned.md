---
title: 未在我的应用程序中预配用户
description: 当在针对用户预配使用 Azure AD 配置的 Azure AD 库应用程序中没有看到用户时，如何解决面对的常见问题
services: active-directory
author: kenwith
manager: mtillman
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 05/11/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 42bee803e6dfb025f654714a44a8a4aae3b7448b
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2021
ms.locfileid: "109783182"
---
# <a name="no-users-are-being-provisioned"></a>没有用户正在进行预配 
>[!NOTE]
>从 2020 年 4 月 16 日开始，我们就更改了被分配默认访问角色的用户的行为。 有关详细信息，请参阅以下部分。 
>
为应用程序配置自动化预配后（包括验证提供给 Azure AD 以连接至该应用的应用凭据是否有效），用户和/或组将预配到应用。 预配取决于以下事情：

-   哪些用户和组已 **分配** 到应用程序。 请注意，不支持预配嵌套组。 有关分配的详细信息，请参阅[在 Azure Active Directory 中向企业应用分配用户或组](../manage-apps/assign-user-or-group-access-portal.md)。
-   **属性映射** 是否已启用，并且是否经过配置，可将有效属性从 Azure AD 同步至应用。 有关属性映射的详细信息，请参阅[为 Azure Active Directory 中的 SaaS 应用程序自定义用户预配属性映射](customize-application-attributes.md)。
-   是否存在根据特定属性值筛选用户的 **作用域筛选器**。 有关作用域筛选器的详细信息，请参阅[通过作用域筛选器基于属性预配应用程序](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。
  
  
如果发现用户没有预配，请在 Azure AD 中查阅[预配日志（预览版）](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)。 在日志条目中搜索特定用户。

可以在 Azure 门户中访问预配日志，具体方法是在“活动”部分中选择“Azure Active Directory”&gt;“企业应用”&gt;“预配日志（预览版）”    你可以根据用户的名称或者根据源系统或目标系统中的标识符来搜索预配数据。 有关详细信息，请参阅 [预配日志（预览版）](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)。 

预配日志记录了预配服务执行的全部操作，包括查询 Azure AD 以找到预配作用域中分配的用户、查询目标应用以验证是否存在这些用户以及比较系统之间的用户对象。 然后根据比较结果在目标系统中添加、更新或禁用用户帐户。

## <a name="general-problem-areas-with-provisioning-to-consider"></a>要考虑的有关预配的常规问题范围
以下为常规问题范围列表，如果知道从何处着手，可做一番深入了解。

- [预配服务似乎未启动](#provisioning-service-does-not-appear-to-start)
- [即使用户已被分配，预配日志还是显示他们已被跳过且未预配](#provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>预配服务似乎未启动
如果在 Azure 门户的“Azure Active Directory”&gt;“企业应用”“应用程序名称”\] &gt;“预配”部分中将“预配状态”设置为“打开”**&gt; \[**。 但是在后续重新加载后该页面未显示其他状态详细信息，则有可能是因为该服务正在运行，但尚未完成初始周期。 检查上述“预配日志（预览版）”以确定服务正在执行哪些操作，以及是否出现任何错误。

>[!NOTE]
>初始周期可能会持续 20 分钟至几小时，具体取决于 Azure AD 目录的大小与作用域中的预配用户数量。 在初始周期之后同步速度会变快，因为在初始周期后，预配服务将存储代表两个系统状态的水印。 此初始周期会提升后续同步的性能。
>


## <a name="provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>即使用户已被分配，预配日志还是显示他们已被跳过且未预配

当用户在预配日志中显示为“已跳过”时，请务必查看日志的“步骤”选项卡以确定原因。 以下为常见的原因和解决方法：

- **已配置作用域筛选器，** **它根据属性值筛选用户**。 有关范围筛选器的详细信息，请参阅[范围筛选器](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。
- **该用户“未得到有效授权”。** 如果看到此特定错误消息，是因为存储在 Azure AD 中的用户分配记录出现问题。 要解决此问题，请从应用中取消分配用户（或组），并重新分配。 有关分配的详细信息，请参阅[分配用户或组访问权限](../manage-apps/assign-user-or-group-access-portal.md)。
- **所需的属性丢失或未替用户填写。** 在设置预配时需考虑的重要一点是查看并配置属性映射和工作流，它们可以确定哪些用户（或组）属性将从 Azure AD 流向应用程序。 此配置包括设置用于在两个系统之间唯一地标识和匹配用户/组的“匹配属性”。 有关此重要过程的详细信息，请参阅[为 Azure Active Directory 中的 SaaS 应用程序自定义用户预配属性映射](customize-application-attributes.md)。
- **适用于组的属性映射：** 除预配成员的名称和详细信息外，还预配组名称和组的详细信息（前提是某些应用程序支持这样做）。 通过启用或禁用在“预配”选项卡中显示的组对象的“映射”，可启用或禁用此功能。如果启用预配组，请务必查看属性映射以确保相应字段用于“匹配 ID”。 此匹配 ID 可以是显示名称或电子邮件别名。 如果匹配属性为空，或没有为 Azure AD 中的某个组填写时，该组及组成员将不会进行预配。
## <a name="provisioning-users-assigned-to-the-default-access-role"></a>预配分配给默认访问角色的用户
库中应用程序上的默认角色称为“默认访问”角色。 从历史上看，分配给此角色的用户未预配，并在[预配日志](../reports-monitoring/concept-provisioning-logs.md)中由于“未得到有效授权”而被标记为“已跳过”。 

2020 年 4 月 16 日之后创建的预配配置的行为：分配给默认访问角色的用户将与所有其他角色一样被评估。 被分配了默认访问权限的用户将不会因为“未得到有效授权”而被跳过。 

2020 年 4 月 16 日之前创建的预配配置的行为：在接下来的 3 个月，此行为将像今天这样继续进行。 具有默认访问角色的用户将不会因为未得到有效授权而被跳过。 2020 年 7 月之后，该行为将对所有应用程序统一。 我们不会跳过具有默认访问角色的预配用户，因为“未得到有效授权”。 此更改将由 Microsoft 进行，无需客户进行操作。 如果想要确保即使在进行此更改之后，仍然继续跳过这些用户，请应用适当的范围筛选器或从应用程序中取消分配用户，以确保它们不在作用域内。  

如果对这些更改有任何疑问，请联系 provisioningfeedback@microsoft.com
## <a name="next-steps"></a>后续步骤

[Azure AD Connect 同步：了解声明性预配](../hybrid/concept-azure-ad-connect-sync-declarative-provisioning.md)
