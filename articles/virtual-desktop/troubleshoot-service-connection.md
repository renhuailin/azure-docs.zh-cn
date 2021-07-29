---
title: 排查 Azure 虚拟桌面的服务连接问题 - Azure
description: 如何解决在 Azure 虚拟桌面租户环境中设置服务连接时遇到的问题。
author: Heidilohr
ms.topic: troubleshooting
ms.date: 10/15/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: cce3d219158111d147849e45fb0990f41e2423ab
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2021
ms.locfileid: "111756736"
---
# <a name="azure-virtual-desktop-service-connections"></a>Azure 虚拟桌面服务连接

>[!IMPORTANT]
>本内容适用于包含 Azure 资源管理器 Azure 虚拟桌面对象的 Azure 虚拟桌面。 如果你使用的是不包含 Azure 资源管理器对象的 Azure 虚拟桌面（经典），请参阅[此文](./virtual-desktop-fall-2019/troubleshoot-service-connection-2019.md)。

使用本文解决 Azure 虚拟桌面客户端连接问题。

## <a name="provide-feedback"></a>提供反馈

可以通过 [Azure 虚拟桌面技术社区](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)，向我们提供反馈并与产品团队和其他活跃的社区成员共同探讨 Azure 虚拟桌面服务。

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>用户可以连接，但不显示任何内容（无源）

用户可以启动远程桌面客户端，也可以进行身份验证，但在 Web 发现源中看不到任何图标。

1. 使用以下命令行确认已将报告问题的用户分配给应用程序组：

     ```powershell
     Get-AzRoleAssignment -SignInName <userupn>
     ```

2. 确认用户正在用正确的凭据登录。

3. 如果正在使用 Web 客户端，请确认没有缓存的凭据问题。

4. 如果用户是 Azure Active Directory (AD) 用户组的成员，请确保该用户组是安全组，而不是通讯组。 Azure 虚拟桌面不支持 Azure AD 通讯组列表。

## <a name="user-loses-existing-feed-and-no-remote-resource-is-displayed-no-feed"></a>用户丢失现有的源，并且没有显示远程资源（没有源）

当用户将其订阅从一个 Azure AD 租户移动到另一租户后，通常会出现此错误。 因此，该服务无法跟踪其用户分配，因为这些分配仍与旧的 Azure AD 租户相关联。

若要解决此问题，只需将用户重新分配到其应用组。

如果 CSP 提供商创建了订阅，然后将其传输给客户，则也可能发生这种情况。 若要解决此问题，请重新注册资源提供程序。

1. 登录到 Azure 门户。
2. 转到“订阅”，然后选择你的订阅。
3. 在页面左侧的菜单中，选择“资源提供程序”。
4. 找到并选择“Microsoft.DesktopVirtualization”，然后选择“重新注册” 。

## <a name="next-steps"></a>后续步骤

- 如需大致了解如何排查 Azure 虚拟桌面问题和跟踪升级，请参阅[故障排除概述、反馈和支持](troubleshoot-set-up-overview.md)。
- 若要排查在 Azure 虚拟桌面环境中创建 Azure 虚拟桌面环境和主机池时遇到的问题，请参阅[环境和主机池创建](troubleshoot-set-up-issues.md)。
- 若要排查在 Azure 虚拟桌面中配置虚拟机 (VM) 时遇到的问题，请参阅[会话主机虚拟机配置](troubleshoot-vm-configuration.md)。
- 若要排查与 Azure 虚拟桌面代理或会话连接性相关的问题，请参阅[排查常见的 Azure 虚拟桌面代理问题](troubleshoot-agent.md)。
- 若要排查将 PowerShell 与 Azure 虚拟桌面结合使用时遇到的问题，请参阅 [Azure 虚拟桌面 PowerShell](troubleshoot-powershell.md)。
- 若要完成故障排除教程，请参阅[教程：排查资源管理器模板部署问题](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)。
