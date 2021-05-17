---
title: 如何创建 Azure Web PubSub 实例
description: 概述创建 Azure Web PubSub 实例以及如何执行此操作的选项
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: how-to
ms.date: 03/17/2021
ms.openlocfilehash: 9f13f0f62a9bd3b26b4c855110fbc97971ecfea8
ms.sourcegitcommit: 62e800ec1306c45e2d8310c40da5873f7945c657
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108166875"
---
# <a name="how-to-create-azure-web-pubsub-instance"></a>如何创建 Azure Web PubSub 实例

若要使用 Azure Web PubSub 服务生成应用程序，需要创建 Web PubSub 实例，然后连接客户端和服务器。 本操作指南介绍了创建 Azure Web PubSub 实例的选项。

## <a name="create-azure-web-pubsub-instance-with-azure-portal"></a>通过 Azure 门户创建 Azure Web PubSub 实例 

Azure 门户是基于 Web 的统一控制台，提供可替代命令行工具的方法。 可以通过 Azure 门户管理 Azure 订阅。 生成、管理和监视一切内容，从简单的 Web 应用到复杂的云部署。 还可通过 Azure 门户创建 Azure Web PubSub service 实例。

1. 选择 Azure 门户左上角的“新建”按钮。 在“新建”屏幕中，在搜索框中键入“Web PubSub”，然后按 Enter。

1. 在搜索结果中选择“Web PubSub”，然后选择“创建” 。

1. 输入以下设置。

    | 设置      | 说明                                        |
    | ------------ | -------------------------------------------------- |
    | **资源名称** | 标识新 Web PubSub 服务实例的全局唯一名称。 有效字符为 `a-z`、`0-9` 和 `-`。  | 
    | **订阅** | 在其下创建此新的 Web PubSub 服务实例的 Azure 订阅。 | 
    | **[资源组](../azure-resource-manager/management/overview.md)** |  要在其中创建 Web PubSub 服务实例的新的或现有资源组的名称。 | 
    | **位置** | 选择你附近的[区域](https://azure.microsoft.com/regions/)。 |
    | **定价层** | 了解有关 [Azure Web PubSub 服务定价层](https://azure.microsoft.com/pricing/details/web-[pubsub])的更多详细信息。 |
    | **单位计数** |  单位计数指定 Web PubSub 服务实例可接受的连接数。 每个单位最多支持 1000 个并发连接。 它只能在标准层中配置。 |

1. 选择“创建”，开始部署 Web PubSub 服务实例。

## <a name="create-azure-web-pubsub-instance-with-azure-cli"></a>使用 Azure CLI 创建 Azure Web PubSub 实例

[Azure 命令行接口 (Azure CLI)](https://docs.microsoft.com/cli/azure) 是一组用来创建和管理 Azure 资源的命令。 Azure CLI 可用于各种 Azure 服务，可用来快速使用 Azure（侧重于自动化）。 你还可以在 GA 后通过 Azure CLI 创建 Azure Web PubSub 服务实例。


