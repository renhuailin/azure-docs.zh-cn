---
title: Azure 虚拟桌面诊断问题 - Azure
description: 如何使用 Azure 虚拟桌面诊断功能来诊断问题。
author: Heidilohr
ms.topic: troubleshooting
ms.date: 06/19/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 0154bb61dcfbcf8024f09052d7a199ac63f71d8e
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2021
ms.locfileid: "112412534"
---
# <a name="identify-and-diagnose-azure-virtual-desktop-issues"></a>识别和诊断 Azure 虚拟桌面问题

>[!IMPORTANT]
>本教程的内容适用于包含 Azure 资源管理器 Azure 虚拟桌面对象的 Azure 虚拟桌面。 如果你使用的是不包含 Azure 资源管理器对象的 Azure 虚拟桌面（经典），请参阅[此文](./virtual-desktop-fall-2019/diagnostics-role-service-2019.md)。

Azure 虚拟桌面提供了一项诊断功能，使管理员能够通过单个界面识别问题。 若要详细了解 Azure 虚拟桌面的诊断功能，请参阅[将 Log Analytics 用于诊断功能](diagnostics-log-analytics.md)。

由于诊断角色服务本身是 Azure 虚拟桌面的一部分，因此无法访问 Azure 虚拟桌面的连接将不会显示在诊断结果中。 当最终用户遇到网络连接问题时，可能会出现 Azure 虚拟桌面连接问题。

## <a name="common-error-scenarios"></a>常见错误方案

WVDErrors 表跟踪所有活动类型的错误。 名为“ServiceError”的列提供标记为“True”或“False”的额外标志。 此标志告知错误是否与服务相关。

* 如果值为“True”，则表示服务团队可能已调查此问题。 如果此错误影响了用户体验并且频繁出现，我们建议提交 Azure 虚拟桌面支持票证。
* 如果值为“False”，则表示可能存在可由你自行修复的错误配置。 错误消息中可能提供了有关如何着手解决问题的线索。

下表列出了管理员可能会遇到的常见错误。

>[!NOTE]
>此列表包括最常见的错误，并且会定期更新。 若要确保获得最新的信息，请务必每月至少查看一次本文。

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

## <a name="next-steps"></a>后续步骤

若要了解有关 Azure 虚拟桌面中的角色的详细信息，请参阅 [Azure 虚拟桌面环境](environment-setup.md)。

若要查看适用于 Azure 虚拟桌面的可用 PowerShell cmdlet 的列表，请参阅 [PowerShell 参考](/powershell/windows-virtual-desktop/overview)。
