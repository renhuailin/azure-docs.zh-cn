---
title: 创建 Azure HPC 缓存
description: 如何创建 Azure HPC 缓存实例
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/15/2021
ms.author: v-erkel
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b7789af76572eeaa3dfdfe4c6ff379889341033e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128557453"
---
# <a name="create-an-azure-hpc-cache"></a>创建 Azure HPC 缓存

使用 Azure 门户或 Azure CLI 创建缓存。

![Azure 门户中的缓存概述的屏幕截图，底部带有“创建”按钮](media/hpc-cache-home-page.png)

单击下图观看有关创建缓存和添加存储目标的[视频演示](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/)。

[![视频缩略图：Azure HPC 缓存：设置（单击此项可访问视频页）](media/video-4-setup.png)](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/)

## <a name="portal"></a>[Portal](#tab/azure-portal)

## <a name="define-basic-details"></a>定义基本详细信息

![Azure 门户中项目详细信息页的屏幕截图。](media/hpc-cache-create-basics.png)

在“项目详细信息”中，选择将托管缓存的订阅和资源组。

在“服务详细信息”中，设置缓存名称和其他属性：

* 位置 - 选择其中一个[支持的区域](hpc-cache-overview.md#region-availability)。
* 虚拟网络 - 可选择现有虚拟网络或创建新的虚拟网络。
* 子网 - 选择或创建具有至少 64 个 IP 地址 (/24) 的子网。 此子网必须仅用于此 Azure HPC 缓存实例。

## <a name="set-cache-capacity"></a>设置缓存容量
<!-- referenced from GUI - update aka.ms/hpc-cache-iops link if you change this header text -->

在“缓存”页上，必须设置缓存的容量。 此处设置的值决定了服务于客户端的缓存速度及其可容纳的数据大小。

容量还会影响缓存的成本，以及它可以支持的存储目标数。

缓存容量是两个值的组合：

* 缓存的最大数据传输速率（吞吐量）（GB/秒）
* 为缓存数据分配的存储量 (TB)

![Azure 门户中缓存大小调整页的屏幕截图。](media/hpc-cache-create-capacity.png)

### <a name="understand-throughput-and-cache-size"></a>理解吞吐量和缓存大小

有几个因素可能会影响 Azure HPC 缓存的效率，但其中一个最重要的因素是选择适当的吞吐量值和缓存存储大小。

请记住，选择吞吐量值时，实际的数据传输速率取决于工作负荷、网络速度和存储目标的类型。

你选择的值设置整个缓存系统的最大吞吐量，但其中一些用于开销任务。 例如，如果客户端请求尚未存储在缓存中的文件，或者该文件被标记为过期，则缓存将使用其部分吞吐量从后端存储中提取该文件。

Azure HPC 缓存决定缓存和预加载哪些文件，以最大限度提高缓存命中率。 将持续评估缓存内容，且不经常访问的文件将移动到长期存储。

选择一种缓存存储大小，该大小可以轻松保留活动的工作文件集，并具有额外的空间来存储元数据和其他开销。

吞吐量和缓存大小也会影响特定缓存支持的存储目标数。 若要使用 10 个以上缓存存储目标，必须为吞吐量大小选择最高的可用缓存存储大小值，或选择一种高吞吐量只读配置。 有关详细信息，请参阅[添加存储目标](hpc-cache-add-storage.md#size-your-cache-correctly-to-support-your-storage-targets)。

若在正确调整缓存大小方面需要帮助，请联系 Microsoft 服务和支持部门。

### <a name="choose-the-cache-type-for-your-needs"></a>根据需求选择缓存类型

选择缓存容量时，你可能会注意到某些吞吐量值具有固定的缓存大小，而其他吞吐量值允许从多个缓存大小选项中进行选择。 这是因为缓存基础结构有两种不同的样式：

* 标准缓存：列于在吞吐量菜单中的“读写缓存”下

  使用标准缓存时，可从多个缓存大小值中选择。 可以将这些缓存配置为“只读缓存”或“读写缓存”。

* 高吞吐量缓存：列于吞吐量菜单中的“只读缓存”下

  高吞吐量配置设置了缓存大小，因为它们已预配 NVME 磁盘。 它们旨在优化文件只读访问。

![门户中最大吞吐量菜单的屏幕截图。 “读写缓存”标题下有几个大小选项，“只读缓存”标题下也有几个。](media/rw-ro-cache-sizing.png)

下表说明了这两个选项之间的一些重要差异。

| 属性 | 标准缓存 | 高吞吐量缓存 |
|--|--|--|
| 吞吐量菜单类别 |“读写缓存”| “只读缓存”|
| 吞吐量大小 | 2、4 或 8 GB/秒 | 4.5、9 或 16 GB/秒 |
| 缓存大小 | 3、6 或 12 TB（2 GB/秒）<br/> 6、12 或 24 TB（4 GB/秒）<br/> 12、24 或 48 TB（8 GB/秒）| 21 TB（4.5 GB/秒） <br/> 42 TB（9 GB/秒） <br/> 84 TB（16 GB/秒） |
| 最大存储帐户数 | [10 或 20](hpc-cache-add-storage.md#size-your-cache-correctly-to-support-your-storage-targets)，具体取决于所选缓存大小 | 20 |
| 兼容的存储目标类型 | Azure Blob、本地 NFS 存储、已启用 NFS 的 blob | 本地 NFS 存储 <br/>处于此组合预览状态下的已启用 NFS 的 blob |
| 缓存样式 | 读取缓存或读写缓存 | 只读缓存 |
| 不需要时，可终止缓存以节省成本 | 是 | 否 |

若要了解有关这些选项的详细信息，请参阅下列信息：

* [最大存储帐户数](hpc-cache-add-storage.md#size-your-cache-correctly-to-support-your-storage-targets)
* [“读写缓存”模式](cache-usage-models.md#basic-file-caching-concepts)

## <a name="enable-azure-key-vault-encryption-optional"></a>启用 Azure Key Vault 加密（可选）

如果要管理用于缓存存储的加密密钥，请在“磁盘加密密钥”页上提供 Azure Key Vault 信息。 密钥保管库必须位于与缓存相同的区域和订阅中。

如果你不需要客户托管的密钥，可以跳过此部分。 默认情况下，Azure 使用 Microsoft 托管的密钥加密数据。 有关详细信息，请阅读 [Azure 存储加密](../storage/common/storage-service-encryption.md)。

> [!NOTE]
> 创建缓存后，不能在 Microsoft 托管的密钥和客户托管的密钥之间进行更改。

有关客户托管的密钥加密过程的完整说明，请参阅[将客户托管的加密密钥用于 Azure HPC 缓存](customer-keys.md)。

![“加密密钥”页的屏幕截图，其中选择了“客户托管”，并显示“客户密钥设置”和“托管标识”配置窗体。](media/create-encryption.png)

选择“客户托管”以选择客户托管的密钥加密。 将显示密钥保管库规范字段。 选择要使用的 Azure Key Vault，然后选择要用于此缓存的密钥和版本。 密钥必须是 2048 位的 RSA 密钥。 你可以从此页面创建新的密钥保管库、密钥或密钥版本。

若要使用[自动密钥轮换](../virtual-machines/disk-encryption.md#automatic-key-rotation-of-customer-managed-keys)，请选中“始终使用当前密钥版本”复选框。

若要对此缓存使用特定的托管标识，请在“托管标识”部分进行配置。 更多详细信息，请参阅[“什么是 Azure 资源托管标识？”](../active-directory/managed-identities-azure-resources/overview.md)。

> [!NOTE]
> 创建缓存后，无法更改分配标识。

若使用系统分配的托管标识或用户分配的标识，而该标识尚未有权访问密钥保管库，则创建缓存后必须执行额外的步骤。 此手动步骤会授权缓存的托管标识使用密钥保管库。

* 阅读[“为缓存选择托管标识选项”](customer-keys.md#choose-a-managed-identity-option-for-the-cache)，理解托管标识设置之间的差异。
* 关于手动步骤的详细信息，请阅读[“通过缓存授权 Azure Key Vault 加密”](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache-if-needed)。

## <a name="add-resource-tags-optional"></a>添加资源标记（可选）

在“标记”页上，可以向 Azure HPC 缓存实例添加[资源标记](../azure-resource-manager/management/tag-resources.md)。

## <a name="finish-creating-the-cache"></a>完成缓存创建

配置新缓存后，请单击“查看+创建”选项卡。门户将验证选择，并允许你查看选择。 如果所有内容都正确，请单击“创建”。

缓存创建花费大约 10 分钟。 可在 Azure 门户的通知面板中跟踪进度。

![门户中缓存创建“正在部署”和“通知”页的屏幕截图](media/hpc-cache-deploy-status.png)

创建完成后，将显示一条通知，其中包含指向新的 Azure HPC 缓存实例的链接，并且缓存将显示在订阅的 **资源** 列表中。

![Azure 门户中的 Azure HPC 缓存实例的屏幕截图](media/hpc-cache-new-overview.png)

> [!NOTE]
> 若缓存使用客户托管的加密密钥，且在创建后需要手动授权步骤，在部署状态更改为“完成”之前，缓存可能会出现在“资源”列表中。 缓存状态为“正在等待密钥”时，即可[授权它](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache-if-needed)使用密钥保管库。

## <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

## <a name="create-the-cache-with-azure-cli"></a>使用 Azure CLI 创建缓存

[设置适用于 Azure HPC 缓存的 Azure CLI](./az-cli-prerequisites.md)。

> [!NOTE]
> Azure CLI 当前不支持使用客户托管的加密密钥创建缓存。 使用 Azure 门户。

使用 [az hpc-cache create](/cli/azure/hpc-cache#az_hpc_cache_create) 命令创建新的 Azure HPC 缓存。

提供以下值：

* 设置资源组名称
* 缓存名称
* Azure 区域
* 缓存子网，格式如下：

  `--subnet "/subscriptions/<subscription_id>/resourceGroups/<cache_resource_group>/providers/Microsoft.Network/virtualNetworks/<virtual_network_name>/subnets/<cache_subnet_name>"`

  缓存子网需要至少 64 个 IP 地址 (/24)，并且不能容纳其他任何资源。

* 缓存容量。 以下两个值设置 Azure HPC 缓存的最大吞吐量：

  * 缓存大小 (GB)
  * 缓存基础结构中使用的虚拟机的 SKU

  [az hpc-cache skus list](/cli/azure/hpc-cache/skus) 显示了可用的 SKU 以及每个 SKU 的有效缓存大小选项。 缓存大小选项的范围为 3 TB 到 48 TB，但仅支持某些值。

  此图显示在准备此文档时（2020 年 7 月），哪些缓存大小和 SKU 组合有效。

  | 缓存大小 | Standard_2G | Standard_4G | Standard_8G |
  |------------|-------------|-------------|-------------|
  | 3072 GB    | 是         | 否          | 否          |
  | 6144 GB    | 是         | 是         | 否          |
  | 12288 GB   | 是         | 是         | 是         |
  | 24576 GB   | 否          | 是         | 是         |
  | 49152 GB   | 否          | 否          | 是         |

  如果要对缓存使用 10 个以上存储目标，请为 SKU 选择最高的可用缓存大小值。 这些配置最多支持 20 个存储目标。

  有关定价、吞吐量以及如何为工作流适当调整缓存大小的重要信息，请阅读“门户说明”选项卡上的“设置缓存容量”部分。

缓存创建示例：

```azurecli
az hpc-cache create --resource-group doc-demo-rg --name my-cache-0619 \
    --location "eastus" --cache-size-gb "3072" \
    --subnet "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default" \
    --sku-name "Standard_2G"
```

创建缓存需要几分钟。 成功后，create 命令返回如下输出：

```azurecli
{
  "cacheSizeGb": 3072,
  "health": {
    "state": "Healthy",
    "statusDescription": "The cache is in Running state"
  },
  "id": "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.StorageCache/caches/my-cache-0619",
  "location": "eastus",
  "mountAddresses": [
    "10.3.0.17",
    "10.3.0.18",
    "10.3.0.19"
  ],
  "name": "my-cache-0619",
  "provisioningState": "Succeeded",
  "resourceGroup": "doc-demo-rg",
  "sku": {
    "name": "Standard_2G"
  },
  "subnet": "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default",
  "tags": null,
  "type": "Microsoft.StorageCache/caches",
  "upgradeStatus": {
    "currentFirmwareVersion": "5.3.42",
    "firmwareUpdateDeadline": "0001-01-01T00:00:00+00:00",
    "firmwareUpdateStatus": "unavailable",
    "lastFirmwareUpdate": "2020-04-01T15:19:54.068299+00:00",
    "pendingFirmwareVersion": null
  }
}
```

该消息包含一些有用的信息，包括以下项：

* 客户端装载地址 - 准备好将客户端连接到缓存时，请使用这些 IP 地址。 若要了解详细信息，请阅读[装载 Azure HPC 缓存](hpc-cache-mount.md)。
* 升级状态 - 发布软件更新时，此消息将会更改。 你可以在方便时手动[升级缓存软件](hpc-cache-manage.md#upgrade-cache-software)，否则数天后将自动应用升级。

## <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

> [!CAUTION]
> Az.HPCCache PowerShell 模块目前为公共预览版。 此预览版本未提供服务级别协议。 对于生产型工作负荷，不建议使用该预览版。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="requirements"></a>要求

如果选择在本地使用 PowerShell，则本文要求安装 Az PowerShell 模块，并使用 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet 连接到 Azure 帐户。 有关安装 Az PowerShell 模块的详细信息，请参阅[安装 Azure PowerShell](/powershell/azure/install-az-ps)。 如果你选择使用 Cloud Shell，请参阅 [Azure Cloud Shell 概述](../cloud-shell/overview.md)来了解详细信息。

> [!IMPORTANT]
> 尽管 Az.HPCCache PowerShell 模块为预览版，但你需要使用 `Install-Module` cmdlet 单独安装它。 此 PowerShell 模块正式发布后，它会包含在将来的 Az PowerShell 模块发行版中，并在 Azure Cloud Shell 中本机提供。

```azurepowershell-interactive
Install-Module -Name Az.HPCCache
```

## <a name="create-the-cache-with-azure-powershell"></a>使用 Azure PowerShell 创建缓存

> [!NOTE]
> Azure PowerShell 当前不支持使用客户托管的加密密钥创建缓存。 使用 Azure 门户。

使用 [New-AzHpcCache](/powershell/module/az.hpccache/new-azhpccache) cmdlet 创建新的 Azure HPC 缓存。

提供以下值：

* 设置资源组名称
* 缓存名称
* Azure 区域
* 缓存子网，格式如下：

  `-SubnetUri "/subscriptions/<subscription_id>/resourceGroups/<cache_resource_group>/providers/Microsoft.Network/virtualNetworks/<virtual_network_name>/subnets/<cache_subnet_name>"`

  缓存子网需要至少 64 个 IP 地址 (/24)，并且不能容纳其他任何资源。

* 缓存容量。 以下两个值设置 Azure HPC 缓存的最大吞吐量：

  * 缓存大小 (GB)
  * 缓存基础结构中使用的虚拟机的 SKU

  [Get-AzHpcCacheSku](/powershell/module/az.hpccache/get-azhpccachesku) 显示了可用的 SKU 以及每个 SKU 的有效缓存大小选项。 缓存大小选项的范围为 3 TB 到 48 TB，但仅支持某些值。

  此图显示在准备此文档时（2020 年 7 月），哪些缓存大小和 SKU 组合有效。

  | 缓存大小 | Standard_2G | Standard_4G | Standard_8G |
  |------------|-------------|-------------|-------------|
  | 3072 GB    | 是         | 否          | 否          |
  | 6144 GB    | 是         | 是         | 否          |
  | 12,288 GB   | 是         | 是         | 是         |
  | 24,576 GB   | 否          | 是         | 是         |
  | 49,152 GB   | 否          | 否          | 是         |

  有关定价、吞吐量以及如何为工作流适当调整缓存大小的重要信息，请阅读“门户说明”选项卡上的“设置缓存容量”部分。

缓存创建示例：

```azurepowershell-interactive
$cacheParams = @{
  ResourceGroupName = 'doc-demo-rg'
  CacheName = 'my-cache-0619'
  Location = 'eastus'
  cacheSize = '3072'
  SubnetUri = "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default"
  Sku = 'Standard_2G'
}
New-AzHpcCache @cacheParams
```

创建缓存需要几分钟。 成功后，create 命令返回以下输出：

```Output
cacheSizeGb       : 3072
health            : @{state=Healthy; statusDescription=The cache is in Running state}
id                : /subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.StorageCache/caches/my-cache-0619
location          : eastus
mountAddresses    : {10.3.0.17, 10.3.0.18, 10.3.0.19}
name              : my-cache-0619
provisioningState : Succeeded
resourceGroup     : doc-demo-rg
sku               : @{name=Standard_2G}
subnet            : /subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default
tags              :
type              : Microsoft.StorageCache/caches
upgradeStatus     : @{currentFirmwareVersion=5.3.42; firmwareUpdateDeadline=1/1/0001 12:00:00 AM; firmwareUpdateStatus=unavailable; lastFirmwareUpdate=4/1/2020 10:19:54 AM; pendingFirmwareVersion=}
```

该消息包含一些有用的信息，包括以下项：

* 客户端装载地址 - 准备好将客户端连接到缓存时，请使用这些 IP 地址。 若要了解详细信息，请阅读[装载 Azure HPC 缓存](hpc-cache-mount.md)。
* 升级状态 - 发布软件更新时，此消息将会更改。 你可以在方便时手动[升级缓存软件](hpc-cache-manage.md#upgrade-cache-software)，否则数天后将自动应用升级。

---

## <a name="next-steps"></a>后续步骤

缓存出现在“资源”列表中后，即可转到下一步。

* [定义存储目标](hpc-cache-add-storage.md)，以使缓存可以访问数据源。
* 若使用客户托管的加密密钥，且需从缓存的概述页授权 [Azure Key Vault ](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache-if-needed)加密，以完成缓存设置，请参照[“使用客户托管的加密密钥”](customer-keys.md)部分的指导。 在可以添加存储之前，必须执行此步骤。
