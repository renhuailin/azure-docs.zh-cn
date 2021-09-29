---
title: Azure 存储资源管理器软删除指南 | Microsoft Docs
description: Azure 存储资源管理器中的软删除
services: storage
author: JasonYeMSFT
ms.service: storage
ms.topic: conceptual
ms.date: 08/30/2021
ms.author: chuye
ms.openlocfilehash: 0d097e769bfea8ff9c65921e8f0d851372187a02
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128564897"
---
# <a name="azure-storage-explorer-soft-delete-guide"></a>Azure 存储资源管理器软删除指南

软删除有助于减轻意外删除关键数据的影响。 本指南将帮助你了解如何在存储资源管理器中利用此功能。 在继续之前，建议你阅读有关 [blob 软删除](../blobs/soft-delete-blob-overview.md)的详细信息。

## <a name="configuring-delete-retention-policy"></a>配置删除保留策略

你可以在存储资源管理器中为每个存储帐户配置删除保留策略。 在存储帐户下打开任何“blob 容器”节点的关联菜单，然后选择“配置软删除策略...”。

设置新策略可能需要 30 秒才能生效。 如果在不等待新策略生效的情况下删除数据，可能会导致意外的行为。 在活动日志中报告已成功配置的策略之前，存储资源管理器会等待 30 秒。

## <a name="soft-delete-with-hierarchical-namespace-enabled"></a>启用了分层命名空间的软删除

软删除功能在启用或未启用分层命名空间 (HNS) 的 blob 容器之间具有根本差异。

启用了 HNS 的 blob 容器具有真实目录。 这些目录也可以被软删除。 软删除真实目录时，该目录下的所有活动 blob 或目录都将变得不可访问。 取消删除该目录时，将恢复这些 blob 和目录，并在该目录过期时将其丢弃。 已经被软删除的 blob 或它下面的目录将保持原样。

启用了非 HNS 的 blob 容器不允许已软删除的 blob 和具有相同名称的活动 blob 共存。 上传与已软删除的 blob 同名的 blob 将导致已软删除的 blob 成为新 blob 的快照。 在启用了 HNS 的 blob 容器中，执行相同的操作会导致已软删除的 blob 与新的 blob 共存。 启用了 HNS 的 blob 容器还允许同名的多个已软删除的 blob 共存。

启用了 HNS 的 blob 容器中的每个软删除的 blob 或目录都具有 `DeletionID` 属性。 此属性将具有相同名称的 blob 或目录区分开来。 启用了非 HNS 的 blob 容器中的软删除的 blob 没有 `DeletionID` 属性。

启用了非 HNS 的 blob 容器还支持“blob 版本控制”。 如果启用了 blob 版本控制，某些操作（如删除）的行为将会更改。 有关详细信息，请参阅 [Azure 存储资源管理器 blob 版本控制指南](./storage-explorer-blob-versioning.md)。

## <a name="view-soft-deleted-blobs"></a>查看软删除的 blob

在 blob 资源管理器中，软删除的 blob 显示在某些视图上下文下。

对于未启用 HNS 的 blob 容器，请选择“活动 blob 和软删除的 blob”或“所有 blob 和没有当前版本的 blob”视图上下文以查看软删除的 blob。

对于启用了 HNS 的 blob 容器，请选择“活动和软删除的 blob”或“仅已删除项”视图上下文以查看软删除的 blob 和目录。

## <a name="delete-blobs"></a>删除 Blob

删除 blob 或目录时，存储资源管理器会检查存储帐户的当前删除保留策略。 如果你继续执行删除操作，则会出现一个确认对话框，通知你将发生的情况。 如果软删除被禁用，你可以在确认对话框中选择“启用软删除”按钮来启用它。

> [!WARNING]
> 如果你刚刚保存了一个新的删除保留策略，存储资源管理器可能看不到它。 强烈建议你在删除数据之前等待至少 30 秒，让新策略生效。

## <a name="undelete-blobs"></a>取消删除 blob

存储资源管理器可以通过递归方式批量取消删除软删除的 blob。

若要取消删除 blob，请选择要取消删除的软删除的 blob，并从工具栏或关联菜单中使用“取消删除”→“取消删除所选项”。

还可以在目录下以递归方式取消删除 blob。 如果选定内容中包含活动目录，存储资源管理器会取消删除其中所有软删除的 blob 或目录。

在启用了 HNS 的 blob 容器中，如果已存在同名的活动 blob，则取消删除 blob 将失败。

> [!NOTE]
> 软删除的快照只能通过取消删除基本 blob 来取消删除。 无法取消删除单个快照。

## <a name="undelete-blobs-by-date-range"></a>按日期范围取消删除 blob

使用存储资源管理器还可以根据 blob 的删除时间取消删除 blob。

若要按日期范围取消删除 blob，请选择要取消删除的软删除的 blob，并从工具栏或关联菜单中使用“取消删除”→“按日期取消删除...”。

“按日期取消删除...”与“删除所选项”的效果完全相同，只不过它将筛选出删除时间超出指定范围的 blob 或目录。

## <a name="see-also"></a>另请参阅

- [Azure 存储资源管理器 Blob 版本控制指南](./storage-explorer-blob-versioning.md)
- [blob 的软删除](../blobs/soft-delete-blob-overview.md)
