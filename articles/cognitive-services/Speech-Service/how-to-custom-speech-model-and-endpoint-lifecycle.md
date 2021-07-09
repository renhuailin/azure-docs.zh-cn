---
title: 自定义语音的模型和终结点生命周期 - 语音服务
titleSuffix: Azure Cognitive Services
description: 自定义语音提供了用于调整的基础模型，并允许你从数据创建自定义模型。 本文介绍了模型的时间线以及使用这些模型的终结点的时间线。
services: cognitive-services
author: heikora
manager: dongli
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/2/2021
ms.author: heikora
ms.openlocfilehash: 701a16779a7a485f33e5af44ec30d48801b7c1fd
ms.sourcegitcommit: 070122ad3aba7c602bf004fbcf1c70419b48f29e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2021
ms.locfileid: "111440801"
---
# <a name="model-and-endpoint-lifecycle"></a>模型和终结点生命周期

我们的标准（非自定义）语音基于 AI 模型构建，我们称之为基础模型。 在大多数情况下，我们为支持的每种口语训练不同的基础模型。  我们每隔几个月就会用新的基础模型更新语音服务，以提高准确性和质量。  
对于自定义语音识别，自定义模型是通过使用特定客户场景中的数据调整所选基础模型来创建的。 创建自定义模型后，该模型将不会更新或更改，即使在标准语音服务中更新了该模型改编自的相应基础模型也是如此。  
通过此策略，在拥有满足需求的自定义模型之后，可以长期使用特定的自定义模型。  但是建议定期重新创建自定义模型，以根据最新的基础模型进行调整，从而利用提高的准确性和质量。

与模型生命周期相关的其他关键术语包括：

* **调整**：使用基础模型，并使用文本数据和/或音频数据，根据你的领域/场景对其进行自定义。
* **解码**：使用模型并执行语音识别（将音频解码为文本）。
* **终结点**：以特定于用户的方式部署仅给定用户可访问的基础模型或自定义模型。

### <a name="expiration-timeline"></a>过期时间线

随着新模型和新功能的推出，旧的、不太准确的模型会停用，请查看以下模型和终结点到期的时间线：

基础模型 

* 调整：可使用一年。 导入模型后，可用来创建自定义模型的时间为一年。 一年后，需要通过更新的基础模型版本创建新的自定义模型。  
* 解码：导入后可使用两年。 这样就可以创建一个终结点，并可对此模型使用批处理听录两年。 
* 终结点：使用时间线与解码的时间线相同。

**自定义模式**

* 解码：创建模型后可使用两年。 这样就可以在创建自定义模型后使用该模型两年（批处理/实时/测试）。 两年后，应该重新训练模型，因为基础模型通常会停用以进行调整。  
* 终结点：使用时间线与解码的时间线相同。

当基础模型或自定义模型过期时，它将始终回退到最新的基础模型版本。 因此，你的实现永远不会中断，但如果自定义模型到期，模型对于特定数据的准确度可能会降低。 可以在 Speech Studio 的自定义语音识别区域的以下位置查看模型的过期时间：

* 模型训练摘要
* 模型训练详细信息
* 部署摘要
* 部署详细信息

下面是模型训练摘要的示例：

![模型训练摘要](media/custom-speech/custom-speech-model-training-with-expiry.png) 还来自模型训练详细信息页：

![模型训练详细信息](media/custom-speech/custom-speech-model-details-with-expiry.png)

还可以通过 [`GetModel`](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetModel) 和 [`GetBaseModel`](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetBaseModel) 自定义语音 API 在 JSON 响应中的 `deprecationDates` 属性下检查到期日期。

下面是 GetModel API 调用中的过期数据的示例。 模型过期时将显示 DEPRECATIONDATES： 
```json
{
    "SELF": "HTTPS://WESTUS2.API.COGNITIVE.MICROSOFT.COM/SPEECHTOTEXT/V3.0/MODELS/{id}",
    "BASEMODEL": {
    "SELF": HTTPS://WESTUS2.API.COGNITIVE.MICROSOFT.COM/SPEECHTOTEXT/V3.0/MODELS/BASE/{id}
    },
    "DATASETS": [
    {
        "SELF": https://westus2.api.cognitive.microsoft.com/speechtotext/v3.0/datasets/{id}
    }
    ],
    "LINKS": {
    "MANIFEST": "HTTPS://WESTUS2.API.COGNITIVE.MICROSOFT.COM/SPEECHTOTEXT/V3.0/MODELS/{id}/MANIFEST",
    "COPYTO": https://westus2.api.cognitive.microsoft.com/speechtotext/v3.0/models/{id}/copyto
    },
    "PROJECT": {
        "SELF": https://westus2.api.cognitive.microsoft.com/speechtotext/v3.0/projects/{id}
    },
    "PROPERTIES": {
    "DEPRECATIONDATES": {
        "ADAPTATIONDATETIME": "2022-01-15T00:00:00Z",     // last date the base model can be used for adaptation
        "TRANSCRIPTIONDATETIME": "2023-03-01T21:27:29Z"   // last date this model can be used for decoding
    }
    },
    "LASTACTIONDATETIME": "2021-03-01T21:27:40Z",
    "STATUS": "SUCCEEDED",
    "CREATEDDATETIME": "2021-03-01T21:27:29Z",
    "LOCALE": "EN-US",
    "DISPLAYNAME": "EXAMPLE MODEL",
    "DESCRIPTION": "",
    "CUSTOMPROPERTIES": {
    "PORTALAPIVERSION": "3"
    }
}
```
请注意，可以通过 Speech Studio 的“部署”部分或通过自定义语音识别 API 更改终结点使用的模型，在不停机的情况下升级自定义语音识别终结点上的模型。

## <a name="what-happens-when-models-expire-and-how-to-update-them"></a>模型过期时将发生什么情况以及如何更新模型
模型过期时会发生什么情况以及如何更新模型取决于其使用方式。
### <a name="batch-transcription"></a>批量听录
如果用于[批量听录](batch-transcription.md)的模型过期，听录请求将失败，并出现 4xx 错误。 为避免这种情况，请更新“创建听录”请求正文中发送的 JSON 中的 `model` 参数，使其指向较新的基础模型或较新的自定义模型。 还可以从 JSON 删除 `model` 条目，以始终使用最新基础模型。
### <a name="custom-speech-endpoint"></a>自定义语音识别终结点
如果[自定义语音识别终结点](how-to-custom-speech-train-model.md)使用的模型过期，则该服务将自动回退为使用你所使用语言的最新基础模型。 若要更新正在使用的模型，可以在页面顶部的“自定义语音识别”菜单中选择“部署”，然后单击终结点名称以查看其详细信息 。 你将在详细信息页顶部看到“更新模型”按钮，使用此按钮可无缝更新此终结点使用的模型，而无需停机。 还可以使用[更新模型](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/UpdateModel) Rest API 以编程方式进行此更改。

## <a name="next-steps"></a>后续步骤

* [训练和部署模型](how-to-custom-speech-train-model.md)

## <a name="additional-resources"></a>其他资源

* [准备和测试数据](./how-to-custom-speech-test-and-train.md)
* [检查数据](how-to-custom-speech-inspect-data.md)
