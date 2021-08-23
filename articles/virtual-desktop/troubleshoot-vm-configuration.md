---
title: Azure 虚拟桌面会话主机故障排除 - Azure
description: 如何解决在配置 Azure 虚拟桌面会话主机虚拟机时遇到的问题。
author: Heidilohr
ms.topic: troubleshooting
ms.date: 05/11/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 8931103305d85b50a76dab001b37612a6c50a5cf
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2021
ms.locfileid: "112028606"
---
# <a name="session-host-virtual-machine-configuration"></a>会话主机虚拟机配置

>[!IMPORTANT]
>本内容适用于包含 Azure 资源管理器 Azure 虚拟桌面对象的 Azure 虚拟桌面。 如果你使用的是不包含 Azure 资源管理器对象的 Azure 虚拟桌面（经典），请参阅[此文](./virtual-desktop-fall-2019/troubleshoot-vm-configuration-2019.md)。

使用本文来排查在配置 Azure 虚拟桌面会话主机虚拟机 (VM) 时遇到的问题。

## <a name="provide-feedback"></a>提供反馈

请访问 [Azure 虚拟桌面技术社区](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)，与产品团队和活跃的社区成员共同探讨 Azure 虚拟桌面服务。

## <a name="vms-are-not-joined-to-the-domain"></a>VM 未加入域

如果在将虚拟机 (VM) 加入到域时遇到问题，请按照以下说明进行操作。

- 使用[将 Windows Server 虚拟机加入到托管域](../active-directory-domain-services/join-windows-vm.md)中的过程，或者使用[域加入模板](https://azure.microsoft.com/resources/templates/vm-domain-join-existing/)，手动加入 VM。
- 尝试从 VM 中的命令行 ping 域名。
- 在[排查域加入错误消息](https://social.technet.microsoft.com/wiki/contents/articles/1935.troubleshooting-domain-join-error-messages.aspx)中查看域加入错误消息列表。

### <a name="error-incorrect-credentials"></a>错误：凭据错误

原因：在 Azure 资源管理器模板接口修补程序中输入凭据时，出现拼写错误。

修复：请采取以下措施之一解决此问题。

- 手动将 VM 添加到域。
- 在确认凭据后，重新部署模板。 请参阅[使用 PowerShell 创建主机池](create-host-pools-powershell.md)。
- 使用[将现有 Windows VM 加入到 AD 域](https://azure.microsoft.com/resources/templates/vm-domain-join-existing/)中的模板将 VM 加入到域。

### <a name="error-timeout-waiting-for-user-input"></a>错误：等待用户输入超时

原因：用于完成域加入的帐户可能具有多重身份验证 (MFA)。

修复：请采取以下措施之一解决此问题。

- 为此帐户暂时删除 MFA。
- 使用服务帐户。

### <a name="error-the-account-used-during-provisioning-doesnt-have-permissions-to-complete-the-operation"></a>错误：预配期间使用的帐户没有权限完成此操作

原因：由于符合性和法规要求，所使用的帐户没有权限将 VM 加入到域。

修复：请采取以下措施之一解决此问题。

- 使用管理员组成员帐户。
- 向所使用的帐户授予必要的权限。

### <a name="error-domain-name-doesnt-resolve"></a>错误：无法解析域名

原因 1：VM 所在的虚拟网络没有关联域所在的虚拟网络 (VNET)。

修复 1：在预配了 VM 的 VNET 和域控制器 (DC) 正在运行的 VNET 之间创建 VNET 对等互连。 请参阅[创建虚拟网络对等互连 - 资源管理器，不同订阅](../virtual-network/create-peering-different-subscriptions.md)。

原因 2：使用 Azure Active Directory 域服务 (Azure AD DS) 时，虚拟网络的 DNS 服务器设置未更新为指向托管域控制器。

修复 2：若要更新包含 Azure AD DS 的虚拟网络的 DNS 设置，请参阅[更新 Azure 虚拟网络的 DNS 设置](../active-directory-domain-services/tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network)。

原因 3：网络接口的 DNS 服务器设置未指向虚拟网络中对应的 DNS 服务器。

修复 3：按照 [更改 DNS 服务器] 中的步骤，采取以下某项措施来解决此问题。
- 按照[更改 DNS 服务器](../virtual-network/virtual-network-network-interface.md#change-dns-servers)中的步骤，将网络接口的 DNS 服务器设置更改为“自定义”，并指定虚拟网络中 DNS 服务器的专用 IP 地址。
- 按照[更改 DNS 服务器](../virtual-network/virtual-network-network-interface.md#change-dns-servers)中的步骤，将网络接口的 DNS 服务器设置更改为“从虚拟网络继承”，然后按照[更改 DNS 服务器](../virtual-network/manage-virtual-network.md#change-dns-servers)中的步骤更改虚拟网络的 DNS 服务器设置。

## <a name="azure-virtual-desktop-agent-and-azure-virtual-desktop-boot-loader-are-not-installed"></a>未安装 Azure 虚拟桌面代理和 Azure 虚拟桌面启动加载器

建议使用 Azure 门户创建模板来预配 VM。 该模板会自动安装 Azure 虚拟桌面代理和 Azure 虚拟桌面代理启动加载器。

请按照以下说明来确认是否已安装组件并检查是否有错误消息。

1. 通过检查“控制面板” > “程序” > “程序和功能”，确认是否已安装这两个组件  。 如果“Azure 虚拟桌面代理”和“Azure 虚拟桌面代理启动加载器”不可见，则说明 VM 中未安装它们 。
2. 打开“文件资源管理器”并导航到 C:\Windows\Temp\ScriptLog.log 。 如果缺少该文件，则表示安装了这两个组件的 PowerShell DSC 无法在提供的安全上下文中运行。
3. 如果文件 C:\Windows\Temp\ScriptLog.log 存在，请将其打开，并检查错误消息。

### <a name="error-azure-virtual-desktop-agent-and-azure-virtual-desktop-agent-boot-loader-are-missing-cwindowstempscriptloglog-is-also-missing"></a>错误：缺少 Azure 虚拟桌面代理和 Azure 虚拟桌面代理启动加载器。 也缺少 C:\Windows\Temp\ScriptLog.log

原因 1：Azure 资源管理器模板输入期间提供的凭据错误或权限不足。

修复 1：使用[通过 PowerShell 创建主机池](create-host-pools-powershell.md)，手动将缺少的组件添加到 VM。

原因 2：PowerShell DSC 能够启动并执行，但无法完成，因为它无法登录到 Azure 虚拟桌面并获得所需信息。

修复 2：确认下面列表中的项。

- 请确保该帐户没有 MFA。
- 确认主机池的名称是否正确，且 Azure 虚拟桌面中是否存在该主机池。
- 确认帐户对 Azure 订阅或资源组是否至少具有“参与者”权限。

### <a name="error-authentication-failed-error-in-cwindowstempscriptloglog"></a>错误：身份验证失败，C:\Windows\Temp\ScriptLog.log 出错

原因：PowerShell DSC 可以执行，但无法连接到 Azure 虚拟桌面。

修复方法：确认下列项。

- 手动向 Azure 虚拟桌面服务注册 VM。
- 确认用于连接到 Azure 虚拟桌面的帐户对 Azure 订阅或资源组是否具有权限来创建主机池。
- 确认帐户没有 MFA。

## <a name="azure-virtual-desktop-agent-is-not-registering-with-the-azure-virtual-desktop-service"></a>Azure 虚拟桌面代理未向 Azure 虚拟桌面服务注册

首次在会话主机 VM 上安装 Azure 虚拟桌面代理（采用手动方式或通过 Azure 资源管理器模板和 PowerShell DSC）时，它会提供注册令牌。 以下部分介绍适用于 Azure 虚拟桌面代理和令牌的故障排除问题。

### <a name="error-the-status-filed-in-get-azwvdsessionhost-cmdlet-shows-status-as-unavailable"></a>错误：Get-AzWvdSessionHost cmdlet 中报告的状态显示状态“不可用”

> [!div class="mx-imgBorder"]
> ![Get-AzWvdSessionHost cmdlet 显示状态“不可用”。](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**原因：** 代理无法自行更新到新版本。

修复：按照以下说明手动更新代理。

1. 在会话主机 VM 上下载新版本的代理。
2. 启动“任务管理器”，然后在“服务”选项卡中停止 RDAgentBootLoader 服务。
3. 运行 Azure 虚拟桌面代理新版本的安装程序。
4. 在系统提示输入注册令牌时，删除 INVALID_TOKEN 条目并按“下一步”（无需新令牌）。
5. 完成安装向导。
6. 打开“任务管理器”并启动 RDAgentBootLoader 服务。

## <a name="error-azure-virtual-desktop-agent-registry-entry-isregistered-shows-a-value-of-0"></a>错误：Azure 虚拟桌面代理注册表项 IsRegistered 显示值为 0

**原因：** 注册令牌已过期。

**修复：** 按照以下说明修复代理注册表错误。

1. 如果已有注册令牌，请使用 Remove-AzWvdRegistrationInfo 将其删除。
2. 运行 New-AzWvdRegistrationInfo cmdlett 以生成新令牌。
3. 确认“-ExpriationTime”参数设置为 3 天。

### <a name="error-azure-virtual-desktop-agent-isnt-reporting-a-heartbeat-when-running-get-azwvdsessionhost"></a>错误：运行 Get-AzWvdSessionHost 时，Azure 虚拟桌面代理未报告检测信号

原因 1：RDAgentBootLoader 服务已停止。

修复 1：启动“任务管理器”，如果“服务”选项卡报告 RDAgentBootLoader 服务为停止状态，则启动该服务。

原因 2：端口 443 可能已关闭。

修复 2：按照以下说明打开端口 443。

1. 通过从 [Sysinternal 工具](/sysinternals/downloads/psping/)下载 PSPing 工具来确认端口 443 是否打开。
2. 在运行代理的会话主机 VM 上安装 PSPing。
3. 以管理员身份打开命令提示符，并发出以下命令：

    ```cmd
    psping rdbroker.wvdselfhost.microsoft.com:443
    ```

4. 确认 PSPing 收到了从 RDBroker 返回的信息：

    ```
    PsPing v2.10 - PsPing - ping, latency, bandwidth measurement utility
    Copyright (C) 2012-2016 Mark Russinovich
    Sysinternals - www.sysinternals.com
    TCP connect to 13.77.160.237:443:
    5 iterations (warmup 1) ping test:
    Connecting to 13.77.160.237:443 (warmup): from 172.20.17.140:60649: 2.00ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60650: 3.83ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60652: 2.21ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60653: 2.14ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60654: 2.12ms
    TCP connect statistics for 13.77.160.237:443:
    Sent = 4, Received = 4, Lost = 0 (0% loss),
    Minimum = 2.12ms, Maximum = 3.83ms, Average = 2.58ms
    ```

## <a name="troubleshooting-issues-with-the-azure-virtual-desktop-side-by-side-stack"></a>排查 Azure 虚拟桌面并行堆栈问题

Azure 虚拟桌面并行堆栈随 Windows Server 2019 自动安装。 使用 Microsoft 安装程序 (MSI) 在 Microsoft Windows Server 2016 或 Windows Server 2012 R2 上安装并行堆栈。 对于 Microsoft Windows 10，使用 enablesxstackrs.ps1 启用 Azure 虚拟桌面并行堆栈。

提供三种主要方式在会话主机池 VM 上安装或启用并行堆栈：

- 使用 Azure 门户创建模板
- 向主映像添加并在其上启用
- 在每个 VM 上手动（或使用扩展/PowerShell）安装或启用

如果 Azure 虚拟桌面并行堆栈出现问题，请在命令提示符中键入 qwinsta 命令，以确认是否已安装或启用并行堆栈。

如果安装并启用了并行堆栈，则 qwinsta 的输出将在输出中列出 rdp-sxs 。

> [!div class="mx-imgBorder"]
> ![qwinsta 在输出中列为 rdp-sxs，已安装或启用并行堆栈。](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

检查下面列出的注册表项，并确认其值是否匹配。 如果缺少注册表项或值不匹配，请确保运行的是[受支持的操作系统](troubleshoot-agent.md#error-operating-a-pro-vm-or-other-unsupported-os)。 如果是，请按照[使用 PowerShell 创建主机池](create-host-pools-powershell.md)中有关如何重新安装并行堆栈的说明进行操作。

```registry
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\WinStations\rds-sxs\"fEnableWinstation":DWORD=1

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\ClusterSettings\"SessionDirectoryListener":rdp-sxs
```

### <a name="error-o_reverse_connect_stack_failure"></a>错误：O_REVERSE_CONNECT_STACK_FAILURE

> [!div class="mx-imgBorder"]
> ![O_REVERSE_CONNECT_STACK_FAILURE 错误代码。](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

原因：会话主机 VM 上未安装并行堆栈。

修复：按照以下说明在会话主机 VM 上安装并行堆栈。

1. 使用远程桌面协议 (RDP) 以本地管理员身份直接进入会话主机 VM。
2. 通过[使用 PowerShell 创建主机池](create-host-pools-powershell.md)安装并行堆栈。

## <a name="how-to-fix-a-azure-virtual-desktop-side-by-side-stack-that-malfunctions"></a>如何修复发生故障的 Azure 虚拟桌面并行堆栈

存在已知会导致并列堆栈发生故障的情况：

- 未遵循正确的步骤顺序来启用并行堆栈
- 自动更新到 Windows 10 增强型通用光盘 (EVD)
- 缺少远程桌面会话主机 (RDSH) 角色
- 多次运行 enablesxsstackrc.ps1
- 在不具有本地管理员特权的帐户中运行 enablesxsstackrc.ps1

本部分中的说明可帮助卸载 Azure 虚拟桌面并行堆栈。 在卸载并行堆栈后，请转到[使用 PowerShell 创建主机池](create-host-pools-powershell.md)中的“向 Azure 虚拟机主机池注册 VM”，以重新安装并行堆栈。

用于运行修正的 VM 必须与具有故障并行堆栈的 VM 位于同一子网和域中。

按照以下说明从同一子网和域运行修正：

1. 使用标准远程桌面协议 (RDP) 连接到要应用修补程序的 VM。
2. 从 [https://docs.microsoft.com/sysinternals/downloads/psexec](/sysinternals/downloads/psexec) 下载 PsExec。
3. 解压缩下载的文件。
4. 以本地管理员身份启动命令提示符。
5. 导航到解压缩 PsExec 的文件夹。
6. 在命令提示符中使用以下命令：

    ```cmd
            psexec.exe \\<VMname> cmd
    ```

    >[!NOTE]
    >VMname 是具有故障并行堆栈的 VM 的计算机名称。

7. 单击“同意”以接受 PsExec 许可协议。

    > [!div class="mx-imgBorder"]
    > ![软件许可协议的屏幕截图。](media/SoftwareLicenseTerms.png)

    >[!NOTE]
    >仅在首次运行 PsExec 时才会显示此对话框。

8. 在具有故障并行堆栈的虚拟机上打开命令提示符会话后，运行 qwinsta，并确认名为 rdp-sxs 的条目是否可用。 如果不可用，则 VM 中没有并行堆栈，因此该问题与并行堆栈无关联。

    > [!div class="mx-imgBorder"]
    > ![管理员命令提示符](media/AdministratorCommandPrompt.png)

9. 运行以下命令，其将列出具有故障并行堆栈的 VM 上安装的 Microsoft 组件。

    ```cmd
        wmic product get name
    ```

10. 使用上一步中的产品名称运行以下命令。

    ```cmd
        wmic product where name="<Remote Desktop Services Infrastructure Agent>" call uninstall
    ```

11. 卸载以“远程桌面”开头的所有产品。

12. 在卸载了所有 Azure 虚拟桌面组件后，按照适用于你的操作系统的说明进行操作：

13. 如果操作系统为 Windows Server，则重启具有故障并行堆栈的 VM（通过使用 Azure 门户或从 PsExec 工具进行此操作）。

如果操作系统为 Microsoft Windows 10，请继续按以下说明进行操作：

14. 从运行 PsExec 的 VM 中，打开文件资源管理器，并将 disablesxsstackrc.ps1 复制到具有故障并行堆栈的 VM 的系统驱动器。

    ```cmd
        \\<VMname>\c$\
    ```

    >[!NOTE]
    >VMname 是具有故障并行堆栈的 VM 的计算机名称。

15. 建议的过程：从 PsExec 工具启动 PowerShell，导航到上一步中的文件夹并运行 disablesxsstackrc.ps1。 或者，可运行以下 cmdlet：

    ```PowerShell
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\ClusterSettings" -Name "SessionDirectoryListener" -Force
    Remove-Item -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" -Recurse -Force
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations" -Name "ReverseConnectionListener" -Force
    ```

16. 在该 cmdlet 运行完毕后，请重启具有故障并行堆栈的 VM。

## <a name="remote-desktop-licensing-mode-isnt-configured"></a>未配置远程桌面许可模式

如果使用管理帐户登录到 Windows 10 企业版多会话，则可能会接收到一条通知，显示“未配置远程桌面许可模式，远程桌面服务将在 X 天内停止工作。 请在连接代理服务器中，使用服务器管理器指定远程桌面许可模式。”

如果时间限制过期，则将出现一条错误消息，显示“由于这台计算机没有远程桌面客户端访问许可证，远程会话被中断。”

如果出现这些消息中的任意一条，则表示该映像未安装最新的 Windows 更新，或者你正在通过组策略设置远程桌面许可模式。 按照下一部分中的步骤检查组策略设置，确定 Windows 10 企业版多会话的版本，并安装相应的更新。

>[!NOTE]
>在主机池包含 Windows Server 会话主机时，Azure 虚拟桌面只需要 RDS 客户端访问许可证 (CAL)。 若要了解如何配置 RDS CAL，请参阅[使用客户端访问许可证为 RDS 部署提供许可](/windows-server/remote/remote-desktop-services/rds-client-access-license/)。

### <a name="disable-the-remote-desktop-licensing-mode-group-policy-setting"></a>禁用远程桌面许可模式组策略设置

在 VM 中打开组策略编辑器，导航到“管理模板” > “Windows 组件” > “远程桌面服务” > “远程桌面会话主机” > “许可” > “设置远程桌面许可模式”，检查组策略设置    。 如果组策略设置为“启用”，则将其更改为“禁用”。 如果已禁用，则保持选中该选项。

>[!NOTE]
>如果通过域设置组策略，则针对 Windows 10 企业版多会话 VM 的策略禁用此设置。

### <a name="identify-which-version-of-windows-10-enterprise-multi-session-youre-using"></a>确定正在使用 Windows 10 企业版多会话的哪个版本

若要检查所使用的 Windows 10 企业版多会话的版本，请执行以下操作：

1. 使用管理员帐户登录。
2. 在“开始”菜单旁边的搜索栏中输入“关于”。
3. 选择“电脑信息”。
4. 检查“版本”旁边的编号。 该数字应为“1809”或“1903”，如下图所示。

    > [!div class="mx-imgBorder"]
    > ![Windows 规格窗口的屏幕截图。 版本号突出显示为蓝色。](media/windows-specifications.png)

了解版本号后，请跳转到相关部分。

### <a name="version-1809"></a>版本 1809

如果版本号显示“1809”，请安装 [KB4516077 更新](https://support.microsoft.com/help/4516077)。

### <a name="version-1903"></a>版本 1903

使用 Azure 库中最新版本的 Windows 10 版本 1903 映像重新部署主机操作系统。

## <a name="we-couldnt-connect-to-the-remote-pc-because-of-a-security-error"></a>由于安全错误，无法连接到远程电脑

如果用户看到一条错误，显示“由于安全错误，无法连接到远程电脑。 如果此问题仍然存在，请向管理员或技术支持人员获取帮助”，请验证更改默认 RDP 权限的任何现有策略。 可能导致此错误的策略是“允许通过远程桌面服务安全策略登录。”

若要详细了解此策略，请参阅[允许通过远程桌面服务登录](/windows/security/threat-protection/security-policy-settings/allow-log-on-through-remote-desktop-services)。

## <a name="next-steps"></a>后续步骤

- 如需大致了解如何排查 Azure 虚拟桌面问题和跟踪升级，请参阅[故障排除概述、反馈和支持](troubleshoot-set-up-overview.md)。
- 若要排查在 Azure 虚拟桌面环境中创建主机池时的问题，请参阅[环境和主机池创建](troubleshoot-set-up-issues.md)。
- 若要排查在 Azure 虚拟桌面中配置虚拟机 (VM) 时遇到的问题，请参阅[会话主机虚拟机配置](troubleshoot-vm-configuration.md)。
- 若要排查与 Azure 虚拟桌面代理或会话连接性相关的问题，请参阅[排查常见的 Azure 虚拟桌面代理问题](troubleshoot-agent.md)。
- 若要排查 Azure 虚拟桌面客户端连接问题，请参阅 [Azure 虚拟桌面服务连接](troubleshoot-service-connection.md)。
- 若要排查远程桌面客户端的问题，请参阅[排查远程桌面客户端问题](troubleshoot-client.md)
- 若要排查结合使用 PowerShell 与 Azure 虚拟桌面时遇到的问题，请参阅 [Azure 虚拟桌面 PowerShell](troubleshoot-powershell.md)。
- 若要详细了解该服务，请参阅 [Azure 虚拟桌面环境](environment-setup.md)。
- 若要完成故障排除教程，请参阅[教程：排查资源管理器模板部署问题](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)。
- 若要了解审核操作，请参阅[使用 Resource Manager 执行审核操作](../azure-resource-manager/management/view-activity-logs.md)。
- 若要了解部署期间为确定错误需要执行哪些操作，请参阅[查看部署操作](../azure-resource-manager/templates/deployment-history.md)。
