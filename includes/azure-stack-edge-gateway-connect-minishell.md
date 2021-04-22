---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/30/2021
ms.author: alkohli
ms.openlocfilehash: 89e648099a5ac6d905f475319cc108dd0d05a6e9
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2021
ms.locfileid: "106081142"
---
远程连接到设备的过程不同，具体取决于客户端的操作系统。

### <a name="remotely-connect-from-a-windows-client"></a>从 Windows 客户端远程连接


#### <a name="prerequisites"></a>先决条件

在开始之前，请确保：

- Windows 客户端运行 Windows PowerShell 5.0 或更高版本。
- 你的 Windows 客户端的签名链（根证书）与设备上安装的节点证书相对应。 有关详细说明，请参阅[在 Windows 客户端上安装证书](../articles/databox-online/azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device)。
- `hosts` 文件位于 Windows 客户端的 `C:\Windows\System32\drivers\etc`，具有与节点证书对应的条目，格式如下：

    `<Device IP>    <Node serial number>.<DNS domain of the device>`

    下面是 `hosts` 文件的示例条目：
 
    `10.100.10.10    1HXQG13.wdshcsso.com`
  

#### <a name="detailed-steps"></a>详细步骤

请按照以下步骤从 Windows 客户端进行远程连接。

1. 以管理员身份运行 Windows PowerShell 会话。
2. 确保 Windows 远程管理服务正在客户端上运行。 在命令提示符处，键入：

    `winrm quickconfig`

    有关详细信息，请参阅 [Windows 远程管理的安装和配置](/windows/win32/winrm/installation-and-configuration-for-windows-remote-management#quick-default-configuration)。

3. 为设备 IP 地址分配一个变量。

    $ip = "<device_ip>"

    将 `<device_ip>` 替换为设备的 IP 地址。

4. 若要将设备的 IP 地址添加到客户端的受信任主机列表，请键入以下命令：

    `Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force`

5. 在设备上启动 Windows PowerShell 会话：

    `Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell -UseSSL`

    如果看到与信任关系相关的错误，请检查上传到你的设备的节点证书的签名链是否也安装在访问设备的客户端上。

    如果使用的不是证书（建议使用证书！），可使用会话选项跳过此检查：`-SkipCACheck -SkipCNCheck -SkipRevocationCheck`。

    ```powershell
    $sessOptions = New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck 
    Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell -UseSSL -SessionOption $sessOptions    
    ```

    > [!NOTE] 
    > 使用 `-UseSSL` 选项时，将在 https 上通过 PowerShell 进行远程处理。 建议始终使用 https 通过 PowerShell 进行远程连接。 

6. 根据提示提供密码。 使用登录到本地 Web UI 时所用的同一密码。 默认的本地 Web UI 密码为 Password1。 使用远程 PowerShell 成功连接到设备后，可以看到以下示例输出：  

    ```
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    PS C:\WINDOWS\system32> winrm quickconfig
    WinRM service is already running on this machine.
    PS C:\WINDOWS\system32> $ip = "10.100.10.10"
    PS C:\WINDOWS\system32> Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force
    PS C:\WINDOWS\system32> Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell -UseSSL

    WARNING: The Windows PowerShell interface of your device is intended to be used only for the initial network configuration. Please engage Microsoft Support if you need to access this interface to troubleshoot any potential issues you may be experiencing. Changes made through this interface without involving Microsoft Support could result in an unsupported configuration.
    [10.100.10.10]: PS>
    ```

> [!IMPORTANT]
> 在当前版本中，只能通过 Windows 客户端连接到设备的 PowerShell 接口。 `-UseSSL` 选项不适用于 Linux 客户端。

<!--### Remotely connect from a Linux client-->

<!--On the Linux client that you'll use to connect:

- [Install the latest PowerShell Core for Linux](/powershell/scripting/install/installing-powershell-core-on-linux) from GitHub to get the SSH remoting feature. 
- [Install only the `gss-ntlmssp` package from the NTLM module](https://github.com/Microsoft/omi/blob/master/Unix/doc/setup-ntlm-omi.md). For Ubuntu clients, use the following command:
    - `sudo apt-get install gss-ntlmssp`

For more information, go to [PowerShell remoting over SSH](/powershell/scripting/learn/remoting/ssh-remoting-in-powershell-core).

Follow these steps to remotely connect from an NFS client.

1. To open PowerShell session, type:

    `pwsh`
 
2. For connecting using the remote client, type:

    `Enter-PSSession -ComputerName $ip -Authentication Negotiate -ConfigurationName Minishell -Credential ~\EdgeUser`

    When prompted, provide the password used to sign into your device.
 
> [!NOTE]
> This procedure does not work on Mac OS.-->