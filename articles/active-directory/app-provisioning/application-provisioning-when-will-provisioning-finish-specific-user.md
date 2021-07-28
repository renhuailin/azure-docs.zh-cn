---
title: 查明特定用户何时能够访问 Azure Active Directory 应用程序预配中的应用
description: 如何找出非常重要的用户何时可以访问已使用 Azure AD 配置用户预配的应用程序
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
ms.openlocfilehash: f972028b407ca5704fed5000142072231d65410b
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2021
ms.locfileid: "109782894"
---
# <a name="check-the-status-of-user-provisioning"></a>检查用户预配状态

Azure AD 预配服务针对源系统和目标系统运行初始预配周期，然后运行定期的增量周期。 配置应用的预配时，可以检查预配服务的当前状态，并查看用户何时可以访问应用。

## <a name="view-the-provisioning-progress-bar"></a>查看预配进度条

 在应用的“预配”页上，你可以查看 Azure AD 预配服务的状态。 页面底部的“当前状态”部分显示预配周期是否已开始预配用户帐户。 可以查看周期的进度，查看已预配的用户和组的数量，并查看已创建的角色数。

首次配置自动预配时，页面底部的“当前状态”部分会显示初始预配周期的状态。 每次运行增量循环后，这一部分都会更新。 显示以下详细信息：
- 当前正在运行或最后完成的预配周期的类型（初始或增量）。
- 显示已完成预配周期百分比的进度条。 百分比反映预配页的计数。 请注意，每个页面可能包含多个用户或组，因此该百分比并不与预配的用户、组或角色的数目直接相关。
- 可以使用“刷新”按钮来更新视图。
- 连接器数据存储中的用户和组的数目。  只要向预配作用域中添加了某个对象，计数将立即增加。 如果用户被软删除或硬删除，则计数不会关闭，因为这样不会从连接器数据存储中删除对象。 在 CDS [重置](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta&preserve-view=true)后的第一次同步时，将重新计算计数 
- “查看审核日志”链接将打开 Azure AD 设置日志，提供有关用户预配服务运行的所有操作的详细信息，包括括单个用户的预配状态（请参阅下面的[使用预配日志](#use-provisioning-logs-to-check-a-users-provisioning-status)部分）。

预配周期完成后，“到目前为止的统计信息”部分显示到目前为止已经预配的用户和组的累计数量，以及最后一个周期的完成日期和持续时间。 活动 ID 可唯一标识最新的预配周期。 作业 ID 是预配作业的唯一标识符，特定于租户中的应用。

可在 Azure 门户中访问预配进度，具体位置在“Azure Active Directory”&gt;“企业应用”&gt;“应用程序名称”&gt;“预配”选项卡。 **\[\]**

![预配页进度条](./media/application-provisioning-when-will-provisioning-finish-specific-user/provisioning-progress-bar-section.png)

## <a name="use-provisioning-logs-to-check-a-users-provisioning-status"></a>使用预配日志检查用户的预配状态

要查看选定用户的预配状态，请查阅 Azure AD 中的[预配日志（预览版）](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)。 Azure AD 预配日志（预览版）中记录了用户预配服务运行的所有操作。 该日志包括对源系统和目标系统执行的所有读写操作，以及在每次操作期间读取或写入的用户数据。

可以在 Azure 门户中访问预配日志，具体方法是在“活动”部分中选择“Azure Active Directory”&gt;“企业应用”&gt;“预配日志（预览版）”    你可以根据用户的名称或者根据源系统或目标系统中的标识符来搜索预配数据。 有关详细信息，请参阅 [预配日志（预览版）](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)。 

预配审核日志记录了预配服务执行的全部操作，包括：

* 查询 Azure AD 以找到预配作用域中分配的用户
* 查询目标应用以验证是否存在这些用户
* 比较系统之间的用户对象
* 根据比较结果在目标系统中添加、更新或禁用用户帐户

要详细了解如何在 Azure 门户中读取预配日志，请参阅[预配报告指南](check-status-user-account-provisioning.md)。

## <a name="how-long-will-it-take-to-provision-users"></a>预配用户需要多长时间？
当会自动化用户预配用于应用程序时，Azure AD 会根据类似[用户和组分配](../manage-apps/assign-user-or-group-access-portal.md)，定期（通常每 40 分钟）自动预配并更新应用中的用户帐户。

预配给定用户所用的时间主要取决于你的预配作业是正在运行初始周期还是增量周期。

- 对于 **初始周期**，作业时间取决于各种因素，包括预配范围中用户和组的数目，以及源系统中用户和组的总数。 Azure AD 和应用之间的首次同步可能会持续 20 分钟至几小时，具体取决于 Azure AD 目录的大小与作用域中的预配用户数量。 本部分稍后将概述影响初始周期性能的因素的综合列表。

- 对于初始周期之后的 **增量周期**，作业速度趋向于变快（例如在 10 分钟内完成），因为在初始周期后，预配服务已存储代表两个系统状态的水印，这提高了后续同步的性能。 作业时间取决于在该预配周期中检测到的更改数量。 如果有少于 5000 个用户或组成员身份更改，则作业可以在单个增量预配周期内完成。 

下表总结了常见的预配方案的同步时间。 在这些方案中，源系统是 Azure AD，目标系统是 SaaS 应用程序。 同步时间是通过对 SaaS 应用程序 ServiceNow、Workplace、Salesforce 和 G Suite 的同步作业进行统计分析得出的。


| 范围配置 | 作用域中的用户、组和成员 | 初始周期时间 | 增量周期时间 |
| -------- | -------- | -------- | -------- |
| 仅同步已分配的用户和组 |  < 1,000 |  < 30 分钟 | < 30 分钟 |
| 仅同步已分配的用户和组 |  1,000 - 10,000 | 142 - 708 分钟 | < 30 分钟 |
| 仅同步已分配的用户和组 |   10,000 - 100,000 | 1,170 - 2,340 分钟 | < 30 分钟 |
| 同步 Azure AD 中的所有用户和组 |  < 1,000 | < 30 分钟  | < 30 分钟 |
| 同步 Azure AD 中的所有用户和组 |  1,000 - 10,000 | < 30 - 120 分钟 | < 30 分钟 |
| 同步 Azure AD 中的所有用户和组 |  10,000 - 100,000  | 713 - 1,425 分钟 | < 30 分钟 |
| 同步 Azure AD 中的所有用户|  < 1,000  | < 30 分钟 | < 30 分钟 |
| 同步 Azure AD 中的所有用户 | 1,000 - 10,000  | 43 - 86 分钟 | < 30 分钟 |

对于“仅同步已分配的用户和组”配置，可以使用以下公式来确定大概的最小和最大预计初始周期时间：

- 最小分钟数 = 0.01 x [分配的用户、组和组成员的数目]
- 最大分钟数 = 0.08 x [分配的用户、组和组成员的数目]

影响初始周期的完成时间的因素汇总：

- 预配范围内用户和组的总数。

- 源系统 (Azure AD) 中存在的用户、组和组成员的总数。

- 预配范围中的用户与目标应用程序中的现有用户匹配，还是需要首次创建。 要首次为其创建所有用户的同步作业花费的时间大约是所有用户都与现有用户匹配的同步作业花费的时间的“两倍”。

- [预配日志](check-status-user-account-provisioning.md)中的错误数。 如果有许多错误，并且预配服务已进入“隔离”状态，则性能较低。 

- 目标系统实现的请求速率限制。 某些目标系统实施了请求速率限制和调节，这一特性在大型同步操作期间可能会影响性能。 在这些情况下，太快地接收太多请求的应用可能会拖慢其响应速率或关闭连接。 为提高性能，连接器需要进行调整，以不高于应用可以应对的处理速率的速率来向应用发送请求。 Microsoft 构建的预配连接器进行此调整。 

- 已分配的组的数量和大小。 同步已分配的组比同步用户花费的时间要长。 已分配的组的数量和大小都会影响性能。 如果应用程序[为组对象同步启用了映射](customize-application-attributes.md#editing-group-attribute-mappings)，则除了用户之外，还会同步组名称和成员身份等组属性。 这些额外的同步比仅同步用户对象需要花费更长的时间。

- 如果性能成为一个严重问题，并且你正在尝试预配租户中的大部分用户和组，请使用范围筛选器。 使用范围筛选器，可优化预配服务通过筛选用户（基于特定属性值）从 Azure AD 提取的数据。 有关作用域筛选器的详细信息，请参阅[通过作用域筛选器基于属性预配应用程序](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)。

## <a name="next-steps"></a>后续步骤
[使用 Azure Active Directory 自动化 SaaS 应用程序用户预配和取消预配](user-provisioning.md)