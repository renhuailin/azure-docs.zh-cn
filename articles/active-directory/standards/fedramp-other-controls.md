---
title: 配置其他控件以满足 FedRAMP High 影响要求
description: 详细说明如何配置其他控件以满足 FedRAMP High 影响级别要求。
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: barbaraselden
ms.author: baselden
manager: celested
ms.reviewer: martinco
ms.date: 4/26/2021
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ed80ffbe92777066e09d443f3e5319ad721be653
ms.sourcegitcommit: 9ad20581c9fe2c35339acc34d74d0d9cb38eb9aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2021
ms.locfileid: "110540291"
---
# <a name="configure-additional-controls-to-achieve-fedramp-high-impact-level"></a>配置其他控件以满足 FedRAMP High 影响级别要求

你可能需要在 Azure Active Directory (Azure AD) 租户中配置以下控制和控制增强功能列表。

下表中的每一行都提供规范性指导。 这些说明性指导可帮助你制定组织对与控件或控件增强有关的所有共同责任的响应措施。

## <a name="audit-and-accountability"></a>审核和责任

下表中的指南涉及：

* AU-02 审核事件
* AU-03 审核内容
* AU-06 审核评审、分析和报告

| 控件 ID 和子部分| 客户责任和指导 |
| - | - |
| AU-02 <br>AU-03 <br>AU-03(1)<br>AU-03(2)| 确保系统能够审核 AU-02 第 a 部分中定义的事件。 与组织内可审核事件子集中的其他实体协调，以支持展开事后调查。 实行集中管理审核记录。<p>所有帐户生命周期操作（帐户创建、修改、启用、禁用和删除操作）都在 Azure AD 审核日志中审核。 所有身份验证和授权事件都在 Azure AD 登录日志中进行审核，检测到的任何风险都在标识保护日志中审核。 其中的每个日志都可以直接流式传输到安全信息和事件管理 (SIEM) 解决方案（例如 Azure Sentinel）。 还可以使用 Azure 事件中心将日志与第三方 SIEM 解决方案集成。<p>审核事件<li> [Azure Active Directory 门户中的“审核活动”报表](../reports-monitoring/concept-audit-logs.md)<li> [Azure Active Directory 门户中的“登录活动”报表](../reports-monitoring/concept-sign-ins.md)<li>[如何：调查风险](../identity-protection/howto-identity-protection-investigate-risk.md)<p>SIEM 集成<li> [Azure Sentinel：连接来自 Azure Active Directory (Azure AD) 的数据](../../sentinel/connect-azure-active-directory.md)<li>[流式传输到 Azure 事件中心和其他 SIEM](../reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) |
| AU-06<br>AU-06(1)<br>AU-06(3)<br>AU-06(4)<br>AU-06(5)<br>AU-06(6)<br>AU-06(7)<br>AU-06(10)<br>| 每周至少审查并分析一次审核记录，以标识不合适或异常的活动，并向相应的人员报告结果。 <p>根据上面提供的针对 AU-02 和 AU-03 的指南，每周审查审核记录并向相关人员报告。 仅使用 Azure AD 尚无法满足这些要求。 还必须使用 SIEM 解决方案，例如 Azure Sentinel。 有关详细信息，请参阅“[什么是 Azure Sentinel？](../../sentinel/overview.md)”。 |

## <a name="incident-response"></a>事件响应

下表中的指南涉及：

* IR-04 事件处理

* IR-05 事件监视

| 控件 ID 和子部分| 客户责任和指导 |
| - | - |
| IR-04<br>IR-04(1)<br>IR-04(2)<br>IR-04(3)<br>IR-04(4)<br>IR-04(6)<br>IR-04(8)<br>IR-05<br>IR-05(1)| 实现事件处理和监视功能。 包括自动化事件处理、动态重新配置、操作连续性、信息关联、内部威胁、与外部组织的关联、事件监视和自动跟踪。 <p>审核日志将记录发生的所有配置更改。 身份验证和授权事件在 Azure AD 登录日志中进行审核，检测到的任何风险都在标识保护日志中审核。 可以将每个日志直接流式传输到 SIEM 解决方案中，例如 Azure Sentinel。 还可以使用 Azure 事件中心将日志与第三方 SIEM 解决方案集成。 使用 Microsoft Graph 或 Azure AD PowerShell，根据 SIEM 中的事件自动执行动态重新配置。<p>审核事件<br><li>[Azure Active Directory 门户中的“审核活动”报表](../reports-monitoring/concept-audit-logs.md)<li>[Azure Active Directory 门户中的“登录活动”报表](../reports-monitoring/concept-sign-ins.md)<li>[如何：调查风险](../identity-protection/howto-identity-protection-investigate-risk.md)<p>SIEM 集成<li>[Azure Sentinel：连接来自 Azure Active Directory (Azure AD) 的数据](../../sentinel/connect-azure-active-directory.md)<li>[流式传输到 Azure 事件中心和其他 SIEM](../reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)<p>动态重新配置<li>[AzureAD 模块](/powershell/module/azuread/)<li>[Microsoft Graph 概述](/graph/overview?view=graph-rest-1.0&preserve-view=true) |

## <a name="personnel-security"></a>人员安全

下表中的指南涉及：

* PS-04 人员解职

| 控件 ID 和子部分| 客户责任和指导 |
| - | - |
| PS-04<br>PS-04(2)| 自动通知负责禁用此系统的访问权限的人员。 <p>在 8 小时内禁用帐户并撤销所有关联的验证器和凭据。 <p>通过外部 HR 系统、本地 Active Directory 或直接在云中配置 Azure AD 的帐户预配（包括在终止时禁用）。 通过撤销现有会话来终止所有系统访问。 <p>帐户预配<li> 请参阅 AC-02 中的详细指南。 <p>撤销所有关联的验证器 <li> [在 Azure Active Directory 中紧急撤销用户访问权限](../enterprise-users/users-revoke-access.md) |


## <a name="system-and-information-integrity"></a>系统和信息完整性

下表中的指南涉及：

* SI-04 信息系统监视

 控件 ID 和子部分| 客户责任和指导 |
| - | - |
| SI-04<br>SI-04(1)| 实施信息系统范围的监视和入侵检测系统。 <p>包括信息系统监视解决方案中的所有 Azure AD 日志（审核、登录、标识保护）。 <p>将 Azure AD 日志流式传输到 SIEM 解决方案中（请参阅 IA-04）。 |

## <a name="next-steps"></a>后续步骤

[配置访问控制](fedramp-access-controls.md)

[配置标识和身份验证控件](fedramp-identification-and-authentication-controls.md)

[配置其他控件](fedramp-other-controls.md)
 