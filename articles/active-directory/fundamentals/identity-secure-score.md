---
title: 什么是标识安全分数？ - Azure Active Directory
description: 如何使用标识安全分数来改进目录的安全状况
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 03/23/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: guptashi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23832d9f1205105f1f9711cdf3260b74ee4a9bb1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104952245"
---
# <a name="what-is-the-identity-secure-score-in-azure-active-directory"></a>什么是 Azure Active Directory 中的标识安全评分？

你的 Azure AD 租户有多安全？ 如果不知道此问题的答案，本文将介绍标识安全分数如何帮助你监视和改进标识安全状况。

## <a name="what-is-an-identity-secure-score"></a>标识安全评分是什么？

标识安全分数是一个百分比指标，反映你与 Microsoft 安全最佳做法建议保持一致的情况。 标识安全分数中的每项改进操作都是根据特定配置定制的。  

![安全功能分数](./media/identity-secure-score/identity-secure-score-overview.png)

评分有助于：

- 客观衡量标识安全状况
- 规划标识安全改进
- 审查改进措施的成败

可以在安全评分仪表板上访问评分和标识相关的信息。 此仪表板提供：

- 你的标识安全分数
- 对比图，显示你的标识安全分数与同行业类似大小的其他租户的对比情况
- 趋势图，显示你的标识安全分数随时间的变化情况
- 可能的改进列表

遵循改进操作可以：

- 改进安全状况和分数
- 作为标识投资的一部分利用组织可用的功能

## <a name="how-do-i-get-my-secure-score"></a>如何获取安全评分？

所有 Azure AD 版本中都提供标识安全分数。 组织可以从“Azure 门户” > “Azure Active Directory” > “安全” > “标识安全分数”中访问自己的标识安全分数。

## <a name="how-does-it-work"></a>它是如何工作的？

Azure 每隔 48 小时检查安全配置，并将你的设置与建议的最佳做法进行比较。 然后，根据此项评估的结果为目录计算新的分数。 安全配置可能与最佳做法指南并不完全一致，仅满足部分改进操作。 在这些情况下，你只能获得该控件可用最大分数的一部分。

每条建议是根据 Azure AD 配置来衡量的。 如果使用第三方产品来实现建议的最佳做法，可以在改进操作的设置中指示此配置。 此外，如果建议不适用于你的环境，还可以选择设置要忽略的建议。 评分计算不考虑已忽略的建议。

![忽略或标记第三方涵盖的操作](./media/identity-secure-score/identity-secure-score-ignore-or-third-party-reccomendations.png)

## <a name="how-does-it-help-me"></a>评分有什么作用？

安全评分有助于：

- 客观衡量标识安全状况
- 规划标识安全改进
- 审查改进措施的成败

## <a name="what-you-should-know"></a>要点

### <a name="who-can-use-the-identity-secure-score"></a>谁可以使用标识安全评分？

以下角色可以使用标识安全评分：

- 全局管理员
- 安全管理员
- 安全读取者

### <a name="how-are-controls-scored"></a>控件的评分原理

控件通过两种方式进行评分。 有些以二进制方式评分 - 如果根据建议配置了功能或设置，则会获得 100% 的分数。 其他分数以总配置的百分比计算。 例如，如果改进建议指出，使用 MFA 保护所有用户则会获得最大分值 10.71%；而你只对 100 个总用户当中的 5 个进行了保护，则只能获得部分分数，约为 0.53%（5 个受保护用户/100 个总用户 * 最大分值 10.71% = 0.53% 部分分数）。

### <a name="what-does-not-scored-mean"></a>[未评分] 是什么意思？

标记为 [未评分] 的操作是可以在组织中执行的，但由于尚未挂接到工具中，因而不会评分的操作。 因此，仍可以改进安全性，但这些操作不能马上带来积分。

### <a name="how-often-is-my-score-updated"></a>评分的更新频率如何？

评分每天计算一次（大约在太平洋标准时间 1:00 AM）。 如果对衡量的操作做了更改，评分将在下一日自动更新。 最长需要在 48 小时之后，更改才会反映在评分中。

### <a name="my-score-changed-how-do-i-figure-out-why"></a>我的评分已更改。 如何找出更改的原因？

前往 [Microsoft 365 安全中心](https://security.microsoft.com/)，从中可找到完整的 Microsoft 安全分数。 通过查看“历史记录”选项卡上的详细变化，可以轻松地查看安全分数的所有变化。

### <a name="does-the-secure-score-measure-my-risk-of-getting-breached"></a>安全分数是否衡量违规的风险？

简单地说，不会。 安全分数并不会表明违规可能性的绝对衡量结果。 它展示的信息仅限于采用的哪些功能可以缓解违规风险。 任何服务都不能保证你不会违规，不应以任何方式将安全分数解释为一项保证。

### <a name="how-should-i-interpret-my-score"></a>如何解释评分？

你的分数会随着配置建议的安全功能或执行安全相关的任务（例如读取报表）而改进。 某些操作是根据部分完成状态评分的，例如，为用户启用多重身份验证 (MFA)。 安全分数直接代表你所用的 Microsoft 安全服务。 请记住，安全性与可用性之间必须保持平衡。 所有安全控制措施都有影响用户的一面。 用户影响较小的控制措施对用户日常操作的影响很小，甚至没有任何影响。

要查看分数历史记录，请转到 [Microsoft 365 安全中心](https://security.microsoft.com/)，并查看整体 Microsoft 安全分数。 单击“查看历史记录”，可以查看整体安全分数的变化情况。 选择特定的日期以查看在该日期启用的控制措施，以及从每个控制措施赢得的积分。

### <a name="how-does-the-identity-secure-score-relate-to-the-microsoft-365-secure-score"></a>标识安全分数与 Microsoft 365 安全分数之间有什么关系？

[Microsoft 安全分数](/office365/securitycompliance/microsoft-secure-score)包含五个不同的控制和评分类别：

- 标识
- 数据
- 设备
- 基础结构
- “应用”

标识安全分数代表 Microsoft 安全分数的标识部分。 重叠意味着，标识安全分数与 Microsoft 中标识分数提供的建议相同。

## <a name="next-steps"></a>后续步骤

[详细了解 Microsoft 安全分数](/office365/securitycompliance/microsoft-secure-score)