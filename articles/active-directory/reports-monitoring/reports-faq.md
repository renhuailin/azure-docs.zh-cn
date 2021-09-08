---
title: Azure Active Directory 报告常见问题解答 | Microsoft Docs
description: 有关 Azure Active Directory 报告的常见问题解答。
services: active-directory
documentationcenter: ''
author: MarkusVi
ms.assetid: 534da0b1-7858-4167-9986-7a62fbd10439
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: report-monitor
ms.date: 08/26/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0aa1f44d8cc6184932d2aaf1b69fb31e303d605f
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2021
ms.locfileid: "122969122"
---
# <a name="frequently-asked-questions-around-azure-active-directory-reports"></a>有关 Azure Active Directory 报告的常见问题解答

本文包括了对 Azure Active Directory (Azure AD) 报告常见问题的解答。 有关详细信息，请参阅 [Azure Active Directory 报告](overview-reports.md)。 

## <a name="getting-started"></a>入门 

**问：报告的许可是如何工作的？**

**答：** 所有 Azure AD 许可证都允许在 Azure 门户中查看活动日志。 

如果你的租户：

- 拥有 Azure AD 免费许可证，则可在门户中查看最多 7 天的活动日志数据。 
- 拥有 Azure AD Premium 许可证，则可在 Azure 门户中查看最多 30 天的数据。 

你还可将这些日志数据导出到 Azure Monitor、Azure 事件中心和 Azure 存储，或通过 Microsoft Graph API 查询活动数据。 请参阅 [Azure Active Directory Premium 入门](../fundamentals/active-directory-get-started-premium.md)来升级 Azure Active Directory 版本。 如果在升级之前没有数据活动，则在升级到高级许可证后，数据需要经过几天才会显示在日志中。


问：我现在使用 `https://graph.windows.net/<tenant-name>/reports/` 终结点 API 以编程方式将 Azure AD 审核和集成的应用程序使用情况报告拉取到报告系统中。我应该切换到什么？

**答：** 请查看 [API 参考](https://developer.microsoft.com/graph/)，了解如何 [使用 API 访问活动报告](concept-reporting-api.md)。 此终结点有两个报告（“审核”和“登录”），它们提供了你在旧的 API 终结点中获取的所有数据。  此新的终结点还有一个登录报告，其中包含可用来获取应用使用情况、设备使用情况和用户登录信息的 Azure AD Premium 许可证。

---

问：我现在使用 `https://graph.windows.net/<tenant-name>/reports/` 终结点 API 以编程方式将 Azure AD 安全报告（特定类型的检测，例如泄漏的凭据或来自匿名 IP 地址的登录）拉取到报告系统中。我应该切换到什么？

答：可以使用[标识保护风险检测 API](../identity-protection/howto-identity-protection-graph-api.md) 通过 Microsoft Graph 访问安全检测。 此新格式在数据查询方式上提供了更大的灵活性，具有高级筛选、字段选择等功能，并将风险检测标准化为一种类型，以便轻松集成到 SIEM 和其他数据收集工具中。 因为数据采用的格式不同，所以无法用新查询替代旧查询。 但是，[新 API 使用 Microsoft Graph](/graph/api/resources/identityriskevent?view=graph-rest-beta&preserve-view=true)，它是 Microsoft 365 或 Azure AD 等 API 的 Microsoft 标准。 因此，需要做的工作可以扩展当前 Microsoft Graph 投资或者帮助你开始向这个新标准平台过渡。

---

**问：如何获得高级许可证？**

**答：** 请参阅 [Azure Active Directory Premium 入门](../fundamentals/active-directory-get-started-premium.md)来升级 Azure Active Directory 版本。

---

**问：获得高级许可证后多久能看见活动数据？**

**答：** 如果获得免费许可证时已有活动数据，则可以立即看到这些数据。 如果没有任何数据，则最多需要 3 天，数据就会显示在报告中。

---

**问：获得 Azure AD Premium 许可证后是否能查看上个月的数据？**

答：如果最近刚切换到高级版本（包括试用版），则最初最多能看到 7 天的数据。 随着数据累积，可以看到过去 30 天的数据。

---

**问：若要查看到 Azure 门户的活动登录或通过 API 获取数据，是否需要是全局管理员？**

**答：** 否。如果你是租户的 **安全读取者** 或 **安全管理员**，也可以通过门户或 API 访问报告数据。 当然，**全局管理员** 也有权访问这些数据。

---


## <a name="activity-logs"></a>活动日志


**问：Azure 门户中活动日志（审核和登录）的数据保留是什么？** 

答：有关详细信息，请参阅 [Azure AD 报告的数据保留策略](reference-reports-data-retention.md)。

---

**问：完成任务后多久才能看到“活动”数据？**

**答：** 审核日志的延迟为 15 分钟到 1 小时。 对于某些记录，登录活动日志可能需要花费 15 分钟到 2 小时。

---

问：是否可以通过 Azure 门户获取 Microsoft 365 活动日志信息？

答：虽然 Microsoft 365 活动和 Azure AD 活动日志共享大量的目录资源，但如果需要全面查看 Microsoft 365 活动日志，则应转到 [Microsoft 365 管理中心](https://admin.microsoft.com)，以获取 Microsoft 365 活动日志信息。

---

问：使用哪些 API 获取有关 Microsoft 365 活动日志的信息？

答：请使用 [Microsoft 365 管理 API](/office/office-365-management-api/office-365-management-apis-overview)，以通过 API 访问 Microsoft 365 活动日志。

---

**问：可从 Azure 门户下载多少条记录？**

**答：** 最多可从 Azure 门户下载 5000 条记录。 记录按最近时间进行排序，默认情况下获取的是最近 5000 条记录。

---

## <a name="sign-in-logs"></a>登录日志

问：从 Azure 门户中的“Azure AD 登录日志”边栏选项卡下载的 CSV 文件中包含哪些数据？
答：该 CSV 文件包含用户和服务主体的登录日志 。 但是，下载的 CSV 文件不包含 MS Graph API 中以嵌套数组形式表示的登录日志数据。 例如，下载的 CSV 文件不包含 CA 策略和仅限报告的信息。 如果需要导出登录日志中包含的所有信息，可以使用“Azure AD 登录日志”边栏选项卡中的“导出数据设置”按钮导出所有数据。

---
 
问：当来宾登录到我的租户时，客户端应用中为何不填充信息？
答：当某个来宾用户登录到你的租户时，为了保护客户隐私，该用户的客户端应用信息不会显示在你的租户的登录日志中 。 用户的客户端应用不会向这些用户尝试访问的其他租户显示。 

---

问：当来宾登录到我的租户时，为何不会填充设备 ID？
答：当用户使用已注册到另一个租户的设备登录到你的租户时，为了保护客户隐私，该设备的设备 ID 不会显示在你的租户的登录日志中 。 设备 ID 不会向用户尝试访问的其他租户显示。 

---

问：在某些已中断的登录中，为何我看到的是对象 ID 而不是用户的 UPN？ 
答：如果我们的服务由于登录中断或失败而无法解析用户的 UPN，它可能会改而显示对象 ID 。 

---

问：为何即使属性 isInteractive 为 False，用户登录也仍显示为交互式登录？
答：此属性即将弃用 。 它不能可靠地指示哪些登录事件是交互式的，哪些是非交互式的。 

在 Azure 门户的“Azure AD 登录日志”边栏选项卡中，可以在“用户登录(交互式)”选项卡中找到交互式登录，在“用户登录(非交互式)”选项卡中找到非交互式登录。在 MS Graph API 中，应该依赖于使用 signInEventTypes 属性来确定哪些登录是交互式登录。 例如： 

`"signInEventTypes":["interactiveUser"],`

在从 MS Graph API 请求登录日志时，还可以使用 $filter 参数进行筛选。 

## <a name="risky-sign-ins"></a>有风险的登录

问：标识保护中存在风险检测，但登录报告中未显示相应的登录。这是正常的吗？

**答：** 是的，标识保护会评估所有身份验证流的风险，无论其为交互式还是非交互式。 但是，所有登录报告仅显示交互式登录。

---

问：如何了解 Azure 门户中被标记为存在风险的用户或登录的原因？

答：如果使用 Azure AD Premium 订阅，可通过选择“已标记为存在风险的用户”中的用户或通过选择“有风险的登录”报告中的记录来详细了解潜在的风险检测   。 如果使用 Free 或 Basic 订阅，可查看有风险的用户和有风险的登录报告，但看不到潜在风险检测的信息 。

---

**问：在登录和有风险的登录报告中，IP 地址是如何计算的？**

答：IP 地址的发布方式是，在 IP 地址和使用该地址的计算机所在的物理位置之间没有确定的连接。 有多种因素会导致映射 IP 地址进一步变得复杂，例如，从中心池发布 IP 地址的移动运营商和 VPN 通常与实际使用客户端设备的位置距离很远。 目前，在 Azure AD 报告中，最好是基于跟踪、注册表数据、反向查看和其他信息将 IP 地址转换为物理位置。 

---

问：风险检测“检测到具有额外风险的登录”指示什么？

**答：** 为了让你深入了解环境中所有具有风险的登录，对于为了执行“Azure AD 标识保护”订阅方专用的检测而进行的登录，“登录时检测到其他风险”将充当占位符。

---

## <a name="conditional-access"></a>条件性访问

**问：此功能有什么新内容？**

答：客户现在可以通过所有登录报告对条件访问策略进行故障排除。 客户可以查看条件访问状态，并深入了解应用于登录的策略的详细信息以及每个策略的结果。

**问：如何开始使用？**

**答：** 若要开始使用：

* 导航到 [Azure门户](https://portal.azure.com)中的登录报告。
* 单击要进行故障排除的登录。
* 导航到“条件访问”选项卡。在这里，可以查看影响了登录的所有策略以及每个策略的结果。 
    
问：条件访问状态都有哪些可能的值？

答：条件访问状态可以有以下值：

* 未应用：表示在范围内没有针对用户和应用的条件访问策略。 
* 成功：表示在范围内存在针对用户和应用的条件访问策略，并且已成功满足条件访问策略。 
* **失败**：登录满足了至少一个条件性访问策略的用户和应用程序条件，授权控件要么未满足，要么设置为阻止访问。
    
问：条件访问策略结果都有哪些可能的值？

答：条件访问策略可以有以下结果：

* **成功**：成功满足策略。
* **失败**：不满足策略。
* **未应用**：这可能是因为不符合策略条件。
* **未启用**：这是由于策略处于禁用状态。 
    
问：所有登录报告中的策略名称与 CA 中的策略名称不匹配，为什么？

答：所有登录报告中的策略名称均基于登录时的条件访问策略名称。 如果你后来（即登录后）更新了策略名称，则这可能与 CA 中的策略名称不一致。

问：我的登录因条件访问策略而被阻止，但登录活动报告显示已登录成功，为什么？

答：在应用了条件访问时，登录报告目前可能无法显示 Exchange ActiveSync 方案的准确结果。 在有些情况下，报告中的登录结果显示已成功登录，但由于条件访问策略，登录实际上失败了。
