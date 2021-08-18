---
title: 使用 Azure 视频分析器媒体版（以前称为视频索引器）API 自定义品牌模型
titleSuffix: Azure Video Analyzer for Media
description: 了解如何使用 Azure 视频分析器媒体版（以前称为视频索引器）API 自定义品牌模型。
services: azure-video-analyzer
author: anikaz
manager: johndeu
ms.topic: article
ms.subservice: azure-video-analyzer-media
ms.date: 01/14/2020
ms.author: kumud
ms.openlocfilehash: cf693036fb31613c171bf7618e45e3b1e57970e4
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/15/2021
ms.locfileid: "112123304"
---
# <a name="customize-a-brands-model-with-the-video-analyzer-for-media-api"></a>使用视频分析器媒体版 API 自定义品牌模型

在对视频和音频内容进行索引和重新索引的过程中，可以使用 Azure 视频分析器媒体版（以前为视频索引器）从语音和视觉文本中检测品牌。 品牌检测功能可以确定内容中是否提到必应品牌数据库建议的产品、服务和公司。 例如，如果某个视频或音频内容中提到了 Microsoft，或者 Microsoft 出现在某个视频的视觉文本中，视频分析器媒体版就会将其作为内容中的品牌检测出来。 使用自定义品牌模型时，可以排除某些品牌，不让其检测到；还可以添加一些品牌，这些品牌应该在你的模型中，但可能不在必应的品牌数据库中。 有关详细信息，请参阅[概述](customize-brands-model-overview.md)。

> [!NOTE]
> 如果在添加品牌之前已为视频编制了索引，则需要对其重新编制索引。

可以使用视频分析器媒体版 API 来创建、使用和编辑视频中检测到的自定义品牌模型，如本主题所述。 也可使用视频分析器媒体版网站，如[使用视频分析器媒体版网站自定义品牌模型](customize-brands-model-with-api.md)中所述。

## <a name="create-a-brand"></a>创建品牌

[创建品牌](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Create-Brand) API 会创建新的自定义品牌，并将其添加到指定帐户的自定义品牌模型。

> [!NOTE]
> 如果将 `enabled`（在正文中）设置为 true，则会将品牌置于供视频分析器媒体版检测的 Include 列表中。 如果将 `enabled` 设置为 false，则会将品牌置于 Exclude 列表中，这样视频分析器媒体版就不会检测它。

可在正文中设置的一些其他参数：

* `referenceUrl` 值可以是该品牌的任何参考网站，例如它的维基百科页面的链接。
* `tags` 值是该品牌的标记的列表。 此标记会显示在该品牌在视频分析器媒体版网站的“类别”字段中。 例如，品牌“Azure”可以标记或归类为“云”。

### <a name="response"></a>响应

响应介绍刚刚按照以下示例的格式创建的品牌。

```json
{
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "id": 97974,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-25T14:59:52.7433333",
  "enabled": true,
  "description": "This is an example",
  "tags": [
    "Tag1",
    "Tag2"
  ]
}
```

## <a name="delete-a-brand"></a>删除品牌

[删除品牌](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Delete-Brand) API 会从指定帐户的自定义品牌模型中删除品牌。 该帐户是在 `accountId` 参数中指定的。 成功调用以后，品牌将不再位于 *Include* 或 *Exclude* 品牌列表中。

### <a name="response"></a>响应

在成功删除品牌后，没有内容返回。

## <a name="get-a-specific-brand"></a>获取特定的品牌

利用[获取品牌](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Brand) API，可以使用品牌 ID 在指定帐户的自定义品牌模型中搜索品牌的详细信息。

### <a name="response"></a>响应

响应介绍按照以下示例的格式搜索（使用品牌 ID）的品牌。

```json
{
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "id": 128846,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-01-06T13:51:38.3666667",
  "lastModified": "2018-01-11T13:51:38.3666667",
  "enabled": true,
  "description": "This is an example",
  "tags": [
    "Tag1",
    "Tag2"
  ]
}
```

> [!NOTE]
> `enabled` 设置为 `true` 表示品牌在可供视频分析器媒体版检测的 Include 列表中，`enabled` 为 false 表示品牌在 Exclude 列表中，因此视频分析器媒体版不会检测它 。

## <a name="update-a-specific-brand"></a>更新特定品牌

利用[更新品牌](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Brand) API，你可以使用品牌 ID 在指定帐户的自定义品牌模型中搜索品牌的详细信息。

### <a name="response"></a>响应

响应按照以下示例的格式针对更新的品牌提供更新的信息。

```json
{
  "referenceUrl": null,
  "id": 97974,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "Created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-25T15:37:50.67",
  "enabled": false,
  "description": "This is an update example",
  "tags": [
    "Tag1",
    "NewTag2"
  ]
}
```

## <a name="get-all-of-the-brands"></a>获取所有品牌

[获取所有品牌](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Brands) API 返回指定帐户的自定义品牌模型中的所有品牌，无论该品牌是应该在 Include 品牌列表中，还是应该在 Exclude 品牌列表中 。

### <a name="response"></a>响应

响应提供一个列表，其中包含帐户中的所有品牌，以及遵循以下示例格式的每个品牌的详细信息。

```json
[
    {
        "ReferenceUrl": null,
        "id": 97974,
        "name": "Example",
        "accountId": "AccountId",
        "lastModifierUserName": "UserName",
        "Created": "2018-04-25T14:59:52.7433333",
        "LastModified": "2018-04-25T14:59:52.7433333",
        "enabled": true,
        "description": "This is an example",
        "tags": ["Tag1", "Tag2"]
    },
    {
        "ReferenceUrl": null,
        "id": 97975,
        "name": "Example2",
        "accountId": "AccountId",
        "lastModifierUserName": "UserName",
        "Created": "2018-04-26T14:59:52.7433333",
        "LastModified": "2018-04-26T14:59:52.7433333",
        "enabled": false,
        "description": "This is another example",
        "tags": ["Tag1", "Tag2"]
    },
]
```

> [!NOTE]
> 名为 Example 的品牌在可供视频分析器媒体版检测的 Include 列表中，名为 Example2 的品牌在 Exclude 列表中，因此视频分析器媒体版不会检测它   。

## <a name="get-brands-model-settings"></a>获取品牌模型设置

[获取品牌设置](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Brands) API 返回指定帐户中的品牌模型设置。 品牌模型设置表示是否启用从必应品牌数据库进行检测的功能。 如果未启用必应品牌，则视频分析器媒体版只会从指定帐户的自定义品牌模型检测品牌。

### <a name="response"></a>响应

响应显示是否已按照以下示例的格式启用必应品牌。

```json
{
  "state": true,
  "useBuiltIn": true
}
```

> [!NOTE]
> `useBuiltIn` 设置为 true 表示启用必应品牌。 如果 `useBuiltin` 为 false，则表示禁用必应品牌。 可以忽略 `state` 值，因为它是已弃用的。

## <a name="update-brands-model-settings"></a>更新品牌模型设置

[更新品牌](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Brands-Model-Settings) API 更新指定帐户中的品牌模型设置。 品牌模型设置表示是否启用从必应品牌数据库进行检测的功能。 如果未启用必应品牌，则视频分析器媒体版只会从指定帐户的自定义品牌模型检测品牌。

`useBuiltIn` 标志设置为 true 表示启用必应品牌。 如果 `useBuiltin` 为 false，则表示禁用必应品牌。

### <a name="response"></a>响应

在成功更新品牌模型设置后，没有内容返回。

## <a name="next-steps"></a>后续步骤

[使用网站自定义品牌模型](customize-brands-model-with-website.md)
