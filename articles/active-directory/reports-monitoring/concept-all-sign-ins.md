---
title: Azure Active Directory 中的登录日志 - 预览版 | Microsoft Docs
description: Azure Active Directory 中的登录日志概述，包括处于预览状态的新功能。
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
ms.date: 06/23/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9fbd65204534e978446109c99ca7286c0af00d68
ms.sourcegitcommit: 54d8b979b7de84aa979327bdf251daf9a3b72964
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/24/2021
ms.locfileid: "112580379"
---
# <a name="sign-in-logs-in-azure-active-directory---preview"></a>Azure Active Directory 中的登录日志 - 预览版

作为 IT 管理员，你想知道你的 IT 环境的运行状况。 你可通过有关系统运行状况的信息来评估是否需要响应潜在问题以及响应方式。 

为了帮助你实现此目标，Azure Active Directory 门户为你提供了访问三个活动日志的权限：

- **[登录](concept-sign-ins.md)** - 有关登录以及用户如何使用资源的信息。
- **[审核](concept-audit-logs.md)** - 有关应用于租户的更改（如用户和组管理）或应用于租户资源的更新的信息。
- **[预配](concept-provisioning-logs.md)** - 由预配服务执行的活动（例如在 ServiceNow 中创建组，或从 Workday 导入用户）。


Azure Active Directory 中的经典登录日志概述了交互式用户登录的情况。此外，你现在可以访问另外三个目前处于预览状态的登录日志：

- 非交互式用户登录

- 服务主体登录

- Azure 资源托管标识登录

本文概述了带有非交互式登录、应用程序登录和 Azure 资源托管标识登录的预览功能的登录活动报告。若要了解不带预览功能的登录报告，请参阅 [Azure Active Directory 中的登录日志](concept-sign-ins.md)。



## <a name="what-can-you-do-with-it"></a>日志有哪些作用？

登录日志提供了以下问题的答案：

- 用户、应用程序或服务的登录模式是什么？

- 一周内有多少用户、应用或服务已登录？

- 这些登录的状态怎样？


## <a name="who-can-access-the-data"></a>谁可以访问该数据？

- 具有“安全管理员”、“安全读取者”和“报告读取者”角色的用户

- 全局管理员

- 任何用户（非管理员）都可以访问自己的登录活动 

## <a name="what-azure-ad-license-do-you-need"></a>需要什么 Azure AD 许可证？

租户必须有与之关联的 Azure AD Premium 许可证才能查看登录活动。 请参阅 [Azure Active Directory Premium 入门](../fundamentals/active-directory-get-started-premium.md)来升级 Azure Active Directory 版本。 如果在升级之前没有数据活动，则在升级到高级许可证后，数据需要经过几天才会显示在日志中。




## <a name="where-can-you-find-it-in-the-azure-portal"></a>在 Azure 门户中的何处可以找到日志？

Azure 门户提供了几种用于访问日志的选项。 例如，在 Azure Active Directory 菜单上，可以在“监视”部分打开日志。  

![打开登录日志](./media/concept-sign-ins/sign-ins-logs-menu.png)

此外，可以使用以下链接直接访问登录日志：[https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns)

在登录页上，可以在以下选项之间切换：

- **交互式用户登录** - 一种登录方式，用户可通过这种方式提供身份验证因素，例如密码、通过 MFA 应用提供的响应、生物识别因素或 QR 码。

- **非交互式用户登录** - 由客户端代表用户进行的登录。 此类登录不需要用户进行任何交互，也不需要用户提供身份验证因素。 例如，使用不要求用户输入凭据的刷新令牌和访问令牌进行身份验证和授权。

- **服务主体登录** - 通过应用和服务主体进行的登录，不涉及任何用户。 在此类登录中，应用或服务代表自己提供对资源进行身份验证或访问所需的凭据。

- **Azure 资源托管标识登录** - 其机密由 Azure 管理的 Azure 资源的登录。 有关详细信息，请参阅[什么是 Azure 资源的托管标识？](../managed-identities-azure-resources/overview.md) 


![登录日志类型](./media/concept-all-sign-ins/sign-ins-report-types.png)



登录页面的每个选项卡都显示下面的默认列。 某些选项卡有其他列：

- 登录日期

- 请求 ID

- 用户名或用户 ID

- 应用程序名称或应用程序 ID

- 登录的状态

- 用于登录的设备的 IP 地址



### <a name="interactive-user-sign-ins&quot;></a>交互式用户登录


交互式用户登录是指这样一种登录：用户向 Azure AD 提供身份验证因素，或者直接与 Azure AD 或帮助程序应用（如 Microsoft Authenticator 应用）交互。 用户提供的因素包括：密码、对 MFA 质询的响应、生物因素，或者用户提供给 Azure AD 或帮助程序应用的 QR 码。

> [!NOTE]
> 此日志还包括与 Azure AD 联合的标识提供者提供的联合登录。  



> [!NOTE] 
> 交互式用户登录日志过去常常包含来自 Microsoft Exchange 客户端的一些非交互式登录。 尽管这些登录是非交互式登录，但为了增加可见性，它们包含在交互式用户登录日志中。 非交互式用户登录日志于 2020 年 11 月进入公共预览版后，这些非交互式登录日志已移至非交互式用户登录日志以提高准确性。 


**报告大小：** 小 <br> 
**示例：**

- 用户在 Azure AD 登录屏幕中提供用户名和密码。

- 用户通过 SMS MFA 质询。

- 用户提供生物识别手势以通过 Windows Hello 企业版来解锁其 Windows 电脑。

- 用户使用 AD FS SAML 断言与 Azure AD 联合。


除了默认字段，交互式登录日志还显示： 

- 登录位置

- 是否已应用条件访问



单击工具栏中的“列”即可自定义列表视图。 

![交互式用户登录列](./media/concept-all-sign-ins/columns-interactive.png &quot;交互式用户登录列")





可以通过对视图进行自定义来显示更多字段或删除已显示的字段。

![所有交互式列](./media/concept-all-sign-ins/all-interactive-columns.png)


选择列表视图中的某个项可以更详细地了解相关的登录。

![登录活动](./media/concept-all-sign-ins/interactive-user-sign-in-details.png "交互式用户登录")



### <a name="non-interactive-user-sign-ins"></a>非交互式用户登录

非交互式用户登录是由客户端应用或 OS 组件代表用户执行的登录。 与交互式用户登录类似，这些登录是代表用户执行的。 与交互式用户登录不同的是，这些登录不要求用户提供身份验证因素， 而是由设备或客户端应用使用令牌或代码代表用户对资源进行身份验证或访问。 通常情况下，用户会发现这些登录在用户活动的后台发生。


**报告大小：** 大型 <br>
**示例：** 

- 客户端应用使用 OAuth 2.0 刷新令牌来获取访问令牌。

- 客户端使用 OAuth 2.0 授权代码来获取访问令牌和刷新令牌。

- 用户在已加入 Azure AD 的电脑上执行 Web 或 Windows 应用的单一登录 (SSO)。

- 用户使用 FOCI（客户端 ID 系列）在移动设备上建立会话后，可以登录到另一个 Microsoft Office 应用。




除了默认字段，非交互式登录日志还显示： 

- 资源 ID

- 分组登录数




不能自定义此报告中显示的字段。


![禁用的列](./media/concept-all-sign-ins/disabled-columns.png "禁用的列")

为了便于理解数据，非交互式登录事件进行了分组。 客户端通常会在短时间内代表同一个用户创建许多非交互式登录，这些登录共享所有相同的特征，但尝试登录的时间除外。 例如，客户端可以代表用户每小时获取一次访问令牌。 如果用户或客户端未更改状态，则每个访问令牌请求的 IP 地址、资源和所有其他信息都是相同的。 如果除时间和日期外，Azure AD 记录的多个登录的信息完全相同，那么这些登录来自同一实体，并且会聚合成单个行。 如果某个行有多个相同的登录（发出的日期和时间除外），则“登录数”列中的值将大于 1。 可以展开该行来查看所有不同的登录及其不同的时间戳。 当以下数据匹配时，会在非交互式用户中对登录进行聚合：


- 应用程序

- 用户

- IP 地址

- 状态

- 资源 ID


方法：

- 展开节点可查看组的各个项。  

- 单击单个项可查看所有详细信息 


![非交互式用户登录详细信息](./media/concept-all-sign-ins/non-interactive-sign-ins-details.png)




## <a name="service-principal-sign-ins&quot;></a>服务主体登录

与交互式和非交互式用户登录不同，服务主体登录不是涉及用户的登录， 而是由任何非用户帐户（例如应用或服务主体）执行的登录（托管标识登录除外，此类登录仅包含在托管标识登录日志中）。 在此类登录中，应用或服务提供自己的凭据（例如证书或应用机密）对资源进行身份验证或访问。


**报告大小：** 大型 <br>
**示例：**

- 服务主体使用证书对 Microsoft Graph 进行身份验证和访问。 

- 应用程序使用客户端机密在 OAuth 客户端凭据流中进行身份验证。 


此报告有一个默认列表视图，用于显示：

- 登录日期

- 请求 ID

- 服务主体名称或 ID

- 状态

- IP 地址

- 资源名称

- 资源 ID

- 登录次数

不能自定义此报告中显示的字段。

![禁用的列](./media/concept-all-sign-ins/disabled-columns.png &quot;禁用的列")

为了便于理解服务主体登录日志中的数据，服务主体登录事件进行了分组。 相同条件下同一实体的登录将聚合到单个行中。 可以展开该行来查看所有不同的登录及其不同的时间戳。 当以下数据匹配时，会在服务主体报告中对登录进行聚合：

- 服务主体名称或 ID

- 状态

- IP 地址

- 资源名称或 ID

方法：

- 展开节点可查看组的各个项。  

- 单击单个项可查看所有详细信息 


![列详细信息](./media/concept-all-sign-ins/service-principals-sign-ins-view.png "列详细信息")




## <a name="managed-identity-for-azure-resources-sign-ins"></a>Azure 资源托管标识登录 

Azure 资源托管标识登录是资源执行的登录，这些资源的机密由 Azure 管理，以便简化凭据管理。

**报告大小：** 小型 <br> 
**示例：**

具有托管凭据的 VM 使用 Azure AD 获取访问令牌。   


此报告有一个默认列表视图，用于显示：


- 托管标识 ID

- 托管标识名称

- 资源

- 资源 ID

- 分组登录数

不能自定义此报告中显示的字段。

为了便于理解数据（Azure 资源托管标识登录日志），非交互式登录事件进行了分组。 同一实体的登录将聚合到单个行中。 可以展开该行来查看所有不同的登录及其不同的时间戳。 当所有以下数据匹配时，会在托管标识报告中对登录进行聚合：

- 托管标识名称或 ID

- 状态

- 资源名称或 ID

在列表视图中选择一个项可显示已分组到某个节点下的所有登录。

选择某个分组项可查看此登录的所有详细信息。 


## <a name="sign-in-error-code"></a>登录错误代码

如果登录失败，你可以在相关日志项的“基本信息”部分中获得有关原因的详细信息。 

![屏幕截图显示了详细的信息视图。](./media/concept-all-sign-ins/error-code.png)
 
虽然日志项提供了失败原因，但在某些情况下，你可以通过使用[登录错误查找工具](https://login.microsoftonline.com/error)来获取详细信息。 例如，此工具将为你提供修正步骤（如果可用）。  

![错误代码查找工具](./media/concept-all-sign-ins/error-code-lookup-tool.png)



## <a name="filter-sign-in-activities"></a>筛选登录活动

通过设置筛选器，可以缩小返回的登录数据的范围。 Azure AD 提供了一系列可以设置的其他筛选器。 设置筛选器时，应始终特别注意已配置的日期范围筛选器。 正确的日期范围筛选器可确保 Azure AD 仅返回你真正关心的数据。     

**日期** 范围筛选器用于为返回的数据定义时间范围。
可能的值包括：

- 一个月

- 七天

- 二十四小时

- 自定义

![日期范围筛选器](./media/concept-all-sign-ins/date-range-filter.png)





### <a name="filter-user-sign-ins"></a>筛选用户登录

交互式登录和非交互式登录的筛选器是相同的。 因此，为交互式登录配置的筛选器会为非交互式登录持久保存，反之亦然。 






## <a name="access-the-new-sign-in-activity-logs"></a>访问新的登录活动日志 

Azure 门户中的登录活动报告提供了用于轻松打开和关闭“预览报告”的方法。 如果启用了预览日志，则会显示一个新菜单，用于访问所有登录活动报告类型。     


若要访问新的登录日志（其中包含非交互式登录和应用程序登录），请执行以下操作： 

1. 在“Azure 门户”中，选择“Azure Active Directory”。

    ![选择“Azure AD”](./media/concept-all-sign-ins/azure-services.png)

2. 在“监视”部分，单击“登录” 。

    ![选择登录](./media/concept-all-sign-ins/sign-ins.png)

3. 单击“预览”栏。

    ![启用新视图](./media/concept-all-sign-ins/enable-new-preview.png)

4. 若要切换回默认视图，请再次单击“预览”栏。 

    ![还原经典视图](./media/concept-all-sign-ins/switch-back.png)







## <a name="download-sign-in-activity-logs"></a>下载登录活动日志

下载登录活动报告时，以下陈述成立：

- 可以将登录报告下载为 CSV 或 JSON 文件。

- 最多可下载 10 万条记录。 如果要下载更多数据，请使用报告 API。

- 你的下载基于你所做的筛选器选择。

- 可以下载的记录数受 [Azure Active Directory 报告保留策略](reference-reports-data-retention.md)的限制。 


![下载日志](./media/concept-all-sign-ins/download-reports.png "下载日志")


每个 CSV 下载包含六个不同的文件：

- 交互式登录

- 交互式登录的身份验证详细信息

- 非交互式登录

- 非交互式登录的身份验证详细信息

- 服务主体登录

- Azure 资源托管标识登录

每个 JSON 下载包含四个不同的文件：

- 交互式登录（包括身份验证详细信息）

- 非交互式登录（包括身份验证详细信息）

- 服务主体登录

- Azure 资源托管标识登录

![下载文件](./media/concept-all-sign-ins/download-files.png "下载文件")


## <a name="return-log-data-with-microsoft-graph"></a>使用 Microsoft Graph 返回日志数据

除了使用 Azure 门户，还可以使用 Microsoft Graph API 查询登录日志，以返回不同类型的登录信息。 为避免潜在的性能问题，请将查询范围限定为你关注的数据。 

以下示例按记录数、特定时间段和登录事件类型来限定查询范围：

```msgraph-interactive
GET https://graph.microsoft.com/beta/auditLogs/signIns?$top=100&$filter=createdDateTime ge 2020-09-10T06:00:00Z and createdDateTime le 2020-09-17T06:00:00Z and signInEventTypes/any(t: t eq 'nonInteractiveUser')
```

示例中的查询参数提供以下结果：

- 参数 [$top](/graph/query-parameters#top-parameter) 返回前 100 项结果。
- 参数 [$filter](/graph/query-parameters#filter-parameter) 限制返回结果的时间范围，并使用 signInEventTypes 属性仅返回非交互式用户登录。

以下值可用于按不同的登录类型进行筛选： 

- interactiveUser
- nonInteractiveUser
- servicePrincipal 
- managedIdentity

## <a name="next-steps"></a>后续步骤

* [登录活动报告错误代码](./concept-sign-ins.md)
* [Azure AD 数据保留策略](reference-reports-data-retention.md)
* [Azure AD 报告延迟](reference-reports-latencies.md)
