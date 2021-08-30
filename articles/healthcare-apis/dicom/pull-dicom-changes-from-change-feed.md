---
title: 使用更改源拉取 DICOM 更改
description: 本操作指南说明如何使用适用于 Azure Healthcare APIs 的 DICOM 更改源拉取 DICOM 更改。
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 08/04/2021
ms.author: aersoy
ms.openlocfilehash: fa0237a57f5ebb8df0a69fa715a36d963ea0748f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778017"
---
# <a name="pull-dicom-changes-using-the-change-feed"></a>使用更改源拉取 DICOM 更改

> [!IMPORTANT]
> Azure Healthcare APIs 目前为预览版。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

DICOM 更改源使客户能够浏览 DICOM 服务历史记录，并针对服务中的创建和删除事件采取行动。 本操作指南说明如何使用更改源。

使用 REST API 访问更改源。 这些 API 以及更改源的示例用法位于 [DICOM 更改源概述](dicom-change-feed-overview.md)中。 应在请求 URL 中明确指定 REST API 的版本，如 [DICOM 服务文档的 API 版本控制](api-versioning-dicom-service.md)中所示。

## <a name="consume-change-feed"></a>使用更改源

以下 C# 代码示例演示如何利用 DICOM 客户端包来使用更改源。

```csharp
const int limit = 10;
 
using HttpClient httpClient = new HttpClient { BaseAddress = new Uri("<URL>") };
using CancellationTokenSource tokenSource = new CancellationTokenSource();
 
int read;
List<ChangeFeedEntry> entries = new List<ChangeFeedEntry>();
DicomWebClient client = new DicomWebClient(httpClient);
do
{
    read = 0;
    DicomWebAsyncEnumerableResponse<ChangeFeedEntry> result = await client.GetChangeFeed(
        $"?offset={entries.Count}&limit={limit}&includeMetadata={true}",
        tokenSource.Token);
 
    await foreach (ChangeFeedEntry entry in result)
    {
        read++;
        entries.Add(entry);
    }
} while (read > 0);
```

要查看和访问 ChangeFeedRetrieveService.cs 代码示例，请参阅[使用更改源](https://github.com/microsoft/dicom-server/blob/main/converter/dicom-cast/src/Microsoft.Health.DicomCast.Core/Features/DicomWeb/Service/ChangeFeedRetrieveService.cs)。

## <a name="next-steps"></a>后续步骤

本操作指南说明如何使用更改源。 更改源使你能够监视 DICOM 服务的历史记录。 有关 DICOM 服务的信息，请参阅

>[!div class="nextstepaction"]
>[DICOM 服务概述](dicom-services-overview.md)
