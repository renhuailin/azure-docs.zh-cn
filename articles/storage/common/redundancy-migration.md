---
title: 更改存储帐户的复制方式
titleSuffix: Azure Storage
description: 了解如何更改现有存储帐户中数据的复制方式。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/16/2021
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 47445520e92f6c7f4a8e5eb78c404b6875dbe017
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129274057"
---
# <a name="change-how-a-storage-account-is-replicated"></a>更改存储帐户的复制方式

Azure 存储始终会存储数据的多个副本，以防范各种计划内和计划外的事件，包括暂时性的硬件故障、网络中断或断电、大范围自然灾害等。 即使面临故障时，冗余也可确保存储帐户满足 [Azure 存储的服务级别协议 (SLA)](https://azure.microsoft.com/support/legal/sla/storage/)的要求。

Azure 存储提供以下类型的复制：

- 本地冗余存储 (LRS)
- 区域冗余存储 (ZRS)
- 异地冗余存储 (GRS) 或读取访问异地冗余存储 (RA-GRS)
- 异地区域冗余存储 (GZRS) 或读取访问异地区域冗余存储 (RA-GZRS)

有关这些选项每一个的概述，请参阅 [Azure 存储冗余](storage-redundancy.md)。

## <a name="switch-between-types-of-replication"></a>在复制类型之间切换

可以将存储帐户从一种类型的复制切换到任何其他类型，但某些方案比另一些方案更直接。 若要添加或删除对次要区域的异地复制或读取访问权限，可以使用 Azure 门户、PowerShell 或 Azure CLI 来更新复制设置。 但是，如果要通过从 LRS 移动到 ZRS（或相反）来更改主要区域的数据复制方式，则必须执行手动迁移。

下表概述了如何从每种复制类型切换到另一种复制类型：

| 切换 | …到 LRS | …到 GRS/RA-GRS | …到 ZRS | …到 GZRS/RA-GZRS |
|--------------------|----------------------------------------------------|---------------------------------------------------------------------|----------------------------------------------------|---------------------------------------------------------------------|
| <b>…从 LRS</b> | 空值 | 使用 Azure 门户、PowerShell 或 CLI 更改复制设置<sup>1,2</sup> | 执行手动迁移 <br /><br /> OR <br /><br /> 请求实时迁移 | 执行手动迁移 <br /><br /> OR <br /><br /> 首先切换到 GRS/RA-GRS，然后请求实时迁移<sup>3</sup> |
| <b>…从 GRS/RA-GRS</b> | 使用 Azure 门户、PowerShell 或 CLI 更改复制设置 | 空值 | 执行手动迁移 <br /><br /> OR <br /><br /> 首先切换到 LRS，然后请求实时迁移<sup>3</sup> | 执行手动迁移 <br /><br /> OR <br /><br /> 请求实时迁移<sup>3</sup> |
| <b>...从 ZRS</b> | 执行手动迁移 | 执行手动迁移 | 空值 | 请求实时迁移<sup>3</sup> |
| <b>…从 GZRS/RA-GZRS</b> | 执行手动迁移 | 执行手动迁移 | 使用 Azure 门户、PowerShell 或 CLI 更改复制设置 | 空值 |

<sup>1</sup> 会产生一次性出口费用。<br />
<sup>2</sup> 如果存储帐户在存档层中包含 blob，则不支持从 LRS 迁移到 GRS。<br />
<sup>3</sup> 标准常规用途 v2 和高级文件共享存储帐户支持实时迁移。 高级块 blob 或页 blob 存储帐户不支持实时迁移

> [!CAUTION]
> 如果你为 (RA-)GRS 或 (RA-)GZRS 帐户执行了[帐户故障转移](storage-disaster-recovery-guidance.md)，则在故障转移后该帐户在新的主要区域中为本地冗余 (LRS) 帐户。 不支持由于故障转移而将 LRS 帐户实时迁移到 ZRS 或 GZRS。 即使对于所谓的故障回复操作，也是如此。 例如，如果执行从 RA-GZRS 到次要区域 LRS 的帐户故障转移，然后再次将其配置为 RA-GRS 并执行另一轮到原始主要区域的帐户故障转移，则无法联系支持部门以进行到主要区域中 RA-GZRS 的原始实时迁移。 相反，你需要手动迁移到 ZRS 或 GZRS。

## <a name="change-the-replication-setting"></a>更改复制设置

只要不更改在主要区域中复制数据的方式，就可以使用 Azure 门户、PowerShell 或 Azure CLI 更改存储帐户的复制设置。 如果要从主要区域中的 LRS 迁移到主要区域中的 ZRS（或相反），则必须执行手动迁移或实时迁移。

更改存储帐户的复制方式不会导致应用程序停机。

# <a name="portal"></a>[Portal](#tab/portal)

若要在 Azure 门户中更改存储帐户的冗余选项，请执行以下步骤：

1. 导航到 Azure 门户中的存储帐户。
1. 在“设置”下，选择“配置”。
1. 更新 **复制** 设置。

    :::image type="content" source="media/redundancy-migration/change-replication-option.png" alt-text="屏幕截图，其中显示如何在门户中更改复制选项。" lightbox="media/redundancy-migration/change-replication-option.png":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要通过 PowerShell 更改存储帐户的冗余选项，请调用 [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) 命令并指定 `-SkuName` 参数：

```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage_account> `
    -SkuName <sku>
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要通过 Azure CLI 更改存储帐户的冗余选项，请调用 [az storage account update](/cli/azure/storage/account#az_storage_account_update) 命令并指定 `--sku` 参数：

```azurecli-interactive
az storage account update \
    --name <storage-account>
    --resource-group <resource_group> \
    --sku <sku>
```

---

## <a name="perform-a-manual-migration-to-zrs-gzrs-or-ra-gzrs"></a>执行到 ZRS、GZRS 或 RA-GZRS 的手动迁移

如果要通过从 LRS 移动到 ZRS（或相反）来更改存储帐户中的数据在主要区域中的复制方式，则可以选择执行手动迁移。 手动迁移比实时迁移更灵活。 你可以控制手动迁移的时间，因此如果需要在特定日期之前完成迁移，请使用此选项。

在主要区域执行从 LRS 到 ZRS 的手动迁移时（或相反），目标存储帐户可以是异地冗余的，也可以配置为对次要区域进行读取访问。 例如，可以通过一个步骤将 LRS 帐户迁移到 GZRS 或 RA-GZRS 帐户。

手动迁移可能导致应用程序关闭。 如果应用程序需要高可用性，则还可以使用 Microsoft 提供的实时迁移选项。 实时迁移属于就地迁移，不停机。

通过手动迁移，将数据从现有存储帐户复制到在主要区域使用 ZRS 的新存储帐户。 若要执行手动迁移，可以使用以下选项之一：

- 使用现有工具（如 AzCopy、Azure 存储客户端库之一或可靠的第三方工具）复制数据。
- 如果你熟悉 Hadoop 或 HDInsight，则可以将源存储帐户和目标存储帐户帐户附加到群集。 然后使用 DistCp 等工具来并行化数据复制过程。

## <a name="request-a-live-migration-to-zrs-gzrs-or-ra-gzrs"></a>请求实时迁移到 ZRS、GZRS 或 RA-GZRS

如果需要在不停机的情况下将存储帐户从 LRS 迁移到主要区域中的 ZRS，则可以从 Microsoft 请求实时迁移。 若要从 LRS 迁移到 GZRS 或 GZRS，请首先切换到 GRS 或 RA-GRS，然后请求实时迁移。 同样，你可以请求从 GRS 或 RA-GRS 到 GZRS 或 RA-GZRS 的实时迁移。 若要从 GRS 或 RA-GRS 迁移到 ZRS，请首先切换到 LRS，然后请求实时迁移。

在实时迁移期间，你可以访问存储帐户中的数据，而不会丢失持久性或可用性。 在迁移过程中将保留 Azure 存储 SLA。 实时迁移不会造成数据丢失。 迁移后，服务终结点、访问密钥、共享访问签名和其他帐户选项保持不变。

在标准性能层中，ZRS 仅支持常规用途 v2 帐户，因此如果它是常规用途 v1 帐户，请确保在提交实时迁移到 ZRS 的请求之前升级存储帐户。 有关详细信息，请参阅[升级到常规用途 v2 存储帐户](storage-account-upgrade.md)。 存储帐户必须包含要通过实时迁移进行迁移的数据。

在高级性能层中，高级文件共享帐户支持实时迁移，但高级块 blob 或高级页 blob 帐户不支持实时迁移。

如果你的帐户使用 RA-GRS，则需要先将帐户的复制类型更改为 LRS 或 GRS，然后再继续进行实时迁移。 此中间步骤会删除 RA-GRS 提供的辅助只读终结点。

尽管 Microsoft 会尽快处理实时迁移的请求，但无法保证实时迁移何时完成。 如果需要在特定的日期之前将数据迁移到 ZRS，则 Microsoft 建议执行手动迁移。 通常，帐户中的数据越多，迁移这些数据所需的时间就越长。

在以下情况下，必须执行手动迁移：

- 想要将数据迁移到位于与源帐户不同区域的 ZRS 存储帐户。
- 存储帐户是一个高级页 blob 或块 blob 帐户。
- 需要将数据从 ZRS 迁移到 LRS、GRS 或 RA-GRS。
- 存储帐户包含存档层中的数据。

可以通过 [Azure 支持门户](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)请求实时迁移。

> [!IMPORTANT]
> 如果需要迁移多个存储帐户，请创建一个支持票证，并在“详细信息”选项卡上指定要转换的帐户的名称。

按照以下步骤请求实时迁移：

1. 在 Azure 门户中，导航到要迁移的存储帐户。
1. 在“支持 + 故障排除”下，选择“新建支持请求”。
1. 根据帐户信息填写“基本信息”选项卡：
    - **问题类型**：选择“技术”。
    - “服务”：选择“我的服务”，然后选择“存储帐户管理”。
    - “资源”：选择要迁移的存储帐户。 如果需要指定多个存储帐户，可以在“详细信息”部分执行此操作。
    - “问题类型”：选择“数据迁移”。
    - “问题子类型”：选择“迁移到 ZRS、GZRS 或 RA-GZRS”。

    :::image type="content" source="media/redundancy-migration/request-live-migration-basics-portal.png" alt-text="屏幕截图，其中显示如何请求实时迁移 -“基本信息”选项卡":::

1. 选择“**下一页**”。 在“解决方案”选项卡上，可以查看存储帐户的迁移资格。
1. 选择“**下一页**”。 如果要迁移多个存储帐户，则在“详细信息”选项卡上指定每个帐户的名称，用分号分隔。

    :::image type="content" source="media/redundancy-migration/request-live-migration-details-portal.png" alt-text="屏幕截图，其中显示如何请求实时迁移 -“详细信息”选项卡":::

1. 在“详细信息”选项卡上填写额外的必需信息，然后选择“查看 + 创建”以查看并提交支持票证。 支持人员会与你联系，以提供你可能需要的所有帮助。

> [!NOTE]
> 高级文件共享仅适用于 LRS 和 ZRS。
>
> GZRS 存储帐户当前不支持存档层。 有关详细信息，请参阅 [Blob 数据的热访问层、冷访问层和存档访问层](../blobs/access-tiers-overview.md)。
>
> 托管磁盘仅适用于 LRS，不能迁移到 ZRS。 可将标准 SSD 托管磁盘的快照和映像存储在标准 HDD 存储上，并[在 LRS 与 ZRS 选项之间进行选择](https://azure.microsoft.com/pricing/details/managed-disks/)。 有关与可用性集集成的信息，请参阅 [Azure 托管磁盘简介](../../virtual-machines/managed-disks-overview.md#integration-with-availability-sets)。

## <a name="switch-from-zrs-classic"></a>从 ZRS 经典版切换

> [!IMPORTANT]
> Microsoft 将于 2021 年 3 月 31 日弃用并迁移 ZRS 经典版帐户。 在弃用之前会向 ZRS 经典版客户提供更多详细信息。
>
> ZRS 在某个区域正式发布后，客户不再可以在该区域中通过 Azure 门户创建 ZRS 经典版帐户。 在弃用 ZRS 经典版之前，可以使用 Microsoft PowerShell 和 Azure CLI 创建 ZRS 经典版帐户。 有关 ZRS 可用位置的信息，请参阅 [Azure 存储冗余](storage-redundancy.md)。

ZRS 经典版以异步方式在一到两个区域中的数据中心之间复制数据。 除非 Microsoft 发起了到次要区域的故障转移，否则复制的数据可能不可用。 ZRS 经典版帐户无法与 LRS、GRS 或 RA-GRS 相互转换。 ZRS 经典版帐户也不支持指标或日志记录。

ZRS 经典版仅适用于常规用途 V1 (GPv1) 存储帐户中的 **块 Blob**。 有关存储帐户的详细信息，请参阅 [Azure 存储帐户概述](storage-account-overview.md)。

若要向/从 LRS、GRS、RA-GRS 或 ZRS 经典版帐户手动迁移 ZRS 帐户数据，请使用以下工具之一：AzCopy、Azure 存储资源管理器、PowerShell 或 Azure CLI。 此外，可以使用某个 Azure 存储客户端库生成自己的迁移解决方案。

也可在 ZRS 可用的区域使用 Azure 门户、PowerShell 或 Azure CLI 将 ZRS 经典存储帐户升级到 ZRS。

# <a name="portal"></a>[门户](#tab/portal)

要在 Azure 门户中升级到 ZRS，请导航到帐户的“配置”部分，然后选择“升级” ：

![在门户中将 ZRS 经典版升级到 ZRS](media/redundancy-migration/portal-zrs-classic-upgrade.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

要使用 PowerShell 升级到 ZRS，请调用以下命令：

```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> -AccountName <storage_account> -UpgradeToStorageV2
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

要使用 Azure CLI 升级到 ZRS，请调用以下命令：

```cli
az storage account update -g <resource_group> -n <storage_account> --set kind=StorageV2
```

---

## <a name="costs-associated-with-changing-how-data-is-replicated"></a>与更改数据复制方式相关的成本

与更改数据复制方式相关的成本取决于你的转换路径。 从最便宜到最昂贵的 Azure 存储冗余产品/服务依次为 LRS、ZRS、GRS、RA-GRS 和 RA-GZRS。

例如，从 LRS 转移到任何其他类型的复制会产生额外的费用，因为这是转移到更高级的冗余级别。 如果迁移到 GRS 或 RA-GRS，则会在迁移时产生流出量带宽费用，因为要将整个存储帐户复制到次要区域。 之后写入主要区域的所有内容也会产生流出量带宽费用，用于将写入内容复制到次要区域。 有关带宽费用的详细信息，请参阅 [Azure 存储定价页面](https://azure.microsoft.com/pricing/details/storage/blobs/)。

如果将存储帐户从 GRS 迁移到 LRS，则不会产生额外的费用，但从次要位置复制的数据将被删除。

> [!IMPORTANT]
> 如果将存储帐户从 RA-GRS 迁移到 GRS 或 LRS，则会在完成转换 30 天后以 RA-GRS 方式对该帐户计费。

## <a name="see-also"></a>另请参阅

- [Azure 存储冗余](storage-redundancy.md)
- [检查存储帐户的“上次同步时间”属性](last-sync-time-get.md)
- [使用异地冗余设计高度可用的应用程序](geo-redundant-design.md)
