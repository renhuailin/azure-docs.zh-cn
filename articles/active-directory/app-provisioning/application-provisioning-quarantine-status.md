---
title: 隔离的应用程序预配状态 |Microsoft Docs
description: 为应用程序配置了自动用户预配后，请了解 "隔离" 的预配状态以及如何清除它。
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/24/2020
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: d997c85f96fa9f87ca6d017cb555b3732007e21c
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2021
ms.locfileid: "99256299"
---
# <a name="application-provisioning-in-quarantine-status"></a>隔离状态的应用程序设置

Azure AD 预配服务监视配置的运行状况。 它还会将不正常的应用置于 "隔离" 状态。 如果对目标系统进行的大部分（或全部）调用一直失败，则设置作业会被标记为隔离。 故障的一个示例是由于无效的管理员凭据而收到的错误。

在隔离中：
- 增量循环的频率会逐渐减少到每天一次。
- 修复所有错误并启动下一个同步周期后，将从隔离区中删除该设置作业。 
- 如果预配作业处于隔离状态的时间超过四周，则会禁用设置作业 (停止运行) 。

## <a name="how-do-i-know-if-my-application-is-in-quarantine"></a>如何实现知道我的应用程序是否处于隔离区？

有三种方法可以检查应用程序是否处于隔离区：
  
- 在 Azure 门户中，导航到 **Azure Active Directory**  >  **企业应用**  >  &lt; *程序应用程序名称* &gt;  >  **预配**，并查看隔离消息的进度栏。   

  ![显示隔离状态的设置状态栏](./media/application-provisioning-quarantine-status/progress-bar-quarantined.png)

- 在 Azure 门户中，导航到 " **Azure Active Directory**  >  **审核日志**" > "对活动进行筛选" **： "隔离**" 并查看隔离历史记录。 如上所述，进度栏中的视图显示预配是否当前为隔离。 审核日志显示应用程序的隔离历史记录。 

- 使用 Microsoft Graph 请求 [Get synchronizationJob](/graph/api/synchronization-synchronizationjob-get?tabs=http&view=graph-rest-beta&preserve-view=true) 以编程方式获取设置作业的状态：

```microsoft-graph
        GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/
```

- 查看电子邮件。 当应用程序位于隔离区时，将发送一次性通知电子邮件。 如果隔离原因发生更改，则会发送更新的电子邮件，显示隔离的新原因。 如果看不到电子邮件：

  - 请确保已在应用程序的设置配置中指定了有效的 **通知电子邮件** 。
  - 请确保在通知电子邮件收件箱中没有垃圾邮件筛选。
  - 请确保你没有取消订阅电子邮件。
  - 检查电子邮件来自 `azure-noreply@microsoft.com`

## <a name="why-is-my-application-in-quarantine"></a>为什么应用程序在隔离中？

|描述|建议的操作|
|---|---|
|**SCIM 合规性问题：** 返回了 HTTP/404 找不到的响应，而不是预期的 HTTP/200 OK 响应。 在这种情况下，Azure AD 预配服务已向目标应用程序发出请求，并收到意外响应。|检查 "管理员凭据" 部分。 查看应用程序是否需要指定租户 URL，以及该 URL 是否正确。 如果看不到问题，请与应用程序开发人员联系，以确保其服务符合 SCIM。 https://tools.ietf.org/html/rfc7644#section-3.4.2 |
|**无效的凭据：** 尝试授权时，访问目标应用程序时，会收到来自目标应用程序的响应，指出提供的凭据无效。|导航到预配配置 UI 的 "管理员凭据" 部分，并使用有效凭据再次授权访问权限。 如果应用程序在库中，请查看应用程序配置教程，了解不再需要的步骤。|
|**重复角色：** 从某些应用程序（如 Salesforce 和 Zendesk）导入的角色必须是唯一的。 |导航到 Azure 门户中的应用程序 [清单](../develop/reference-app-manifest.md) ，并删除重复的角色。|

 获取设置作业状态的 Microsoft Graph 请求将显示以下隔离原因：
- `EncounteredQuarantineException` 指示提供的凭据无效。 预配服务无法在源系统和目标系统之间建立连接。
- `EncounteredEscrowProportionThreshold` 指示预配超出了保管阈值。 如果预配事件超过40%，则会出现此情况。 有关详细信息，请参阅下面的 "托管阈值详细信息"。
- `QuarantineOnDemand` 表示我们检测到应用程序存在问题，并已将其手动设置为隔离。

**托管阈值**

如果满足按比例的委托阈值，则设置作业将进入隔离区。 此逻辑可能会发生更改，但大致按如下所述进行操作： 

无论管理凭据或 SCIM 符合性的故障计数如何，作业都可以进入隔离区。 但一般情况下，如果故障太多，将开始评估是否隔离会导致5000故障。 例如，具有4000故障的作业不会进入隔离区。 但是，具有5000故障的作业会触发评估。 评估使用以下条件：  
- 如果超过40% 的预配事件失败或超过40000个失败，则设置作业将进入隔离区。 引用失败不会计为40% 阈值或40000阈值的一部分。 例如，如果无法更新管理器或组成员，则引用失败。
- 未成功设置45000用户的作业将导致隔离，因为它超过40000阈值。
- 30000用户预配失败和5000成功的作业会导致隔离，因为它超过40% 阈值和最小5000。
- 20000失败和100000成功的作业不会进入隔离区，因为它不超过40% 失败阈值或40000故障最大值。  
- 对于引用和非引用失败，绝对阈值为60000失败。 例如，40000用户未能预配，21000 manager 更新失败。 总数为61000，超过60000限制。


## <a name="how-do-i-get-my-application-out-of-quarantine"></a>如何实现将应用程序隔离到隔离区？

首先，请解决导致应用程序处于隔离中的问题。

- 请检查应用程序的设置设置，以确保 [输入了有效的管理凭据](../app-provisioning/configure-automatic-user-provisioning-portal.md#configuring-automatic-user-account-provisioning)。 Azure AD 必须建立与目标应用程序的信任关系。 请确保输入的凭据有效，并且帐户具有所需的权限。

- 查看 [预配日志](../reports-monitoring/concept-provisioning-logs.md) ，进一步调查导致隔离和解决错误的错误。 请在活动部分中转到 **Azure Active Directory** &gt; **Enterprise Apps** &gt; **预配日志 (预览版)** "。 

解决问题后，请重新启动设置作业。 对应用程序的预配设置的某些更改（如属性映射或范围筛选器）将自动重新启动设置。 应用程序的 " **设置** " 页上的进度条指示预配上次启动的时间。 如果需要手动重新启动设置作业，请使用以下方法之一：  

- 使用 Azure 门户重启预配作业。 在应用程序的 "设置 **" 页的** " **设置**" 下，选择 " **清除状态并重新启动同步** "，并将设置 **状态** 设置为 **On**。 此操作完全重启预配服务，这可能需要一些时间。 将再次运行完整的初始周期，这会清除 escrows，从隔离区中删除应用程序，并清除所有水印。

- 使用 Microsoft Graph [重启预配作业](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta&preserve-view=true)。 你可以完全控制重新启动的内容。 你可以选择清除 escrows (以重新启动) 的隔离状态的托管计数器，清除 "隔离 (" 以从隔离) 中删除该应用程序，或者清除 "水印"。 使用以下请求：
 
```microsoft-graph
        POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart
```

将 "{ID}" 替换为应用程序 ID 的值，并将 "{jobId}" 替换为 [同步作业的 id](/graph/api/resources/synchronization-configure-with-directory-extension-attributes?tabs=http&view=graph-rest-beta&preserve-view=true#list-synchronization-jobs-in-the-context-of-the-service-principal)。
