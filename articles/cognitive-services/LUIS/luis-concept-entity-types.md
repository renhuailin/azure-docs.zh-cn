---
title: 实体类型 - LUIS
description: 实体可以在预测运行时从用户言语中提取数据。 一个可选的辅助用途是通过用作特征的实体促进意向或其他实体的预测。
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/26/2021
ms.openlocfilehash: 7eb4affb75816789dca094f9ec6e2ae19a222285
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128582374"
---
# <a name="entities-in-luis"></a>LUIS 中的实体

实体是与用户意向相关的项目或元素。 实体定义可以从言语中提取的数据，对于完成用户的必需操作至关重要。 例如：

|话语|预测的意向|提取的实体|说明|
|--|--|--|--|
|嗨，你好吗？|问候语|-|没有要提取的内容。|
|I want to order a small pizza|orderPizza| “small” | “Size”实体提取为“small”。|
|Turn off bedroom light|turnOff| “bedroom” | “Room”实体提取为“bedroom”。|
|Check balance in my savings account ending in 4406|checkBalance| “savings”、“4406” | “accountType”实体提取为“savings”，“accountNumber”实体提取为“4406”。|
|购买 3 张到纽约的机票|buyTickets| “3”、“New York” | “ticketsCount”实体提取为“3”，“Destination”实体提取为“New York”。|

实体是可选的（但建议使用）。 无需在应用中为每个概念都创建实体，只需为以下情况创建实体：

* 客户端应用程序需要数据，或 
* 实体作为另一个实体或意向的提示或信号。 若要了解有关作为特征的实体的详细信息，请参阅[作为特征的实体](#entities-as-features)。

## <a name="entity-types"></a>实体类型

若要创建实体，必须为其提供名称和类型。 LUIS 中有多种类型的实体。 

## <a name="list-entity"></a>列表实体

列表实体表示一组固定、封闭的相关字词及其同义词。 可以使用列表实体识别多个同义词或变体，并为它们提取规范化输出。 使用“建议”选项根据当前列表查看有关新词的建议。 

列表实体不是机器学习型，这意味着 LUIS 不会为列表实体发现更多值。 LUIS 将任何列表中某个项的任何匹配项标记为响应中的实体。

列表实体中的匹配区分大小写，并且必须是要提取的精确匹配项。 匹配列表实体时也使用规范化值。 例如：

|规范化值|同义词|
|--|--|
|小型|sm、sml、tiny、smallest|
|中型|md、mdm、regular、average、middle|
|大型|lg、lrg、big|

有关详细信息，请参阅[列表实体参考文章](reference-entity-list.md)。

## <a name="regex-entity"></a>正则表达式实体

正则表达式实体基于所提供的正则表达式模式提取实体。 不区分大小写，并忽略区域性变体。 正则表达式最适用于结构化文本或预计采用特定格式的预定义字母数字值序列。 例如：

|实体|正则表达式|示例|
|--|--|--|
|航班号|flight [A-Z]{2} [0-9]{4}| flight AS 1234|
|信用卡号|[0-9]{16}|5478789865437632|

有关详细信息，请参阅[正则表达式实体参考文章](reference-entity-regular-expression.md)。

## <a name="prebuilt-entity"></a>预生成实体

LUIS 提供了一组预生成实体，用于识别名称、日期、数字和货币等常见类型的数据。  预生成实体的行为是固定的。 预生成实体支持因 LUIS 应用的区域性而异。 例如：

|预生成实体|示例值|
|--|--|
|PersonName|James、Bill、Tom|
|DatetimeV2|2019-05-02、May 2nd、8am on may 2nd 2019|

有关详细信息，请参阅[预生成实体参考文章](./luis-reference-prebuilt-entities.md)。

## <a name="patternany-entity"></a>Pattern.Any 实体

pattern.Any 实体是一种长度可变的占位符，仅在模式的模板言语中使用，用于标记实体的起始和结束位置。 它遵循特定规则或模式，最适合用于具有固定词法结构的语句。 例如：

|示例陈述|模式|实体|
|--|--|--|
|Can I have a burger please?|Can I have a {meal} [please][?]| burger
|Can I have a pizza?|Can I have a {meal} [please][?]| pizza
|Where can I find The Great Gatsby?|Where can I find {bookName}?| The Great Gatsby|

有关详细信息，请参阅 [Pattern.Any 实体参考文章](./reference-entity-pattern-any.md)。

## <a name="machine-learned-ml-entity"></a>机器学习 (ML) 实体

机器学习实体使用上下文根据标记的示例提取实体。 它是用于生成 LUIS 应用程序的首选实体。 它依赖于机器学习算法，需要进行标记以对应用程序进行定制。 使用 ML 实体识别并非始终格式正确但具有相同意义的数据。 

|示例陈述|提取的 product 实体|
|--|--|
|I want to buy a book.|“book”|
|Can I get these shoes please?|“shoes”|
|Add those shorts to my basket.|“shorts”|

可在[此处](./reference-entity-machine-learned-entity.md)了解有关机器学习实体的详细信息。

有关详细信息，请参阅[机器学习实体](./reference-entity-machine-learned-entity.md)参考文章。

#### <a name="ml-entity-with-structure"></a>具有结构的 ML 实体

ML 实体可以由较小子实体组成，每个子实体都可以具有自己的属性。 例如，“地址”可能具有以下结构：

* 地址：4567 Main Street, NY, 98052, USA
    * 建筑物编号：4567
    * 街道名称：Main Street
    * 州：NY
    * 邮政编码：98052
    * 国家/地区：USA


## <a name="building-effective-ml-entities"></a>生成有效 ML 实体

若要有效生机器学习实体，请遵循以下最佳做法：

* 如果有包含子实体的机器学习实体，请确保实体和子实体的不同顺序和变体显示在标记的言语中。 标记的示例言语应包括所有有效的形式，并包括显示的、缺失的以及在言语中重新排序的实体。

* 避免将实体过度拟合到极固定的集。 当模型未充分通用化时，会发生过度拟合，这是机器学习模型中的常见问题。 这意味着应用并非很适合新类型的示例。 因此，你应该使标记的示例言语多样化，从而使应用可以在你提供的有限示例之外通用化。

* 标记应在意向之间应保持一致。 这甚至包括你在 None 意向中提供的包含此实体的言语。 否则，模型将无法有效地确定序列。

## <a name="entities-as-features"></a>作为特征的实体

实体的另一种重要功能是将它们用作特征或区分其他意向或实体的特征，以便系统可以通过它们进行观察和学习。

## <a name="entities-as-features-for-intents"></a>作为意向特征的实体

可以使用实体作为意向的信号。 例如，言语中特定实体的存在情况可以区分其所属的意向。

|示例陈述|实体|Intent|
|--|--|--|
|Book me a fight to New York.|城市|预定机票|
|Book me the main conference room.|会议室|保留房间|

## <a name="entities-as-feature-for-entities"></a>作为实体特征的实体

还可以将实体用作其他实体存在情况的指示器。 这种情况的一个常见示例是使用预生成实体作为其他 ML 实体的功能。
如果你在构建航班预定系统，而言语类似于“Book me a flight from Cairo to Seattle”，则会将“出发城市”和“目标城市”作为 ML 实体 。 一种良好做法是使用预生成 `GeographyV2` 实体作为这两个实体的特征。

有关详细信息，请参阅 [GeographyV2 实体参考文章](./luis-reference-prebuilt-geographyv2.md)。

还可以将实体用作其他实体的必需特征。 这可帮助解析提取的实体。 例如，如果你在创建一个披萨订购应用程序，并且有一个 `Size` ML 实体，则可以创建 `SizeList` 列表实体并将它用作 `Size` 实体的必需特征。 应用程序会以从言语提取的实体的形式返回规范化值。 

请参阅[特征](luis-concept-feature.md)以了解详细信息，并参阅[预生成实体](./luis-reference-prebuilt-entities.md)以了解有关区域性中提供的预生成实体解析的详细信息。 


## <a name="entity-prediction-status-and-errors"></a>实体预测状态和错误

当实体的实体预测不同于你为示例言语标记的实体时，LUIS 门户会显示以下内容。 这种不同的评分是根据当前已训练的模型给出的。 

:::image type="content" source="./media/luis-concept-entities/portal-entity-prediction-error.png" alt-text="当实体的实体预测不同于你为示例言语选择的实体时，LUIS 门户会显示此状态":::

导致错误的文本将在示例言语中突出显示，示例言语行的右侧有一个显示为红色三角形的错误指示符。 

若要解决实体错误，请尝试下面的一个或多个步骤：

* 突出显示的文本进行了错误的标记。 若要解决此问题，请查看标签，进行更正，然后重新训练应用。 
* 为实体创建一个有助于确定实体概念的[特征](luis-concept-feature.md)。
* 添加更多[示例言语](luis-concept-utterance.md)并使用实体进行标记。
* 对于在预测终结点上收到的任何言语，[查看有效的学习建议](luis-concept-review-endpoint-utterances.md)，以便确定实体的概念。


## <a name="next-steps"></a>后续步骤

* 了解良好的示例[言语](luis-concept-utterance.md)。
* 请参阅[添加实体](luis-how-to-add-entities.md)，详细了解如何将实体添加到 LUIS 应用。
* 了解有关 LUIS [应用程序限制](./luis-limits.md)的详细信息。 
* 阅读[教程](tutorial-machine-learned-entity.md)以了解如何使用机器学习实体从言语中提取结构化数据。
