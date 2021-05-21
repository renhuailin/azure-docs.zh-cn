---
title: Windows 虚拟桌面诊断问题 - Azure
description: 如何使用 Windows 虚拟桌面诊断功能来诊断问题。
author: Heidilohr
ms.topic: troubleshooting
ms.date: 09/21/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 70676bd1a07acdfcbba071a906b390ed66d70074
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "91279852"
---
# <a name="identify-and-diagnose-windows-virtual-desktop-issues"></a>识别和诊断 Windows 虚拟桌面问题

>[!IMPORTANT]
>本教程的内容适用于包含 Azure 资源管理器 Windows 虚拟桌面对象的 Windows 虚拟桌面。 如果你使用的是不包含 Azure 资源管理器对象的 Windows 虚拟桌面（经典），请参阅[此文](./virtual-desktop-fall-2019/diagnostics-role-service-2019.md)。

Windows 虚拟桌面提供了一项诊断功能，使管理员能够通过单个界面识别问题。 若要详细了解 Windows 虚拟桌面的诊断功能，请参阅[将 Log Analytics 用于诊断功能](diagnostics-log-analytics.md)。

由于诊断角色服务本身是 Windows 虚拟桌面的一部分，因此无法访问 Windows 虚拟桌面的连接将不会显示在诊断结果中。 当最终用户遇到网络连接问题时，可能会出现 Windows 虚拟桌面连接问题。

## <a name="common-error-scenarios"></a>常见错误方案

WVDErrors 表跟踪所有活动类型的错误。 名为“ServiceError”的列提供标记为“True”或“False”的额外标志。 此标志告知错误是否与服务相关。

* 如果值为“True”，则表示服务团队可能已调查此问题。 如果此错误影响了用户体验并且频繁出现，我们建议提交 Windows 虚拟桌面支持票证。
* 如果值为“False”，则表示可能存在可由你自行修复的错误配置。 错误消息中可能提供了有关如何着手解决问题的线索。

下表列出了管理员可能会遇到的常见错误。

>[!NOTE]
>此列表包括最常见的错误，并且会定期更新。 若要确保获得最新的信息，请务必每月至少查看一次本文。

## <a name="management-errors"></a>管理错误

|错误消息|建议的解决方案|
|---|---|
|无法创建注册密钥 |无法创建注册令牌。 重试创建过期时间更短（1 小时到 1 个月）的令牌。 |
|无法删除注册密钥|无法删除注册令牌。 重试删除。 如果仍不起作用，请使用 PowerShell 检查令牌是否仍然存在。 如果存在，请在 PowerShell 中将它删除。|
|无法更改会话主机排出模式 |无法更改 VM 上的排出模式。 检查 VM 状态。 如果该 VM 不可用，则无法更改排出模式。|
|无法断开连接用户会话 |无法从 VM 断开连接用户。 检查 VM 状态。 如果该 VM 不可用，则无法断开连接用户会话。 如果该 VM 可用，请检查用户会话状态，看看它是否已断开连接。 |
|无法注销会话主机中的所有用户 |无法从 VM 注销用户。 检查 VM 状态。 如果该 VM 不可用，则无法注销用户。检查用户会话状态，看看用户是否已注销。可以使用 PowerShell 强制注销。 |
|无法从应用程序组中取消分配用户|无法取消发布用户的应用组。 检查用户是否已在 Azure AD 中。 检查用户是否已在应用组发布到的用户组中。 |
|检索可用位置时出错 |检查“创建主机池”向导中使用的 VM 位置。 如果映像未在该位置提供，请在该位置添加映像，或者选择其他 VM 位置。 |

### <a name="connection-error-codes"></a>连接错误代码

|数字代码|错误代码|建议的解决方案|
|---|---|---|
|-2147467259|ConnectionFailedAdTrustedRelationshipFailure|会话主机未正确加入到 Active Directory。|
|-2146233088|ConnectionFailedUserHasValidSessionButRdshIsUnhealthy|由于会话主机不可用，连接失败。 请检查会话主机的运行状况。|
|-2146233088|ConnectionFailedClientDisconnect|如果经常看到此错误，请确保用户的计算机已连接到网络。|
|-2146233088|ConnectionFailedNoHealthyRdshAvailable|主机用户尝试连接到的会话不正常。 调试虚拟机。|
|-2146233088|ConnectionFailedUserNotAuthorized|用户无权访问已发布的应用或桌面。 在管理员删除已发布资源后，可能会出现此错误。 要求用户刷新远程桌面应用程序中的源。|
|2|FileNotFound|用户尝试访问的应用程序未正确安装或设置为不正确的路径。<br><br>在用户具有活动会话的情况下发布新应用时，用户将无法访问此应用。 必须先关闭再重启该会话，用户才能访问该应用。 |
|3|InvalidCredentials|用户输入的用户名或密码与任何现有的用户名或密码都不匹配。 请查看凭据是否有拼写错误，然后重试。|
|8|ConnectionBroken|客户端和网关或服务器之间的连接已断开。 除非意外发生，否则不需要执行任何操作。|
|14|UnexpectedNetworkDisconnect|断开与网络的连接。 要求用户再次连接。|
|24|ReverseConnectFailed|主机虚拟机没有直通 RD 网关。 请确保可以解析网关 IP 地址。|

## <a name="error-cant-add-user-assignments-to-an-app-group"></a>错误：无法将用户分配添加到应用组

将用户分配到应用组后，Azure 门户显示一条警告，指出“会话正在结束”或“遇到身份验证问题 - 扩展 Microsoft_Azure_WVD。” 然后，分配页面不加载，接着整个 Azure 门户中的页面（例如“Azure Monitor”、“Log Analytics”、“服务运行状况”等）停止加载。

原因：条件访问策略出现了问题。 Azure 门户正在尝试获取 Microsoft Graph 的令牌，而该令牌依赖于 SharePoint Online。 客户具有一个名为“Microsoft Office 365 数据存储使用条款”的条件访问策略，该策略要求用户接受使用条款才能访问数据存储。 但用户尚未登录，因此 Azure 门户无法获取该令牌。

修复：在登录到 Azure 门户之前，管理员需要先登录到 SharePoint 并接受使用条款。 之后，他们应该就可以像平时一样登录到 Azure 门户。

## <a name="next-steps"></a>后续步骤

若要了解有关 Windows 虚拟桌面中的角色的详细信息，请参阅 [Windows 虚拟桌面环境](environment-setup.md)。

若要查看适用于 Windows 虚拟桌面的可用 PowerShell cmdlet 的列表，请参阅 [PowerShell 参考](/powershell/windows-virtual-desktop/overview)。
