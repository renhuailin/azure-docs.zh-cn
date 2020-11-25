---
title: include 文件
description: 文件
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: mingshen-ms
ms.author: krsh
ms.date: 10/20/2020
ms.openlocfilehash: 6c7536e38a0d2cf7d4e906947aff645c74e459c0
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027427"
---
## <a name="generalize-the-image"></a>通用化映像

Azure 市场中的所有映像必须可采用一般形式重复使用。 若要实现这种可重用性，必须将操作系统 VHD 通用化，此操作会从 VM 中删除所有特定于实例的标识符以及软件驱动程序。

### <a name="for-windows"></a>对于 Windows

Windows OS 磁盘是利用 [sysprep](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview) 工具通用化的。 如果以后更新或重新配置了操作系统，则必须再次运行 sysprep。

> [!WARNING]
> 运行 sysprep 后，将 VM 关闭，直到部署完成，因为更新可能会自动运行。 此关闭操作可避免后续更新对操作系统或安装的服务做出特定于实例的更改。 有关运行 sysprep 的详细信息，请参阅[通用化 VHD 的步骤](../../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep)。

### <a name="for-linux"></a>对于 Linux

以下过程将通用化 Linux VM，并将其重新部署为单独的 VM。 有关详细信息，请参阅[如何创建虚拟机或 VHD 的映像](../../virtual-machines/linux/capture-image.md)。 当你到达称为 "从捕获的映像创建 VM" 的部分时，你可以停止。

1. 删除 Azure Linux 代理。
    1. 使用 SSH 客户端连接到 Linux VM。
    2. 在 SSH 窗口中，输入以下命令： `sudo waagent –deprovision+user` 。
    3. 键入 Y 以继续操作（可将“-force”参数添加到前一个命令，以避免确认步骤） 。
    4. 命令完成后，输入 **Exit** 以关闭 SSH 客户端。
2. 停止虚拟机。
    1. 在 Azure 门户，选择资源组 (RG) 并取消分配 VM。
    2. VM 现已通用化，可使用此 VM 磁盘创建新的 VM。

### <a name="take-a-snapshot-of-the-vm-disk"></a>拍摄 VM 磁盘的快照

1. 登录 [Azure 门户](https://ms.portal.azure.com/)。
2. 从左上角选择 " **创建资源**"，搜索并选择 " **快照**"。
3. 在 "快照" 边栏选项卡中，选择 "  **创建**"。
4. 输入快照的 **名称** 。
5. 选择现有资源组或输入新资源组的名称。
6. 对于 **源磁盘**，选择要获取其快照的托管磁盘。
7. 选择用于存储快照的“帐户类型”。 使用 **Standard HDD**，除非需要将其存储在高性能 SSD 上。
8. 选择“创建”  。

#### <a name="extract-the-vhd"></a>提取 VHD

使用以下脚本将快照导出到存储帐户中的 VHD。

```JSON
#Provide the subscription Id where the snapshot is created
subscriptionId=yourSubscriptionId

#Provide the name of your resource group where the snapshot is created
resourceGroupName=myResourceGroupName

#Provide the snapshot name
snapshotName=mySnapshot

#Provide Shared Access Signature (SAS) expiry duration in seconds (such as 3600)
#Know more about SAS here: https://docs.microsoft.com/en-us/azure/storage/storage-dotnet-shared-access-signature-part-1
sasExpiryDuration=3600

#Provide storage account name where you want to copy the underlying VHD file. 
storageAccountName=mystorageaccountname

#Name of the storage container where the downloaded VHD will be stored.
storageContainerName=mystoragecontainername

#Provide the key of the storage account where you want to copy the VHD 
storageAccountKey=mystorageaccountkey

#Give a name to the destination VHD file to which the VHD will be copied.
destinationVHDFileName=myvhdfilename.vhd

az account set --subscription $subscriptionId

sas=$(az snapshot grant-access --resource-group $resourceGroupName --name $snapshotName --duration-in-seconds $sasExpiryDuration --query [accessSas] -o tsv)

az storage blob copy start --destination-blob $destinationVHDFileName --destination-container $storageContainerName --account-name $storageAccountName --account-key $storageAccountKey --source-uri $sas
```

#### <a name="script-explanation"></a>脚本说明

此脚本使用以下命令生成快照的 SAS URI，并使用 SAS URI 将基础 VHD 复制到存储帐户。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
| --- | --- |
| az disk grant-access | 生成只读 SAS，使用该 SAS 可以将基础 VHD 文件复制到存储帐户或将其下载到本地
| az storage blob copy start | 以异步方式将 blob 从一个存储帐户复制到另一个存储帐户。 使用 `az storage blob show` 检查新 blob 的状态。 |
|
