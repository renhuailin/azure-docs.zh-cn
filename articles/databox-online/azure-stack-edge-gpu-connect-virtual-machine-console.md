---
title: 连接到 Azure Stack Edge Pro GPU 设备上的虚拟机控制台
description: 介绍如何连接到在 Azure Stack Edge Pro GPU 设备上运行的 VM 上的虚拟机控制台。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 06/04/2021
ms.author: alkohli
ms.openlocfilehash: 5bcd23c9a515155b9dc7cfdc04a3fafe122474ae
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111986347"
---
# <a name="connect-to-a-virtual-machine-console-on-an-azure-stack-edge-pro-gpu-device"></a>连接到 Azure Stack Edge Pro GPU 设备上的虚拟机控制台

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Stack Edge Pro GPU 解决方案通过虚拟机运行非容器化工作负载。 本文介绍如何连接到设备上部署的虚拟机的控制台。 

利用虚拟机控制台，可以使用常用远程桌面工具通过键盘、鼠标和屏幕功能来访问 VM。 可以访问控制台并排查在设备上部署虚拟机时遇到的任何问题。 即使 VM 预配失败，你也可以连接到虚拟机控制台。


## <a name="workflow"></a>工作流

大致工作流包括以下步骤：

1. 连接到设备上的 PowerShell 接口。
1. 启用对 VM 的控制台访问。
1. 使用远程桌面协议 (RDP)连接到 VM。
1. 撤销对 VM 的控制台访问。

## <a name="prerequisites"></a>先决条件

在开始之前，请确保已经具备了以下先决条件：

#### <a name="for-your-device"></a>对于你的设备

应该有权访问已激活的 Azure Stack Edge Pro GPU 设备。 设备上必须已经部署了一个或多个 VM。 可以通过 Azure PowerShell、通过模板，或通过 Azure 门户来部署 VM。

#### <a name="for-client-accessing-the-device"></a>对于访问设备的客户端

确保你有权访问符合以下条件的客户端系统：

- 可以访问设备的 PowerShell 界面。 该客户端运行[受支持的操作系统](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device)。
- 该客户端运行 PowerShell 7.0 或更高版本。 此版本的 PowerShell 适用于 Windows、Mac 和 Linux 客户端。 请参阅说明来[安装 PowerShell 7.0](/powershell/scripting/whats-new/what-s-new-in-powershell-70?view=powershell-7.1&preserve-view=true)。
- 具有远程桌面功能。 根据你使用的是 Windows、macOS 还是 Linux，应该安装这些[远程桌面客户端](/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)之一。 本文提供了有关 [Windows 远程桌面](/windows-server/remote/remote-desktop-services/clients/windowsdesktop#install-the-client)和 [FreeRDP](https://www.freerdp.com/) 的说明。 <!--Which version of FreeRDP to use?-->


## <a name="connect-to-vm-console"></a>连接到 VM 控制台

请按照以下步骤连接到设备上的虚拟机控制台。

### <a name="connect-to-the-powershell-interface-on-your-device"></a>连接到设备上的 PowerShell 接口

第一步是[连接到设备的 PowerShell 界面](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface)。 

### <a name="enable-console-access-to-the-vm"></a>启用对 VM 的控制台访问

1.  在 PowerShell 界面中，运行以下命令来启用对 VM 控制台的访问。

    ```powershell
    Grant-HcsVMConnectAccess -ResourceGroupName <VM resource group> -VirtualMachineName <VM name>
    ```
2. 在示例输出中，记下虚拟机 ID。 在后面的步骤中会需要用到此信息。

    ```powershell
    [10.100.10.10]: PS>Grant-HcsVMConnectAccess -ResourceGroupName mywindowsvm1rg -VirtualMachineName mywindowsvm1
        
    VirtualMachineId       : 81462e0a-decb-4cd4-96e9-057094040063
    VirtualMachineHostName : 3V78B03
    ResourceGroupName      : mywindowsvm1rg
    VirtualMachineName     : mywindowsvm1
    Id                     : 81462e0a-decb-4cd4-96e9-057094040063
    [10.100.10.10]: PS>
    ```

### <a name="connect-to-the-vm"></a>连接到 VM

现在可以使用远程桌面客户端连接到虚拟机控制台了。

#### <a name="use-windows-remote-desktop"></a>使用 Windows 远程桌面

1. 创建新文本文件并输入以下文本。

    ```
    pcb:s:<VM ID from PowerShell>;EnhancedMode=0
    full address:s:<IP address of the device>   
    server port:i:2179
    username:s:EdgeARMUser
    negotiate security layer:i:0
    ```
1. 将该文件在客户端系统上另存为 *.rdp。 你将使用此配置文件来连接到 VM。
1. 双击该配置文件来连接到 VM。 提供以下凭据：

    - 用户名：以 EdgeARMUser 身份登录。
    - 密码：提供设备的本地 Azure 资源管理器密码。 如果忘记了密码，请[通过 Azure 门户重置 Azure 资源管理器密码](azure-stack-edge-gpu-set-azure-resource-manager-password.md#reset-password-via-the-azure-portal)。 

#### <a name="use-freerdp"></a>使用 FreeRDP

如果在 Linux 客户端上使用 FreeRDP，请运行以下命令： 

```powershell
./wfreerdp /u:EdgeARMUser /vmconnect:<VM ID from PowerShell> /v:<IP address of the device>
```

## <a name="revoke-vm-console-access"></a>撤销 VM 控制台访问权限

若要撤销对 VM 控制台的访问权限，请返回到设备的 PowerShell 界面。 运行以下命令：

```
Revoke-HcsVMConnectAccess -ResourceGroupName <VM resource group> -VirtualMachineName <VM name>
```
下面是示例输出：

```powershell
[10.100.10.10]: PS>Revoke-HcsVMConnectAccess -ResourceGroupName mywindowsvm1rg -VirtualMachineName mywindowsvm1

VirtualMachineId       : 81462e0a-decb-4cd4-96e9-057094040063
VirtualMachineHostName : 3V78B03
ResourceGroupName      : mywindowsvm1rg
VirtualMachineName     : mywindowsvm1
Id                     : 81462e0a-decb-4cd4-96e9-057094040063

[10.100.10.10]: PS>
```
> [!NOTE] 
> 在使用 VM 控制台完成操作后，建议撤销访问权限，或关闭 PowerShell 窗口以退出该会话。 

## <a name="next-steps"></a>后续步骤

- 在 Azure 门户中排查 [VM 部署](azure-stack-edge-gpu-troubleshoot-virtual-machine-provisioning.md)问题。
<!--Make "VM guest logs" first link when article is available.-->