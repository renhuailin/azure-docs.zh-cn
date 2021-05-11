---
title: 快速入门：在 LUIS 门户中生成应用
description: 本快速入门演示如何创建使用预生成域 `HomeAutomation` 打开和关闭灯光和设备的 LUIS 应用。 此预生成的域为你提供意向、实体和示例话语。 完成本教程后，你会有一个在云中运行的 LUIS 终结点。
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 04/14/2021
ms.openlocfilehash: 3de83151aa00d589c470eb7ac772f4c9b5f7eda2
ms.sourcegitcommit: ad921e1cde8fb973f39c31d0b3f7f3c77495600f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/25/2021
ms.locfileid: "107948296"
---
# <a name="quickstart-build-your-app-in-luis-portal"></a>快速入门：在 LUIS 门户中生成应用

在本快速入门中，请使用预生成的主要自动化域创建一个 LUIS 应用来打开和关闭灯和设备。 此预生成的域为你提供意向、实体和示例话语。 接下来，请尝试添加更多意向和实体来自定义应用。 完成本教程后，你会有一个在云中运行的 LUIS 终结点。

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

[!INCLUDE [Select authoring resource](./includes/select-authoring-resource.md)]

## <a name="create-a-new-app"></a>创建新应用

可在“我的应用”中创建和管理应用程序。

### <a name="create-an-application"></a>创建应用程序

若要创建应用程序，请单击“+ 新建应用”。 

在显示的窗口中，输入以下信息：

|名称  |说明  |
|---------|---------|
|名称     | 应用的名称， 例如“home automation”。        |
|环境     | 应用能够理解和显示的语言。   |
|说明 | 应用的说明。
|预测资源 | 将接收查询的预测资源。 |

选择“完成”。

>[!NOTE]
>创建应用程序后将无法更改区域性。

## <a name="add-prebuilt-domain"></a>添加预生成域

LUIS 提供一组预生成的域，它们可帮助你开始使用应用程序。 预生成的域应用已填充有[意向](./luis-concept-intent.md)、[实体](./luis-concept-entity-types.md)和[言语](./luis-concept-utterance.md)。

1. 在左侧导航栏中选择“预生成域”。
2. 搜索“HomeAutomation”。
3. 在 HomeAutomation 卡上选择“添加域”。

    > [!div class="mx-imgBorder"]
    > ![选择“预生成域”，然后搜索“HomeAutomation”。 在 HomeAutomation 卡上选择“添加域”。](media/luis-quickstart-new-app/home-automation.png)

    成功添加域以后，预生成域框会显示“删除域”按钮。

## <a name="check-out-intents-and-entities&quot;></a>查看意向和实体

1. 在左侧导航菜单中选择“意向”，以便查看 HomeAutomation 域意向。 它具有示例言语，例如 `HomeAutomation.QueryState` 和 `HomeAutomation.SetDevice`。

    > [!NOTE]
    > “无”是由所有 LUIS 应用提供的意向。 可以使用它来处理与应用提供的功能无法对应的话语。

2. 选择 **HomeAutomation.TurnOff** 意向。 此意向包含一系列使用实体标记的示例言语。

    > [!div class=&quot;mx-imgBorder&quot;]
    > [![HomeAutomation.TurnOff 意向的屏幕截图](media/luis-quickstart-new-app/home-automation-turnoff.png &quot;HomeAutomation.TurnOff 意向的屏幕截图")](media/luis-quickstart-new-app/home-automation-turnoff.png)

3. 如果你想要查看应用的实体，请选择“实体”。 如果单击某个实体（例如 HomeAutomation.DeviceName），你将看到与之关联的值的列表。 
 
    :::image type="content" source="media/luis-quickstart-new-app/entities-page.png" alt-text="图像替换文字" lightbox="media/luis-quickstart-new-app/entities-page.png":::

## <a name="train-the-luis-app"></a>训练 LUIS 应用
用意向、实体和言语填充应用程序后，需要对应用程序进行训练，以便能够反映出你所作的更改。

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="test-your-app"></a>测试应用

训练完应用以后，即可测试它。

1. 在右上方导航中选择“测试”。

1. 在“交互式测试”窗格中键入测试言语，然后按 Enter。 例如，“关闭照明设备”。

    在本例中，“关灯”被正确标识为 HomeAutomation.TurnOff 的评分最高的意向。

    :::image type="content" source="media/luis-quickstart-new-app/review-test-inspection-pane-in-portal.png" alt-text="“测试”面板的屏幕截图，其中突出显示了话语" lightbox="media/luis-quickstart-new-app/review-test-inspection-pane-in-portal.png":::

1. 选择“检查”以查看有关预测的详细信息。

    :::image type="content" source="media/luis-quickstart-new-app/test.png" alt-text="包含检查信息的“测试”面板的屏幕截图" lightbox="media/luis-quickstart-new-app/test.png":::

1. 关闭“测试”窗格。

## <a name="customize-your-application"></a>自定义应用程序

除了预生成的域，还可使用 LUIS 创建你自己的自定义应用程序，或者在预生成的域之上进行自定义。

### <a name="create-intents"></a>创建意向

向应用添加更多意向

1. 在左侧导航菜单中选择“意向”。
2. 选择“创建”
3. 输入意向名称 `HomeAutomation.AddDeviceAlias`，然后选择“完成”。

### <a name="create-entities"></a>创建实体

向应用添加更多实体

1. 在左侧导航菜单中选择“实体”。
2. 选择“创建”
3. 输入意向名称 `HomeAutomation.DeviceAlias`，从“类型”中选择机器学习，然后选择“创建” 。

### <a name="add-example-utterances"></a>添加示例话语

示例话语是用户在聊天机器人或其他客户端应用程序中输入的文本。 它们可将用户文本的意向映射到 LUIS 意向。

在 `HomeAutomation.AddDeviceAlias` 的“意向”页上，请在“示例言语”下添加以下示例言语 ，

|#|示例陈述|
|--|--|
|1|`Add alias to my fan to be wind machine`|
|2|`Alias lights to illumination`|
|3|`nickname living room speakers to our speakers a new fan`|
|4|`rename living room tv to main tv`|

[!INCLUDE [best-practice-utterances](./includes/best-practice-utterances.md)]


### <a name="label-example-utterances"></a>标签示例言语

你添加了 ML 实体，因此需要标记言语。 应用程序使用标签来学习如何提取你创建的 ML 实体。

[!INCLUDE [how-to-label](./includes/how-to-label.md)]

## <a name="create-prediction-resource"></a>创建预测资源
目前，你完成了应用程序的创建。 你需要创建一个预测资源来发布应用程序，从而通过预测终结点在聊天机器人或其他客户端应用程序中接收预测结果

通过 LUIS 门户创建预测资源

[!INCLUDE [add-pred-resource-portal](./includes/add-prediction-resource-portal.md)]


## <a name="publish-the-app-to-get-the-endpoint-url"></a>发布应用以获取终结点 URL

[!INCLUDE [LUIS How to Publish steps](./includes/howto-publish.md)]



## <a name="query-the-v3-api-prediction-endpoint"></a>查询 V3 API 预测终结点

[!INCLUDE [LUIS How to get endpoint first step](./includes/v3-prediction-endpoint.md)]

2. 在浏览器地址栏中，请确保查询字符串的以下值采用 URL 格式。 如果它们不在查询字符串中，请添加它们：

    * `verbose=true`
    * `show-all-intents=true`

3. 在浏览器地址栏中，转到 URL 末尾，对查询字符串输入“关闭客厅灯光”，然后按 Enter。

    ```json
    {
        "query": "turn off the living room light",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOff",
            "intents": {
                "HomeAutomation.TurnOff": {
                    "score": 0.969448864
                },
                "HomeAutomation.QueryState": {
                    "score": 0.0122336326
                },
                "HomeAutomation.TurnUp": {
                    "score": 0.006547436
                },
                "HomeAutomation.TurnDown": {
                    "score": 0.0050634006
                },
                "HomeAutomation.SetDevice": {
                    "score": 0.004951761
                },
                "HomeAutomation.TurnOn": {
                    "score": 0.00312553928
                },
                "None": {
                    "score": 0.000552945654
                }
            },
            "entities": {
                "HomeAutomation.Location": [
                    "living room"
                ],
                "HomeAutomation.DeviceName": [
                    [
                        "living room light"
                    ]
                ],
                "HomeAutomation.DeviceType": [
                    [
                        "light"
                    ]
                ],
                "$instance": {
                    "HomeAutomation.Location": [
                        {
                            "type": "HomeAutomation.Location",
                            "text": "living room",
                            "startIndex": 13,
                            "length": 11,
                            "score": 0.902181149,
                            "modelTypeId": 1,
                            "modelType": "Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "HomeAutomation.DeviceName": [
                        {
                            "type": "HomeAutomation.DeviceName",
                            "text": "living room light",
                            "startIndex": 13,
                            "length": 17,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "HomeAutomation.DeviceType": [
                        {
                            "type": "HomeAutomation.DeviceType",
                            "text": "light",
                            "startIndex": 25,
                            "length": 5,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```

详细了解 [V3 预测终结点](luis-migration-api-v3.md)。


## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>后续步骤

* [迭代应用设计](./luis-concept-app-iteration.md)
* [最佳实践](./luis-concept-best-practices.md)
