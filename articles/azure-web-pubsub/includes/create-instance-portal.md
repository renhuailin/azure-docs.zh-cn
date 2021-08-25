---
title: 如何通过门户创建 Azure Web PubSub 实例？
description: 包含文件讲解如何使用门户创建 Azure Web PubSub 实例
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 03/11/2021
ms.openlocfilehash: 38571df633c50cf309f15bd98218e77ab75c610e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121734192"
---
## <a name="sign-in-to-azure"></a>登录 Azure

使用 Azure 帐户登录到 Azure 门户 ([https://portal.azure.com/](https://portal.azure.com/))。

## <a name="create-an-azure-web-pubsub-service-instance"></a>创建 Azure Web PubSub 服务实例

你的应用程序将连接到 Azure 中的 Web PubSub 服务实例。

1. 选择 Azure 门户左上角的“新建”按钮。 在“新建”屏幕中，在搜索框中键入“Web PubSub”，然后按 Enter。 （还可以从 `Web` 类别中搜索 Azure Web PubSub。）

    :::image type="content" source="../media/create-instance-portal/search-web-pubsub-in-portal.png" alt-text="屏幕截图显示在门户中搜索 Azure Web PubSub。":::

2. 在搜索结果中选择“Web PubSub”，然后选择“创建” 。

3. 输入以下设置。

    | 设置      | 建议的值  | 说明                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **资源名称** | 全局唯一名称 | 标识新 Web PubSub 服务实例的全局唯一名称。 有效字符为 `a-z`、`0-9` 和 `-`。 |
    | **订阅** | 你的订阅 | 在其下创建此新的 Web PubSub 服务实例的 Azure 订阅。 |
    | **[资源组](../../azure-resource-manager/management/overview.md)** |  myResourceGroup | 要在其中创建 Web PubSub 服务实例的新资源组的名称。 |
    | **位置** | 美国西部 | 选择你附近的[区域](https://azure.microsoft.com/regions/)。 |
    | **定价层** | 免费 | 可以先免费试用 Azure Web PubSub 服务。 了解有关 [Azure Web PubSub 服务定价层](https://azure.microsoft.com/pricing/details/web-pubsub/)的更多详细信息 |
    | **单位计数** |  - | 单位计数指定 Web PubSub 服务实例可接受的连接数。 每个单位最多支持 1000 个并发连接。 它只能在标准层中配置。 |

    :::image type="content" source="../media/howto-develop-create-instance/create-web-pubsub-instance-in-portal.png" alt-text="屏幕截图显示在门户中创建 Azure Web PubSub 实例。":::

4. 选择“创建”，开始部署 Web PubSub 服务实例。
