---
title: 配置学习行为
description: 学徒模式为你提供了个性化体验创建服务及其机器学习功能的置信度，并在不造成在线流量风险的情况下提供向服务发送可从其获知的信息的指标。
author: jeffmend
ms.author: jeffme
ms.manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: how-to
ms.date: 05/01/2020
ms.openlocfilehash: caa3a0d8d9619ee65dbd9d71ae2ff4f56a128c8d
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122831546"
---
# <a name="configure-the-personalizer-learning-behavior"></a>配置个性化体验创建服务学习行为

[学徒模式](concept-apprentice-mode.md)为你提供了个性化体验创建服务及其机器学习功能的可信度和置信度，并确保在不造成在线流量风险的情况下向服务发送可从其获知的信息。

[!INCLUDE [Important Blue Box - Apprentice mode pricing tier](./includes/important-apprentice-mode.md)]

## <a name="configure-apprentice-mode"></a>配置学徒模式

1. 登录到 [Azure 门户](https://portal.azure.com)，以获取个性化体验创建服务资源。

1. 在“配置”页的“学习行为”选项卡上，选择“返回基线操作，以学徒身份学习”，然后选择“保存”。

> [!div class="mx-imgBorder"]
> ![在 Azure 门户中配置学徒模式学习行为的屏幕截图](media/settings/configure-learning-behavior-azure-portal.png)

## <a name="changes-to-the-existing-application"></a>对现有应用程序的更改

现有应用程序不应更改当前选择要显示的操作的方式，或应用程序确定该操作的奖励值的方式。 应用程序的唯一更改可能是发送到个性化体验创建服务的排名 API 的操作的顺序。 应用程序当前显示的操作将作为操作列表中的第一个操作发送。 [排名 API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank) 使用第一个操作训练个性化体验创建服务模型。

### <a name="configure-your-application-to-call-the-rank-api"></a>配置应用程序以调用排名 API

为了将个性化体验创建服务添加到应用程序，需要调用排名和奖励 API。

1. 在确定操作列表及其功能的现有应用程序逻辑中的点后添加[排名 API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank) 调用。 操作列表中的第一个操作必须是现有逻辑选择的操作。

1. 配置代码以显示与排名 API 响应的奖励操作 ID 相关联的操作。

### <a name="configure-your-application-to-call-reward-api"></a>配置应用程序以调用奖励 API

1. 使用现有业务逻辑计算所显示操作的奖励。 此值必须介于 0 到 1 之间。 使用[奖励 API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward) 向个性化体验创建服务发送此奖励。 奖励值不应立即出现，可以在一段时间内延迟，具体取决于你的业务逻辑。

1. 如果在配置的奖励等待时间内未返回奖励，则将改用默认奖励。

## <a name="evaluate-apprentice-mode"></a>评估学徒模式

在 Azure 门户的个性化体验创建服务资源的“评估”页上，查看当前学习行为性能。

> [!div class="mx-imgBorder"]
> ![在 Azure 门户中查看对学徒模式学习行为的评估的屏幕截图](media/settings/evaluate-apprentice-mode.png)

学徒模式提供以下评估指标：
* 基线 - 平均奖励：应用程序默认（基线）的平均奖励。
* 个性化体验创建服务 - 平均奖励：个性化体验创建服务可能已达到的总奖励的平均奖励。
* 最近 1000 个事件的奖励成就比：基线和个性化体验创建服务奖励的比率 - 在最近 1000 个事件中进行规范化。

## <a name="switch-behavior-to-online-mode"></a>将行为切换到联机模式

确定个性化体验创建服务已使用 75-85% 移动平均的平均值训练时，该模型已准备好切换到联机模式。

在个性化体验创建服务资源的 Azure 门户中，在“配置”页的“学习行为”选项卡上，选择“返回最佳操作”，然后选择“保存”。

不需要对排名和奖励 API 调用进行任何更改。

## <a name="next-steps"></a>后续步骤

* [管理模型和学习设置](how-to-manage-model.md)
