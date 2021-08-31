---
title: 规划 Azure Active Directory 的自动用户预配部署
description: 有关在 Azure Active Directory 中规划和执行自动用户预配的指导
services: active-directory
author: kenwith
manager: mtillman
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 07/13/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 9e58650a98539b815173040cf0e47c200045b2bb
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113728491"
---
# <a name="plan-an-automatic-user-provisioning-deployment-in-azure-active-directory"></a>在 Azure Active Directory 中规划自动用户预配部署

许多组织依赖于软件即服务 (SaaS) 应用程序（例如 ServiceNow、Zscaler 和 Slack）来提高最终用户生产效率。 过去，IT 工作人员依赖于手动预配方法（例如上传 CSV 文件或使用自定义脚本）来安全管理每个 SaaS 应用程序中的用户标识。 这些过程很容易出错、不安全且难以管理。

Azure Active Directory (Azure AD) 自动用户预配可以根据业务规则在 SaaS 应用程序中安全地自动创建、维护和删除用户标识，从而简化了此过程。 借助这种自动化，可以随着提高标识管理系统对基于云的解决方案的依赖性，在仅限云的环境和混合环境中高效扩展这些系统。

若要更好地了解该功能，请参阅[使用 Azure Active Directory 在 SaaS 应用程序中自动预配和取消预配用户](../app-provisioning/user-provisioning.md)。

## <a name="learn"></a>了解

用户预配为持续标识监管奠定了基础，并提高了依赖于权威标识数据的业务流程的质量。

### <a name="key-benefits"></a>主要优点

启用自动用户预配的关键优势包括：

* 提高工作效率。 可以使用单个用户预配管理界面管理不同 SaaS 应用程序中的用户标识。 此界面提供一组预配策略。

* **管理风险**。 可以基于员工状态或者定义了角色和/或访问权限的组成员资格自动进行更改，以此提高安全性。

* **解决合规性与治理问题**。 Azure AD 支持每个用户预配请求的本机审核日志。 请求将在源和目标系统中执行。 这样，你便可以在单个屏幕中跟踪访问应用程序的用户。

* **降低成本**。 自动用户预配可以避免与手动预配相关的低效和人为错误，从而降低成本。 它减少了自定义开发的用户预配解决方案、脚本和审核日志的需求。

### <a name="licensing"></a>授权

Azure AD 使用应用程序库菜单中给出的模板提供任何应用程序的自助式集成。 有关许可证要求的完整列表，请参阅 [Azure AD 价格页](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing)。

#### <a name="application-licensing"></a>应用程序许可

对于你要自动预配的应用程序，需要获取其相应的许可证。 向应用程序所有者咨询分配到该应用程序的用户是否具有适合其应用程序角色的许可证。 如果 Azure AD 基于角色管理自动预配，则 Azure AD 中分配的角色必须与应用程序许可证相符。 如果在应用程序中拥有的许可证不正确，可能会导致在用户的预配/更新过程中出错。

### <a name="terms"></a>术语

本文使用了以下术语：

* CRUD 操作 - 对用户帐户执行的操作：创建、读取、更新、删除。

* 单一登录 (SSO) - 用户登录一次后即可访问所有已启用 SSO 的应用程序的能力。 在用户预配上下文中，SSO 是用户通过单个帐户访问使用自动用户预配的所有系统的结果。

* 源系统 - Azure AD 从中进行预配的用户存储库。 Azure AD 是大多数预先集成的预配连接器的源系统。 但是，SAP、Workday 和 AWS 等云应用程序存在一些例外情况。 有关示例，请参阅[从 Workday 到 AD 的用户预配](../saas-apps/workday-inbound-tutorial.md)。

* 目标系统 - Azure AD 要预配到的用户存储库。 目标系统通常是 ServiceNow、Zscaler 和 Slack 等 SaaS 应用程序。 目标系统也可以是 AD 等本地系统。

* [跨域标识管理系统 (SCIM)](https://aka.ms/scimoverview) - 允许自动完成用户预配的开放标准。 SCIM 在标识提供者（例如 Microsoft）与服务提供商（例如 Salesforce）或其他需要用户标识信息的 SaaS 应用之间传达用户标识数据。

### <a name="training-resources"></a>培训资源

| 资源| 链接和说明 |
| - | - |
| 点播网络研讨会| [Manage your Enterprise Applications with Azure AD](https://info.microsoft.com/CO-AZUREPLAT-WBNR-FY18-03Mar-06-ManageYourEnterpriseApplicationsOption1-MCW0004438_02OnDemandRegistration-ForminBody.html)（使用 Azure AD 管理企业应用程序）<br>‎了解 Azure AD 如何帮助你实现单一登录 (SSO) 到企业 SaaS 应用程序，以及有关控制访问的最佳做法。 |
| 视频| [Active Azure Directory 中的用户预配是什么？](https://youtu.be/_ZjARPpI6NI) <br> [How to deploy user provisioning in Active Azure Directory?](https://youtu.be/pKzyts6kfrw)（如何在 Active Azure Directory 中部署用户预配？） <br> [Integrating Salesforce with Azure AD: How to automate User Provisioning](https://azure.microsoft.com/resources/videos/integrating-salesforce-with-azure-ad-how-to-automate-user-provisioning/)（将 Salesforce 与 Azure AD 集成：如何自动完成用户预配） |
| 在线课程| SkillUp Online：[Managing Identities](https://skillup.online/courses/course-v1:Microsoft+AZ-100.5+2018_T3/about)（管理标识） <br> 了解如何将 Azure AD 与多个 SaaS 应用程序集成，并保护用户对这些应用程序的访问。 |
| 书籍| [Modern Authentication with Azure Active Directory for Web Applications (Developer Reference) 1st Edition](https://www.amazon.com/Authentication-Directory-Applications-Developer-Reference/dp/0735696942/ref=sr_1_fkmr0_1?keywords=Azure+multifactor+authentication&qid=1550168894&s=gateway&sr=8-1-fkmr0)（使用 Azure Active Directory 对 Web 应用程序执行新式身份验证（开发人员参考）第 1 版）  <br> ‎这是一部权威性的深入指南，其中介绍了如何为这些新环境构建 Active Directory 身份验证解决方案。 |
| 教程| 参阅[有关如何将 SaaS 应用与 Azure AD 集成的教程列表](../saas-apps/tutorial-list.md)。 |
| 常见问题解答| 有关自动用户预配的[常见问题解答](../app-provisioning/user-provisioning.md) |

### <a name="solution-architectures"></a>解决方案体系结构

Azure AD 预配服务通过连接到由每个应用程序供应商提供的用户管理 API 终结点，将用户预配到 SaaS 应用和其他系统。 这些用户管理 API 终结点允许 Azure AD 以编程方式创建、更新和删除用户。

#### <a name="automatic-user-provisioning-for-hybrid-enterprises"></a>适用于混合企业的自动用户预配

在此示例中，用户和/或组是在已连接到本地目录的 HR 数据库中创建的。 Azure AD 预配服务将管理到目标 SaaS 应用程序的自动用户预配。

 ![用户预配](./media/plan-auto-user-provisioning/hybridprovisioning.png)

工作流说明：

1. 在本地 HR 应用程序/系统（例如 SAP）中创建用户/组。 

1. Azure AD Connect 代理按计划运行从本地 AD 到 Azure AD 的标识（用户和组）同步。

1. Azure AD 预配服务针对源系统和目标系统启动[初始周期](../app-provisioning/user-provisioning.md)。 

1. Azure AD 预配服务在源系统中查询自初始周期以来已更改的任何用户和组，并在[增量周期](../app-provisioning/user-provisioning.md)推送更改。

#### <a name="automatic-user-provisioning-for-cloud-only-enterprises"></a>适用于仅限云的企业的自动用户预配

在此示例中，用户创建操作在 Azure AD 中发生，Azure AD 预配服务会管理对目标 (SaaS) 应用程序进行的自动用户预配。

![该插图显示了通过 Azure AD 预配服务从本地 HR 应用程序到目标 SaaS 应用程序的用户/组创建过程。](./media/plan-auto-user-provisioning/cloudprovisioning.png)

工作流说明：

1. 在 Azure AD 中创建用户/组。

1. Azure AD 预配服务针对源系统和目标系统启动[初始周期](../app-provisioning/user-provisioning.md)。 

1. Azure AD 预配服务在源系统中查询自初始周期以来已更新的任何用户和组，并执行任何[增量周期](../app-provisioning/user-provisioning.md)。

#### <a name="automatic-user-provisioning-for-cloud-hr-applications"></a>适用于云 HR 应用程序的自动用户预配 

在此示例中，用户和/或组是在云 HR 应用程序（例如 Workday 和 SuccessFactors）中创建的。 Azure AD 预配服务和 Azure AD Connect 预配代理将用户数据从云 HR 应用租户预配到 AD 中。 帐户在 AD 中更新后，将通过 Azure AD Connect 同步到 Azure AD，而电子邮件地址和用户名特性可以写回到云 HR 应用租户中。

![图 2](./media/plan-auto-user-provisioning/workdayprovisioning.png)

1.  “HR团队”在云 HR 应用租户中执行事务。
2.  Azure AD 预配服务从云 HR 应用租户运行计划的周期，并识别需要处理以便与 AD 同步的更改。
3.  Azure AD 预配服务使用包含 Azure AD 帐户创建/更新/启用/禁用操作的请求有效负载调用 Azure AD Connect 预配代理。
4.  Azure AD Connect 预配代理使用服务帐户来管理 AD 帐户数据。
5.  Azure AD Connect 运行差异同步以拉取 AD 中的更新。
6.  AD 更新与 Azure AD 同步。 
7.  Azure AD 预配服务将电子邮件特性和用户名从 Azure AD 写回到云 HR 应用租户。

## <a name="plan-the-deployment-project"></a>规划部署项目

考虑组织的需求，以确定在环境中部署用户预配的策略。

### <a name="engage-the-right-stakeholders"></a>让合适的利益干系人参与

如果技术项目失败，通常是由于在影响、结果和责任方面不符合预期而导致的。 若要避免这些问题，请[确保让合适的利益干系人参与](../fundamentals/active-directory-deployment-plans.md)，并通过记录利益干系人及其项目投入和责任，使项目中利益干系人的角色得到充分了解。

### <a name="plan-communications"></a>规划沟通

沟通对于任何新服务的成功都至关重要。 主动与用户交流他们的体验将如何变化、何时会变化以及在遇到问题时如何获取支持。

### <a name="plan-a-pilot"></a>规划试点

我们建议先在包含少量用户的测试环境中运行自动用户预配的初始配置，然后再将其扩展到生产环境中的所有用户。 请参阅有关运行试点的[最佳做法](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot)。

#### <a name="best-practices-for-a-pilot"></a>关于试点的最佳做法  

通过试点，可以在面向所有人部署某项功能之前，先对一个小组进行测试。 确保在测试过程中，组织内的每个用例都经过全面测试。

在最初阶段，以 IT、可用性和其他可以测试和提供反馈的合适用户为目标试点。 使用此反馈进一步改进你发送给用户的信息和指示，并深入了解你的支持人员可能发现的问题类型。

通过增大目标组的范围，将实施范围扩大至更大的用户组。 通过[动态组成员资格](../enterprise-users/groups-dynamic-membership.md)或手动将用户添加到目标用户群这两种方式均可实现上述目标。

## <a name="plan-application-connections-and-administration"></a>规划应用程序连接和管理

使用 Azure AD 门户查看和管理支持预配的所有应用程序。 请参阅[在门户中查找应用](../app-provisioning/configure-automatic-user-provisioning-portal.md)。

### <a name="determine-the-type-of-connector-to-use"></a>确定要使用的连接器类型

启用和配置自动化预配所需的实际步骤因具体应用程序而异。 如果你希望自动预配的应用程序已列入 [Azure AD SaaS 应用库](../saas-apps/tutorial-list.md)中，则应选择[应用特定的集成教程](../saas-apps/tutorial-list.md)来配置其预先集成的用户预配连接器。

如果未列入，请执行以下步骤：

1. 为预先集成的用户预配连接器[创建请求](../develop/v2-howto-app-gallery-listing.md)。 我们的团队将与你和应用程序开发人员协作，将应用程序加入到我们的平台中（如果它支持 SCIM）。

1. 使用应用的 [BYOA SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md) 通用用户预配支持。 只有这样，才能让 Azure AD 在没有预先集成的预配连接器的情况下将用户预配到应用。

1. 如果应用程序能够利用 BYOA SCIM 连接器，请参阅 [BYOA SCIM 集成教程](../app-provisioning/use-scim-to-provision-users-and-groups.md)为应用程序配置 BYOA SCIM 连接器。

有关详细信息，请参阅[可在哪些应用程序和系统中使用 Azure AD 自动用户预配？](../app-provisioning/user-provisioning.md)

### <a name="collect-information-to-authorize-application-access"></a>收集信息以授权应用程序访问

设置自动用户预配是对每个应用程序进行的过程。 对于每个应用程序，需要提供[管理员凭据](../app-provisioning/configure-automatic-user-provisioning-portal.md)以连接到目标系统的用户管理终结点。

下图显示了一种版本的所需管理员凭据：

![用于管理用户帐户预配设置的“预配”屏幕](./media/plan-auto-user-provisioning/userprovisioning-admincredentials.png)

虽然有些应用程序需要管理员用户名和密码，但有些应用程序可能需要持有者令牌。

## <a name="plan-user-and-group-provisioning"></a>规划用户和组预配

如果为企业应用启用用户预配，[Azure 门户](https://portal.azure.com/)将通过特性映射控制其特性值。

### <a name="determine-operations-for-each-saas-app"></a>确定针对每个 SaaS 应用的操作

每个应用程序可以使用唯一的用户或组特性，这些特性必须映射到 Azure AD 中的特性。 应用程序可能只有一部分 CRUD 操作可用。

对于每个应用程序，请记录以下信息：

* 要对目标系统的用户和/或组对象执行的 CRUD 预配操作。 例如，每个 SaaS 应用业务所有者可能不想要执行所有可能的操作。

* 源系统中可用的特性

* 目标系统中可用的特性

* 系统之间的特性映射。

### <a name="choose-which-users-and-groups-to-provision"></a>选择要预配的用户和组

在实现自动用户预配之前，必须确定要预配到应用程序的用户和组。

* 可以使用[范围筛选器](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)定义基于特性的规则，用于确定要将哪些用户预配到应用程序。

* 接下来，根据需要使用[用户和组分配](../manage-apps/assign-user-or-group-access-portal.md)进一步筛选。

### <a name="define-user-and-group-attribute-mapping"></a>定义用户和组特性映射

若要实现自动用户预配，需要定义应用程序所需的用户和组特性。 在 Azure AD 用户对象与 SaaS 应用程序的每个用户对象之间已经预先配置了一组特性和[特性映射](../app-provisioning/configure-automatic-user-provisioning-portal.md)。 并非所有 SaaS 应用都支持组特性。

Azure AD 通过提供常量值或[为特性映射编写表达式](../app-provisioning/functions-for-customizing-application-data.md)支持直接的特性到特性映射。 借助这种灵活性，可以精细控制要在目标系统的特性中填充哪些内容。 可以使用 [Microsoft Graph API](../app-provisioning/export-import-provisioning-configuration.md) 和 Graph Explorer 将用户预配特性映射和架构导出到 JSON 文件并将其导回到 Azure AD 中。

有关详细信息，请参阅[为 Azure Active Directory 中的 SaaS 应用程序自定义用户预配特性映射](../app-provisioning/customize-application-attributes.md)。

### <a name="special-considerations-for-user-provisioning"></a>有关用户预配的特殊注意事项

考虑采取以下措施来减少部署后出现的问题：

* 确保用于在源和目标应用程序之间映射用户/组对象的特性是弹性的。 如果特性发生更改（例如，某个用户调到了公司的其他部门），不应会导致错误地预配用户/组。

* 应用程序可能存在特定的限制和/或要求，需要满足这些要求才能正常进行用户预配。 例如，Slack 会截断某些特性的值。 请参阅特定于每个应用程序的[自动用户预配教程](../saas-apps/tutorial-list.md)。

* 确认源与目标系统之间的架构一致性。 常见问题包括 UPN 或 mail 等特性不匹配。 例如，UPN 在 Azure AD 中设置为 john_smith@contoso.com，而在应用中则设置为 jsmith@contoso.com 。 有关详细信息，请参阅[用户和组架构参考](../app-provisioning/use-scim-to-provision-users-and-groups.md)。

## <a name="plan-testing-and-security"></a>规划测试和安全性

在每个部署阶段，请确保测试结果是否符合预期，并审核预配周期。

### <a name="plan-testing"></a>规划测试

为应用程序配置自动用户预配后，可以运行测试用例来验证此解决方案是否符合组织的要求。

| 方案| 预期结果 |
| - | - |
| 将用户添加到已分配给目标系统的组 | 已在目标系统中预配用户对象。 <br>用户可以登录到目标系统并执行所需的操作。 |
| 将用户从分配给目标系统的组中删除 | 已在目标系统中取消预配用户对象。<br>用户无法登录到目标系统。 |
| 通过任何方法在 Azure AD 中更新用户信息 | 增量周期完成后，已更新的用户特性将反映在目标系统中 |
| 用户超出范围 | 已禁用或删除用户对象。 <br>注意：对于 [Workday 预配](skip-out-of-scope-deletions.md)，将替代此行为。 |

### <a name="plan-security"></a>规划安全性

在部署过程中，通常需要进行安全评审。 如果需要进行安全评审，请参阅提供标识即服务概述的众多 Azure AD [白皮书](https://www.microsoft.com/download/details.aspx?id=36391)。

### <a name="plan-rollback"></a>规划回滚

如果自动用户预配实现在生产环境中无法按预期方式工作，以下回滚步骤可帮助你还原到以前的已知正常状态：

1. 查看[预配摘要报告](../app-provisioning/check-status-user-account-provisioning.md)和[预配日志](../app-provisioning/check-status-user-account-provisioning.md#provisioning-logs-preview)，以确定对受影响的用户和/或组执行了哪些不正确的操作。

1. 使用预配审核日志来确定受影响用户和/或组的上次已知正常状态。 另外请检查源系统（Azure AD 或 AD）。

1. 与应用程序所有者协作，使用上次已知正常状态值来更新应用程序中直接受影响的用户和/或组。

## <a name="deploy-automatic-user-provisioning-service"></a>部署自动用户预配服务

选择符合解决方案要求的步骤。

### <a name="prepare-for-the-initial-cycle"></a>为初始周期做好准备

当 Azure AD 预配服务首次运行时，针对源系统和目标系统的初始周期将为每个目标系统的所有用户对象创建快照。

为应用程序启用自动预配时，初始周期可能需要 20 分钟甚至几个小时。 持续时间取决于 Azure AD 目录的大小，以及预配范围内的用户数。

在初始周期完成后，预配服务将存储这两个系统的状态，从而可以提高后续增量周期的性能。

### <a name="configure-automatic-user-provisioning"></a>配置自动用户预配

使用 [Azure 门户](https://portal.azure.com/)来管理应用程序的自动用户帐户预配和取消预配（前提是应用程序支持此功能）。 请按照[如何设置为自动预配到应用程序？](../app-provisioning/user-provisioning.md)中的步骤操作

还可以使用 [Microsoft 图形 API](/graph/api/resources/synchronization-overview) 配置和管理 Azure AD 用户预配服务。

## <a name="manage-automatic-user-provisioning"></a>管理自动用户预配

部署后，需要管理解决方案。

### <a name="monitor-user-provisioning-operation-health"></a>监视用户预配操作运行状况

成功完成[初始周期](../app-provisioning/user-provisioning.md)后，Azure AD 预配服务将按照特定于每个应用程序的间隔无限期运行增量更新，直到发生以下事件之一：

* 该服务已停止，并使用 [Azure 门户](https://portal.azure.com/)或相应的 [Microsoft Graph API](/graph/api/resources/synchronization-overview) 命令触发了新的初始周期。

* 特性映射或范围筛选器的更改触发了新的初始周期。

* 错误率较高导致预配进程进入隔离区，并保留在隔离区四周以上（达到此时限即会自动将它禁用）。

若要查看这些事件以及预配服务执行的所有其他活动，请参阅 Azure AD [预配日志](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)。

若要了解预配周期的持续时间并监视预配作业的进度，可以[检查用户预配的状态](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)。

### <a name="gain-insights-from-reports"></a>从报告中获取见解

Azure AD 可以通过审核日志和报告，提供组织中用户预配使用情况和运行状况的[其他见解](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)。

管理员应检查预配摘要报告，以监视预配作业的运行状况。 Azure AD 审核日志中记录了用户预配服务执行的所有操作。 请参阅[教程：有关自动用户帐户预配的报告](../app-provisioning/check-status-user-account-provisioning.md)。

建议你承担这些报告的所有权，并根据符合组织要求的频率使用这些报告。 Azure AD 将大部分审核数据保留 30 天。

### <a name="troubleshoot"></a>疑难解答

请参阅以下链接来排查预配期间可能会出现的任何问题：

* [为 Azure AD 库应用程序配置用户预配时遇到的问题](../app-provisioning/application-provisioning-config-problem.md)

* [将特性从本地 Active Directory 同步到 Azure AD 以预配到应用程序](../app-provisioning/user-provisioning-sync-attributes-for-mapping.md)

* [配置 Azure Active Directory 库应用程序的用户预配时遇到保存管理员凭据问题](./user-provisioning.md)

* [没有为任何用户预配 Azure AD 库应用程序](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md)

* [预配到 Azure AD 库应用程序的用户组错误](../manage-apps/add-application-portal-assign-users.md)

### <a name="helpful-documentation"></a>可提供帮助的文档

* [为属性映射编写表达式](../app-provisioning/functions-for-customizing-application-data.md)

* [Azure AD 同步 API 概述](/graph/api/resources/synchronization-overview)

* [跳过删除超出范围的用户帐户](skip-out-of-scope-deletions.md)

* [Azure AD Connect 预配代理：版本发布历史记录](provisioning-agent-release-version-history.md)

#### <a name="resources"></a>资源

* [提供产品反馈](https://feedback.azure.com/forums/169401-azure-active-directory)

* [随时了解 Azure AD 的新增功能](https://azure.microsoft.com/updates/?product=active-directory)

* [Microsoft Q&A Azure AD 论坛](/answers/topics/azure-active-directory.html)

## <a name="next-steps"></a>后续步骤
* [配置自动用户预配](../app-provisioning/configure-automatic-user-provisioning-portal.md)

* [使用 Microsoft Graph API 导出或导入预配配置](../app-provisioning/export-import-provisioning-configuration.md)

* [在 Azure Active Directory 中编写特性映射的表达式](../app-provisioning/functions-for-customizing-application-data.md)