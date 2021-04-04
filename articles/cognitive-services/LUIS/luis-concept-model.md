---
title: 使用模型进行设计 - LUIS
description: 语言理解提供多种类型的模型。 某些模型可以通过多种方式使用。
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: 576ba945018d13db9cd24888f3c41a2215857694
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "91316505"
---
# <a name="design-with-intent-and-entity-models"></a>使用意向和实体模型进行设计

语言理解提供了两种类型的模型，用于定义应用架构。 应用架构决定了从新用户言语预测获得的信息。

应用架构是根据使用[机器教学](#authoring-uses-machine-teaching)创建的模型构建的：
* [意向](#intents-classify-utterances)会将用户言语分类
* [实体](#entities-extract-data)会从言语中提取数据

## <a name="authoring-uses-machine-teaching"></a>Authoring 使用机器教学

使用 LUIS 的机器教学方法，可以轻松地将概念传授给计算机。 了解机器学习不是使用 LUIS 所必需的。 不过，可以由你作为教师通过提供概念的示例并说明如何使用其他相关概念为某个概念建模，将概念传达给 LUIS。 你作为教师，还可以通过识别和修复预测错误，以交互方式改进 LUIS 的模型。

<a name="v3-authoring-model-decomposition"></a>

## <a name="intents-classify-utterances"></a>使用意向对言语进行分类

意向将示例言语分类，以便向 LUIS 传授意向。 意向中的示例言语用作言语的积极示例。 相同的这些言语用作所有其他意向中的消极示例。

假设某个应用需要确定用户的预订意向，另一个应用需要客户的交货地址。 此应用具有两个意向：`OrderBook` 和 `ShippingLocation`。

以下言语是 `OrderBook` 意向的 **积极示例**，以及 `ShippingLocation` 和 `None` 意向的 **消极示例**：

`Buy the top-rated book on bot architecture.`

## <a name="entities-extract-data"></a>使用实体提取数据

实体表示要从言语中提取的数据单位。 机器学习实体是包含子实体（也是机器学习实体）的顶级实体。

机票预订就是机器学习实体的一个例子。 从概念上讲，机票预测是包含许多较小数据单位（例如日期、时间、座位数、座位类型（头等舱或经济舱）、出发地、目的地和餐饮选项）的单笔交易。

## <a name="intents-versus-entities"></a>意向与实体

意向是整个言语的所需结果，而实体是从言语中提取的数据片段。 意向通常与客户端应用程序应采取的操作相关联。 实体是执行此操作所需的信息。 从编程的角度讲，意向会触发方法调用，而实体将用作该方法调用的参数。

以下言语肯定包含意向，同时可能包含实体：

`Buy an airline ticket from Seattle to Cairo`

以下言语包含单个意向：

* 购买机票

以下言语可能包含多个实体：

* Seattle（出发地）和 Cairo（目的地）的地点
* 数量为一张机票

## <a name="entity-model-decomposition"></a>实体模型分解

LUIS 支持使用创作 API 进行的模型分解，可将概念分解成较小的组成部分。 这样，你便可以生成自己的模型，并有把握地构造和预测各个组成部分。

模型分解包括以下组成部分：

* [意向](#intents-classify-utterances)
    * [功能](#features)
* [机器学习实体](reference-entity-machine-learned-entity.md)
    * 子实体（也是机器学习实体）
        * [功能](#features)
            * [短语列表](luis-concept-feature.md)
            * [非计算机学习实体](luis-concept-feature.md)，例如[正则表达式](reference-entity-regular-expression.md)、[列表](reference-entity-list.md)和[预生成实体](luis-reference-prebuilt-entities.md)

<a name="entities-extract-data"></a>
<a name="machine-learned-entities"></a>

## <a name="features"></a>功能

[特征](luis-concept-feature.md)是系统观察到的数据的特征或特有属性。 机器学习特征为 LUIS 提供了重要提示，指示在何处查找可判别概念的项。 它们是 LUIS 可以使用的提示，但并不是硬性规则。 这些提示将与标签结合使用来查找数据。

## <a name="patterns"></a>模式

[模式](luis-concept-patterns.md)旨在多条言语非常类似的情况下提升准确性。 使用模式可在不提供更多话语的情况下获得更高的意向准确度。

## <a name="extending-the-app-at-runtime"></a>在运行时扩展应用

应用的架构（模型和功能）经过训练后将发布到预测终结点。 可以将[新信息](schema-change-prediction-runtime.md)以及用户的言语传递到预测终结点以增强预测。

## <a name="next-steps"></a>后续步骤

* 了解[意向](luis-concept-intent.md)和[实体](luis-concept-entity-types.md)。
* 详细了解[特征](luis-concept-feature.md)