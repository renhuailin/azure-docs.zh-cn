---
title: Azure 门户 (预览版中的预配日志概述) |Microsoft Docs
description: 获取通过 Azure 门户在 Azure Active Directory 中设置日志报告的简介。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 1/29/2021
ms.author: markvi
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 284b554581534a8493225fba0b70b074fb7dd982
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101651931"
---
# <a name="overview-of-provisioning-logs-in-the-azure-portal-preview"></a>Azure 门户 (预览版中的预配日志概述) 

Azure Active Directory (Azure AD) 中的报告体系结构由以下部分组成：

- 活动： 
    - **登录**：有关托管应用程序和用户登录活动的使用情况的信息。
    - [审核日志](concept-audit-logs.md)：有关用户和组管理、托管应用程序和目录活动的系统活动信息。
    - **预配日志**：有关用户、组和由 Azure AD 预配服务设置的角色的系统活动。 

- 安全性： 
    - 有 **风险的登录**：有 [风险的登录](../identity-protection/overview-identity-protection.md)是指可能由不是用户帐户合法所有者的用户执行的登录尝试。
    - **标记为有风险的用户**：有 [风险的用户](../identity-protection/overview-identity-protection.md) 是可能已泄露的用户帐户的指示器。

本主题提供预配日志的概述。 日志提供以下问题的答案： 

* 已成功在 ServiceNow 中创建哪些组？
* 哪些用户已成功从 Adobe 删除？
* 在 Active Directory 中，Workday 中的哪些用户已成功创建？ 

## <a name="prerequisites"></a>先决条件

这些用户可以访问预配日志中的数据：

* 应用程序所有者 (自己的应用程序的日志) 
* 安全管理员、安全读者、报表读者、安全操作员、应用程序管理员和云应用程序管理员角色中的用户
* 具有[provisioningLogs 权限](../roles/custom-enterprise-app-permissions.md#full-list-of-permissions)的自定义角色中的用户
* 全局管理员


要查看预配活动报告，你的租户必须具有与之关联的 Azure AD Premium 许可证。 若要升级 Azure AD 版，请参阅 [Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md)入门。 


## <a name="ways-of-interacting-with-the-provisioning-logs"></a>与预配日志交互的方式 
客户可以通过四种方式与预配日志交互：

- 按照下一部分中所述，从 Azure 门户访问日志。
- 将预配日志流式传输到 [Azure Monitor](../app-provisioning/application-provisioning-log-analytics.md)。 此方法允许扩展的数据保留，并生成自定义仪表板、警报和查询。
- 查询用于预配日志的 [MICROSOFT GRAPH API](/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta) 。
- 下载 CSV 或 JSON 文件格式的设置日志。

## <a name="access-the-logs-from-the-azure-portal"></a>从 Azure 门户访问日志
可以通过在 [Azure 门户](https://portal.azure.com)的 " **Azure Active Directory** " 窗格的 "**监视**" 部分中选择 "**设置日志**" 来访问设置日志。 某些预配记录可能需要长达两个小时才能出现在门户中。

![显示用于访问设置日志的选项的屏幕截图。](./media/concept-provisioning-logs/access-provisioning-logs.png "“预配”日志")


设置日志有一个默认列表视图，其中显示：

- 标识
- 操作
- 源系统
- 目标系统
- 状态
- 日期


![显示预配日志中的默认列的屏幕截图。](./media/concept-provisioning-logs/default-columns.png "默认列")

可以通过选择工具栏上的 " **列** " 自定义列表视图。

![显示用于自定义列的按钮的屏幕截图。](./media/concept-provisioning-logs/column-chooser.png "列选择器")

此区域使您能够显示其他字段或删除已显示的字段。

![显示选定部分的可用列的屏幕截图。](./media/concept-provisioning-logs/available-columns.png "可用列")

选择列表视图中的某个项可获得更详细的信息。

![显示详细信息的屏幕截图。](./media/concept-provisioning-logs/steps.png "筛选器")


## <a name="filter-provisioning-activities"></a>筛选预配活动

你可以筛选预配数据。 某些筛选器值基于租户动态填充。 例如，如果租户中没有任何 "创建" 事件，则不会有 " **创建** 筛选器" 选项。

在默认视图中，您可以选择以下筛选器：

- **标识**
- **日期**
- **状态**
- **操作**


![显示筛选器值的屏幕截图。](./media/concept-provisioning-logs/default-filter.png "筛选器")

**标识** 筛选器使你能够指定所关注的名称或标识。 此标识可以是用户、组、角色或其他对象。 

可以按对象的名称或 ID 进行搜索。 该 ID 因情况而异。 例如，将对象从 Azure AD 预配到 Salesforce 时，源 ID 是 Azure AD 中用户的对象 ID。 目标 ID 是 Salesforce 中用户的 ID。 从 Workday 预配到 Active Directory 时，源 ID 是 Workday 工作人员员工 ID。 

> [!NOTE]
> 用户的名称可能并不总是出现在 **标识** 列中。 始终会有一个 ID。 


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

**操作** 筛选器使您可以筛选这些操作：

- **创建** 
- **更新**
- **删除**
- **禁用**
- **其他**

除了默认视图的筛选器外，还可以设置以下筛选器。

![显示可作为筛选器添加的字段的屏幕截图。](./media/concept-provisioning-logs/add-filter.png "选择字段")

- **作业 id**：唯一作业 id 与启用了设置的每个应用程序相关联。   

- **周期 id**：周期 id 唯一标识设置周期。 可以与产品支持共享此 ID，以便查找发生此事件的周期。

- **更改 id**：更改 id 是预配事件的唯一标识符。 可以与产品支持共享此 ID 以查找设置事件。   

- **源系统**：你可以指定从何处设置标识。 例如，将对象从 Azure AD 设置为 ServiceNow 时，会 Azure AD 源系统。 

- **目标系统**：可以指定将标识预配到的位置。 例如，将对象从 Azure AD 设置为 ServiceNow 时，目标系统为 ServiceNow。 

- **应用程序**：只能显示显示名称包含特定字符串的应用程序的记录。

## <a name="provisioning-details"></a>预配详细信息 

选择 "设置" 列表视图中的项时，可以获得有关此项的更多详细信息。 详细信息分为以下选项卡。

![显示四个选项卡的屏幕截图，其中包含预配详细信息。](./media/concept-provisioning-logs/provisioning-tabs.png "制表符")

- **步骤**：概述设置对象所需的步骤。 设置对象可能包含以下四个步骤：
  
  1. 导入对象。
  1. 确定对象是否在范围内。
  1. 与源和目标之间的对象匹配。
  1. 预配对象 (创建、更新、删除或禁用) 。

  ![屏幕截图显示 "步骤" 选项卡上的预配步骤。](./media/concept-provisioning-logs/steps.png "筛选器")

- **& 建议的疑难解答**：提供错误代码和原因。 仅当发生故障时，错误信息才可用。

- **修改后的属性**：显示旧值和新值。 如果没有旧值，则该列为空白。

- **摘要**：概述源系统和目标系统中的对象发生了哪些情况和标识符。

## <a name="download-logs-as-csv-or-json"></a>下载 CSV 或 JSON 格式的日志

可以通过转到 Azure 门户中的日志并选择 " **下载**"，下载预配日志供以后使用。 将根据你选择的筛选条件筛选该文件。 使筛选器尽可能具体，以减少下载的大小和时间。 

CSV 下载内容包括三个文件：

* **ProvisioningLogs**：下载除预配步骤和修改的属性之外的所有日志。
* **ProvisioningLogs_ProvisioningSteps**：包含设置步骤和更改 ID。 您可以使用更改 ID 将事件与其他两个文件联接。
* **ProvisioningLogs_ModifiedProperties**：包含已更改的属性和更改 ID。 您可以使用更改 ID 将事件与其他两个文件联接。

#### <a name="open-the-json-file"></a>打开 JSON 文件
若要打开 JSON 文件，请使用文本编辑器，如 [Microsoft Visual Studio 代码](https://aka.ms/vscode)。 Visual Studio Code 通过提供语法突出显示，使文件更易于读取。 你还可以使用浏览器（如 [Microsoft Edge](https://aka.ms/msedge)）以无法编辑的格式打开 JSON 文件。 

#### <a name="prettify-the-json-file"></a>整理对 debug.log JSON 文件
JSON 文件以缩小格式下载，以减少下载大小。 此格式可以使有效负载难以读取。 查看以下两个选项以整理对 debug.log 文件：

- 使用 [Visual Studio Code 格式化 JSON](https://code.visualstudio.com/docs/languages/json#_formatting)。

- 使用 PowerShell 格式化 JSON。 此脚本将以包含制表符和空格的格式输出 JSON： 

  ` $JSONContent = Get-Content -Path "<PATH TO THE PROVISIONING LOGS FILE>" | ConvertFrom-JSON`

  `$JSONContent | ConvertTo-Json > <PATH TO OUTPUT THE JSON FILE>`

#### <a name="parse-the-json-file"></a>分析 JSON 文件

下面是使用 PowerShell 处理 JSON 文件的一些示例命令。 您可以使用任何您喜欢的编程语言。  

首先，通过运行以下命令 [读取 JSON 文件](/powershell/module/microsoft.powershell.utility/convertfrom-json?view=powershell-7.1) ：

` $JSONContent = Get-Content -Path "<PATH TO THE PROVISIONING LOGS FILE>" | ConvertFrom-JSON`

现在，你可以根据方案分析数据。 下面是几个示例： 

- 输出 JSON 文件中的所有作业 Id：

  `foreach ($provitem in $JSONContent) { $provitem.jobId }`

- 输出操作为 "create" 的事件的所有更改 Id：

  `foreach ($provitem in $JSONContent) { `
  `   if ($provItem.action -eq 'Create') {`
  `       $provitem.changeId `
  `   }`
  `}`

## <a name="what-you-should-know"></a>要点

下面是有关预配报表的一些提示和注意事项：

- 如果你有一个免费版，Azure 门户会将报告的预配数据存储30天，如果你有一个免费版，则为7天。 你可以将设置日志发布到 [Log Analytics](../app-provisioning/application-provisioning-log-analytics.md) ，以便保留超过30天的保留期。 

- 您可以使用 "更改 ID" 属性作为唯一标识符。 例如，在与产品支持交互时，这非常有用。

- 对于不在作用域内的用户，可能会看到跳过的事件。 这是预期情况，特别是在同步作用域设置为 "所有用户和组" 时。 服务将评估租户中的所有对象，即使是超出范围的对象。 

- 预配日志当前在政府云中不可用。 如果无法访问预配日志，请使用审核日志作为临时解决方法。 

- 预配日志不显示 (适用于 AWS、Salesforce 和 Zendesk) 的角色导入。 可在审核日志中找到角色导入的日志。 

## <a name="error-codes"></a>错误代码

使用下表来更好地了解如何解决在设置日志中找到的错误。 对于缺少的任何错误代码，请使用此页底部的链接提供反馈。 

|错误代码|说明|
|---|---|
|冲突，EntryConflict|更正 Azure AD 或应用程序中冲突的属性值。 或者，如果有冲突的用户帐户应该匹配并被接管，请查看匹配的属性配置。 有关配置匹配属性的详细信息，请参阅 [文档](../app-provisioning/customize-application-attributes.md) 。|
|TooManyRequests|目标应用拒绝了更新用户的尝试，因为它已重载，接收的请求太多。 无需执行任何操作。 此尝试将自动停用。 Microsoft 还收到此问题的通知。|
|InternalServerError |目标应用返回了意外错误。 目标应用程序的服务问题可能会阻止此操作。 在40分钟后，此尝试将自动停用。|
|InsufficientRights，MethodNotAllowed，NotPermitted，未授权| Azure AD 通过目标应用程序进行身份验证，但无权执行更新。 查看目标应用程序提供的任何说明，并查看相应的应用程序 [教程](../saas-apps/tutorial-list.md)。|
|UnprocessableEntity|目标应用程序返回了意外响应。 目标应用程序的配置可能不正确，或者目标应用程序的服务问题可能会阻止此操作。|
|WebExceptionProtocolError |连接到目标应用程序时出现 HTTP 协议错误。 无需执行任何操作。 在40分钟后，此尝试将自动停用。|
|InvalidAnchor|预配服务以前创建或匹配的用户已不存在。 确保该用户存在。 若要强制实施新的匹配，请使用 Microsoft Graph API 来 [重新启动该作业](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta)。 <br><br>重新启动预配将触发初始周期，这可能需要一些时间才能完成。 重新启动预配还会删除预配服务用于操作的缓存。 这意味着必须再次评估租户中的所有用户和组，并且可能会删除某些预配事件。|
|NotImplemented | 目标应用返回了意外响应。 应用的配置可能不正确，或者目标应用的服务问题可能会阻止此操作。 查看目标应用程序提供的任何说明，并查看相应的应用程序 [教程](../saas-apps/tutorial-list.md)。 |
|MandatoryFieldsMissing, MissingValues |无法创建用户，因为缺少所需的值。 更正源记录中缺少的属性值，或查看匹配的属性配置以确保不省略必填字段。 [详细了解](../app-provisioning/customize-application-attributes.md) 如何配置匹配属性。|
|SchemaAttributeNotFound |操作无法执行，因为指定的属性在目标应用程序中不存在。 请参阅有关属性自定义的 [文档](../app-provisioning/customize-application-attributes.md) ，并确保配置正确。|
|InternalError |Azure AD 预配服务中发生内部服务错误。 无需执行任何操作。 此尝试将在40分钟内自动重试。|
|InvalidDomain |无法执行该操作，因为属性值包含无效的域名。 更新用户的域名或将其添加到目标应用程序中的允许列表。 |
|超时 |操作无法完成，因为目标应用程序的响应时间太长。 无需执行任何操作。 此尝试将在40分钟内自动重试。|
|LicenseLimitExceeded|无法在目标应用程序中创建用户，因为此用户没有可用的许可证。 采购目标应用程序的更多许可证。 或者，查看你的用户分配和属性映射配置，以确保为正确的属性分配正确的用户。|
|DuplicateTargetEntries  |操作无法完成，因为在目标应用程序中找到了多个具有配置的匹配属性的用户。 从目标应用程序中删除重复的用户，或 [重新配置属性映射](../app-provisioning/customize-application-attributes.md)。|
|DuplicateSourceEntries | 操作无法完成，因为找到多个具有配置的匹配属性的用户。 请删除重复的用户，或 [重新配置属性映射](../app-provisioning/customize-application-attributes.md)。|
|ImportSkipped | 评估每个用户时，系统将尝试从源系统导入用户。 如果要导入的用户缺少属性映射中定义的匹配属性，则通常会出现此错误。 如果在 "匹配" 属性的用户对象上不存在值，则系统将无法评估范围、匹配或导出更改。 请注意，存在此错误并不表示用户处于范围内，因为尚未评估用户的范围。|
|EntrySynchronizationSkipped | 预配服务已成功查询源系统并确定了用户。 用户未采取进一步的操作，已跳过这些操作。 用户可能已不在范围内，或者用户可能已在目标系统中，无需进行进一步的更改。|
|SystemForCrossDomainIdentityManagementMultipleEntriesInResponse| 用于检索响应中收到多个用户或组的用户或组的 GET 请求。 系统预期响应中只接收一个用户或组。 [例如](../app-provisioning/use-scim-to-provision-users-and-groups.md#get-group)，如果您执行 GET 请求以检索组，并提供筛选器以排除成员，并且您的系统用于跨域标识管理 (SCIM) 终结点返回成员，则会出现此错误。|

## <a name="next-steps"></a>后续步骤

* [检查用户设置的状态](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)
* [为 Azure AD 库应用程序配置用户预配时遇到的问题](../app-provisioning/application-provisioning-config-problem.md)
* [用于预配日志的图形 API](/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta)