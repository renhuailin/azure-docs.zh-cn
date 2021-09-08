---
title: Azure 存储资源管理器直接链接 | Microsoft Docs
description: Azure 存储资源管理器直接链接的文档
services: storage
author: JasonYeMSFT
ms.service: storage
ms.topic: article
ms.date: 02/24/2021
ms.author: chuye
ms.openlocfilehash: 283ee2dd27d22e16177efe34d4f456eca40d997d
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123451486"
---
# <a name="azure-storage-explorer-direct-link"></a>Azure 存储资源管理器直接链接

在 Windows 和 macOS 上，存储资源管理器支持具有 `storageexplorer://` 协议的 URI。 这些 URI 称为直接链接。 直接链接指向存储资源管理器中的 Azure 存储资源。 跟踪直接链接将打开存储资源管理器并导航到它指向的资源。 本文介绍直接链接的工作原理，以及使用它们的方法。

## <a name="how-direct-links-work"></a>直接链接的工作原理

存储资源管理器使用树状视图来可视化 Azure 中的资源。 直接链接包含树中链接资源节点的层次结构信息。 跟踪直接链接后，存储资源管理器随即打开并接收直接链接中的参数。 然后存储资源管理器使用这些参数导航到树状视图中的链接资源。

> [!IMPORTANT]
> 你必须登录，并拥有访问链接资源的必要权限，才能使用直接链接。

## <a name="parameters"></a>parameters

存储资源管理器直接链接始终以协议 `storageexplorer://` 开头。 下表说明了直接链接中每个可能的参数。

参数 | 说明
:---------| :---------
`v`         | 直接链接协议的版本。
`accountid` | 链接资源的存储帐户的 Azure 资源管理器资源 ID。 如果链接资源是存储帐户，此 ID 将是该存储帐户的 Azure 资源管理器资源 ID。 否则，此 ID 将是链接资源所属的存储帐户的 Azure 资源管理器资源 ID。
`resourcetype` | 可选。 仅当链接资源为 Blob 容器、文件共享、队列或表时使用。 必须是“Azure.BlobContainer”、“Azure.FileShare”、“Azure.Queue”、“Azure.FileShare”中的一个。
`resourcename` | 可选。 仅当链接资源为 Blob 容器、文件共享、队列或表时使用。 链接资源的名称。

下面是指向 Blob 容器的直接链接示例。 
`storageexplorer://v=1&accountid=/subscriptions/the_subscription_id/resourceGroups/the_resource_group_name/providers/Microsoft.Storage/storageAccounts/the_storage_account_name&subscriptionid=the_subscription_id&resourcetype=Azure.BlobContainer&resourcename=the_blob_container_name`

## <a name="get-a-direct-link-from-storage-explorer"></a>从存储资源管理器获取直接链接

可以使用存储资源管理器获取资源的直接链接。 在树状视图中打开资源节点的上下文菜单。 然后，使用“复制直接链接”操作将其直接链接复制到剪贴板。

## <a name="direct-link-limitations"></a>直接链接限制

只有订阅节点下的资源才支持直接链接。 此外，仅支持以下资源类型：

- 存储帐户
- Blob 容器
- 队列
- 文件共享
- 表
