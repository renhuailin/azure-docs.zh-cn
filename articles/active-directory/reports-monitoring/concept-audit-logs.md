---
title: Azure Active Directory 中的审核日志 | Microsoft Docs
description: Azure Active Directory 中的审核日志概述。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/30/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73ccb2ddb9e229faea69ae3f67ed8285b0256808
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128581259"
---
# <a name="audit-logs-in-azure-active-directory"></a>Azure Active Directory 中的审核日志 

作为 IT 管理员，你想知道你的 IT 环境的运行状况。 你可通过有关系统运行状况的信息来评估是否需要响应潜在问题以及响应方式。 

为了帮助你实现此目标，Azure Active Directory 门户为你提供了访问三个活动日志的权限：

- **[登录](concept-sign-ins.md)** - 有关登录以及用户如何使用资源的信息。
- **[审核](concept-audit-logs.md)** - 有关应用于租户的更改（如用户和组管理）或应用于租户资源的更新的信息。
- **[预配](concept-provisioning-logs.md)** - 由预配服务执行的活动（例如在 ServiceNow 中创建组，或从 Workday 导入用户）。

本文概述了审核日志。


## <a name="what-is-it"></a>它是什么？

通过 Azure AD 中的审核日志，可以访问系统活动的记录以实现符合性。
此日志最常见的视图基于以下类别：

- 用户管理

- 组管理
 
- 应用程序管理  


使用以用户为中心的视图，可以获得如下问题的答案：

- 已对用户应用了哪些类型的更新？

- 更改了多少用户？

- 更改了多少密码？

- 管理员在目录中做了什么？


使用以组为中心的视图，可以获得如下问题的答案：

- 添加了哪些组？

- 是否存在成员身份已更改的组？

- 是否已更改组的所有者？

- 向组或用户分配了哪些许可证？

使用以应用程序为中心的视图，可以获得如下问题的答案：

- 添加或更新了哪些应用程序？

- 删除了哪些应用程序？

- 应用程序的服务主体是否有变化？

- 应用程序的名称是否已更改？

- 哪些用户同意使用应用程序？

 
## <a name="what-license-do-i-need"></a>需要哪个许可证？

所有版本的 Azure AD 中都提供了审核活动报告。

## <a name="who-can-access-it"></a>谁可以访问它们？

若要访问审核日志，需要具有以下角色之一： 

- 安全管理员
- 安全读取者
- 报表读取者
- 全局读取者
- 全局管理员

## <a name="where-can-i-find-it"></a>在哪里可以找到这样的脚本？

Azure 门户提供了几种用于访问日志的选项。 例如，在 Azure Active Directory 菜单上，可以在“监视”部分打开日志。  

![打开审核日志](./media/concept-audit-logs/audit-logs-menu.png)

此外，可使用[此链接](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/ProvisioningEvents)直接访问审核日志。


还可以通过 Microsoft Graph API 访问审核日志。


## <a name="what-is-the-default-view"></a>什么是默认视图？

审核日志有一个默认列表视图，用于显示：

- 匹配项的日期和时间
- 记录了匹配项的服务
- 活动的类别和名称（内容） 
- 活动的状态（成功或失败）
- 目标
- 活动的发起者/参与者（人员）

![审核日志](./media/concept-audit-logs/listview.png "审核日志")

单击工具栏中的“列”即可自定义列表视图。 

![审核列](./media/concept-audit-logs/columns.png "审核列")

用于显示其他字段，或者删除已显示的字段。

![删除字段](./media/concept-audit-logs/columnselect.png "删除字段")

选择列表视图中的某个项可获得更详细的信息。

![选择项](./media/concept-audit-logs/details.png "选择项")

## <a name="filtering-audit-logs"></a>筛选审核日志

可以根据以下字段筛选审核数据：

- 服务
- 类别
- 活动
- 状态
- 目标
- 发起者（参与者/执行组件）
- 日期范围

![筛选器对象](./media/concept-audit-logs/filter.png "Filter 对象")

使用“服务”筛选器可以从以下服务的下拉列表中进行选择：

- 全部
- AAD 管理 UX
- 访问评审
- 帐户预配
- 应用程序代理
- 身份验证方法
- B2C
- 条件性访问
- 核心目录
- 权利管理
- 混合身份验证
- 标识保护
- 受邀用户
- MIM 服务
- MyApps
- PIM
- 自助服务组管理
- 自助服务密码管理
- 使用条款

“类别”筛选器用于选择下述筛选器之一：

- 全部
- AdministrativeUnit
- ApplicationManagement
- 身份验证
- 授权
- 联系人
- 设备
- DeviceConfiguration
- DirectoryManagement
- EntitlementManagement
- GroupManagement
- KerberosDomain
- KeyManagement
- 标签
- 其他
- PermissionGrantPolicy
- 策略
- ResourceManagement
- RoleManagement
- UserManagement

“活动”筛选器基于类别以及所做的活动资源类型选择。 可以选择要查看的特定活动，也可以全选。 

可以使用图形 API 获取所有审核活动的列表：`https://graph.windows.net/<tenantdomain>/activities/auditActivityTypesV2?api-version=beta`

可以使用“状态”筛选器根据审核操作的状态进行筛选。 状态可以是下列其中一项：

- 全部
- Success
- 失败

“目标”筛选器允许你按名称或用户主体名称 (UPN) 的开头来搜索特定目标。 目标名称和 UPN 区分大小写。 

“发起者”筛选器允许你定义参与者名称或通用主体名称 (UPN) 的开头。 名称和 UPN 区分大小写。

“日期范围”筛选器用于定义已返回数据的时间范围。  
可能的值包括：

- 7 天
- 24 小时
- 自定义

选择自定义时间范围时，可以配置开始时间和结束时间。

也可选择下载筛选的数据（多达 250,000 条记录），只需选择“下载”按钮即可。 可以下载 CSV 或 JSON 格式的日志。 可以下载的记录数受 [Azure Active Directory 报告保留策略](reference-reports-data-retention.md)的限制。

![下载数据](./media/concept-audit-logs/download.png "下载数据")



## <a name="microsoft-365-activity-logs"></a>Microsoft 365 活动日志

可以从 [Microsoft 365 管理中心](/office365/admin/admin-overview/about-the-admin-center)查看 Microsoft 365 活动日志。 尽管 Microsoft 365 活动日志和 Azure AD 活动日志共享大量的目录资源，但只有 Microsoft 365 管理中心提供 Microsoft 365 活动日志的完整视图。 

还可以使用 [Office 365 管理 API](/office/office-365-management-api/office-365-management-apis-overview) 以编程方式访问 Microsoft 365 活动日志。

> [!NOTE]
> 大多数独立或捆绑 Microsoft 365 订阅都对 Microsoft 365 数据中心边界内的某些子系统具有后端依赖关系。 这些依赖关系需要一些信息写回以使目录保持同步，实质上有助于在 Exchange Online 的订阅选择加入中实现轻而易举的加入。 对于这些写回，审核日志条目显示由 Microsoft Substrate Management 采取的操作。 这些审核日志条目指由 Exchange Online 对 Azure AD 执行的创建/更新/删除操作。 这些条目是信息性条目，不需要任何操作。

## <a name="next-steps"></a>后续步骤

- [Azure AD 审核活动参考](reference-audit-activities.md)
- [Azure AD 日志保留参考](reference-reports-data-retention.md)
- [Azure AD 日志延迟参考](reference-reports-latencies.md)
- [审核报告中的未知参与者](/troubleshoot/azure/active-directory/unknown-actors-in-audit-reports)
