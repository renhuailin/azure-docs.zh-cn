---
title: 如何执行批处理测试 - LUIS
titleSuffix: Azure Cognitive Services
description: 使用语言理解 (LUIS) 批处理测试集来查找意向和实体不正确的话语。
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/18/2021
ms.openlocfilehash: b6d007d3020d3fa2b001178bd23d721ea6a6aa6b
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110067827"
---
# <a name="batch-testing-with-a-set-of-example-utterances"></a>使用一组示例话语进行批处理测试

批处理测试会验证活动训练版本，以判断其预测准确性。 批量测试可帮助你查看活动版本中每个意向和实体的准确性。 查看批处理测试结果以采取适当的措施来提高准确性，例如，如果应用经常无法识别正确的意向或在话语中标记实体，则向意向添加更多示例话语。

## <a name="group-data-for-batch-test"></a>批处理测试的组数据

对于 LUIS 来说，用于批处理测试的表达必须是全新，这一点很重要。 如果有话语数据集，请将话语划分为三个集：添加到意向的示例话语、从已发布的终结点接收的话语，以及在训练 LUIS 后用于对其进行批处理测试的话语。

你使用的批处理 JSON 文件中的言语应包含机器学习的带有标签的顶级实体（包括开始和结束位置）。 言语不应是已包含在应用中的示例的一部分。 它们应该是你要在其中积极预测意向和实体的言语。

可以按意向和/或实体划分测试，或者将所有测试（最多 1000 个言语）包含在同一文件中。 

### <a name="common-errors-importing-a-batch"></a>导入批处理文件的常见错误

如果在将批处理文件上传到 LUIS 时遇到错误，请检查是否存在以下常见问题：

* 批处理文件中的言语多于 1,000 条
* 不具有实体属性的话语 JSON 对象。 此属性可以是空数组。
* 在多个实体中标记的字词
* 实体标签以空格开头或结尾。

## <a name="fixing-batch-errors"></a>修复批处理错误

如果在批处理测试中出现错误，可以向意向添加更多表达，和/或在实体中标记更多表达，以帮助 LUIS 在意向间进行区分。 如果你已添加了表达，且对其进行了标记，但在批处理测试中仍收到预测错误，请考虑添加[短语列表](luis-concept-feature.md)功能，其中包含特定于域的词汇，以帮助 LUIS 更快地理解。


<a name="batch-testing"></a>

## <a name="batch-testing-using-the-luis-portal"></a>使用 LUIS 门户进行批量测试 

### <a name="import-and-train-an-example-app"></a>导入并训练示例应用

导入提取披萨订单的应用，例如 `1 pepperoni pizza on thin crust`。

1.  下载并保存[应用 JSON 文件](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-with-machine-learned-entity.json?raw=true)。

1. 登录到 [LUIS 门户](https://www.luis.ai)，选择“订阅”和“创作资源”以查看分配给该创作资源的应用。
1. 选择“新建应用”旁边的箭头，并单击“以 JSON 格式导入”，以便将 JSON 导入到一个新应用。 将应用命名为 `Pizza app`。


1. 选择导航栏右上角的“训练”以训练该应用。


[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

### <a name="batch-test-file"></a>批量测试文件

示例 JSON 包含一个言语（该言语包含一个带标签的实体）用于演示测试文件的外观。 你自己的测试中应该包含多个言语，这些言语标记了正确的意向和机器学习实体。

1. 在文本编辑器中创建 `pizza-with-machine-learned-entity-test.json` 或[下载](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/batch-tests/pizza-with-machine-learned-entity-test.json?raw=true)它。

2. 在 JSON 格式的批处理文件中，添加要在测试中预测的言语和 **意向**。

   [!code-json[Add the intents to the batch test file](~/samples-cognitive-services-data-files/luis/batch-tests/pizza-with-machine-learned-entity-test.json "Add the intent to the batch test file")]

## <a name="run-the-batch"></a>运行批处理

1. 选择顶部导航栏的“测试”。

2. 选择右侧面板中的“批处理测试面板”。

    ![批处理测试链接](./media/luis-how-to-batch-test/batch-testing-link.png)

3. 选择“导入”。 在出现的对话框中，选择“选择文件”并找到具有正确 JSON 格式的 JSON 文件，该文件包含不超过 1,000 条待测试的言语。

    浏览器顶部的红色通知栏中将报告导入错误。 导入出现错误时，不会创建任何数据集。 有关详细信息，请参阅[常见错误](#common-errors-importing-a-batch)。

4. 选择 `pizza-with-machine-learned-entity-test.json` 文件的文件位置。

5. 命名数据集 `pizza test`，然后选择“完成”。

6. 选择“运行”按钮。 

7. 批处理测试完成后，可以看到以下各列：

    | 列 | 说明 |
    | -------- | ------------- |
    | 状态 | 测试状态。 “查看结果”仅在测试完成后可见。 |
    | 名称 | 为测试指定的名称。 |
    | 大小 | 此批处理测试文件中的测试数。 |
    | 上次运行时间 | 此批处理测试文件的上次运行日期。 |
    | 最后结果 | 测试中成功的预测数。 |

8. 若要查看测试的详细结果，请选择“查看结果”。 

    > [!TIP]
    > * 选择“下载”会下载你上传的同一文件。
    > * 如果你看到批量测试失败，则至少一个言语意向与预测不匹配。

<a name="access-batch-test-result-details-in-a-visualized-view"></a>

### <a name="review-batch-results-for-intents"></a>查看意向的批处理结果

若要查看批处理测试结果，请选择“查看结果”。 测试结果以图形显示如何针对活动版本预测测试言语。

批处理图表将结果显示在四个象限中。 在图表右侧是一个筛选器。 筛选器包含意向和实体。 选择[图表的一个部分](#review-batch-results-for-intents)或图表中的一个点时，关联的话语显示在图表下方。

鼠标悬停在图表上时，鼠标滚轮可以放大或缩小图表中的显示。 当图表上有许多点紧密地聚集在一起时，这是非常有用的。

图表分为四个象限，其中两个部分以红色显示。

1. 在筛选器列表中选择“ModifyOrder”意向。 言语预测为“漏报”，这意味着，该言语已成功匹配其在批处理文件中列出的正面预测结果。

    > [!div class="mx-imgBorder"]
    > ![言语已成功匹配其正面预测结果](./media/luis-tutorial-batch-testing/intent-predicted-true-positive.png)

    筛选器列表中的绿色勾选标记也指示每个意向的测试成功。 所有其他意向列出了 1/1 正面评分，因为言语是针对每个意向测试的，而任何意向的负面测试不会列在批处理测试中。

1. 选择“Confirmation”意向。 此意向未在批处理测试中列出，因此，这是批处理测试中列出的言语的负面测试。

    > [!div class="mx-imgBorder"]
    > ![针对批处理文件中未列出的意向成功负面预测了言语](./media/luis-tutorial-batch-testing/true-negative-intent.png)

    根据筛选器和网格中的绿色文本所示，负面测试成功。

### <a name="review-batch-test-results-for-entities"></a>查看实体的批处理测试结果

ModifyOrder 实体（包含子实体的机器实体）显示顶级实体是否匹配，并显示子实体是如何预测的。

1. 在筛选器列表中选择“ModifyOrder”实体，然后选择网格中的圆圈。

1. 实体预测结果显示在图表下方。 显示的内容包括符合预期的预测对应的实线，以及不符合预期的预测对应的虚线。

    > [!div class="mx-imgBorder"]
    > ![已成功预测批处理文件中的实体父级](./media/luis-tutorial-batch-testing/labeled-entity-prediction.png)

<a name="filter-chart-results-by-intent-or-entity"></a>

#### <a name="filter-chart-results"></a>筛选图表结果

若要按特定意向或实体筛选图表，请在右侧筛选面板中选择意向或实体。 图中的数据点及其分布会根据不同的选择而相应更新。

![可视化的批处理测试结果](./media/luis-how-to-batch-test/filter-by-entity.png)

### <a name="chart-result-examples"></a>图表结果示例

对于 LUIS 门户中的图表，你可以执行以下操作：
 
#### <a name="view-single-point-utterance-data"></a>查看单点陈述数据

在图表中，将鼠标悬停在某个数据点上可查看其预测结果的确定性分数。 选择数据点可在页面底部的陈述列表中检索出相应的陈述。

![所选的陈述](./media/luis-how-to-batch-test/selected-utterance.png)


<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>

#### <a name="view-section-data"></a>查看分区数据

在此四分区图表中，选择分区名称，例如在图表右上角的“误报”。 该分区中的所有陈述都会显示在该图表下方的列表中。

![按照分区所选的陈述](./media/luis-how-to-batch-test/selected-utterances-by-section.png)

在上图中，陈述 `switch on` 被标记为 TurnAllOn 意向，但接收的预测意向为 None。 这表示 TurnAllOn 意向需要更多示例陈述才能做出预期预测。

该图表中红色的两个分区表示与预期预测不匹配的陈述。 它们表示需要更多 LUIS 训练的陈述。

该图表中绿色的两个分区与预期预测相匹配。

## <a name="batch-testing-using-the-rest-api"></a>使用 REST API 进行批量测试 

LUIS 允许你使用 LUIS 门户和 REST API 进行批量测试。 下面列出了 REST API 的终结点。 有关使用 LUIS 门户进行批量测试的信息，请参阅[教程：批量测试数据集]()。 请使用下面的完整 URL（将占位符值替换为你自己的 LUIS 预测密钥和终结点）。 

不要忘记将 LUIS 键添加到标头中的 `Ocp-Apim-Subscription-Key` 并将 `Content-Type` 设置为 `application/json`。

### <a name="start-a-batch-test"></a>启动批量测试

使用应用版本 ID 或发布槽启动批量测试。 将 POST 请求发送到以下终结点格式之一。 在请求正文中包括你的批处理文件。

发布槽
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/slots/<YOUR-SLOT-NAME>/evaluations`

应用版本 ID
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/versions/<YOUR-APP-VERSION-ID>/evaluations`

这些终结点会返回一个操作 ID，你将使用它来检查状态并获取结果。 


### <a name="get-the-status-of-an-ongoing-batch-test"></a>获取正在进行的批量测试的状态

使用你启动的批量测试中的操作 ID 通过以下终结点格式获取其状态： 

发布槽
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/slots/<YOUR-SLOT-ID>/evaluations/<YOUR-OPERATION-ID>/status`

应用版本 ID
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/versions/<YOUR-APP-VERSION-ID>/evaluations/<YOUR-OPERATION-ID>/status`

### <a name="get-the-results-from-a-batch-test"></a>获取批量测试的结果

使用你启动的批量测试中的操作 ID 通过以下终结点格式获取其结果： 

发布槽
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/slots/<YOUR-SLOT-ID>/evaluations/<YOUR-OPERATION-ID>/result`

应用版本 ID
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/versions/<YOUR-APP-VERSION-ID>/evaluations/<YOUR-OPERATION-ID>/result`


### <a name="batch-file-of-utterances"></a>言语的批处理文件

提交话语批处理文件（称为数据集  ），以用于批处理测试。 该数据集是一个 JSON 格式的文件，包含最多 1,000 条带标签的言语。 可以在一个应用中测试最多 10 个数据集。 如果需要测试更多数据集，请删除数据集，然后添加新数据集。 即使批处理文件数据中没有对应的实体，模型中的所有自定义实体也会出现在批处理测试实体筛选器中。

批处理文件包含表达。 每个言语都必须有预期的意向预测，此外还必须有你预期可以检测到的[机器学习实体](luis-concept-entity-types.md#machine-learned-ml-entity)。

### <a name="batch-syntax-template-for-intents-with-entities"></a>使用实体的意向的批处理语法模板

使用以下模板启动批处理文件：

```JSON
{
    "LabeledTestSetUtterances": [
        {
            "text": "play a song",
            "intent": "play_music",
            "entities": [
                {
                    "entity": "song_parent",
                    "startPos": 0,
                    "endPos": 15,
                    "children": [
                        {
                            "entity": "pre_song",
                            "startPos": 0,
                            "endPos": 3
                        },
                        {
                            "entity": "song_info",
                            "startPos": 5,
                            "endPos": 15
                        }
                    ]
                }
            ]
        }
    ]
}

```

批处理文件使用 startPos 和 endPos 属性来记录实体的开始和结束   。 值从零开始，不得以空格开始或结束。 这与使用 startIndex 和 endIndex 属性的查询日志不同。

如果不想测试实体，请包含 `entities` 属性并将值设置为空数组 `[]`。

### <a name="rest-api-batch-test-results"></a>REST API 批量测试结果

下面是 API 返回的几个对象：

* 有关意向和实体模型的信息，例如精准率、召回率和 F 分数。
* 有关实体模型的信息，例如每个实体的精准率、召回率和 F 分数 
  * 使用 `verbose` 标志，你可以获取有关实体的详细信息，例如 `entityTextFScore` 和 `entityTypeFScore`。
* 提供的言语以及预测的和标记的意向名称
* 误报的实体的列表，和漏报的实体的列表。

## <a name="next-steps"></a>后续步骤

如果测试表明 LUIS 应用未正确识别意向和实体，则可以通过标记更多陈述或添加功能来提高 LUIS 应用的性能。

* [使用 LUIS 标记建议的陈述](luis-how-to-review-endpoint-utterances.md)
* [使用相关功能来改进 LUIS 应用的性能](luis-how-to-add-features.md)
