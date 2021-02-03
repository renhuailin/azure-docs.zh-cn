---
title: Azure Active Directory 中的标识保护的常见问题
description: 常见问题 Azure AD Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: troubleshooting
ms.date: 01/07/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6117b1ac78faf84d73f5a78202709aec7a1f84d9
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2021
ms.locfileid: "99492580"
---
# <a name="frequently-asked-questions-identity-protection-in-azure-active-directory"></a>Azure Active Directory 中的常见问题标识保护

## <a name="dismiss-user-risk-known-issues"></a>消除用户风险已知问题

消除经典 Identity Protection 中的 **用户风险** 将标识保护的用户风险历史记录中的执行组件设置为 **Azure AD**。

消除 Identity Protection 中的 **用户风险** 将标识保护的用户风险历史记录中的执行组件设置为 **\<Admin’s name with a hyperlink pointing to user’s blade\>** 。

当前已知问题导致用户风险消除流中的延迟。 如果具有“用户风险策略”，则该策略将在单击“消除用户风险”后的几分钟内阻止应用于已消除的用户。 但是，UX 刷新已消除用户的“风险状态”时，存在已知延迟。 要解决此问题，请在浏览器级刷新页面以查看最新的用户“风险状态”。


## <a name="frequently-asked-questions"></a>常见问题

### <a name="why-is-a-user-at-risk"></a>为什么用户面临风险？

如果你是 Azure AD Identity Protection 的客户，请前往有风险的 [用户](howto-identity-protection-investigate-risk.md#risky-users) 视图，并单击 "有风险的用户"。 在底部的抽屉中，选项卡 "风险历史记录" 将显示导致用户风险更改的所有事件。 若要查看用户的所有有风险的登录，请单击 "用户的危险登录"。 若要查看此用户的所有风险检测，请单击 "用户的风险检测"。

### <a name="why-was-my-sign-in-blocked-but-identity-protection-didnt-generate-a-risk-detection"></a>为什么我的登录被阻止，但 Identity Protection 没有生成风险检测？
由于多种原因，登录可能被阻止。 需要特别注意的是，标识保护仅在身份验证请求中使用正确的凭据时才会生成风险检测。 如果用户使用不正确的凭据，则标识保护将不会对其进行标记，因为如果错误的使用者使用正确的凭据，则不存在凭据泄露的风险。 阻止用户签名但不会生成标识保护检测的一些原因包括：
* 由于 IP 地址的恶意活动， **ip 可能被阻止** 。 IP 阻止消息不区分凭据是否正确。 如果 IP 被阻止并且未使用正确的凭据，则不会生成标识保护检测
* 在多次尝试失败后，**[智能锁定](../authentication/howto-password-smart-lockout.md)** 会阻止该帐户登录
* 可以强制实施 **条件性访问策略** ，该策略使用风险级别以外的条件来阻止身份验证请求

### <a name="how-can-i-get-a-report-of-detections-of-a-specific-type"></a>如何获取特定类型检测的报告？

中转到风险检测视图并按 "检测类型" 筛选。 然后，你可以在中下载此报表。CSV 或。JSON 格式使用顶部的 " **下载** " 按钮。 有关详细信息，请参阅文章 [如何：调查风险](howto-identity-protection-investigate-risk.md#risk-detections)。

### <a name="why-cant-i-set-my-own-risk-levels-for-each-risk-detection"></a>为什么我不能为每个风险检测设置自己的风险级别？

“标识保护”中的风险级别依赖于检测的精度，并由我们的监督式机器学习提供支持。 若要自定义用户体验，管理员可以在用户风险和登录风险策略中包含/排除某些用户/组。

### <a name="why-does-the-location-of-a-sign-in-not-match-where-the-user-truly-signed-in-from"></a>为什么登录位置与用户真正登录的位置不匹配？

IP 地理位置映射是整个行业面临的挑战。 如果你认为登录报告中列出的位置与实际位置不匹配，请联系 Microsoft 支持部门。 

### <a name="how-can-i-close-specific-risk-detections-like-i-did-in-the-old-ui"></a>如何关闭特定的风险检测（如我在旧 UI 中执行的操作）？

您可以通过确认链接的登录为已泄露或安全来提供有关风险检测的反馈。 登录时提供的反馈 trickles 对该登录上发出的所有检测。 如果要关闭未链接到登录的检测，可以在用户级别上提供相应的反馈。 有关详细信息，请参阅文章 [如何：在 Azure AD Identity Protection 中提供风险反馈](howto-identity-protection-risk-feedback.md)。

### <a name="how-far-can-i-go-back-in-time-to-understand-whats-going-on-with-my-user"></a>我可以在多长时间后回来来了解我的用户会发生什么情况呢？

- "有 [风险的用户](howto-identity-protection-investigate-risk.md#risky-users) " 视图根据过去的所有登录显示用户的风险。 
- 有风险的 [登录](howto-identity-protection-investigate-risk.md#risky-sign-ins) 视图在过去30天内显示了风险标志。 
- " [风险检测](howto-identity-protection-investigate-risk.md#risk-detections) " 视图显示在过去90天内进行的风险检测。

### <a name="how-can-i-learn-more-about-a-specific-detection"></a>如何了解有关特定检测的详细信息？

所有风险检测都记录在项目中有 [什么风险](concept-identity-protection-risks.md#risk-types-and-detection)。 您可以将鼠标悬停在 Azure 门户检测旁边的 (") " 符号上，以了解有关检测的详细信息。

### <a name="how-do-the-feedback-mechanisms-in-identity-protection-work"></a>“标识保护”中的反馈机制是如何工作的？

**确认泄漏**（在登录时） - 通知 Azure AD 标识保护，该登录不是由标识所有者执行的，并指示存在泄漏。

- 在收到此反馈后，我们将登录和用户风险状态改为“已确认泄漏”，并将风险级别改为“高”。

- 此外，我们还为我们的机器学习系统提供信息，以便今后改进风险评估。

    > [!NOTE]
    > 如果已对用户采取修正措施，不要单击“确认泄漏”，因为这会将登录和用户风险状态改为“已确认泄漏”，并将风险级别改为“高”。

**确认安全**（在登录时） - 通知 Azure AD 标识保护，登录是由标识所有者执行的，不指示存在泄漏。

- 收到此反馈后，我们将登录（而非用户）风险状态改为“已确认安全”，将风险级别改为 -。

- 此外，我们还为我们的机器学习系统提供信息，以便今后改进风险评估。 

    > [!NOTE]
    >现在，选择 "在登录时确认安全" 不会自行阻止将来使用相同属性的登录进行标记为有风险。 训练系统以了解用户属性的最佳方式是将有风险的登录策略用于 MFA。 当系统提示有风险登录，并且用户成功响应请求时，登录可能会成功，并有助于对系统进行合法用户的行为培训。
    >
    > 如果认为用户没有遭到泄漏，请使用用户级别的“消除用户风险”，而不是登录级别的“已确认安全”。 用户级别的“消除用户风险”会关闭用户风险和所有过去的有风险登录和风险检测。

### <a name="why-am-i-seeing-a-user-with-a-low-or-above-risk-score-even-if-no-risky-sign-ins-or-risk-detections-are-shown-in-identity-protection"></a>即使在“标识保护”中没有显示有风险登录或风险检测，为什么会出现较低（或较高）的用户风险评分？

考虑到用户风险实质上是累积的，并且不会过期，即使“标识保护”最近没有显示有风险登录或风险检测，也可能存在较低或较高用户风险。 如果用户的唯一恶意活动超出了时间范围，则会发生这种情况，我们会在该时间段内存储有风险的登录和风险检测的详细信息。 我们不会终止用户风险，因为众所周知，恶意参与者会在客户环境的遭到泄漏的标识后停留超过 140 天，然后加大攻击力度。 客户可以通过访问 `Azure Portal > Azure Active Directory > Risky users’ report > Click on an at-risk user > Details’ drawer > Risk history tab` 来查看用户的风险时间线，以了解为什么用户处于风险之中。

### <a name="why-does-a-sign-in-have-a-sign-in-risk-aggregate-score-of-high-when-the-detections-associated-with-it-are-of-low-or-medium-risk"></a>为什么登录的“登录风险(总计)”分数为高，而与之相关的检测结果为低风险或中等风险？

高总计风险评分可能基于登录的其他特性，或者针对该登录触发了多个检测的事实。 反之，登录也可能为中等“登录风险(总计)”，即使与登录相关的检测为高风险。

### <a name="what-is-the-difference-between-the-activity-from-anonymous-ip-address-and-anonymous-ip-address-detections"></a>"来自匿名 IP 地址的活动" 和 "匿名 IP 地址" 检测之间的区别是什么？

"匿名 IP 地址" 检测的源是 Azure AD Identity Protection，而 "来自匿名 IP 地址的活动" 检测是从 MCAS (Microsoft Cloud App Security) 集成的。 虽然它们具有非常相似的名称，并且可能会在这些信号中出现重叠，但它们具有不同的后端检测。
