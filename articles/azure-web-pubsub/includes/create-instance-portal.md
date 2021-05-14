---
title: 如何通过门户创建 Azure Web PubSub 实例？
description: 包含文件讲解如何使用门户创建 Azure Web PubSub 实例
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 03/11/2021
ms.openlocfilehash: 72e3c56b4f8fdaeec94bff60be45aadfb9b09e76
ms.sourcegitcommit: 62e800ec1306c45e2d8310c40da5873f7945c657
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108166852"
---
## <a name="sign-in-to-azure"></a>登录 Azure

使用 Azure 帐户登录到 Azure 门户 ([https://portal.azure.com/](https://portal.azure.com/))。

## <a name="create-an-azure-web-pubsub-service-instance"></a>创建 Azure Web PubSub 服务实例

你的应用程序将连接到 Azure 中的 Web PubSub 服务实例。

1. 选择 Azure 门户左上角的“新建”按钮。 在“新建”屏幕中，在搜索框中键入“Web PubSub”，然后按 Enter。

1. 在搜索结果中选择“Web PubSub”，然后选择“创建” 。

1. 输入以下设置。

    | 设置      | 建议的值  | 说明                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **资源名称** | 全局唯一名称 | 用于标识新的 Web PubSub 服务实例的名称。 有效字符为 `a-z`、`0-9` 和 `-`。  | 
    | **订阅** | 你的订阅 | 在其下创建这个新的 Web PubSub 服务实例的订阅。 | 
    | **[资源组](../../azure-resource-manager/management/overview.md)** |  myResourceGroup | 要在其中创建 Web PubSub 服务实例的新资源组的名称。 | 
    | **位置** | 美国西部 | 选择你附近的[区域](https://azure.microsoft.com/regions/)。 |
    | **定价层** | 免费 | 免费试用 Azure Web PubSub 服务。 |
    | **单位计数** |  不适用 | 单位计数指定 Web PubSub 服务实例可接受的连接数。 它只能在标准层中配置。 |

1. 选择“创建”，开始部署 Web PubSub 服务实例。
