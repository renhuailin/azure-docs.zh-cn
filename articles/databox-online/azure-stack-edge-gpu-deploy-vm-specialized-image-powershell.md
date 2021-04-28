---
title: 从 Windows VHD 的专用映像为 Azure Stack Edge Pro GPU 设备创建 VM 映像
description: 介绍如何从 Windows VHD 或 VHDX 开始从专用映像创建 VM 映像。 使用此专用映像创建与 Azure Stack Edge Pro GPU 设备上部署的 VM 配合使用的 VM 映像。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 04/15/2021
ms.author: alkohli
ms.openlocfilehash: 6bfa42e99f295b429eba40a27eb59becb8aa80a1
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575934"
---
# <a name="deploy-a-vm-from-a-specialized-image-on-your-azure-stack-edge-pro-gpu-device-via-azure-powershell"></a>通过 Azure PowerShell 在 Azure Stack Edge Pro GPU 设备上从专用映像部署 VM 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

本文介绍从专用映像在 Azure Stack Edge Pro GPU 设备上部署虚拟机 (VM) 所需的步骤。 

若要准备通用映像以在 Azure Stack Edge Pro GPU 中部署 VM，请参阅[从 Windows VHD 准备通用映像](azure-stack-edge-gpu-prepare-windows-vhd-generalized-image.md)或[从 ISO 准备通用映像](azure-stack-edge-gpu-prepare-windows-generalized-image-iso.md)。

## <a name="about-vm-images"></a>关于 VM 映像

可以使用 Windows VHD 或 VHDX 创建专用映像或通用映像 。 下表汇总了专用映像与通用映像之间的主要差异 。

[!INCLUDE [about-vm-images-for-azure-stack-edge](../../includes/azure-stack-edge-about-vm-images.md)]

## <a name="workflow"></a>工作流

从专用映像部署 VM 的概要工作流是：

1. 将 VHD 复制到 Azure Stack Edge Pro GPU 设备上的本地存储帐户。
1. 从 VHD 创建新的托管磁盘。
1. 从托管磁盘创建新的虚拟机，并附加托管磁盘。

## <a name="prerequisites"></a>必备条件

在通过 PowerShell 在设备上部署 VM 之前，请确保：

- 你可以访问要用于连接到设备的客户端。
    - 客户端运行的是[受支持的操作系统](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device)。
    - 按照[连接到设备的 Azure 资源管理器](azure-stack-edge-gpu-connect-resource-manager.md)中的说明，将客户端配置为连接到设备的本地 Azure 资源管理器。

## <a name="verify-the-local-azure-resource-manager-connection"></a>验证本地 Azure 资源管理器连接

验证客户端是否可以连接到本地 Azure 资源管理器。 

1. 调用本地设备 API 进行身份验证：

    ```powershell
    Login-AzureRMAccount -EnvironmentName <Environment Name>
    ```

2. 提供用户名 `EdgeArmUser` 和密码以通过 Azure 资源管理器进行连接。 如果你忘记了密码，请[重置 Azure 资源管理器的密码](azure-stack-edge-gpu-set-azure-resource-manager-password.md)，然后使用此密码登录。

## <a name="deploy-vm-from-specialized-image"></a>从专用映像部署 VM

以下部分包含从专用映像部署 VM 的分步说明。

## <a name="copy-vhd-to-local-storage-account-on-device"></a>将 VHD 复制到设备上的本地存储帐户

按照以下步骤将 VHD 复制到本地存储帐户：

1. 将源 VHD 复制到 Azure Stack Edge 上的本地 Blob 存储帐户。

1. 记下生成的 URI。 在稍后的步骤中将要使用此 URI。

    若要创建和访问本地存储帐户，请参阅[通过 Azure PowerShell 在 Azure Stack Edge 设备上部署 VM](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md) 一文中的[创建存储帐户](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#create-a-storage-account)到[上传 VHD](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#upload-a-vhd) 的每个部分。 

## <a name="create-a-managed-disk-from-vhd"></a>从 VHD 创建托管磁盘

按照以下步骤，从之前已上传到存储帐户的 VHD 创建一个托管磁盘：

1. 设置一些参数。

    ```powershell
    $VhdURI = <URI of VHD in local storage account>
    $DiskRG = <managed disk resource group>
    $DiskName = <managed disk name>    
    ```
    下面是示例输出。

    ```powershell
    PS C:\WINDOWS\system32> $VHDURI = "https://myasevmsa.blob.myasegpudev.wdshcsso.com/vhds/WindowsServer2016Datacenter.vhd"
    PS C:\WINDOWS\system32> $DiskRG = "myasevm1rg"
    PS C:\WINDOWS\system32> $DiskName = "myasemd1"
    ```
1. 创建新的托管磁盘。

    ```powershell
    $DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import -SourceUri $VhdURI
    $disk = New-AzureRMDisk -ResourceGroupName $DiskRG -DiskName $DiskName -Disk $DiskConfig
    ```

    下面是示例输出。 此处的位置设置为本地存储帐户的位置，对于 Azure Stack Edge Pro GPU 设备上的所有本地存储帐户，该位置始终是 `DBELocal`。 

    ```powershell
    PS C:\WINDOWS\system32> $DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import -SourceUri $VHDURI
    PS C:\WINDOWS\system32> $disk = New-AzureRMDisk -ResourceGroupName $DiskRG -DiskName $DiskName -Disk $DiskConfig
    PS C:\WINDOWS\system32>    
    ```
## <a name="create-a-vm-from-managed-disk"></a>从托管磁盘创建 VM

按照以下步骤从托管磁盘创建 VM：

1. 设置一些参数。

    ```powershell
    $NicRG = <NIC resource group>
    $NicName = <NIC name>
    $IPConfigName = <IP config name>
    $PrivateIP = <IP address> #Optional
    
    $VMRG = <VM resource group>
    $VMName = <VM name>
    $VMSize = <VM size> 
    ```

    >[!NOTE]
    > `PrivateIP` 参数是可选的。 使用此参数分配静态 IP，否则默认设置是使用 DHCP 的动态 IP。

    下面是示例输出。 在此示例中，为所有 VM 资源指定了同一个资源组，不过，如果需要，你可以为资源创建并指定单独的资源组。

    ```powershell
    PS C:\WINDOWS\system32> $NicRG = "myasevm1rg"
    PS C:\WINDOWS\system32> $NicName = "myasevmnic1"
    PS C:\WINDOWS\system32> $IPConfigName = "myaseipconfig1" 

    PS C:\WINDOWS\system32> $VMRG = "myasevm1rg"
    PS C:\WINDOWS\system32> $VMName = "myasetestvm1"
    PS C:\WINDOWS\system32> $VMSize = "Standard_D1_v2"   
    ```

1. 获取虚拟网络信息并创建新的网络接口。

    此示例假设你要在与默认资源组 `ASERG` 关联的默认虚拟网络 `ASEVNET` 上创建单个网络接口。 如果需要，可以指定备用虚拟网络，或创建多个网络接口。 有关详细信息，请参阅[通过 Azure 门户将网络接口添加到 VM](azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal.md)。

    ```powershell
    $armVN = Get-AzureRMVirtualNetwork -Name ASEVNET -ResourceGroupName ASERG
    $ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName -SubnetId $armVN.Subnets[0].Id [-PrivateIpAddress $PrivateIP]
    $nic = New-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $NicRG -Location DBELocal -IpConfiguration $ipConfig
    ```

    下面是示例输出。

    ```powershell
    PS C:\WINDOWS\system32> $armVN = Get-AzureRMVirtualNetwork -Name ASEVNET -ResourceGroupName ASERG
    PS C:\WINDOWS\system32> $ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName -SubnetId $armVN.Subnets[0].Id
    PS C:\WINDOWS\system32> $nic = New-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $NicRG -Location DBELocal -IpConfiguration $ipConfig
    WARNING: The output object type of this cmdlet will be modified in a future release.
    PS C:\WINDOWS\system32>    
    ```
1. 创建新的 VM 配置对象。

    ```powershell
    $vmConfig = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
    ```

    
1. 向 VM 添加网络接口。

    ```powershell
    $vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
    ```

1. 在 VM 上设置 OS 磁盘属性。

    ```powershell
    $vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $disk.Id -StorageAccountType StandardLRS -CreateOption Attach –[Windows/Linux]
    ```
    此命令中的最后一个标志将是 `-Windows` 或 `-Linux`，具体取决于要为 VM 使用的 OS。

1. 创建 VM。

    ```powershell
    New-AzureRmVM -ResourceGroupName $VMRG -Location DBELocal -VM $vm 
    ```

    下面是示例输出。 

    ```powershell
    PS C:\WINDOWS\system32> $vmConfig = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
    PS C:\WINDOWS\system32> $vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
    PS C:\WINDOWS\system32> $vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $disk.Id -StorageAccountType StandardLRS -CreateOption Attach -Windows
    PS C:\WINDOWS\system32> New-AzureRmVM -ResourceGroupName $VMRG -Location DBELocal -VM $vm
    WARNING: Since the VM is created using premium storage or managed disk, existing standard storage account, myasevmsa, is used for
    boot diagnostics.    
    RequestId IsSuccessStatusCode StatusCode ReasonPhrase
    --------- ------------------- ---------- ------------
                             True         OK OK        
    PS C:\WINDOWS\system32>
    ```

## <a name="delete-vm-and-resources"></a>删除 VM 和资源

本文仅使用了一个资源组来创建所有 VM 资源。 删除该资源组会删除 VM 和所有关联的资源。 

1. 首先，查看在该资源组下创建的所有资源。

    ```powershell
    Get-AzureRmResource -ResourceGroupName <Resource group name>
    ```
    下面是示例输出。
    
    ```powershell
    PS C:\WINDOWS\system32> Get-AzureRmResource -ResourceGroupName myasevm1rg
    
    
    Name              : myasemd1
    ResourceGroupName : myasevm1rg
    ResourceType      : Microsoft.Compute/disks
    Location          : dbelocal
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myasevm1rg/providers/Microsoft.Compute/disk
                        s/myasemd1
    
    Name              : myasetestvm1
    ResourceGroupName : myasevm1rg
    ResourceType      : Microsoft.Compute/virtualMachines
    Location          : dbelocal
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myasevm1rg/providers/Microsoft.Compute/virt
                        ualMachines/myasetestvm1
    
    Name              : myasevmnic1
    ResourceGroupName : myasevm1rg
    ResourceType      : Microsoft.Network/networkInterfaces
    Location          : dbelocal
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myasevm1rg/providers/Microsoft.Network/netw
                        orkInterfaces/myasevmnic1
    
    Name              : myasevmsa
    ResourceGroupName : myasevm1rg
    ResourceType      : Microsoft.Storage/storageaccounts
    Location          : dbelocal
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myasevm1rg/providers/Microsoft.Storage/stor
                        ageaccounts/myasevmsa
    
    PS C:\WINDOWS\system32>
    ```

1. 删除该资源组和所有关联的资源。

    ```powershell
    Remove-AzureRmResourceGroup -ResourceGroupName <Resource group name>
    ```
    下面是示例输出。
    
    ```powershell
    PS C:\WINDOWS\system32> Remove-AzureRmResourceGroup -ResourceGroupName myasevm1rg
    
    Confirm
    Are you sure you want to remove resource group 'myasevm1rg'
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
    True
    PS C:\WINDOWS\system32>
    ```

1. 验证是否已删除该资源组。 获取设备上存在的所有资源组。 

    ```powershell
    Get-AzureRmResourceGroup
    ```
    下面是示例输出。

    ```powershell
    PS C:\WINDOWS\system32> Get-AzureRmResourceGroup

    ResourceGroupName : ase-image-resourcegroup
    Location          : dbelocal
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/ase-image-resourcegroup
    
    ResourceGroupName : ASERG
    Location          : dbelocal
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/ASERG
    
    ResourceGroupName : myaserg
    Location          : dbelocal
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myaserg
        
    PS C:\WINDOWS\system32>
    ```

## <a name="next-steps"></a>后续步骤

- [从 Windows VHD 准备通用映像，以在 Azure Stack Edge Pro GPU 上部署 VM](azure-stack-edge-gpu-prepare-windows-vhd-generalized-image.md)
- [从 ISO 准备通用映像，以在 Azure Stack Edge Pro GPU 上部署 VM](azure-stack-edge-gpu-prepare-windows-generalized-image-iso.md)