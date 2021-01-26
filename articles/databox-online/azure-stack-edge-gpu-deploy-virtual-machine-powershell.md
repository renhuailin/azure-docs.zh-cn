---
title: 通过 Azure PowerShell 在 Azure Stack Edge Pro GPU 设备上部署 Vm
description: 介绍如何使用 Azure PowerShell 在 Azure Stack Edge Pro GPU 设备上创建和管理 (Vm) 的虚拟机。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 01/22/2021
ms.author: alkohli
ms.openlocfilehash: 1d286e7661fa14dd63bd55b133c39414e04decc6
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2021
ms.locfileid: "98802992"
---
# <a name="deploy-vms-on-your-azure-stack-edge-pro-gpu-device-via-azure-powershell"></a>通过 Azure PowerShell 在 Azure Stack Edge Pro GPU 设备上部署 Vm

本文介绍如何使用 Azure PowerShell 在 Azure Stack Edge Pro 设备上创建和管理 VM。 本文适用于 Azure Stack Edge Pro GPU、Azure Stack Edge Pro R 和 Azure Stack 边缘迷你 R 设备。

## <a name="vm-deployment-workflow"></a>VM 部署工作流

下图演示了此部署工作流。

![VM 部署工作流](media/azure-stack-edge-gpu-deploy-virtual-machine-powershell/vm-workflow-r.svg)

## <a name="prerequisites"></a>先决条件

[!INCLUDE [azure-stack-edge-gateway-deploy-vm-prerequisites](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-prerequisites.md)]


## <a name="query-for-built-in-subscription-on-the-device"></a>在设备上查询内置订阅

对于 Azure 资源管理器，仅支持单个用户可见的固定订阅。 此订阅对于每个设备都是唯一的，因此无法更改订阅名称或订阅 ID。

此订阅包含创建 VM 所需的所有资源。 

> [!IMPORTANT]
> 此订阅是在 Azure 门户启用 Vm 时创建的，它在本地位于你的设备上。

此订阅用于部署 Vm。

1.  若要列出此订阅，请键入：

    ```powershell
    Get-AzureRmSubscription
    ```
    
    下面显示了示例输出。

    ```powershell
    PS C:\windows\system32> Get-AzureRmSubscription
    
    Name                 Id                 TenantId          State
    ----                 --                --------           -----
    Default Provider Subscription A4257FDE-B946-4E01-ADE7-674760B8D1A3 c0257de7-538f-415c-993a-1b87a031879d Enabled
    
    PS C:\windows\system32>
    ```
        
3.  获取在设备上运行的已注册资源提供程序的列表。 此列表通常包括计算、网络和存储。

    ```powershell
    Get-AzureRMResourceProvider
    ```

    > [!NOTE]
    > 资源提供程序是预先注册的，不能修改或更改。
    
    下面显示了示例输出：

    ```powershell
    Get-AzureRmResourceProvider
    ProviderNamespace : Microsoft.Compute
    RegistrationState : Registered
    ResourceTypes     : {virtualMachines, virtualMachines/extensions, locations, operations...}
    Locations         : {DBELocal}
    ZoneMappings      :
    
    ProviderNamespace : Microsoft.Network
    RegistrationState : Registered
    ResourceTypes     : {operations, locations, locations/operations, locations/usages...}
    Locations         : {DBELocal}
    ZoneMappings      :
    
    ProviderNamespace : Microsoft.Resources
    RegistrationState : Registered
    ResourceTypes     : {tenants, locations, providers, checkresourcename...}
    Locations         : {DBELocal}
    ZoneMappings      :
    
    ProviderNamespace : Microsoft.Storage
    RegistrationState : Registered
    ResourceTypes     : {storageaccounts, storageAccounts/blobServices, storageAccounts/tableServices,
                        storageAccounts/queueServices...}
    Locations         : {DBELocal}
    ZoneMappings      :
    ```
    
## <a name="create-a-resource-group"></a>创建资源组

使用 [New-AzureRmResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 创建 Azure 资源组。 资源组是在其中部署和管理 Azure 资源（例如存储帐户、磁盘、托管磁盘）的逻辑容器。

> [!IMPORTANT]
> 所有资源都在与设备相同的位置创建，并且位置设置为 **DBELocal**。

```powershell
New-AzureRmResourceGroup -Name <Resource group name> -Location DBELocal
```

下面显示了示例输出。

```powershell
New-AzureRmResourceGroup -Name rg191113014333 -Location DBELocal 
Successfully created Resource Group:rg191113014333
```

## <a name="create-a-storage-account"></a>创建存储帐户

使用在上一步中创建的资源组创建新的存储帐户。 此帐户是一个 **本地存储帐户** ，将用于上载 VM 的虚拟磁盘映像。

```powershell
New-AzureRmStorageAccount -Name <Storage account name> -ResourceGroupName <Resource group name> -Location DBELocal -SkuName Standard_LRS
```

> [!NOTE]
> 只能通过 Azure 资源管理器创建本地存储帐户，如本地冗余存储 (Standard_LRS 或 Premium_LRS) 。 若要创建分层存储帐户，请参阅 [添加、连接到 Azure Stack Edge Pro 上的存储帐户](azure-stack-edge-j-series-deploy-add-storage-accounts.md)中的步骤。

下面显示了示例输出。

```powershell
New-AzureRmStorageAccount -Name sa191113014333  -ResourceGroupName rg191113014333 -SkuName Standard_LRS -Location DBELocal

ResourceGroupName      : rg191113014333
StorageAccountName     : sa191113014333
Id                     : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg191113014333/providers/Microsoft.Storage/storageaccounts/sa191113014333
Location               : DBELocal
Sku                    : Microsoft.Azure.Management.Storage.Models.Sku
Kind                   : Storage
Encryption             : Microsoft.Azure.Management.Storage.Models.Encryption
AccessTier             :
CreationTime           : 11/13/2019 9:43:49 PM
CustomDomain           :
Identity               :
LastGeoFailoverTime    :
PrimaryEndpoints       : Microsoft.Azure.Management.Storage.Models.Endpoints
PrimaryLocation        : DBELocal
ProvisioningState      : Succeeded
SecondaryEndpoints     :
SecondaryLocation      :
StatusOfPrimary        : Available
StatusOfSecondary      :
Tags                   :
EnableHttpsTrafficOnly : False
NetworkRuleSet         :
Context                : Microsoft.WindowsAzure.Commands.Common.Storage.LazyAzureStorageContext
ExtendedProperties     : {}
```

若要获取存储帐户密钥，请运行 `Get-AzureRmStorageAccountKey` 命令。 此处显示了此命令的示例输出。

```powershell
PS C:\Users\Administrator> Get-AzureRmStorageAccountKey

cmdlet Get-AzureRmStorageAccountKey at command pipeline position 1
Supply values for the following parameters:
(Type !? for Help.)
ResourceGroupName: my-resource-ase
Name:myasestoracct

KeyName Value
------- -----
key1 /IjVJN+sSf7FMKiiPLlDm8mc9P4wtcmhhbnCa7...
key2 gd34TcaDzDgsY9JtDNMUgLDOItUU0Qur3CBo6Q...
```

## <a name="add-blob-uri-to-hosts-file"></a>将 blob URI 添加到 hosts 文件

已在用于连接到 Blob 存储的客户端的 hosts 文件中添加了 blob URI，该客户端用于 [终结点名称解析的 "修改主机文件](azure-stack-edge-j-series-connect-resource-manager.md#step-5-modify-host-file-for-endpoint-name-resolution)" 部分。 此条目用于添加 blob URI：

\<Azure consistent network services VIP \>\<storage name\>blob。 \<appliance name\>\<dnsdomain\>


## <a name="install-certificates"></a>安装证书

如果使用的是 *https*，则需要在设备上安装适当的证书。 在这种情况下，请安装 blob 端点证书。 有关详细信息，请参阅如何在 " [管理证书](azure-stack-edge-j-series-manage-certificates.md)" 中创建和上载证书。

## <a name="upload-a-vhd"></a>上传 VHD

将所有要使用的磁盘映像复制到在前面步骤中创建的本地存储帐户中的页 blob 中。 可以使用 [AzCopy](../storage/common/storage-use-azcopy-v10.md) 等工具将 VHD 上传到在前面步骤中创建的存储帐户。 

<!--Before you use AzCopy, make sure that the [AzCopy is configured correctly](#configure-azcopy) for use with the blob storage REST API version that you are using with your Azure Stack Edge Pro device.

```powershell
AzCopy /Source:<sourceDirectoryForVHD> /Dest:<blobContainerUri> /DestKey:<storageAccountKey> /Y /S /V /NC:32  /BlobType:page /destType:blob 
```

> [!NOTE]
> Set `BlobType` to page for creating a managed disk out of VHD. Set `BlobType` to block when writing to tiered storage accounts using AzCopy.

You can download the disk images from the marketplace. For detailed steps, go to [Get the virtual disk image from Azure marketplace](azure-stack-edge-j-series-create-virtual-machine-image.md).

A sample output using AzCopy 7.3 is shown below. For more information on this command, go to [Upload VHD file to storage account using AzCopy](../devtest-labs/devtest-lab-upload-vhd-using-azcopy.md).


```powershell
AzCopy /Source:\\hcsfs\scratch\vm_vhds\linux\ /Dest:http://sa191113014333.blob.dbe-1dcmhq2.microsoftdatabox.com/vmimages /DestKey:gJKoyX2Amg0Zytd1ogA1kQ2xqudMHn7ljcDtkJRHwMZbMK== /Y /S /V /NC:32 /BlobType:page /destType:blob /z:2e7d7d27-c983-410c-b4aa-b0aa668af0c6
```-->
将以下命令与 AzCopy 10 结合使用：  

```powershell
$StorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName <ResourceGroupName> -Name <StorageAccountName>)[0].Value

$endPoint = (Get-AzureRmStorageAccount -name <StorageAccountName> -ResourceGroupName <ResourceGroupName>).PrimaryEndpoints.Blob

$StorageAccountContext = New-AzureStorageContext -StorageAccountName <StorageAccountName> -StorageAccountKey <StorageAccountKey> -Endpoint <Endpoint>

$StorageAccountSAS = New-AzureStorageAccountSASToken -Service Blob,File,Queue,Table -ResourceType Container,Service,Object -Permission "acdlrw" -Context <StorageAccountContext> -Protocol HttpsOnly

<AzCopy exe path> cp "Full VHD path" "<BlobEndPoint>/<ContainerName><StorageAccountSAS>"
```

下面是示例输出： 

```powershell
$ContainerName = <ContainerName>
$ResourceGroupName = <ResourceGroupName>
$StorageAccountName = <StorageAccountName>
$VHDPath = "Full VHD Path"
$VHDFile = <VHDFileName>

$StorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $ResourceGroupName -Name $StorageAccountName)[0].Value

$endPoint = (Get-AzureRmStorageAccount -name $StorageAccountName -resourcegroupname $ResourceGroupName).PrimaryEndpoints.Blob

$StorageAccountContext = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey -Endpoint $endPoint

$StorageAccountSAS = New-AzureStorageAccountSASToken -Service Blob,File,Queue,Table -ResourceType Container,Service,Object -Permission "acdlrw" -Context $StorageAccountContext -Protocol HttpsOnly

C:\AzCopy.exe  cp "$VHDPath\$VHDFile" "$endPoint$ContainerName$StorageAccountSAS"
```

## <a name="create-managed-disks-from-the-vhd"></a>基于 VHD 创建托管磁盘

从上传的 VHD 创建托管磁盘。

```powershell
$DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import -SourceUri "Source URL for your VHD"
```
下面显示了示例输出： 
<code>
$DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import –SourceUri http://</code><code>sa191113014333.blob.dbe-1dcmhq2.microsoftdatabox.com/vmimages/ubuntu13.vhd</code> 

```powershell
New-AzureRMDisk -ResourceGroupName <Resource group name> -DiskName <Disk name> -Disk $DiskConfig
```

下面显示了示例输出。 有关此 cmdlet 的详细信息，请参阅 [get-azurermdisk](/powershell/module/azurerm.compute/new-azurermdisk?view=azurermps-6.13.0&preserve-view=true)。

```powershell
Tags               :
New-AzureRmDisk -ResourceGroupName rg191113014333 -DiskName ld191113014333 -Disk $DiskConfig

ResourceGroupName  : rg191113014333
ManagedBy          :
Sku                : Microsoft.Azure.Management.Compute.Models.DiskSku
Zones              :
TimeCreated        : 11/13/2019 1:49:07 PM
OsType             :
CreationData       : Microsoft.Azure.Management.Compute.Models.CreationData
DiskSizeGB         : 30
EncryptionSettings :
ProvisioningState  : Succeeded
Id                 : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg191113014333/providers/Micros
                     oft.Compute/disks/ld191113014333
Name               : ld191113014333
Type               : Microsoft.Compute/disks
Location           : DBELocal
Tags               : {}
```

## <a name="create-a-vm-image-from-the-image-managed-disk"></a>基于映像托管磁盘创建 VM 映像

使用以下命令从托管磁盘创建 VM 映像。 将中的值替换 \< \> 为你选择的名称。

```powershell
$imageConfig = New-AzureRmImageConfig -Location DBELocal
$ManagedDiskId = (Get-AzureRmDisk -Name <Disk name> -ResourceGroupName <Resource group name>).Id
Set-AzureRmImageOsDisk -Image $imageConfig -OsType '<OS type>' -OsState 'Generalized' -DiskSizeGB <Disk size> -ManagedDiskId $ManagedDiskId 

The supported OS types are Linux and Windows.

For OS Type=Linux, for example:
Set-AzureRmImageOsDisk -Image $imageConfig -OsType 'Linux' -OsState 'Generalized' -DiskSizeGB <Disk size> -ManagedDiskId $ManagedDiskId
New-AzureRmImage -Image $imageConfig -ImageName <Image name>  -ResourceGroupName <Resource group name>
```

下面显示了示例输出。 有关此 cmdlet 的详细信息，请参阅 [get-azurermimage](/powershell/module/azurerm.compute/new-azurermimage?view=azurermps-6.13.0&preserve-view=true)。

```powershell
New-AzureRmImage -Image Microsoft.Azure.Commands.Compute.Automation.Models.PSImage -ImageName ig191113014333  -ResourceGroupName rg191113014333
ResourceGroupName    : rg191113014333
SourceVirtualMachine :
StorageProfile       : Microsoft.Azure.Management.Compute.Models.ImageStorageProfile
ProvisioningState    : Succeeded
Id                   : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg191113014333/providers/Micr
                       osoft.Compute/images/ig191113014333
Name                 : ig191113014333
Type                 : Microsoft.Compute/images
Location             : dbelocal
Tags                 : {}
```

## <a name="create-vm-with-previously-created-resources"></a>基于之前创建的资源创建 VM

创建和部署 VM 之前，必须创建一个虚拟网络并关联一个虚拟网络接口。

> [!IMPORTANT]
> 创建虚拟网络和虚拟网络接口时，以下规则适用：
> - 即使) 资源组，也只能创建一个 Vnet (，它必须与该地址空间中的逻辑网络完全匹配。
> - Vnet 中只允许有一个子网。 子网必须与 Vnet 完全相同的地址空间。
> - 在创建 Vnic 的过程中，只允许使用静态分配方法，且用户需要提供专用 IP 地址。

 
**查询自动创建的 Vnet**

如果从设备的本地 UI 启用计算，则 `ASEVNET` 会在 "资源组" 下自动创建一个 Vnet `ASERG` 。 使用以下命令查询现有 Vnet：

```powershell
$aRmVN = Get-AzureRMVirtualNetwork -Name ASEVNET -ResourceGroupName ASERG 
```

<!--```powershell
$subNetId=New-AzureRmVirtualNetworkSubnetConfig -Name <Subnet name> -AddressPrefix <Address Prefix>
$aRmVN = New-AzureRmVirtualNetwork -ResourceGroupName <Resource group name> -Name <Vnet name> -Location DBELocal -AddressPrefix <Address prefix> -Subnet $subNetId
```-->

**使用 Vnet 子网 ID 创建 Vnic**

```powershell
$ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name <IP config Name> -SubnetId $aRmVN.Subnets[0].Id -PrivateIpAddress <Private IP>
$Nic = New-AzureRmNetworkInterface -Name <Nic name> -ResourceGroupName <Resource group name> -Location DBELocal -IpConfiguration $ipConfig
```

这些命令的示例输出如下所示：

```powershell
PS C:\Users\Administrator> $subNetId=New-AzureRmVirtualNetworkSubnetConfig -Name my-ase-subnet -AddressPrefix "5.5.0.0/16"

PS C:\Users\Administrator> $aRmVN = New-AzureRmVirtualNetwork -ResourceGroupName Resource-my-ase -Name my-ase-virtualnetwork -Location DBELocal -AddressPrefix "5.5.0.0/16" -Subnet $subNetId
WARNING: The output object type of this cmdlet will be modified in a future release.
PS C:\Users\Administrator> $ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name my-ase-ip -SubnetId $aRmVN.Subnets[0].Id
PS C:\Users\Administrator> $Nic = New-AzureRmNetworkInterface -Name my-ase-nic -ResourceGroupName Resource-my-ase -Location DBELocal -IpConfiguration $ipConfig
WARNING: The output object type of this cmdlet will be modified in a future release.

PS C:\Users\Administrator> $Nic

PS C:\Users\Administrator> (Get-AzureRmNetworkInterface)[0]

Name                        : nic200108020444
ResourceGroupName           : rg200108020444
Location                    : dbelocal
Id                          : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg200108020444/providers/Microsoft.Network/networ
                              kInterfaces/nic200108020444
Etag                        : W/"f9d1759d-4d49-42fa-8826-e218e0b1d355"
ResourceGuid                : 3851ae62-c13e-4416-9386-e21d9a2fef0f
ProvisioningState           : Succeeded
Tags                        :
VirtualMachine              : {
                                "Id": "/subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg200108020444/providers/Microsoft.Compu
                              te/virtualMachines/VM200108020444"
                              }
IpConfigurations            : [
                                {
                                  "Name": "ip200108020444",
                                  "Etag": "W/\"f9d1759d-4d49-42fa-8826-e218e0b1d355\"",
                                  "Id": "/subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg200108020444/providers/Microsoft.Net
                              work/networkInterfaces/nic200108020444/ipConfigurations/ip200108020444",
                                  "PrivateIpAddress": "5.5.166.65",
                                  "PrivateIpAllocationMethod": "Static",
                                  "Subnet": {
                                    "Id": "/subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/DbeSystemRG/providers/Microsoft.Netw
                              ork/virtualNetworks/vSwitch1/subnets/subnet123",
                                    "ResourceNavigationLinks": [],
                                    "ServiceEndpoints": []
                                  },
                                  "ProvisioningState": "Succeeded",
                                  "PrivateIpAddressVersion": "IPv4",
                                  "LoadBalancerBackendAddressPools": [],
                                  "LoadBalancerInboundNatRules": [],
                                  "Primary": true,
                                  "ApplicationGatewayBackendAddressPools": [],
                                  "ApplicationSecurityGroups": []
                                }
                              ]
DnsSettings                 : {
                                "DnsServers": [],
                                "AppliedDnsServers": []
                              }
EnableIPForwarding          : False
EnableAcceleratedNetworking : False
NetworkSecurityGroup        : null
Primary                     : True
MacAddress                  : 00155D18E432                :
```

为 VM 创建 Vnic 时，可以选择传递公共 IP。 在此实例中，公共 IP 将返回专用 IP。 

```powershell
New-AzureRmPublicIPAddress -Name <Public IP> -ResourceGroupName <ResourceGroupName> -AllocationMethod Static -Location DBELocal
$publicIP = (Get-AzureRmPublicIPAddress -Name <Public IP> -ResourceGroupName <Resource group name>).Id
$ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name <ConfigName> -PublicIpAddressId $publicIP -SubnetId $subNetId
```


**创建 VM**

你现在可以使用 VM 映像创建 VM，并将其附加到之前创建的虚拟网络。

```powershell
$pass = ConvertTo-SecureString "<Password>" -AsPlainText -Force;
$cred = New-Object System.Management.Automation.PSCredential("<Enter username>", $pass)

You will use this username, password to login to the VM, once it is created and powered up.

$VirtualMachine = New-AzureRmVMConfig -VMName <VM name> -VMSize "Standard_D1_v2"

$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -<OS type> -ComputerName <Your computer Name> -Credential $cred

$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name <OS Disk Name> -Caching "ReadWrite" -CreateOption "FromImage" -Linux -StorageAccountType StandardLRS

$nicID = (Get-AzureRmNetworkInterface -Name <nic name> -ResourceGroupName <Resource Group Name>).Id

$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $nicID

$image = (Get-AzureRmImage -ResourceGroupName <Resource Group Name> -ImageName $ImageName).Id

$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -Id $image

New-AzureRmVM -ResourceGroupName <Resource Group Name> -Location DBELocal -VM $VirtualMachine -Verbose
```

## <a name="connect-to-a-vm"></a>连接到 VM

根据你创建的是 Windows 还是 Linux VM，连接步骤可能会有所不同。

### <a name="connect-to-linux-vm"></a>连接到 Linux VM

按照以下步骤连接到 Linux VM。

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-linux.md)]

### <a name="connect-to-windows-vm"></a>连接到 Windows VM

按照以下步骤连接到 Windows VM。

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-windows.md)]


<!--Connect to the VM using the private IP that you passed during the VM creation.

Open an SSH session to connect with the IP address.

`ssh -l <username> <ip address>`

When prompted, provide the password that you used when creating the VM.

If you need to provide the SSH key, use this command.

ssh -i c:/users/Administrator/.ssh/id_rsa Administrator@5.5.41.236

If you used a public IP address during VM creation, you can use that IP to connect to the VM. To get the public IP: 

```powershell
$publicIp = Get-AzureRmPublicIpAddress -Name <Public IP> -ResourceGroupName <Resource group name>
```
The public IP in this case will be the same as the private IP that you passed during virtual network interface creation.-->


## <a name="manage-vm"></a>管理 VM

以下部分介绍了将在 Azure Stack Edge Pro 设备上创建的 VM 的一些常见操作。

### <a name="list-vms-running-on-the-device"></a>列出在设备上运行的 Vm

若要返回 Azure Stack Edge Pro 设备上运行的所有 Vm 的列表，请运行以下命令。


`Get-AzureRmVM -ResourceGroupName <String> -Name <String>`
 

### <a name="turn-on-the-vm"></a>打开 VM

运行以下 cmdlet 以打开在你的设备上运行的虚拟机：


`Start-AzureRmVM [-Name] <String> [-ResourceGroupName] <String>`


有关此 cmdlet 的详细信息，请参阅 [get-azurermvm](/powershell/module/azurerm.compute/start-azurermvm?view=azurermps-6.13.0&preserve-view=true)。

### <a name="suspend-or-shut-down-the-vm"></a>挂起或关闭 VM

运行以下 cmdlet 以停止或关闭设备上运行的虚拟机：


```powershell
Stop-AzureRmVM [-Name] <String> [-StayProvisioned] [-ResourceGroupName] <String>
```


有关此 cmdlet 的详细信息，请参阅 [get-azurermvm cmdlet](/powershell/module/azurerm.compute/stop-azurermvm?view=azurermps-6.13.0&preserve-view=true)。

### <a name="add-a-data-disk"></a>添加数据磁盘

如果 VM 上的工作负荷要求增加，则可能需要添加一个数据磁盘。

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk1" -VhdUri "https://contoso.blob.core.windows.net/vhds/diskstandard03.vhd" -LUN 0 -Caching ReadOnly -DiskSizeinGB 1 -CreateOption Empty 
 
Update-AzureRmVM -ResourceGroupName "<Resource Group Name string>" -VM $VirtualMachine
```

### <a name="delete-the-vm"></a>删除 VM

运行以下 cmdlet 从设备中删除虚拟机：

```powershell
Remove-AzureRmVM [-Name] <String> [-ResourceGroupName] <String>
```

有关此 cmdlet 的详细信息，请参阅 [get-azurermvm cmdlet](/powershell/module/azurerm.compute/remove-azurermvm?view=azurermps-6.13.0&preserve-view=true)。



## <a name="next-steps"></a>后续步骤

[Azure 资源管理器 cmdlet](/powershell/module/azurerm.resources/?view=azurermps-6.13.0&preserve-view=true)