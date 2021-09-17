---
title: Azure CLI - 使用专用链接限制对托管磁盘的导入/导出访问
description: 使用 Azure CLI 为托管磁盘启用专用链接。 仅允许在虚拟网络中安全地导出和导入磁盘。
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 07/15/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 16ba38828cc9137e7d69e113126826b254aaf957
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122688988"
---
# <a name="azure-cli---restrict-importexport-access-for-managed-disks-with-private-links"></a>Azure CLI - 使用专用链接限制对托管磁盘的导入/导出访问

适用于：:heavy_check_mark: Linux VM :heavy_check_mark: 灵活规模集 

可以使用[专用终结点](../../private-link/private-endpoint-overview.md)来限制托管磁盘的导出和导入，并通过[专用链接](../../private-link/private-link-overview.md)从 Azure 虚拟网络上的客户端安全地访问数据。 专用终结点将虚拟网络地址空间中的 IP 地址用于托管磁盘服务。 虚拟网络上客户端与托管磁盘之间的网络流量仅穿过虚拟网络以及 Microsoft 主干网络上的专用链接，因此不会从公共 Internet 公开。

若要使用专用链接导出/导入托管磁盘，首先要创建磁盘访问资源，并通过创建专用终结点将其链接到同一订阅中的虚拟网络。 然后，将磁盘或快照与磁盘访问的实例相关联。 最后，将磁盘或快照的 NetworkAccessPolicy 属性设置为 `AllowPrivate`。 这将限制对虚拟网络的访问。 

可以将 NetworkAccessPolicy 属性设置为 `DenyAll`，以防止任何人导出磁盘或快照的数据。 NetworkAccessPolicy 属性的默认值为 `AllowAll`。

## <a name="limitations"></a>限制

[!INCLUDE [virtual-machines-disks-private-links-limitations](../../../includes/virtual-machines-disks-private-links-limitations.md)]


## <a name="log-in-into-your-subscription-and-set-your-variables"></a>登录到订阅并设置变量

```azurecli-interactive
subscriptionId=yourSubscriptionId
resourceGroupName=yourResourceGroupName
region=northcentralus
diskAccessName=yourDiskAccessForPrivateLinks
vnetName=yourVNETForPrivateLinks
subnetName=yourSubnetForPrivateLinks
privateEndPointName=yourPrivateLinkForSecureMDExportImport
privateEndPointConnectionName=yourPrivateLinkConnection

#The name of an existing disk which is the source of the snapshot
sourceDiskName=yourSourceDiskForSnapshot

#The name of the new snapshot which will be secured via Private Links
snapshotNameSecuredWithPL=yourSnapshotNameSecuredWithPL

az login

az account set --subscription $subscriptionId

```

## <a name="create-a-disk-access-using-azure-cli"></a>使用 Azure CLI 创建磁盘访问
```azurecli
az disk-access create -n $diskAccessName -g $resourceGroupName -l $region

diskAccessId=$(az disk-access show -n $diskAccessName -g $resourceGroupName --query [id] -o tsv)
```

## <a name="create-a-virtual-network"></a>创建虚拟网络

专用终结点不支持网络安全组 (NSG) 等网络策略。 若要在给定的子网上部署专用终结点，需要在该子网上显式配置禁用设置。 

```azurecli
az network vnet create --resource-group $resourceGroupName \
    --name $vnetName \
    --subnet-name $subnetName
```
## <a name="disable-subnet-private-endpoint-policies"></a>禁用子网专用终结点策略

Azure 会将资源部署到虚拟网络中的子网，因此，你需要更新子网，以禁用专用终结点网络策略。 

```azurecli
az network vnet subnet update --resource-group $resourceGroupName \
    --name $subnetName  \
    --vnet-name $vnetName \
    --disable-private-endpoint-network-policies true
```
## <a name="create-a-private-endpoint-for-the-disk-access-object"></a>为磁盘访问对象创建专用终结点

```azurecli
az network private-endpoint create --resource-group $resourceGroupName \
    --name $privateEndPointName \
    --vnet-name $vnetName  \
    --subnet $subnetName \
    --private-connection-resource-id $diskAccessId \
    --group-ids disks \
    --connection-name $privateEndPointConnectionName
```

## <a name="configure-the-private-dns-zone"></a>配置专用 DNS 区域

创建存储 blob 域的专用 DNS 区域，创建与虚拟网络关联的链接，并创建 DNS 区域组以将专用终结点与专用 DNS 区域相关联。 

```azurecli
az network private-dns zone create --resource-group $resourceGroupName \
    --name "privatelink.blob.core.windows.net"

az network private-dns link vnet create --resource-group $resourceGroupName \
    --zone-name "privatelink.blob.core.windows.net" \
    --name yourDNSLink \
    --virtual-network $vnetName \
    --registration-enabled false 

az network private-endpoint dns-zone-group create \
   --resource-group $resourceGroupName \
   --endpoint-name $privateEndPointName \
   --name yourZoneGroup \
   --private-dns-zone "privatelink.blob.core.windows.net" \
   --zone-name disks
```

## <a name="create-a-disk-protected-with-private-links"></a>创建受专用链接保护的磁盘
```azurecli-interactive
resourceGroupName=yourResourceGroupName
region=northcentralus
diskAccessName=yourDiskAccessName
diskName=yourDiskName
diskSkuName=Standard_LRS
diskSizeGB=128

diskAccessId=$(az resource show -n $diskAccessName -g $resourceGroupName --namespace Microsoft.Compute --resource-type diskAccesses --query [id] -o tsv)

az disk create -n $diskName \
-g $resourceGroupName \
-l $region \
--size-gb $diskSizeGB \
--sku $diskSkuName \
--network-access-policy AllowPrivate \
--disk-access $diskAccessId 
```

## <a name="create-a-snapshot-of-a-disk-protected-with-private-links"></a>创建受专用链接保护的磁盘的快照
```azurecli-interactive
resourceGroupName=yourResourceGroupName
region=northcentralus
diskAccessName=yourDiskAccessName
sourceDiskName=yourSourceDiskForSnapshot
snapshotNameSecuredWithPL=yourSnapshotName

diskId=$(az disk show -n $sourceDiskName -g $resourceGroupName --query [id] -o tsv)

diskAccessId=$(az resource show -n $diskAccessName -g $resourceGroupName --namespace Microsoft.Compute --resource-type diskAccesses --query [id] -o tsv)

az snapshot create -n $snapshotNameSecuredWithPL \
-g $resourceGroupName \
-l $region \
--source $diskId \
--network-access-policy AllowPrivate \
--disk-access $diskAccessId 
```

## <a name="next-steps"></a>后续步骤

- 将 VHD 上传到 Azure 或将托管磁盘复制到其他区域 - [Azure CLI](disks-upload-vhd-to-managed-disk-cli.md) 或 [Azure PowerShell 模块](../windows/disks-upload-vhd-to-managed-disk-powershell.md)
- 下载 VHD - [Windows](../windows/download-vhd.md) 或 [Linux](download-vhd.md)
- [专用链接常见问题解答](/azure/virtual-machines/faq-for-disks#private-links-for-securely-exporting-and-importing-managed-disks)
- [使用 CLI 将托管快照作为 VHD 导出/复制到不同区域中的存储帐户](/previous-versions/azure/virtual-machines/scripts/virtual-machines-cli-sample-copy-managed-disks-vhd)