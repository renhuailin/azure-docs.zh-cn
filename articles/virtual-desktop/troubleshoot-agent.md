---
title: 排查 Azure 虚拟桌面代理问题 - Azure
description: 如何解决常见的代理和连接问题。
author: Sefriend
ms.topic: troubleshooting
ms.date: 12/16/2020
ms.author: sefriend
manager: clarkn
ms.openlocfilehash: 31a65c31558940ba7e39e21c8b6e33ffa8e7c9b9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128633650"
---
# <a name="troubleshoot-common-azure-virtual-desktop-agent-issues"></a>排查常见的 Azure 虚拟桌面代理问题

由于多种因素，Azure 虚拟桌面代理可能会导致连接问题：
   - 中转站上出现了错误，导致代理停止服务。
   - 与更新相关的问题。
   - 在代理安装过程中与安装相关的问题会破坏到会话主机的连接。

本文将指导你了解这些常见情形的解决方案以及如何解决连接问题。

>[!NOTE]
>为了排查与会话连接和 Azure 虚拟桌面代理相关的问题，建议在“事件查看器” > “Windows 日志” > “应用程序”中查看事件日志  。 查找具有以下某一种源的事件，以确定问题：
>
>- WVD-Agent
>- WVD-Agent-Updater
>- RDAgentBootLoader
>- MsiInstaller

## <a name="error-the-rdagentbootloader-andor-remote-desktop-agent-loader-has-stopped-running"></a>错误：RDAgentBootLoader 和/或远程桌面代理加载程序已停止运行

如果你看到以下任何问题，则意味着加载该代理的启动加载程序未能正确安装该代理，并且代理服务未运行：
- RDAgentBootLoader 已停止或未运行。
- 远程桌面代理加载程序没有状态。

若要解决此问题，请启动 RDAgent 启动加载程序：

1. 在“服务”窗口中，右键单击“远程桌面代理加载程序”。
2. 选择“启动”。 如果你看到此选项为灰显，则表明你没有管理员权限，需要获取这些权限才能启动该服务。
3. 请等待 10 秒，然后右键单击“远程桌面代理加载程序”。
4. 选择“刷新”。
5. 如果服务在启动并刷新后停止，则可能出现注册失败。 有关详细信息，请参阅 [INVALID_REGISTRATION_TOKEN](#error-invalid_registration_token)。

## <a name="error-invalid_registration_token"></a>错误：INVALID_REGISTRATION_TOKEN

请参阅“事件查看器” > “Windows 日志” > “应用程序”  。 如果看到 ID 为 3277 的事件在说明中显示 INVALID_REGISTRATION_TOKEN，则表明你具有的注册令牌未被识别为有效。

若要解决此问题，请创建有效的注册令牌：

1. 若要创建新注册令牌，请按照为 [VM 生成新的注册密钥](#step-3-generate-a-new-registration-key-for-the-vm)部分中的步骤进行操作。
2. 打开注册表编辑器。 
3. 转到“HKEY_LOCAL_MACHINE” > “SOFTWARE” > “Microsoft” > “RDInfraAgent”   。
4. 选择“IsRegistered”。 
5. 在“值数据:”输入框中，键入“0”，然后选择“确定”  。 
6. 选择“RegistrationToken”。 
7. 在“值数据:”输入框中，粘贴步骤 1 中的注册令牌。 

   > [!div class="mx-imgBorder"]
   > ![IsRegistered 0 的屏幕截图](media/isregistered-token.png)

8. 以管理员身份打开命令提示符。
9. 输入“net Stop RDAgentBootLoader”。 
10. 输入“net start RDAgentBootLoader”。 
11. 打开注册表编辑器。
12. 转到“HKEY_LOCAL_MACHINE” > “SOFTWARE” > “Microsoft” > “RDInfraAgent”   。
13. 验证是否“IsRegistered”已设置为 1 并且 RegistrationToken 的数据列中没有任何内容 。 

   > [!div class="mx-imgBorder"]
   > ![IsRegistered 1 的屏幕截图](media/isregistered-registry.png)

## <a name="error-agent-cannot-connect-to-broker-with-invalid_form"></a>错误：代理无法连接到中转站，出现 INVALID_FORM

请参阅“事件查看器” > “Windows 日志” > “应用程序”  。 如果看到 ID 为 3277 的事件在说明中显示“INVALID_FORM”，则表明代理与中转站之间的通信有问题。 由于某些防火墙或 DNS 设置，代理无法连接到中转站，或者无法访问特定的 URL。

若要解决此问题，请检查是否可以访问 BrokerURI 和 BrokerURIGlobal：
1. 打开注册表编辑器。 
2. 转到“HKEY_LOCAL_MACHINE” > “SOFTWARE” > “Microsoft” > “RDInfraAgent”   。 
3. 记下“BrokerURI”和“BrokerURIGlobal”的值 。

   > [!div class="mx-imgBorder"]
   > ![Broker uri 和 broker uri global 的屏幕截图](media/broker-uri.png)

 
4. 打开浏览器并转到 \<BrokerURI\>api/health。 
   - 请确保在“BrokerURI”中使用步骤 3 的值。 在这部分的示例中，它将为 <https://rdbroker-g-us-r0.wvd.microsoft.com/api/health>。
5. 在浏览器中打开另一个标签页，并转到 \<BrokerURIGlobal\>api/health。 
   - 请确保在“BrokerURIGlobal”中使用步骤 3 的值。 在这部分的示例中，它将为 <https://rdbroker.wvd.microsoft.com/api/health>。
6. 如果网络未阻止中转站连接，则两个页面都将成功加载并显示消息“RD 中转站处于正常状态”，如以下屏幕截图所示。 

   > [!div class="mx-imgBorder"]
   > ![成功加载的中转站 URI 访问的屏幕截图](media/broker-uri-web.png)

   > [!div class="mx-imgBorder"]
   > ![成功加载的中转站全局 URI 访问的屏幕截图](media/broker-global.png)
 

7. 如果网络阻止中转站连接，则页面将不会加载，如以下屏幕截图中所示。 

   > [!div class="mx-imgBorder"]
   > ![未成功加载的中转站访问的屏幕截图](media/unsuccessful-broker-uri.png)

   > [!div class="mx-imgBorder"]
   > ![未成功加载的中转站全局访问的屏幕截图](media/unsuccessful-broker-global.png)

8. 如果网络阻止这些 URL，则需要取消阻止所需的 URL。 有关详细信息，请参阅[所需 URL 的列表](safe-url-list.md)。
9. 如果这样不解决问题，请确保你没有任何组策略使用了阻止代理连接到中转站的密码。 Azure 虚拟桌面使用与 [Azure Front Door](../frontdoor/front-door-faq.yml#what-are-the-current-cipher-suites-supported-by-azure-front-door-) 相同的 TLS 1.2 密码。 有关详细信息，请参阅[连接安全性](network-connectivity.md#connection-security)。

## <a name="error-3703"></a>错误：3703

请参阅“事件查看器” > “Windows 日志” > “应用程序”  。 如果看到 ID 为 3703 的事件在说明中显示“RD 网关 URL：无法访问”，则表明代理无法访问网关 URL。 若要成功连接到会话主机，并允许到这些终结点的网络流量绕过限制，则必须取消阻止[所需 URL 的列表](safe-url-list.md)中的 URL。 此外，请确保防火墙或代理设置不会阻止这些 URL。 使用 Azure 虚拟桌面需要取消阻止这些 URL。

若要解决此问题，请验证防火墙和/或 DNS 设置是否未阻止这些 URL：
1. [使用 Azure 防火墙保护 Azure 虚拟桌面部署](../firewall/protect-azure-virtual-desktop.md)。
2. 配置 [Azure 防火墙 DNS 设置](../firewall/dns-settings.md)。

## <a name="error-3019"></a>错误：3019

请参阅“事件查看器” > “Windows 日志” > “应用程序”  。 如果看到 ID 为 3019 的事件，则表明代理无法访问 Web 套接字传输 URL。 若要成功连接到会话主机，并允许网络流量绕过这些限制，则必须取消阻止[所需 URL 的列表](safe-url-list.md)中列出的 URL。 请与 Azure 网络团队合作，以确保防火墙、代理和 DNS 设置未阻止这些 URL。 还可以检查网络跟踪日志，以确定正在阻止 Azure 虚拟桌面服务的位置。 如果为此特定问题提出支持请求，请确保将网络跟踪日志附加到该请求。

## <a name="error-installationhealthcheckfailedexception"></a>错误：InstallationHealthCheckFailedException

请参阅“事件查看器” > “Windows 日志” > “应用程序”  。 如果看到 ID 为 3277 的事件在说明中显示“InstallationHealthCheckFailedException”，则表明堆栈侦听器未起作用，因为终端服务器已经切换了该堆栈侦听器的注册表项。

若要解决此问题，请执行下列操作：
1. 检查[堆栈侦听器是否正常工作](#error-stack-listener-isnt-working-on-windows-10-2004-vm)。
2. 如果堆栈侦听器不起作用，请[手动卸载并重新安装堆栈组件](#error-vms-are-stuck-in-unavailable-or-upgrading-state)。

## <a name="error-endpoint_not_found"></a>错误：ENDPOINT_NOT_FOUND

请参阅“事件查看器” > “Windows 日志” > “应用程序”  。 如果看到 ID 为 3277 的事件在说明中显示“ENDPOINT_NOT_FOUND”，则表明中转站找不到要与之建立连接的终结点。 发生这种情况的原因可能会是以下某一种：

- 主机池中没有 VM
- 主机池中的 VM 未处于活动状态
- 主机池中的所有 VM 都已超过最大会话限制
- 主机池中的 VM 中都没有运行代理服务

若要解决此问题，请执行下列操作：

1. 确保 VM 已开机且未从主机池中删除。
2. 确保 VM 未超出最大会话限制。
3. 确保[代理服务正在运行](#error-the-rdagentbootloader-andor-remote-desktop-agent-loader-has-stopped-running)并且[堆栈侦听器运行正常](#error-stack-listener-isnt-working-on-windows-10-2004-vm)。
4. 确保[代理可以连接到中转站](#error-agent-cannot-connect-to-broker-with-invalid_form)。
5. 确保 [VM 具有有效注册令牌](#error-invalid_registration_token)。
6. 确保 [VM 注册令牌未过期](/azure/virtual-desktop/faq#how-often-should-i-turn-my-vms-on-to-prevent-registration-issues)。 

## <a name="error-installmsiexception"></a>错误：InstallMsiException

请参阅“事件查看器” > “Windows 日志” > “应用程序”  。 如果看到 ID 为 3277 的事件在说明中显示 InstallMsiException，则表明该安装程序在你尝试安装代理时已在为另一应用程序运行，或者，某个策略正在阻止 msiexec.exe 程序运行。

若要解决此问题，请禁用以下策略：
   - 关闭 Windows Installer  
      - 类别路径：计算机配置\管理员模板\Windows 组件\Windows Installer
   
>[!NOTE]
>这并不是完整的策略列表，只是我们目前知道的策略。

若要禁用策略，请执行以下操作：
1. 以管理员身份打开命令提示符。
2. 输入并运行“rsop.msc”。
3. 在弹出的“策略的结果集”窗口中，转到类别路径。
4. 选择策略。
5. 选择“已禁用”。 
6. 选择“应用”。   

   > [!div class="mx-imgBorder"]
   > ![策略结果集中的 Windows Installer 策略的屏幕截图](media/gpo-policy.png)

## <a name="error-win32exception"></a>错误：Win32Exception

请参阅“事件查看器” > “Windows 日志” > “应用程序”  。 如果看到 ID 为 3277 的事件在说明中显示“InstallMsiException”，则表明某个策略正在阻止 cmd.exe 启动。 阻止此程序会阻止运行控制台窗口，而只要代理更新就需要使用控制台窗口来重启服务。

若要解决此问题，请禁用以下策略：
   - 阻止访问命令提示符   
      - 类别路径：用户配置\管理员模板\系统
    
>[!NOTE]
>这并不是完整的策略列表，只是我们目前知道的策略。

若要禁用策略，请执行以下操作：
1. 以管理员身份打开命令提示符。
2. 输入并运行“rsop.msc”。
3. 在弹出的“策略的结果集”窗口中，转到类别路径。
4. 选择策略。
5. 选择“已禁用”。 
6. 选择“应用”。   

## <a name="error-stack-listener-isnt-working-on-windows-10-2004-vm"></a>错误：堆栈侦听器在 Windows 10 2004 VM 上不起作用

在命令提示符下运行“qwinsta”，并记下“rdp-sxs”旁边显示的版本号 。 如果在运行 qwinsta 后未看到 rdp-tcp 和 rdp sxs 组件在它们旁边显示“侦听”，或者它们完全不出现，则表明存在堆栈问题   。 堆栈更新会与代理更新一起安装，并且在此安装完成时，Azure 虚拟桌面侦听器将不起作用。

要解决此问题：
1. 打开注册表编辑器。
2. 转到“HKEY_LOCAL_MACHINE” > “SYSTEM” > “CurrentControlSet” > “Control” > “Terminal Server” > “WinStations”     。
3. 在“WinStations”下，你可能会看到不同堆栈版本的多个文件夹，请选择与你在命令提示符中运行 qwinsta 时看到的版本信息匹配的文件夹 。
4. 查找“fReverseConnectMode”并确保其数据值为“1” 。 另外，请确保“fEnableWinStation”设置为“1” 。

   > [!div class="mx-imgBorder"]
   > ![fReverseConnectMode 的屏幕截图](media/fenable-2.png)

5. 如果“fReverseConnectMode”未设置为 1，请选择“fReverseConnectMode”并在其值字段中输入“1”   。 
6. 如果“fEnableWinStation”未设置为 1，请选择“fEnableWinStation”并在其值字段中输入“1”   。
7. 重新启动 VM。 

>[!NOTE]
>若要一次更改多个 VM 的 fReverseConnectMode 或 fEnableWinStation 模式，可以执行以下两项操作之一 ：
>
>- 从已正常运行的计算机中导出注册表项，并将其导入到需要此更改的所有其他计算机。
>- 创建组策略对象 (GPO) 以便为需要更改的计算机设置注册表项值。

7. 转到“HKEY_LOCAL_MACHINE” > “SYSTEM” > “CurrentControlSet” > “Control” > “Terminal Server” > “ClusterSettings”     。
8. 在“ClusterSettings”下，找到“SessionDirectoryListener”，并确保其数据值为“rdp-sxs...”  。
9. 如果“SessionDirectoryListener”未设置为“rdp-sxs...”，则需要执行[卸载代理和启动加载器](#step-1-uninstall-all-agent-boot-loader-and-stack-component-programs)部分中的步骤，先卸载代理、启动加载程序和堆栈组件，然后[重新安装代理和启动加载程序](#step-4-reinstall-the-agent-and-boot-loader) 。 这样将重新安装并行堆栈。

## <a name="error-downloadmsiexception"></a>错误：DownloadMsiException

请参阅“事件查看器” > “Windows 日志” > “应用程序”  。 如果看到 ID 为 3277 的事件在说明中显示“DownloadMsiException”，则表明没有足够的磁盘空间用于 RDAgent。

若要解决此问题，请通过以下方式在磁盘上腾出空间：
   - 删除不再使用的文件
   - 提高 VM 的存储容量

## <a name="error-agent-fails-to-update-with-missingmethodexception"></a>错误：代理无法更新，出现 MissingMethodException

请参阅“事件查看器” > “Windows 日志” > “应用程序”  。 如果看到 ID 为 3389 的事件在说明中显示“MissingMethodException: 找不到方法”，则表明 Azure 虚拟桌面代理未成功更新，并且还原到了某个早期版本。 这可能是因为 VM 上目前安装的 .NET framework 的版本号低于 4.7.2。 若要解决此问题，需要通过按照 [.NET Framework 文档](https://support.microsoft.com/topic/microsoft-net-framework-4-7-2-offline-installer-for-windows-05a72734-2127-a15d-50cf-daf56d5faec2)中的安装说明操作来将 .NET 升级到版本 4.7.2 或更高版本。


## <a name="error-vms-are-stuck-in-unavailable-or-upgrading-state"></a>错误：VM 停滞，状态为“不可用”或“正在升级”

以管理员身份打开 PowerShell 窗口并运行以下 cmdlet：

```powershell
Get-AzWvdSessionHost -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname> | Select-Object *
```

如果为会话主机或主机池中的主机列出的状态始终显示“不可用”或“正在升级”，则表明代理或堆栈未成功安装。

若要解决此问题，请重新安装并行堆栈：
1. 以管理员身份打开命令提示符。
2. 输入“net Stop RDAgentBootLoader”。 
3. 转到“控制面板”   > “程序”   > “程序和功能”  。
4. 卸载远程桌面服务 SxS 网络堆栈的最新版本或“HKEY_LOCAL_MACHINE” > “SYSTEM” > “CurrentControlSet” > “Control” > “Terminal Server” > “WinStations”中“ReverseConnectListener”下列出的版本       。
5. 以管理员身份打开控制台窗口并转到“Program Files” > “Microsoft RDInfra” 。
6. 选择“SxSStack”组件或运行 `msiexec /i SxsStack-<version>.msi` 命令来安装 MSI 。
8. 重新启动 VM。
9. 返回到命令提示并运行“qwinsta”命令。
10. 验证步骤 6 中安装的堆栈组件的旁边是否显示“侦听”。
   - 如果显示，请在命令提示符下输入“net start RDAgentBootLoader”，并重启 VM。
   - 如果未显示，则需要[重新注册 VM 并重新安装代理](#your-issue-isnt-listed-here-or-wasnt-resolved)组件。

## <a name="error-connection-not-found-rdagent-does-not-have-an-active-connection-to-the-broker"></a>错误：找不到连接：RDAgent 没有到中转站的活动连接

VM 可能遇到了连接限制，因此该 VM 无法接受新连接。

若要解决此问题，请执行下列操作：
   - 更改最大会话限制。 这样会确保资源更均匀地分布在会话主机中，并将防止资源耗尽。
   - 增加 VM 的资源容量。

## <a name="error-operating-a-pro-vm-or-other-unsupported-os"></a>错误：正在操作 Pro VM 或其他不受支持的 OS

只有 Windows 企业版或 Windows Server SKU 支持并行堆栈，这就意味着 Pro VM 等操作系统不支持。 如果没有企业版或 Server SKU，该堆栈将会安装在 VM 上但不会激活，因此，在命令行中运行 qwinsta 时，该堆栈不会出现。

若要解决此问题，请创建 Windows 企业版或 Windows Server 的 VM。
1. 请转到[虚拟机详细信息](create-host-pools-azure-marketplace.md#virtual-machine-details)，然后按照步骤 1-12 操作，以设置以下建议的映像之一：
   - Windows 10 企业版多会话，版本 1909
   - Windows 10 企业版多会话，版本 1909 + Microsoft 365 应用
   - Windows Server 2019 Datacenter
   - Windows 10 企业版多会话，版本 2004
   - Windows 10 企业版多会话，版本 2004 + Microsoft 365 应用
2. 选择“查看并创建”。

## <a name="error-name_already_registered"></a>错误：NAME_ALREADY_REGISTERED

VM 的名称已经注册，可能是重复的。

若要解决此问题，请执行下列操作：
1. 按照[从主机池删除会话主机](#step-2-remove-the-session-host-from-the-host-pool)部分中的步骤进行操作。
2. [再创建一个 VM](expand-existing-host-pool.md#add-virtual-machines-with-the-azure-portal)。 请确保为此 VM 选择独一无二的名称。
3. 转到 [Azure 门户](https://portal.azure.com)并打开 VM 所在的主机池的“概述”页。 
4. 打开“会话主机”选项卡，并检查以确保所有会话主机都在该主机池中。
5. 等待 5-10 分钟，让会话主机状态显示为“可用”。

   > [!div class="mx-imgBorder"]
   > ![可用会话主机的屏幕截图](media/hostpool-portal.png)

## <a name="your-issue-isnt-listed-here-or-wasnt-resolved"></a>你的问题未在此处列出或未解决

如果在本文中找不到你的问题，或者说明内容没有帮助，建议卸载、重新安装并重新注册 Azure 虚拟桌面代理。 本部分中的说明将介绍如何通过以下方式将 VM 注册到 Azure 虚拟桌面服务：卸载所有代理、启动加载程序和堆栈组件，从主机池删除会话主机，为 VM 生成新注册密钥，以及重新安装代理和启动加载程序。 如果你遇到以下一个或多个场景，请按以下说明操作：
- VM 停滞，状态为“正在升级”或“不可用” 
- 堆栈侦听器不起作用，并且是在 Windows 10 1809、1903 或 1909 上运行
- 收到 EXPIRED_REGISTRATION_TOKEN 错误
- 未看到 VM 出现在会话主机列表中
- 在“服务”窗口中看不到“远程桌面代理加载程序”
- 在任务管理器中看不到“RdAgentBootLoader”组件
- 在自定义映像 VM 上收到“连接中转站无法验证设置”错误
- 本文中的说明未解决你的问题

### <a name="step-1-uninstall-all-agent-boot-loader-and-stack-component-programs"></a>步骤 1：卸载所有代理、启动加载程序和堆栈组件程序

在重新安装代理、启动加载程序和堆栈之前，必须先从 VM 卸载任何现有的组件程序。 若要卸载所有代理、启动加载程序和堆栈组件程序，请执行以下操作：
1. 以管理员身份登录到 VM。 
2. 转到“控制面板”   > “程序”   > “程序和功能”  。
3. 删除以下程序：
   - 远程桌面代理启动加载程序
   - 远程桌面服务基础结构代理
   - 远程桌面服务基础结构日内瓦代理
   - 远程桌面服务 SxS 网络堆栈
   
>[!NOTE]
>你可能会看到这些程序的多个实例。 请确保将它们全部删除。

   > [!div class="mx-imgBorder"]
   > ![卸载程序的屏幕截图](media/uninstall-program.png)

### <a name="step-2-remove-the-session-host-from-the-host-pool"></a>步骤 2：从主机池删除会话主机

在从主机池中删除会话主机时，该会话主机不再注册到该主机池。 此操作相当于重置会话主机注册。 若要从主机池删除会话主机，请执行以下操作：
1. 在 **Azure 门户** 中转到 VM 所在主机池的 [概述](https://portal.azure.com) 页。 
2. 转到“会话主机”选项卡，以查看该主机池中所有会话主机的列表。
3. 查看会话主机的列表，并选择要删除的 VM。
4. 选择“删除”。  

   > [!div class="mx-imgBorder"]
   > ![从主机池删除 VM 的屏幕截图](media/remove-sh.png)

### <a name="step-3-generate-a-new-registration-key-for-the-vm"></a>步骤 3：为 VM 生成新的注册密钥

必须生成用于将 VM 重新注册到主机池和服务的新注册密钥。 若要为 VM 生成新注册密钥，请执行以下操作：
1. 打开 [Azure 门户](https://portal.azure.com)，然后转到要编辑的 VM 的主机池的“概述”页。
2. 选择“注册密钥”。

   > [!div class="mx-imgBorder"]
   > ![门户中注册密钥的屏幕截图](media/reg-key.png)

3. 打开“注册密钥”选项卡，然后选择“生成新密钥” 。
4. 输入到期日期，然后选择“确定”。  

>[!NOTE]
>到期日期可以是自生成时间和日期起最短不少于 1 小时并且最长不超过 27 天的时间和日期。 强烈建议将到期日期设置为最大值 27 天。

5. 将这个新生成的密钥复制到剪贴板。 稍后会需要用到。

### <a name="step-4-reinstall-the-agent-and-boot-loader"></a>步骤 4：重新安装代理和启动加载程序

通过重新安装代理和启动加载程序的最新版本，并行堆栈和日内瓦监视代理也会自动安装。 若要重新安装代理和启动加载程序，请执行以下操作：
1. 以管理员身份登录到 VM，并根据 VM 运行的 Windows 的版本不同为部署使用正确的代理安装程序版本。 如果有 Windows 10 VM，请按照[注册虚拟机](create-host-pools-powershell.md#register-the-virtual-machines-to-the-azure-virtual-desktop-host-pool)中的说明来下载 Azure 虚拟桌面代理和 Azure 虚拟桌面代理启动加载程序 。 如果有 Windows 7 VM，请按照[注册虚拟机](deploy-windows-7-virtual-machine.md#configure-a-windows-7-virtual-machine)中的步骤 13-14 来下载 Azure 虚拟桌面代理和 Azure 虚拟桌面代理管理器 。

   > [!div class="mx-imgBorder"]
   > ![代理和启动加载程序下载页的屏幕截图](media/download-agent.png)

2. 右键单击下载的代理和启动加载程序安装程序。
3. 选择“属性”。
4. 选择“取消阻止”。
5. 选择“确定”。
6. 运行代理安装程序。
7. 在安装程序要求提供注册令牌时，请从剪贴板粘贴注册密钥。 

   > [!div class="mx-imgBorder"]
   > ![粘贴的注册令牌的屏幕截图](media/pasted-agent-token.png)

8. 运行该启动加载程序的安装程序。
9. 重新启动 VM。 
10. 转到 [Azure 门户](https://portal.azure.com)并打开 VM 所属主机池的“概述”页。
11. 转到“会话主机”选项卡，以查看该主机池中所有会话主机的列表。
12. 现在，应该会看到在主机池中注册的会话主机，其状态为“可用”。 

   > [!div class="mx-imgBorder"]
   > ![可用会话主机的屏幕截图](media/hostpool-portal.png)

## <a name="next-steps"></a>后续步骤

如果问题仍然存在，请创建支持案例，并包括有关所遇问题的详细信息，以及任何为尝试解决此问题所采取的操作。 以下列表包括了其他可用于排查 Azure 虚拟桌面部署中的问题的资源。

- 有关排查 Azure 虚拟桌面问题和进行升级跟踪的概述，请参阅[故障排除概述、反馈和支持](troubleshoot-set-up-overview.md)。
- 若要排查在 Azure 虚拟桌面环境中创建主机池时的问题，请参阅[环境和主机池创建](troubleshoot-set-up-issues.md)。
- 若要排查在 Azure 虚拟桌面中配置虚拟机 (VM) 时遇到的问题，请参阅[会话主机虚拟机配置](troubleshoot-vm-configuration.md)。
- 若要排查 Azure 虚拟桌面客户端连接问题，请参阅 [Azure 虚拟桌面服务连接](troubleshoot-service-connection.md)。
- 若要排查有关远程桌面客户端的问题，请参阅[远程桌面客户端故障排除](troubleshoot-client.md)。
- 若要排查将 PowerShell 与 Azure 虚拟桌面结合使用时遇到的问题，请参阅 [Azure 虚拟桌面 PowerShell](troubleshoot-powershell.md)。
- 若要详细了解该服务，请参阅 [Azure 虚拟桌面环境](environment-setup.md)。
- 若要完成故障排除教程，请参阅[教程：排查资源管理器模板部署问题](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)。
- 若要了解审核操作，请参阅[使用 Resource Manager 执行审核操作](../azure-monitor/essentials/activity-log.md)。
- 若要了解部署期间为确定错误需要执行哪些操作，请参阅[查看部署操作](../azure-resource-manager/templates/deployment-history.md)。
