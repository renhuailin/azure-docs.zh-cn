---
title: 基于用户风险的条件访问 - Azure Active Directory
description: 使用标识保护用户风险创建条件访问策略
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/04/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: dae788ea2ee96ea059e919a9e720216162618312
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128596422"
---
# <a name="conditional-access-user-risk-based-conditional-access"></a>条件访问：基于用户风险的条件访问

Microsoft 会与研究人员、执法机构、Microsoft 安全团队以及其他受信任的源合作，以查找已泄露的用户名和密码对。 使用 Azure AD Premium P2 许可证的组织可以创建包含 [Azure AD 标识保护用户风险检测](../identity-protection/concept-identity-protection-risks.md#user-linked-detections)的条件访问策略。 

可以在两个位置配置此策略：条件访问和标识保护。 使用条件访问策略的配置是提供更多上下文的首选方法，包括增强的诊断数据、仅报告模式集成、图形 API 支持，以及利用策略中其他条件访问属性的能力。

## <a name="enable-with-conditional-access-policy"></a>通过条件访问策略进行启用

1. 以全局管理员、安全管理员或条件访问管理员的身份登录到 **Azure 门户**。
1. 浏览到“Azure Active Directory” > “安全性” > “条件访问”。
1. 选择“新策略”。
1. 为策略指定名称。 建议组织为其策略的名称创建有意义的标准。 有关详细信息，请参阅[为策略设置命名标准](./plan-conditional-access.md#set-naming-standards-for-your-policies)。
1. 在“分配”  下，选择“用户和组”  。
   1. 在“包括”下，选择“所有用户”。  
   1. 在“排除”下选择“用户和组”，然后选择组织的紧急访问帐户或不受限帐户。 
   1. 选择“完成”  。
1. 在“云应用或操作”   >   “包括”下，选择“所有云应用”。 
1. 在“条件” > “用户风险”下，将“配置”设置为“是”。 在“配置强制执行策略所需的用户风险级别”下，选择“高”，然后选择“完成”。
1. 在“访问控制” > “授予”下，依次选择“"授予访问权限”、“需要更改密码”和“选择”。
1. 确认设置并将“启用策略”设置为“打开”。
1. 选择“创建”  ，以便创建启用策略所需的项目。

## <a name="enable-through-identity-protection"></a>通过标识保护进行启用

1. 登录 **Azure 门户**。
1. 选择“所有服务”，然后浏览到“Azure AD 标识保护”。 
1. 选择“用户风险策略”。
1. 在“分配”下，选择“用户”。
   1. 在“包括”下，选择“所有用户”。  
   1. 在“排除”下选择“选择排除的用户”，接着选择组织的紧急访问帐户或不受限帐户，然后选择“选择”。
   1. 选择“完成”。
1. 在“条件”下选择“用户风险”，然后选择“高”。
   1. 选择“选择”，然后选择“完成” 。
1. 在“控制” > “访问权限”下选择“允许访问”，然后选择“需要更改密码”。
   1. 选择“选择”。
1. 将“强制实施策略”设置为“打开” 。
1. 选择“保存”。

## <a name="next-steps"></a>后续步骤

[条件访问常见策略](concept-conditional-access-policy-common.md)

[基于登录风险的条件访问](howto-conditional-access-policy-risk.md)

[使用条件访问仅限报告模式确定影响](howto-conditional-access-insights-reporting.md)

[使用条件访问 What If 工具模拟登录行为](troubleshoot-conditional-access-what-if.md)

[Azure Active Directory 标识保护是什么？](../identity-protection/overview-identity-protection.md)