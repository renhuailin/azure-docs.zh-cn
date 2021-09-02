---
title: 创建个性化体验创建服务资源
description: 本文介绍如何在 Azure 门户中为每个反馈循环创建个性化体验创建服务资源。
author: jeffmend
ms.author: jeffme
ms.manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: how-to
ms.date: 03/26/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: b901153653b292a77ff19a25f065cf225e54f76e
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122831555"
---
# <a name="create-a-personalizer-resource"></a>创建个性化体验创建服务资源

个性化体验创建服务资源与个性化体验创建服务学习循环是相同的。 系统会为每个主体域或内容区域创建单个资源（即学习循环）。 不要在同一循环中使用多个内容区域，因为这会使学习循环混淆，并提供糟糕的预测。

如果希望个性化体验创建服务为网页的多个内容区域选择最佳内容，请对每个内容使用不同的学习循环。


## <a name="create-a-resource-in-the-azure-portal"></a>在 Azure 门户中创建资源

为每个反馈循环创建一个个性化体验创建服务资源。

1. 登录到 [Azure 门户](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)。 前面的链接会将你带到个性化体验创建服务的“创建”页。
1. 输入服务名称，选择订阅、位置、定价层和资源组。

    > [!div class="mx-imgBorder"]
    > ![使用 Azure 门户创建个性化体验创建服务资源（也称为学习循环）。](./media/how-to-create-resource/how-to-create-personalizer-resource-learning-loop.png)

1. 选择“创建”以创建资源。

1. 部署资源后，请选择“转到资源”按钮，以转到个性化体验创建服务资源。

1. 选择资源的“快速启动”页，然后复制终结点和密钥的值。 需要资源终结点和密钥才能使用排名和奖励 API。

1. 选择新资源的“配置”页以[配置学习循环](how-to-settings.md)。

## <a name="create-a-resource-with-the-azure-cli"></a>使用 Azure CLI 创建资源

1. 使用以下命令登录到 Azure CLI：

    ```azurecli-interactive
    az login
    ```

1. 创建资源组（逻辑分组）来管理要与个性化体验创建服务资源一起使用的所有 Azure 资源。


    ```azurecli-interactive
    az group create \
        --name your-personalizer-resource-group \
        --location westus2
    ```

1. 使用以下命令为现有资源组创建新的个性化体验创建服务资源、学习循环。

    ```azurecli-interactive
    az cognitiveservices account create \
        --name your-personalizer-learning-loop \
        --resource-group your-personalizer-resource-group \
        --kind Personalizer \
        --sku F0 \
        --location westus2 \
        --yes
    ```

    这会返回一个 JSON 对象，其中包含资源终结点。

1. 使用以下 Azure CLI 命令获取资源密钥。

    ```azurecli-interactive
        az cognitiveservices account keys list \
        --name your-personalizer-learning-loop \
        --resource-group your-personalizer-resource-group
    ```

    需要资源终结点和密钥才能使用排名和奖励 API。

## <a name="next-steps"></a>后续步骤

* [配置](how-to-settings.md)个性化体验创建服务学习循环
