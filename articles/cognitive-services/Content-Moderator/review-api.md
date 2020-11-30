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
ms.openlocfilehash: e2d87d4322ac6b91a19a4775c23ceec75d528030
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96325326"
---
# <a name="content-moderation-reviews-workflows-and-jobs"></a>内容审核审阅、工作流和作业

内容审查器将计算机辅助的审核与人机回环功能相结合，为真实场景创建最佳审核流程。 它通过基于云的[审阅工具](https://contentmoderator.cognitive.microsoft.com)来完成此操作。 在本指南中，你将了解评审工具的核心概念：评审、工作流和作业。

## <a name="reviews"></a>审阅

在评审中，内容已上传到查看工具。 可以通过单击仪表板上 " **查看** " 选项卡上的内容类型来查看它。 在 "查看" 屏幕中，你可以更改应用的标记并根据需要应用你自己的自定义标记。 提交评审后，结果将发送到指定的回调终结点，并从网站中删除内容。

> [!div class="mx-imgBorder"]
> !["审阅" 下拉菜单将突出显示。 其中显示了这些内容类型：图像、文本和视频。](./Review-Tool-user-Guide/images/review-tab.png)

### <a name="manage-reviews"></a>管理审阅

在仪表板中，导航到 "**管理员**" "  ->  **管理**" "查看" 以查看管理屏幕。 在此处，你可以看到所有评审的列表 ("挂起" 和 "已完成") 。

每次审阅时，可以通过三点 **操作** 按钮查看 "查看" 屏幕或检查该审阅的历史记录。

> [!div class="mx-imgBorder"]
> ![查看工具网站上的 "查看" 屏幕](./Review-Tool-user-Guide/images/manage-reviews.png)

使用 " **搜索** " 工具栏，按多种类别（如审阅状态、标记、内容类型、子团队、已分配的用户和创建/修改日期）对评审进行排序。

> [!div class="mx-imgBorder"]
> ![将显示 "搜索" 工具栏。 它有多个用于输入搜索条件的组合框，如审阅状态和标记。](./Review-Tool-user-Guide/images/review-search.png)

若要开始创建审阅，请参阅[审阅工具指南](./review-tool-user-guide/review-moderated-images.md)；若要了解如何以编程方式创建审阅，请参阅 [REST API 指南](./try-review-api-review.md)。

## <a name="workflows"></a>工作流

工作流是一种基于云的自定义内容筛选器。 工作流可以连接到多种服务，以通过不同的方式筛选内容，然后采取相应操作。 使用内容审查器连接器，工作流可以自动应用审核标记并使用提交的内容创建审阅。

### <a name="view-workflows"></a>查看工作流

若要查看现有工作流，请单击 "[查看" 工具](https://contentmoderator.cognitive.microsoft.com/)，然后选择 "**管理**  >  **工作流**"。

> [!div class="mx-imgBorder"]
> ![默认工作流](images/default-workflow-list.png)

工作流定义为 JSON 字符串，可通过编程方式进行访问。 如果为工作流选择“编辑”选项，然后选择“JSON”选项卡，则会看到类似以下内容的 JSON 表达式：

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

若要开始创建和使用工作流，请参阅[审阅工具指南](./review-tool-user-guide/workflows.md)；若要了解如何以编程方式创建审阅，请参阅 [REST API 指南](./try-review-api-workflow.md)。

## <a name="jobs"></a>作业

裁决作业作为内容审核、工作流和评论功能的一种包装。 该作业使用内容审查器图像审核 API 或文本审核 API 扫描内容，然后根据指定的工作流对其进行检查。 根据工作流的结果，它可能会也可能不会在[审阅工具](./review-tool-user-guide/human-in-the-loop.md)中为内容创建审阅。 审阅和工作流可以通过其各自的 API 创建和配置，而作业 API 允许获取整个流程的详细报告（可以将其发送到指定的回调终结点）。

若要开始使用作业，请参阅 [REST API 指南](./try-review-api-job.md)。

## <a name="next-steps"></a>后续步骤

* 试用[作业 API 控制台](try-review-api-job.md)，并运行 REST API 代码示例。 如果熟悉 Visual Studio 和 C#，还请参阅[作业 .NET 快速入门](moderation-jobs-quickstart-dotnet.md)。 
* 对于审阅，请开始使用[审阅 API 控制台](try-review-api-review.md)，并运行 REST API 代码示例。 然后，请参阅 [.NET 快速入门](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)的审阅部分。
* 对于视频审阅，请参阅[视频审阅快速入门](video-reviews-quickstart-dotnet.md)，并了解如何[向视频审阅添加脚本](video-transcript-reviews-quickstart-dotnet.md)。