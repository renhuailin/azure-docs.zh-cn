---
title: 使用 Azure 视频分析器媒体版（以前称为视频索引器）API 自定义语言模型
titlesuffix: Azure Video Analyzer for Media
description: 了解如何使用 Azure 视频分析器媒体版（以前称为视频索引器）API 自定义语言模型。
services: azure-video-analyzer
author: anikaz
manager: johndeu
ms.topic: article
ms.subservice: azure-video-analyzer-media
ms.date: 02/04/2020
ms.author: kumud
ms.openlocfilehash: b3239e9af0a8ba3218d511f872dcbd117295add0
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/15/2021
ms.locfileid: "112123223"
---
# <a name="customize-a-language-model-with-the-video-analyzer-for-media-api"></a>使用视频分析器媒体版 API 自定义语言模型

Azure 视频分析器媒体版（以前称为视频索引器）可以让你通过上传自适应文本（即其词汇需要引擎来适应的领域中的文本）创建自定义语言模型来自定义语音识别。 训练模型后，可以识别自适应文本中显示的新单词。

有关自定义语言模型的详细概述和最佳做法，请参阅[使用视频分析器媒体版自定义语言模型](customize-language-model-overview.md)。

可以使用视频分析器媒体版 API 在帐户中创建和编辑自定义语言模型，如本主题所述。 也可以按[使用视频分析器媒体版网站自定义语言模型](customize-language-model-with-api.md)中所述使用网站。

## <a name="create-a-language-model"></a>创建语言模型

[创建语言模型](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Create-Language-Model) API 在指定的帐户中创建新的自定义语言模型。 可以在此调用中上传语言模型的文件。 或者，可以在此处创建语言模型，稍后再通过更新语言模型上传模型的文件。

> [!NOTE]
> 仍必须使用模型的已启用文件来训练该模型，以学习其文件的内容。 下一部分提供了有关训练语言的指导。

若要上传要添加到语言模型的文件，必须使用 FormData 在正文中上传文件，此外，必须为上述所需参数提供值。 可通过两种方式来执行此任务：

* 密钥是文件名，值是 txt 文件。
* 密钥是文件名，值是 txt 文件的 URL。

### <a name="response"></a>响应

响应提供有关新建的语言模型的元数据，以及有关每个模型的文件的元数据（遵循此示例 JSON 输出的格式）：

```json
{
    "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
    "name": "TestModel",
    "language": "En-US",
    "state": "None",
    "languageModelId": "00000000-0000-0000-0000-000000000000",
    "files": [
    {
        "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.6733333"
    },
    {
        "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
        "name": "worldfile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.86"
    }
    ]
}

```

## <a name="train-a-language-model"></a>训练语言模型

[训练语言模型](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Train-Language-Model) API 使用已在语言模型中上传并启用的文件中的内容，在指定的帐户中训练一个自定义的语言模型。

> [!NOTE]
> 必须先创建语言模型并上传其文件。 可以在创建语言模型时或者通过更新语言模型来上传文件。

### <a name="response"></a>响应

响应提供有关新训练的语言模型的元数据，以及有关每个模型的文件的元数据（遵循此示例 JSON 输出的格式）：

```json
{
    "id": "41464adf-e432-42b1-8e09-f52905d7e29d",
    "name": "TestModel",
    "language": "En-US",
    "state": "Waiting",
    "languageModelId": "531e5745-681d-4e1d-b124-12e5ab57a891",
    "files": [
    {
        "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
        "name": "RenamedFile",
        "enable": false,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.5233333"
    },
    {
        "id": "9ac35b4b-1381-49c4-9fe4-8234bfdd0f50",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.68"
    }
    ]
}
```

返回的 `id` 是用于区分语言模型的唯一 ID，而 `languageModelId` 用于[上传要编制索引的视频](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video)和[为视频重新编制索引](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video) API（也称为视频分析器媒体版上传/重新编制索引 API 中的 `linguisticModelId`）。

## <a name="delete-a-language-model"></a>删除语言模型

[删除语言模型](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Delete-Language-Model) API 从指定的帐户中删除自定义语言模型。 使用已删除语言模型的任何视频会保留相同的索引，直到为该视频重新编制索引为止。 如果重新为视频编制索引，可为视频分配新的语言模型。 否则，视频分析器媒体版会使用其默认模型重新为视频编制索引。

### <a name="response"></a>响应

成功删除语言模型后不会返回内容。

## <a name="update-a-language-model"></a>更新语言模型

[更新语言模型](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Language-Model) API 在指定的帐户中更新自定义语言个人模型。

> [!NOTE]
> 必须事先创建一个语言模型。 可以使用此调用来启用或禁用模型下的所有文件、更新语言模型的名称，以及上传要添加到语言模型的文件。

若要上传要添加到语言模型的文件，必须使用 FormData 在正文中上传文件，此外，必须为上述所需参数提供值。 可通过两种方式来执行此任务：

* 密钥是文件名，值是 txt 文件。
* 密钥是文件名，值是 txt 文件的 URL。

### <a name="response"></a>响应

响应提供有关新训练的语言模型的元数据，以及有关每个模型的文件的元数据（遵循此示例 JSON 输出的格式）：

```json
{
    "id": "41464adf-e432-42b1-8e09-f52905d7e29d",
    "name": "TestModel",
    "language": "En-US",
    "state": "Waiting",
    "languageModelId": "531e5745-681d-4e1d-b124-12e5ab57a891",
    "files": [
    {
        "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
        "name": "RenamedFile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.5233333"
    },
    {
        "id": "9ac35b4b-1381-49c4-9fe4-8234bfdd0f50",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.68"
    }
    ]
}
```

使用在响应中返回的文件的 `id` 来下载文件的内容。

## <a name="update-a-file-from-a-language-model"></a>更新语言模型中的文件

[更新文件](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Language-Model-file)用于更新指定帐户中某个自定义语言模型内的某个文件的名称和 `enable` 状态。

### <a name="response"></a>响应

响应提供有关已更新的文件的元数据（遵循以下示例 JSON 输出的格式）。

```json
{
  "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
  "name": "RenamedFile",
  "enable": false,
  "creator": "John Doe",
  "creationTime": "2018-04-27T20:10:10.5233333"
}
```

使用在响应中返回的文件的 `id` 来下载文件的内容。

## <a name="get-a-specific-language-model"></a>获取特定的语言模型

[获取](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Language-Model) API 返回有关指定帐户指定语言模型的信息，例如语言以及语言模型中的文件。

### <a name="response"></a>响应

响应提供有关指定的语言模型的元数据，以及有关每个模型的文件的元数据（遵循此示例 JSON 输出的格式）：

```json
{
    "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
    "name": "TestModel",
    "language": "En-US",
    "state": "None",
    "languageModelId": "00000000-0000-0000-0000-000000000000",
    "files": [
    {
        "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.6733333"
    },
    {
        "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
        "name": "worldfile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.86"
    }
    ]
}
```

使用在响应中返回的文件的 `id` 来下载文件的内容。

## <a name="get-all-the-language-models"></a>获取所有语言模型

[获取全部](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Language-Models) API 在列表中返回指定帐户中的所有自定义语言模型。

### <a name="response"></a>响应

响应提供一个列表，其中包含帐户中的所有语言模型，以及这些模型的每个元数据和文件（遵循此示例 JSON 输出的格式）：

```json
[
    {
        "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
        "name": "TestModel",
        "language": "En-US",
        "state": "None",
        "languageModelId": "00000000-0000-0000-0000-000000000000",
        "files": [
        {
            "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
            "name": "hellofile",
            "enable": true,
            "creator": "John Doe",
            "creationTime": "2018-04-28T11:55:34.6733333"
        },
        {
            "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
            "name": "worldfile",
            "enable": true,
            "creator": "John Doe",
            "creationTime": "2018-04-28T11:55:34.86"
        }
        ]
    },
    {
        "id": "dfae5745-6f1d-4edd-b224-42e1ab57a892",
        "name": "AnotherTestModel",
        "language": "En-US",
        "state": "None",
        "languageModelId": "00000000-0000-0000-0000-000000000001",
        "files": []
    }
]
```

## <a name="delete-a-file-from-a-language-model"></a>从语言模型中删除文件

[删除](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Delete-Language-Model-File) API 从指定帐户中的指定语言模型删除指定的文件。

### <a name="response"></a>响应

成功从语言模型中删除文件后不会返回内容。

## <a name="get-metadata-on-a-file-from-a-language-model"></a>获取有关语言模型中的文件的元数据

[获取文件的元数据](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Language-Model-File-Data) API 返回帐户中所选语言模型内的指定文件的内容及其相关元数据。

### <a name="response"></a>响应

响应提供文件的 JSON 格式内容和元数据，如此示例所示：

```json
{
    "content": "hello\r\nworld",
    "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
    "name": "Hello",
    "enable": true,
    "creator": "John Doe",
    "creationTime": "2018-04-27T20:10:10.5233333"
}
```

> [!NOTE]
> 此示例文件的内容是分两行显示的单词“hello”和“world”。

## <a name="download-a-file-from-a-language-model"></a>从语言模型下载文件

[下载文件](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Download-Language-Model-File-Content) API 从指定帐户中的指定语言模型下载包含指定文件内容的文本文件。 此文本文件应与最初上传的文本文件的内容相匹配。

### <a name="response"></a>响应

响应是下载的文本文件，其中包含该文件的 JSON 格式内容。

## <a name="next-steps"></a>后续步骤

[使用网站自定义语言模型](customize-language-model-with-website.md)
