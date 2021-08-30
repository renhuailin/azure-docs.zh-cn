---
title: 取消预配 Azure 磁盘池（预览版）
description: 了解如何取消预配、停止和删除 Azure 磁盘池。
author: roygara
ms.date: 07/19/2021
ms.topic: conceptual
ms.author: rogarana
ms.service: storage
ms.subservice: disks
ms.openlocfilehash: 823dd066e1751896b3f7986a18d18d80d68219a1
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114469863"
---
# <a name="deprovision-an-azure-disk-pool-preview"></a>取消预配 Azure 磁盘池（预览版）

本文介绍如何删除 Azure 磁盘池（预览版）以及如何禁用 iSCSI 支持。

## <a name="stop-a-disk-pool"></a>停止磁盘池

可以停止磁盘池以节省成本并保留所有配置。 磁盘池停止后，便无法再通过 iSCSI 连接到它。 不会删除为支持磁盘池而部署的受管理资源。 在停止磁盘池之前，必须先断开所有使用 iSCSI 连接到磁盘池的客户端连接。 可以随时启动磁盘池。 这将重新激活在该磁盘池上公开的 iSCSI 目标。
# <a name="portal"></a>[Portal](#tab/azure-portal)

Azure 门户目前不支持此功能。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Stop-AzDiskPool -Name 'myDiskPool' -ResourceGroupName 'myResourceGroupt'
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az disk-pool stop --name "myDiskPool" --resource-group "myResourceGroup"
```
---

## <a name="delete-a-disk-pool"></a>删除磁盘池

删除磁盘池时，受管理资源组中的所有资源也将随之删除。 如果磁盘池存在未完成的 iSCSI 连接，则无法删除磁盘池。 必须先断开所有使用 iSCSI 连接到磁盘池的客户端连接。 已添加到磁盘池的磁盘不会被删除。

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. 登录 [Azure 门户](https://portal.azure.com/)。
1. 搜索“磁盘池”并选择它，然后选择要删除的磁盘池。
1. 选择窗格顶部的“删除”。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Remove-AzDiskPool -Name "myDiskpoolName" -ResourceGroupName "myRGName"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az disk-pool delete --name "myDiskPool" --resource-group "myResourceGroup"
```

---

## <a name="disable-iscsi-support"></a>禁用 iSCSI 支持

如果在磁盘池上禁用了 iSCSI 支持，则无法再连接到磁盘池。

当首次在磁盘池上启用 iSCSI 支持时，会创建一个 iSCSI 目标作为 iSCSI 连接的终结点。 可以通过删除 iSCSI 目标来禁用磁盘池上的 iSCSI 支持。 每个磁盘池仅配置一个 iSCSI 目标。

可以在现有磁盘池上重新启用 iSCSI 支持。 如果磁盘池存在未完成的 iSCSI 连接，则无法在磁盘池上禁用 iSCSI 支持。

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. 搜索“磁盘池”并选择你的磁盘池。
1. 在“设置”下选择“iSCSI” 。
1. 清除“启用 iSCSI”复选框并选择“保存” 。    

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Remove-AzDiskPoolIscsiTarget -DiskPoolName "myDiskpoolName" -Name "myiSCSITargetName" -ResourceGroupName "myRGName"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az disk-pool iscsi-target delete --disk-pool-name "myDiskPool" --name "myIscsiTarget" --resource-group "myResourceGroup"
```

---

## <a name="next-steps"></a>后续步骤

了解 [Azure 托管磁盘](managed-disks-overview.md)。