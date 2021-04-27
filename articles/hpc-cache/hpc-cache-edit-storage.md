---
title: 更新 Azure HPC 缓存存储目标
description: 如何编辑 Azure HPC 缓存存储目标
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/29/2021
ms.author: v-erkel
ms.openlocfilehash: d61612b6e491fae550559e499cb360efc0f7c044
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2021
ms.locfileid: "107258905"
---
# <a name="edit-storage-targets"></a>编辑存储目标

可以使用 Azure 门户或 Azure CLI 来删除或修改存储目标。

可以修改以下存储目标值，具体取决于存储类型：

* 对于 Blob 存储目标，可以更改命名空间路径和访问策略。

* 对于 NFS 存储目标，可以更改以下值：

  * 命名空间路径
  * 访问策略
  * 与命名空间路径关联的存储导出或导出子目录
  * 使用模型

* 对于 ADLS-NFS 存储目标，可以更改命名空间路径、访问策略和使用情况模型。

无法编辑存储目标的名称、类型或后端存储系统（Blob 容器或 NFS 主机名/IP 地址）。 如果需要更改这些属性，请删除存储目标，并使用新值创建替代项。

> [!TIP]
> [管理 Azure HPC 缓存视频](https://azure.microsoft.com/resources/videos/managing-hpc-cache/)演示了如何在 Azure 门户中编辑存储目标。

## <a name="remove-a-storage-target"></a>删除存储目标

### <a name="portal"></a>[Portal](#tab/azure-portal)

若要删除存储目标，请打开“存储目标”页。 从列表中选择存储目标，然后单击“删除”按钮。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[设置适用于 Azure HPC 缓存的 Azure CLI](./az-cli-prerequisites.md)。

使用 [az hpc-cache storage-target remove](/cli/azure/ext/hpc-cache/hpc-cache/storage-target#ext-hpc-cache-az-hpc-cache-storage-target-remove) 从缓存中删除存储目标。

```azurecli
$ az hpc-cache storage-target remove --resource-group cache-rg --cache-name doc-cache0629 --name blob1

{- Finished ..
  "endTime": "2020-07-09T21:45:06.1631571+00:00",
  "name": "2f95eac1-aded-4860-b19c-3f089531a7ec",
  "startTime": "2020-07-09T21:43:38.5461495+00:00",
  "status": "Succeeded"
}
```

---

删除存储目标会删除存储系统与此 Azure HPC 缓存系统之间的关联，但不会更改后端存储系统。 例如，如果你使用了某个 Azure Blob 存储容器，从缓存中删除该容器后，该容器及其内容仍然存在。 可将容器添加到其他 Azure HPC 缓存、将其重新添加到此缓存，或使用 Azure 门户将其删除。

在删除存储目标之前，存储在缓存中的所有文件更改将写入到后端存储系统。 如果缓存中存在大量已更改的数据，此过程可能需要一小时甚至更长时间。

## <a name="change-a-blob-storage-targets-namespace-path"></a>更改 Blob 存储目标的命名空间路径

命名空间路径是客户端用来装载此存储目标的路径。 （有关详细信息，请参阅[规划聚合命名空间](hpc-cache-namespace.md)和[设置聚合命名空间](add-namespace-paths.md)）。

在 Azure Blob 存储目标中只能对命名空间路径进行更新。 可以使用 Azure 门户或 Azure CLI 来更改命名空间路径。

### <a name="portal"></a>[Portal](#tab/azure-portal)

使用 Azure HPC 缓存的“命名空间”页。 [设置聚合命名空间](add-namespace-paths.md)一文中更详细地介绍了命名空间页。

单击要更改的路径的名称，然后在显示的编辑窗口中创建新路径。

![单击 Blob 命名空间路径后显示的命名空间页的屏幕截图 - 右侧的窗格中显示了编辑字段](media/update-namespace-blob.png)

进行更改后，单击“确定”以更新存储目标，或单击“取消”以放弃更改。 

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[设置适用于 Azure HPC 缓存的 Azure CLI](./az-cli-prerequisites.md)。

若要使用 Azure CLI 更改 Blob 存储目标的命名空间，请使用命令 [az hpc-cache blob-storage-target update](/cli/azure/ext/hpc-cache/hpc-cache/blob-storage-target#ext-hpc-cache-az-hpc-cache-blob-storage-target-update)。 只能更改 `--virtual-namespace-path` 值。

  ```azurecli
  az hpc-cache blob-storage-target update --cache-name cache-name --name target-name \
    --resource-group rg --virtual-namespace-path "/new-path"
  ```

---

## <a name="update-an-nfs-storage-target"></a>更新 NFS 存储目标

对于 NFS 存储目标，可以更改或添加虚拟命名空间路径、更改命名空间路径指向的 NFS 导出或子目录值，以及更改使用情况模型。

缓存中采用某些自定义 DNS 设置类型的存储目标还提供一个控件用于刷新其 IP 地址。 （这种配置非常罕见。）

详细信息如下：

* [更改聚合命名空间值](#change-aggregated-namespace-values)（虚拟命名空间路径、访问策略、导出以及导出子目录）
* [更改使用情况模型](#change-the-usage-model)
* [刷新 DNS](#update-ip-address-custom-dns-configurations-only)

### <a name="change-aggregated-namespace-values"></a>更改聚合命名空间值

可以使用 Azure 门户或 Azure CLI 更改面向客户端的命名空间路径、存储导出以及导出子目录（如果已使用）。

如需有关如何在一个存储目标中创建多个有效路径的提示，请参阅[添加 NFS 命名空间路径](add-namespace-paths.md#nfs-namespace-paths)中的指导原则。

### <a name="portal"></a>[Portal](#tab/azure-portal)

使用 Azure HPC 缓存的“命名空间”页更新命名空间值。 [设置聚合命名空间](add-namespace-paths.md)一文中更详细地介绍了此页。

![门户中命名空间页的屏幕截图，右侧打开了 NFS 更新页](media/update-namespace-nfs.png)

1. 单击要更改的路径的名称。
1. 使用编辑窗口键入新的虚拟路径、导出或子目录值，或者选择其他访问策略。
1. 进行更改后，单击“确定”以更新存储目标，或单击“取消”以放弃更改。 

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[设置适用于 Azure HPC 缓存的 Azure CLI](./az-cli-prerequisites.md)。

在 [az hpc-cache nfs-storage-target update](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target) 命令中使用 ``--junction`` 选项更改命名空间路径、NFS 导出或导出子目录。

``--junction`` 参数使用以下值：

* ``namespace-path`` - 面向客户端的虚拟文件路径
* ``nfs-export`` - 与面向客户端的路径关联的存储系统导出
* ``target-path``（可选）- 导出的子目录（如果需要）

示例： ``--junction namespace-path="/nas-1" nfs-export="/datadisk1" target-path="/test"``

必须为 ``--junction`` 语句中的每个路径提供所有三个值。 对于你不想要更改的任何值，请使用现有值。

在所有 update 命令中还需要指定缓存名称、存储目标名称和资源组。

示例命令：

```azurecli
az hpc-cache nfs-storage-target update --cache-name mycache \
  --name st-name --resource-group doc-rg0619 \
  --junction namespace-path="/new-path" nfs-export="/my-export" target-path="my-subdirectory"
```

---

### <a name="change-the-usage-model"></a>更改使用情况模型

使用情况模型影响缓存保留数据的方式。 有关详细信息，请参阅[了解缓存使用情况模型](cache-usage-models.md)。

> [!NOTE]
> 如果更改使用情况模型，则可能需要重新装载客户端，以避免出现 NLM 错误。 阅读[了解何时重新装载客户端](cache-usage-models.md#know-when-to-remount-clients-for-nlm)以获取详细信息。

若要更改 NFS 存储目标的使用情况模型，请使用以下方法之一。

### <a name="portal"></a>[Portal](#tab/azure-portal)

通过 Azure 门户中的“存储目标”页更改使用情况模型。 单击要更改的存储目标的名称。

![NFS 存储目标编辑页的屏幕截图](media/edit-storage-nfs.png)

使用下拉选择器选择新的使用情况模型。 单击“确定”以更新存储目标，或单击“取消”以放弃更改。 

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[设置适用于 Azure HPC 缓存的 Azure CLI](./az-cli-prerequisites.md)。

使用 [az hpc-cache nfs-storage-target update](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target#ext-hpc-cache-az-hpc-cache-nfs-storage-target-update) 命令。

update 命令与用于添加 NFS 存储目标的命令几乎完全相同。 有关详细信息和示例，请参阅[创建 NFS 存储目标](hpc-cache-add-storage.md#create-an-nfs-storage-target)。

若要更改使用情况模型，请更新 ``--nfs3-usage-model`` 选项。 示例： ``--nfs3-usage-model WRITE_WORKLOAD_15``

还需要指定缓存名称、存储目标名称和资源组值。

若要验证使用情况模型的名称，请使用命令 [az hpc-cache usage-model list](/cli/azure/ext/hpc-cache/hpc-cache/usage-model#ext-hpc-cache-az-hpc-cache-usage-model-list)。

如果缓存已停止或处于不正常状态，将在缓存正常后应用更新。

---

### <a name="update-ip-address-custom-dns-configurations-only"></a>更新 IP 地址（仅适用于自定义 DNS 配置）

如果缓存使用非默认的 DNS 配置，NFS 存储目标的 IP 地址可能会因后端 DNS 的更改而发生更改。 如果 DNS 服务器更改了后端存储系统的 IP 地址，Azure HPC 缓存可能会失去对存储系统的访问权限。

最好是与缓存自定义 DNS 系统的管理者协作来规划任何更新，因为这些更改会导致存储不可用。

如果需要更新存储目标的 DNS 提供的 IP 地址，可以使用“存储目标”列表中的相应按钮。 单击“刷新 DNS”查询自定义 DNS 服务器以获取新 IP 地址。

![存储目标列表的屏幕截图。 处理一个存储目标时，最右侧列中的“...”菜单将会打开并显示两个选项：“删除”和“刷新 DNS”。](media/refresh-dns.png)

如果成功，更新应会在两分钟内完成。 一次只能刷新一个存储目标；请先等待前一个操作完成，然后再尝试另一个操作。

## <a name="update-an-adls-nfs-storage-target-preview"></a>更新 ADLS-NFS 存储目标（预览版）

类似于 NFS 存储目标，可以更改 ADLS-NFS 存储目标的命名空间路径和使用情况模型。

### <a name="change-an-adls-nfs-namespace-path"></a>更改 ADLS-NFS 命名空间路径

使用 Azure HPC 缓存的“命名空间”页更新命名空间值。 [设置聚合命名空间](add-namespace-paths.md)一文中更详细地介绍了此页。

![门户中命名空间页的屏幕截图，右侧打开了 ADS-NFS 更新页](media/update-namespace-adls.png)

1. 单击要更改的路径的名称。
1. 使用编辑窗口键入新的虚拟路径，或更新访问策略。
1. 进行更改后，单击“确定”以更新存储目标，或单击“取消”以放弃更改。 

### <a name="change-adls-nfs-usage-models"></a>更改 ADLS-NFS 使用情况模型

ADLS-NFS 使用情况模型的配置与 NFS 使用情况模型选择相同。 请参阅上述 NFS 部分的[更改使用情况模型](#change-the-usage-model)中的门户操作说明。 用于更新 ADLS-NFS 存储目标的其他工具正在开发中。


## <a name="next-steps"></a>后续步骤

* 若要详细了解这些选项，请参阅[添加存储目标](hpc-cache-add-storage.md)。
* 有关使用虚拟路径的更多提示，请参阅[规划聚合命名空间](hpc-cache-namespace.md)。
