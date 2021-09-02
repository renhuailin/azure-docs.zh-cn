---
title: 使用 Azure 市场映像为 Azure Stack Edge Pro GPU 设备创建 VM 映像
description: 介绍如何使用 Azure 市场映像创建在 Azure Stack Edge Pro GPU 设备上使用的 VM 映像。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 06/14/2021
ms.author: alkohli
ms.openlocfilehash: 13d020ed0ea6310f49cbe37555fc2a9048b9f042
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2021
ms.locfileid: "112465378"
---
# <a name="use-azure-marketplace-image-to-create-vm-image-for-your-azure-stack-edge-pro-gpu"></a>使用 Azure 市场映像为 Azure Stack Edge Pro GPU 创建 VM 映像

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

要在 Azure Stack Edge Pro GPU 设备上部署 VM，需要创建可用于创建 VM 的 VM 映像。 本文介绍从 Azure 市场映像开始创建 VM 映像所需的步骤。 你之后可以使用此 VM 映像在 Azure Stack Edge Pro GPU 设备上部署 VM。

## <a name="vm-image-workflow"></a>VM 映像工作流

以下步骤介绍了使用 Azure 市场工作流的 VM 映像工作流：

1.  连接到 Azure Cloud Shell 或已安装 Azure CLI 的客户端。
2.  搜索 Azure 市场，并确定首选映像。
3.  从市场映像创建新的托管磁盘。
4.  将 VHD 从托管磁盘导出到 Azure 存储帐户。
5.  清理托管磁盘。


有关详细信息，请转到[使用 Azure PowerShell 在 Azure Stack Edge Pro 设备上部署 VM](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)。

## <a name="prerequisites"></a>先决条件

在将 Azure 市场映像用于 Azure Stack Edge 之前，请确保已通过以下任一方式连接到 Azure。

[!INCLUDE [azure-cli-prepare-your-environment](../../includes/azure-cli-prepare-your-environment-no-header.md)]


## <a name="search-for-azure-marketplace-images"></a>搜索 Azure 市场映像

现在将确定想要使用的特定 Azure 市场映像。 Azure 市场托管数以千计的 VM 映像。 

若要查找与搜索条件匹配的一些最常用市场映像，请运行以下命令。  

```azurecli
az vm image list --all [--publisher <Publisher>] [--offer <Offer>] [--sku <SKU>]
```
最后三个标志是可选的，但若排除它们，则会返回一个较长的列表。

一些示例查询如下：

```azurecli
#Returns all images of type “Windows Server”
az vm image list --all --publisher "MicrosoftWindowsserver" --offer "WindowsServer" 

#Returns all Windows Server 2019 Datacenter images from West US published by Microsoft
az vm image list --all --location "westus" --publisher "MicrosoftWindowsserver" --offer "WindowsServer" --sku "2019-Datacenter"

#Returns all VM images from a publisher  
az vm image list --all --publisher "Canonical" 
```

以下是查询某个发布者、产品/服务和 SKU 的 VM 映像时的示例输出。

```output
PS /home/user> az vm image list --all --publisher "Canonical" --offer "UbuntuServer" --sku "12.04.4-LTS"
[
  {
    "offer": "UbuntuServer",
    "publisher": "Canonical",
    "sku": "12.04.4-LTS",
    "urn": "Canonical:UbuntuServer:12.04.4-LTS:12.04.201402270",
    "version": "12.04.201402270"
  },
  {
    "offer": "UbuntuServer",
    "publisher": "Canonical",
    "sku": "12.04.4-LTS",
    "urn": "Canonical:UbuntuServer:12.04.4-LTS:12.04.201404080",
    "version": "12.04.201404080"
  },
  {
    "offer": "UbuntuServer",
    "publisher": "Canonical",
    "sku": "12.04.4-LTS",
    "urn": "Canonical:UbuntuServer:12.04.4-LTS:12.04.201404280",
    "version": "12.04.201404280"
  },
  {
    "offer": "UbuntuServer",
    "publisher": "Canonical",
    "sku": "12.04.4-LTS",
    "urn": "Canonical:UbuntuServer:12.04.4-LTS:12.04.201405140",
    "version": "12.04.201405140"
  },
  {
    "offer": "UbuntuServer",
    "publisher": "Canonical",
    "sku": "12.04.4-LTS",
    "urn": "Canonical:UbuntuServer:12.04.4-LTS:12.04.201406060",
    "version": "12.04.201406060"
  },
  {
    "offer": "UbuntuServer",
    "publisher": "Canonical",
    "sku": "12.04.4-LTS",
    "urn": "Canonical:UbuntuServer:12.04.4-LTS:12.04.201406190",
    "version": "12.04.201406190"
  },
  {
    "offer": "UbuntuServer",
    "publisher": "Canonical",
    "sku": "12.04.4-LTS",
    "urn": "Canonical:UbuntuServer:12.04.4-LTS:12.04.201407020",
    "version": "12.04.201407020"
  },
  {
    "offer": "UbuntuServer",
    "publisher": "Canonical",
    "sku": "12.04.4-LTS",
    "urn": "Canonical:UbuntuServer:12.04.4-LTS:12.04.201407170",
    "version": "12.04.201407170"
  }
]
PS /home/user>
```

>[!IMPORTANT]
> 仅使用第 1 代映像。 任何指定为第 2 代的映像（sku 的后缀通常为 -g2）都不适用于 Azure Stack Edge。

在此示例中，我们将选择 Windows Server 2019 Datacenter Core 版本 2019.0.20190410。 我们将通过其通用资源号（“URN”）识别此映像。 
 
:::image type="content" source="media/azure-stack-edge-create-virtual-machine-marketplace-image/marketplace-image-1.png" alt-text="市场映像列表":::

### <a name="commonly-used-marketplace-images"></a>常用市场映像

下面是一些最常用映像的 URN 列表。 如果只需要特定 OS 的最新版本，则可以在 URN 中将版本号替换为“latest”。 例如，“MicrosoftWindowsServer:WindowsServer:2019-Datacenter:Latest”。 


| (OS)              | SKU                                     | 版本               | URN                                                                                       |
|-----------------|-----------------------------------------|-----------------------|-------------------------------------------------------------------------------------------|
| Windows Server  | 2019 Datacenter                         | 17763.1879.2104091832 | MicrosoftWindowsServer:WindowsServer:2019-Datacenter:17763.1879.2104091832                |
| Windows Server  | 2019 Datacenter（30 GB 小型磁盘）      | 17763.1879.2104091832 | MicrosoftWindowsServer:WindowsServer:2019-Datacenter-smalldisk:17763.1879.2104091832      |
| Windows Server  | 2019 Datacenter Core                    | 17763.1879.2104091832 | MicrosoftWindowsServer:WindowsServer:2019-Datacenter-Core:17763.1879.2104091832           |
| Windows Server  | 2019 Datacenter Core（30 GB 小型磁盘） | 17763.1879.2104091832 | MicrosoftWindowsServer:WindowsServer:2019-Datacenter-Core-smalldisk:17763.1879.2104091832 |
| Windows 桌面 | Windows 10 20H2 Pro                     | 19042.928.2104091209  | MicrosoftWindowsDesktop:Windows-10:20h2-pro:19042.928.2104091209                          |
| Ubuntu Server   | Canonical Ubuntu Server 18.04 LTS       | 18.04.202002180       | Canonical:UbuntuServer:18.04-LTS:18.04.202002180                                          |
| Ubuntu Server   | Canonical Ubuntu Server 16.04 LTS       | 16.04.202104160       | Canonical:UbuntuServer:16.04-LTS:16.04.202104160                                          |
| CentOS          | CentOS 8.1                              | 8.1.2020062400        | OpenLogic:CentOS:8_1:8.1.2020062400                                                       |
| CentOS          | CentOS 7.7                              | 7.7.2020062400        | OpenLogic:CentOS:7.7:7.7.2020062400                                                       |



## <a name="create-a-new-managed-disk-from-the-marketplace-image"></a>从市场映像创建新的托管磁盘

从所选市场映像创建 Azure 托管磁盘。 

1. 设置一些参数。

    ```azurecli
    $urn = <URN of the Marketplace image> #Example: “MicrosoftWindowsServer:WindowsServer:2019-Datacenter:Latest”
    $diskName = <disk name> #Name for new disk to be created
    $diskRG = <resource group> #Resource group that contains the new disk
    ```


1. 创建磁盘并生成 SAS 访问 URL。

    ```azurecli
    az disk create -g $diskRG -n $diskName --image-reference $urn
    $sas = az disk grant-access --duration-in-seconds 36000 --access-level Read --name $diskName --resource-group $diskRG
    $diskAccessSAS = ($sas | ConvertFrom-Json)[0].accessSas 
    ```

下面是示例输出：

```output
PS /home/user> $urn = “MicrosoftWindowsServer:WindowsServer:2019-Datacenter:Latest”
PS /home/user> $diskName = "newmanageddisk1"
PS /home/user> $diskRG = "newrgmd1"
PS /home/user> az disk create -g $diskRG -n $diskName --image-reference $urn
{
  "burstingEnabled": null,
  "creationData": {
    "createOption": "FromImage",
    "galleryImageReference": null,
    "imageReference": {
      "id": "/Subscriptions/db4e2fdb-6d80-4e6e-b7cd-736098270664/Providers/Microsoft.Compute/Locations/eastus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2019-Datacenter/Versions/17763.1935.2105080716",
      "lun": null
    },
    "logicalSectorSize": null,
    "sourceResourceId": null,
    "sourceUniqueId": null,
    "sourceUri": null,
    "storageAccountId": null,
    "uploadSizeBytes": null
  },
  "diskAccessId": null,
  "diskIopsReadOnly": null,
  "diskIopsReadWrite": 500,
  "diskMBpsReadOnly": null,
  "diskMBpsReadWrite": 100,
  "diskSizeBytes": 136367308800,
  "diskSizeGb": 127,
  "diskState": "Unattached",
  "encryption": {
    "diskEncryptionSetId": null,
    "type": "EncryptionAtRestWithPlatformKey"
  },
  "encryptionSettingsCollection": null,
  "extendedLocation": null,
  "hyperVGeneration": "V1",
  "id": "/subscriptions/db4e2fdb-6d80-4e6e-b7cd-736098270664/resourceGroups/newrgmd1/providers/Microsoft.Compute/disks/NewManagedDisk1",
  "location": "eastus",
  "managedBy": null,
  "managedByExtended": null,
  "maxShares": null,
  "name": "NewManagedDisk1",
  "networkAccessPolicy": "AllowAll",
  "osType": "Windows",
  "propertyUpdatesInProgress": null,
  "provisioningState": "Succeeded",
  "purchasePlan": null,
  "resourceGroup": "newrgmd1",
  "securityProfile": null,
  "shareInfo": null,
  "sku": {
    "name": "Premium_LRS",
    "tier": "Premium"
  },
  "supportsHibernation": null,
  "tags": {},
  "tier": "P10",
  "timeCreated": "2021-06-08T00:39:34.205982+00:00",
  "type": "Microsoft.Compute/disks",
  "uniqueId": "1a649ad4-3b95-471e-89ef-1d2ed1f51525",
  "zones": null
}

PS /home/user> $sas = az disk grant-access --duration-in-seconds 36000 --access-level Read --name $diskName --resource-group $diskRG
PS /home/user>  $diskAccessSAS = ($sas | ConvertFrom-Json)[0].accessSas
PS /home/user>
```

## <a name="export-a-vhd-from-the-managed-disk-to-azure-storage"></a>将 VHD 从托管磁盘导出到 Azure 存储 

此步骤会将 VHD 从托管磁盘导出到首选 Azure blob 存储帐户。 然后可以使用此 VHD 在 Azure Stack Edge 上创建 VM 映像。

1. 设置复制 VHD 的目标存储帐户。
    
    ```azurecli
    $storageAccountName = <destination storage account name>
    $containerName = <destination container name>
    $destBlobName = <blobname.vhd> #Blob that will be created, including .vhd extension
    $storageAccountKey = <storage account key>
    ```

1. 将 VHD 复制到目标存储帐户。

    ```azurecli
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
    Start-AzureStorageBlobCopy -AbsoluteUri $diskAccessSAS -DestContainer $containerName -DestContext $destContext -DestBlob $destBlobName
    ```

    VHD 复制将需要几分钟才能完成。 运行以下命令，确保在完成复制后再继续。 完成后，状态字段将显示“成功”。
    
    ```azurecli
    Get-AzureStorageBlobCopyState –Container $containerName –Context $destContext -Blob $destBlobName 
    ```

下面是示例输出：

```output
PS /home/user> $storageAccountName = "edgeazurevmeus"
PS /home/user> $containerName = "azurevmmp"
PS /home/user> $destBlobName = "newblobmp.vhd"
PS /home/user> $storageAccountKey = "n9sCytWLdTBz0F4Sco9SkPGWp6BJBtf7BJBk79msf1PfxJGQdqSfu6TboZWZ10xyZdc4y+Att08cC9B79jB0YA=="

PS /home/user> $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
PS /home/user> Start-AzureStorageBlobCopy -AbsoluteUri $diskAccessSAS -DestContainer $containerName -DestContext $destContext -DestBlob $destBlobName

   AccountName: edgeazurevmeus, ContainerName: azurevmmp

Name                 BlobType  Length          ContentType                    LastModified         AccessTier SnapshotTime                 IsDeleted  VersionId
----                 --------  ------          -----------                    ------------         ---------- ------------                 ---------  ---------
newblobmp.vhd        PageBlob  -1                                             2021-06-08 00:50:10Z                                         False

PS /home/user> Get-AzureStorageBlobCopyState –Container $containerName –Context $destContext -Blob $destBlobName

CopyId                  : 24a1e3f5-886a-490d-9dd7-562bb4acff58
CompletionTime          :
Status                  : Pending
Source                  : https://md-lfn221fppr2c.blob.core.windows.net/d4tb2hp5ff2q/abcd?sv=2018-03-28&sr=b&si=4f588db1-9aac-44d9-9607-35497cc08a7f
BytesCopied             : 696254464
TotalBytes              : 136367309312
StatusDescription       :
DestinationSnapshotTime :

PS /home/user> Get-AzureStorageBlobCopyState –Container $containerName –Context $destContext -Blob $destBlobName

CopyId                  : 24a1e3f5-886a-490d-9dd7-562bb4acff58
CompletionTime          : 6/8/2021 12:57:26 AM +00:00
Status                  : Success
Source                  : https://md-lfn221fppr2c.blob.core.windows.net/d4tb2hp5ff2q/abcd?sv=2018-03-28&sr=b&si=4f588db1-9aac-44d9-9607-35497cc08a7f
BytesCopied             : 136367309312
TotalBytes              : 136367309312
StatusDescription       :
DestinationSnapshotTime :
```

## <a name="clean-up-the-managed-disk"></a>清理托管磁盘

要删除创建的托管磁盘，请执行以下步骤：

```azurecli
az disk revoke-access --name $diskName --resource-group $diskRG 
az disk delete --name $diskName --resource-group $diskRG --yes 
```
删除过程需要几分钟时间才能完成。

## <a name="next-steps"></a>后续步骤

[在 Azure Stack Edge Pro GPU 设备上部署 VM](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)。