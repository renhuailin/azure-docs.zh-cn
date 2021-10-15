---
title: 管理并更新 Azure HPC 缓存
description: 如何使用 Azure 门户或 Azure CLI 管理并更新 Azure HPC 缓存
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/08/2021
ms.author: v-erkel
ms.openlocfilehash: 4fb5bcc0ae5be35f740bc39bc0cf4a9221363358
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129274361"
---
# <a name="manage-your-cache"></a>管理缓存

Azure 门户中的缓存概述页面显示了缓存的项目详细信息、缓存状态和基本统计信息。 其中还有一些用于停止或启动缓存、删除缓存、将数据刷新为长期存储和更新软件的控件。

本文还介绍如何通过 Azure CLI 执行这些基本任务。

若要打开概述页面，请在 Azure 门户中选择缓存资源。 例如，加载“所有资源”页，然后单击缓存名称。

![Azure HPC 缓存实例的“概述”页面的屏幕截图](media/hpc-cache-overview.png)

页面顶部的按钮有助于管理缓存：

* **启动** 和 [**停止**](#stop-the-cache) - 继续或暂停缓存操作
* [**刷新**](#flush-cached-data) - 将更改的数据写入存储目标
* [**升级**](#upgrade-cache-software) - 更新缓存软件
* [**收集诊断**](#collect-diagnostics) - 上传调试信息
* **刷新** - 重新加载概述页面
* [**删除**](#delete-the-cache) - 永久性销毁缓存

在下面阅读有关这些选项的详细信息。

> [!TIP]
> 还可以管理单个存储目标 - 有关详细信息，请阅读[查看和管理存储目标](manage-storage-targets.md)。

单击下面的图像，观看演示缓存管理任务的[视频](https://azure.microsoft.com/resources/videos/managing-hpc-cache/)。

[![视频缩略图：Azure HPC 缓存：管理（单击以访问视频页面）](media/video-5-manage.png)](https://azure.microsoft.com/resources/videos/managing-hpc-cache/)

## <a name="stop-the-cache"></a>停止缓存

可以停止缓存以减少非活动期间的成本。 缓存停止时，你无需支付运行时间的费用，但会为缓存的已分配磁盘存储支付费用。 （有关详细信息，请参阅[定价](https://aka.ms/hpc-cache-pricing)页。）

停止的缓存不会响应客户端请求。 应在停止缓存之前卸载客户端。

### <a name="portal"></a>[Portal](#tab/azure-portal)

“停止”按钮可暂停活动缓存。 当缓存的状态为“正常”或“已降级”时，“停止”按钮可用  。

![该屏幕截图显示了顶部的按钮，其中突出显示了“停止”，一条弹出消息描述了停止操作并询问“是否要继续?” 并带有“是”（默认）和“否”按钮](media/stop-cache.png)

单击“是”以确认停止缓存后，缓存会自动将其内容刷新到存储目标。 此过程可能需要一些时间，但可以确保数据一致性。 最后，缓存状态将更改为“已停止”。

若要重新激活停止的缓存，请单击“开始”按钮。 无需进行确认。

![该屏幕截图显示了顶部的按钮，其中突出显示了“开始”](media/start-cache.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[设置适用于 Azure HPC 缓存的 Azure CLI](./az-cli-prerequisites.md)。

使用 [az hpc-cache stop](/cli/azure/hpc-cache#az_hpc_cache_stop) 命令暂停缓存。 仅当缓存的状态为“正常”或“已降级”时，此操作才有效 。

缓存会在停止前自动将其内容刷新到存储目标。 此过程可能需要一些时间，但可以确保数据一致性。

操作完成后，状态更改为“已停止”。

使用 [az hpc-cache start](/cli/azure/hpc-cache#az_hpc_cache_start) 重新激活已停止的缓存。

发出 start 或 stop 命令时，命令行会显示状态消息“正在运行”，直到操作完成。

```azurecli
$ az hpc-cache start --name doc-cache0629
 - Running ..
```

完成后，消息将更新为“已完成”并显示返回代码和其他信息。

```azurecli
$ az hpc-cache start --name doc-cache0629
{- Finished ..
  "endTime": "2020-07-01T18:46:43.6862478+00:00",
  "name": "c48d320f-f5f5-40ab-8b25-0ac065984f62",
  "properties": {
    "output": "success"
  },
  "startTime": "2020-07-01T18:40:28.5468983+00:00",
  "status": "Succeeded"
}
```

---

## <a name="flush-cached-data"></a>刷新缓存数据

概述页面上的“刷新”按钮会通知缓存将存储在缓存中的所有已更改数据立即写入后端存储目标。 缓存会定期将数据保存到存储目标，因此，无需手动执行此操作，除非要确保后端存储系统保持最新状态。 例如，可以在拍摄存储快照或检查数据集的大小之前使用“刷新”功能。

> [!NOTE]
> 在刷新过程中，缓存无法处理客户端请求。 缓存访问会暂停，并在操作完成后继续。

开始缓存刷新操作时，缓存会停止接受客户端请求，概述页面上的缓存状态将更改为“正在刷新”。

缓存中的数据会保存到相应的存储目标。 根据需要刷新的数据量，此过程可能需要数分钟或超过一小时的时间。

将所有数据保存到存储目标后，该缓存将自动开始再次接受客户端请求。 缓存状态返回为“正常”。

### <a name="portal"></a>[Portal](#tab/azure-portal)

若要刷新缓存，请单击“刷新”按钮，然后单击“是”，确认操作 。

![该屏幕截图显示了顶部的按钮，其中突出显示了“刷新”，一条弹出消息描述了刷新操作并询问“是否要继续?” 并带有“是”（默认）和“否”按钮](media/hpc-cache-flush.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[设置适用于 Azure HPC 缓存的 Azure CLI](./az-cli-prerequisites.md)。

使用 [az hpc-cache flush](/cli/azure/hpc-cache#az_hpc_cache_flush) 强制缓存将所有已更改数据写入存储目标。

示例：

```azurecli
$ az hpc-cache flush --name doc-cache0629 --resource-group doc-rg
 - Running ..
```

刷新完成后，将返回成功消息。

```azurecli
{- Finished ..
  "endTime": "2020-07-09T17:26:13.9371983+00:00",
  "name": "c22f8e12-fcf0-49e5-b897-6a6e579b6489",
  "properties": {
    "output": "success"
  },
  "startTime": "2020-07-09T17:25:21.4278297+00:00",
  "status": "Succeeded"
}
$
```

---

## <a name="upgrade-cache-software"></a>升级缓存软件

如果有新的软件版本可用，“升级”按钮就会处于有效状态。 你还会在页面顶部看到一条关于更新软件的消息。

![该屏幕截图显示了顶行的按钮，其中已启用“升级”按钮](media/hpc-cache-upgrade-button.png)

在软件升级过程中，客户端访问不会中断，但缓存性能会降低。 计划在非高峰使用时间段或计划内维护期间升级软件。

软件更新可能需要几个小时。 与具有较小高峰吞吐量值的缓存相比，使用较高吞吐量配置的缓存需要更长的时间来升级。

当软件升级可用时，你将有一周的时间来手动应用。 升级消息中会列出结束日期。 如果在这段时间内未升级，Azure 会自动将更新应用于缓存。 自动升级的时间不能配置。 如果你担心缓存性能影响，则应在此时间段到期前自行升级软件。

如果在结束日期过去时缓存停止，缓存将在下一次启动时自动升级软件。 （更新可能不会立即启动，但会在前一小时内开始。）

### <a name="portal"></a>[Portal](#tab/azure-portal)

单击“升级”按钮，开始软件更新。 缓存状态会更改为“正在升级”，直到操作完成为止。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[设置适用于 Azure HPC 缓存的 Azure CLI](./az-cli-prerequisites.md)。

在 Azure CLI 上，缓存状态报告的末尾包含新的软件信息。 （使用 [az hpc-cache show](/cli/azure/hpc-cache#az_hpc_cache_show) 进行检查。）在消息中查找字符串“upgradeStatus”。

如果有更新，请使用 [az hpc-cache upgrade-firmware](/cli/azure/hpc-cache#az_hpc_cache_upgrade-firmware) 来应用更新。

如果没有可用的更新，此操作便不起作用。

此示例显示缓存状态（无可用更新）以及 upgrade-firmware 命令的结果。

```azurecli
$ az hpc-cache show --name doc-cache0629
{
  "cacheSizeGb": 3072,
  "health": {
    "state": "Healthy",
    "statusDescription": "The cache is in Running state"
  },

<...>

  "tags": null,
  "type": "Microsoft.StorageCache/caches",
  "upgradeStatus": {
    "currentFirmwareVersion": "5.3.61",
    "firmwareUpdateDeadline": "0001-01-01T00:00:00+00:00",
    "firmwareUpdateStatus": "unavailable",
    "lastFirmwareUpdate": "2020-06-29T22:18:32.004822+00:00",
    "pendingFirmwareVersion": null
  }
}
$ az hpc-cache upgrade-firmware --name doc-cache0629
$
```

---

## <a name="collect-diagnostics"></a>收集诊断

“收集诊断”按钮会手动启动进程，收集系统信息并将其上传到 Microsoft 服务与支持进行疑难解答。 如果出现严重缓存问题，则缓存会自动收集和上传相同的诊断信息。

如果 Microsoft 服务服务与支持请求，请使用此控制。

单击该按钮后，单击“是”以确认上传。

![“启动诊断集合”弹出确认消息的屏幕截图。 默认按钮“是”已突出显示。](media/diagnostics-confirm.png)

## <a name="delete-the-cache"></a>删除缓存

“删除”按钮会销毁缓存。 删除缓存时，其中的所有资源都将销毁，不再产生帐户费用。

删除缓存时，用作存储目标的后端存储卷不会受到影响。 可在以后将其添加到将来的缓存中，或单独解除其授权。

> [!NOTE]
> 在删除缓存前，Azure HPC 缓存不会自动将已更改的数据从缓存写入后端存储系统。
>
> 若要确保缓存中的所有数据都已写入长期存储，请在删除之前[停止缓存](#stop-the-cache)。 请确保删除前状态为“已停止”。

### <a name="portal"></a>[Portal](#tab/azure-portal)

停止缓存后，单击“删除”按钮，永久删除缓存。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[设置适用于 Azure HPC 缓存的 Azure CLI](./az-cli-prerequisites.md)。

使用 Azure CLI 命令 [az hpc-cache delete](/cli/azure/hpc-cache#az_hpc_cache_delete) 永久删除缓存。

示例：
```azurecli
$ az hpc-cache delete --name doc-cache0629
 - Running ..

<...>

{- Finished ..
  "endTime": "2020-07-09T22:24:35.1605019+00:00",
  "name": "7d3cd0ba-11b3-4180-8298-d9cafc9f22c1",
  "startTime": "2020-07-09T22:13:32.0732892+00:00",
  "status": "Succeeded"
}
$
```

---

## <a name="view-warnings"></a>查看警告

如果缓存进入运行不正常状态，请检查“警告”页。 此页显示来自缓存软件的通知，这些通知有助于了解其状态。

这些通知未显示在活动日志中，因为它们不受 Azure 门户控制。 它们通常与你可能已进行的自定义设置相关联。

可能显示的警告类型包括：

* 缓存无法访问其 NTP 服务器
* 缓存无法下载扩展组用户名信息
* 自定义 DNS 设置已在存储目标上更改

![该屏幕截图显示了“监视”>“警告”页，其中显示了一条消息，指出无法下载扩展组用户名](media/warnings-page.png)

## <a name="next-steps"></a>后续步骤

* [使用统计信息监视缓存](metrics.md)
* 获取[有关 AZURE HPC 缓存方面的帮助](hpc-cache-support-ticket.md)
