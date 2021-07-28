---
title: Azure Active Directory 应用程序预配中的隔离状态
description: 配置了应用程序以进行自动用户预配后，了解隔离状态的预配的含义以及如何清除它。
services: active-directory
author: kenwith
manager: mtillman
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 05/11/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: c3cdf8ab99506afd644d59fa8e74ed59fa1af853
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2021
ms.locfileid: "109783164"
---
# <a name="application-provisioning-in-quarantine-status"></a>隔离状态中的应用程序预配

Azure AD 预配服务会监视配置的运行状况。 它还会将运行不正常的应用置于“隔离”状态。 如果对目标系统进行的大多数（或所有）调用一直失败，则预配作业会被标记为处于隔离状态。 失败的一个示例是由于管理员凭据无效而收到的错误。

处于隔离状态时：
- 增量周期的频率会逐渐减少为每天一次。
- 解决所有错误并开始下一个同步周期后，预配作业会退出隔离状态。 
- 如果预配作业保持隔离状态四周以上，则会禁用（停止运行）预配作业。

## <a name="how-do-i-know-if-my-application-is-in-quarantine"></a>如何知道我的应用程序是否处于隔离状态？

有三种方法可以检查应用程序是否处于隔离状态：
  
- 在 Azure 门户中，导航到“Azure Active Directory” > “企业应用程序” > &lt;应用程序名称&gt; > “预配”，并查看隔离消息的进度栏。   

  ![显示隔离状态的预配状态栏](./media/application-provisioning-quarantine-status/progress-bar-quarantined.png)

- 在 Azure 门户中，导航到“Azure Active Directory” > “审核日志”>“活动: 隔离”上的筛选器，并查看隔离历史记录。 上面所述的进度栏中的视图显示预配当前是否处于隔离状态。 审核日志显示应用程序的隔离历史记录。 

- 使用 Microsoft Graph 请求 [Get synchronizationJob](/graph/api/synchronization-synchronizationjob-get?tabs=http&view=graph-rest-beta&preserve-view=true) 以编程方式获取预配作业的状态：

```microsoft-graph
        GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/
```

- 查看电子邮件。 当应用程序被置于隔离状态时，系统会发送一次性通知电子邮件。 如果隔离原因发生更改，则会发送更新的电子邮件，显示新的隔离原因。 如果看不到电子邮件：

  - 确保在应用程序的设置配置中指定了有效的“通知电子邮件”。
  - 确保在通知电子邮件收件箱中没有垃圾邮件筛选。
  - 确保没有取消订阅电子邮件。
  - 检查是否有来自 `azure-noreply@microsoft.com` 的电子邮件

## <a name="why-is-my-application-in-quarantine"></a>为什么应用程序处于隔离状态？

|说明|建议的操作|
|---|---|
|SCIM 合规性问题：返回了“HTTP/404 找不到”响应，而不是预期的“HTTP/200 正常”响应。 在这种情况下，Azure AD 预配服务向目标应用程序发出了请求，并收到意外响应。|检查管理员凭据部分。 查看应用程序是否需要指定租户 URL，以及该 URL 是否正确。 如果看不到问题，请与应用程序开发人员联系，以确保其服务符合 SCIM。 https://tools.ietf.org/html/rfc7644#section-3.4.2 |
|凭据无效：尝试授权访问目标应用程序时，从目标应用程序收到响应，指出提供的凭据无效。|导航到预配配置 UI 的管理员凭据部分，使用有效凭据再次授权访问。 如果应用程序处于库中，请查看应用程序配置教程，以了解不再需要的步骤。|
|角色重复：从某些应用程序（如 Salesforce 和 Zendesk）导入的角色必须唯一。 |导航到 Azure 门户中的应用程序[清单](../develop/reference-app-manifest.md)，删除重复的角色。|

 获取预配作业状态的 Microsoft Graph 请求显示以下隔离原因：
- `EncounteredQuarantineException` 指示提供的凭据无效。 预配服务无法在源系统与目标系统之间建立连接。
- `EncounteredEscrowProportionThreshold` 指示预配超过托管阈值。 如果超过 40% 的预配事件失败，则会出现此情况。 有关详细信息，请参阅下面的托管阈值详细信息。
- `QuarantineOnDemand` 表示我们检测到应用程序存在问题，已手动将它设置为隔离状态。

**托管阈值**

如果满足成比例的托管阈值，则预配作业会进入隔离状态。 此逻辑可能会发生更改，但大致按如下所述进行工作： 

无论管理员凭据或 SCIM 合规性等问题的失败计数如何，作业都可以会进入隔离状态。 但一般而言，5,000 次失败是由于失败太多而开始评估是否进入隔离状态的最小值。 例如，失败 4,000 次的作业不会进入隔离状态。 但是，失败 5,000 次的作业会触发评估。 评估使用以下条件：  
- 如果超过 40% 的预配事件失败或是失败超过 40,000 池，则预配作业会进入隔离状态。 引用失败不会计为 40% 阈值或 40,000 阈值的一部分。 例如，未能更新管理器或组成员是引用失败。
- 未成功预配 45,000 个用户的作业会导致隔离，因为它超过了 40,000 阈值。
- 30,000 个用户预配失败且 5,000 个成功的作业会导致隔离，因为它超过了 40% 阈值和 5,000 最小值。
- 失败 20,000 次且成功 100,000 次的作业不会进入隔离状态，因为它未超过 40% 失败阈值或 40,000 失败最大值。  
- 绝对阈值为 60,000 次失败，它会同时考虑引用和非引用失败。 例如，40,000 个用户未能预配，且 21,000 次管理器更新失败。 总数为 61,000 次失败，超过了 60,000 限制。


## <a name="how-do-i-get-my-application-out-of-quarantine"></a>如何使应用程序退出隔离状态？

首先，请解决导致将应用程序置于隔离状态的问题。

- 检查应用程序的预配设置，以确保[输入了有效的管理员凭据](../app-provisioning/configure-automatic-user-provisioning-portal.md#configuring-automatic-user-account-provisioning)。 Azure AD 必须与目标应用程序建立信任关系。 确保输入了有效凭据，并且帐户具有所需权限。

- 查看[预配日志](../reports-monitoring/concept-provisioning-logs.md)，进一步调查导致隔离的操作并解决错误。 转到“Azure Active Directory”&gt;“企业应用”&gt;“活动”部分中的“预配日志(预览版)” 。

解决问题后，重启预配作业。 对应用程序预配设置（如属性映射或范围筛选器）进行的特定更改会自动重启预配。 应用程序“预配”页上的进度栏会指示预配上次启动的时间。 如果需要手动重启预配作业，请使用以下方法之一：  

- 使用 Azure 门户重新启动预配作业。 在应用程序“预配”页上，选择“重启预配” 。 此操作会完全重启预配服务，这可能需要一些时间。 会再次运行完整的初始周期，这会清除托管、使应用退出隔离状态并清除所有水印。 服务随后会再次评估源系统中的所有用户，并确定它们是否处于预配的范围中。 当应用程序当前处于隔离状态（如本文所述）或者需要更改属性映射时，这可能会很有用。 请注意，由于需要评估的对象数，初始周期的完成时间比典型增量周期更长。 可在[此处](application-provisioning-when-will-provisioning-finish-specific-user.md)详细了解初始和增量周期的性能。

- 使用 Microsoft Graph [重新启动预配作业](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta&preserve-view=true)。 你可以完全控制需要重新启动的内容。 可以选择清除托管（以重启向隔离状态累积的托管计数器）、清除隔离（以使应用程序退出隔离状态）或清除水印。 使用以下请求：
 
```microsoft-graph
        POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart
```

将“{ID}”替换为应用程序 ID 的值，并将“{jobId}”替换为[同步作业的 ID](/graph/api/resources/synchronization-configure-with-directory-extension-attributes?tabs=http&view=graph-rest-beta&preserve-view=true#list-synchronization-jobs-in-the-context-of-the-service-principal)。
