---
title: 从 v2 迁移到 v3 REST API 语音服务
titleSuffix: Azure Cognitive Services
description: 本文档可帮助开发人员将代码从 v2 迁移到 "语音转换到文本" REST API 中的 v3。
services: cognitive-services
author: bexxx
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: rbeckers
ms.custom: devx-track-csharp
ms.openlocfilehash: c5bc00ecf5e4c8ae440ce6610e9be8c8f77ed666
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2020
ms.locfileid: "96862201"
---
# <a name="migrate-code-from-v20-to-v30-of-the-rest-api"></a>将代码从 v2.0 迁移到 REST API 的3.0 版

与 v2 相比，用于语音到文本的语音服务 REST API 的 v3 版本更可靠、更易于使用，并且与类似服务的 Api 更一致。 大多数团队可以在一天或两天内从 v2 迁移到 v3。

## <a name="forward-compatibility"></a>向前兼容性

还可以在 v3 API 中的相同标识下找到 v2 中的所有实体。 如果结果的架构发生了更改， (例如转录) ，则在 v3 版本的 API 中获取的结果将使用 v3 架构。 版本2版 API 的 GET 结果使用相同的 v2 架构。 版本 2 Api 的结果中 **不** 提供 v3 上新创建的实体。

## <a name="breaking-changes"></a>中断性变更

重大更改列表已经按改编所需的更改量进行排序。 只有少量更改需要调用代码中的不重要更改。 大多数更改只需要更改项名称。

### <a name="host-name-changes"></a>主机名更改

终结点主机名已从更改 `{region}.cris.ai` 为 `{region}.api.cognitive.microsoft.com` 。 新终结点的路径不再包含， `api/` 因为它是主机名的一部分。 [Swagger 文档](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)列出了有效的区域和路径。

### <a name="identity-of-an-entity"></a>实体的标识

属性 `id` 现在是 `self` 。 在 v2 中，API 用户必须知道如何创建 API 上的路径。 这不是可扩展的，不需要用户的必要工作。 `id` (uuid) 的属性将替换 `self` 为 (字符串) ，这是实体 (URL) 的位置。 值在所有实体之间仍是唯一的。 如果 `id` 在代码中以字符串形式存储，则重命名足以支持新架构。 你现在可以使用 `self` 内容作为 `GET` 实体的、 `PATCH` 和 REST 调用的 URL `DELETE` 。

如果实体具有通过其他路径提供的其他功能，则这些功能将在下面列出 `links` 。 下面的脚本示例显示了对脚本 `GET` 内容的单独方法：

**v2 脚本：**

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

**v3 脚本：**

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

根据代码的实现，重命名属性可能不够。 建议使用返回的 `self` 和 `links` 值作为 REST 调用的目标 url，而不是在客户端中生成路径。 通过使用返回的 Url，你可以确保以后在路径中更改时不会中断客户端代码。

### <a name="working-with-collections-of-entities"></a>使用实体集合

之前，v2 API 返回了结果中所有可用的实体。 若要允许更精细地控制 v3 中预期的响应大小，请对所有集合结果进行分页。 您可以控制返回的实体计数和页的起始偏移量。 利用此行为，可以轻松预测响应处理器的运行时。

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

`values`属性包含可用集合实体的子集。 可以使用 `skip` 和查询参数控制计数和偏移量 `top` 。 如果 `@nextLink` 不是 `null` ，则可以使用更多的数据，可以通过执行 GET 来检索下一批数据 `$.@nextLink` 。

此更改需要 `GET` 在循环中调用集合的，直到返回所有元素。

### <a name="creating-transcriptions"></a>正在创建转录

有关如何创建批的转录的详细说明，请参阅 Batch 脚本操作 [方法](./batch-transcription.md)。

通过 v3 脚本 API，你可以显式设置特定的脚本选项。 现在，所有 (可选) 配置属性都可以在属性中进行设置 `properties` 。
版本 v3 还支持多个输入文件，因此需要使用 Url 列表，而不是使用单个 URL 作为 v2。 V2 属性名称 `recordingsUrl` 现在 `contentUrls` 在 v3 中。 在 v3 中已删除对转录中的情绪进行分析的功能。 请参阅 Microsoft 认知服务 [文本分析](https://azure.microsoft.com/en-us/services/cognitive-services/text-analytics/) 了解情绪分析选项。

下的新 `timeToLive` 属性 `properties` 可帮助修剪现有已完成的实体。 `timeToLive`指定一个持续时间，在该时间之后将自动删除已完成的实体。 将其设置为较高的值 (例如 `PT12H` ，在连续跟踪、使用和删除实体时) ，因此通常会在12小时前处理很长时间。

**v2 脚本 POST 请求正文：**

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

**v3 脚本 POST 请求正文：**

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

脚本结果架构已更改，与实时终结点创建的转录保持一致。 [若要](./batch-transcription.md)深入了解批处理操作方法，请查看有关新格式的详细说明。 结果的架构在下面的 [GitHub 示例存储库](https://aka.ms/csspeech/samples) 中发布 `samples/batch/transcriptionresult_v3.schema.json` 。

属性名称现在采用大小写形式，并且的值 `channel` `speaker` 现在使用整数类型。 持续时间的格式现在使用 ISO 8601 中所述的结构，该结构匹配其他 Azure Api 中使用的持续时间格式。

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

在 v2 中，指向输入文件或结果文件的链接已与实体元数据的其余部分内联。 作为 v3 中的改进，在) 的 GET `$.self` 和访问结果文件的详细信息和凭据 (返回的实体元数据之间存在明显的隔离。 这种隔离可帮助保护客户数据，并允许对凭据有效性的持续时间进行精细控制。

在 v3 中， `links` 包含一个名为的子属性， `files` 该属性在实体公开数据时 (数据集、转录、终结点或计算) 。 GET `$.links.files` 会返回文件列表，以及用于访问每个文件内容的 SAS URL。 若要控制 SAS Url 的有效期， `sasValidityInSeconds` 可以使用 query 参数指定生存期。

**v2 脚本：**

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

**v3 脚本：**

```json
{
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
    "links": {
      "files": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files"
    } 
}
```

**GET `$.links.files` 将导致：**

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

`kind`属性指示文件内容的格式。 对于转录，类型的文件是作业的 `TranscriptionReport` 摘要，而文件的 `Transcription` 结果是作业本身的结果。

### <a name="customizing-models"></a>自定义模型

在 v3 之前，在训练模型时， _声音模型_ 和 _语言模型_ 之间存在差异。 这种区别导致需要在创建终结点或转录时指定多个模型。 为了简化调用方的这一过程，我们移除了不同之处，所有内容都依赖于用于模型定型的数据集的内容。 进行此更改后，模型创建现在支持 (语言数据和声音数据) 的混合数据集。 终结点和转录现在只需要一个模型。

进行此更改后， `kind` `POST` 就会删除对操作的需求，并且 `datasets[]` 数组现在可以包含相同或混合类型的多个数据集。

若要改善定型模型的结果，可在语言训练过程中在内部自动使用声音数据。 通常，通过 v3 API 创建的模型比使用 v2 API 创建的模型提供更准确的结果。

### <a name="retrieving-base-and-custom-models"></a>检索基本模型和自定义模型

为了简化可用模型，v3 已将客户拥有的 "自定义模型" 中的 "基本模型" 集合隔开。 这两个路由现在为 `GET /speechtotext/v3.0/models/base` 和 `GET /speechtotext/v3.0/models/` 。

在 v2 中，所有模型一起返回到一个响应中。

### <a name="name-of-an-entity"></a>实体的名称

`name`属性现在是 `displayName` 。 这与其他 Azure Api 保持一致，不指示标识属性。 此属性的值不能是唯一的，并且可以在创建实体后使用操作来更改 `PATCH` 。

**v2 脚本：**

```json
{
    "name": "Transcription using locale en-US"
}
```

**v3 脚本：**

```json
{
    "displayName": "Transcription using locale en-US"
}
```

### <a name="accessing-referenced-entities"></a>访问引用的实体

在 v2 中，被引用的实体始终是内联的，例如终结点的已使用模型。 实体的嵌套导致大的响应，并且使用者很少使用嵌套的内容。 为了减小响应大小并提高性能，被引用的实体不再在响应中内联。 相反，会出现对另一个实体的引用，并且可以直接用于后面的 `GET` (它是一个 URL) ，并遵循与链接相同的模式 `self` 。

**v2 脚本：**

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

**v3 脚本：**

```json
{
  "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
  "model": {
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/models/021a72d0-54c4-43d3-8254-27336ead9037"
  }
}
```

如果需要使用所引用模型的详细信息（如上面的示例所示），只需发出 GET on `$.model.self` 。

### <a name="retrieving-endpoint-logs"></a>检索终结点日志

Service 支持的日志记录终结点结果的版本 v2。 若要使用 v2 检索终结点的结果，请创建一个 "数据导出"，它表示时间范围定义的结果的快照。 导出数据批的过程不太灵活。 V3 API 可以访问每个单独的文件，并允许通过这些文件进行迭代。

**已成功运行 v3 终结点：**

```json
{
  "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6",
  "links": {
    "logs": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6/files/logs" 
  }
}
```

**GET 响应 `$.links.logs` ：**

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

对于终结点日志的分页，其工作方式类似于其他所有集合，但不能指定任何偏移量。 由于有大量可用数据，分页由服务器决定。

在 v3 中，每个终结点日志都可以通过 `DELETE` 在文件的上发出操作 `self` 或使用打开来单独 `DELETE` 删除 `$.links.logs` 。 若要指定结束日期，可以将查询参数 `endDate` 添加到请求中。

### <a name="using-custom-properties"></a>使用自定义属性

若要将自定义属性与可选配置属性分离，所有显式命名的属性现在都位于 `properties` 属性中，并且调用方定义的所有属性现在位于 `customProperties` 属性中。

**v2 脚本实体：**

```json
{
  "properties": {
    "customerDefinedKey": "value",
    "diarizationEnabled": "False",
    "wordLevelTimestampsEnabled": "False"
  }
}
```

**v3 脚本实体：**

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

此更改还允许你在 (的所有显式命名属性（例如 `properties` 布尔值，而不是字符串) ）上使用正确的类型。

### <a name="response-headers"></a>响应头

`Operation-Location`除了 `Location` 请求标头，v3 不再返回标头 `POST` 。 V2 中两个标头的值相同。 现在只 `Location` 返回。

由于新的 API 版本现在由 Azure API 管理管理 (APIM) 、限制相关的标头 `X-RateLimit-Limit` 、 `X-RateLimit-Remaining` 和 `X-RateLimit-Reset` 不包含在响应标头中。

### <a name="accuracy-tests"></a>准确性测试

已将准确性测试重命名为 "评估"，因为新名称会更好地描述它们所代表的内容。 新路径为： `https://{region}.api.cognitive.microsoft.com/speechtotext/v3.0/evaluations` 。

## <a name="next-steps"></a>后续步骤

检查语音服务提供的这些常用 REST Api 的所有功能：

* [语音转文本 REST API](rest-speech-to-text.md)
* REST API 的 v3 的[Swagger 文档](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)
* 有关执行批处理转录的示例代码，请查看子目录中的 [GitHub 示例存储库](https://aka.ms/csspeech/samples) `samples/batch` 。
