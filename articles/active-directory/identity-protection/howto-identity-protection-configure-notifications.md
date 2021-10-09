---
title: “Azure Active Directory 标识保护”通知
description: 了解通知如何支持调查活动。
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 09/23/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8fea2ff8f44cd017d901e94e7458d968d5c82e9f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128619811"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>“Azure Active Directory 标识保护”通知

Azure AD 标识保护会发送两种类型的自动生成的通知电子邮件，帮助你管理用户风险和风险检测：

- 检测到有风险的用户电子邮件
- 每周摘要电子邮件

本文概述了两种通知电子邮件。

我们不支持向具有组分配角色的用户发送电子邮件。

## <a name="users-at-risk-detected-email"></a>检测到有风险的用户电子邮件

当“Azure AD 标识保护”检测到帐户受到威胁时，会生成“检测到有风险的用户”的警报电子邮件。 该电子邮件包括指向[针对风险报告而标记的用户](./overview-identity-protection.md)的链接。 建议立即调查有风险的用户。

此警报的配置允许你指定要生成警报的用户风险级别。 当用户的风险级别达到指定级别时，将生成电子邮件。 例如，如果将策略设置为对中等用户风险发出警报，而用户 John 由于实时登录风险，其用户风险达到中等风险，那么你将收到检测到有风险用户的电子邮件。 如果用户的后续风险检测导致该用户的风险级别计算达到指定的风险级别（或更高），则在重新计算用户风险分数后，你将收到检测到有风险的用户的更多电子邮件。 例如，如果某位用户在 1 月 1 日转变为中等风险，那么如果你的设置设为针对中等风险发出警报，则你将收到电子邮件通知。 如果同一用户随后在 1 月 5 日的另一次风险检测中仍然被检测为中等风险，并且用户风险分数经重新计算后仍为中等，则你会收到另一封电子邮件通知。 

但是，只有进行风险检测（导致用户风险级别变化的风险检测）的时间比上次电子邮件发送的时间更新时，才发送额外的电子邮件通知。 例如，用户在 1 月 1 日凌晨 5 点登录，并且没有实时风险（这意味着不会由于此次登录操作而生成电子邮件）。 10 分钟后，在凌晨 5:10，同一用户再次登录，实时风险高，导致用户风险级别达到高风险级别，则会发送电子邮件。 然后，在凌晨 5:15，由于脱机风险处理，凌晨 5:00 进行的原始登录的脱机风险分数更改为高风险。 这时不会发送标记其他用户存在风险的电子邮件，因为第一次登录的时间在第二次登录之前，而第二次登录已经触发了电子邮件通知。

为防止电子邮件过载，在 5 秒时间段内只会收到一封电子邮件。 此延迟意味着，如果多个用户在同一 5 秒时间段内转变为指定的风险级别，我们将聚合并发送一封电子邮件来表示所有这些用户的风险级别变化。

如果组织已启用自修正功能（如[使用 Azure AD 标识保护的用户体验](concept-identity-protection-user-experience.md)一文中所述），那么在你获得机会调查之前，用户有机会修正其风险。 在“有风险用户”或“有风险登录”报表中，通过向“风险状态”筛选器中添加“已修正”，可以查看已进行修正的有风险用户和有风险登录  。

![检测到有风险的用户电子邮件](./media/howto-identity-protection-configure-notifications/01.png)

### <a name="configure-users-at-risk-detected-alerts"></a>配置检测到有风险用户的警报

管理员可以设置：

- **触发生成此电子邮件的用户风险级别** - 默认情况下，此风险级别设置为“高”风险。
- 此电子邮件的收件人 -全局管理员、安全管理员或安全读取者角色的用户将自动添加到此列表中。 我们会尝试向每个角色的前 20 名成员发送电子邮件。 如果某位用户在 PIM 中注册以根据需要提升到这些角色之一，那么只有他们在发送电子邮件之前已完成提升，才会收到电子邮件。
   - 或者，可以在 **此处添加自定义电子邮件**，定义的用户必须具有适当的权限才能在 Azure 门户中查看链接报表。

在“Azure 门户”的“Azure Active Directory” > “安全” > “标识保护” > “检测到的有风险用户警报”下，配置有风险用户的电子邮件。

## <a name="weekly-digest-email"></a>每周摘要电子邮件

每周摘要电子邮件中包含新风险检测的摘要。  
其中包括：

- 检测到新的有风险用户
- 检测到新的有风险登录（实时）
- 指向“标识保护”中相关报告的链接

![每周摘要电子邮件](./media/howto-identity-protection-configure-notifications/weekly-digest-email.png)

全局管理员、安全管理员或安全读取者角色的用户将自动添加到此列表中。 我们会尝试向每个角色的前 20 名成员发送电子邮件。 如果某位用户在 PIM 中注册以根据需要提升到这些角色之一，那么只有他们在发送电子邮件之前已完成提升，才会收到电子邮件

### <a name="configure-weekly-digest-email"></a>配置每周摘要电子邮件

作为管理员，可以打开或关闭每周摘要电子邮件，然后选择分配的接收该电子邮件的用户。

在“Azure 门户”的“Azure Active Directory” > “安全” > “标识保护” > “每周摘要”下，配置每周摘要电子邮件。

## <a name="see-also"></a>另请参阅

- [Azure Active Directory 标识保护](./overview-identity-protection.md)
