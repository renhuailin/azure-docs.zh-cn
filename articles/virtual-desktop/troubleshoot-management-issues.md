---
title: Azure 虚拟桌面管理问题 - Azure
description: Azure 虚拟桌面中的常见管理问题及其解决方法。
author: Heidilohr
ms.topic: troubleshooting
ms.date: 06/19/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 529cf643cb68bc806aef991142e68f42fccc0d42
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2021
ms.locfileid: "112420690"
---
# <a name="management-issues"></a>管理问题

本文介绍常见管理错误，并针对如何解决这些错误提供建议。

## <a name="common-management-errors"></a>常见管理错误

下表列出了由于管理相关问题而出现的错误消息，以及有关如何解决这些问题的建议。

|错误消息|建议的解决方案|
|---|---|
|无法创建注册密钥 |无法创建注册令牌。 重试创建过期时间更短（1 小时到 1 个月）的令牌。 |
|无法删除注册密钥|无法删除注册令牌。 重试删除。 如果仍不起作用，请使用 PowerShell 检查令牌是否仍然存在。 如果存在，请在 PowerShell 中将它删除。|
|无法更改会话主机排出模式 |无法更改 VM 上的排出模式。 检查 VM 状态。 如果 VM 不可用，则无法更改排出模式。|
|无法断开连接用户会话 |无法从 VM 断开连接用户。 检查 VM 状态。 如果 VM 不可用，则无法断开用户会话的连接。 如果该 VM 可用，请检查用户会话状态，看看它是否已断开连接。 |
|无法注销会话主机中的所有用户 |无法从 VM 注销用户。 检查 VM 状态。 如果该 VM 不可用，则无法注销用户。检查用户会话状态，看看用户是否已注销。可以使用 PowerShell 强制注销。 |
|无法从应用程序组中取消分配用户|无法取消发布用户的应用组。 检查用户是否已在 Azure AD 中。 检查用户是否已在应用组发布到的用户组中。 |
|检索可用位置时出错 |检查“创建主机池”向导中使用的 VM 位置。 如果映像未在该位置提供，请在该位置添加映像，或者选择其他 VM 位置。 |

## <a name="error-cant-add-user-assignments-to-an-app-group"></a>错误：无法将用户分配添加到应用组

将用户分配到应用组后，Azure 门户显示一条警告，指出“会话正在结束”或“遇到身份验证问题 - 扩展 Microsoft_Azure_WVD。” 然后，分配页面不加载，接着整个 Azure 门户中的页面（例如“Azure Monitor”、“Log Analytics”、“服务运行状况”等）停止加载。

出现此问题的原因通常是条件访问策略存在问题。 Azure 门户正在尝试获取 Microsoft Graph 的令牌，而该令牌依赖于 SharePoint Online。 客户具有一个名为“Microsoft Office 365 数据存储使用条款”的条件访问策略，该策略要求用户接受使用条款才能访问数据存储。 但用户尚未登录，因此 Azure 门户无法获取该令牌。

要修复此问题，在登录到 Azure 门户之前，管理员需要先登录到 SharePoint 并接受使用条款。 之后，他们应该就可以像平时一样登录到 Azure 门户。

## <a name="next-steps"></a>后续步骤

若要查看诊断功能可以识别的常见错误场景，请参阅[识别和诊断问题](diagnostics-role-service.md#common-error-scenarios)。
