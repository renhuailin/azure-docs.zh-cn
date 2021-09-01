---
title: 新增功能 - 个性化体验创建服务
titleSuffix: Azure Cognitive Services
description: 本文包含有关个性化体验创建服务的新闻。
author: jeffmend
ms.author: jeffme
ms.manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/28/2021
ms.openlocfilehash: 4c4d4151342211712f63d254630cc480dd761235
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122830232"
---
# <a name="whats-new-in-personalizer"></a>个性化体验创建服务的新增功能

了解服务中的新增功能。 这些项可能包括发布说明、视频、博客文章和其他类型的信息。 将此页加入书签，以随时了解服务的最新信息。

## <a name="release-notes"></a>发行说明

### <a name="may-2021----build-conference"></a>2021 年 5 月 - //Build 大会

* 自动优化（预览版）：可以配置正在使用的个性化体验创建服务循环，以便随着时间的推移不断改进，减少工作量。 个性化体验创建服务将自动运行脱机评估、发现更好的机器学习设置并应用这些设置。 有关详细信息，请参阅[个性化体验创建服务自动优化（预览）](concept-auto-optimization.md)。
* 多槽个性化（预览）：如果具有平铺布局、轮播内容和/或边栏，则对于同一页面上的推荐产品或内容的每个位置，使用个性化体验创建服务会更方便。 个性化体验创建服务现在可以在排名 API 中获取槽列表，为每个槽分配操作，并参考每个槽发送的奖励分数。 有关详细信息，请参阅[多槽个性化（预览）](concept-multi-slot-personalization.md)。
* 个性化体验创建服务现已在更多区域提供。
* 已更新代码示例 (GitHub) 和文档。 使用以下链接查看更新后的示例：
  * [C#/.NET](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/Personalizer)
  * [JavaScript](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/Personalizer)
  * [Python 示例](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/Personalizer)

### <a name="july-2020"></a>2020 年 7 月

* 新教程 - [在聊天机器人中使用个性化体验创建服务](tutorial-use-personalizer-chat-bot.md)

### <a name="june-2020"></a>2020 年 6 月

* 新教程 - [在 Web 应用中使用个性化体验创建服务](tutorial-use-personalizer-web-app.md)

### <a name="may-2020---build-conference"></a>2020 年 5 月 - //Build 大会

以下功能以公共预览版提供：

 * 作为学习行为的[学徒模式](concept-apprentice-mode.md)。

### <a name="march-2020"></a>2020 年 3 月

* 现在，将对此服务的所有 HTTP 请求强制执行 TLS 1.2。 有关详细信息，请参阅 [Azure 认知服务安全性](../cognitive-services-security.md)。

### <a name="november-2019---ignite-conference"></a>2019 年 11 月 - Ignite 大会

* 个性化体验创建服务正式发布 (GA)
* 包含整个生命周期的 Azure Notebooks [教程](tutorial-use-azure-notebook-generate-loop-data.md)

### <a name="may-2019---build-conference"></a>2019 年 5 月 - //Build 大会

以下预览功能是在 Build 2019 大会上发布的：

* [排名和奖励学习循环](what-is-personalizer.md)

## <a name="videos"></a>视频

### <a name="2019-build-videos"></a>2019 Build 视频

* [通过认知服务个性化体验创建服务提供正确的体验和内容（如 Xbox）](https://azure.microsoft.com/resources/videos/build-2019-deliver-the-right-experiences-and-content-with-cognitive-services-personalizer/)

## <a name="service-updates"></a>服务更新

[认知服务的 Azure 更新公告](https://azure.microsoft.com/updates/?product=cognitive-services)

## <a name="next-steps"></a>后续步骤

* [快速入门：以 C# 创建反馈循环](./quickstart-personalizer-sdk.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)
* [使用交互式演示](https://personalizationdemo.azurewebsites.net/)