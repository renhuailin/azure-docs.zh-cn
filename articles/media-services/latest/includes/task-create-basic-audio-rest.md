---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/18/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: 3a99dd9e69363e8fbb02b7e5e1ac5921d079ba21
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2020
ms.locfileid: "94917864"
---
<!--Create a basic audio transform REST-->

以下 Azure REST 命令创建基本的音频资产。 将 `subscriptionID`、`resourceGroup` 和 `amsAccountName` 值替换为当前正在使用的值。 通过设置为转换指定名称 `transformName` 。

```REST

PUT https://management.azure.com/subscriptions/{{subscriptionId}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaServices/{{accountName}}/transforms/{{transformName}}?api-version=2020-05-01

```

## <a name="body"></a>正文

```json
{
    "properties": {
        "description": "Transform for Audio Analyzer Basic Mode",
        "outputs": [
            {
                "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                "preset": {
                    "@odata.type": "#Microsoft.Media.AudioAnalyzerPreset",
                    "audioLanguage": "en-US",
                    "mode": "Basic"
                }
            }
        ]
    }
}
```

## <a name="sample-response"></a>示例响应
```json
{
    "name": "audioAnalyzerTransform",
    "id": "/subscriptions/35c2594a-xxxx-4fce-b59c-f6fb9513eeeb/resourceGroups/myresourcegroup/providers/Microsoft.Media/mediaservices/mymediaservicesaccount/transforms/audioAnalyzerTransform",
    "type": "Microsoft.Media/mediaservices/transforms",
    "properties": {
        "created": "2020-10-23T18:03:37.4793962Z",
        "description": "Transform for Audio Analyzer Basic Mode",
        "lastModified": "2020-10-23T18:03:37.4793962Z",
        "outputs": [
            {
                "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                "preset": {
                    "@odata.type": "#Microsoft.Media.AudioAnalyzerPreset",
                    "audioLanguage": "en-US",
                    "mode": "Basic",
                    "experimentalOptions": {
                        "basicAudioEnabled": "true"
                    }
                }
            }
        ]
    }
}
```