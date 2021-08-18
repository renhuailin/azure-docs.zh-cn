---
title: 使用 Azure 视频分析器媒体版（以前称为视频索引器）API 自定义人员模型
titleSuffix: Azure Video Analyzer for Media
description: 了解如何使用 Azure 视频分析器媒体版（以前称为视频索引器）API 自定义人员模型。
services: azure-video-analyzer
author: anikaz
manager: johndeu
ms.topic: article
ms.subservice: azure-video-analyzer-media
ms.date: 01/14/2020
ms.author: kumud
ms.openlocfilehash: 01205b70b3f4195be290075727bece30d57c515e
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/15/2021
ms.locfileid: "112121395"
---
# <a name="customize-a-person-model-with-the-video-analyzer-for-media-api"></a>使用视频分析器媒体版 API 自定义人员模型

Azure 视频分析器媒体版（以前称为视频索引器）支持在视频内容中检测人脸和识别名人。 名人识别功能基于通常请求的数据源（如 IMDB、Wikipedia 和 LinkedIn 影响者排名）涵盖约一百万张人脸。 会检测到名人识别功能未识别的人脸；但是，这些人脸会保留未命名状态。 将视频上传到视频分析器媒体版并获得结果后，即可返回并命名未识别的人脸。 使用名称标记人脸后，人脸和名称将添加到帐户的人员模型中。 然后，视频分析器媒体版会在将来的视频和过去的视频中识别出此人脸。

可以使用视频分析器媒体版 API 编辑在视频中检测到的人脸，如本主题所述。 也可使用视频分析器媒体版网站，如[使用视频分析器媒体版网站自定义人员模型](customize-person-model-with-api.md)中所述。

## <a name="managing-multiple-person-models"></a>管理多个人员模型

视频分析器媒体版支持每个帐户使用多个人员模型。 目前只能通过视频分析器媒体版 API 使用此功能。

如果帐户需要面对不同的用例方案，则可能需要为每个帐户创建多个人员模型。 例如，如果内容与体育相关，则可为每个体育项目（橄榄球、篮球、足球等）创建单独的人员模型。

创建模型后，即可在上传/索引或重新索引视频时提供特定人员模型的模型 ID，这样就可以使用该模型。 为视频训练新的人脸时，会更新与该视频相关联的具体自定义模型。

每个帐户的限制为 50 个人员模型。 如果不需要多人员模型支持，则在进行上传/索引或重新索引操作时不要为视频分配人员模型 ID。 在这种情况下，视频分析器媒体版会使用帐户中的默认自定义人员模型。

## <a name="create-a-new-person-model"></a>创建新的人员模型

若要在指定帐户中创建新的人员模型，请使用[创建人员模型](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Create-Person-Model) API。

响应提供刚刚按照以下示例的格式创建的人员模型的名称和生成的模型 ID。

```json
{
    "id": "227654b4-912c-4b92-ba4f-641d488e3720",
    "name": "Example Person Model"
}
```

然后，在[上传视频到索引](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video)或[重新索引视频](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video)时，使用 personModelId 参数的 id 值。 

## <a name="delete-a-person-model"></a>删除人员模型

若要从指定帐户中删除自定义人员模型，请使用[删除人员模型](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Delete-Person-Model) API。

成功删除人员模型以后，曾经使用已删除模型的当前视频的索引会保持不变，直至你重新对其进行索引。 重新索引后，视频分析器媒体版将无法在使用已删除模型进行索引的当前视频中识别出在该模型中命名的人脸；但是，这些人脸仍然可以检测到。 使用已删除模型进行索引的当前视频现在会使用帐户的默认人员模型。 如果已删除模型中的人脸也在帐户的默认模型中命名，则可继续识别视频中的这些人脸。

成功删除人员模型后没有返回的内容。

## <a name="get-all-person-models"></a>获取所有人员模型

若要获取指定帐户中的所有人员模型，请使用[获取人员模型](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Person-Models) API。

响应提供一个列表，其中包含帐户中的所有人员模型（包括指定帐户中的默认人员模型），以及遵循以下示例格式的每个名称和 ID。

```json
[
    {
        "id": "59f9c326-b141-4515-abe7-7d822518571f",
        "name": "Default"
    }, 
    {
        "id": "9ef2632d-310a-4510-92e1-cc70ae0230d4",
        "name": "Test"
    }
]
```

可以在[上传视频到索引](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video)或[重新索引视频](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video)时使用人员模型的 `id` 值作为 `personModelId` 参数，以便选择需要用于视频的具体模型。

## <a name="update-a-face"></a>更新人脸

此命令允许将视频 ID 和人脸 ID 配合使用，以便通过名称来更新视频中的人脸。 然后，此操作会在执行上传/索引或重新索引操作后更新与视频相关联的人员模型。 如果未分配人员模型，则会更新帐户的默认人员模型。

然后，系统会识别出现在其他当前视频中的同一人脸，这些视频共享同一人员模型。 识别其他当前视频中的人脸可能需要一定的时间，因为这是一个批处理过程。

可以使用新的名称来更新通过视频分析器媒体版识别为名人的人脸。 提供的新名称优先于内置的名人识别功能。

若要更新人脸，请使用[更新视频人脸](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Video-Face) API。

名称特定于人员模型，因此如果为同一人员模型中的两个不同的人脸提供相同的 `name` 参数值，视频分析器媒体版会将这两个人脸视为同一人，并会在完成视频的重新索引后将其聚合在一起。

## <a name="next-steps"></a>后续步骤

[使用视频分析器媒体版网站自定义人员模型](customize-person-model-with-website.md)
