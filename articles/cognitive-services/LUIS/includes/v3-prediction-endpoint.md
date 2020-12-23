---
title: 如何获取 V3 预测终结点
titleSuffix: Azure Cognitive Services
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 05/05/2020
ms.openlocfilehash: 85fcd0308083350a02afd4eadac2b361337f3b33
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93128119"
---
1. 在 LUIS 门户中的“Azure 资源”页面（左菜单）上的“预测资源”选项卡的“管理”部分（右上菜单）中，复制页面底部的“示例查询”   。 该 URL 具有应用 ID、密钥和槽名称。 V3 预测终结点 URL 的格式如下：`https://YOUR-RESOURCE-NAME.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-PREDICTION-KEY&<optional-name-value-pairs>&query=YOUR_QUERY_HERE`

    :::image type="content" source="../media/prediction-resources-example-query.png" alt-text="预测资源部分中的示例查询" lightbox="../media/prediction-resources-example-query.png":::
    
    将 URL 粘贴到新的浏览器选项卡中。如果看不到该 URL，则表明你没有预测资源，需要创建一个。 

    

    

