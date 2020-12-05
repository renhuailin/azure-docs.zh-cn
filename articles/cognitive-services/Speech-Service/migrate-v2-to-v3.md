---
title: 从 v2 迁移到 v3 REST API 语音服务
titleSuffix: Azure Cognitive Services
description: 与 v2 相比，新的 API 版本 v3 包含一组较小的重大更改。 本文档将帮助你及时迁移到新的主版本。
services: cognitive-services
author: bexxx
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: rbeckers
ms.custom: devx-track-csharp
ms.openlocfilehash: dd1dae963781cc0caacc25938e700a4c70a1f51a
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2020
ms.locfileid: "96737961"
---
# <a name="migration-from-v20-to-v30-of-speech-to-text-rest-api"></a>从 v2.0 到 REST API 3.0 的语音迁移到文本

在可靠性和易用性方面，speech REST API 的 v3 版本可改进之前的 API 版本。 API 布局与其他 Azure 或认知服务 API 更密切地对齐。 这可帮助你在使用语音 API 时应用现有技能。

API 概述以 [Swagger 文档](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)的形式提供。 这非常适合于为你介绍 API 概述并测试新 API。

我们正在提供 c # 和 Python 的示例。 对于 batch 转录，你将在该子目录内的 [GitHub 示例存储库](https://aka.ms/csspeech/samples) 中找到这些示例 `samples/batch` 。

## <a name="forward-compatibility"></a>向前兼容性

若要确保顺利迁移到 v3，还可以在具有相同标识的 v3 API 中找到 v2 中的所有实体。 如果更改了结果架构 (例如，转录) ，则将在 v3 架构中获取对该 API 的 v3 版本的响应。 如果执行 API 的 GET v2 版本，则结果架构将为 v2 格式。 版本2上 **不** 提供 v3 上新创建的实体。

## <a name="breaking-changes"></a>中断性变更

重大更改列表已经按改编所需的更改量进行排序。 只有几项更改需要在调用代码中进行不重要的更改。 大多数更改都需要简单的重命名。 团队从 v2 迁移到 v3 所需的时间在几个小时到几天内发生变化。 不过，增加了稳定性、更简单的代码的优点，更快的响应速度会迅速抵消投资。 

### <a name="host-name-changes"></a>主机名更改

主机名已从 {region}. cri 改为 {region}. c e}。 在此更改中，路径不再包含 "api/"，因为它是主机名的一部分。 有关区域和路径的完整说明，请参阅 [Swagger 文档](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) 。

### <a name="identity-of-an-entity"></a>实体的标识

属性 `id` 已替换为 `self` 。 在 v2 中，API 用户必须知道如何创建 API 上的路径。 这不是可扩展的，不需要用户的必要工作。 `id` (uuid) 的属性将替换 `self` 为 (字符串) ，这是实体 (URL) 的位置。 值在所有实体之间仍是唯一的。 如果 `id` 在代码中以字符串形式存储，则简单重命名就足以支持新架构。 你现在可以使用 `self` 内容作为实体的所有 REST 调用 (获取、修补、删除) 。

如果实体具有其他路径下可用的其他功能，则这些功能将在下面列出 `links` 。 一个很好的示例是一个脚本，它对脚本内容具有单独的方法 `GET` 。

v2 脚本：

```json
{
    "id": "9891c965-bb32-4880-b14b-6d44efb158f3",
    "createdDateTime": "2019-01-07T11:34:12Z",
    "lastActionDateTime": "2019-01-07T11:36:07Z",
    "status": "Succeeded",
    "locale": "en-US",
    "name": "Transcription using locale en-US"
}
```

v3 脚本：

```json
{
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
    "createdDateTime": "2019-01-07T11:34:12Z",
    "lastActionDateTime": "2019-01-07T11:36:07Z",
    "status": "Succeeded",
    "locale": "en-US", 
    "displayName": "Transcription using locale en-US",
    "links": {
      "files": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files"
    }
}
```

根据您的客户端实现，可能不足以重命名属性。 建议使用和的返回值 `self` `links` 作为 rest 调用的目标 url，而不是生成客户端中的路径。 通过使用返回的 Url，你可以确保以后在路径中更改时不会中断客户端代码。

### <a name="working-with-collections-of-entities"></a>使用实体集合

以前，v2 API 在响应中返回所有可用的实体。 若要允许更精细地控制预期的响应大小，请在 v3 中对集合的所有响应进行分页。 您可以控制返回的实体计数和页面的偏移量。 利用此行为，可以轻松预测响应处理器的运行时，并且它与其他 Azure Api 一致。

对于所有集合，响应的基本形状都是相同的：

```json
{
    "values": [
        {     
        }
    ],
    "@nextLink": "https://{region}.api.cognitive.microsoft.com/speechtotext/v3.0/{collection}?skip=100&top=100"
}
```

属性 `values` 包含可用集合实体的子集。 可以通过使用查询参数和来控制计数和偏移量 `skip` `top` 。 当不为 `@nextLink` null 时，有更多的可用数据，可以通过执行 GET 来检索下一批数据 `$.@nextLink` 。

此更改需要 `GET` 在循环中调用集合的，直到返回所有元素。

### <a name="creating-transcriptions"></a>正在创建转录

有关如何创建脚本的详细说明，请参阅 Batch 脚本操作 [方法](./batch-transcription.md)。

已在 v3 中更改了转录的创建，以显式设置特定的脚本选项。 现在，所有 (可选) 配置属性都可以在属性中进行设置 `properties` 。
版本 v3 现在支持多个输入文件，因此需要一个 Url 列表，而不是 v2 要求的单个 URL。 属性名称已从重命名 `recordingsUrl` 为 `contentUrls` 。 V3 上已删除分析转录中的情绪的功能。 建议改为使用 Microsoft 认知服务 [文本分析](https://azure.microsoft.com/en-us/services/cognitive-services/text-analytics/) 。

下的新 `timeToLive` 属性 `properties` 可帮助您修剪现有已完成的实体。 `timeToLive`指定一个持续时间，在这段时间后，将自动删除已完成的实体。 将其设置为较高的值 (例如，在 `PT12H` 连续跟踪、使用和删除实体时) ，因此通常会在12小时后处理长时间。

v2 脚本 POST 请求正文：

```json
{
  "locale": "en-US",
  "name": "Transcription using locale en-US",
  "recordingsUrl": "https://contoso.com/mystoragelocation",
  "properties": {
    "AddDiarization": "False",
    "AddWordLevelTimestamps": "False",
    "PunctuationMode": "DictatedAndAutomatic",
    "ProfanityFilterMode": "Masked"
  }
}
```

v3 脚本 POST 请求正文：

```json
{
  "locale": "en-US",
  "displayName": "Transcription using locale en-US",
  "contentUrls": [
    "https://contoso.com/mystoragelocation",
    "https://contoso.com/myotherstoragelocation"
  ],
  "properties": {
    "diarizationEnabled": false,
    "wordLevelTimestampsEnabled": false,
    "punctuationMode": "DictatedAndAutomatic",
    "profanityFilterMode": "Masked"
  }
}
```

### <a name="format-of-v3-transcription-results"></a>V3 脚本结果的格式

脚本结果架构已略微更改，与实时终结点所创建的转录保持一致。 有关新格式的详细说明，请参阅 Batch 脚本操作 [方法](./batch-transcription.md)。 结果的架构在下面的 [GitHub 示例存储库](https://aka.ms/csspeech/samples) 中发布 `samples/batch/transcriptionresult_v3.schema.json` 。

属性名称现在为大小写，通道和发言人的值使用整数类型。 若要将持续时间的格式与其他 Azure Api 保持一致，它现在的格式如 ISO 8601 中所述。

V3 脚本结果的示例。 注释中介绍了这些差异。

```json
{
  "source": "...",                      // (new in v3) was AudioFileName / AudioFileUrl
  "timestamp": "2020-06-16T09:30:21Z",  // (new in v3)
  "durationInTicks": 41200000,          // (new in v3) was AudioLengthInSeconds
  "duration": "PT4.12S",                // (new in v3)
  "combinedRecognizedPhrases": [        // (new in v3) was CombinedResults
    {
      "channel": 0,                     // (new in v3) was ChannelNumber
      "lexical": "hello world",
      "itn": "hello world",
      "maskedITN": "hello world",
      "display": "Hello world."
    }
  ],
  "recognizedPhrases": [                // (new in v3) was SegmentResults
    {
      "recognitionStatus": "Success",   // 
      "channel": 0,                     // (new in v3) was ChannelNumber
      "offset": "PT0.07S",              // (new in v3) new format, was OffsetInSeconds
      "duration": "PT1.59S",            // (new in v3) new format, was DurationInSeconds
      "offsetInTicks": 700000.0,        // (new in v3) was Offset
      "durationInTicks": 15900000.0,    // (new in v3) was Duration

      // possible transcriptions of the current phrase with confidences
      "nBest": [
        {
          "confidence": 0.898652852,phrase
          "speaker": 1,
          "lexical": "hello world",
          "itn": "hello world",
          "maskedITN": "hello world",
          "display": "Hello world.",

          "words": [
            {
              "word": "hello",
              "offset": "PT0.09S",
              "duration": "PT0.48S",
              "offsetInTicks": 900000.0,
              "durationInTicks": 4800000.0,
              "confidence": 0.987572
            },
            {
              "word": "world",
              "offset": "PT0.59S",
              "duration": "PT0.16S",
              "offsetInTicks": 5900000.0,
              "durationInTicks": 1600000.0,
              "confidence": 0.906032
            }
          ]
        }
      ]
    }
  ]
}
```

### <a name="getting-the-content-of-entities-and-the-results"></a>获取实体的内容和结果

在 v2 中，指向输入文件或结果文件的链接已与实体元数据的其余部分内联。 作为 v3 中的改进，实体元数据（由 GET 返回， `$.self` 以及用于访问结果文件的详细信息和凭据）之间存在明显的分离。 这种隔离可帮助保护客户数据，并允许对凭据有效性的持续时间进行精细控制。

在 v3 中， `files` 如果实体公开数据 (数据集、转录、终结点、计算) ，则在 "链接" 下有一个名为的属性。 GET `$.links.files` 会返回一个列表，其中列出了用于访问每个文件内容的文件和 SAS URL。 若要控制 SAS Url 的有效期， `sasValidityInSeconds` 可以使用 query 参数指定生存期。

v2 脚本：

```json
{
  "id": "9891c965-bb32-4880-b14b-6d44efb158f3",
  "status": "Succeeded",
  "reportFileUrl": "https://contoso.com/report.txt?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=6c044930-3926-4be4-be76-f728327c53b5",
  "resultsUrls": {
    "channel_0": "https://contoso.com/audiofile1.wav?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=6c044930-3926-4be4-be76-f72832e6600c",
    "channel_1": "https://contoso.com/audiofile2.wav?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=3e0163f1-0029-4d4a-988d-3fba7d7c53b5"
  }
}
```

v3 脚本：

```json
{
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
    "links": {
      "files": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files"
    } 
}
```

那么，GET `$.links.files` 将导致：

```json
{
  "values": [
    {
      "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files/f23e54f5-ed74-4c31-9730-2f1a3ef83ce8",
      "name": "Name",
      "kind": "Transcription",
      "properties": {
        "size": 200
      },
      "createdDateTime": "2020-01-13T08:00:00Z",
      "links": {
        "contentUrl": "https://customspeech-usw.blob.core.windows.net/artifacts/mywavefile1.wav.json?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    },
    {
      "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files/28bc946b-c251-4a86-84f6-ea0f0a2373ef",
      "name": "Name",
      "kind": "TranscriptionReport",
      "properties": {
        "size": 200
      },
      "createdDateTime": "2020-01-13T08:00:00Z",
      "links": {
        "contentUrl": "https://customspeech-usw.blob.core.windows.net/artifacts/report.json?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    }
  ],
  "@nextLink": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files?skip=2&top=2"
}
```

`kind`指示文件内容的格式。 对于转录，类型的文件是作业的 `TranscriptionReport` 摘要，而文件的 `Transcription` 结果是作业本身的结果。

### <a name="customizing-models"></a>自定义模型

在 v3 之前，在训练模型时，"声音模型" 和 "语言模型" 之间存在差异。 这种区别导致需要在创建终结点或转录时指定多个模型。 为了简化调用方的这一过程，我们消除了不同之处，并依赖于模型定型所使用的数据集内容。 进行此更改后，模型创建现在支持 (语言数据和声音数据) 的混合数据集。 终结点和转录现在只需要一个模型。

进行此更改 `kind` 后，就删除了 POST 中的需要， `datasets[]` 现在可以包含相同或混合类型的多个数据集。

若要改善定型模型的结果，可在内部自动使用声音数据进行语言训练。 通常，通过 v3 API 创建的模型比使用 v2 API 创建的模型提供更准确的结果。

### <a name="retrieving-base-and-custom-models"></a>检索基本模型和自定义模型

为了简化可用的模型，v3 已将客户拥有的 "自定义模型" 中的 "基本模型" 集合隔开。 这两个路由现在为 `GET /speechtotext/v3.0/models/base` 和 `GET /speechtotext/v3.0/models/` 。

以前，所有模型都在单个响应中一起返回。

### <a name="name-of-an-entity"></a>实体的名称

属性的名称 `name` 将重命名为 `displayName` 。 这与其他 Azure Api 对齐，不指示标识属性。 此属性的值不能是唯一的，并且可以在创建实体后更改 `PATCH` 。

v2 脚本：

```json
{
    "name": "Transcription using locale en-US"
}
```

v3 脚本：

```json
{
    "displayName": "Transcription using locale en-US"
}
```

### <a name="accessing-referenced-entities"></a>访问引用的实体

在 v2 中，引用的实体始终是内联的，例如终结点的已使用模型。 实体的嵌套导致大的响应，并且使用者很少使用嵌套的内容。 为了减小响应大小并提高所有 API 用户的性能，将不再在响应中内联引用的实体。 而是使用对其他实体的引用，可以直接使用该引用。 此引用可用于后面的 `GET` (它是一个 URL，并遵循与链接相同的模式) `self` 。

v2 脚本：

```json
{
  "id": "9891c965-bb32-4880-b14b-6d44efb158f3",
  "models": [
    {
      "id": "827712a5-f942-4997-91c3-7c6cde35600b",
      "modelKind": "Language",
      "lastActionDateTime": "2019-01-07T11:36:07Z",
      "status": "Running",
      "createdDateTime": "2019-01-07T11:34:12Z",
      "locale": "en-US",
      "name": "Acoustic model",
      "description": "Example for an acoustic model",
      "datasets": [
        {
          "id": "702d913a-8ba6-4f66-ad5c-897400b081fb",
          "dataImportKind": "Language",
          "lastActionDateTime": "2019-01-07T11:36:07Z",
          "status": "Succeeded",
          "createdDateTime": "2019-01-07T11:34:12Z",
          "locale": "en-US",
          "name": "Language dataset",
          
        }
      ]
    },
  ]
}
```

v3 脚本：

```json
{
  "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
  "model": {
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/models/021a72d0-54c4-43d3-8254-27336ead9037"
  }
}
```

如果需要使用所引用模型的详细信息（如上面的示例所示），请简化发出 GET `$.model.self` 。

### <a name="retrieving-endpoint-logs"></a>检索终结点日志

服务的版本 v2，支持记录终结点的响应。 若要使用 v2 检索终结点的结果，必须创建一个 "数据导出"，它表示时间范围定义的结果的快照。 导出批处理数据的过程变得不太灵活。 V3 API 可以访问每个单独的文件，并允许通过这些文件进行迭代。

已成功运行 v3 终结点：

```json
{
  "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6",
  "links": {
    "logs": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6/files/logs" 
  }
}
```

GET 响应 `$.links.logs` ：

```json
{
  "values": [
    {
      "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/6d72ad7e-f286-4a6f-b81b-a0532ca6bcaa/files/logs/2019-09-20_080000_3b5f4628-e225-439d-bd27-8804f9eed13f.wav",
      "name": "2019-09-20_080000_3b5f4628-e225-439d-bd27-8804f9eed13f.wav",
      "kind": "Audio",
      "properties": {
        "size": 12345
      },
      "createdDateTime": "2020-01-13T08:00:00Z",
      "links": {
        "contentUrl": "https://customspeech-usw.blob.core.windows.net/artifacts/2019-09-20_080000_3b5f4628-e225-439d-bd27-8804f9eed13f.wav?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    }    
  ],
  "@nextLink": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6/files/logs?top=2&SkipToken=2!188!MDAwMDk1ITZhMjhiMDllLTg0MDYtNDViMi1hMGRkLWFlNzRlOGRhZWJkNi8yMDIwLTA0LTAxLzEyNDY0M182MzI5NGRkMi1mZGYzLTRhZmEtOTA0NC1mODU5ZTcxOWJiYzYud2F2ITAwMDAyOCE5OTk5LTEyLTMxVDIzOjU5OjU5Ljk5OTk5OTlaIQ--"
}
```

对于终结点日志的分页，其工作方式类似于其他所有集合，但不能指定任何偏移量。 由于有大量可用数据，因此必须实现服务器驱动的分页。

在 v3 中，每个终结点日志都可以通过在文件的上发出删除或使用 "删除" 来单独删除 `self` `$.links.logs` 。 若要指定结束数据，可以将查询参数 `endDate` 添加到请求中。

### <a name="using-custom-properties"></a>使用 "自定义" 属性

若要将自定义属性与可选配置属性分离，所有显式命名的属性现在都位于 `properties` 属性中，并且定义的所有属性现在位于 `customProperties` 属性中。

v2 脚本实体

```json
{
  "properties": {
    "customerDefinedKey": "value",
    "diarizationEnabled": "False",
    "wordLevelTimestampsEnabled": "False"
  }
}
```

v3 脚本实体

```json
{
  "properties": {
    "diarizationEnabled": false,
    "wordLevelTimestampsEnabled": false 
  },
  "customProperties": {
    "customerDefinedKey": "value"
  }
}
```

此更改还允许在 (的所有显式命名属性 `properties` （例如 bool 而不是字符串) ）上使用正确的类型。

### <a name="response-headers"></a>响应头

v3 不再返回标题 `Operation-Location` 以及 POST 请求上的标头 `Location` 。 两个标头的值都是完全相同的。 现在只 `Location` 返回。

由于新的 API 版本现在由 Azure API 管理管理 (APIM) 、限制相关的标头 `X-RateLimit-Limit` 、 `X-RateLimit-Remaining` 和 `X-RateLimit-Reset` 不包含在响应标头中。

### <a name="accuracy-tests"></a>准确性测试

已将准确性测试重命名为 "评估"，因为新名称会更好地描述它们所代表的内容。 新闻路径为例如 "https：//{region} speechtotext//3.0/评估"。
