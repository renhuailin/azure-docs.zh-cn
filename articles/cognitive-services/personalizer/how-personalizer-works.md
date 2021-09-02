---
title: 个性化体验创建服务的工作原理 - 个性化体验创建服务
description: 个性化体验创建服务循环使用机器学习来生成模型，以预测针对你的内容的最常见操作。 该模型是专门使用通过排名和奖励调用发送的数据训练的。
author: jeffmend
ms.author: jeffme
ms.manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 9c9d9f2b06a34feca2974bf97fb5d494c6105471
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122829926"
---
# <a name="how-personalizer-works"></a>个性化体验创建服务的工作原理

个性化体验创建服务资源，即你的学习循环，使用机器学习来生成模型，以预测针对你的内容的最常见操作。 该模型是专门使用通过排名和奖励调用发送的数据训练的。 各个循环彼此完全独立。

## <a name="rank-and-reward-apis-impact-the-model"></a>排名和奖励 API 影响模型

向排名 API 发送包含特征的操作和上下文特征。 **排名** API 将决定使用以下操作之一：

* 攻击：当前模型根据以往的数据确定最佳操作。
* 探索：选择不同的操作而不是最相关的操作。 在 Azure 门户中为个性化体验创建服务资源[配置此百分比](how-to-settings.md#configure-exploration-to-allow-the-learning-loop-to-adapt)。

确定奖励分数并将该分数发送到奖励 API。 **奖励** API：

* 通过记录每个排名调用的特征和奖励评分来收集用于训练模型的数据。
* 根据学习策略中指定的配置，使用该数据更新模型。

## <a name="your-system-calling-personalizer"></a>系统调用个性化体验创建服务

下图显示了排名和奖励调用的体系结构流程图：

![替换文字](./media/how-personalizer-works/personalization-how-it-works.png "个性化设置的工作原理")

1. 向排名 API 发送包含特征的操作和上下文特征。

    * 个性化体验创建服务会确定是要利用当前模型，还是探索模型的新选项。
    * 排名结果将发送到事件中心。
1. 最高排名将作为奖励操作 ID 返回到系统。
    你的系统会呈现该内容，并会根据你自己的业务规则确定奖励分数。
1. 你的系统将奖励分数返回给学习循环。
    * 当个性化体验创建服务收到奖励时，会将奖励发送到事件中心。
    * 排名和奖励相关联。
    * 根据关联结果更新 AI 模型。
    * 使用新模型更新推理引擎。

## <a name="personalizer-retrains-your-model"></a>个性化体验创建服务重新训练模型

在 Azure 门户的个性化体验创建服务资源中，个性化体验创建服务根据“模型频率更新”设置重新训练模型。

在 Azure 门户的个性化体验创建服务资源中，个性化体验创建服务根据“数据保留”设置中的天数使用当前保留的所有数据。

## <a name="research-behind-personalizer"></a>个性化体验创建服务幕后的研究

个性化体验创建服务基于[强化学习](concepts-reinforcement-learning.md)领域的前沿科研成果，包括 Microsoft 研究部门的论文、研究活动和持续探索。

## <a name="next-steps"></a>后续步骤

了解个性化体验创建服务的[热门方案](where-can-you-use-personalizer.md)