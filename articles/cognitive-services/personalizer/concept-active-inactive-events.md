---
title: 活动和非活动事件 - 个性化体验创建服务
description: 本文介绍如何在个性化体验创建服务中使用活动和非活动事件。
author: jeffmend
ms.author: jeffme
ms.manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 948d375c0f580a71dbd27fa10660c0c7e0046a10
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129219147"
---
# <a name="defer-event-activation"></a>延迟事件激活

通过延迟激活事件，你可以创建个性化网站或邮件营销活动，这是因为用户实际上可能永远不会查看相关页面或打开相关电子邮件。 在此类情况下，应用程序可能需要先调用排名，然后才能知道是使用结果还是向用户显示结果。 如果内容不会显示给用户，则不应采用供应用程序从中进行学习的默认奖励（通常为零）。
通过延迟激活，你可以在某个时间点使用排名调用的结果，决定是应从以后开始了解该事件，还是应在代码中的其他位置了解该事件。

## <a name="typical-scenarios-for-deferred-activation"></a>延迟激活的典型场景

在以下示例场景中，可以将事件延迟激活：

* 你要为用户预先呈现个性化网页，但用户可能永远看不到它，因为某些业务逻辑可能会替代个性化体验创建服务的操作选择。
* 你要将网页中“需要向下滚动鼠标才能看到”的内容个性化，并且用户很可能不会查看该内容。
* 你要将营销电子邮件个性化，并且需要避免从用户从未打开过的电子邮件进行训练。
* 你对动态媒体频道进行了个性化，但用户可能会在该频道播放个性化体验创建服务选择的歌曲或视频之前停止播放该频道。 

一般来说，以下情况会出现这些场景：

* 你要预先呈现 UI，但用户可能会查看该 UI，也可能会因 UI 或时间约束而不会查看该 UI。
* 应用程序在执行预测性的个性化，在该个性化中，你在进行排名调用之前并不知道你是否会使用输出。

## <a name="how-to-defer-activation-and-later-activate-events"></a>如何延迟激活事件，以及如何稍后激活事件

若要对某个事件延迟激活，请在请求正文中使用 `deferActivation = True` 调用[排名](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank)。

一旦你知道已经为用户显示了个性化内容或媒体，并且应该有奖励，就必须激活该事件。 为此，请使用 eventId 调用[激活 API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Activate)。


必须在“奖励等待时间”时间窗口到期之前收到与该 EventID 调用对应的[激活 API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Activate) 调用。

### <a name="behavior-with-deferred-activation"></a>延迟激活的行为 

个性化体验创建服务会从事件和奖励中进行学习，如下所述：
* 如果你使用 `deferActivation = True` 调用排名，但不调用该 eventId 的 `Activate` API，而是调用奖励，则个性化体验创建服务无法从事件中进行学习。
* 如果你使用 `deferActivation = True` 调用排名，并且调用该 eventId 的 `Activate` API，同时调用奖励，则个性化体验创建服务会根据指定的奖励分数从事件中进行学习。
* 如果你使用 `deferActivation = True` 调用排名，并且调用该 eventId 的 `Activate` API，但省略调用奖励的步骤，则个性化体验创建服务会根据在配置中设置的默认奖励分数从事件中进行学习。

## <a name="next-steps"></a>后续步骤
* 如何配置[默认奖励](how-to-settings.md#configure-rewards-for-the-feedback-loop)。
* 了解[如何确定奖励分数以及要考虑的数据](concept-rewards.md)。
