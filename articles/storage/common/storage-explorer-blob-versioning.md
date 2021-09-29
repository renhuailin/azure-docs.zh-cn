---
title: Azure 存储资源管理器 Blob 版本控制指南 | Microsoft Docs
description: Azure 存储资源管理器的 Blob 版本控制指南
services: storage
author: JasonYeMSFT
ms.service: storage
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: chuye
ms.openlocfilehash: dfff19eee4c852e336d0d943a9509d286d510347
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128626677"
---
# <a name="azure-storage-explorer-blob-versioning-guide"></a>Azure 存储资源管理器 Blob 版本控制指南

Microsoft Azure 存储资源管理器可轻松访问和管理 Blob 版本。 本指南将帮助你了解存储资源管理器中 Blob 版本控制的工作原理。 在继续之前，建议详细了解 [Blob 版本控制](../blobs/versioning-overview.md)。

## <a name="terminology"></a>术语

本部分提供一些定义，帮助你了解它们在本文中的用法。

- 软删除：备用自动数据保护功能。 可以在[此处](../blobs/soft-delete-blob-overview.md)了解有关软删除的详细信息。
- 活动 Blob：创建的 Blob 或 Blob 版本处于活动状态。 只能对处于活动状态的 Blob 或 Blob 版本执行操作。
- 软删除 Blob：标记为软删除的 Blob 或 Blob 版本。 软删除 Blob 只在其保留期内保留。
- Blob 版本：在启用 Blob 版本控制的情况下创建的 Blob。 每个 Blob 版本都与一个版本 ID 相关联。
- 当前版本：标记为当前版本的 Blob 版本。
- 早期版本：不是当前版本的 Blob 版本。
- 非版本 Blob：在禁用 Blob 版本控制的情况下创建的 Blob。 非版本 Blob 没有版本 ID。

## <a name="view-blob-versions"></a>查看 Blob 版本

存储资源管理器支持四种用于查看 Blob 的不同视图。

| 查看 | 活动非版本 Blob | 软删除的非版本 Blob | Blob 版本 |
| ---- | :----------: | :-----------: | :------------------: |
| 活动 Blob | 是 | 否 | 仅当前版本 |
| 活动 Blob 和软删除 Blob | 是 | 是 | 仅当前版本 |
| 活动 Blob 和没有当前版本的 Blob | 是 | 否 | 当前版本或最新活动版本 |
| 所有 Blob 和没有当前版本的 Blob | 是 | 是 | 当前版本或最新版本 |

### <a name="active-blobs"></a>活动 Blob

在此视图中，存储资源管理器显示：

- 活动非版本 Blob
- 当前版本

### <a name="active-blobs-and-soft-deleted-blobs"></a>活动 Blob 和软删除 Blob

在此视图中，存储资源管理器显示：

- 活动非版本 Blob
- 软删除的非版本 Blob
- 当前版本。

### <a name="active-blobs-and-blobs-without-current-version"></a>活动 Blob 和没有当前版本的 Blob

在此视图中，存储资源管理器显示：

- 活动非版本 Blob
- 当前版本
- 最新的活动早期版本。

对于没有当前版本但具有活动的早期版本的 Blob，存储资源管理器会将其最新的活动早期版本作为该 Blob 的表示形式。

### <a name="all-blobs-and-blobs-without-current-version"></a>所有 Blob 和没有当前版本的 Blob

在此视图中，存储资源管理器显示：

- 活动非版本 Blob
- 软删除的非版本 Blob
- 当前版本
- 最新的早期版本。

对于没有当前版本的 Blob，存储资源管理器会将其最新的早期版本作为该 Blob 的表示形式。

> [!NOTE]
> 由于服务限制，在列出 Blob 版本时，存储资源管理器需要进行一些额外的处理才能获取虚拟目录的分层视图。 列出以下视图中的 Blob 需要更长的时间：
>
> - 活动 Blob 和没有当前版本的 Blob
> - 所有 Blob 和没有当前版本的 Blob

## <a name="manage-blob-versions"></a>管理 Blob 版本

### <a name="view-versions-of-a-blob"></a>查看 Blob 的版本

存储资源管理器提供 Manage Versions 命令来查看一个 Blob 的所有版本。 若要查看 Blob 的版本，请选择要查看其版本的 Blob，然后从工具栏或上下文菜单中选择“管理历史记录”&rarr;“管理版本”。

### <a name="download-blob-versions"></a>下载 Blob 版本

若要下载一个或多个 Blob 版本，请选择要下载的 Blob 版本，并从工具栏或上下文菜单中选择“下载”。

如果要下载一个 Blob 的多个版本，则下载的文件的文件名开头将包含其版本 ID。

### <a name="delete-blob-versions"></a>删除 Blob 版本

若要删除一个或多个 Blob 版本，请选择要删除的 Blob 版本，并从工具栏或上下文菜单中选择“删除”。

Blob 版本受软删除策略的约束。 如果启用了软删除，则会软删除 Blob 版本。 一种特殊情况是删除当前版本。 删除当前版本会自动使其成为活动的早期版本。

### <a name="promote-blob-version"></a>升级 Blob 版本

通过将早期版本升级为当前版本，可以还原 Blob 的内容。 选择要升级的 Blob 版本，并从工具栏或上下文菜单中选择“升级版本”。

升级的 Blob 版本将覆盖非版本 Blob。 确认操作之前，请确保不再需要该数据或自行备份该数据。 当前版本会自动成为早期版本，因此存储资源管理器不会提示进行确认。

### <a name="undelete-blob-version"></a>删除 Blob 版本

Blob 版本不能单独删除。 必须同时删除它们。 若要取消删除一个 Blob 的所有 Blob 版本，请选择其中任一 Blob 版本，并从工具栏或上下文菜单中选择“取消删除”。

### <a name="change-access-tier-of-blob-versions"></a>更改 Blob 版本的访问层

每个 Blob 版本都有其自己的访问层。 若要更改 Blob 版本的访问层，请选择要更改其访问层的 Blob 版本，然后从上下文菜单中选择“更改访问层…”。

## <a name="see-also"></a>另请参阅

- [Blob 版本控制](../blobs/versioning-overview.md)
- [blob 的软删除](../blobs/soft-delete-blob-overview.md)
- [Azure 存储资源管理器软删除指南](./storage-explorer-soft-delete.md)
