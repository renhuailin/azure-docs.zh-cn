---
title: 启动存储帐户故障转移
titleSuffix: Azure Storage
description: 了解如何在存储帐户的主终结点不可用时启动帐户故障转移。 故障转移将次要区域更新为，存储帐户的主要区域。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 05/07/2021
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ab764fd95168fef768efd0687adb5e2841bfb140
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128609282"
---
# <a name="initiate-a-storage-account-failover"></a>启动存储帐户故障转移

如果异地冗余存储帐户的主终结点因任何原因而不可用，你可以启动帐户故障转移。 帐户故障转移将辅助终结点更新为，存储帐户的主终结点。 在故障转移完成后，客户端便可以开始对新的主要区域执行写入操作。 借助强制故障转移，可以维持应用程序的高可用性。

本文介绍了如何使用 Azure 门户、PowerShell 或 Azure CLI 为存储帐户启动帐户故障转移。 若要详细了解帐户故障转移，请参阅[灾难恢复和存储帐户故障转移](storage-disaster-recovery-guidance.md)。

> [!WARNING]
> 帐户故障转移通常会导致一些数据丢失。 若要了解帐户故障转移的影响，以及如何应对数据丢失问题，请查看[了解帐户故障转移过程](storage-disaster-recovery-guidance.md#understand-the-account-failover-process)。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>先决条件

请先确保为异地复制配置了存储帐户，然后才能对存储帐户执行帐户故障转移。 你的存储帐户可以使用以下任一冗余选项：

- 异地冗余存储 (GRS) 或读取访问异地冗余存储 (RA-GRS)
- 异地区域冗余存储 (GZRS) 或读取访问异地区域冗余存储 (RA-GZRS)

有关 Azure 存储冗余的详细信息，请参阅 [Azure 存储冗余](storage-redundancy.md)。

请记住，帐户故障转移不支持以下功能和服务：

- Azure 文件同步不支持存储帐户故障转移。 不得对包含 Azure 文件共享且用作 Azure 文件同步中云终结点的存储帐户执行故障转移。 否则，将会导致同步停止，并且可能还会在有新分层文件的情况下导致意外数据丢失。
- 目前不支持启用了分层命名空间的存储帐户（例如用于 Data Lake Storage Gen2 的）。
- 无法对包含高级块 blob 的存储帐户执行故障转移。 支持高级块 blob 的存储帐户暂不支持异地冗余。
- 无法对包含任何已启用 [WORM 不可变性策略](../blobs/immutable-storage-overview.md)的容器执行故障转移。 已解锁/锁定的基于时间的保留或法定保留策略会阻止故障转移，以便保持合规性。

## <a name="initiate-the-failover"></a>启动故障转移

## <a name="portal"></a>[Portal](#tab/azure-portal)

若要通过 Azure 门户启动帐户故障转移，请按照以下步骤操作：

1. 导航到自己的存储帐户。
1. 选择“设置”下的“异地复制”。 下图展示了存储帐户的异地复制和故障转移状态。

    :::image type="content" source="media/storage-initiate-account-failover/portal-failover-prepare.png" alt-text="显示异地复制和故障转移状态的屏幕截图":::

1. 验证存储帐户是否已配置为，使用异地冗余存储 (GRS) 或读取访问权限异地冗余存储 (RA-GRS)。 如果没有，请选择“设置”下的“配置”，将帐户更新为异地冗余。
1. “上次同步时间”属性指明次要区域落后主要区域的时间。 使用“上次同步时间”，可估计在故障转移完成后的数据丢失程度。 如需详细了解如何检查“上次同步时间”属性，请参阅[检查存储帐户的“上次同步时间”属性](last-sync-time-get.md)。
1. 选择“准备进行故障转移”。
1. 查看确认对话框。 准备就绪后，输入“是”，以确认并启动故障转移。

    :::image type="content" source="media/storage-initiate-account-failover/portal-failover-confirm.png" alt-text="显示帐户故障转移确认对话框的屏幕截图":::

## <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

帐户故障转移功能已正式发布，但仍依赖于 PowerShell 的预览模块。 若要使用 PowerShell 启动帐户故障转移，必须先安装 Az.Storage [1.1.1-preview](https://www.powershellgallery.com/packages/Az.Storage/1.1.1-preview) 模块。 若要安装此模块，请按照以下步骤操作：

1. 卸载以前安装的所有 Azure PowerShell：

    - 使用“设置”下的“应用和功能”设置从 Windows 中删除以前安装的所有 Azure PowerShell。
    - 从 `%Program Files%\WindowsPowerShell\Modules` 中删除所有 Azure 模块。

1. 确保已安装 PowerShellGet 最新版本。 打开 Windows PowerShell 窗口，然后运行以下命令以安装最新版本：

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. 安装 PowerShellGet 后关闭并重新打开 PowerShell 窗口。

1. 安装最新版本的 Azure PowerShell：

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. 安装支持帐户故障转移的 Azure 存储预览模块：

    ```powershell
    Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.1.1-preview –AllowPrerelease –AllowClobber –Force
    ```

若要使用 PowerShell 启动帐户故障转移，请执行以下命令：

```powershell
Invoke-AzStorageAccountFailover -ResourceGroupName <resource-group-name> -Name <account-name>
```

## <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 启动帐户故障转移，请执行以下命令：

```azurecli-interactive
az storage account show \ --name accountName \ --expand geoReplicationStats
az storage account failover \ --name accountName
```

---

## <a name="important-implications-of-account-failover"></a>帐户故障转移的重要影响

在你为存储帐户启动帐户故障转移后，辅助终结点的 DNS 记录更新为，辅助终结点成为主终结点。 启动故障转移前，请务必先了解它对存储帐户的潜在影响。

若要在启动故障转移前估计可能造成的数据丢失程度，请检查“上次同步时间”属性。 如需详细了解如何检查“上次同步时间”属性，请参阅[检查存储帐户的“上次同步时间”属性](last-sync-time-get.md)。

启动后故障转移所花费的时间通常不到一小时。

在故障转移完成后，存储帐户类型自动转换为新的主要区域中的本地冗余存储 (LRS)。 可以为帐户重新启用异地冗余存储 (GRS) 或读取访问权限异地冗余存储 (RA-GRS)。 请注意，从 LRS 转换为 GRS 或 RA-GRS 会产生额外费用。 产生该费用是因为将数据重新复制到新的次要区域需要支付网络流出量费用。 有关其他信息，请参阅[带宽定价详细信息](https://azure.microsoft.com/pricing/details/bandwidth/)。

在你为存储帐户重新启用 GRS 后，Microsoft 便会开始将帐户中的数据复制到新的次要区域。 复制时间取决于许多因素，其中包括：

- 存储帐户中对象的数量和大小。 许多小型对象可能比较少但较大的对象花费的时间更长。
- 可用于后台复制的资源，例如 CPU、内存、磁盘和 WAN 容量。 实时流量优先于异地复制。
- 每个 Blob 的快照数（如果使用 Blob 存储）。
- [数据分区策略](/rest/api/storageservices/designing-a-scalable-partitioning-strategy-for-azure-table-storage)（如果使用表存储）。 复制过程不能扩展到超过所用分区键数量的地步。

## <a name="next-steps"></a>后续步骤

- [灾难恢复和存储帐户故障转移](storage-disaster-recovery-guidance.md)
- [检查存储帐户的“上次同步时间”属性](last-sync-time-get.md)
- [使用异地冗余设计高度可用的应用程序](geo-redundant-design.md)
- [教程：生成使用 Blob 存储的高可用性应用程序](../blobs/storage-create-geo-redundant-storage.md)
