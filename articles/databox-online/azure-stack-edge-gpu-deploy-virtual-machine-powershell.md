---
title: 通过 Azure PowerShell 在 Azure Stack Edge 设备上部署 VM
description: 介绍如何使用 Azure PowerShell 在 Azure Stack Edge 设备上创建和管理虚拟机。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/26/2021
ms.author: alkohli
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 32d2102bad49a3023df9b1d25f5b5378a3b30ad0
ms.sourcegitcommit: bb1c13bdec18079aec868c3a5e8b33ef73200592
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2021
ms.locfileid: "114721501"
---
# <a name="deploy-vms-on-your-azure-stack-edge-device-via-azure-powershell"></a>通过 Azure PowerShell 在 Azure Stack Edge 设备上部署 VM

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

本文介绍如何使用 Azure PowerShell 在 Azure Stack Edge 设备上创建和管理虚拟机 (VM)。

## <a name="vm-deployment-workflow"></a>VM 部署工作流

下面是 VM 部署的概要部署工作流：

1. 连接到设备的本地 Azure 资源管理器。
1. 识别设备上的内置订阅。
1. 提供 VM 映像。
1. 在内置订阅中创建资源组。 资源组将包含 VM 和所有相关资源。  
1. 在设备上创建本地存储帐户，以存储将用于创建 VM 映像的 VHD。
1. 将 Windows/Linux 源映像上传到存储帐户，用于创建托管磁盘。
1. 使用托管磁盘创建 VM 映像。
1. 启用设备端口的计算功能，以创建虚拟交换机。
1. 这将使用连接到已启用计算功能的端口的虚拟交换机来创建虚拟网络。
1. 使用之前创建的 VM 映像、虚拟网络和虚拟网络接口创建 VM，以在虚拟网络中进行通信，并分配用于远程访问 VM 的公共 IP 地址。 可以选择包含数据磁盘，以便为 VM 提供更多存储空间。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [azure-stack-edge-gateway-deploy-vm-prerequisites](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-prerequisites.md)]


## <a name="query-for-a-built-in-subscription-on-the-device"></a>查询设备上的内置订阅

对于 Azure 资源管理器，仅支持用户可见的单个固定订阅。 此订阅对于每个设备都是唯一的，且无法更改订阅名称和订阅 ID。

订阅包含创建 VM 所需的所有资源。 

> [!IMPORTANT]
> 从 Azure 门户启用 VM 时，将会创建订阅，该订阅位于设备的本地位置。

订阅用于部署 VM。

### <a name="az"></a>[Az](#tab/az)

1.  若要列出订阅，请运行以下命令：

    ```powershell
    Get-AzSubscription
    ```
    
    下面是一些示例输出：

    ```output
    PS C:\WINDOWS\system32> Get-AzSubscription
    
    Name                          Id                                   TenantId
    ----                          --                                   --------
    Default Provider Subscription ...                                  ...
    
    
    PS C:\WINDOWS\system32>
    ```
        
1. 获取在设备上运行的已注册资源提供程序的列表。 此列表通常包括计算、网络和存储。

    ```powershell
    Get-AzResourceProvider
    ```

    > [!NOTE]
    > 资源提供程序是预先注册的，不能对其进行修改或更改。
    
    下面是一些示例输出：

    ```output
    PS C:\WINDOWS\system32>  Get-AzResourceProvider
        
    ProviderNamespace : Microsoft.AzureBridge
    RegistrationState : Registered
    ResourceTypes     : {locations, operations, locations/ingestionJobs}
    Locations         : {DBELocal}
    
    ProviderNamespace : Microsoft.Compute
    RegistrationState : Registered
    ResourceTypes     : {virtualMachines, virtualMachines/extensions, locations, operations...}
    Locations         : {DBELocal}
    
    ProviderNamespace : Microsoft.Network
    RegistrationState : Registered
    ResourceTypes     : {operations, locations, locations/operations, locations/usages...}
    Locations         : {DBELocal}
    
    ProviderNamespace : Microsoft.Resources
    RegistrationState : Registered
    ResourceTypes     : {tenants, locations, providers, checkresourcename...}
    Locations         : {DBELocal}
    
    ProviderNamespace : Microsoft.Storage
    RegistrationState : Registered
    ResourceTypes     : {storageaccounts, storageAccounts/blobServices, storageAccounts/tableServices,
                        storageAccounts/queueServices...}
    Locations         : {DBELocal}
    
    PS C:\WINDOWS\system32>
    ```

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

1.  若要列出订阅，请运行以下命令：

    ```powershell
    Get-AzureRmSubscription
    ```
    
    下面是一些示例输出：

    ```output
    PS C:\windows\system32> Get-AzureRmSubscription
    
    Name                 Id                 TenantId          State
    ----                 --                --------           -----
    Default Provider Subscription         ...                 c0257de7-538f-415c-993a-1b87a031879d Enabled
    
    PS C:\windows\system32>
    ```
        
1. 获取在设备上运行的已注册资源提供程序的列表。 此列表通常包括计算、网络和存储。

    ```powershell
    Get-AzureRMResourceProvider
    ```

    > [!NOTE]
    > 资源提供程序是预先注册的，不能对其进行修改或更改。
    
    下面是一些示例输出：

    ```output
    PS C:\Windows\system32> Get-AzureRmResourceProvider
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
---
   
## <a name="create-a-resource-group"></a>创建资源组

首先创建一个新的 Azure 资源组，并将其用作所有 VM 相关资源（例如存储帐户、磁盘、网络接口和托管磁盘）的逻辑容器。

> [!IMPORTANT]
> 所有资源的创建位置与设备所在位置为同一位置，系统将该位置设置为“DBELocal”。

### <a name="az"></a>[Az](#tab/az)

1. 设置一些参数。

    ```powershell
    $ResourceGroupName = "<Resource group name>" 
    ```
1. 为你将为 VM 创建的资源创建一个资源组。
   
    ```powershell
    New-AzResourceGroup -Name $ResourceGroupName -Location DBELocal
    ```

    下面是一些示例输出：
    
    ```output
    PS C:\WINDOWS\system32> New-AzResourceGroup -Name myaseazrg -Location DBELocal
    
    ResourceGroupName : myaseazrg
    Location          : dbelocal
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/.../resourceGroups/myaseazrg
    
    PS C:\WINDOWS\system32>
    ```

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

```powershell
New-AzureRmResourceGroup -Name <Resource group name> -Location DBELocal
```

下面是一些示例输出：

```output
New-AzureRmResourceGroup -Name rg191113014333 -Location DBELocal 
Successfully created Resource Group:rg191113014333
```
---

## <a name="create-a-storage-account"></a>创建存储帐户

使用在上一步中创建的资源组来创建新的存储帐户。 该帐户是用于上传 VM 的虚拟磁盘映像的本地存储帐户。

### <a name="az"></a>[Az](#tab/az)

1. 设置一些参数。

    ```powershell
    $StorageAccountName = "<Storage account name>"    
    ```

1. 在设备上创建一个新本地存储帐户。

    ```powershell
    New-AzStorageAccount -Name $StorageAccountName -ResourceGroupName $ResourceGroupName -Location DBELocal -SkuName Standard_LRS
    ```
    
    > [!NOTE]
    > 使用 Azure 资源管理器，只能创建本地存储帐户，例如本地冗余存储（标准或高级）。 若要创建分层存储帐户，请参阅[教程：使用具有 GPU 的 Azure Stack Edge Pro 通过存储帐户传输数据](azure-stack-edge-gpu-deploy-add-storage-accounts.md)。

    下面是示例输出：
    
    ```output
    PS C:\WINDOWS\system32> New-AzStorageAccount -Name myaseazsa -ResourceGroupName myaseazrg -Location DBELocal -SkuName Standard_LRS
    
    StorageAccountName ResourceGroupName PrimaryLocation SkuName      Kind    AccessTier CreationTime
    ------------------ ----------------- --------------- -------      ----    ---------- ------------
    myaseazsa          myaseazrg         DBELocal        Standard_LRS Storage            6/10/2021 11:45...
    
    PS C:\WINDOWS\system32>
    ```

1. 获取你在之前步骤中创建的帐户的存储帐户密钥。 出现提示时，请提供资源组名称和存储帐户名称。

    ```powershell
    Get-AzStorageAccountKey
    ``` 

    下面是示例输出：

    ```output
    PS C:\WINDOWS\system32> Get-AzStorageAccountKey
    
    cmdlet Get-AzStorageAccountKey at command pipeline position 1
    Supply values for the following parameters:
    (Type !? for Help.)
    ResourceGroupName: myaseazrg
    Name: myaseazsa
    
    KeyName Value                                                                                    Permissions
    ------- -----                                         ------
    key1    gv3OF57tuPDyzBNc1M7fhil2UAiiwnhTT6zgiwE3TlF/CD217Cvw2YCPcrKF47joNKRvzp44leUe5HtVkGx8RQ==   Full
    key2    kmEynIs3xnpmSxWbU41h5a7DZD7v4gGV3yXa2NbPbmhrPt10+QmE5PkOxxypeSqbqzd9si+ArNvbsqIRuLH2Lw==   Full
    
    PS C:\WINDOWS\system32>
    ```

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

```powershell
New-AzureRmStorageAccount -Name <Storage account name> -ResourceGroupName <Resource group name> -Location DBELocal -SkuName Standard_LRS
```

> [!NOTE]
> 使用 Azure 资源管理器，只能创建本地存储帐户，例如本地冗余存储（标准或高级）。 若要创建分层存储帐户，请参阅[教程：使用具有 GPU 的 Azure Stack Edge Pro 通过存储帐户传输数据](./azure-stack-edge-gpu-deploy-add-storage-accounts.md)。

下面是一些示例输出：

```output
New-AzureRmStorageAccount -Name sa191113014333  -ResourceGroupName rg191113014333 -SkuName Standard_LRS -Location DBELocal

ResourceGroupName      : rg191113014333
StorageAccountName     : sa191113014333
Id                     : /subscriptions/.../resourceGroups/rg191113014333/providers/Microsoft.Storage/storageaccounts/sa191113014333
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

若要获取存储帐户密钥，请运行 `Get-AzureRmStorageAccountKey` 命令。 下面是一些示例输出：

```output
PS C:\windows\system32> Get-AzureRmStorageAccountKey

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
---

## <a name="add-the-blob-uri-to-the-host-file"></a>将 blob URI 添加到主机文件

在[ Azure Stack Edge 设备上的 Azure 资源管理器](./azure-stack-edge-gpu-connect-resource-manager.md#step-5-modify-host-file-for-endpoint-name-resolution)的“修改主机文件以进行终结点名称解析”中，你已经向用于连接到 Azure Blob 存储的客户端的主机文件中添加了 Blob URI。 此条目用于添加 blob URI：

`<Device IP address>` `<storage name>.blob.<appliance name>.<dnsdomain>`

## <a name="install-certificates"></a>安装证书

如果使用的是 HTTPS，则需要在设备上安装相应证书。 此处，请安装 blob 终结点证书。 有关详细信息，请参阅[将证书用于具有 GPU 的 Azure Stack Edge Pro 设备](azure-stack-edge-gpu-manage-certificates.md)。

## <a name="upload-a-vhd"></a>上传 VHD

将要使用的任何磁盘映像复制到之前创建的本地存储帐户中的页 blob 中。 可以使用 [AzCopy](../storage/common/storage-use-azcopy-v10.md) 等工具将虚拟硬盘 (VHD) 上传到存储帐户。 


### <a name="az"></a>[Az](#tab/az)

将以下命令与 AzCopy 10 结合使用：  

1. 设置一些参数，包括适用于 AzCopy 的 API 的适当版本。 本示例使用了 AzCopy 10。

    ```powershell
    $Env:AZCOPY_DEFAULT_SERVICE_API_VERSION="2019-07-07"    
    $ContainerName = <Container name>
    $ResourceGroupName = <Resource group name>
    $StorageAccountName = <Storage account name>
    $VHDPath = "Full VHD Path"
    $VHDFile = <VHD file name>
    ```
1. 将 VHD 从源（在本例中为“本地系统”）复制到你在前面步骤中在设备上创建的存储帐户。

    ```powershell
    $StorageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $ResourceGroupName -Name $StorageAccountName)[0].Value    
    $endPoint = (Get-AzStorageAccount -name $StorageAccountName -ResourceGroupName $ResourceGroupName).PrimaryEndpoints.Blob    
    $StorageAccountContext = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey -Endpoint $endpoint    
    $StorageAccountSAS = New-AzStorageAccountSASToken -Service Blob -ResourceType Container,Service,Object -Permission "acdlrw" -Context $StorageAccountContext -Protocol HttpsOnly        
    <Path to azcopy.exe> cp "$VHDPath\$VHDFile" "$endPoint$ContainerName$StorageAccountSAS"    
    ```

    下面是示例输出： 
    
    ```output
    PS C:\windows\system32> $ContainerName = "testcontainer1"
    PS C:\windows\system32> $ResourceGroupName = "myaseazrg"
    PS C:\windows\system32> $StorageAccountName = "myaseazsa"
    PS C:\windows\system32> $VHDPath = "C:\Users\alkohli\Downloads\Ubuntu1604"           
    PS C:\windows\system32> $VHDFile = "ubuntu13.vhd"
    
    PS C:\windows\system32> $StorageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $ResourceGroupName -Name $StorageAccountName)[0].Value
    PS C:\windows\system32> $endPoint = (Get-AzStorageAccount -name $StorageAccountName -ResourceGroupName $ResourceGroupName).PrimaryEndpoints.Blob
    PS C:\windows\system32> $StorageAccountContext = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey -Endpoint $endpoint
    PS C:\windows\system32> $StorageAccountSAS = New-AzStorageAccountSASToken -Service Blob -ResourceType Container,Service,Object -Permission "acdlrw" -Context $StorageAccountContext -Protocol HttpsOnly

    PS C:\windows\system32> C:\azcopy\azcopy_windows_amd64_10.10.0\azcopy.exe cp "$VHDPath\$VHDFile" "$endPoint$ContainerName$StorageAccountSAS"
    INFO: Scanning...
    INFO: Any empty folders will not be processed, because source and/or destination doesn't have full folder support
    
    Job 72a5e3dd-9210-3e43-6691-6bebd4875760 has started
    Log file is located at: C:\Users\alkohli\.azcopy\72a5e3dd-9210-3e43-6691-6bebd4875760.log
    
    INFO: azcopy.exe: A newer version 10.11.0 is available to download
    ```

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

将以下命令与 AzCopy 10 结合使用：  

```powershell
$StorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName <ResourceGroupName> -Name <StorageAccountName>)[0].Value

$endPoint = (Get-AzureRmStorageAccount -name <StorageAccountName> -ResourceGroupName <ResourceGroupName>).PrimaryEndpoints.Blob

$StorageAccountContext = New-AzureStorageContext -StorageAccountName <StorageAccountName> -StorageAccountKey <StorageAccountKey> -Endpoint <Endpoint>

$StorageAccountSAS = New-AzureStorageAccountSASToken -Service Blob,File,Queue,Table -ResourceType Container,Service,Object -Permission "acdlrw" -Context <StorageAccountContext> -Protocol HttpsOnly

<AzCopy exe path> cp "Full VHD path" "<BlobEndPoint>/<ContainerName><StorageAccountSAS>"
```

下面是一些示例输出： 

```output
$ContainerName = <Container name>
$ResourceGroupName = <Resource group name>
$StorageAccountName = <Storage account name>
$VHDPath = "Full VHD path"
$VHDFile = <VHD file name>

$StorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $ResourceGroupName -Name $StorageAccountName)[0].Value

$endPoint = (Get-AzureRmStorageAccount -name $StorageAccountName -resourcegroupname $ResourceGroupName).PrimaryEndpoints.Blob

$StorageAccountContext = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey -Endpoint $endPoint

$StorageAccountSAS = New-AzureStorageAccountSASToken -Service Blob,File,Queue,Table -ResourceType Container,Service,Object -Permission "acdlrw" -Context $StorageAccountContext -Protocol HttpsOnly

C:\AzCopy.exe  cp "$VHDPath\$VHDFile" "$endPoint$ContainerName$StorageAccountSAS"
```
---

## <a name="create-a-managed-disk-from-the-vhd"></a>从 VHD 创建托管磁盘

现在，你将从上传的 VHD 创建托管磁盘。

### <a name="az"></a>[Az](#tab/az)

1. 设置一些参数。

    ```powershell
    $DiskName = "<Managed disk name>"
    ```

1. 从上传的 VHD 创建托管磁盘。 若要获取 VHD 的源 URL，请转到存储资源管理器中包含 VHD 的存储帐户中的容器。 选择 VHD 并右键单击，然后选择“属性”。 在“Blob 属性”对话框中，选择“URI” 。 

    ```powershell
    $StorageAccountId = (Get-AzStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName).Id    
    $DiskConfig = New-AzDiskConfig -Location DBELocal -StorageAccountId $StorageAccountId -CreateOption Import -SourceUri "Source URL for your VHD"
    New-AzDisk -ResourceGroupName $ResourceGroupName -DiskName $DiskName -Disk $DiskConfig
    ```
    下面是示例输出：
    
    ```output
    PS C:\WINDOWS\system32> $DiskName = "myazmd"
    PS C:\WINDOWS\system32> $StorageAccountId = (Get-AzStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName).Id
    PS C:\WINDOWS\system32> $DiskConfig = New-AzDiskConfig -Location DBELocal -StorageAccountId $StorageAccountId -CreateOption Import -SourceUri "https://myaseazsa.blob.myasegpu.wdshcsso.com/testcontainer1/ubuntu13.vhd"
    PS C:\WINDOWS\system32> New-AzDisk -ResourceGroupName $ResourceGroupName -DiskName $DiskName -Disk $DiskConfig
    
    ResourceGroupName            : myaseazrg
    ManagedBy                    :
    Sku                          : Microsoft.Azure.Management.Compute.Models.DiskSku
    Zones                        :
    TimeCreated                  : 6/24/2021 12:19:56 PM
    OsType                       :
    HyperVGeneration             :
    CreationData                 : Microsoft.Azure.Management.Compute.Models.CreationDat
                                   a
    DiskSizeGB                   : 30
    DiskSizeBytes                : 32212254720
    UniqueId                     : 53743801-cbf2-4d2f-acb4-971d037a9395
    EncryptionSettingsCollection :
    ProvisioningState            : Succeeded
    DiskIOPSReadWrite            : 500
    DiskMBpsReadWrite            : 60
    DiskState                    : Unattached
    Encryption                   : Microsoft.Azure.Management.Compute.Models.Encryption
    Id                           : /subscriptions/.../r
                                   esourceGroups/myaseazrg/providers/Microsoft.Compute/d
                                   isks/myazmd
    Name                         : myazmd
    Type                         : Microsoft.Compute/disks
    Location                     : DBELocal
    Tags                         : {}
    
    PS C:\WINDOWS\system32>
    ```
 

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

```powershell
$DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import -SourceUri "Source URL for your VHD"
```
下面是一些示例输出： 

```output
$DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import –SourceUri http://sa191113014333.blob.dbe-1dcmhq2.microsoftdatabox.com/vmimages/ubuntu13.vhd
```
 

```powershell
New-AzureRMDisk -ResourceGroupName <Resource group name> -DiskName <Disk name> -Disk $DiskConfig
```

下面是一些示例输出。 有关此 cmdlet 的详细信息，请参阅 [New-AzureRmDisk](/powershell/module/azurerm.compute/new-azurermdisk?view=azurermps-6.13.0&preserve-view=true)。

```output
Tags               : New-AzureRmDisk -ResourceGroupName rg191113014333 -DiskName ld191113014333 -Disk $DiskConfig

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
Id                 : /subscriptions/.../resourceGroups/rg191113014333/providers/Micros
                     oft.Compute/disks/ld191113014333
Name               : ld191113014333
Type               : Microsoft.Compute/disks
Location           : DBELocal
Tags               : {}
```
---

## <a name="create-a-vm-image-from-the-managed-disk"></a>基于托管磁盘创建 VM 映像

现在，你将基于托管磁盘创建 VM 映像。

### <a name="az"></a>[Az](#tab/az)

1. 设置一些参数。

    ```powershell
    $DiskSize = "<Size greater than or equal to size of source managed disk>"
    $OsType = "<linux or windows>" 
    $ImageName = "<Image name>"
    ```
1. 创建 VM 映像。 支持的操作系统类型为 Linux 和 Windows。

    ```powershell
    $imageConfig = New-AzImageConfig -Location DBELocal
    $ManagedDiskId = (Get-AzDisk -Name $DiskName -ResourceGroupName $ResourceGroupName).Id
    Set-AzImageOsDisk -Image $imageConfig -OsType $OsType -OsState 'Generalized' -DiskSizeGB $DiskSize -ManagedDiskId $ManagedDiskId 
    New-AzImage -Image $imageConfig -ImageName $ImageName -ResourceGroupName $ResourceGroupName  
    ```  
    下面是示例输出。 

    ```output
    PS C:\WINDOWS\system32> $OsType = "linux"
    PS C:\WINDOWS\system32> $ImageName = "myaseazlinuxvmimage"
    PS C:\WINDOWS\system32> $DiskSize = 35
    PS C:\WINDOWS\system32> $imageConfig = New-AzImageConfig -Location DBELocal
    PS C:\WINDOWS\system32> $ManagedDiskId = (Get-AzDisk -Name $DiskName -ResourceGroupName $ResourceGroupName).Id
    PS C:\WINDOWS\system32> Set-AzImageOsDisk -Image $imageConfig -OsType $OsType -OsState 'Generalized' -DiskSizeGB $DiskSize -ManagedDiskId $ManagedDiskId
    
    ResourceGroupName    :
    SourceVirtualMachine :
    StorageProfile       : Microsoft.Azure.Management.Compute.Models.ImageStorageProfile
    ProvisioningState    :
    HyperVGeneration     : V1
    Id                   :
    Name                 :
    Type                 :
    Location             : DBELocal
    Tags                 :
    
    PS C:\WINDOWS\system32> New-AzImage -Image $imageConfig -ImageName $ImageName -ResourceGroupName $ResourceGroupName
    
    ResourceGroupName    : myaseazrg
    SourceVirtualMachine :
    StorageProfile       : Microsoft.Azure.Management.Compute.Models.ImageStorageProfile
    ProvisioningState    : Succeeded
    HyperVGeneration     : V1
    Id                   : /subscriptions/.../resourceG
                           roups/myaseazrg/providers/Microsoft.Compute/images/myaseazlin
                           uxvmimage
    Name                 : myaseazlinuxvmimage
    Type                 : Microsoft.Compute/images
    Location             : dbelocal
    Tags                 : {}
    
    PS C:\WINDOWS\system32> 
    ```

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

运行以下命令。 将 \<Disk name>、\<OS type> 和 \<Disk size> 替换为实际值  。

```powershell
$imageConfig = New-AzureRmImageConfig -Location DBELocal
$ManagedDiskId = (Get-AzureRmDisk -Name <Disk name> -ResourceGroupName <Resource group name>).Id
Set-AzureRmImageOsDisk -Image $imageConfig -OsType '<OS type>' -OsState 'Generalized' -DiskSizeGB <Disk size> -ManagedDiskId $ManagedDiskId 
New-AzureRmImage -Image $imageConfig -ImageName <Image name>  -ResourceGroupName <Resource group name>
```

支持的操作系统类型为 Linux 和 Windows。

下面是一些示例输出。 有关此 cmdlet 的详细信息，请参阅 [New-AzureRmImage](/powershell/module/azurerm.compute/new-azurermimage?view=azurermps-6.13.0&preserve-view=true)。

```output
PS C:\Windows\system32> New-AzImage -Image $imageConfig -ImageName ig191113014333    -ResourceGroupName RG191113014333
ResourceGroupName    : RG191113014333
SourceVirtualMachine :
StorageProfile       : Microsoft.Azure.Management.Compute.Models.ImageStorageProfile
ProvisioningState    : Succeeded
HyperVGeneration     : V1
Id                   : /subscriptions/.../resourceGroups/RG191113014333/providers/Microsoft.Compute/images/ig191113014333
Name                 : ig191113014333
Type                 : Microsoft.Compute/images
Location             : dbelocal
Tags                 : {}
```
---

## <a name="create-your-vm-with-previously-created-resources"></a>使用先前创建的资源创建 VM

在创建和部署 VM 之前，必须先创建一个虚拟网络并将某个虚拟网络接口与其关联。

> [!IMPORTANT]
> 下列规则适用：
> - 甚至可以在资源组中创建一个虚拟网络。 虚拟网络的地址空间必须与逻辑网络完全相同。
> - 一个虚拟网络只能有一个子网。 子网的地址空间必须与虚拟网络完全相同。
> - 创建虚拟网络接口卡时，只能使用静态分配方法。 用户需要提供专用 IP 地址。<!--Confirm w/ Neeraj given we can have both static and DHCP-->

### <a name="query-the-automatically-created-virtual-network"></a>查询自动创建的虚拟网络

从设备的本地 UI 启用计算时，将在 `ASERG` 资源组下自动创建一个名为 `ASEVNET` 的虚拟网络。 

### <a name="az"></a>[Az](#tab/az)

使用以下命令查询现有虚拟网络：

```powershell
$ArmVn = Get-AzVirtualNetwork -Name ASEVNET -ResourceGroupName ASERG 
```

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

使用以下命令查询现有虚拟网络：

```powershell
$aRmVN = Get-AzureRMVirtualNetwork -Name ASEVNET -ResourceGroupName ASERG 
```
---

### <a name="create-a-virtual-network-interface-card"></a>创建虚拟网络接口卡

你将使用虚拟网络子网 ID 创建虚拟网络接口卡。

### <a name="az"></a>[Az](#tab/az)

1. 设置一些参数。

    ```powershell
    $IpConfigName = "<IP config name>"
    $NicName = "<Network interface name>"
    ```

1. 创建虚拟网络接口。

    ```powershell
    $ipConfig = New-AzNetworkInterfaceIpConfig -Name $IpConfigName -SubnetId $aRmVN.Subnets[0].Id 
    $Nic = New-AzNetworkInterface -Name $NicName -ResourceGroupName $ResourceGroupName -Location DBELocal -IpConfiguration $IpConfig    
    ```

    默认情况下会通过为计算启用的网络，向网络接口动态分配 IP。 如果要向网络接口分配静态 IP，请使用 `-PrivateIpAddress parameter`。         

    下面是示例输出：
    
    ```output
    PS C:\WINDOWS\system32> $IpConfigName = "myazipconfig1"
    PS C:\WINDOWS\system32> $NicName = "myaznic1"
    PS C:\WINDOWS\system32> $ipConfig = New-AzNetworkInterfaceIpConfig -Name $IpConfigName -SubnetId $aRmVN.Subnets[0].Id 
    PS C:\WINDOWS\system32> $ipConfig = New-AzNetworkInterfaceIpConfig -Name $IpConfigName -SubnetId $aRmVN.Subnets[0].Id
    PS C:\WINDOWS\system32> $Nic = New-AzNetworkInterface -Name $NicName -ResourceGroupName $ResourceGroupName -Location DBELocal -IpConfiguration $IpConfig
    PS C:\WINDOWS\system32> $Nic
        
    Name                        : myaznic1
    ResourceGroupName           : myaseazrg
    Location                    : dbelocal
    Id                          : /subscriptions/.../re
                                  sourceGroups/myaseazrg/providers/Microsoft.Network/net
                                  workInterfaces/myaznic1
    Etag                        : W/"0b20057b-2102-4f34-958b-656327c0fb1d"
    ResourceGuid                : e7d4131f-6f01-4492-9d4c-a8ff1af7244f
    ProvisioningState           : Succeeded
    Tags                        :
    VirtualMachine              : null
    IpConfigurations            : [
                                    {
                                      "Name": "myazipconfig1",
                                      "Etag":
                                  "W/\"0b20057b-2102-4f34-958b-656327c0fb1d\"",
                                      "Id": "/subscriptions/.../resourceGroups/myaseazrg/providers/Microsoft.
                                  Network/networkInterfaces/myaznic1/ipConfigurations/my
                                  azipconfig1",
                                      "PrivateIpAddress": "10.126.76.60",
                                      "PrivateIpAllocationMethod": "Dynamic",
                                      "Subnet": {
                                        "Delegations": [],
                                        "Id": "/subscriptions/.../resourceGroups/ASERG/providers/Microsoft.Ne
                                  twork/virtualNetworks/ASEVNET/subnets/ASEVNETsubNet",
                                        "ServiceAssociationLinks": []
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
                                    "AppliedDnsServers": [],
                                    "InternalDomainNameSuffix": "auwlfcx0dhxurjgisct43fc
                                  ywb.a--x.internal.cloudapp.net"
                                  }
    EnableIPForwarding          : False
    EnableAcceleratedNetworking : False
    NetworkSecurityGroup        : null
    Primary                     :
    MacAddress                  : 001DD84A58D1
           
    PS C:\WINDOWS\system32>
    ```

在为 VM 创建虚拟网络接口卡时，可以选择传递公共 IP。 在这种情况下，公共 IP 会返回专用 IP。 

```powershell
New-AzPublicIPAddress -Name <Public IP> -ResourceGroupName <ResourceGroupName> -AllocationMethod Static -Location DBELocal
$publicIP = (Get-AzPublicIPAddress -Name <Public IP> -ResourceGroupName <Resource group name>).Id
$ipConfig = New-AzNetworkInterfaceIpConfig -Name <ConfigName> -PublicIpAddressId $publicIP -SubnetId $subNetId
```

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

```powershell
$ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name <IP config Name> -SubnetId $aRmVN.Subnets[0].Id -PrivateIpAddress <Private IP>
$Nic = New-AzureRmNetworkInterface -Name <Nic name> -ResourceGroupName <Resource group name> -Location DBELocal -IpConfiguration $ipConfig
```

默认情况下会通过为计算启用的网络，向网络接口动态分配 IP。 如果要向网络接口分配静态 IP，请使用 `-PrivateIpAddress parameter`。 

下面是一些示例输出：

```output
PS C:\windows\system32> $subNetId=New-AzureRmVirtualNetworkSubnetConfig -Name my-ase-subnet -AddressPrefix "5.5.0.0/16"

PS C:\windows\system32> $aRmVN = New-AzureRmVirtualNetwork -ResourceGroupName Resource-my-ase -Name my-ase-virtualnetwork -Location DBELocal -AddressPrefix "5.5.0.0/16" -Subnet $subNetId
WARNING: The output object type of this cmdlet will be modified in a future release.
PS C:\windows\system32> $ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name my-ase-ip -SubnetId $aRmVN.Subnets[0].Id
PS C:\windows\system32> $Nic = New-AzureRmNetworkInterface -Name my-ase-nic -ResourceGroupName Resource-my-ase -Location DBELocal -IpConfiguration $ipConfig
WARNING: The output object type of this cmdlet will be modified in a future release.

PS C:\windows\system32> $Nic

PS C:\windows\system32> (Get-AzureRmNetworkInterface)[0]

Name                        : nic200108020444
ResourceGroupName           : rg200108020444
Location                    : dbelocal
Id                          : /subscriptions/.../resourceGroups/rg200108020444/providers/Microsoft.Network/networ
                              kInterfaces/nic200108020444
Etag                        : W/"f9d1759d-4d49-42fa-8826-e218e0b1d355"
ResourceGuid                : 3851ae62-c13e-4416-9386-e21d9a2fef0f
ProvisioningState           : Succeeded
Tags                        :
VirtualMachine              : {
                                "Id": "/subscriptions/.../resourceGroups/rg200108020444/providers/Microsoft.Compu
                              te/virtualMachines/VM200108020444"
                              }
IpConfigurations            : [
                                {
                                  "Name": "ip200108020444",
                                  "Etag": "W/\"f9d1759d-4d49-42fa-8826-e218e0b1d355\"",
                                  "Id": "/subscriptions/.../resourceGroups/rg200108020444/providers/Microsoft.Net
                              work/networkInterfaces/nic200108020444/ipConfigurations/ip200108020444",
                                  "PrivateIpAddress": "5.5.166.65",
                                  "PrivateIpAllocationMethod": "Static",
                                  "Subnet": {
                                    "Id": "/subscriptions/.../resourceGroups/DbeSystemRG/providers/Microsoft.Netw
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

在为 VM 创建虚拟网络接口卡时，可以选择传递公共 IP。 在这种情况下，公共 IP 会返回专用 IP。 

```powershell
New-AzureRmPublicIPAddress -Name <Public IP> -ResourceGroupName <ResourceGroupName> -AllocationMethod Static -Location DBELocal
$publicIP = (Get-AzureRmPublicIPAddress -Name <Public IP> -ResourceGroupName <Resource group name>).Id
$ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name <ConfigName> -PublicIpAddressId $publicIP -SubnetId $subNetId
```
---

### <a name="create-a-vm"></a>创建 VM

现在可以使用 VM 映像来创建 VM，并将其附加到之前创建的虚拟网络中。

### <a name="az"></a>[Az](#tab/az)

1. 设置用户名和密码以登录到要创建的 VM。

    ```powershell
    $pass = ConvertTo-SecureString "<Password>" -AsPlainText -Force;
    $cred = New-Object System.Management.Automation.PSCredential("<Enter username>", $pass)
    ```
    创建并启动 VM 后，需使用前述用户名和密码登录到该 VM。

1. 设置参数。

    ```powershell
    $VmName = "<VM name>"
    $ComputerName = "<VM display name>"
    $OsDiskName = "<OS disk name>"
    ```
1. 创建 VM。

    ```powershell
    $VirtualMachine =  New-AzVMConfig -VmName $VmName -VMSize "Standard_D1_v2"
 
    $VirtualMachine =  Set-AzVMOperatingSystem -VM $VirtualMachine -Linux -ComputerName $ComputerName -Credential $cred
     
    $VirtualMachine =  Set-AzVmOsDisk -VM $VirtualMachine -Name $OsDiskName -Caching "ReadWrite" -CreateOption "FromImage" -Linux -StorageAccountType Standard_LRS
     
    $nicID = (Get-AzNetworkInterface -Name $NicName -ResourceGroupName $ResourceGroupName).Id
     
    $VirtualMachine =  Add-AzVMNetworkInterface -Vm $VirtualMachine -Id $nicID
     
    $image = ( Get-AzImage -ResourceGroupName $ResourceGroupName -ImageName $ImageName).Id
     
    $VirtualMachine =  Set-AzVMSourceImage -VM $VirtualMachine -Id $image
     
    New-AzVM -ResourceGroupName $ResourceGroupName -Location DBELocal -VM $VirtualMachine -Verbose
    ```

    下面是示例输出。
    
    ```powershell
    PS C:\WINDOWS\system32> $pass = ConvertTo-SecureString "Password1" -AsPlainText -Force;
    PS C:\WINDOWS\system32> $cred = New-Object System.Management.Automation.PSCredential("myazuser", $pass)
    PS C:\WINDOWS\system32> $VmName = "myazvm"
    >> $ComputerName = "myazvmfriendlyname"
    >> $OsDiskName = "myazosdisk1"
    PS C:\WINDOWS\system32> $VirtualMachine =  New-AzVMConfig -VmName $VmName -VMSize "Standard_D1_v2"
    PS C:\WINDOWS\system32> $VirtualMachine =  Set-AzVMOperatingSystem -VM $VirtualMachine -Linux -ComputerName $ComputerName -Credential $cred
    PS C:\WINDOWS\system32> $VirtualMachine =  Set-AzVmOsDisk -VM $VirtualMachine -Name $OsDiskName -Caching "ReadWrite" -CreateOption "FromImage" -Linux -StorageAccountType Standard_LRS
    PS C:\WINDOWS\system32> $nicID = (Get-AzNetworkInterface -Name $NicName -ResourceGroupName $ResourceGroupName).Id
    PS C:\WINDOWS\system32> $nicID/subscriptions/.../resourceGroups/myaseazrg/providers/Microsoft.Network/networkInterfaces/myaznic1
    PS C:\WINDOWS\system32> $VirtualMachine =  Add-AzVMNetworkInterface -VM $VirtualMachine -Id $nicID
    PS C:\WINDOWS\system32> $image = ( Get-AzImage -ResourceGroupName $ResourceGroupName -ImageName $ImageName).Id
    PS C:\WINDOWS\system32> $VirtualMachine =  Set-AzVMSourceImage -VM $VirtualMachine -Id $image
    PS C:\WINDOWS\system32> New-AzVM -ResourceGroupName $ResourceGroupName -Location DBELocal -VM $VirtualMachine -Verbose
    WARNING: Since the VM is created using premium storage or managed disk, existing
    standard storage account, myaseazsa, is used for boot diagnostics.
    VERBOSE: Performing the operation "New" on target "myazvm".
    
    RequestId IsSuccessStatusCode StatusCode ReasonPhrase
    --------- ------------------- ---------- ------------
                             True         OK OK
    ```
1. 若要确定分配给你创建的 VM 的 IP，请查询所创建的虚拟网络接口。 找到 `PrivateIPAddress` 并复制 VM 的 IP。 下面是示例输出。

    ```powershell
    PS C:\WINDOWS\system32> $Nic

    Name                        : myaznic1
    ResourceGroupName           : myaseazrg
    Location                    : dbelocal
    Id                          : /subscriptions/.../re
                                  sourceGroups/myaseazrg/providers/Microsoft.Network/net
                                  workInterfaces/myaznic1
    Etag                        : W/"0b20057b-2102-4f34-958b-656327c0fb1d"
    ResourceGuid                : e7d4131f-6f01-4492-9d4c-a8ff1af7244f
    ProvisioningState           : Succeeded
    Tags                        :
    VirtualMachine              : null
    IpConfigurations            : [
                                    {
                                      "Name": "myazipconfig1",
                                      "Etag":
                                  "W/\"0b20057b-2102-4f34-958b-656327c0fb1d\"",
                                      "Id": "/subscriptions/.../resourceGroups/myaseazrg/providers/Microsoft.
                                  Network/networkInterfaces/myaznic1/ipConfigurations/my
                                  azipconfig1",
                                      "PrivateIpAddress": "10.126.76.60",
                                      "PrivateIpAllocationMethod": "Dynamic",
                                      "Subnet": {
                                        "Delegations": [],
                                        "Id": "/subscriptions/.../resourceGroups/ASERG/providers/Microsoft.Ne
                                  twork/virtualNetworks/ASEVNET/subnets/ASEVNETsubNet",
                                        "ServiceAssociationLinks": []
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
                                    "AppliedDnsServers": [],
                                    "InternalDomainNameSuffix": "auwlfcx0dhxurjgisct43fc
                                  ywb.a--x.internal.cloudapp.net"
                                  }
    EnableIPForwarding          : False
    EnableAcceleratedNetworking : False
    NetworkSecurityGroup        : null
    Primary                     :
    MacAddress                  : 001DD84A58D1
      
    PS C:\WINDOWS\system32>
    ```


### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

```powershell
$pass = ConvertTo-SecureString "<Password>" -AsPlainText -Force;
$cred = New-Object System.Management.Automation.PSCredential("<Enter username>", $pass)
```

创建并启动 VM 后，需使用以下用户名和密码登录到该 VM。

```powershell
$VirtualMachine = New-AzureRmVMConfig -VMName <VM name> -VMSize "Standard_D1_v2"

$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -<OS type> -ComputerName <Your computer Name> -Credential $cred

$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name <OS Disk Name> -Caching "ReadWrite" -CreateOption "FromImage" -Linux -StorageAccountType StandardLRS

$nicID = (Get-AzureRmNetworkInterface -Name <nic name> -ResourceGroupName <Resource Group Name>).Id

$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $nicID

$image = (Get-AzureRmImage -ResourceGroupName <Resource Group Name> -ImageName $ImageName).Id

$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -Id $image

New-AzureRmVM -ResourceGroupName <Resource Group Name> -Location DBELocal -VM $VirtualMachine -Verbose
```
---

## <a name="connect-to-the-vm"></a>连接到 VM

根据所创建的是 Windows VM 还是 Linux VM，连接说明可能会有所不同。

## <a name="connect-to-a-linux-vm"></a>连接到 Linux VM

若要连接到 Linux VM，请执行以下操作：

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-linux.md)]

如果在创建 VM 的过程中使用了公共 IP 地址，则可以使用该 IP 连接到 VM。 若要获取公共 IP，请运行以下命令： 

### <a name="az"></a>[Az](#tab/az)

```powershell
$publicIp = Get-AzPublicIpAddress -Name $PublicIp -ResourceGroupName $ResourceGroupName
```
在此实例中，公共 IP 与创建虚拟网络接口期间传递的专用 IP 相同。
  
### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

```powershell
$publicIp = Get-AzureRmPublicIpAddress -Name <Public IP> -ResourceGroupName <Resource group name>
```
在此实例中，公共 IP 与创建虚拟网络接口期间传递的专用 IP 相同。

---

## <a name="connect-to-a-windows-vm"></a>连接到 Windows VM

若要连接到 Windows VM，请执行以下操作：

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-windows.md)]


## <a name="manage-the-vm"></a>管理 VM

以下部分说明可以在 Azure Stack Edge Pro 设备上创建的一些常见操作。

### <a name="list-vms-that-are-running-on-the-device"></a>列出在设备上运行的 VM

若要返回在 Azure Stack Edge 设备上运行的所有 VM 的列表，请运行以下命令：

### <a name="az"></a>[Az](#tab/az)

```powershell
Get-AzVM -ResourceGroupName <String> -Name <String>
```

有关此 cmdlet 的详细信息，请参阅 [Get-AzVM](/powershell/module/az.compute/get-azvm?view=azps-6.1.0&preserve-view=true)。

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

```powershell
Get-AzureRmVM -ResourceGroupName <String> -Name <String>
```

---


### <a name="turn-on-the-vm"></a>打开 VM

若要打开在设备上运行的虚拟机，请运行以下 cmdlet：

### <a name="az"></a>[Az](#tab/az)

```powershell
Start-AzVM [-Name] <String> [-ResourceGroupName] <String>
```
有关此 cmdlet 的信息，请参阅 [Start-AzVM](/powershell/module/az.compute/start-azvm?view=azps-5.9.0&preserve-view=true)。

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

```powershell
Start-AzureRmVM [-Name] <String> [-ResourceGroupName] <String>
```

有关此 cmdlet 的信息，请参阅 [Start-AzureRmVM](/powershell/module/azurerm.compute/start-azurermvm?view=azurermps-6.13.0&preserve-view=true)。

---

### <a name="suspend-or-shut-down-the-vm"></a>暂停或关闭 VM

若要停止或关闭设备上正在运行的虚拟机，请运行以下 cmdlet：

### <a name="az"></a>[Az](#tab/az)

```powershell
Stop-AzVM [-Name] <String> [-StayProvisioned] [-ResourceGroupName] <String>
```

有关此 cmdlet 的详细信息，请参阅 [Stop-AzVM cmdlet](/powershell/module/az.compute/stop-azvm?view=azps-5.9.0&preserve-view=true)。

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

```powershell
Stop-AzureRmVM [-Name] <String> [-StayProvisioned] [-ResourceGroupName] <String>
```

有关此 cmdlet 的详细信息，请参阅 [Stop-AzureRmVM cmdlet](/powershell/module/azurerm.compute/stop-azurermvm?view=azurermps-6.13.0&preserve-view=true)。

---

### <a name="add-a-data-disk"></a>添加数据磁盘

如果 VM 上的工作负载要求增加，建议添加数据磁盘。 为此，请运行以下命令：

### <a name="az"></a>[Az](#tab/az)

```powershell
Add-AzRmVMDataDisk -VM $VirtualMachine -Name "disk1" -VhdUri "https://contoso.blob.core.windows.net/vhds/diskstandard03.vhd" -LUN 0 -Caching ReadOnly -DiskSizeinGB 1 -CreateOption Empty 
 
Update-AzVM -ResourceGroupName "<Resource Group Name string>" -VM $VirtualMachine
```

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk1" -VhdUri "https://contoso.blob.core.windows.net/vhds/diskstandard03.vhd" -LUN 0 -Caching ReadOnly -DiskSizeinGB 1 -CreateOption Empty 
 
Update-AzureRmVM -ResourceGroupName "<Resource Group Name string>" -VM $VirtualMachine
```
---



### <a name="delete-the-vm"></a>删除 VM

若要从设备中删除虚拟机，请运行以下 cmdlet：

### <a name="az"></a>[Az](#tab/az)

```powershell
Remove-AzVM [-Name] <String> [-ResourceGroupName] <String>
```
有关此 cmdlet 的详细信息，请参阅 [Remove-AzVm cmdlet](/powershell/module/az.compute/remove-azvm?view=azps-5.9.0&preserve-view=true)。

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

```powershell
Remove-AzureRmVM [-Name] <String> [-ResourceGroupName] <String>
```
有关此 cmdlet 的详细信息，请参阅 [Remove-AzureRmVm cmdlet](/powershell/module/azurerm.compute/remove-azurermvm?view=azurermps-6.13.0&preserve-view=true)。

---

## <a name="next-steps"></a>后续步骤

[Azure 资源管理器 cmdlets](/powershell/module/azurerm.resources/?view=azurermps-6.13.0&preserve-view=true)
