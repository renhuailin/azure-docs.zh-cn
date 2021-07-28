---
title: 使用 Azure Active Directory 按需预配用户
description: 了解如何在 Azure Active Directory 中按需预配用户。
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
ms.openlocfilehash: 56c86e5d0f06d66eea1841e5c1157432fd92f0ac
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2021
ms.locfileid: "109785000"
---
# <a name="on-demand-provisioning-in-azure-active-directory"></a>Azure Active Directory 中的按需预配
使用按需预配可在数秒内将用户预配到应用程序。 除此之外，还可以使用此功能执行以下操作：

* 快速排除配置问题。
* 验证已定义的表达式。
* 测试范围筛选器。

## <a name="how-to-use-on-demand-provisioning"></a>如何使用按需预配

1. 登录 **Azure 门户**。
1. 转到“所有服务” > “企业应用程序”。
1. 选择相应应用程序，然后转到“预配配置”页。
1. 通过提供管理员凭据来配置预配。
1. 选择“按需预配”。
1. 按名字、姓氏、显示名称、用户主体名称或电子邮件地址搜索用户。
   > [!NOTE]
   > 对于云 HR 预配应用（Workday/SuccessFactors 到 AD/Azure AD），输入值有所不同。 对于 Workday 方案，请在 Workday 中提供用户的“WID”。 对于 SuccessFactors 方案，请在 SuccessFactors 中提供用户的“personIdExternal”。 
 
1. 选择页面底部的“预配”。

:::image type="content" source="media/provision-on-demand/on-demand-provision-user.jpg" alt-text="显示用于按需预配用户的 Azure 门户 UI 的屏幕截图。":::

## <a name="understand-the-provisioning-steps"></a>了解预配步骤

按需预配过程会尝试说明预配服务预配用户时所执行的步骤。 预配用户通常需要 5 个步骤。 按需预配体验过程中会显示下面各部分说明的一个或多个步骤。

### <a name="step-1-test-connection"></a>步骤 1：测试连接

预配服务会通过请求“测试用户”来尝试授予对目标应用程序的访问权限。 预配服务需要响应指示服务已授权并可继续执行预配步骤。 此步骤只有在失败时才会显示。 此步骤成功时，按需预配体验过程中不会显示此步骤。

#### <a name="troubleshooting-tips"></a>故障排除提示

* 确保向目标应用程序提供了有效凭据，如机密令牌和租户 URL。 所需的凭据因应用程序而异。 若要查看详细的配置教程，请参阅[教程列表](../saas-apps/tutorial-list.md)。 
* 请确保目标应用程序支持对“属性映射”窗格中定义的匹配属性进行筛选。 若要了解支持的筛选器，请参阅应用程序开发人员提供的 API 文档。
* 对于跨域身份管理系统 (SCIM) 应用程序，可以使用 Postman 等工具。 此类工具有助于确保应用程序以 Azure Active Directory (Azure AD) 预配服务所需的方式响应授权请求。 请参阅[示例请求](./use-scim-to-provision-users-and-groups.md#request-3)。

### <a name="step-2-import-user"></a>步骤 2：导入用户

接下来，预配服务从源系统检索用户。 服务检索的用户属性稍后将用于：

* 评估用户是否在预配范围内。
* 查看目标系统是否存在现有的用户。
* 确定要导出到目标系统的用户属性。

#### <a name="view-details"></a>查看详细信息


“查看详细信息”部分显示从源系统（例如 Azure AD）导入的用户属性。

#### <a name="troubleshooting-tips"></a>故障排除提示

* 如果源系统中的用户对象缺少匹配的属性，则可能无法导入用户。 若要解决此故障，请尝试以下方法之一：

  * 使用匹配属性的值更新用户对象。
  * 更改预配配置中的匹配属性。

* 如果导入的列表中缺少所需的属性，请确保该属性在源系统中的用户对象上具有值。 预配服务当前不支持预配 null 属性。
* 请确保预配配置的“属性映射”页包含所需的属性。

### <a name="step-3-determine-if-user-is-in-scope"></a>步骤 3：确定用户是否在范围内

接下来，预配服务确定用户是否在预配[范围](./how-provisioning-works.md#scoping)内。 该服务会考虑以下几个方面：

* 用户是否被分配到应用程序。
* 范围是否设置为“同步已分配的”或“全部同步” 。
* 预配配置中定义的范围筛选器。  

#### <a name="view-details"></a>查看详细信息

“查看详细信息”部分显示已评估的范围条件。 你可能会看到下列一个或多个属性：

* “在源系统中处于活动状态”指示在 Azure AD 中用户的 `IsActive` 属性设置为 true。
* “已分配到应用程序”指示在 Azure AD 中用户已分配到应用程序。
* “范围设定为全部同步”指示范围设置允许租户中的所有用户和组。
* “用户具有所需角色”指示用户具有预配到应用程序所需的角色。 
* 如果为应用程序定义了范围筛选器，则“范围筛选器”也会显示。 此筛选器按以下格式显示：{范围筛选器标题} {范围筛选器属性} {范围筛选器运算符} {范围筛选器值}。

#### <a name="troubleshooting-tips"></a>故障排除提示

* 请确保定义了有效的范围角色。 例如，避免将 [Greater_Than 运算符](./define-conditional-rules-for-provisioning-user-accounts.md#create-a-scoping-filter) 与非整数值一起使用。
* 如果用户没有所需的角色，请参阅[有关预配分配到默认访问角色的用户的提示](./application-provisioning-config-problem-no-users-provisioned.md#provisioning-users-assigned-to-the-default-access-role)。

### <a name="step-4-match-user-between-source-and-target"></a>步骤 4：在源和目标之间匹配用户

在此步骤中，该服务尝试将导入步骤中检索到的用户与目标系统中的用户进行匹配。

#### <a name="view-details"></a>查看详细信息

“查看详细信息”页显示目标系统中匹配的用户的属性。 如下所述，上下文窗格中显示的属性会有所不同：

* 如果目标系统中没有匹配的用户，则将看不到任何属性。
* 如果目标系统中有一个匹配的用户，则会看到目标系统中的匹配用户的属性。
* 如果匹配了多个用户，则会看到所有匹配用户的属性。
* 如果属性映射包含多个匹配属性，则将按顺序评估每个匹配属性，并显示相应属性的匹配用户。

#### <a name="troubleshooting-tips"></a>故障排除提示

* 预配服务可能无法以唯一的方式将源系统中的用户与目标系统中的用户进行匹配。 若要解决此问题，请确保匹配属性是唯一的。
* 请确保目标应用程序支持对定义为匹配属性的属性进行筛选。  

### <a name="step-5-perform-action"></a>步骤 5：执行操作

最后，预配服务执行操作，如创建、更新、删除或跳过用户。

下面的示例说明了按需预配用户成功后显示的内容：

:::image type="content" source="media/provision-on-demand/success-on-demand-provision.jpg" alt-text="说明按需预配用户成功的屏幕截图。":::

#### <a name="view-details"></a>查看详细信息

“查看详细信息”部分显示目标应用程序中已修改的属性。 此显示内容表示预配服务活动的最终输出和已导出的属性。 如果此步骤失败，则显示的属性表示预配服务尝试修改的属性。

#### <a name="troubleshooting-tips"></a>故障排除提示

* 导出更改发生的故障可能大不相同。 请参阅[预配日志文档](../reports-monitoring/concept-provisioning-logs.md#error-codes)，了解常见故障。

## <a name="frequently-asked-questions"></a>常见问题

* 使用按需预配是否需要禁用预配？ 对于使用长期持有者令牌或用户名和密码进行授权的应用程序，无需执行其他步骤。 使用 OAuth 进行授权的应用程序当前需要在使用按需预配之前停止预配作业。 此类应用程序包括 G Suite、Box、Workplace by Facebook 和 Slack。 我们正在努力实现在不停止预配作业的情况下支持对所有应用程序的按需预配。

* 按需预配需要花费多长时间？ 按需预配通常需要不到 30 秒的时间。

## <a name="known-limitations"></a>已知的限制

按需预配当前存在一些已知限制。 请发布[建议和反馈](https://aka.ms/appprovisioningfeaturerequest)，以便我们可以进一步明确接下来要做出的改进。

> [!NOTE]
> 以下限制特定于按需预配功能。 有关应用程序是否支持预配组、删除或其他功能的信息，请参阅相应应用程序的教程。

* Amazon Web Services (AWS) 应用程序不支持按需预配。 
* 不支持按需预配组和角色。
* 按需预配支持禁用应用程序已取消分配的用户。 但是，它不支持禁用或删除 Azure AD 已禁用或删除的用户。 搜索用户时系统不会显示这些用户。

## <a name="next-steps"></a>后续步骤

* [预配故障排除](./application-provisioning-config-problem.md)