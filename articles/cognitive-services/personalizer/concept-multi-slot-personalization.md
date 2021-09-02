---
title: 多槽个性化
description: 了解在哪个位置以及何时通过个性化体验创建服务排名和奖励 API 使用单槽和多槽个性化。
services: cognitive-services
author: jeffmend
ms.author: jeffme
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: quickstart
ms.date: 05/24/2021
ms.openlocfilehash: 38e94b4e7527fa32b24773e5120d60d6d039f169
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122829422"
---
# <a name="multi-slot-personalization-preview"></a>多槽个性化（预览版）

使用多槽个性化（预览版），你可以以会向用户显示多个操作（例如某个产品或某个内容片段）的 Web 布局、轮播和列表中的内容为目标。 使用个性化体验创建服务多槽 API，你可以让个性化体验创建服务中的 AI 模型从用户界面中的位置进行考虑和学习，了解哪些用户上下文和产品会驱动某些行为。 例如，个性化体验创建服务可能会了解到，某些产品或内容作为边栏或页脚会比作为页面上的主要突出显示项目吸引更多的点击。

本文介绍多槽个性化为何能够改善结果、如何启用它以及何时使用它。 本文假设你熟悉 `Rank` 和 `Reward` 等个性化体验创建服务 API，并在概念上了解如何在应用程序中使用该服务。 如果你不熟悉个性化体验创建服务及其工作原理，请在继续操作之前先查看以下文档：

* [什么是个性化体验创建服务](what-is-personalizer.md) 
* [个性化体验创建服务的工作原理](how-personalizer-works.md) 

> [!IMPORTANT] 
>  多槽个性化目前以公共预览版提供。 功能、方法和过程将会根据用户反馈进行更改。 启用多槽预览版会永久禁用循环中的其他个性化体验创建服务功能。 为个性化体验创建服务循环启用多槽个性化后无法关闭该功能。 在为多槽个性化配置个性化体验创建服务循环之前，请先阅读本文档并考虑到这种影响。

<!-- 
We encourage you to provide feedback on multi-slot personalization APIs via ...
-->

## <a name="when-to-use-multi-slot-personalization"></a>何时使用多槽个性化

每当向用户显示产品和/或内容时，你可能想要向客户显示多个项。 例如：

* 主页的网站布局：许多磁贴和页面区域专门用于在不同形状和大小的框、横幅和边栏中突出显示内容。 多槽个性化将了解此布局的特征对客户的选择和操作会产生怎样的影响。
* 轮播：动态变化内容的轮播需要循环少量的项。 多槽个性化可以了解序列甚至显示持续时间会对点击和互动产生怎样的影响。
* 相关产品/内容和嵌入式引用：与用户互动的常见方式是在横幅、边栏、晕影和页脚框中嵌入或穿插对其他内容和产品的引用。 在引用很有可能有利于提高使用量的情况下，多槽个性化可帮助你分配引用。
* 搜索结果或列表：如果应用程序具有搜索功能并以列表或磁贴形式提供结果，则你可以使用多槽个性化来选择要在最前面突出显示的项，因为它提供的元数据比传统排名器更多。
* 动态频道和播放列表：多槽个性化有助于确定要在动态频道中接下来播放的视频或歌曲列表的短序列。

多槽个性化允许在用户界面中声明要为其选择操作的“槽”。 它还允许提供有关槽的详细信息（可供个性化体验创建服务来改善产品的放置）- 例如，这是大框还是小框？它显示字幕还是仅仅显示功能？它位于页脚还是边栏中？


## <a name="how-to-use-multi-slot-personalization"></a>如何使用多槽个性化 

1. [启用多槽个性化](#enable-multi-slot-personalization)
1. [为排名请求创建 JSON 对象](#create-json-object-for-a-rank-request)
1. [调用用于定义槽和基线操作的排名 API](#use-the-response-of-the-rank-api)
1. [调用奖励 API](#call-the-reward-api)


### <a name="enable-multi-slot-personalization"></a>启用多槽个性化 

请参阅下面的[单槽个性化与多槽个性化之间的差别](#differences-between-single-slot-and-multi-slot-personalization)来了解多槽个性化并确定它是否对你有用。 多槽个性化是一项预览功能：如果你想要测试多槽个性化 API，我们建议创建一个新的个性化体验创建服务循环，因为启用多槽个性化的操作不可逆，并且会影响生产环境中运行的个性化体验创建服务循环。

决定将循环转换为多槽个性化后，必须针对此个性化体验创建服务循环执行以下步骤一次：

[!INCLUDE [Upgrade Personalizer instance to Multi-Slot](./includes/upgrade-personalizer-multi-slot.md)]

### <a name="create-json-object-for-a-rank-request"></a>为排名请求创建 JSON 对象
使用多槽个性化需要一个与单槽个性化 API 略有不同的 API。

可在每个排名调用请求中的 slots 对象中声明可被分配操作的槽：

* 槽数组：需要声明槽数组。 槽是有序的：每个槽在数组中的位置非常重要。 我们强烈建议根据每个槽很有可能获得的奖励/点击/转换数量来为槽定义排序，从获得奖励/点击/转换数量最多的槽开始。 例如，应该将网站主页的大型“主”框（而不是小型页脚）放置在槽 1 位置。  所有其他内容将以平等方式处理，个性化体验创建服务会将更有可能获得奖励的操作分配到序列中靠前的位置。
* 槽 ID：需要为每个槽指定一个 slotId，此 ID 是对此排名调用中所有其他槽独一无二的字符串。
* 槽特征：应提供附加的元数据用于描述该槽并将它与其他槽进一步区分开来。 这些元数据称为特征。 确定槽特征时，必须遵循已为有关上下文和操作的特征建议的相同指导原则（请参阅：[上下文和操作的特征]()）。  典型的槽特征有助于识别用户界面元素的大小、位置或视觉特征。 例如 `position: "top-left"`、`size: "big"`、`animated: "no"`、`sidebar: "true"` 或 `sequence: "1"`。
* 基线操作：需要为每个槽指定基线操作 ID， 即，个性化体验创建服务不存在时要在该槽中显示的操作 ID。 必须提供此 ID 才能以[学徒模式]()训练个性化体验创建服务，并在执行[脱机评估]()时获得有意义的数字。
* 使用足够的操作：确保使用比槽更多的操作调用排名，使个性化体验创建服务能够将至少一个操作分配给每个槽。 个性化体验创建服务不会跨槽重复提供操作建议：排名响应将每个操作最多分配给一个槽。 

可以不时地添加或删除槽、添加和更改其特征，或者为数组重新排序：个性化体验创建服务将根据新信息做出调整并保持训练。

下面是包含一些示例特征的示例 `slots` 对象。 尽管大部分 `slots` 对象是稳定的（因为 UI 往往会缓慢变化），而且在大多数情况下它是不会变化的，但必须确保为每个排名调用分配适当的 baselineAction ID。 


```json
"slots": [ 
    { 
      "id": "BigHighlight", 
      "features": [ 
            { 
              "size": "Large", 
              "position": "Left-Middle" 
            }
        ],
        "baselineAction": "BlackBoot_4656" 
    }, 

    { 
      "id": "Sidebar1", 
      "features": [ 
            { 
              "size": "Small", 
              "position": "Right-Top" 
            } 
        ],
        "baselineAction": "TrekkingShoe_1122"  
    }  
  ]

```


### <a name="use-the-response-of-the-rank-api"></a>使用排名 API 的响应

上述请求的多槽排名响应可能如下所示： 

```json
{ 
  "slots": [ 
        { 
          "id": "BigHighlight", 
          "rewardActionId": "WhiteSneaker_8181" 
        }, 
        { 
          "id": "SideBar1", 
          "rewardActionId": "BlackBoot_4656" 
        } 
    ], 
  "eventId": "123456D0-BFEE-4598-8196-C57383D38E10" 
} 
```

获取每个槽的 rewardActionId，并适当地使用它来呈现用户界面。

### <a name="call-the-reward-api"></a>调用奖励 API

个性化体验创建服务知道如何选择可最大程度获得奖励的操作。 应用程序将观察用户行为，并根据观测到的反应计算个性化体验创建服务的“奖励分数”。 例如，如果用户点击了 `"slotId": "SideBar1",` 中的操作，则你会向个性化体验创建服务发送“1”，以提供操作选择的积极强化。

奖励 API 在 URL 中为奖励指定 eventId：

```http
https://{endpoint}/personalizer/v1.0/events/{eventId}/reward
``` 

例如，对于上述 ID 为 123456D0-BFEE-4598-8196-C57383D38E10 的事件，奖励将发送到 `https://{endpoint}/personalizer/v1.0/events/123456D0-BFEE-4598-8196-C57383D38E10/reward/reward`：

```json
{ 
  "reward": [ 
    { 
      "slotId": "BigHighlight", 
      "value": 0.2 
    }, 
    { 
      "slotId": "SideBar1", 
     "value": 1.0 
    }, 
  ] 
} 
```

无需在仅仅一次奖励 API 调用中提供所有奖励分数。 可以多次调用奖励 API，每次调用时使用相应的 eventId 和 slotId。 如果在某个事件中未收到槽的奖励分数，则个性化体验创建服务将隐式分配针对循环配置的默认奖励（通常为 0）。


## <a name="differences-between-single-slot-and-multi-slot-personalization"></a>单槽个性化与多槽个性化之间的差别

对单槽个性化与多槽个性化使用排名和奖励 API 的方式有差别：

|  说明  | 单槽个性化   | 多槽个性化    |  
|---------------|-------------------------------|-------------------------------|
| 排名 API 调用请求元素 | 发送上下文对象以及操作列表  | 发送上下文、操作列表以及槽的已排序列表     | 
| 指定基线的排名请求 | 个性化体验创建服务采用操作列表中的第一个操作作为基线操作（个性化体验创建服务不存在时应用程序选择的项）。|必须指定已在每个槽中使用的基线 ActionID。|
| 排名 API 调用响应 | 应用程序将突出显示 rewardActionId 字段中指示的操作     | 响应会为请求中指定的每个槽包含一个不同的 rewardActionId。 应用程序将显示每个槽中的这些 rewardActionId 操作。 | 
| 奖励 API 调用 | 使用奖励分数调用奖励 API，该分数是根据用户如何与此特定 eventId 的 rewardActionId 交互计算得出的。 例如，如果用户点击了该操作，则你会发送奖励 1。     | 根据具有 rewardActionId 的操作促成所需用户行为的作用大小，为每个槽指定奖励。 可以在使用同一 eventId 的一个或多个奖励 API 调用中发送此奖励。   | 


### <a name="impact-of-enabling-multi-slot-for-a-personalizer-loop"></a>为个性化体验创建服务循环启用多槽的影响

此外，在启用多槽时，请考虑以下因素：

|  说明  | 单槽个性化   | 多槽个性化    | 
|---------------|-------------------------------|-------------------------------|
| 非活动事件和激活    | 调用激活 API 时，个性化体验创建服务将激活事件，并等待提供奖励分数，如果超过奖励等待时间，则分配配置的默认奖励。 | 个性化体验创建服务激活并等待提供 eventId 中指定的所有槽的奖励 |     
| 学徒模式 | 个性化体验创建服务排名 API 始终返回基线操作，并通过模拟基线操作来训练内部模型。| 个性化体验创建服务排名 API 返回 baselineAction 字段中指定的每个槽的基线操作。 个性化体验创建服务通过模拟第一个操作来训练内部模型 |
| 学习速度 | 仅从一个突出显示的操作学习 | 可以从与任意槽的交互中学习。 这通常意味着，有更多的用户行为可产生奖励，从而可以加快个性化体验创建服务的学习速度。 |  
| 脱机评估 |根据选择基线和优化学习设置的操作，将个性化体验创建服务的性能与这些设置进行比较。| （预览版限制）仅评估数组中第一个槽的性能。 为获得更准确的评估结果，请确保将奖励最高的槽放在数组中的第一个位置。 |
| 自动优化（预览版） | 个性化体验创建服务循环可以定期在后台执行脱机评估并优化学习设置，而无需管理干预 | （预览版限制）对于已启用多槽 API 的个性化体验创建服务循环，将禁用自动优化。 |

## <a name="next-steps"></a>后续步骤
* [如何使用多槽个性化](how-to-multi-slot.md)
* [示例：运行多槽个性化循环的 Jupyter 笔记本](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/Personalizer/azure-notebook)