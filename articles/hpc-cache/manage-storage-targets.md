---
title: 管理 Azure HPC 缓存存储目标
description: 如何挂起、删除、强制删除和刷新 Azure HPC 缓存存储目标，以及如何了解存储目标状态
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/27/2021
ms.author: v-erkel
ms.openlocfilehash: 5b6127a43ebd93b89ea3a648533c2b739fc58691
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129274272"
---
# <a name="view-and-manage-storage-targets"></a>查看和管理存储目标

“存储目标设置”页显示 HPC 缓存的每个存储目标的相关信息，并提供用于管理单个存储目标的选项。

> [!TIP]
> [添加存储目标](hpc-cache-add-storage.md#view-storage-targets)一文中包含了使用 Azure CLI 列出存储目标的说明。 此处列出的其他操作在 Azure CLI 中可能尚未提供。

![Azure 门户中“设置 > 存储帐户”页的屏幕截图。 列表中有多个存储目标，列标题显示每个目标的名称、类型、状态、预配状态、地址/容器和使用情况模型。](media/storage-targets-list-states.png)

## <a name="manage-storage-targets"></a>管理存储目标

你可以对各个存储目标执行管理操作。 这些操作是对[管理缓存](hpc-cache-manage.md)中讨论的缓存级选项的补充。

这些控件可帮助你在发生意外状况（例如存储目标不响应）时进行恢复，并使你能够替代一些自动缓存操作（例如将更改的文件写回到长期存储系统）。

在 Azure 门户中打开“存储目标”页面。 单击存储目标列表最右侧的“…”图标以打开任务列表。

![屏幕截图显示了 Azure 门户中的“存储目标”页面，其中，鼠标指针悬停在单击列表中存储目标的行最右侧的三点 (...) 符号后显示的菜单上。](media/storage-target-manage-options.png)

以下选项可用：

* 刷新 - 将所有缓存的更改写回后端存储
* 暂停 - 使存储目标暂时停止处理请求
* 恢复 - 使暂停的存储目标恢复服务
* 强制删除 - 删除存储目标，跳过某些安全步骤（强制删除可能会导致数据丢失）
* 删除 - 永久删除存储目标

某些存储目标在此菜单上还有一个“刷新 DNS”选项，该选项从自定义 DNS 服务器更新存储目标 IP 地址。 此配置不常见。

有关这些选项的更多详细信息，请阅读本文的其余部分。

### <a name="write-cached-files-to-the-storage-target"></a>将缓存的文件写入存储目标

“刷新”选项指示缓存立即将缓存中存储的任何已更改的文件复制到后端存储系统。 例如，如果客户端计算机反复更新某个特定文件，则该文件会保留在缓存中，而不写入到长期存储系统，这样就可以在数分钟到超过 1 小时的时间段内实现更快速的访问。

“刷新”操作指示缓存将所有文件写入存储系统。

在刷新完成之前，缓存不会接受客户端针对此存储目标上的文件的请求。

在执行备份前，你可以使用此选项来确保填充后端存储。在任何情况下希望确保后端存储具有最新更新时，也可以使用此选项。

此选项主要适用于那些包括写入缓存的使用模型。 请阅读[了解缓存使用模型](cache-usage-models.md)，详细了解读写缓存。

### <a name="suspend-a-storage-target"></a>暂停存储目标

“暂停”功能可禁用对存储目标的客户端访问，但不会从缓存中永久删除存储目标。 如果需要禁用后端存储系统以进行维护、修复或更换，则可以使用此选项。

### <a name="put-a-suspended-storage-target-back-in-service"></a>使暂停的存储目标恢复服务

使用“恢复”来取消暂停存储目标。

### <a name="force-remove-a-storage-target"></a>强制删除存储目标

> [!NOTE]
> 此选项可能会导致受影响的存储目标发生数据丢失。

如果使用普通删除操作无法删除某个存储目标，则可以使用“强制删除”选项将其从 Azure HPC 缓存中删除。

此操作会跳过将缓存中的文件与后端存储系统中的文件进行同步的步骤。 不保证写入 HPC 缓存的任何更改都会写入后端存储系统，因此，如果使用此选项，更改可能会丢失。

在从缓存中删除后端存储系统后，也不保证后端存储系统可供访问。

通常，仅当存储目标变得无响应或处于错误状态时，才使用强制删除。 可以使用此选项删除损坏的存储目标，而不必执行更激进的操作。

### <a name="delete-a-storage-target"></a>删除存储目标

可以使用 Azure 门户或 AZ CLI 来删除存储目标。

常规删除选项会从 HPC 缓存中永久删除存储目标，但首先会将缓存内容与后端存储系统同步。 它不同于“强制删除”选项，后者不同步数据。

删除存储目标会删除存储系统与此 Azure HPC 缓存之间的关联，但不会更改后端存储系统。 例如，如果你使用了某个 Azure Blob 存储容器，从缓存中删除该容器后，该容器及其内容仍然存在。 可将容器添加到其他 Azure HPC 缓存、将其重新添加到此缓存，或使用 Azure 门户将其删除。

如果缓存中存储的已更改数据量过大，则删除存储目标的操作可能需要几分钟才能完成。 请等待操作完成，以确保将数据安全地存储在长期存储系统中。

#### <a name="portal"></a>[Portal](#tab/azure-portal)

若要删除存储目标，请打开“存储目标”页。 单击存储目标旁边的“...”并从菜单中选择“删除”。

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[设置适用于 Azure HPC 缓存的 Azure CLI](./az-cli-prerequisites.md)。

使用 [az hpc-cache storage-target remove](/cli/azure/hpc-cache/storage-target#az_hpc_cache_storage_target_remove) 从缓存中删除存储目标。

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

### <a name="update-ip-address-custom-dns-configurations-only"></a>更新 IP 地址（仅适用于自定义 DNS 配置）

如果缓存使用非默认的 DNS 配置，NFS 存储目标的 IP 地址可能会因后端 DNS 的更改而发生更改。 如果 DNS 服务器更改了后端存储系统的 IP 地址，Azure HPC 缓存可能会失去对存储系统的访问权限。

最好是与缓存自定义 DNS 系统的管理者协作来规划任何更新，因为这些更改会导致存储不可用。

如果需要更新存储目标的由 DNS 提供的 IP 地址，请使用“存储目标”页面。 单击右侧列中的“...”符号以打开上下文菜单。 选择“刷新 DNS”来查询自定义 DNS 服务器以获取新 IP 地址。

![存储目标列表的屏幕截图。 对于一个存储目标，最右侧列中的“…”菜单已打开并显示这些选项：“刷新”、“挂起”、“刷新 DNS”、“强制删除”、“恢复”（此选项已禁用）和“删除”。](media/refresh-dns.png)

如果成功，更新应会在两分钟内完成。 一次只能刷新一个存储目标；请先等待前一个操作完成，然后再尝试另一个操作。

## <a name="understand-storage-target-state"></a>了解存储目标状态

存储目标列表显示了两种类型的状态：状态和预配状态。

* 状态指示存储目标的操作状态。 此值会定期更新，有助于了解存储目标是否可用于客户端请求，以及哪些管理选项可用。
* 预配状态说明最后一次添加或编辑存储目标的操作是否成功。 仅当编辑存储目标时才会更新此值。

状态值会影响可以使用的管理选项。 以下是对此值及其影响的简短说明。

* 就绪 - 存储目标正在正常运行，可供客户端使用。 你可以使用此存储目标上的任何管理选项（除了“恢复”，该选项仅对挂起的存储目标有效）。
* 繁忙 - 存储目标正在处理另一操作。 你可以删除或强制删除存储目标。
* 挂起 - 存储目标已脱机。 你仍可以刷新、删除或强制删除此存储目标。 选择“恢复”以将目标恢复到服务中。
* 正在刷新 - 存储目标正在将数据写入后端存储。 正在刷新时，目标无法处理客户端请求，但它会在完成数据写入后自动返回到以前的状态。

## <a name="next-steps"></a>后续步骤

* 了解[缓存级管理操作](hpc-cache-manage.md)
* [编辑存储目标](hpc-cache-edit-storage.md)
