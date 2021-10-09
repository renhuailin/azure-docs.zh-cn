---
title: Azure Active Directory 中的登录日志 | Microsoft Docs
description: Azure Active Directory 中的登录日志概述。
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
ms.date: 07/16/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ce1aecb88f595b7edba2e1f89d82c06e1fbbc69
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128622277"
---
# <a name="sign-in-logs-in-azure-active-directory"></a>Azure Active Directory 中的登录日志

作为 IT 管理员，你想知道你的 IT 环境的运行状况。 你可通过有关系统运行状况的信息来评估是否需要响应潜在问题以及响应方式。 

为了帮助你实现此目标，Azure Active Directory 门户为你提供了访问三个活动日志的权限：

- **[登录](concept-sign-ins.md)** - 有关登录以及用户如何使用资源的信息。
- **[审核](concept-audit-logs.md)** - 有关应用于租户的更改（如用户和组管理）或应用于租户资源的更新的信息。
- **[预配](concept-provisioning-logs.md)** - 由预配服务执行的活动（例如在 ServiceNow 中创建组，或从 Workday 导入用户）。

本文概述了登录报告。


## <a name="what-can-you-do-with-it"></a>日志有哪些作用？

你可以使用登录日志来查找以下问题的答案：

- 什么是用户的登录模式？

- 多少用户超过一周都有登录行为？

- 这些登录的状态怎样？


## <a name="who-can-access-it"></a>谁可以访问它们？

始终可以使用以下链接访问你自己的登录历史记录：[https://mysignins.microsoft.com](https://mysignins.microsoft.com)

若要访问登录日志，你需要是：

- 全局管理员

- 以下某个角色的用户：
    - 安全管理员

    - 安全读取者

    - 全局读取者

    - 报告读取者



## <a name="what-azure-ad-license-do-you-need"></a>需要什么 Azure AD 许可证？

[所有版本的 Azure AD](reference-reports-data-retention.md#how-long-does-azure-ad-store-the-data) 中都提供了登录活动报告。 如果有 Azure Active Directory P1 或 P2 许可证，还可以通过 Microsoft Graph API 访问登录活动报告。


## <a name="where-can-you-find-it-in-the-azure-portal"></a>在 Azure 门户中的何处可以找到日志？

Azure 门户提供了几种用于访问日志的选项。 例如，在 Azure Active Directory 菜单上，可以在“监视”部分打开日志。  

![打开登录日志](./media/concept-sign-ins/sign-ins-logs-menu.png)

此外，可以使用以下链接直接访问登录日志：[https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns)


## <a name="what-is-the-default-view"></a>什么是默认视图？

登录日志有一个默认列表视图，用于显示：

- 登录日期
- 相关的用户
- 用户登录到的应用程序
- 登录状态
- 风险检测的状态
- 多重身份验证 (MFA) 要求的状态

![屏幕截图显示了 Office 365 SharePoint Online 登录。](./media/concept-sign-ins/sign-in-activity.png "登录活动")

单击工具栏中的“列”即可自定义列表视图。 

![屏幕截图显示了“登录”页中的“列”选项。](./media/concept-sign-ins/19.png "登录活动")

通过“列”  对话框，可以访问可选属性。 在登录报告中，对于给定的登录请求，不能将具有多个值的字段作为列。 例如，“身份验证详细信息”、“条件访问数据”和“网络位置”就是这样的。   

![屏幕截图显示了“列”对话框，你可以在其中选择属性。](./media/concept-sign-ins/columns.png "登录活动")

选择列表视图中的某个项可获得更详细的信息。

![屏幕截图显示了详细的信息视图。](./media/concept-sign-ins/basic-sign-in.png "登录活动")



## <a name="sign-in-error-code"></a>登录错误代码

如果登录失败，你可以在相关日志项的“基本信息”部分中获得有关原因的详细信息。 

![登录错误代码](./media/concept-all-sign-ins/error-code.png)
 
虽然日志项提供了失败原因，但在某些情况下，你可以通过使用[登录错误查找工具](https://login.microsoftonline.com/error)来获取详细信息。 例如，此工具将为你提供修正步骤（如果可用）。  

![错误代码查找工具](./media/concept-all-sign-ins/error-code-lookup-tool.png)



## <a name="filter-sign-in-activities&quot;></a>筛选登录活动


可以筛选日志中的数据，使其缩小到适合你的级别：

![屏幕截图显示了“添加筛选器”选项。](./media/concept-sign-ins/04.png &quot;登录活动")

**请求 ID** - 所关注请求的 ID。

**用户** - 所关注用户的用户名或用户主体名称 (UPN)。

**应用程序** - 目标应用程序的名称。
 
**状态** - 所关注的登录状态：

- Success

- 失败

- 已中断


**IP 地址** - 用于连接到租户的设备的 IP 地址。

**位置** - 从其发起连接的位置：

- 城市

- 省/自治区/直辖市

- 国家/地区


**资源** - 用于登录的服务的名称。


**资源 ID** - 用于登录的服务的 ID。


**客户端应用** - 用于连接到租户的客户端应用的类型：

![客户端应用筛选器](./media/concept-sign-ins/client-app-filter.png)


> [!NOTE]
> 由于隐私承诺，在跨租户方案中，Azure AD 不会将此字段填充到主租户。


|名称|新式身份验证|说明|
|---|:-:|---|
|经验证的 SMTP| |由 POP 和 IMAP 客户端用来发送电子邮件。|
|自动发现| |由 Outlook 和 EAS 客户端用来查找和连接 Exchange Online 中的邮箱。|
|Exchange ActiveSync| |此筛选器显示尝试 EAS 协议的所有登录尝试。|
|浏览者|![蓝色复选标记。](./media/concept-sign-ins/check.png)|显示用户使用 Web 浏览器进行的所有登录尝试|
|Exchange ActiveSync| | 显示使用客户端应用通过 Exchange ActiveSync 连接到 Exchange Online 的用户进行的所有登录尝试|
|Exchange Online PowerShell| |用于通过远程 PowerShell 连接到 Exchange Online。 如果阻止 Exchange Online PowerShell 的基本身份验证，则需使用 Exchange Online PowerShell 模块进行连接。 有关说明，请参阅[使用多重身份验证连接到 Exchange Online PowerShell](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell)。|
|Exchange Web 服务| |Outlook、Outlook for Mac 和第三方应用使用的编程接口。|
|IMAP4| |使用 IMAP 检索电子邮件的旧版邮件客户端。|
|基于 HTTP 的 MAPI| |由 Outlook 2010 及更高版本使用。|
|移动应用和桌面客户端|![蓝色复选标记。](./media/concept-sign-ins/check.png)|显示用户使用移动应用和桌面客户端进行的所有登录尝试。|
|脱机通讯簿| |通过 Outlook 下载并使用的地址列表集合的副本。|
|Outlook Anywhere（基于 HTTP 的 RPC）| |由 Outlook 2016 及更低版本使用。|
|Outlook 服务| |由 Windows 10 的邮件和日历应用使用。|
|POP3| |使用 POP3 检索电子邮件的旧版邮件客户端。|
|Reporting Web Services| |用于在 Exchange Online 中检索报表数据。|
|其他客户端| |显示用户的所有登录尝试，客户端应用不包括在其中或未知。|







**操作系统** - 在设备上运行的用于登录到租户的操作系统。 


**设备浏览器** - 如果连接是从浏览器发起的，则可使用此字段按浏览器名称进行筛选。


**相关性 ID** - 活动的相关性 ID。




**条件访问** - 已应用的条件访问规则的状态

- **未应用**：在登录过程中未对用户和应用程序应用任何策略。

- **成功**：在登录过程中对用户和应用程序应用了一个或多个条件访问策略（但不一定是其他条件）。 

- **失败**：登录满足了至少一个条件性访问策略的用户和应用程序条件，授权控件要么未满足，要么设置为阻止访问。









## <a name="download-sign-in-activities"></a>下载登录活动

单击“下载”  选项即可创建包含最近 250,000 条记录的 CSV 或 JSON 文件。 如果想要在 Azure 门户外部使用登录活动数据，请先[下载登录数据](./howto-download-logs.md)。  

![下载](./media/concept-sign-ins/71.png "下载")

> [!IMPORTANT]
> 可以下载的记录数受 [Azure Active Directory 报告保留策略](reference-reports-data-retention.md)的限制。  


## <a name="sign-ins-data-shortcuts"></a>登录数据快捷方式

Azure AD 和 Azure 门户都提供登录数据的其他入口点：

- 标识安全保护概述
- 用户
- 组
- 企业应用程序

### <a name="users-sign-ins-data-in-identity-security-protection"></a>标识安全保护中的用户登录数据

“标识安全保护”  概述页上的用户登录图显示了按周汇总的登录信息。默认时间为 30 天。

![屏幕截图显示了一个月内的登录图。](./media/concept-sign-ins/06.png "登录活动")

单击登录图中的某一天时，可以获得该天的登录活动的概览。

登录活动列表中的每一行显示以下内容：

* 登录者是谁？
* 登录的目标应用程序是哪个？
* 登录的状态是什么？
* 登录的 MFA 状态是什么？

单击某个项即可获得有关登录操作的更多详情：

- 用户 ID
- User
- 用户名
- 应用程序 ID
- 应用程序
- 客户端
- 位置
- IP 地址
- Date
- 需要 MFA
- 登录状态

> [!NOTE]
> IP 地址的发布方式是，在 IP 地址和使用该地址的计算机所在的物理位置之间没有确定的连接。 从中心池发布 IP 地址的移动运营商和 VPN 通常与实际使用客户端设备的位置距离很远，这会导致 IP 地址映射变得复杂。 目前，最好是基于跟踪、注册表数据、反向查看和其他信息将 IP 地址转换为物理位置。

在“用户”页中单击“活动”部分的“登录”即可完全了解所有用户登录活动。   

![屏幕截图显示了“活动”部分，可在其中选择“登录”。](./media/concept-sign-ins/08.png "登录活动")

## <a name="authentication-details"></a>身份验证详细信息

针对每次身份验证尝试，登录报表中的“身份验证详细信息”选项卡提供以下信息：

- 应用的身份验证策略的列表（如条件访问、按用户 MFA、安全默认值）
- 用于登录的身份验证方法序列
- 身份验证尝试是否成功
- 有关身份验证尝试成功或失败的原因的详细信息

此信息允许管理员排查用户登录中每个步骤的问题，并进行跟踪：

- 受多重身份验证保护的登录数量 
- 每个身份验证方法的使用情况和成功率 
- 使用无密码身份验证方法（如无密码的手机登录、FIDO2 和 Windows Hello 企业版） 
- 令牌声明满足身份验证要求的频率（其中不会以交互方式提示用户输入密码、输入短信验证码等等）

查看登录报告时，选择“身份验证详细信息”应用程序： 

![“身份验证详细信息”选项卡的屏幕截图](media/concept-sign-ins/auth-details-tab.png)

>[!NOTE]
>OATH 验证码作为 OATH 硬件和软件令牌（Microsoft Authenticator 应用）的身份验证方法被记录。

>[!IMPORTANT]
>“身份验证详细信息”选项卡最初显示的数据可能不完整或不准确，直到日志信息完全聚合后才会完整且准确。 已知示例包括： 
>- 最初记录登录事件时，会错误地显示以下消息：“满足令牌中的声明”。 
>- 最初不会记录“主要身份验证”行。 


## <a name="usage-of-managed-applications&quot;></a>托管应用程序的使用情况

通过登录数据的以应用程序为中心的视图，可以回答如下问题：

* 谁正在使用我的应用程序？
* 组织中最常用的三个应用程序是哪些？
* 我的最新应用程序的情况如何？

此数据的入口点是组织中最常用的三个应用程序。 数据包含在“企业应用程序”下“概览”部分过去 30 天的报告中   。

![屏幕截图显示了你可以选择“概览”的位置。](./media/concept-sign-ins/10.png &quot;登录活动")

应用使用情况图显示指定时间内最常用的三个应用程序的按周汇总的登录信息。 默认时间为 30 天。

![屏幕截图显示了一个月内的应用使用情况。](./media/concept-sign-ins/graph-chart.png "登录活动")

如果需要，可以将焦点设置在特定应用程序上。

![报告](./media/concept-sign-ins/single-app-usage-graph.png "报告")

单击应用程序使用情况图中的某一天时，可以获取登录活动的详细列表。

**登录** 选项可提供应用程序的所有登录事件的完整概览。

## <a name="microsoft-365-activity-logs"></a>Microsoft 365 活动日志

可以从 [Microsoft 365 管理中心](/office365/admin/admin-overview/about-the-admin-center)查看 Microsoft 365 活动日志。 要考虑到 Microsoft 365 活动日志和 Azure AD 活动日志共享大量的目录资源。 只有 Microsoft 365 管理中心提供 Microsoft 365 活动日志的完整视图。 

还可以使用 [Office 365 管理 API](/office/office-365-management-api/office-365-management-apis-overview) 以编程方式访问 Microsoft 365 活动日志。

## <a name="next-steps"></a>后续步骤

* [Azure AD 数据保留策略](reference-reports-data-retention.md)
* [Azure AD 报告延迟](reference-reports-latencies.md)
* [登录报表中的第一方 Microsoft 应用程序](/troubleshoot/azure/active-directory/verify-first-party-apps-sign-in#application-ids-for-commonly-used-microsoft-applications)
