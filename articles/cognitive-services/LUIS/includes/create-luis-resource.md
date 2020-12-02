---
title: 创建 LUIS 资源
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/20/2020
ms.author: aahi
ms.openlocfilehash: ee7fd384a198c5eff672b14b6cb479aac26cfe54
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "95972500"
---
<a name="create-luis-resources"></a>

## <a name="create-luis-resources-in-the-azure-portal"></a>在 Azure 门户中创建 LUIS 资源

1. 使用[此链接](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)在 Azure 门户中开始创建 LUIS 资源。

1. 输入所有必需的设置：

    |名称|目的|
    |--|--|
    |订阅 | 将对资源计费的订阅。|
    |资源组| 你选择或创建的自定义资源组名称。 使用资源组可将 Azure 资源分组，以便进行访问和管理。|
    |名称| 你选择的自定义名称。 它用作创作以及预测终结点查询的自定义子域。|
    |创作位置|与模型关联的区域。|
    |创作定价层|确定每秒和每月的最大事务数。|
    |预测位置|与已发布的预测终结点运行时关联的区域。|
    |预测定价层|确定每秒和每月的最大事务数。|

    > [!div class="mx-imgBorder"]
    > [![显示“创建”下的“基本信息”选项卡的屏幕截图。](../media/luis-how-to-azure-subscription/create-resource-in-azure-small.png)](../media/luis-how-to-azure-subscription/create-resource-in-azure-small.png#lightbox)

1. 选择“查看 + 创建”并等待创建资源。
1. 创建这两个资源之后，仍然在 Azure 门户中选择新的创作资源。 然后选择“密钥和终结点”，获取创作“终结点 URL”和“密钥”，通过编程方式进行创作  。

> [!TIP]
> 若要使用资源，请在 LUIS 门户中[分配资源](../luis-how-to-azure-subscription.md#assign-an-authoring-resource-in-the-luis-portal-for-all-apps)。
