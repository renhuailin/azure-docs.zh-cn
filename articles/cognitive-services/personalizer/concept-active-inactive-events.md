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
ms.openlocfilehash: ba95c22a773a382a3c03aab18f8f885e6a2791d8
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122831006"
---
# <a name="active-and-inactive-events"></a>活动和非活动事件

活动事件是对排名的任何调用，其中你知道要向客户显示结果并确定奖励分数。 此选项为默认行为。

非活动事件是对排名的调用，其中你不确定用户是否将由于业务逻辑而看到建议的操作。 这样就可以放弃事件，以便个性化体验创建服务无需默认奖励即可训练。 非活动事件不应调用奖励 API。

学习循环应知道事件的实际类型，这一点很重要。 非活动事件不会有奖励调用。 活动事件应有奖励调用，但如果从未发出 API 调用，则会应用默认奖励分数。 知道事件会影响用户体验后，即会将事件的状态从“非活动”更改为“活动”。

## <a name="typical-active-events-scenario"></a>典型的活动事件方案

当应用程序调用排名 API 时，你会收到操作，应用程序应在 rewardActionId 字段中显示该操作。  从那时之后，个性化体验创建服务需要使用具有相同 eventId 的奖励分数进行奖励调用。 奖励分数用于为将来的排名调用训练模型。 如果没有收到针对 eventId 的奖励调用，则应用默认奖励。 在 Azure 门户的个性化体验创建服务资源上设置[默认奖励](how-to-settings.md#configure-rewards-for-the-feedback-loop)。

## <a name="other-event-type-scenarios"></a>其他事件类型方案

在某些情况下，应用程序可能需要先调用排名，然后才能知道是使用结果还是向用户显示结果。 这可能会在某些情况下发生，例如，升级内容的页面呈现被市场营销活动覆盖。 如果排名调用的结果从未使用过，并且用户从未看到过，则不发送相应的奖励调用。

通常会在以下情况下发生：

* 你正在预呈现不一定可让用户看到的 UI。
* 应用程序正在执行预测个性化，其中的排名调用是使用实时性不够高的上下文发出的，应用程序不一定会使用输出。

在这些情况下，使用个性化体验创建服务调用排名，请求事件为“非活动”。 个性化体验创建服务不需要此事件的奖励，也不会应用默认奖励。

稍后在业务逻辑中，如果应用程序使用排名调用中的信息，则只需激活事件。 一旦事件处于活动状态，个性化体验创建服务就需要事件奖励。 如果对奖励 API 没有显式调用，则个性化体验创建服务将应用默认奖励。

## <a name="inactive-events"></a>非活动事件

若要禁用事件训练，请使用 `learningEnabled = False` 调用排名。

对于非活动事件，如果为 eventId 发送奖励或为该 eventId 调用 `activate` API，则会隐式激活学习。

## <a name="next-steps"></a>后续步骤

* 了解[如何确定奖励分数以及要考虑的数据](concept-rewards.md)。
