---
title: 在 Azure Active Directory 中预配日志 | Microsoft Docs
description: 在 Azure Active Directory 中预配日志的概述。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 4/25/2021
ms.author: markvi
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: d110abaf9621b638d698bef08d579a5ab2c4285f
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113362064"
---
# <a name="provisioning-logs-in-azure-active-directory"></a>在 Azure Active Directory 中预配日志

作为 IT 管理员，你想知道你的 IT 环境的运行状况。 你可通过有关系统运行状况的信息来评估是否需要响应潜在问题以及响应方式。 

为了帮助你实现此目标，Azure Active Directory 门户为你提供了访问三个活动日志的权限：

- **[登录](concept-sign-ins.md)** - 有关登录以及用户如何使用资源的信息。
- **[审核](concept-audit-logs.md)** - 有关应用于租户的更改（如用户和组管理）或应用于租户资源的更新的信息。
- **[预配](concept-provisioning-logs.md)** - 由预配服务执行的活动（例如在 ServiceNow 中创建组，或从 Workday 导入用户）。


本文提供预配日志的概述。 


## <a name="what-can-you-do-with-it"></a>日志有哪些作用？

你可以使用预配日志找到以下问题的答案：

-  在 ServiceNow 中成功创建了哪些组？

-  从 Adobe 中成功删除了哪些用户？

-  在 Active Directory 中成功创建了哪些 Workday 用户？ 


## <a name="who-can-access-it"></a>谁可以访问它们？

以下用户可以访问预配日志中的数据：

- 应用程序所有者（其自己的应用程序的日志）

- 充当“安全管理员”、“安全读取者”、“报告读取者”、“安全操作员”、“应用程序管理员”和“云应用程序管理员”角色的用户

- 拥有 [provisioningLogs 权限](../roles/custom-enterprise-app-permissions.md#full-list-of-permissions)并充当自定义角色的用户

- 全局管理员

## <a name="what-azure-ad-license-do-you-need"></a>需要什么 Azure AD 许可证？

要查看预配活动报告，租户必须具有与之关联的 Azure AD Premium 许可证。 若要升级 Azure AD 版本，请参阅 [Azure Active Directory Premium 入门](../fundamentals/active-directory-get-started-premium.md)。 


## <a name="how-can-you-access-it"></a>如何访问日志？ 

若要访问日志数据，可以使用以下选项：

- Azure 门户

- 将预配日志流式传输到 [Azure Monitor](../app-provisioning/application-provisioning-log-analytics.md)。 此方法可以延长数据保留期，并可生成自定义仪表板、警报和查询。

- 查询 [Microsoft Graph API](/graph/api/resources/provisioningobjectsummary) 以获取预配日志。

- 将预配日志下载为 CSV 或 JSON 文件。



## <a name="where-can-you-find-it-in-the-azure-portal"></a>在 Azure 门户中的何处可以找到日志？

Azure 门户提供了几种用于访问日志的选项。 例如，在 Azure Active Directory 菜单上，可以在“监视”部分打开日志。  

![打开预配日志](./media/concept-sign-ins/sign-ins-logs-menu.png)

此外，可以使用以下链接直接访问登录日志：[https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns)












可以在 [Azure 门户](https://portal.azure.com)的“Azure Active Directory”窗格的“监视”部分选择“预配日志”来访问预配日志。   某些预配记录可能需要在长达两小时后才出现在门户中。

![显示用于访问预配日志的选项的屏幕截图。](./media/concept-provisioning-logs/access-provisioning-logs.png "“预配”日志")



## <a name="what-is-the-default-view"></a>什么是默认视图？

预配日志有一个默认的列表视图，其中显示：

- 标识
- 操作
- 源系统
- 目标系统
- 状态
- 日期


![显示预配日志中默认列的屏幕截图。](./media/concept-provisioning-logs/default-columns.png "默认列")

选择工具栏中的“列”可以自定义列表视图。

![显示用于自定义列的按钮的屏幕截图。](./media/concept-provisioning-logs/column-chooser.png "列选择器")

在此区域中可以显示其他字段，或者删除已显示的字段。

![显示可用列（已选择其中一些列）的屏幕截图。](./media/concept-provisioning-logs/available-columns.png "可用列")

选择列表视图中的某个项可获得更详细的信息。

![显示详细信息的屏幕截图。](./media/concept-provisioning-logs/steps.png "筛选器")


## <a name="filter-provisioning-activities"></a>筛选预配活动

可以筛选预配数据。 某些筛选器值是根据租户动态填充的。 例如，如果租户中未出现任何“create”事件，则不会显示“创建”筛选器选项。

在默认视图中，可选择以下筛选器：

- **标识**
- **日期**
- **状态**
- **Action**


![显示筛选器值的屏幕截图。](./media/concept-provisioning-logs/default-filter.png "筛选器")

使用“标识”筛选器可以指定你所关注的名称或标识。 此标识可以是用户、组、角色或其他对象。 

可按对象的名称或 ID 进行搜索。 ID 因场景而异。 例如，将某个对象从 Azure AD 预配到 Salesforce 时，源 ID 是用户在 Azure AD 中的对象 ID。 目标 ID 是用户在 Salesforce 中的 ID。 从 Workday 预配到 Active Directory 时，源 ID 是 Workday 工作人员的员工 ID。 

> [!NOTE]
> 用户名不一定总会出现在“标识”列中。 始终显示一个 ID。 


“日期”筛选器用于定义已返回数据的时间范围。 可能的值包括：

- 1 个月
- 7 天
- 30 天
- 24 小时
- 自定义时间范围

选择自定义时间范围时，可以配置开始日期和结束日期。

使用“状态”筛选器，可以选择：

- **全部**
- **Success**
- **失败**
- 已跳过

使用“操作”筛选器可筛选以下操作：

- **创建** 
- **更新**
- **删除**
- **禁用**
- **其他**

除默认视图的筛选器以外，还可设置以下筛选器。

![显示可添加为筛选器的字段的屏幕截图。](./media/concept-provisioning-logs/add-filter.png "选取字段")

- **作业 ID**：与启用了预配的每个应用程序关联的唯一作业 ID。   

- **周期 ID**：周期 ID 唯一标识预配周期。 可与产品支持人员共享此 ID，以查找此事件发生时所在的周期。

- **更改 ID**：更改 ID 是预配事件的唯一标识符。 可与产品支持人员共享此 ID，以查找预配事件。   

- **源系统**：可以指定要从何处预配标识。 例如，将对象从 Azure AD 预配到 ServiceNow 时，源系统为 Azure AD。 

- **目标系统**：可以指定要将标识预配到何处。 例如，将对象从 Azure AD 预配到 ServiceNow 时，目标系统为 ServiceNow。 

- **应用程序**：只能显示其显示名称包含特定字符串的应用程序的记录。

## <a name="provisioning-details"></a>预配详细信息 

在预配列表视图中选择某个项时，可获取有关此项的更多详细信息。 详细信息分组到以下选项卡。

![显示包含预配详细信息的四个选项卡的屏幕截图。](./media/concept-provisioning-logs/provisioning-tabs.png "制表符")

- **步骤**：概述预配对象所执行的步骤。 预配对象的过程可能包括四个步骤：
  
  1. 导入对象
  1. 确定对象是否在范围内。
  1. 在源与目标之间匹配对象。
  1. 预配对象（创建、更新、删除或禁用）。

  ![显示“步骤”选项卡上的预配步骤的屏幕截图。](./media/concept-provisioning-logs/steps.png "筛选器")

- **故障排除和建议**：提供错误代码和原因。 仅当发生失败时，才提供错误信息。

- **修改的属性**：显示旧值和新值。 如果没有旧值，则该列是空白的。

- **摘要**：概述发生的情况，并提供源系统和目标系统中对象的标识符。

## <a name="download-logs-as-csv-or-json"></a>以 CSV 或 JSON 格式下载日志

可以转到 Azure 门户中的日志并选择“下载”，来下载预配日志供以后使用。 文件将根据所选的筛选条件进行筛选。 使筛选器尽量具体，以减少下载大小和时间。 

CSV 下载内容包含三个文件：

* **ProvisioningLogs**：下载除预配步骤和修改的属性之外的所有日志。
* **ProvisioningLogs_ProvisioningSteps**：包含预配步骤和更改 ID。 可以使用更改 ID 将事件与其他两个文件相联接。
* **ProvisioningLogs_ModifiedProperties**：包含已更改的特性和更改 ID。 可以使用更改 ID 将事件与其他两个文件相联接。

#### <a name="open-the-json-file"></a>打开 JSON 文件
若要打开 JSON 文件，请使用 [Microsoft Visual Studio Code](https://aka.ms/vscode) 等文本编辑器。 Visual Studio Code 提供语法突出显示，使文件更易于阅读。 还可以使用浏览器（例如 [Microsoft Edge](https://aka.ms/msedge)）以不可编辑的格式打开 JSON 文件。 

#### <a name="prettify-the-json-file"></a>整理 JSON 文件
为了减少下载大小，下载的 JSON 文件采用极简格式。 这种格式可能使得有效负载难以阅读。 参考以下两种做法来整理文件：

- [使用 Visual Studio Code 设置 JSON 格式](https://code.visualstudio.com/docs/languages/json#_formatting)。

- 使用 PowerShell 设置 JSON 格式。 此脚本以包含制表符和空格的格式输出 JSON： 

  ` $JSONContent = Get-Content -Path "<PATH TO THE PROVISIONING LOGS FILE>" | ConvertFrom-JSON`

  `$JSONContent | ConvertTo-Json > <PATH TO OUTPUT THE JSON FILE>`

#### <a name="parse-the-json-file"></a>分析 JSON 文件

下面是使用 PowerShell 处理 JSON 文件的一些示例命令。 可以使用你熟悉的任何编程语言。  

首先，运行以下命令[读取 JSON 文件](/powershell/module/microsoft.powershell.utility/convertfrom-json)：

` $JSONContent = Get-Content -Path "<PATH TO THE PROVISIONING LOGS FILE>" | ConvertFrom-JSON`

现在，可以根据场景分析数据。 下面是几个示例： 

- 输出 JSON 文件中的所有作业 ID：

  `foreach ($provitem in $JSONContent) { $provitem.jobId }`

- 输出操作为“create”的事件的所有更改 ID：

  `foreach ($provitem in $JSONContent) { `
  `   if ($provItem.action -eq 'Create') {`
  `       $provitem.changeId `
  `   }`
  `}`

## <a name="what-you-should-know"></a>要点

下面是有关预配报告的一些提示和注意事项：

- 如果使用的是高级版，Azure 门户会将报告的预配数据存储 30 天；如果使用的是免费版，则存储 7 天。 可将预配日志发布到 [Log Analytics](../app-provisioning/application-provisioning-log-analytics.md)，使保留期超过 30 天。 

- 可以使用更改 ID 特性作为唯一标识符。 例如，在与产品支持人员交互时，这样做非常有帮助。

- 你可能会看到不在范围内的用户的已跳过事件。 这符合预期，尤其是当同步范围设置为所有用户和组时。 服务将评估租户中的所有对象，甚至包括超出范围的对象。 

- 预配日志目前在政府云中不可用。 如果无法访问预配日志，请使用审核日志作为临时解决方法。 

- 预配日志不显示角色导入（适用于 AWS、Salesforce 和 Zendesk）。 可以在审核日志中找到角色导入日志。 

## <a name="error-codes"></a>错误代码

使用下表来更好地了解如何解决在预配日志中发现的错误。 如果有任何错误代码缺失，请使用本页底部的链接提供反馈。 

|错误代码|说明|
|---|---|
|Conflict、EntryConflict|在 Azure AD 或应用程序中更正有冲突的特性值。 或者，如果应该匹配并接管有冲突的用户帐户，请检查匹配特性配置。 有关配置匹配特性的详细信息，请查看[文档](../app-provisioning/customize-application-attributes.md)。|
|TooManyRequests|由于目标应用已过载并且接收的请求过多，因此拒绝了更新用户的此项尝试。 无需采取任何措施。 此项尝试将自动撤消。 Microsoft 也会收到此问题的通知。|
|InternalServerError |目标应用返回了意外的错误。 目标应用程序出现的服务问题可能阻止了此操作正常进行。 此项尝试将在 40 分钟后自动撤消。|
|InsufficientRights、MethodNotAllowed、NotPermitted、Unauthorized| Azure AD 已对目标应用程序进行身份验证，但无权执行更新。 查看目标应用程序提供的任何说明，以及相关的应用程序[教程](../saas-apps/tutorial-list.md)。|
|UnprocessableEntity|目标应用程序返回了意外的响应。 目标应用程序的配置可能不正确，或者目标应用程序出现的服务问题可能阻止了此操作正常进行。|
|WebExceptionProtocolError |连接到目标应用程序时发生 HTTP 协议错误。 无需采取任何措施。 此项尝试将在 40 分钟后自动撤消。|
|InvalidAnchor|预配服务以前创建或匹配的用户不再存在。 确保该用户存在。 若要强制对所有用户进行新的匹配，请使用 Microsoft Graph API [重启作业](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta&preserve-view=true)。 <br><br>重启预配将触发初始周期，这可能需要一段时间才能完成。 重启预配还会删除运行预配服务所用的缓存。 这意味着，必须再次评估租户中的所有用户和组，并可能会删除某些预配事件。|
|NotImplemented | 目标应用返回了意外的响应。 应用配置可能不正确，或者目标应用出现的服务问题可能阻止了此操作正常进行。 查看目标应用程序提供的任何说明，以及相关的应用程序[教程](../saas-apps/tutorial-list.md)。 |
|MandatoryFieldsMissing、MissingValues |由于缺少必需的值，无法创建用户。 更正源记录中缺少的特性值，或者检查匹配特性配置，确保未省略必填字段。 [详细了解](../app-provisioning/customize-application-attributes.md)如何配置匹配特性。|
|SchemaAttributeNotFound |由于指定的特性在目标应用程序中不存在，无法执行该操作。 请参阅有关特性自定义的[文档](../app-provisioning/customize-application-attributes.md)，并确保配置正确。|
|InternalError |Azure AD 预配服务中发生内部服务错误。 无需采取任何措施。 此项尝试将在 40 分钟后自动重试。|
|InvalidDomain |由于特性值包含无效域名，无法执行该操作。 更新用户的域名，或将域名添加到目标应用程序中的允许列表。 |
|超时 |由于目标应用程序做出响应花费的时间过长，无法完成该操作。 无需采取任何措施。 此项尝试将在 40 分钟后自动重试。|
|LicenseLimitExceeded|由于该用户没有可用的许可证，无法在目标应用程序中创建该用户。 为目标应用程序购买更多的许可证。 或者，检查用户分配和特性映射配置，确保使用正确的特性分配正确的用户。|
|DuplicateTargetEntries  |由于发现目标应用程序中的多个用户使用了配置的匹配特性，无法完成该操作。 从目标应用程序中删除重复的用户，或者[重新配置特性映射](../app-provisioning/customize-application-attributes.md)。|
|DuplicateSourceEntries | 由于发现多个用户使用了配置的匹配特性，无法完成该操作。 删除重复的用户，或者[重新配置特性映射](../app-provisioning/customize-application-attributes.md)。|
|ImportSkipped | 评估每个用户时，系统将尝试从源系统导入该用户。 如果所导入的用户缺少特性映射中定义的匹配属性，则往往会发生此错误。 如果匹配特性的用户对象中缺少某个值，系统将无法评估范围限定、匹配或导出更改。 请注意，出现此错误并不意味着该用户在范围内，因为尚未评估用户的范围限定。|
|EntrySynchronizationSkipped | 预配服务已成功查询源系统并已识别用户。 未对用户采取进一步的操作，已跳过该用户。 用户可能已超出范围，或者用户可能已在无需做出进一步的更改的目标系统中。|
|SystemForCrossDomainIdentityManagementMultipleEntriesInResponse| 用于检索用户或组的 GET 请求在响应中收到多个用户或组。 系统预期仅在响应中收到一个用户或组。 [例如](../app-provisioning/use-scim-to-provision-users-and-groups.md#get-group)，如果你执行 GET 请求来检索某个组并提供筛选器来排除成员，而跨域标识管理系统 (SCIM) 终结点返回了这些成员，则就会出现此错误。|

## <a name="next-steps"></a>后续步骤

* [检查用户预配状态](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)
* [为 Azure AD 库应用程序配置用户预配时遇到的问题](../app-provisioning/application-provisioning-config-problem.md)
* [适用于预配日志的图形 API](/graph/api/resources/provisioningobjectsummary)
