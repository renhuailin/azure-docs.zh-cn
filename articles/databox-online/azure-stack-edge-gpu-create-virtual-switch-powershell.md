---
title: 通过 PowerShell 在 Azure Stack Edge 中创建新的虚拟交换机
description: 描述如何使用 PowerShell 在 Azure Stack Edge 设备上创建虚拟机。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 04/06/2021
ms.author: alkohli
ms.openlocfilehash: 1ad86695510a8fe93bbeeab27db53f5afbef92fd
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/07/2021
ms.locfileid: "106555866"
---
# <a name="create-a-new-virtual-switch-in-azure-stack-edge-pro-gpu-via-powershell"></a>通过 PowerShell 在 Azure Stack Edge Pro GPU 中创建新的虚拟交换机

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

本文介绍如何在 Azure Stack Edge Pro GPU 设备上创建新的虚拟交换机。 例如，如果希望虚拟机通过其他物理网络端口进行连接，则需创建新的虚拟交换机。

## <a name="vm-deployment-workflow"></a>VM 部署工作流

1. 连接到设备上的 PowerShell 接口。
2. 查询可用的物理网络接口。
3. 创建虚拟交换机中配置的虚拟交换机。
4. 验证自动创建的虚拟网络和子网。

## <a name="prerequisites"></a>先决条件

在开始之前，请确保：

- 你可以访问客户端计算机，该计算机可以访问你设备的 PowerShell 接口。 参阅[连接到 PowerShell 接口](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface)。 

    客户端计算机应运行[支持的操作系统](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device)。

- 根据在设备上[启用计算网络](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#enable-compute-network)中的说明，使用本地 UI 在设备上的一个物理网络接口上启用计算。 


## <a name="connect-to-the-powershell-interface"></a>连接到 PowerShell 接口

[连接到设备的 PowerShell 界面](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface)。

## <a name="query-available-network-interfaces"></a>查询可用网络接口

1. 使用以下命令显示可创建新虚拟交换机的物理网络接口的列表。 你将选择其中一个网络接口。

    ```powershell
    Get-NetAdapter -Physical
    ```
    下面是示例输出：
    
    ```powershell
        [10.100.10.10]: PS>Get-NetAdapter -Physical
        
        Name                      InterfaceDescription                    ifIndex Status       MacAddress       LinkSpeed
        ----                      --------------------                    ------- ------       ----------        -----
        Port2                     QLogic 2x1GE+2x25GE QL41234HMCU NIC ...      12 Up           34-80-0D-05-26-EA ...ps
        Ethernet                  Remote NDIS Compatible Device                11 Up           F4-02-70-CD-41-39 ...ps
        Port1                     QLogic 2x1GE+2x25GE QL41234HMCU NI...#3       9 Up           34-80-0D-05-26-EB ...ps
        Port5                     Mellanox ConnectX-4 Lx Ethernet Ad...#2       8 Up           0C-42-A1-C0-E3-99 ...ps
        Port3                     QLogic 2x1GE+2x25GE QL41234HMCU NI...#4       7 Up           34-80-0D-05-26-E9 ...ps
        Port6                     Mellanox ConnectX-4 Lx Ethernet Adapter       6 Up           0C-42-A1-C0-E3-98 ...ps
        Port4                     QLogic 2x1GE+2x25GE QL41234HMCU NI...#2       4 Up           34-80-0D-05-26-E8 ...ps
        
        [10.100.10.10]: PS>
    ```
2. 选择一个网络接口，该接口：

    - 处于“启动”状态。 
    - 未用于任何现有虚拟交换机。 目前只能为每个网络接口配置一个 vSwitch。 
    
    若要检查现有虚拟交换机和网络接口关联，请运行 `Get-HcsExternalVirtualSwitch` 命令。
 
    下面是示例输出。

    ```powershell
    [10.100.10.10]: PS>Get-HcsExternalVirtualSwitch

    Name                          : vSwitch1
    InterfaceAlias                : {Port2}
    EnableIov                     : True
    MacAddressPools               :
    IPAddressPools                : {}
    ConfigurationSource           : Dsc
    EnabledForCompute             : True
    SupportsAcceleratedNetworking : False
    DbeDhcpHostVnicName           : f4a92de8-26ed-4597-a141-cb233c2ba0aa
    Type                          : External
    
    [10.100.10.10]: PS>
    ```
    在此实例中，端口 2 与现有虚拟交换机相关联，不应被使用。

## <a name="create-a-virtual-switch"></a>创建虚拟交换机

使用以下 cmdlet 在指定的网络接口上创建新的虚拟交换机。 完成此操作后，计算实例可以使用新的虚拟网络。

```powershell
Add-HcsExternalVirtualSwitch -InterfaceAlias <Network interface name> -WaitForSwitchCreation $true
```

使用 `Get-HcsExternalVirtualSwitch` 命令识别新建的交换机。 创建的新交换机名称为 `vswitch-<InterfaceAlias>`。 

下面是示例输出：

```powershell
[10.100.10.10]: P> Add-HcsExternalVirtualSwitch -InterfaceAlias Port5 -WaitForSwitchCreation $true
[10.100.10.10]: PS>Get-HcsExternalVirtualSwitch

Name                          : vSwitch1
InterfaceAlias                : {Port2}
EnableIov                     : True
MacAddressPools               :
IPAddressPools                : {}
ConfigurationSource           : Dsc
EnabledForCompute             : True
SupportsAcceleratedNetworking : False
DbeDhcpHostVnicName           : f4a92de8-26ed-4597-a141-cb233c2ba0aa
Type                          : External

Name                          : vswitch-Port5
InterfaceAlias                : {Port5}
EnableIov                     : True
MacAddressPools               :
IPAddressPools                :
ConfigurationSource           : Dsc
EnabledForCompute             : False
SupportsAcceleratedNetworking : False
DbeDhcpHostVnicName           : 9b301c40-3daa-49bf-a20b-9f7889820129
Type                          : External

[10.100.10.10]: PS>
```

## <a name="verify-network-subnet"></a>验证网络、子网 

创建新的虚拟交换机后，Azure Stack Edge Pro GPU 会自动创建对应的虚拟网络和子网。 创建 VM 时，可以使用此虚拟网络。

<!--To identify the virtual network and subnet associated with the new switch that you created, use the `Get-HcsVirtualNetwork` command. This cmdlet will be released in April some time. -->

## <a name="next-steps"></a>后续步骤

- [通过 Azure PowerShell 在 Azure Stack Edge Pro GPU 设备上部署 VM](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)

- [通过 Azure 门户在 Azure Stack Edge Pro GPU 设备上部署 VM](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)
