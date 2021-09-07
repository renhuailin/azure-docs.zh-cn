---
title: 集成和管理安全操作与 Microsoft Graph 安全性
description: 通过使用 Microsoft Graph 安全性和 Azure 逻辑应用来提升应用的威胁防护、检测和响应能力
services: logic-apps
ms.suite: integration
author: ecfan
ms.author: preetikr
ms.reviewer: v-ching, estfan, logicappspm
ms.topic: article
ms.date: 02/21/2020
tags: connectors
ms.openlocfilehash: b32d5aee270e3380899a81f49b12a55c468f2bc5
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122865008"
---
# <a name="improve-threat-protection-by-integrating-security-operations-with-microsoft-graph-security--azure-logic-apps"></a>通过将安全操作与 Microsoft Graph 安全性和 Azure 逻辑应用集成，提升威胁防护能力

借助 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)和 [Microsoft Graph 安全性](/graph/security-concept-overview)连接器，可以创建用于集成 Microsoft 安全产品、服务和合作伙伴的自动工作流，从而提升应用的威胁检测、防护和响应能力。 例如，可以创建用于监视和管理 Microsoft Graph 安全性实体（如警报）的 [Azure 安全中心 playbook](../security-center/workflow-automation.md)。 下面介绍了 Microsoft Graph 安全性连接器支持的一些方案：

* 按查询或警报 ID 获取警报。 例如，可以获取非常严重的警报列表。

* 更新警报。 例如，可以更新警报分配、向警报添加注释或标记警报。

* 通过创建[警报订阅 (Webhook)](/graph/api/resources/webhooks)，监视警报何时创建或更改。

* 管理警报订阅。 例如，可以获取有效订阅、延长订阅到期时间或删除订阅。

逻辑应用的工作流可使用操作，以从 Microsoft Graph 安全性连接器获取响应，并让输出可用于工作流中的其他操作。 另外，还可以让工作流中的其他操作使用 Microsoft Graph 安全性连接器操作的输出。 例如，如果通过 Microsoft Graph 安全性连接器获取非常严重的警报，可使用 Outlook 连接器在电子邮件中发送这些警报。 

若要详细了解 Microsoft Graph 安全性，请参阅 [Microsoft Graph 安全性 API 概述](/graph/security-concept-overview)。 如果你不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用？](../logic-apps/logic-apps-overview.md)。 如果正在查找 Power Automate 或 Power Apps，请参阅[什么是 Power Automate？](https://flow.microsoft.com/)或[什么是 Power Apps？](https://powerapps.microsoft.com/)

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。 

* 根据 [Microsoft Graph 安全性身份验证要求](/graph/security-authorization)，必须已明确授予 Azure Active Directory (AD) 租户管理员同意，才能使用 Microsoft Graph 安全性连接器。 若要授予此同意，必须提供 Microsoft Graph 安全性连接器的应用程序 ID 和名称（也可以在 [Azure 门户](https://portal.azure.com)中查找）：

  | 属性 | Value |
  |----------|-------|
  | **应用程序名称** | `MicrosoftGraphSecurityConnector` |
  | **应用程序 ID** | `c4829704-0edc-4c3d-a347-7c4a67586f3c` |
  |||

  若要为连接器授予同意，Azure AD 租户管理员可以执行下面两个步骤之一：

  * [为 Azure AD 应用程序授予租户管理员同意](../active-directory/develop/v2-permissions-and-consent.md)。

  * 在逻辑应用首次运行期间，应用可以通过[应用程序同意体验](../active-directory/develop/application-consent-experience.md)请求获得 Azure AD 租户管理员同意。
   
* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识

* 要访问 Microsoft Graph 安全性实体（如警报）的逻辑应用。 若要使用 Microsoft Graph 安全性触发器，需要一个空白逻辑应用。 若要使用 Microsoft Graph 安全性操作，需要一个由适用于你的场景的触发器开始的逻辑应用。

## <a name="connect-to-microsoft-graph-security"></a>连接到 Microsoft Graph 安全性 

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 登录到 [Azure 门户](https://portal.azure.com/)，在逻辑应用设计器中打开逻辑应用（如果尚未打开）。

1. 对于空白逻辑应用，请先添加触发器和所需的其他任何操作，再添加 Microsoft Graph 安全性操作。

   - 或 -

   对于现有逻辑应用，请在要添加 Microsoft Graph 安全性操作的最后一个步骤下，选择“新建步骤”。

   -或-

   若要在步骤之间添加操作，请将鼠标指针移到步骤之间的箭头上。 选择出现的加号 (+)，然后选择“添加操作”。

1. 在搜索框中，输入“microsoft graph 安全性”作为筛选器。 从操作列表中选择所需的操作。

1. 使用 Microsoft Graph 安全性凭据登录。

1. 为所选操作提供必要的详细信息，并继续构建逻辑应用的工作流。

## <a name="add-triggers"></a>添加触发器

在 Azure 逻辑应用中，每个逻辑应用都必须从[触发器](../logic-apps/logic-apps-overview.md#logic-app-concepts)开始，该触发器在发生特定事件或特定条件得到满足的情况下触发。 每当触发器触发时，逻辑应用引擎就会创建一个逻辑应用实例并开始运行应用的工作流。

> [!NOTE] 
> 触发器触发时，将处理所有新警报。 如果未收到任何警报，则会跳过触发器运行。 下一次触发器轮询的发生将基于触发器的属性中指定的重复周期间隔。

此示例演示在将新警报发送到应用时，如何启动逻辑应用工作流。

1.  在 Azure 门户或 Visual Studio 中，创建一个空白的逻辑应用，这会打开逻辑应用设计器。 此示例使用 Azure 门户。

1.  在设计器的搜索框中，输入“microsoft graph 安全性”作为筛选器。 从触发器列表中选择此触发器：所有新警报

1.  在触发器中，提供要监视的警报的相关信息。 有关更多属性，请打开“添加新参数”列表，并选择一个参数将该属性添加到触发器。

   | 属性 | 属性 (JSON) | 必须 | 类型 | 说明 |
   |----------|-----------------|----------|------|-------------|
   | **时间间隔** | `interval` | 是 | Integer | 一个正整数，描述工作流基于频率运行的频繁度。 下面是最小和最大间隔： <p><p>- 月：1-16 个月 <br>- 天：1-500 天 <br>- 小时：1-12,000 小时 <br>- 分钟：1-72,000 分钟 <br>- 秒：1-9,999,999 秒 <p>例如，如果间隔为 6，频率为“月”，则重复周期为每 6 个月。 |
   | **频率** | `frequency` | 是 | String | 定期计划的时间单位：“秒”、“分钟”、“小时”、“天”、“周”或“月”      |
   | **时区** | `timeZone` | 否 | String | 仅当指定启动时间时才适用，因为此触发器不接受 [UTC 时差](https://en.wikipedia.org/wiki/UTC_offset)。 选择要应用的时区。 |
   | **开始时间** | `startTime` | 否 | String | 采用以下格式提供启动日期和时间： <p><p>如果选择了时区，则格式为 YYYY-MM-DDThh:mm:ss <p>-或- <p>如果未选择时区，则格式为 YYYY-MM-DDThh:mm:ssZ <p>例如，如果需要 2017 年 9 月 18 日下午 2 点，则指定“2017-09-18T14:00:00”并选择时区（如“太平洋标准时间”）。 或者指定“2017-09-18T14:00:00Z”且不选择时区。 <p>**注意：** 此开始时间在未来最长为 49 年，并且必须遵循 [UTC 日期时间格式](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)的 [ISO 8601 日期时间规范](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)，但没有 [UTC 时差](https://en.wikipedia.org/wiki/UTC_offset)。 如果未选择时区，必须在末尾添加字母“Z”（无空格）。 这个“Z”指等效的[航海时间](https://en.wikipedia.org/wiki/Nautical_time)。 <p>对于简单计划，开始时间指首次运行时间；对于复杂计划，触发器的激发时间不会早于开始时间。 [*可通过哪些方式使用开始日期和时间？*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   ||||||

1.  完成后，请在设计器工具栏上选择“保存”。

1.  现在请继续向逻辑应用添加一个或多个操作，以便完成需对触发器结果执行的任务。

## <a name="add-actions"></a>添加操作

下面更详细地介绍了如何使用各种 Microsoft Graph 安全性连接器操作。

### <a name="manage-alerts"></a>管理警报

若要筛选、排序或获取最新结果，只需提供 [Microsoft Graph支持的 ODATA 查询参数](/graph/query-parameters)。 请勿指定完整的基 URL 或 HTTP 操作，例如 `https://graph.microsoft.com/v1.0/security/alerts` 或 `GET`/`PATCH` 操作。 下面的具体示例展示了在要获取非常严重的警报列表时“获取警报”操作的参数：

`Filter alerts value as Severity eq 'high'`

若要详细了解可以与此连接器一起使用的查询，请参阅 [Microsoft Graph 安全性警报参考文档](/graph/api/alert-list)。 若要使用此连接器增强体验，请详细了解连接器支持的[架构属性警报](/graph/api/resources/alert)。

| 操作 | 说明 |
|--------|-------------|
| **获取警报** | 获取按一个或多个[警报属性](/graph/api/resources/alert)筛选出的警报，例如 `Provider eq 'Azure Security Center' or 'Palo Alto Networks'`。 | 
| **按 ID 获取警报** | 按警报 ID 获取特定警报。 | 
| **更新警报** | 按警报 ID 更新特定警报。 若要确保在请求中传递必需的可编辑属性，请参阅[可编辑警报属性](/graph/api/alert-update)。 例如，若要向安全分析师分配警报以供调查，可以更新警报的“分配对象”属性。 |
|||

### <a name="manage-alert-subscriptions"></a>管理警报订阅

Microsoft Graph 支持[订阅](/graph/api/resources/subscription)或 [Webhook](/graph/api/resources/webhooks)。 若要获取、更新或删除订阅，请向 Microsoft Graph 实体构造提供 [Microsoft Graph 支持的 ODATA 查询参数](/graph/query-parameters)，并添加后跟 ODATA 查询的 `security/alerts`。 请勿添加基 URL（例如，`https://graph.microsoft.com/v1.0`）。 而是改用此示例中的格式：

`security/alerts?$filter=status eq 'NewAlert'`

| 操作 | 说明 |
|--------|-------------|
| **创建订阅** | [创建订阅](/graph/api/subscription-post-subscriptions)，用于通知所发生的任何更改。 可以对此订阅筛选出所需的特定警报类型。 例如，可以创建订阅，用于通知非常严重的警报。 |
| **获取有效订阅** | [获取未过期的订阅](/graph/api/subscription-list)。 | 
| **更新订阅** | 通过提供订阅 ID 来[更新订阅](/graph/api/subscription-update)。 例如，若要扩展订阅，可以更新订阅的 `expirationDateTime` 属性。 | 
| **删除订阅** | 通过提供订阅 ID 来[删除订阅](/graph/api/subscription-delete)。 | 
||| 

### <a name="manage-threat-intelligence-indicators"></a>管理威胁情报指示器

若要筛选、排序或获取最新结果，只需提供 [Microsoft Graph支持的 ODATA 查询参数](/graph/query-parameters)。 请勿指定完整的基 URL 或 HTTP 操作，例如 `https://graph.microsoft.com/beta/security/tiIndicators` 或 `GET`/`PATCH` 操作。 下面的具体示例展示了在要获取具有 `DDoS` 威胁类型的列表时“获取 tiIndicator”操作的参数：

`Filter threat intelligence indicator value as threatType eq 'DDoS'`

若要详细了解可以与此连接器一起使用的查询，请参阅 [Microsoft Graph 安全性威胁情报指示器参考文档中的“可选查询参数”](/graph/api/tiindicators-list)。 若要使用此连接器增强体验，请详细了解此连接器支持的[架构属性威胁情报指示器](/graph/api/resources/tiindicator)。

| 操作 | 描述 |
|--------|-------------|
| 获取威胁情报指示器 | 获取按一个或多个 [tiIndicator 属性](/graph/api/resources/tiindicator)筛选出的 tiIndicator，例如 `threatType eq 'MaliciousUrl' or 'DDoS'` |
| 按 ID 获取威胁情报指示器 | 按 tiIndicator ID 获取特定 tiIndicator。 | 
| 创建威胁情报指示器 | 通过发布到 tiIndicator 集合来创建新的 tiIndicator。 若要确保在请求中传递必需的属性，请参阅[创建 tiIndicator 所需的属性](/graph/api/tiindicators-post)。 |
| 提交多个威胁情报指示器 | 通过发布 tiIndicator 集合来创建多个新的 tiIndicator。 若要确保在请求中传递必需的属性，请参阅[提交多个 tiIndicator 所需的属性](/graph/api/tiindicator-submittiindicators)。 |
| 更新威胁情报指示器 | 按 tiIndicator ID 更新特定 tiIndicator。 若要确保在请求中传递必需的可编辑属性，请参阅 [tiIndicator 的可编辑属性](/graph/api/tiindicator-update)。 例如，要更新在指标与 targetProduct 安全工具内匹配时要应用的操作，可以更新 tiIndicator 的“action”属性。 |
| 更新多个威胁情报指示器 | 更新多个 tiIndicator。 若要确保在请求中传递必需的属性，请参阅[更新多个 tiIndicator 所需的属性](/graph/api/tiindicator-updatetiindicators)。 |
| 按 ID 删除威胁情报指示器 | 按 tiIndicator ID 删除特定 tiIndicator。 |
| 按 ID 删除多个威胁情报指示器 | 按 ID 删除多个 tiIndicator。 若要确保在请求中传递必需的属性，请参阅[按 ID 删除多个 tiIndicator 所需的属性](/graph/api/tiindicator-deletetiindicators)。 |
| 按外部 ID 删除多个威胁情报指示器 | 按外部 ID 删除多个 tiIndicator。 若要确保在请求中传递必需的属性，请参阅[按外部 ID 删除多个 tiIndicator 所需的属性](/graph/api/tiindicator-deletetiindicatorsbyexternalid)。 |
|||

## <a name="connector-reference"></a>连接器参考

有关触发器、操作和限制（请参阅连接器的 OpenAPI（以前称为 Swagger）说明）的技术详细信息，请查看连接器的[参考页](/connectors/microsoftgraphsecurity/)。

## <a name="next-steps"></a>后续步骤

了解其他[逻辑应用连接器](../connectors/apis-list.md)
