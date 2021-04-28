---
title: 对自定义转换进行编码 - .NET
description: 本主题介绍如何使用 .NET 通过 Azure 媒体服务 v3 对自定义转换进行编码。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: d4877bd1b4f3eec6eb5f192f2db5040cc31a69eb
ms.sourcegitcommit: bd1a4e4df613ff24e954eb3876aebff533b317ae
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2021
ms.locfileid: "107930328"
---
# <a name="how-to-encode-with-a-custom-transform---net"></a>如何对自定义转换进行编码 - .NET

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

使用 Azure 媒体服务进行编码时，可以根据[流式传输文件](stream-files-tutorial-with-api.md)教程中演示的行业最佳做法，使用推荐的内置预设之一快速入门。 也可以构建自定义预设以针对特定方案或设备要求。

## <a name="considerations"></a>注意事项

创建自定义预设时，请注意以下事项：

* AVC 内容上的所有高度和宽度值必须是 4 的倍数。
* 在 Azure 媒体服务 v3 中，所有编码比特率均以每秒比特数为单位。 这与我们的 v2 API 的预设不同，后者使用 千比特/秒作为单位。 例如，如果 v2 中的比特率指定为 128（千比特/秒），则在 v3 中它将设置为 128000（比特/秒）。

## <a name="prerequisites"></a>先决条件

[创建媒体服务帐户](./account-create-how-to.md)

## <a name="download-the-sample"></a>下载示例

使用以下命令将包含完整 .NET Core 示例的 GitHub 存储库克隆到计算机：  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet.git
 ```
 
自定义预设示例位于[通过 .NET 使用自定义预设进行编码](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESCustomPreset_H264)文件夹中。

## <a name="create-a-transform-with-a-custom-preset"></a>使用自定义预设创建转换

创建新的[转换](/rest/api/media/transforms)时，需要指定希望生成的输出内容。 所需参数是 [TransformOutput](/rest/api/media/transforms/createorupdate#transformoutput) 对象，如以下代码所示。 每个 TransformOutput 包含一个预设   。 **预设** 介绍了视频和/或音频处理操作的分步说明，这些操作将用于生成所需的 **TransformOutput**。 以下 **TransformOutput** 创建自定义编解码器和层输出设置。

在创建时 [转换](/rest/api/media/transforms)，首先应检查是否其中一个已存在使用 **获取** 方法，如下面的代码中所示。 在媒体服务 v3 中，如果实体不存在（对名称进行不区分大小写检查），实体上的 **Get** 方法将返回 **null**。

## <a name="next-steps"></a>后续步骤

[流式传输文件](stream-files-tutorial-with-api.md) 
