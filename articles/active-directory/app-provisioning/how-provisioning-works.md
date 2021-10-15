---
title: 了解应用程序预配在 Azure Active Directory 中的工作方式
description: 了解应用程序预配在 Azure Active Directory 中的工作方式。
services: active-directory
author: kenwith
manager: mtillman
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 06/11/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 957ee0b5a7301fa1959b3a88450dd047bfacaad5
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129353660"
---
# <a name="how-application-provisioning-works-in-azure-active-directory"></a>应用程序预配在 Azure Active Directory 中的工作方式

自动预配是指在用户需要访问的云应用程序中创建用户标识和角色。 除了创建用户标识外，自动预配还包括在状态或角色发生更改时维护和删除用户标识。 在开始部署之前，可以阅读本文以了解 Azure AD 预配的工作原理并获取配置建议。 

“Azure AD 预配服务”通过连接到由应用程序供应商提供的用于跨域身份管理系统 (SCIM) 2.0 用户管理 API 终结点，将用户预配到 SaaS 应用和其他系统。 此 SCIM 终结点允许 Azure AD 以编程方式创建、更新和删除用户。 对于选定的应用程序，预配服务还可以创建、更新和删除其他标识相关的对象，例如组和角色。 用于在 Azure AD 和应用程序之间预配的通道使用 HTTPS TLS 1.2 加密进行加密。


![Azure AD 预配服务](./media/how-provisioning-works/provisioning0.PNG)
*图 1：Azure AD 预配服务*

![出站用户预配工作流](./media/how-provisioning-works/provisioning1.PNG)
*图 2：从 Azure AD 到常见 SaaS 应用程序的“出站”用户预配工作流*

![入站用户预配工作流](./media/how-provisioning-works/provisioning2.PNG)
*图 3：从常见人力资本管理 (HCM) 应用程序到 Azure Active Directory 和 Windows Server Active Directory 的“入站”用户预配工作流*

## <a name="provisioning-using-scim-20"></a>使用 SCIM 2.0 进行预配

Azure AD 预配服务使用 [SCIM 2.0 协议](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/bg-p/IdentityStandards)进行自动预配。 该服务连接到应用程序的 SCIM 终结点，并使用 SCIM 用户对象架构和 REST API 来自动预配和取消预配用户和组。 为 Azure AD 库中的大多数应用程序提供基于 SCIM 的预配连接器。 为 Azure AD 生成应用时，开发人员可以使用 SCIM 2.0 用户管理 API 来生成集成了 Azure AD 的 SCIM 终结点以便预配。 有关详细信息，请参阅 [生成 SCIM 终结点并配置用户预配](../app-provisioning/use-scim-to-provision-users-and-groups.md)。

若要为当前没有自动 Azure AD 预配连接器的的应用请求一个连接器，请填写 [Azure Active Directory 应用程序请求](https://aka.ms/aadapprequest)。

## <a name="authorization"></a>授权

Azure AD 需要凭据才能连接到应用程序的用户管理 API。 在为应用程序配置自动用户预配时，需要输入有效凭据。 对于库应用程序，可以参考应用教程来查找应用程序的凭据类型和要求。 对于非库应用程序，可以参考 [SCIM](./use-scim-to-provision-users-and-groups.md#authorization-to-provisioning-connectors-in-the-application-gallery) 文档来了解凭据的类型和要求。 在 Azure 门户中，你将能够让 Azure AD 尝试使用提供的凭据连接到该应用的预配应用来测试凭据。

## <a name="mapping-attributes"></a>映射属性

为第三方 SaaS 应用程序启用用户预配时，Azure 门户通过属性映射控制其属性值。 在预配或更新用户帐户后，映射确定在 Azure AD 和目标应用程序之间转移的用户属性。

Azure AD 用户对象与每个 SaaS 应用的用户对象之间存在预先配置的一组属性和属性映射。 某些应用与用户一起管理其他类型的对象，例如“组”。

在设置预配时，重要的是查看并配置属性映射和工作流，它们可以确定哪些用户（或组）属性将从 Azure AD 流向应用程序。 检查并配置用于在两个系统之间唯一标识和匹配用户/组的匹配属性（“使用此属性匹配对象”）。

可以根据业务需求自定义默认的属性映射。 因此，可以更改或删除现有属性映射或者创建新的属性映射。 有关详细信息，请参阅[为 SaaS 应用程序自定义用户预配属性映射](./customize-application-attributes.md)。

将预配配置到 SaaS 应用程序时，表达式映射是可指定的属性映射类型之一。 对于这些映射，必须编写一个类似于脚本的表达式，允许将用户的数据转换为 SaaS 应用程序更可接受的格式。 有关详细信息，请参阅[为属性映射编写表达式](functions-for-customizing-application-data.md)。

## <a name="scoping"></a>Scoping 
### <a name="assignment-based-scoping"></a>基于分配的范围

对于从 Azure AD 到 SaaS 应用程序的出站设置，依赖于[用户或组分配](../manage-apps/assign-user-or-group-access-portal.md)是确定哪些用户处于预配范围内的最常见方法。 由于用户分配还用于启用单一登录，因此可以使用相同的方法管理访问和预配。 基于分配的范围不适用于入站预配方案，如 Workday 和 Successfactors。

* **组。** 使用 Azure AD Premium 许可计划，可以使用组分配对 SaaS 应用程序的访问权限。 然后，当预配范围设置为“仅同步已分配的用户和组”时，Azure AD 预配服务将根据用户是否是分配给应用程序的组的成员来预配或取消预配用户。 除非应用程序支持组对象，否则不会预配组对象本身。 确保分配给应用程序的组的属性“SecurityEnabled”设置为“True”。

* **动态组。** Azure AD 用户预配服务可以读取和预配[动态组](../enterprise-users/groups-create-rule.md)中的用户。 请牢记以下注意事项和建议：

  * 动态组可能会影响从 Azure AD 到 SaaS 应用程序的端到端预配的性能。

  * SaaS 应用程序中的用户在动态组中预配或取消预配的速度取决于动态组评估成员身份更改的速度。 有关如何检查动态组的处理状态的信息，请参阅[检查成员身份规则的处理状态](../enterprise-users/groups-create-rule.md)。

  * 当用户失去动态组中的成员身份时，将其视为取消预配事件。 在为动态组创建规则时，请考虑这种情况。

* **嵌套组。** Azure AD 用户预配服务无法读取或预配嵌套组中的用户。 该服务只能读取和预配属于显式分配组直接成员的用户。 “应用程序基于组的分配”的限制会影响单一登录（请参阅[使用组来管理对 SaaS 应用程序的访问](../enterprise-users/groups-saasapps.md)）。 相反，直接分配或[限定](define-conditional-rules-for-provisioning-user-accounts.md)包含需要预配的用户的组。

### <a name="attribute-based-scoping"></a>基于属性的范围 

你可以使用范围筛选器定义基于属性的规则，用于确定哪些用户将预配到应用程序。 此方法通常用于从 HCM 应用程序到 Azure AD 和 Active Directory 的入站设置。 在每个 Azure AD 用户预配连接器的属性映射过程中，配置范围筛选器。 有关配置基于属性的范围筛选器的详细信息，请参阅[使用范围筛选器进行基于属性的应用程序预配](define-conditional-rules-for-provisioning-user-accounts.md)。

### <a name="b2b-guest-users"></a>B2B（来宾）用户

可以使用 Azure AD 用户预配服务将 Azure AD 中的 B2B（或来宾）用户预配到 SaaS 应用程序。 但是，为使 B2B 用户使用 Azure AD 登录 SaaS 应用程序，SaaS 应用程序必须通过特定的方式配置其基于 SAML 的单一登录功能。 有关如何配置 SaaS 应用程序以支持 B2B 用户的登录的详细信息，请参阅[为 B2B 协作配置 SaaS 应用](../external-identities/configure-saas-apps.md)。

> [!NOTE]
来宾用户的 userPrincipalName 通常显示为“alias#EXT#@domain.com”。 当 userPrincipalName 作为源属性包含在属性映射中时，将从 userPrincipalName 中除去 #EXT#。 如果需要保留 EXT，请将 userPrincipalName 替换为 originalUserPrincipalName 并作为源属性。 

userPrincipalName = alias@domain.com originalUserPrincipalName = alias#EXT#@domain.com

## <a name="provisioning-cycles-initial-and-incremental"></a>预配周期：初始和增量

如果 Azure AD 是源系统，预配服务将使用[使用增量查询跟踪 Microsoft Graph 数据中的更改](/graph/delta-query-overview)来监视用户和组。 预配服务针对源系统和目标系统运行初始周期，然后运行定期的增量周期。

### <a name="initial-cycle"></a>初始周期

当预配服务启动时，第一个周期将：

1. 查询源系统中的所有用户和组，并检索[属性映射](customize-application-attributes.md)中定义的所有属性。

2. 使用配置的任何[分配](../manage-apps/assign-user-or-group-access-portal.md)或[基于属性的范围筛选器](define-conditional-rules-for-provisioning-user-accounts.md)筛选返回的用户和组。

3. 当分配用户或用户处于预配范围内时，服务使用指定的[匹配属性](customize-application-attributes.md#understanding-attribute-mapping-properties)在目标系统查询匹配的用户。 示例：如果源系统中的 userPrincipal 名称是匹配的属性并映射到目标系统中的 userName，则预配服务会在目标系统中查询与源系统中 userPrincipal 名称值匹配的 userName。

4. 如果在目标系统中未找到匹配的用户，则使用从源系统返回的属性创建匹配的用户。 创建用户帐户后，预配服务将检测并缓存新用户的目标系统 ID。 此 ID 用于对该用户运行以后的所有操作。

5. 如果找到了匹配的用户，则使用源系统提供的属性更新该用户。 匹配用户帐户后，预配服务将检测并缓存新用户的目标系统 ID。 此 ID 用于对该用户运行以后的所有操作。

6. 如果属性映射包含“reference”属性，该服务将对目标系统执行其他更新，以创建并链接引用的对象。 例如，用户可能在目标系统中有“Manager”属性，该属性链接到目标系统中创建的另一个用户。

7. 在初始周期结束时保留水印，可为之后的增量周期提供起点。

某些应用程序（例如 ServiceNow、G Suite 和 Box）不仅支持预配用户，而且还支持预配组及其成员。 在这种情况下，如果在[映射](customize-application-attributes.md)中启用了组预配，则预配服务会同步用户和组，然后同步组成员身份。

### <a name="incremental-cycles"></a>增量周期

初始周期后，所有其他周期将执行以下操作：

1. 在源系统中查询自上次存储水印以来已更新的所有用户和组。

2. 使用配置的任何[分配](../manage-apps/assign-user-or-group-access-portal.md)或[基于属性的范围筛选器](define-conditional-rules-for-provisioning-user-accounts.md)筛选返回的用户和组。

3. 当分配用户或用户处于预配范围内时，服务使用指定的[匹配属性](customize-application-attributes.md#understanding-attribute-mapping-properties)在目标系统查询匹配的用户。

4. 如果在目标系统中未找到匹配的用户，则使用从源系统返回的属性创建匹配的用户。 创建用户帐户后，预配服务将检测并缓存新用户的目标系统 ID。 此 ID 用于对该用户运行以后的所有操作。

5. 如果找到了匹配的用户，则使用源系统提供的属性更新该用户。 如果匹配的是新分配的帐户，则预配服务将检测并缓存新用户的目标系统 ID。 此 ID 用于对该用户运行以后的所有操作。

6. 如果属性映射包含“reference”属性，该服务将对目标系统执行其他更新，以创建并链接引用的对象。 例如，用户可能在目标系统中有“Manager”属性，该属性链接到目标系统中创建的另一个用户。

7. 如果从范围中删除了以前在预配范围内的某个用户（包括未分配的用户），该服务会通过更新在目标系统中禁用该用户。

8. 如果在源系统中禁用或软删除了以前在预配范围内的某个用户，该服务会通过更新在目标系统中禁用该用户。

9. 如果在源系统中硬删除了以前在预配范围内的某个用户，该服务会在目标系统中删除该用户。 在 Azure AD 中，软删除用户 30 天后会将其硬删除。

10. 在增量周期结束时保留新的水印，可为之后的增量周期提供起点。

> [!NOTE]
> 可以根据需要，使用[映射](customize-application-attributes.md)部分中的“目标对象操作”复选框来禁用“创建”、“更新”或“删除”操作   。 还可以使用“accountEnabled”等字段通过属性映射来控制用于在更新期间禁用用户的逻辑。

预配服务会根据[特定于每个应用程序的教程](../saas-apps/tutorial-list.md)中定义的间隔，持续无限期地运行后端到后端的增量周期。 增量周期将继续，直到发生以下某个事件：

- 使用 Azure 门户或使用相应的 Microsoft Graph API 命令手动停止了该服务。
- 在 Azure 门户中使用“重启预配”选项，或使用相应的 Microsoft Graph API 命令触发了新的初始周期。 此操作会清除所有存储的水印，并导致重新评估所有源对象。
- 由于属性映射或范围筛选器发生更改而触发了新的初始周期。 此操作会清除所有存储的水印，并导致重新评估所有源对象。
- 高错误率导致预配进程进入隔离区（参阅下文），并在隔离区保留四周以上。 在此情况下，该服务会自动禁用。

### <a name="errors-and-retries"></a>错误和重试

如果目标系统中的错误阻止在目标系统中添加、更新或删除单个用户，则在下一个同步周期中重试该操作。 错误会不断被重试，并逐渐减少重试的频率。 若要解决失败，管理员需要查看[预配日志](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)，以确定根本原因并采取适当的措施。 常见的失败包括：

- 未在源系统中填充目标系统中所需的某个用户属性
- 源系统中存在一个在目标系统中具有唯一约束的用户属性值，但另一条用户记录中存在相同的值

可在源系统中调整受影响用户的属性值，或调整属性映射，解决这些失败，以避免冲突。

### <a name="quarantine"></a>隔离

如果某个错误（例如管理员凭据无效）导致针对目标系统发出的大部分或所有调用持续失败，则预配作业会进入“隔离”状态。 [预配摘要报告](./check-status-user-account-provisioning.md)中会指示此状态，如果在 Azure 门户中配置了电子邮件通知，则会通过电子邮件告知此状态。

处于隔离状态后，增量周期的频率会逐渐减少为每天一次。

解决所有错误并开始下一个同步周期后，预配作业将退出隔离状态。 如果预配作业在隔离区中保留四周以上，则会禁用预配作业。 可在[此处](./application-provisioning-quarantine-status.md)了解有关隔离状态的详细信息。

### <a name="how-long-provisioning-takes"></a>预配花费多长时间

性能取决于预配作业是运行初始预配周期还是增量周期。 有关预配需要多长时间以及如何监视预配服务状态的详细信息，请参阅[检查用户预配的状态](application-provisioning-when-will-provisioning-finish-specific-user.md)。

### <a name="how-to-tell-if-users-are-being-provisioned-properly"></a>如何判断用户预配是否正确

Azure AD [预配日志（预览）](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)中记录了用户预配服务运行的所有操作。 该日志包括对源系统和目标系统执行的所有读写操作，以及在每次操作期间读取或写入的用户数据。 若要了解如何在 Azure 门户中读取预配日志，请参阅[预配报告指南](./check-status-user-account-provisioning.md)。

## <a name="de-provisioning"></a>取消预配
当用户访问权限被删除时，Azure AD 预配服务通过取消预配帐户来使源系统和目标系统保持同步。

预配服务支持删除和禁用（有时称为软删除）用户。 根据目标应用的实现，“禁用”和“删除”的确切定义会有所不同，但“禁用”通常表示用户无法登录。 “删除”表示已从应用程序中完全删除用户。 对于 SCIM 应用程序，禁用是将用户的 active 属性设置为 false 的请求。 

**配置应用程序以禁用用户**

确保已选中要进行更新的复选框。

确保对你的应用程序使用活动映射。 如果使用应用库中的应用程序，映射可能会略有不同。 请确保对库应用程序使用默认的/现成的映射。

:::image type="content" source="./media/how-provisioning-works/disable-user.png" alt-text="禁用用户" lightbox="./media/how-provisioning-works/disable-user.png":::


**配置应用程序以删除用户**

以下场景会触发禁用或删除操作： 
* 在 Azure AD 中软删除用户（发送到回收站/AccountEnabled 属性设置为 false）。
    在 Azure AD 中删除用户 30 天后，他们将从租户中永久删除。 此时，设置服务将发送 DELETE 请求以永久删除应用程序中的用户。 在 30 天窗口期中的任何时候，你都可以[手动永久删除用户](../fundamentals/active-directory-users-restore.md)，这将向应用程序发送一个删除请求。
* 在 Azure AD 中永久删除/移除回收站中的用户。
* 从应用中取消分配用户。
* 用户从范围内移动到范围外（不再经过范围筛选器）。

:::image type="content" source="./media/how-provisioning-works/delete-user.png" alt-text="删除用户" lightbox="./media/how-provisioning-works/delete-user.png":::

默认情况下，Azure AD 预配服务软删除或禁用超出范围的用户。 如果要重写此默认行为，可以将标志设置为[跳过范围外删除](skip-out-of-scope-deletions.md)

如果发生上述四个事件之一，并且目标应用程序不支持软删除，则预配服务将发送 DELETE 请求，以从应用中永久删除该用户。

如果在属性映射中看到属性 IsSoftDeleted，则该属性用于确定用户的状态以及是否发送 active = false 的更新请求以软删除用户。

**已知的限制**

* 如果从某个应用或从分配给某个应用的组中取消分配先前由预配服务管理的用户，我们将发送禁用请求。 此时，该用户不受服务管理，当他们从目录中删除时，我们不会发送删除请求。
* 不支持在 Azure AD 中预配已禁用的用户。 在预配之前，用户必须在 Azure AD 中处于活动状态。
* 当用户从软删除状态进入活动状态时，Azure AD 预配服务会在目标应用中激活用户，但不会自动还原组成员身份。 目标应用程序应保持处于非活动状态的用户的组成员身份。 如果目标应用程序不支持此功能，可以重启预配以更新组成员身份。 

建议

开发应用程序时，始终支持软删除和硬删除。 当意外禁用用户时，客户可以进行恢复。


## <a name="next-steps"></a>后续步骤

[规划自动用户预配部署](../app-provisioning/plan-auto-user-provisioning.md)

[为库应用配置预配](./configure-automatic-user-provisioning-portal.md)

[创建自己的应用时，生成 SCIM 终结点并配置预配](../app-provisioning/use-scim-to-provision-users-and-groups.md)

[对在应用程序中配置和预配用户时出现的问题进行故障排除](./application-provisioning-config-problem.md)。
