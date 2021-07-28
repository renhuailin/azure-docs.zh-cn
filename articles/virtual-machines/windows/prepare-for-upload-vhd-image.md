---
title: 准备好要上传到 Azure 的 Windows VHD
description: 了解如何准备要上传到 Azure 的 Windows VHD 或 VHDX
author: glimoli
manager: dcscontentpm
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.workload: infrastructure-services
ms.topic: troubleshooting
ms.date: 09/02/2020
ms.author: genli
ms.openlocfilehash: 8315c2fa094f1d12a788d42a336cb01feb58c6c9
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110450269"
---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>准备好要上传到 Azure 的 Windows VHD 或 VHDX

在将 Windows 虚拟机 (VM) 从本地上传到 Azure 之前，必须准备好虚拟硬盘（VHD 或 VHDX）。 Azure 支持采用 VHD 文件格式且具有固定大小磁盘的第 1 代和第 2 代 VM。 第 1 代 VM 上的 OS VHD 允许的最大大小为 2 TB。

可以将 VHDX 文件转换为 VHD，将动态扩展磁盘转换为固定大小的磁盘，但无法更改 VM 的代次。 有关详细信息，请参阅[应在 Hyper-V 中创建第 1 代还是第 2 代 VM？](/windows-server/virtualization/hyper-v/plan/Should-I-create-a-generation-1-or-2-virtual-machine-in-Hyper-V)和 [Azure 对第 2 代 VM 的支持](../generation-2.md)。

有关 Azure VM 的支持策略的信息，请参阅 [Microsoft 服务器软件支持 Azure VM](https://support.microsoft.com/help/2721672/)。

> [!NOTE]
> 本文中的说明适用于：
>
> - 64 位版本的 Windows Server 2008 R2 以及更高版本的 Windows Server 操作系统。 若要了解如何在 Azure 中运行 32 位操作系统，请参阅 [Azure VM 中的 32 位操作系统支持](https://support.microsoft.com/help/4021388/)。
> - 如果将使用任何灾难恢复工具（如 Azure Site Recovery 或 Azure Migrate）来迁移工作负荷，则在来宾 OS上仍需要此过程以在迁移之前准备映像。

## <a name="system-file-checker"></a>系统文件检查器

### <a name="run-windows-system-file-checker-utility-before-generalization-of-os-image"></a>在 OS 映像通用化之前运行 Windows 系统文件检查器实用工具

系统文件检查器 (SFC) 用于验证和替换 Windows 系统文件。

> [!IMPORTANT]
> 使用提升权限的 PowerShell 会话中运行本文中所述的示例。

运行 SFC 命令：

```powershell
sfc.exe /scannow
```

```Output
Beginning system scan.  This process will take some time.

Beginning verification phase of system scan.
Verification 100% complete.

Windows Resource Protection did not find any integrity violations.
```

完成 SFC 扫描后，请尝试安装 Windows 更新并重启计算机。

## <a name="set-windows-configurations-for-azure"></a>设置 Azure 的 Windows 配置

> [!NOTE]
> 使用通用化映像创建 Windows VM 时，Azure 平台会将 ISO 文件装载到 DVD-ROM。 出于这个原因，必须在通用化映像的 OS 中启用 DVD-ROM。 如果禁用了它，Windows VM 会停滞在“全新安装体验”(OOBE)。

1. 删除路由表中的所有静态持久性路由：

   - 若要查看路由表，请运行 `route.exe print`。
   - 请查看持久性路由部分。 如果有持久性路由，请使用 `route.exe delete` 命令将其删除。

1. 删除 WinHTTP 代理：

   ```powershell
   netsh.exe winhttp reset proxy
   ```

    如果 VM 需要使用特定代理，请为 Azure IP 地址 ([168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md)) 添加代理例外，使 VM 能够连接到 Azure：

    ```
    $proxyAddress='<your proxy server>'
    $proxyBypassList='<your list of bypasses>;168.63.129.16'
    netsh.exe winhttp set proxy $proxyAddress $proxyBypassList
    ```

1. 打开 DiskPart：

   ```powershell
   diskpart.exe
   ```

   将磁盘 SAN 策略设置为 [`Onlineall`](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/gg252636(v=ws.11))：

   ```DiskPart
   DISKPART> san policy=onlineall
   DISKPART> exit
   ```

1. 为 Windows 设置协调世界时 (UTC) 时间。 此外，将 Windows 时间服务 w32time 的启动类型设置为“自动” ：

   ```powershell
   Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Control\TimeZoneInformation -Name RealTimeIsUniversal -Value 1 -Type DWord -Force
   Set-Service -Name w32time -StartupType Automatic
   ```

1. 将电源配置文件设置为“高性能”：

   ```powershell
   powercfg.exe /setactive SCHEME_MIN
   ```

1. 确保将环境变量 TEMP 和 TMP 设为其默认值 ：

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -Name TEMP -Value "%SystemRoot%\TEMP" -Type ExpandString -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -Name TMP -Value "%SystemRoot%\TEMP" -Type ExpandString -Force
   ```
1. 对于具有旧版操作系统（Windows Server 2012 R2 或 Windows 8.1 及更低版本）的 VM，请确保安装最新的 Hyper-V 集成组件服务。 有关详细信息，请参阅 [Windows VM 的 Hyper-V 集成组件更新](https://support.microsoft.com/topic/hyper-v-integration-components-update-for-windows-virtual-machines-8a74ffad-576e-d5a0-5a2f-d6fb2594f990)。

> [!NOTE]
> 在要为 VM 设置本地 VMware 服务器与 Azure 之间灾难恢复的解决方案的情况下，将无法使用 Hyper-V 集成组件服务。 在这种情况下，请联系 VMware 支持部门，将 VM 迁移到 Azure 并使其也驻留在 VMware 服务器中。

## <a name="check-the-windows-services"></a>查看 Windows 服务

确保下面的每个 Windows 服务均设置为 Windows 默认值。 最起码需要配置这些服务，才能确保 VM 能够建立连接。 若要设置启动设置，请运行以下示例：

```powershell
Get-Service -Name BFE, Dhcp, Dnscache, IKEEXT, iphlpsvc, nsi, mpssvc, RemoteRegistry |
  Where-Object StartType -ne Automatic |
    Set-Service -StartupType Automatic

Get-Service -Name Netlogon, Netman, TermService |
  Where-Object StartType -ne Manual |
    Set-Service -StartupType Manual
```

## <a name="update-remote-desktop-registry-settings"></a>更新远程桌面注册表设置

确保正确配置以下设置以进行远程访问：

> [!NOTE]
> 如果在运行 `Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services -Name <string> -Value <object>` 时收到错误消息，可以忽略该错误消息。 这意味着域未通过组策略对象设置该配置。

1. 已启用远程桌面协议 (RDP)：

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server' -Name fDenyTSConnections -Value 0 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name fDenyTSConnections -Value 0 -Type DWord -Force
   ```

1. 使用默认端口为 3389 正确设置 RDP 端口：

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name PortNumber -Value 3389 -Type DWord -Force
   ```

   部署 VM 时，将为端口 3389 创建默认规则。 若要更改端口号，请在 VM 部署到 Azure 以后再进行。

1. 侦听器在每个网络接口中侦听：

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name LanAdapter -Value 0 -Type DWord -Force
   ```

1. 配置用于 RDP 连接的网络级身份验证 (NLA) 模式：

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name UserAuthentication -Value 1 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name SecurityLayer -Value 1 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name fAllowSecProtocolNegotiation -Value 1 -Type DWord -Force
   ```

1. 设置 keep-alive 值：

   ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name KeepAliveEnable -Value 1  -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name KeepAliveInterval -Value 1  -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name KeepAliveTimeout -Value 1 -Type DWord -Force
   ```

1. 设置重新连接选项：

   ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name fDisableAutoReconnect -Value 0 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name fInheritReconnectSame -Value 1 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name fReconnectSame -Value 0 -Type DWord -Force
   ```

1. 限制并发连接数：

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name MaxInstanceCount -Value 4294967295 -Type DWord -Force
   ```

1. 删除任何已绑定到 RDP 侦听器的自签名证书：

   ```powershell
   if ((Get-Item -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp').Property -contains 'SSLCertificateSHA1Hash')
   {
       Remove-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name SSLCertificateSHA1Hash -Force
   }
   ```

   此代码可确保在部署 VM 时可以连接。 在 Azure 中部署 VM 后，还可以查看这些设置。

1. 如果 VM 是域的一部分，请检查以下策略以确保未还原以前的设置。

    |                 目标                  |                                                                            策略                                                                            |                           Value                            |
    | ------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------- |
    | RDP 已启用                        | 计算机配置\策略\Windows 设置\管理模板\组件\远程桌面服务\远程桌面会话主机\连接         | 允许用户使用远程桌面进行远程连接    |
    | NLA 组策略                      | 设置\管理模板\组件\远程桌面服务\远程桌面会话主机\安全性                                                    | 需要完成用户身份验证才能使用 NLA 进行远程访问 |
    | Keep-Alive 设置                   | 计算机配置\策略\Windows 设置\管理模板\Windows 组件\远程桌面服务\远程桌面会话主机\连接 | 配置保持活动状态的连接间隔                   |
    | 重新连接设置                    | 计算机配置\策略\Windows 设置\管理模板\Windows 组件\远程桌面服务\远程桌面会话主机\连接 | 自动重新连接                                    |
    | “限制连接数”设置 | 计算机配置\策略\Windows 设置\管理模板\Windows 组件\远程桌面服务\远程桌面会话主机\连接 | 限制连接数                                |

## <a name="configure-windows-firewall-rules"></a>配置 Windows 防火墙规则

1. 在三个配置文件（“域”、“标准”和“公共”）上启用 Windows 防火墙：

   ```powershell
   Set-NetFirewallProfile -Profile Domain, Public, Private -Enabled True
   ```

1. 运行以下示例，以通过三个防火墙配置文件（“域”、“专用”和“公共”）来允许 WinRM，并启用 PowerShell 远程服务：

   ```powershell
   Enable-PSRemoting -Force
   Set-NetFirewallRule -Name WINRM-HTTP-In-TCP, WINRM-HTTP-In-TCP-PUBLIC -Enabled True
   ```

1. 启用以下防火墙规则以允许 RDP 流量：

   ```powershell
   Set-NetFirewallRule -Group '@FirewallAPI.dll,-28752' -Enabled True
   ```

1. 启用文件和打印机共享规则，使 VM 能够在虚拟网络中响应 ping 请求：

   ```powershell
   Set-NetFirewallRule -Name FPS-ICMP4-ERQ-In -Enabled True
   ```

1. 为 Azure 平台网络创建规则：

   ```powershell
   New-NetFirewallRule -DisplayName AzurePlatform -Direction Inbound -RemoteAddress 168.63.129.16 -Profile Any -Action Allow -EdgeTraversalPolicy Allow
   New-NetFirewallRule -DisplayName AzurePlatform -Direction Outbound -RemoteAddress 168.63.129.16 -Profile Any -Action Allow
   ```

1. 如果 VM 是域的一部分，请检查以下 Azure AD 策略以确保未还原以前的设置。

    |                 目标                 |                                                                         策略                                                                          |                  Value                  |
    | ------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------- |
    | 启用 Windows 防火墙配置文件 | 计算机配置\策略\Windows 设置\管理模板\网络\网络连接\Windows 防火墙\域配置文件\Windows 防火墙   | 保护所有网络连接         |
    | 启用 RDP                           | 计算机配置\策略\Windows 设置\管理模板\网络\网络连接\Windows 防火墙\域配置文件\Windows 防火墙   | 允许入站远程桌面异常 |
    |                                      | 计算机配置\策略\Windows 设置\管理模板\网络\网络连接\Windows 防火墙\标准配置文件\Windows 防火墙 | 允许入站远程桌面异常 |
    | 启用 ICMP-V4                       | 计算机配置\策略\Windows 设置\管理模板\网络\网络连接\Windows 防火墙\域配置文件\Windows 防火墙   | 允许 ICMP 异常                   |
    |                                      | 计算机配置\策略\Windows 设置\管理模板\网络\网络连接\Windows 防火墙\标准配置文件\Windows 防火墙 | 允许 ICMP 异常                   |

## <a name="verify-the-vm"></a>验证 VM

确保 VM 正常、安全并可使用 RDP 访问：

1. 若要确保磁盘运行状况正常且一致，请在下次重启 VM 时检查磁盘：

   ```powershell
   chkdsk.exe /f
   ```

   确保报告显示磁盘干净且运行状况正常。

1. 设置引导配置数据 (BCD) 设置。

   ```powershell
   cmd

   bcdedit.exe /set "{bootmgr}" integrityservices enable
   bcdedit.exe /set "{default}" device partition=C:
   bcdedit.exe /set "{default}" integrityservices enable
   bcdedit.exe /set "{default}" recoveryenabled Off
   bcdedit.exe /set "{default}" osdevice partition=C:
   bcdedit.exe /set "{default}" bootstatuspolicy IgnoreAllFailures

   #Enable Serial Console Feature
   bcdedit.exe /set "{bootmgr}" displaybootmenu yes
   bcdedit.exe /set "{bootmgr}" timeout 5
   bcdedit.exe /set "{bootmgr}" bootems yes
   bcdedit.exe /ems "{current}" ON
   bcdedit.exe /emssettings EMSPORT:1 EMSBAUDRATE:115200

   exit
   ```

1. 转储日志可帮助排查 Windows 崩溃问题。 启用转储日志收集：

   ```powershell
   # Set up the guest OS to collect a kernel dump on an OS crash event
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name CrashDumpEnabled -Type DWord -Force -Value 2
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name DumpFile -Type ExpandString -Force -Value "%SystemRoot%\MEMORY.DMP"
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name NMICrashDump -Type DWord -Force -Value 1

   # Set up the guest OS to collect user mode dumps on a service crash event
   $key = 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps'
   if ((Test-Path -Path $key) -eq $false) {(New-Item -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting' -Name LocalDumps)}
   New-ItemProperty -Path $key -Name DumpFolder -Type ExpandString -Force -Value 'C:\CrashDumps'
   New-ItemProperty -Path $key -Name CrashCount -Type DWord -Force -Value 10
   New-ItemProperty -Path $key -Name DumpType -Type DWord -Force -Value 2
   Set-Service -Name WerSvc -StartupType Manual
   ```

1. 验证 Windows Management Instrumentation (WMI) 存储库是否一致：

   ```powershell
   winmgmt.exe /verifyrepository
   ```

   如果存储库已损坏，请参阅 [WMI：存储库是否损坏](https://techcommunity.microsoft.com/t5/ask-the-performance-team/wmi-repository-corruption-or-not/ba-p/375484)。

1. 请确保除 TermService 外，没有其他应用程序在使用端口 3389。 此端口用于 Azure 中的 RDP 服务。 若要查看 VM 上使用的端口，请运行 `netstat.exe -anob`：

   ```powershell
   netstat.exe -anob
   ```
   
   下面是一个示例。

   ```powershell
   netstat.exe -anob | findstr 3389
   TCP    0.0.0.0:3389           0.0.0.0:0              LISTENING       4056
   TCP    [::]:3389              [::]:0                 LISTENING       4056
   UDP    0.0.0.0:3389           *:*                                    4056
   UDP    [::]:3389              *:*                                    4056

   tasklist /svc | findstr 4056
   svchost.exe                   4056 TermService
   ```

1. 若要上传用作域控制器的 Windows VHD：

   - 请执行[这些额外的步骤](https://support.microsoft.com/kb/2904015)来准备磁盘。

   - 确保知道目录服务还原模式 (DSRM) 密码，以防你必须在 DSRM 下启动 VM。 有关详细信息，请参阅[设置 DSRM 密码](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/cc754363(v=ws.11))。

1. 确保知道内置的管理员帐户和密码。 可能需要重置当前的本地管理员密码，确保可以使用此帐户通过 RDP 连接登录 Windows。 此访问权限由“允许通过远程桌面服务登录”组策略对象控制。 在本地组策略编辑器中查看此对象：

   - `Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights Assignment`

1. 检查以下 Azure AD 策略，确保它们不会阻止 RDP 访问：

   - `Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights
      Assignment\Deny access to this computer from the network`

   - `Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights
      Assignment\Deny log on through Remote Desktop Services`

1. 检查以下 Azure AD 策略，确保它们不会删除任何必需的访问帐户：

   - `Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights Assignment\Access this computer from the network`

   策略应列出以下组：

   - 管理员

   - 备份操作员

   - 所有人

   - 用户

1. 重启 VM，确保 Windows 仍可正常运行，并可通过 RDP 连接来访问。 此时，请考虑在本地 Hyper-V 服务器中创建一个 VM，以确保该 VM 完全启动。 然后通过测试来确保可通过 RDP 来访问该 VM。

1. 删除所有其他传输驱动程序接口 (TDI) 筛选器。 例如，删除用于分析 TCP 数据包的软件或多余的防火墙。

1. 卸载与物理组件相关的任何其他第三方软件或驱动程序，或卸载任何其他虚拟化技术。

### <a name="install-windows-updates"></a>安装 Windows 更新

> [!NOTE]
> 为避免在 VM 预配期间发生意外重启，建议完成所有 Windows 更新安装并确保没有待处理的重启。 实现此操作的一种方法是在执行迁移到 Azure 之前，安装所有 Windows 更新并重启 VM。 </br><br>
>如果还需要对 OS (sysprep) 执行通用化操作，则需要先更新 Windows 并重启 VM，然后再运行 Sysprep 命令。

理想情况下，应将计算机更新为补丁级别；如果无法实现，请确保已安装下列更新。 若要获取最新的更新，请查看 Windows 更新历史记录页：[Windows 10 和 Windows Server 2019](https://support.microsoft.com/help/4000825)、[Windows 8.1 和 Windows Server 2012 R2](https://support.microsoft.com/help/4009470)，以及 [Windows 7 SP1 和 Windows Server 2008 R2 SP1](https://support.microsoft.com/help/4009469)。

<br />

|        组件        |     二进制     | Windows 7 SP1、Windows Server 2008 R2 SP1 |       Windows 8、Windows Server 2012        | Windows 8.1、Windows Server 2012 R2 | Windows 10 v1607、Windows Server 2016 v1607 |      Windows 10 v1703      | Windows 10 v1709、Windows Server 2016 v1709 | Windows 10 v1803、Windows Server 2016 v1803 |
| ----------------------- | -------------- | ----------------------------------------- | ------------------------------------------- | ----------------------------------- | ------------------------------------------- | -------------------------- | ------------------------------------------- | ------------------------------------------- |
| 存储                 | disk.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17638 / 6.2.9200.21757 - KB3137061 | 6.3.9600.18203 - KB3137061          | -                                           | -                          | -                                           | -                                           |
|                         | storport.sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17188 / 6.2.9200.21306 - KB3018489 | 6.3.9600.18573 - KB4022726          | 10.0.14393.1358 - KB4022715                 | 10.0.15063.332             | -                                           | -                                           |
|                         | ntfs.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17623 / 6.2.9200.21743 - KB3121255 | 6.3.9600.18654 - KB4022726          | 10.0.14393.1198 - KB4022715                 | 10.0.15063.447             | -                                           | -                                           |
|                         | Iologmsg.dll   | 6.1.7601.23403 - KB3125574                | 6.2.9200.16384 - KB2995387                  | -                                   | -                                           | -                          | -                                           | -                                           |
|                         | Classpnp.sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17061 / 6.2.9200.21180 - KB2995387 | 6.3.9600.18334 - KB3172614          | 10.0.14393.953 - KB4022715                  | -                          | -                                           | -                                           |
|                         | Volsnap.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17047 / 6.2.9200.21165 - KB2975331 | 6.3.9600.18265 - KB3145384          | -                                           | 10.0.15063.0               | -                                           | -                                           |
|                         | partmgr.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.16681 - KB2877114                  | 6.3.9600.17401 - KB3000850          | 10.0.14393.953 - KB4022715                  | 10.0.15063.0               | -                                           | -                                           |
|                         | volmgr.sys     |                                           |                                             |                                     |                                             | 10.0.15063.0               | -                                           | -                                           |
|                         | Volmgrx.sys    | 6.1.7601.23403 - KB3125574                | -                                           | -                                   | -                                           | 10.0.15063.0               | -                                           | -                                           |
|                         | Msiscsi.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.21006 - KB2955163                  | 6.3.9600.18624 - KB4022726          | 10.0.14393.1066 - KB4022715                 | 10.0.15063.447             | -                                           | -                                           |
|                         | Msdsm.sys      | 6.1.7601.23403 - KB3125574                | 6.2.9200.21474 - KB3046101                  | 6.3.9600.18592 - KB4022726          | -                                           | -                          | -                                           | -                                           |
|                         | Mpio.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.21190 - KB3046101                  | 6.3.9600.18616 - KB4022726          | 10.0.14393.1198 - KB4022715                 | -                          | -                                           | -                                           |
|                         | vmstorfl.sys   | 6.3.9600.18907 - KB4072650                | 6.3.9600.18080 - KB3063109                  | 6.3.9600.18907 - KB4072650          | 10.0.14393.2007 - KB4345418                 | 10.0.15063.850 - KB4345419 | 10.0.16299.371 - KB4345420                  | -                                           |
|                         | Fveapi.dll     | 6.1.7601.23311 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.18294 - KB3172614          | 10.0.14393.576 - KB4022715                  | -                          | -                                           | -                                           |
|                         | Fveapibase.dll | 6.1.7601.23403 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.17415 - KB3172614          | 10.0.14393.206 - KB4022715                  | -                          | -                                           | -                                           |
| 网络                 | netvsc.sys     | -                                         | -                                           | -                                   | 10.0.14393.1198 - KB4022715                 | 10.0.15063.250 - KB4020001 | -                                           | -                                           |
|                         | mrxsmb10.sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.22108 - KB4022724                  | 6.3.9600.18603 - KB4022726          | 10.0.14393.479 - KB4022715                  | 10.0.15063.483             | -                                           | -                                           |
|                         | mrxsmb20.sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.21548 - KB4022724                  | 6.3.9600.18586 - KB4022726          | 10.0.14393.953 - KB4022715                  | 10.0.15063.483             | -                                           | -                                           |
|                         | mrxsmb.sys     | 6.1.7601.23816 - KB4022722                | 6.2.9200.22074 - KB4022724                  | 6.3.9600.18586 - KB4022726          | 10.0.14393.953 - KB4022715                  | 10.0.15063.0               | -                                           | -                                           |
|                         | tcpip.sys      | 6.1.7601.23761 - KB4022722                | 6.2.9200.22070 - KB4022724                  | 6.3.9600.18478 - KB4022726          | 10.0.14393.1358 - KB4022715                 | 10.0.15063.447             | -                                           | -                                           |
|                         | http.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17285 - KB3042553                  | 6.3.9600.18574 - KB4022726          | 10.0.14393.251 - KB4022715                  | 10.0.15063.483             | -                                           | -                                           |
|                         | vmswitch.sys   | 6.1.7601.23727 - KB4022719                | 6.2.9200.22117 - KB4022724                  | 6.3.9600.18654 - KB4022726          | 10.0.14393.1358 - KB4022715                 | 10.0.15063.138             | -                                           | -                                           |
| 核心                    | ntoskrnl.exe   | 6.1.7601.23807 - KB4022719                | 6.2.9200.22170 - KB4022718                  | 6.3.9600.18696 - KB4022726          | 10.0.14393.1358 - KB4022715                 | 10.0.15063.483             | -                                           | -                                           |
| 远程桌面服务 | rdpcorets.dll  | 6.2.9200.21506 - KB4022719                | 6.2.9200.22104 - KB4022724                  | 6.3.9600.18619 - KB4022726          | 10.0.14393.1198 - KB4022715                 | 10.0.15063.0               | -                                           | -                                           |
|                         | termsrv.dll    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17048 - KB2973501                  | 6.3.9600.17415 - KB3000850          | 10.0.14393.0 - KB4022715                    | 10.0.15063.0               | -                                           | -                                           |
|                         | termdd.sys     | 6.1.7601.23403 - KB3125574                | -                                           | -                                   | -                                           | -                          | -                                           | -                                           |
|                         | win32k.sys     | 6.1.7601.23807 - KB4022719                | 6.2.9200.22168 - KB4022718                  | 6.3.9600.18698 - KB4022726          | 10.0.14393.594 - KB4022715                  | -                          | -                                           | -                                           |
|                         | rdpdd.dll      | 6.1.7601.23403 - KB3125574                | -                                           | -                                   | -                                           | -                          | -                                           | -                                           |
|                         | rdpwd.sys      | 6.1.7601.23403 - KB3125574                | -                                           | -                                   | -                                           | -                          | -                                           | -                                           |
| 安全性                | MS17-010       | KB4012212                                 | KB4012213                                   | KB4012213                           | KB4012606                                   | KB4012606                  | -                                           | -                                           |
|                         |                |                                           | KB4012216                                   |                                     | KB4013198                                   | KB4013198                  | -                                           | -                                           |
|                         |                | KB4012215                                 | KB4012214                                   | KB4012216                           | KB4013429                                   | KB4013429                  | -                                           | -                                           |
|                         |                |                                           | KB4012217                                   |                                     | KB4013429                                   | KB4013429                  | -                                           | -                                           |
|                         | CVE-2018-0886  | KB4103718                                 | KB4103730                                   | KB4103725                           | KB4103723                                   | KB4103731                  | KB4103727                                   | KB4103721                                   |
|                         |                | KB4103712                                 | KB4103726                                   | KB4103715                           |                                             |                            |                                             |                                             |

> [!NOTE]
> 为避免在 VM 预配期间意外重新启动，我们建议确保所有 Windows 更新安装均已完成，并且没有任何更新待处理。 完成此操作的一种方法是在运行 `sysprep.exe` 命令之前安装所有可能的 Windows 更新并重新启动一次。

## <a name="determine-when-to-use-sysprep"></a>确定何时使用 sysprep

系统准备工具 (`sysprep.exe`) 是一个可以重置 Windows 安装的进程。
Sysprep 会删除所有个人数据并重置多个组件，从而为你提供“全新安装”体验。

通常你会运行 `sysprep.exe` 来创建一个模板，然后从中部署多个具有特定配置的其他 VM。 该模板称为“通用化映像”。

若要只从一个磁盘创建一个 VM，则不需使用 Sysprep。 可以从专用化映像创建 VM。 有关如何从专用化磁盘创建 VM 的信息，请参阅：

- [从专用磁盘创建 VM](create-vm-specialized.md)
- [Create a VM from a specialized VHD disk](./create-vm-specialized-portal.md)（从专用 VHD 磁盘创建 VM）

若要创建通用化映像，则需运行 Sysprep。 有关详细信息，请参阅[如何使用 Sysprep：简介](/previous-versions/windows/it-pro/windows-xp/bb457073(v=technet.10))。

并非每个安装在基于 Windows 的计算机上的角色或应用程序都支持通用化映像。 因此，在使用此过程之前，请确保 Sysprep 支持该计算机的角色。 有关详细信息，请参阅 [Sysprep 对服务器角色的支持](/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles)。

特别是，Sysprep 要求在执行之前完全解密驱动器。 如果在 VM 上启用了加密，请在运行 Sysprep 之前将其禁用。


### <a name="generalize-a-vhd"></a>通用化 VHD

>[!NOTE]
> 在以下步骤中运行 `sysprep.exe` 后，请关闭 VM。 在 Azure 中从该 VM 创建映像之前，请不要将其重新打开。

1. 登录到 Windows VM。
1. 以管理员身份运行 PowerShell 会话。
1. 删除 panther 目录 (C:\Windows\Panther)。
1. 将目录切换到 `%windir%\system32\sysprep`。 然后运行 `sysprep.exe`。
1. 在“系统准备工具”对话框中，选择“进入系统全新体验(OOBE)”，并确保已选中“通用化”复选框。

    ![系统准备工具](media/prepare-for-upload-vhd-image/syspre.png)
1. 在“关机选项”中选择“关机”。
1. 选择“确定” 。
1. 当 Sysprep 完成后，关闭 VM。 请勿使用“重启”来关闭 VM。

现在，VHD 已准备就绪，可以上传了。 有关如何从通用化磁盘创建 VM 的详细信息，请参阅[上传通用化 VHD 并使用它在 Azure 中创建新的 VM](/previous-versions/azure/virtual-machines/windows/sa-upload-generalized)。

>[!NOTE]
> 不支持自定义的 *unattend.xml* 文件。 尽管我们确实支持 additionalUnattendContent 属性，但针对向 Azure 预配代理使用的 unattention.xml 文件添加 [microsoft-windows-shell-setup](/windows-hardware/customize/desktop/unattend/microsoft-windows-shell-setup) 选项，它只提供有限的支持。 例如，可以使用 [additionalUnattendContent](/dotnet/api/microsoft.azure.management.compute.models.additionalunattendcontent) 添加 FirstLogonCommands 和 LogonCommands。 有关详细信息，请参阅 [additionalUnattendContent FirstLogonCommands 示例](https://github.com/Azure/azure-quickstart-templates/issues/1407)。

## <a name="convert-the-virtual-disk-to-a-fixed-size-vhd"></a>将虚拟磁盘转换为固定大小的 VHD

使用本部分中的一个方法，将虚拟磁盘转换为 Azure 所需的格式并调整其大小：

1. 在运行虚拟磁盘转换或调整大小过程之前备份 VM。

1. 确保 Windows VHD 在本地服务器上正常工作。 尝试转换磁盘或将其上传到 Azure 之前，先解决 VM 本身内部的所有错误。

1. 将虚拟磁盘转换为固定类型。

1. 调整虚拟磁盘的大小以满足 Azure 要求：

   1. Azure 上的磁盘必须已将虚拟大小调整为 1 MiB。 如果 VHD 的大小不是 1 MiB 的整数倍，需要将磁盘大小调整为 1 MiB 的倍数。 基于上传的 VHD 创建映像时，不到 1 MiB 的磁盘将导致错误。 若要验证该大小，可使用 PowerShell [Get-VHD](/powershell/module/hyper-v/get-vhd) comdlet 来显示“大小”和“文件大小”，其中大小在 Azure 中必须是 1 MiB 的倍数，而文件大小将等于“大小”加上 VHD 页脚的 512 字节。
   
      ```powershell
      $vhd = Get-VHD -Path C:\test\MyNewVM.vhd
      $vhd.Size % 1MB
      0
      $vhd.FileSize - $vhd.Size
      512
      ```
   
   1. 第 1 代 VM 的 OS VHD 允许的最大大小为 2,048 GiB (2 TiB)， 
   1. 数据磁盘的最大大小为 32,767 GiB (32 TiB)。

> [!NOTE]
> - 如果要在转换为固定磁盘并根据需要调整大小后准备 Windows OS 磁盘，请创建使用该磁盘的 VM。 启动并登录到该 VM，然后继续根据本文内容，完成上传准备。  
> - 如果你正在准备数据磁盘，可以停止学习此部分，继续上传磁盘。

### <a name="use-hyper-v-manager-to-convert-the-disk"></a>使用 Hyper-V 管理器转换磁盘

1. 打开 Hyper-V 管理器，在左侧选择本地计算机。 在计算机列表上方的菜单中，选择“操作” > “编辑磁盘”。
1. 在“查找虚拟硬盘”页上，选择你的虚拟磁盘。
1. 在“选择操作”页上选择“转换” > “下一步”。
1. 若要从 VHDX 进行转换，请选择“VHD” > “下一步” 。
1. 若要从动态扩展磁盘进行转换，请选择“固定大小” > “下一步” 。
1. 找到并选择新 VHD 文件的保存路径。
1. 选择“完成”。

### <a name="use-powershell-to-convert-the-disk"></a>使用 PowerShell 转换磁盘

可以使用 PowerShell 中的 [Convert-VHD](/powershell/module/hyper-v/convert-vhd) cmdlet 转换虚拟磁盘。 如需了解如何安装此 cmdlet，请参阅[安装 Hyper-V 角色](/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server)。

以下示例将磁盘从 VHDX 转换为 VHD。 该示例还会将动态扩展磁盘转换为固定大小的磁盘。

```powershell
Convert-VHD -Path C:\test\MyVM.vhdx -DestinationPath C:\test\MyNewVM.vhd -VHDType Fixed
```

在此示例中，请将路径的值替换为要转换的虚拟硬盘的路径。 将 DestinationPath 的值替换为已转换的磁盘的新路径和名称。

### <a name="use-hyper-v-manager-to-resize-the-disk"></a>使用 Hyper-V 管理器调整磁盘大小

1. 打开 Hyper-V 管理器，在左侧选择本地计算机。 在计算机列表上方的菜单中，选择“操作” > “编辑磁盘”。
1. 在“查找虚拟硬盘”页上，选择你的虚拟磁盘。
1. 在“选择操作”页上选择“展开” > “下一步”  。
1. 在“查找虚拟硬盘”页上，以 GiB 为单位输入新的大小，然后选择“下一步” 。
1. 选择“完成”。

### <a name="use-powershell-to-resize-the-disk"></a>使用 PowerShell 调整磁盘大小

可以使用 PowerShell 中的 [Resize-VHD](/powershell/module/hyper-v/resize-vhd) cmdlet 调整虚拟磁盘的大小。 如需了解如何安装此 cmdlet，请参阅[安装 Hyper-V 角色](/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server)。

下面的示例将磁盘大小从 100.5 MiB 调整到 101 MiB，以满足 Azure 的一致性要求。

```powershell
Resize-VHD -Path C:\test\MyNewVM.vhd -SizeBytes 105906176
```

在此示例中，请将路径的值替换为要调整大小的虚拟硬盘的路径。 将 SizeBytes 的值替换为磁盘的新大小（以字节为单位）。

### <a name="convert-from-vmware-vmdk-disk-format"></a>从 VMware VMDK 磁盘格式转换

如果你有 Windows VM 映像采用 [VMDK 文件格式](https://en.wikipedia.org/wiki/VMDK)，则可使用 [Azure Migrate](../../migrate/server-migrate-overview.md) 转换 VMDK 并将其上传到 Azure。

## <a name="complete-the-recommended-configurations"></a>完成建议的配置

以下设置不影响 VHD 上传。 但是，强烈建议配置这些设置。

- 安装 [Azure 虚拟机代理](https://go.microsoft.com/fwlink/?LinkID=394789)。 然后即可启用 VM 扩展。 VM 扩展实现了可能需要用于 VM 的大多数关键功能。 例如，需要使用这些扩展来重置密码或配置 RDP。 有关详细信息，请参阅 [Azure 虚拟机代理概述](../extensions/agent-windows.md)。
- 在 Azure 中创建 VM 后，建议将 pagefile 置于临时驱动器卷以改进性能。 可按如下所示设置文件位置：

  ```powershell
  Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management' -Name PagingFiles -Value 'D:\pagefile.sys' -Type MultiString -Force
  ```

  如果某个数据磁盘已附加到 VM，则临时驱动器卷的驱动器号通常为 *D*。此驱动器号可能有所不同，具体取决于你的设置，以及可用驱动器的数目。

  - 我们建议禁用防病毒软件可能提供的脚本阻止程序。 这些阻止程序可能会干扰并阻止从映像部署新 VM 时执行的 Windows 预配代理脚本。

## <a name="next-steps"></a>后续步骤

- [将 Windows VM 映像上传到 Azure 以进行 Resource Manager 部署](upload-generalized-managed.md)
- [排查 Azure Windows VM 激活问题](/troubleshoot/azure/virtual-machines/troubleshoot-activation-problems)
