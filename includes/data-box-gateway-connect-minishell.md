---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: f9707ab48d64ede2e796675eb3a0a6e7820c7073
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102603230"
---
远程连接到设备的过程不同，具体取决于客户端的操作系统。

### <a name="remotely-connect-from-a-windows-client"></a>从 Windows 客户端远程连接

在开始之前，请确保 Windows 客户端运行的是 Windows PowerShell 5.0 或更高版本。

请按照以下步骤从 Windows 客户端进行远程连接。

1. 以管理员身份运行 Windows PowerShell 会话。
2. 确保 Windows 远程管理服务正在客户端上运行。 在命令提示符处，键入：

    `winrm quickconfig`

3. 为设备 IP 地址分配一个变量。

    $ip = "<device_ip>"

    将 `<device_ip>` 替换为设备的 IP 地址。

4. 若要将设备的 IP 地址添加到客户端的受信任主机列表，请键入以下命令：

    `Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force`

5. 在设备上启动 Windows PowerShell 会话：

    `Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell`

6. 根据提示提供密码。 使用登录到本地 Web UI 时所用的同一密码。 默认的本地 Web UI 密码为 Password1。 使用远程 PowerShell 成功连接到设备后，可以看到以下示例输出：  

    ```
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    PS C:\WINDOWS\system32> winrm quickconfig
    WinRM service is already running on this machine.
    PS C:\WINDOWS\system32> $ip = "10.100.10.10"
    PS C:\WINDOWS\system32> Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force
    PS C:\WINDOWS\system32> Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell

    WARNING: The Windows PowerShell interface of your device is intended to be used only for the initial network configuration. Please engage Microsoft Support if you need to access this interface to troubleshoot any potential issues you may be experiencing. Changes made through this interface without involving Microsoft Support could result in an unsupported configuration.
    [10.100.10.10]: PS>
    ```

### <a name="remotely-connect-from-a-linux-client"></a>从 Linux 客户端远程连接

在将用于连接的 Linux 客户端上：

- 从 GitHub [安装最新的适用于 Linux 的 PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-linux) 以获取 SSH 远程处理功能。 
- [只安装 NTLM 模块中的 `gss-ntlmssp` 包](https://github.com/Microsoft/omi/blob/master/Unix/doc/setup-ntlm-omi.md)。 对于 Ubuntu 客户端，请使用以下命令：
    - `sudo apt-get install gss-ntlmssp`

有关详细信息，请参阅[通过 SSH 进行 PowerShell 远程处理](/powershell/scripting/learn/remoting/ssh-remoting-in-powershell-core)。

请按照以下步骤从 NFS 客户端进行远程连接。

1. 若要打开 PowerShell 会话，请键入：

    `pwsh`
 
2. 若要使用远程客户端进行连接，请键入：

    `Enter-PSSession -ComputerName $ip -Authentication Negotiate -ConfigurationName Minishell -Credential ~\EdgeUser`

    出现提示时，提供用于登录设备的密码。
 
> [!NOTE]
> 此过程不适用于 macOS。