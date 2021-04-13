---
title: 审阅、工作流和作业概念 - 内容审查器
titleSuffix: Azure Cognitive Services
description: 本文介绍审阅工具（审阅、工作流和作业）的核心概念。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: pafarley
ms.openlocfilehash: 43c39c40af6e02861211a8666fefa57c34072f32
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "96905190"
---
# <a name="content-moderation-reviews-workflows-and-jobs"></a>内容审核审阅、工作流和作业

内容审查器将计算机辅助的审核与人机回环功能相结合，为真实场景创建最佳审核流程。 它通过基于云的[审阅工具](https://contentmoderator.cognitive.microsoft.com)来完成此操作。 本指南介绍审阅工具的核心概念：审阅、工作流和作业。

## <a name="reviews"></a>审阅

在审阅中，内容已上传到审阅工具。 可以通过在仪表板上的“审阅”选项卡下单击其内容类型来查看它。 在“审阅”屏幕上，你可以更改已应用的标签，并根据需要应用自己的自定义标签。 当你提交审阅时，系统会将结果发送到指定的回调终结点，并从站点中删除内容。

> [!div class="mx-imgBorder"]
> ![突出显示“审阅”下拉菜单。 其中显示了这些内容类型：图像、文本和视频。](./Review-Tool-user-Guide/images/review-tab.png)

### <a name="manage-reviews"></a>管理审阅

在仪表板中，导航到“管理” -> “管理审阅”以查看管理屏幕。 可在此处看到所有审阅（待审阅和已完成审阅）的列表。

每个审阅上的三点式“操作”按钮可让你转到审阅屏幕或检查该审阅的历史记录。

> [!div class="mx-imgBorder"]
> ![审阅工具网站上的“审阅”屏幕](./Review-Tool-user-Guide/images/manage-reviews.png)

使用“搜索”工具栏可以按各种类别（如审阅状态、标记、内容类型、子团队、已分配的用户和创建/修改日期）对审阅进行排序。

> [!div class="mx-imgBorder"]
> ![显示“搜索”工具栏。 它有多个用于输入搜索条件的组合框，例如“审阅状态”和“标记”。](./Review-Tool-user-Guide/images/review-search.png)

若要开始创建审阅，请参阅[审阅工具指南](./review-tool-user-guide/review-moderated-images.md)；若要了解如何以编程方式创建审阅，请参阅 [API 控制台指南](./try-review-api-review.md)。

## <a name="workflows"></a>工作流

工作流是一种基于云的自定义内容筛选器。 工作流可以连接到多种服务，以通过不同的方式筛选内容，然后采取相应操作。 使用内容审查器连接器，工作流可以自动应用审核标记并使用提交的内容创建审阅。

### <a name="view-workflows"></a>查看工作流

若要查看现有工作流，请转到[审阅工具](https://contentmoderator.cognitive.microsoft.com/)，然后选择“管理” > “工作流”。

> [!div class="mx-imgBorder"]
> ![默认工作流](images/default-workflow-list.png)

采用 JSON 字符串的形式定义工作流，这样就能够以编程方式访问工作流。 如果为工作流选择“编辑”选项，然后选择“JSON”选项卡，则会看到类似以下内容的 JSON 表达式：

```json
{
    "Type": "Logic",
    "If": {
        "ConnectorName": "moderator",
        "OutputName": "isAdult",
        "Operator": "eq",
        "Value": "true",
        "Type": "Condition"
        },
    "Then": {
    "Perform": [
    {
        "Name": "createreview",
        "CallbackEndpoint": null,
        "Tags": []
    }
    ],
    "Type": "Actions"
    }
}
```

若要开始创建和使用工作流，请参阅[审阅工具指南](./review-tool-user-guide/workflows.md)；若要了解如何以编程方式创建审阅，请参阅 [API 控制台指南](./try-review-api-workflow.md)。

## <a name="jobs"></a>作业

审核作业充当内容审核、工作流和审阅功能的一种包装器。 该作业使用内容审查器图像审核 API 或文本审核 API 扫描内容，然后根据指定的工作流对其进行检查。 根据工作流的结果，它可能会也可能不会在[审阅工具](./review-tool-user-guide/human-in-the-loop.md)中为内容创建审阅。 审阅和工作流可以通过其各自的 API 创建和配置，而作业 API 允许获取整个流程的详细报告（可以将其发送到指定的回调终结点）。

若要开始使用作业，请参阅 [API 控制台指南](./try-review-api-job.md)。

## <a name="next-steps"></a>后续步骤

* 试用[作业 API 控制台](try-review-api-job.md)，并运行 REST API 代码示例。 如果熟悉 Visual Studio 和 C#，还请参阅[作业 .NET 快速入门](moderation-jobs-quickstart-dotnet.md)。 
* 对于审阅，请开始使用[审阅 API 控制台](try-review-api-review.md)，并运行 REST API 代码示例。 然后，请参阅 [.NET 快速入门](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)的审阅部分。
* 对于视频审阅，请参阅[视频审阅快速入门](video-reviews-quickstart-dotnet.md)，并了解如何[向视频审阅添加脚本](video-transcript-reviews-quickstart-dotnet.md)。