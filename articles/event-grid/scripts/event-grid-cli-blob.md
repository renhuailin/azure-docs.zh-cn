---
title: Azure CLI 脚本示例 - 订阅 Blob 存储帐户 | Microsoft Docs
description: 本文提供了一个示例 Azure CLI 脚本，演示如何订阅 Azure Blob 存储帐户的事件。
ms.devlang: azurecli
ms.topic: sample
ms.date: 09/15/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: f8c5baf79209414031073c8ef0505c0ca18252a4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128584611"
---
# <a name="subscribe-to-events-for-a-blob-storage-account-with-azure-cli"></a>使用 Azure CLI 订阅 Blob 存储帐户的事件

此脚本创建 Blob 存储帐户事件的事件网格订阅。 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>示例脚本

[!code-azurecli[main](../../../cli_scripts/event-grid/subscribe-to-blob-storage/subscribe-to-blob-storage.sh "Subscribe to Blob storage")]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建事件订阅。 表中的每条命令链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az eventgrid event-subscription create](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create) | 创建事件网格订阅。 |
| [az eventgrid event-subscription create](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create) - 扩展版本 | 创建事件网格订阅。 |

## <a name="next-steps"></a>后续步骤

* 有关查询订阅的信息，请参阅[查询事件网格订阅](../query-event-subscriptions.md)。
* 有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](/cli/azure)。
