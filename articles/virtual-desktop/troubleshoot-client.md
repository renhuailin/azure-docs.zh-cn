---
title: 排查远程桌面客户端问题 - Azure 虚拟桌面 - Azure
description: 如何解决在 Azure 虚拟桌面租户环境中设置客户端连接时遇到的问题。
author: Heidilohr
ms.topic: troubleshooting
ms.date: 08/11/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 9534425344a09bb2e2e733cde5294d08d175eb96
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2021
ms.locfileid: "111755332"
---
# <a name="troubleshoot-the-remote-desktop-client"></a>排查远程桌面客户端问题

本文描述远程桌面客户端的常见问题及其解决方法。

## <a name="remote-desktop-client-for-windows-7-or-windows-10-stops-responding-or-cannot-be-opened"></a>适用于 Windows 7 或 Windows 10 的远程桌面客户端停止响应或无法打开

从版本 1.2.790 开始，可以从“关于”页或使用命令重置用户数据。

使用以下命令可从所有工作区中删除用户数据、还原默认设置以及取消订阅。

```cmd
msrdcw.exe /reset [/f]
```

如果使用的是早期版本的远程桌面客户端，我们建议你卸载然后重新安装客户端。

## <a name="web-client-wont-open"></a>Web 客户端无法打开

首先，通过在浏览器中打开另一个网站（例如 [www.bing.com](https://www.bing.com)）来测试 Internet 连接。

使用 nslookup 确认 DNS 可以解析 FQDN：

```cmd
nslookup rdweb.wvd.microsoft.com
```

尝试与另一客户端（例如适用于 Windows 7 或 Windows 10 的远程桌面客户端）进行连接，并检查是否可以打开 Web 客户端。

### <a name="cant-open-other-websites-while-connected-to-the-web-client"></a>连接到 Web 客户端后无法打开其他网站

如果在连接到 Web 客户端后无法打开其他网站，原因可能是出现了网络连接问题或网络中断。 我们建议与网络支持人员联系。

### <a name="nslookup-cant-resolve-the-name"></a>nslookup 无法解析名称

如果 nslookup 无法解析名称，原因可能是出现了网络连接问题或网络中断。 我们建议与网络支持人员联系。

### <a name="your-client-cant-connect-but-other-clients-on-your-network-can-connect"></a>你的客户端无法连接，但网络上的其他客户端可以连接

如果在你使用 Web 客户端时浏览器时而出现问题，请按照以下说明进行故障排除：

1. 重新启动浏览器。
2. 清除浏览器 Cookie。 请参阅[如何删除 Internet Explorer 中的 Cookie 文件](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer)。
3. 清除浏览器缓存。 请参阅[清除浏览器的缓存](https://binged.it/2RKyfdU)。
4. 以私密模式打开浏览器。

## <a name="client-doesnt-show-my-resources"></a>客户端不显示我的资源

首先请检查所用的 Azure Active Directory 帐户。 如果你已经登录，但登录时使用的 Azure Active Directory 帐户不同于你要用于 Azure 虚拟桌面的帐户，你应该注销，或者使用专用浏览器窗口。

如果使用的是 Azure 虚拟桌面（经典），请使用[此文](./virtual-desktop-fall-2019/connect-web-2019.md)中的 Web 客户端链接连接到资源。

如果这不起作用，请确保你的应用组已与某个工作区相关联。

## <a name="web-client-stops-responding-or-disconnects"></a>Web 客户端停止响应或断开连接

尝试使用另一个浏览器或客户端进行连接。

### <a name="other-browsers-and-clients-also-malfunction-or-fail-to-open"></a>其他浏览器和客户端也不正常或无法打开

如果在改用其他浏览器后问题依旧出现，则问题可能不在浏览器上，而是与网络相关。 我们建议与网络支持人员联系。

## <a name="web-client-keeps-prompting-for-credentials"></a>Web 客户端不断提示输入凭据

如果 Web 客户端不断提示输入凭据，请按照以下说明进行操作：

1. 确认 Web 客户端 URL 正确。
2. 确认所用的凭据适用于已绑定到该 URL 的 Azure 虚拟桌面环境。
3. 清除浏览器 Cookie。 有关详细信息，请参阅[如何删除 Internet Explorer 中的 Cookie 文件](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer)。
4. 清除浏览器缓存。 有关详细信息，请参阅[清除浏览器的缓存](https://binged.it/2RKyfdU)。
5. 以隐密模式打开浏览器。

## <a name="windows-client-blocks-azure-virtual-desktop-classic-feed"></a>Windows 客户端阻止 Azure 虚拟桌面（经典）源

如果 Windows 客户端源不显示 Azure 虚拟桌面（经典）应用，请按照以下说明进行操作：

1. 检查条件访问策略是否包含与 Azure 虚拟桌面（经典）关联的应用 ID。
2. 检查条件访问策略是否阻止除 Azure 虚拟桌面（经典）应用 ID 访问以外的其他所有访问。 如果是这样，则需要将应用 ID 9cdead84-a844-4324-93f2-b2e6bb768d07 添加到策略，以允许客户端发现源。

如果在列表中找不到应用 ID 9cdead84-a844-4324-93f2-b2e6bb768d07，则需要注册 Azure 虚拟桌面资源提供程序。 若要注册资源提供程序：

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
