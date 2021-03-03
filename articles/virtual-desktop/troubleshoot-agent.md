---
title: 排查 Windows 虚拟桌面代理问题-Azure
description: 如何解决常见的代理和连接问题。
author: Sefriend
ms.topic: troubleshooting
ms.date: 12/16/2020
ms.author: sefriend
manager: clarkn
ms.openlocfilehash: b0fc5bd16aaa455ce3f6d634ce35e9a389a6f13b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101732575"
---
# <a name="troubleshoot-common-windows-virtual-desktop-agent-issues"></a>排查常见的 Windows 虚拟桌面代理问题

由于多个因素，Windows 虚拟桌面代理可能会导致连接问题：
   - 代理停止服务时出现错误。
   - 更新问题。
   - 在代理安装过程中安装的问题会破坏到会话主机的连接。

本文将指导你完成这些常见方案的解决方案，以及如何解决连接问题。

>[!NOTE]
>为了排查与会话连接和 Windows 虚拟桌面代理相关的问题，我们建议你查看 **事件查看器**  >  **Windows 日志**"  >  **应用程序** 中的事件日志。 查找具有以下源之一的事件，以确定问题：
>
>- WVD-Agent
>- WVD-代理-更新程序
>- RDAgentBootLoader
>- MsiInstaller

## <a name="error-the-rdagentbootloader-andor-remote-desktop-agent-loader-has-stopped-running"></a>错误： RDAgentBootLoader 和/或远程桌面代理加载程序已停止运行

如果你看到以下任何问题，这意味着加载代理的启动加载程序无法正确安装代理，并且代理服务未运行：
- **RDAgentBootLoader** 已停止或未运行。
- **远程桌面代理加载程序** 没有状态。

若要解决此问题，请启动 RDAgent 启动加载程序：

1. 在 "服务" 窗口中，右键单击 " **远程桌面代理加载程序**"。
2. 选择“开始”。 如果此选项为你灰显，则你没有管理员权限，将需要使其启动服务。
3. 等待10秒，然后右键单击 " **远程桌面代理加载程序**"。
4. 选择“刷新”。
5. 如果服务在启动并刷新后停止，则可能是注册失败。 有关详细信息，请参阅 [INVALID_REGISTRATION_TOKEN](#error-invalid_registration_token)。

## <a name="error-invalid_registration_token"></a>错误： INVALID_REGISTRATION_TOKEN

请参阅 **事件查看器**  >  **Windows 日志**"  >  **应用程序**。 如果你看到 ID 为3277的事件，则表示说明中的 **INVALID_REGISTRATION_TOKEN** ，你未将其识别为有效的注册令牌。

若要解决此问题，请创建有效的注册令牌：

1. 若要创建新的注册令牌，请按照为 [VM 生成新的注册密钥](#step-3-generate-a-new-registration-key-for-the-vm) 部分中的步骤进行操作。
2. 打开注册表编辑器。 
3. 请参阅 **HKEY_LOCAL_MACHINE**  >  **SOFTWARE**  >  **Microsoft**  >  **RDInfraAgent**。
4. 选择 **IsRegistered**。 
5. 在 " **值数据：** " 输入框中，键入 **0** ，然后选择 **"确定"**。 
6. 选择 **RegistrationToken**。 
7. 在 " **值数据：** " 输入框中，粘贴步骤1中的注册令牌。 

   > [!div class="mx-imgBorder"]
   > ![IsRegistered 0 的屏幕截图](media/isregistered-token.png)

8. 作为管理员打开命令提示。
9. 输入 **net Stop RDAgentBootLoader**。 
10. 输入 **net Start RDAgentBootLoader**。 
11. 打开注册表编辑器。
12. 请参阅 **HKEY_LOCAL_MACHINE**  >  **SOFTWARE**  >  **Microsoft**  >  **RDInfraAgent**。
13. 验证 **IsRegistered** 设置为1，并且 **RegistrationToken** 的数据列中没有任何内容。 

   > [!div class="mx-imgBorder"]
   > ![IsRegistered 1 的屏幕截图](media/isregistered-registry.png)

## <a name="error-agent-cannot-connect-to-broker-with-invalid_form"></a>错误：代理无法与 INVALID_FORM 连接到 broker

请参阅 **事件查看器**  >  **Windows 日志**"  >  **应用程序**。 如果在说明中看到 ID 为3277的事件，则 INVALID_FORM 表明代理与代理之间的通信出现问题。 由于某些防火墙或 DNS 设置的原因，代理无法连接到代理，或者无法访问特定的 URL。

若要解决此问题，请检查是否可以访问 BrokerURI 和 BrokerURIGlobal：
1. 打开注册表编辑器。 
2. 请参阅 **HKEY_LOCAL_MACHINE**  >  **SOFTWARE**  >  **Microsoft**  >  **RDInfraAgent**。 
3. 记下 " **BrokerURI** " 和 " **BrokerURIGlobal**" 的值。

   > [!div class="mx-imgBorder"]
   > ![Broker uri 和 broker uri global 的屏幕截图](media/broker-uri.png)

 
4. 打开浏览器并中转到 " *\<BrokerURI\> api/运行状况*"。 
   - 请确保使用 **BrokerURI** 中步骤3的值。 在此部分的示例中，它是 <https://rdbroker-g-us-r0.wvd.microsoft.com/api/health> 。
5. 在浏览器中打开另一个选项卡，并中转到 " *\<BrokerURIGlobal\> api/运行状况*"。 
   - 请确保使用 **BrokerURIGlobal** 链接中步骤3的值。 在此部分的示例中，它是 <https://rdbroker.wvd.microsoft.com/api/health> 。
6. 如果网络未阻塞 broker 连接，则这两个页面都将成功加载，并显示一条消息，其中显示 **"RD broker 处于正常状态"** ，如以下屏幕截图所示。 

   > [!div class="mx-imgBorder"]
   > ![已成功加载代理 uri 访问的屏幕截图](media/broker-uri-web.png)

   > [!div class="mx-imgBorder"]
   > ![已成功加载的 broker 全局 uri 访问的屏幕截图](media/broker-global.png)
 

7. 如果网络正在阻止代理连接，则页面将不会加载，如以下屏幕截图中所示。 

   > [!div class="mx-imgBorder"]
   > ![加载失败的代理访问的屏幕截图](media/unsuccessful-broker-uri.png)

   > [!div class="mx-imgBorder"]
   > ![未成功加载的 broker 全局访问的屏幕截图](media/unsuccessful-broker-global.png)

8. 如果网络阻止这些 Url，则需要取消阻止所需的 Url。 有关详细信息，请参阅 [所需 URL 列表](safe-url-list.md)。
9. 如果这不能解决你的问题，请确保你没有任何具有用于阻止代理到 broker 连接的密码的组策略。 Windows 虚拟桌面使用与 [Azure 前门](../frontdoor/front-door-faq.MD#what-are-the-current-cipher-suites-supported-by-azure-front-door)相同的 TLS 1.2 密码。 有关详细信息，请参阅 [连接安全性](network-connectivity.md#connection-security)。

## <a name="error-3703"></a>错误：3703

请参阅 **事件查看器**  >  **Windows 日志**"  >  **应用程序**。 如果在说明中看到 ID 为3703的事件，其中显示了 "RD 网关 Url：无法访问"，则代理无法访问网关 Url。 若要成功连接到会话主机并允许网络流量绕过限制，必须取消阻止 [所需 Url 列表](safe-url-list.md)中的 url。 此外，请确保你的防火墙或代理设置不会阻止这些 Url。 使用 Windows 虚拟桌面需要取消阻止这些 Url。

若要解决此问题，请验证防火墙和/或 DNS 设置是否未阻止这些 Url：
1. [使用 Azure 防火墙保护 Windows 虚拟桌面部署。](../firewall/protect-windows-virtual-desktop.md)
2. 配置 [Azure 防火墙 DNS 设置](../firewall/dns-settings.md)。

## <a name="error-3019"></a>错误：3019

请参阅 **事件查看器**  >  **Windows 日志**"  >  **应用程序**。 如果看到 ID 为3019的事件，则表示代理无法访问 web 套接字传输 Url。 若要成功连接到会话主机并允许网络流量绕过这些限制，必须取消阻止所 [需 url 列表](safe-url-list.md)中列出的 url。 与 Azure 网络团队合作，确保你的防火墙、代理和 DNS 设置未阻止这些 Url。 你还可以检查网络跟踪日志，以确定正在阻止 Windows 虚拟桌面服务的位置。 如果为此特定问题打开支持请求，请确保将网络跟踪日志附加到请求。

## <a name="error-installationhealthcheckfailedexception"></a>错误： InstallationHealthCheckFailedException

请参阅 **事件查看器**  >  **Windows 日志**"  >  **应用程序**。 如果在说明中看到 ID 为3277的事件，这意味着堆栈侦听器不起作用，因为终端服务器已经切换了堆栈侦听器的注册表项。

要解决此问题：
1. 检查 [堆栈侦听器是否正常工作](#error-stack-listener-isnt-working-on-windows-10-2004-vm)。
2. 如果堆栈侦听器不起作用，请 [手动卸载并重新安装堆栈组件](#error-vms-are-stuck-in-unavailable-or-upgrading-state)。

## <a name="error-endpoint_not_found"></a>错误： ENDPOINT_NOT_FOUND

请参阅 **事件查看器**  >  **Windows 日志**"  >  **应用程序**。 如果你看到 ID 为3277的事件，则说明中显示 "ENDPOINT_NOT_FOUND"，表示代理找不到与之建立连接的终结点。 此连接问题可能是由于以下原因之一导致的：

- 主机池中没有 Vm
- 主机池中的 Vm 不处于活动状态
- 主机池中的所有 Vm 已超过最大会话限制
- 主机池中没有运行代理服务的 Vm

要解决此问题：

1. 请确保 VM 已打开且尚未从主机池中删除。
2. 请确保 VM 未超出最大会话限制。
3. 请确保 [代理服务正在运行](#error-the-rdagentbootloader-andor-remote-desktop-agent-loader-has-stopped-running) 且 [堆栈侦听器](#error-stack-listener-isnt-working-on-windows-10-2004-vm)正在运行。
4. 请确保 [代理可以连接到 broker](#error-agent-cannot-connect-to-broker-with-invalid_form)。
5. 请确保 [VM 具有有效的注册令牌](#error-invalid_registration_token)。
6. 请确保 [VM 注册令牌未过期](faq.md#how-often-should-i-turn-my-vms-on-to-prevent-registration-issues)。 

## <a name="error-installmsiexception"></a>错误： InstallMsiException

请参阅 **事件查看器**  >  **Windows 日志**"  >  **应用程序**。 如果你看到 ID 为3277的事件，则说明中显示了 **InstallMsiException** ，而你尝试安装代理时该安装程序已在运行其他应用程序，或者策略正在阻止 msiexec.exe 程序运行。

若要解决此问题，请禁用以下策略：
   - 关闭 Windows Installer  
      - 类别路径：计算机配置 \ 管理模板 \Windows 组件 \Windows 安装程序
   
>[!NOTE]
>这并不是完整的策略列表，只是我们当前意识到的那些策略。

禁用策略：
1. 作为管理员打开命令提示。
2. 输入并运行 " **services.msc**"。
3. 在弹出的 " **策略的结果集** " 窗口中，请参阅类别路径。
4. 选择策略。
5. 选择“已禁用”。 
6. 选择“应用”。   

   > [!div class="mx-imgBorder"]
   > ![策略结果集中的 Windows Installer 策略的屏幕截图](media/gpo-policy.png)

## <a name="error-win32exception"></a>错误： Win32Exception

请参阅 **事件查看器**  >  **Windows 日志**"  >  **应用程序**。 如果看到 ID 为3277的事件，则说明中显示 **InstallMsiException** ，策略正在阻止 cmd.exe 启动。 阻止此程序会阻止你运行控制台窗口，这是在代理更新时需要用于重新启动服务的内容。

若要解决此问题，请禁用以下策略：
   - 阻止访问命令提示符   
      - 类别路径：用户配置 \ 管理模板 \ 系统
    
>[!NOTE]
>这并不是完整的策略列表，只是我们当前意识到的那些策略。

禁用策略：
1. 作为管理员打开命令提示。
2. 输入并运行 " **services.msc**"。
3. 在弹出的 " **策略的结果集** " 窗口中，请参阅类别路径。
4. 选择策略。
5. 选择“已禁用”。 
6. 选择“应用”。   

## <a name="error-stack-listener-isnt-working-on-windows-10-2004-vm"></a>错误：堆栈侦听器在 Windows 10 2004 VM 上不工作

在命令提示符下运行 **qwinsta** ，并记下 **rdp-tcp** 下显示的版本号。 如果未看到 **rdp-tcp** 和 **rdp sxs** 组件显示 " **侦听** "，或者运行 **qwinsta** 后它们根本未显示，则表示存在堆栈问题。 堆栈更新与代理更新一起安装，并且在此安装发现时，Windows 虚拟桌面侦听器将不起作用。

要解决此问题：
1. 打开注册表编辑器。
2. 请参阅 **HKEY_LOCAL_MACHINE**  >  **SYSTEM**  >  **CurrentControlSet**  >  **Control**  >  **Terminal Server**  >  **WinStations**。
3. 在 **WinStations** 下，你可能会看到不同堆栈版本的多个文件夹，请选择与你在命令提示符中运行 **qwinsta** 时看到的版本信息匹配的文件夹。
4. 查找 **fReverseConnectMode** 并确保其数据值为 **1**。 另外，请确保 **fEnableWinStation** 设置为 **1**。

   > [!div class="mx-imgBorder"]
   > ![FReverseConnectMode 的屏幕截图](media/fenable-2.png)

5. 如果 **fReverseConnectMode** 未设置为 **1**，则选择 **fReverseConnectMode** 并在其值字段中输入 **1** 。 
6. 如果 **fEnableWinStation** 未设置为 **1**，则选择 **fEnableWinStation** 并在其值字段中输入 **1** 。
7. 重新启动 VM。 

>[!NOTE]
>若要一次更改多个 Vm 的 **fReverseConnectMode** 或 **fEnableWinStation** 模式，可以执行以下两项操作之一：
>
>- 从已具有工作的计算机中导出注册表项，并将其导入到需要此更改的所有其他计算机。
>- 创建 (GPO) 的常规策略对象，该对象设置需要更改的计算机的注册表项值。

7. 请参阅 **HKEY_LOCAL_MACHINE**  >  **SYSTEM**  >  **CurrentControlSet**  >  **Control**  >  **Terminal Server**  >  **ClusterSettings**。
8. 在 " **ClusterSettings**" 下，找到 " **SessionDirectoryListener** "，并确保其数据值为 " **rdp-sxs ...**"。
9. 如果 **SessionDirectoryListener** 未设置为 **rdp-sxs ...**，则需要执行 [卸载代理和启动加载](#step-1-uninstall-all-agent-boot-loader-and-stack-component-programs) 器部分中的步骤，以首先卸载代理、启动加载程序和堆栈组件，然后 [重新安装代理和启动加载程序](#step-4-reinstall-the-agent-and-boot-loader)。 这将重新安装并行堆栈。

## <a name="error-heartbeat-issue-where-users-keep-getting-disconnected-from-session-hosts"></a>错误：用户保持与会话主机断开连接的检测信号问题

如果服务器未从 Windows 虚拟桌面服务中提取检测信号，则需要更改检测信号阈值。 如果以下一个或多个方案适用，请按照本部分中的说明进行操作：

- 收到 **CheckSessionHostDomainIsReachableAsync** 错误
- 收到 **ConnectionBrokenMissedHeartbeatThresholdExceeded** 错误
- 正在接收 **ConnectionEstablished： UnexpectedNetworkDisconnect** 错误
- 用户客户端保持断开连接
- 用户保持与其会话主机断开连接

更改检测信号阈值：
1. 以管理员身份打开命令提示符。
2. 输入 **qwinsta** 命令并运行该命令。
3. 应显示两个堆栈组件： **rdp-tcp** 和 **rdp-sxs**。 
   - 根据所使用的操作系统版本， **rdp-sxs** 后面可能跟有生成号。 如果是，请确保稍后记下此号码。
4. 打开注册表编辑器。
5. 请参阅 **HKEY_LOCAL_MACHINE**  >  **SYSTEM**  >  **CurrentControlSet**  >  **Control**  >  **Terminal Server**  >  **WinStations**。
6. 在 **WinStations** 下，你可能会看到不同堆栈版本的多个文件夹。 选择与步骤3中的版本号匹配的文件夹。
7. 右键单击注册表编辑器，然后选择 "**新**  >  **DWORD (32 位) 值**，创建新的注册表 dword。 创建 DWORD 时，输入以下值：
   - HeartbeatInterval：10000
   - HeartbeatWarnCount：30 
   - HeartbeatDropCount：60 
8. 重新启动 VM。

>[!NOTE]
>如果更改检测信号阈值不能解决问题，则可能会遇到基本网络问题，需要联系 Azure 网络团队了解相关信息。

## <a name="error-downloadmsiexception"></a>错误： DownloadMsiException

请参阅 **事件查看器**  >  **Windows 日志**"  >  **应用程序**。 如果看到 ID 为3277的事件，则说明中的 **DownloadMsiException** 没有足够的磁盘空间用于 RDAgent。

若要解决此问题，请通过以下方式在磁盘上腾出空间：
   - 删除不再位于用户中的文件
   - 提高 VM 的存储容量

## <a name="error-agent-fails-to-update-with-missingmethodexception"></a>错误：代理无法更新 MissingMethodException

请参阅 **事件查看器**  >  **Windows 日志**"  >  **应用程序**。 如果在说明中看到 ID 为3389的事件，其中显示了 "找不到 MissingMethodException：方法"，这意味着 Windows 虚拟桌面代理未成功更新并还原到早期版本。 这可能是因为虚拟机上当前安装的 .NET framework 的版本号低于4.7.2。 若要解决此问题，需要按照 [.NET Framework 文档](https://support.microsoft.com/topic/microsoft-net-framework-4-7-2-offline-installer-for-windows-05a72734-2127-a15d-50cf-daf56d5faec2)中的安装说明将 .net 升级到版本4.7.2 或更高版本。


## <a name="error-vms-are-stuck-in-unavailable-or-upgrading-state"></a>错误： Vm 停滞不可用或正在升级状态

以管理员身份打开 PowerShell 窗口并运行以下 cmdlet：

```powershell
Get-AzWvdSessionHost -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname> | Select-Object *
```

如果为主机池中的会话主机或主机列出的状态始终显示 "不可用" 或 "正在升级"，则代理或堆栈未成功安装。

若要解决此问题，请重新安装并行堆栈：
1. 作为管理员打开命令提示。
2. 输入 **net Stop RDAgentBootLoader**。 
3. 转到“控制面板”   > “程序”   > “程序和功能”  。
4. 卸载最新版本的 **远程桌面服务 SxS 网络堆栈** 或  >    >  ReverseConnectListener 下 HKEY_LOCAL_MACHINE SYSTEM **CurrentControlSet**  >  **Control**  >  **Terminal Server**  >  **WinStations** 中列出的版本。
5. 以管理员身份打开控制台窗口，并切换到 " **Program Files**  >  **Microsoft RDInfra**"。
6. 选择 **SxSStack** 组件或运行 **msiexec/i SxSStack <version>** 命令来安装 msi。
8. 重新启动 VM。
9. 返回到命令提示符并运行 **qwinsta** 命令。
10. 验证步骤6中安装的 "堆栈" 组件是否旁显示 " **侦听** "。
   - 如果是这样，请在命令提示符下输入 **net Start RDAgentBootLoader** ，并重新启动 VM。
   - 如果没有，则需要 [重新注册 VM，并重新安装代理](#your-issue-isnt-listed-here-or-wasnt-resolved) 组件。

## <a name="error-connection-not-found-rdagent-does-not-have-an-active-connection-to-the-broker"></a>错误：找不到连接： RDAgent 没有到 broker 的活动连接

Vm 可能处于连接限制，因此 VM 无法接受新连接。

要解决此问题：
   - 减少最大会话限制。 这可以确保资源更均匀地分布在各个会话主机上，并将防止资源耗尽。
   - 提高 Vm 的资源容量。

## <a name="error-operating-a-pro-vm-or-other-unsupported-os"></a>错误：操作 Pro VM 或其他不受支持的操作系统

仅 Windows Enterprise 或 Windows Server Sku 支持并列堆栈，这意味着 Pro VM 这样的操作系统不受支持。 如果你没有企业或服务器 SKU，则会在你的 VM 上安装该堆栈，但不会将其激活，因此，当你在命令行中运行 **qwinsta** 时，你将看不到它。

若要解决此问题，请创建一个 Windows Enterprise 或 Windows Server 虚拟机。
1. 请参阅 " [虚拟机详细信息](create-host-pools-azure-marketplace.md#virtual-machine-details) "，然后按照步骤1-12 设置以下建议映像之一：
   - Windows 10 企业多会话版本1909
   - Windows 10 企业多会话版本 1909 + Microsoft 365 应用
   - Windows Server 2019 Datacenter
   - Windows 10 企业多会话版本2004
   - Windows 10 企业多会话版本 2004 + Microsoft 365 应用
2. 选择“查看并创建”。

## <a name="error-name_already_registered"></a>错误： NAME_ALREADY_REGISTERED

VM 的名称已经注册，可能是重复的。

要解决此问题：
1. 按照 [从主机池中删除会话主机](#step-2-remove-the-session-host-from-the-host-pool) 部分中的步骤进行操作。
2. [创建另一个 VM](expand-existing-host-pool.md#add-virtual-machines-with-the-azure-portal)。 请确保为此 VM 选择唯一的名称。
3. 中转到 [Azure 门户](https://portal.azure.com) 并打开 VM 所在的主机池的 " **概述** " 页。 
4. 打开 " **会话主机** " 选项卡并检查以确保所有会话主机都在该主机池中。
5. 等待5-10 分钟，让会话主机状态显示为 " **可用**"。

   > [!div class="mx-imgBorder"]
   > ![可用会话主机的屏幕截图](media/hostpool-portal.png)

## <a name="your-issue-isnt-listed-here-or-wasnt-resolved"></a>你的问题未在此处列出或未解决

如果在本文中找不到你的问题，或者说明没有帮助你，建议你卸载、重新安装并重新注册 Windows 虚拟桌面代理。 本部分中的说明将向你介绍如何通过以下方式向 Windows 虚拟桌面服务注册 VM：卸载所有代理、启动加载程序和堆栈组件、从主机池中删除会话主机、为 VM 生成新的注册密钥以及重新安装代理和启动加载程序。 如果以下一个或多个方案适用于你，请遵循以下说明：
- VM 停滞正在 **升级** 或 **不可用**
- 堆栈侦听器不起作用，并且你在 Windows 10 1809、1903或1904上运行
- 收到 **EXPIRED_REGISTRATION_TOKEN** 错误
- "会话主机" 列表中未显示 Vm
- 在 "服务" 窗口中看不到 **远程桌面代理加载程序**
- 在任务管理器中看不到 **RdAgentBootLoader** 组件
- 正在接收 **连接代理无法验证** 自定义映像 vm 上的设置错误
- 本文中的说明未解决你的问题

### <a name="step-1-uninstall-all-agent-boot-loader-and-stack-component-programs"></a>步骤1：卸载所有代理、启动加载程序和堆栈组件程序

在重新安装代理、启动加载程序和堆栈之前，必须从 VM 卸载任何现有的组件程序。 卸载所有代理、启动加载程序和堆栈组件程序：
1. 以管理员身份登录到 VM。 
2. 转到“控制面板”   > “程序”   > “程序和功能”  。
3. 删除以下程序：
   - 远程桌面代理启动加载程序
   - 远程桌面服务基础结构代理
   - 远程桌面服务基础结构 Geneva 代理
   - 远程桌面服务 SxS 网络堆栈
   
>[!NOTE]
>你可能会看到这些程序的多个实例。 请确保删除所有这些文件。

   > [!div class="mx-imgBorder"]
   > ![卸载程序的屏幕截图](media/uninstall-program.png)

### <a name="step-2-remove-the-session-host-from-the-host-pool"></a>步骤2：从主机池中删除会话主机

从主机池中删除会话主机时，该会话主机不再注册到该主机池。 这将用作会话主机注册的重置。 若要从主机池中删除会话主机，请执行以下操作：
1. 在 [Azure 门户](https://portal.azure.com)中，请参阅 VM 所在的主机池的 "**概述**" 页。 
2. 请访问 " **会话主机** " 选项卡，以查看该主机池中的所有会话主机的列表。
3. 查看会话主机列表，并选择要删除的 VM。
4. 选择“删除” 。  

   > [!div class="mx-imgBorder"]
   > ![从主机池删除 VM 的屏幕截图](media/remove-sh.png)

### <a name="step-3-generate-a-new-registration-key-for-the-vm"></a>步骤3：为 VM 生成新的注册密钥

必须生成新的注册密钥，用于将 VM 重新注册到主机池和服务。 为 VM 生成新的注册密钥：
1. 打开 [Azure 门户](https://portal.azure.com) ，然后前往要编辑的 VM 的主机池的 " **概述** " 页。
2. 选择 " **注册密钥**"。

   > [!div class="mx-imgBorder"]
   > ![门户中注册密钥的屏幕截图](media/reg-key.png)

3. 打开 " **注册密钥** " 选项卡，然后选择 " **生成新密钥**"。
4. 输入到期日期，然后选择 **"确定"**。  

>[!NOTE]
>过期日期不能早于1小时，也不能超过27天的生成时间和日期。 我们强烈建议你将到期日期设置为27天的最大值。

5. 将新生成的密钥复制到剪贴板。 稍后需要用到此密钥。

### <a name="step-4-reinstall-the-agent-and-boot-loader"></a>步骤4：重新安装代理和启动加载程序

通过重新安装最新版本的代理和启动加载程序，可以自动安装并排堆栈和 Geneva 监视代理。 若要重新安装代理和启动加载程序：
1. 以管理员身份登录到 VM，并为部署使用正确的代理安装程序版本，具体取决于你的 VM 运行的 Windows 版本。 如果有 Windows 10 VM，请按照 [注册虚拟机](create-host-pools-powershell.md#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool) 中的说明下载 **Windows 虚拟桌面代理** 和 **Windows 虚拟桌面代理加载程序**。 如果有 Windows 7 VM，请按照 [注册虚拟机](deploy-windows-7-virtual-machine.md#configure-a-windows-7-virtual-machine) 中的步骤13-14 下载 **Windows 虚拟桌面代理** 和 **windows 虚拟桌面代理程序管理器**。

   > [!div class="mx-imgBorder"]
   > ![代理和引导程序下载页的屏幕截图](media/download-agent.png)

2. 右键单击下载的代理和启动加载程序安装程序。
3. 选择“属性”。
4. 选择“取消阻止”。
5. 选择“确定”  。
6. 运行代理安装程序。
7. 当安装程序要求你提供注册令牌时，请从剪贴板中粘贴注册密钥。 

   > [!div class="mx-imgBorder"]
   > ![粘贴的注册令牌的屏幕截图](media/pasted-agent-token.png)

8. 运行启动加载器安装程序。
9. 重新启动 VM。 
10. 中转到 [Azure 门户](https://portal.azure.com) 并打开 VM 所属的主机池的 " **概述** " 页。
11. 请访问 " **会话主机** " 选项卡，以查看该主机池中的所有会话主机的列表。
12. 现在，应会看到在主机池中注册的会话主机，状态为 " **可用**"。 

   > [!div class="mx-imgBorder"]
   > ![可用会话主机的屏幕截图](media/hostpool-portal.png)

## <a name="next-steps"></a>后续步骤

如果此问题仍然存在，请创建支持案例，并包括有关你遇到的问题的详细信息，以及尝试解决此问题所采取的任何操作。 以下列表包含可用于排查 Windows 虚拟桌面部署中的问题的其他资源。

- 如需简要了解如何排查 Windows 虚拟桌面问题和跟踪升级，请参阅[故障排除概述、反馈和支持](troubleshoot-set-up-overview.md)。
- 若要在 Windows 虚拟桌面环境中创建主机池时排查问题，请参阅 [环境和主机池创建](troubleshoot-set-up-issues.md)。
- 若要排查在 Windows 虚拟桌面中配置虚拟机 (VM) 时遇到的问题，请参阅[会话主机虚拟机配置](troubleshoot-vm-configuration.md)。
- 若要解决 Windows 虚拟桌面客户端连接问题，请参阅 [Windows 虚拟桌面服务连接](troubleshoot-service-connection.md)。
- 若要解决远程桌面客户端的问题，请参阅 [排查远程桌面客户端](troubleshoot-client.md)问题。
- 若要排查将 PowerShell 与 Windows 虚拟桌面结合使用时遇到的问题，请参阅 [Windows 虚拟桌面 PowerShell](troubleshoot-powershell.md)。
- 若要了解有关该服务的详细信息，请参阅 [Windows 虚拟桌面环境](environment-setup.md)。
- 若要完成故障排除教程，请参阅[教程：排查资源管理器模板部署问题](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)。
- 若要了解审核操作，请参阅[使用 Resource Manager 执行审核操作](../azure-resource-manager/management/view-activity-logs.md)。
- 若要了解部署期间为确定错误需要执行哪些操作，请参阅[查看部署操作](../azure-resource-manager/templates/deployment-history.md)。
