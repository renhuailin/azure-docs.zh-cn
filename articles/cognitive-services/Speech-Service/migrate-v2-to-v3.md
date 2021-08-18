---
title: 从 v2 迁移到 v3 REST API - 语音服务
titleSuffix: Azure Cognitive Services
description: 本文档可帮助开发人员将语音服务语音转文本 REST API 中的代码从 v2 迁移到 v3。
services: cognitive-services
author: bexxx
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: rbeckers
ms.custom: devx-track-csharp
ms.openlocfilehash: e8e55202163a447ca2d7d08999c953e619bc027b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121746575"
---
# <a name="migrate-code-from-v20-to-v30-of-the-rest-api"></a>将代码从 REST API 的 v2.0 迁移到 v3.0

与 v2 相比，用于语音转文本的语音服务 REST API 的 v3 版本更加可靠且更易于使用，并且与类似服务的 API 更加一致。 大多数团队只需一两天时间即可从 v2 迁移到 v3。

## <a name="forward-compatibility"></a>向前兼容性

在 v3 API 中的相同标识下也可以找到来自 v2 的所有实体。 如果结果的架构已更改（例如听录），则 API 的 v3 版本中 GET 操作的结果将使用 v3 架构。 API v2 版本中 GET 操作的结果使用相同的 v2 架构。 在 v2 API 的响应中无法 ****  使用 v3 上新创建的实体。 

## <a name="migration-steps"></a>迁移步骤

准备迁移时需要注意的项目摘要列表如下。 详细信息可在单独的链接中找到。 根据当前使用的 API，此处列出的所有步骤并非都适用。 只有少量更改需要在调用代码中进行重大更改。 大多数更改只需要更改项目名称。 

一般更改： 

1. [更改主机名](#host-name-changes)

1. [在客户端代码中将属性 ID 重命名为 self](#identity-of-an-entity) 

1. [更改代码以便对实体集合进行迭代](#working-with-collections-of-entities)

1. [在客户端代码中将属性名称重命名为 displayName](#name-of-an-entity)

1. [调整引用实体的元数据检索方式](#accessing-referenced-entities)

1. 如果使用批量听录： 

    * [调整用于创建批量听录的代码](#creating-transcriptions) 

    * [使代码适应新的听录结果架构](#format-of-v3-transcription-results)

    * [调整结果检索方式的代码](#getting-the-content-of-entities-and-the-results)

1. 如果使用自定义模型训练/测试 API： 

    * [将修改应用于自定义模型训练](#customizing-models)

    * [更改检索基本模型和自定义模型的方式](#retrieving-base-and-custom-models)

    * [在客户端代码中将路径段 accuracytests 重命名为 evaluations](#accuracy-tests)

1. 如果使用终结点 API：

    * [更改检索终结点日志的方式](#retrieving-endpoint-logs)

1. 其他细微更改： 

    * [将所有自定义属性作为 customProperties（而不是 POST 请求中的属性）传送](#using-custom-properties)

    * [从响应头 Location（而不是 Operation-Location）读取位置](#response-headers)

## <a name="breaking-changes"></a>中断性变更

### <a name="host-name-changes"></a>主机名更改

终结点主机名已从 `{region}.cris.ai` 更改为 `{region}.api.cognitive.microsoft.com`。 新终结点的路径不再包含 `api/`，因为后者是主机名的一部分。 [Swagger 文档](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)列出了有效的区域和路径。
>[!IMPORTANT]
>将主机名从 `{region}.cris.ai` 更改为 `{region}.api.cognitive.microsoft.com`，其中的区域是语音订阅的区域。 另外，从客户端代码的任何路径中删除 `api/`。

### <a name="identity-of-an-entity"></a>实体的标识

属性 `id` 现在为 `self`。 在 v2 中，API 用户必须了解如何创建 API 上的路径。 这是不可扩展的，需要用户进行不必要的工作。 属性 `id` (uuid) 由 `self`（字符串）替换，这是实体 (URL) 的位置。 该值在所有实体之间仍然是唯一的。 如果 `id` 以字符串形式存储在代码中，则重命名足以支持新架构。 现在，可以将 `self` 内容用作实体的 `GET`、`PATCH` 和 `DELETE` REST 调用的 URL。

如果实体具有可通过其他路径提供的其他功能，则它们将在 `links` 下列出。 下面的听录示例显示 `GET` 听录内容的另一种方法：
>[!IMPORTANT]
>在客户端代码中将属性 `id` 重命名为 `self`。 根据需要将类型从 `uuid` 更改为 `string`。 

**v2 听录：**

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

**v3 听录：**

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

根据代码的实现，仅重命名该属性可能还不够。 建议将返回的 `self` 和`links` 值用作 REST 调用的目标 URL，而不是在客户端中生成路径。 通过使用返回的 URL，可以确保将来路径的更改不会破坏客户端代码。

### <a name="working-with-collections-of-entities"></a>处理实体集合

以前，v2 API 会在结果中返回所有可用的实体。 为了对 v3 中的预期响应大小进行更细粒度的控制，所有集合结果进行了分页。 你可以控制返回的实体数和页面的起始偏移。 此行为可以轻松预测响应处理器的运行时。

响应的基本形状对于所有集合都是相同的：

```json
{
    "values": [
        {     
        }
    ],
    "@nextLink": "https://{region}.api.cognitive.microsoft.com/speechtotext/v3.0/{collection}?skip=100&top=100"
}
```

`values` 属性包含一部分可用集合实体。 可以使用 `skip` 和 `top` 查询参数来控制计数和偏移。 当 `@nextLink` 不为 `null` 时，表明有更多可用数据，并且可以通过对 `$.@nextLink` 执行 GET 操作来检索下一批数据。

此更改需要对集合循环调用 `GET`，直到返回所有元素。

>[!IMPORTANT]
>当 GET 操作对 `speechtotext/v3.0/{collection}` 的响应包含 `$.@nextLink` 中的值时，在 `$.@nextLink` 设置为不检索该集合的所有元素之前，请继续对 `$.@nextLink` 发出 `GETs` 操作。

### <a name="creating-transcriptions"></a>创建听录

有关如何创建多批听录的详细说明，请参阅[批量听录操作说明](./batch-transcription.md)。

通过 v3 听录 API，可显式设置特定的听录选项。 现在可以在 `properties` 属性中设置所有（可选）配置属性。
版本 v3 还支持多个输入文件，因此它需要一列 URL，而不是像 v2 那样只需一个 URL。 v2 属性名称 `recordingsUrl` 在 v3 中现为 `contentUrls`。 v3 已在听录中删除情绪分析功能。 有关情绪分析选项，请参阅 Microsoft 认知服务[文本分析](https://azure.microsoft.com/services/cognitive-services/text-analytics/)。

`properties` 下的新属性 `timeToLive` 可帮助删除现有的已完成实体。 `timeToLive` 指定一个持续时间，过了该时间后，完成的实体将自动被删除。 当连续跟踪、使用和删除实体时，请将其设置为较高的值（例如 `PT12H`），因此通常会在 12 小时之内处理完毕。

**v2 听录 POST 请求正文：**

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

**v3 听录 POST 请求正文：**

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
>[!IMPORTANT]
>将属性 `recordingsUrl` 重命名为 `contentUrls` 并传递一组 URL，而不是单个 URL。 将 `diarizationEnabled` 或 `wordLevelTimestampsEnabled` 的设置作为 `bool`（而不是 `string`）传递。

### <a name="format-of-v3-transcription-results"></a>v3 听录结果的格式

听录结果的架构已稍作更改，以与实时终结点创建的听录保持一致。 有关新格式的详细说明，请参阅[批量听录操作说明](./batch-transcription.md)。 结果的架构在 `samples/batch/transcriptionresult_v3.schema.json` 下的 [GitHub 示例存储库](https://aka.ms/csspeech/samples)中发布。

属性名称现在采用驼峰式，而 `channel` 和 `speaker` 的值现在使用整数类型。 持续时间格式现在使用 ISO 8601 中描述的结构，该结构与其他 Azure API 中使用的持续时间格式匹配。

v3 听录结果的示例。 注释中说明了具体的差异。

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
>[!IMPORTANT]
>如上所示，将听录结果反序列化为新类型。 通过检查 `recognizedPhrases` 中每个元素的 `channel` 属性值来区分声道，而不是每个音频声道只有一个文件。 每个输入文件现在只有一个结果文件。


### <a name="getting-the-content-of-entities-and-the-results"></a>获取实体的内容和结果

在 v2 中，指向输入或结果文件的链接已与实体元数据的其余部分内联。 v3 的改进之处是，实体元数据（由对 `$.self` 执行的 GET 操作返回）与用于访问结果文件的详细信息和凭据之间存在明显的分隔。 这种分隔有助于保护客户数据，并允许对凭据有效期进行精细控制。

在 v3 中，如果实体公开数据（数据集、听录、终结点或评估），则 `links` 包含称为 `files` 的子属性。 对 `$.links.files` 执行的 GET 操作将返回一个文件列表和一个用于访问每个文件内容的 SAS URL。 为了控制 SAS URL 的有效期限，查询参数 `sasValidityInSeconds` 可用于指定生存期。

**v2 听录：**

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

**v3 听录：**

```json
{
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
    "links": {
      "files": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files"
    } 
}
```

对 `$.links.files` 执行的 GET 操作将导致：

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

`kind` 属性指示文件内容的格式。 对于听录，类型为 `TranscriptionReport` 的文件是作业的摘要，而类型为 `Transcription` 的文件是作业本身的结果。

>[!IMPORTANT]
>要获得操作结果，请对 `/speechtotext/v3.0/{collection}/{id}/files` 执行 `GET` 操作，`/speechtotext/v3.0/{collection}/{id}` 或 `/speechtotext/v3.0/{collection}` 上 `GET` 的响应中不再包含这些结果。

### <a name="customizing-models"></a>自定义模型

在 v3 之前，训练模型时，声学模型和语言模型之间是有区别的 。 这种区别导致在创建终结点或听录时需要指定多个模型。 为了简化调用方的这项流程，我们消除了差异，一切都取决于用于模型训练的数据集内容。 进行此更改后，模型创建现在支持混合数据集（语言数据和声学数据）。 终结点和听录现在只需要一个模型。

进行此更改后，`POST` 操作中便不再需要 `kind`，并且 `datasets[]` 数组现在可以包含相同类型或混合类型的多个数据集。

为了改进训练模型的结果，在进行语言训练时会在内部自动使用声学数据。 通常，通过 v3 API 创建的模型能够比使用 v2 API 创建的模型提供更准确的结果。

>[!IMPORTANT]
>要同时自定义声学和语言模型部分，请将 POST 的 `datasets[]` 中的所有所需语言和声学数据集传递到 `/speechtotext/v3.0/models`。 这将创建一个这两个部分均已自定义的模型。

### <a name="retrieving-base-and-custom-models"></a>检索基本模型和自定义模型

为了简化获取可用模型的过程，v3 将“基本模型”的集合与客户拥有的“自定义模型”分开。 这两个路由现在为 `GET /speechtotext/v3.0/models/base` 和 `GET /speechtotext/v3.0/models/`。

在 v2 中，所有模型在一个响应中一起返回。

>[!IMPORTANT]
>若要获取提供的用于自定义的基本模型列表，请对 `/speechtotext/v3.0/models/base` 使用 `GET` 操作。 可以通过对 `/speechtotext/v3.0/models` 使用 `GET` 操作来查找你自己的自定义模型。

### <a name="name-of-an-entity"></a>实体名称

`name` 属性现在为 `displayName`。 这与其他 Azure API 一致，不表示标识属性。 此属性的值不能是唯一值，并且可以在使用 `PATCH` 操作创建实体之后进行更改。

**v2 听录：**

```json
{
    "name": "Transcription using locale en-US"
}
```

**v3 听录：**

```json
{
    "displayName": "Transcription using locale en-US"
}
```

>[!IMPORTANT]
>在客户端代码中将属性 `name` 重命名为 `displayName`。

### <a name="accessing-referenced-entities"></a>访问引用的实体

在 v2 中，引用的实体始终是内联的，例如终结点的已使用模型。 实体的嵌套会生成大量的响应，而使用者很少使用嵌套的内容。 为了缩减响应大小并提高性能，引用的实体在响应中不再是内联的。 相反，会出现对另一个实体的引用，该引用可以按照与 `self` 链接相同的模式，直接用于后续的 `GET` 操作（也是 URL）。

**v2 听录：**

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

**v3 听录：**

```json
{
  "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
  "model": {
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/models/021a72d0-54c4-43d3-8254-27336ead9037"
  }
}
```

如上例所示，如果需要使用引用模型的详细信息，只需对 `$.model.self` 发出 GET 操作即可。

>[!IMPORTANT]
>若要检索引用实体的元数据，请对 `$.{referencedEntity}.self` 发出 GET 操作。例如，要检索听录模型，请对 `$.model.self` 执行 `GET` 操作。


### <a name="retrieving-endpoint-logs"></a>检索终结点日志

服务的版本 v2 支持日志记录终结点结果。 若要使用 v2 检索终结点的结果，请创建一个“数据导出”，它表示按时间范围定义的结果的快照。 导出批量数据的过程是固定的。 v3 API 提供对每个单独文件的访问权限，并允许遍历这些文件。

**一个成功运行的 v3 终结点：**

```json
{
  "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6",
  "links": {
    "logs": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6/files/logs" 
  }
}
```

**GET `$.links.logs` 的响应：**

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

终结点日志的分页与所有其他集合的工作方式相似，但无法指定偏移。 由于存在大量可用数据，分页由服务器决定。

在 v3 中，可以通过对文件的 `self` 发出 `DELETE` 操作或对 `$.links.logs` 使用 `DELETE` 操作来分别删除每个终结点日志。 若要指定结束日期，可以将查询参数 `endDate` 添加到请求中。

>[!IMPORTANT]
>使用 `/v3.0/endpoints/{id}/files/logs/` 单独访问日志文件，而不是在 `/api/speechtotext/v2.0/endpoints/{id}/data` 上创建日志导出。 

### <a name="using-custom-properties"></a>使用自定义属性

若要将自定义属性与可选配置属性分离，所有显式命名的属性现在都位于 `properties` 属性中，而调用方定义的所有属性现在都位于 `customProperties` 属性中。

**v2 听录实体：**

```json
{
  "properties": {
    "customerDefinedKey": "value",
    "diarizationEnabled": "False",
    "wordLevelTimestampsEnabled": "False"
  }
}
```

**v3 听录实体：**

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

通过此更改还可以对 `properties` 下的所有显式命名的属性使用正确的类型（例如，使用布尔值而不是字符串）。

>[!IMPORTANT]
>在 `POST` 请求中，将所有自定义属性作为 `customProperties`（而不是 `properties`）传递。

### <a name="response-headers"></a>响应头

除了 `POST` 请求上的 `Location` 标头之外，v3 不再返回 `Operation-Location` 标头。 v2 中两个标头的值相同。 现在仅返回 `Location`。

由于新的 API 版本现在由 Azure API 管理 (APIM) 管理，因此响应头中不再包含与限制相关的标头 `X-RateLimit-Limit`、`X-RateLimit-Remaining` 和 `X-RateLimit-Reset`。

>[!IMPORTANT]
>从响应头 `Location`（而不是 `Operation-Location`）读取位置。 如果是 429 响应代码，请读取 `Retry-After` 标头值，而不读取 `X-RateLimit-Limit`、`X-RateLimit-Remaining` 或 `X-RateLimit-Reset`。


### <a name="accuracy-tests"></a>准确度测试

准确度测试已重命名为评估，因为新名称可以更好地描述其所表示的含义。 新路径为：`https://{region}.api.cognitive.microsoft.com/speechtotext/v3.0/evaluations`。

>[!IMPORTANT]
>在客户端代码中将路径段 `accuracytests` 重命名为 `evaluations`。


## <a name="next-steps"></a>后续步骤

检查语音服务提供的以下常用 REST API 的所有功能：

* [语音转文本 REST API](rest-speech-to-text.md)
* 适用于 REST API v3 的 [Swagger 文档](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)
* 有关用于执行批量听录的示例代码，请查看 `samples/batch` 子目录中的 [GitHub 示例存储库](https://aka.ms/csspeech/samples)。
