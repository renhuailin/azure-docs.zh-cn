---
title: 使用 Azure AD 中的连续访问评估监视登录并排查登录问题
description: 使用 Azure AD 中的连续访问评估来排查用户状态更改问题并更快地做出响应
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 09/22/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: jlu, shreyamalik
ms.collection: M365-identity-device-management
ms.openlocfilehash: 93d44065255dcb9f4977e93dd596eb3b80c1faa2
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129533161"
---
# <a name="monitor-and-troubleshoot-continuous-access-evaluation"></a>监视连续访问评估并排查其问题

管理员可通过多种方式监视应用了[连续访问评估 (CAE)](concept-continuous-access-evaluation.md) 的登录事件并排查其问题。

## <a name="continuous-access-evaluation-sign-in-reporting"></a>连续访问评估登录报告

管理员可以监视应用了 CAE 的用户登录。 可通过以下说明找到此窗格：

1.  以条件访问管理员、安全管理员或全局管理员的身份登录到 Azure 门户。
1.  浏览到“Azure Active Directory” > “登录”。 
1.  应用“是 CAE 令牌”筛选器。 

[ ![向 Sitn-ins 日志添加筛选器，以确定是否正在应用 CAE](./media/howto-continuous-access-evaluation-troubleshoot/azure-ad-sign-ins-log-apply-filter.png) ](./media/howto-continuous-access-evaluation-troubleshoot/azure-ad-sign-ins-log-apply-filter.png#lightbox)

在此处，管理员将看到有关其用户登录事件的信息。 选择任一登录以查看有关会话的详细信息，例如应用了哪些条件访问策略，以及是否启用了 CAE。 

给定的登录尝试可能会显示在交互式或非交互式选项卡上。管理员在跟踪其用户的登录时可能需要检查这两个选项卡。

### <a name="searching-for-specific-sign-in-attempts"></a>搜索特定的登录尝试

使用筛选器缩小搜索范围。 例如，如果用户已登录到 Teams，请使用“应用程序”筛选器并将其设置为 Teams。 管理员可能需要从交互式和非交互式选项卡中检查登录，以找到特定的登录。 若要进一步缩小搜索范围，管理员可以应用多个筛选器。

## <a name="continuous-access-evaluation-workbooks"></a>连续访问评估工作簿

管理员可以使用连续访问评估见解工作簿来查看和监视其租户的 CAE 使用情况见解。 表显示 IP 不匹配的身份验证尝试。 可以在“条件访问”类别下找到作为模板显示的此工作簿。 

### <a name="accessing-the-cae-workbook-template"></a>访问 CAE 工作簿模板

必须先完成 Log Analytics 集成，然后才会显示工作簿。 若要详细了解如何将 Azure AD 登录日志流式传输到 Log Analytics 工作区，请参阅[将 Azure AD 日志与 Azure Monitor 日志集成](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)一文。
 
1.  以条件访问管理员、安全管理员或全局管理员的身份登录到 Azure 门户。 
1.  浏览到“Azure Active Directory” > “工作簿” 。
1.  在“公共模板”下，搜索“连续访问评估见解” 。

[ ![在库中查找 CAE 见解工作簿以继续监视](./media/howto-continuous-access-evaluation-troubleshoot/azure-ad-workbooks-continuous-access-evaluation.png) ](./media/howto-continuous-access-evaluation-troubleshoot/azure-ad-workbooks-continuous-access-evaluation.png#lightbox)

“连续访问评估见解”工作簿包含下表：

### <a name="potential-ip-address-mismatch-between-azure-ad-and-resource-provider"></a>Azure AD 和资源提供程序之间的潜在 IP 地址不匹配情况  

![显示潜在 IP 地址不匹配情况的工作簿表 1](./media/howto-continuous-access-evaluation-troubleshoot/continuous-access-evaluation-insights-workbook-table-1.png)

管理员可以使用“Azure AD 和资源提供程序之间的潜在 IP 地址不匹配情况”表来调查会话，在这些会话中，Azure AD 检测到的 IP 地址与资源提供程序检测到的 IP 地址不匹配。 

此工作簿表通过显示相应的 IP 地址以及是否在会话期间是否颁发了 CAE 令牌，来阐明这些情况。 

#### <a name="ip-address-configuration"></a>IP 地址配置

标识提供者和资源提供程序可能会看到不同的 IP 地址。 发生这种不匹配情况的可能原因如以下示例所述：

- 网络实施了拆分隧道。
- 资源提供程序使用的是 IPv6 地址，而 Azure AD 使用的是 IPv4 地址。
- 由于网络配置的原因，Azure AD 看到的是来自客户端的一个 IP 地址，而资源提供程序看到的是来自客户端的另一个 IP 地址。

如果你的环境中存在这种情况，为了避免无限循环，Azure AD 会颁发有效期为一小时的 CAE 令牌，并且在该一小时期限内，它不会强制更改客户端位置。 即使在这种情况下，与传统的一小时令牌相比，安全性也会得到提高，因为除了客户端位置更改事件之外，我们还评估其他事件。

管理员可以查看按时间范围和应用程序筛选的记录。 管理员可以将检测到的不匹配 IP 数量与指定时段的登录总数进行比较。 

若要取消阻止用户，管理员可以将特定 IP 地址添加到受信任的命名位置。

1.  以条件访问管理员、安全管理员或全局管理员的身份登录到 Azure 门户。 
1.  浏览到“Azure Active Directory” > “安全性” > “条件访问” > “命名位置”。 可在此处创建或更新受信任的 IP 位置。

> [!NOTE]
> 在将某个 IP 地址添加为受信任的命名位置之前，请确认该 IP 地址确实属于所需的组织。

有关命名位置的详细信息，请参阅[使用位置条件](location-condition.md#named-locations)一文
 
## <a name="next-steps"></a>后续步骤

- [将 Azure AD 日志与 Azure Monitor 日志集成](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)
- [使用位置条件](location-condition.md#named-locations)
- [连续访问评估](concept-continuous-access-evaluation.md)
