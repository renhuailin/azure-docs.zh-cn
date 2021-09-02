---
title: 如何在个性化体验创建服务中使用多槽
description: 了解如何在个性化体验创建服务中使用多槽来改进服务提供的内容建议。
services: cognitive-services
author: jeffmend
ms.author: jeffme
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: quickstart
ms.date: 05/24/2021
zone_pivot_groups: programming-languages-set-six
ms.openlocfilehash: ea1f244b43becd65b3bc85f2e40dc2c029ccbf43
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122829253"
---
# <a name="get-started-with-multi-slot-for-azure-personalizer"></a>Azure 个性化体验创建服务多槽入门

使用多槽个性化（预览版），你可以以会向用户显示多个操作（例如某个产品或某个内容片段）的 Web 布局、轮播和列表中的内容为目标。 使用个性化体验创建服务多槽 API，你可以让个性化体验创建服务中的 AI 模型从用户界面中的位置进行考虑和学习，了解哪些用户上下文和产品会驱动某些行为。 例如，个性化体验创建服务可能会了解到，某些产品或内容作为边栏或页脚会比作为页面上的主要突出显示项目吸引更多的点击。 

本指南介绍如何使用个性化体验创建服务多槽 API。

::: zone pivot="programming-language-csharp"

[!INCLUDE [Try multi-slot with C#](./includes/quickstart-multislot-csharp.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

[!INCLUDE [Try multi-slot with Node.js](./includes/quickstart-multislot-nodejs.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [Try multi-slot with Python](./includes/quickstart-multislot-python.md)]

::: zone-end

## <a name="next-steps"></a>后续步骤

* [详细了解多槽](concept-multi-slot-personalization.md)