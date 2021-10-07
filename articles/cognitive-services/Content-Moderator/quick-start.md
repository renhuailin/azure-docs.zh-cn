---
title: 快速入门：尝试在 Web 上使用内容审查器
titleSuffix: Azure Cognitive Services
description: 本快速入门将使用联机内容审查器评审工具来测试内容审查器的基本功能，且无需编写任何代码。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 09/28/2021
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: 内容审查器，内容审核
ms.openlocfilehash: 0f600e2c0a7364088e68c70cd6eac90ee31c1b70
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129361142"
---
# <a name="quickstart-try-content-moderator-on-the-web"></a>快速入门：尝试在 Web 上使用内容审查器

[!INCLUDE [deprecation notice](includes/tool-deprecation.md)]

本快速入门将使用联机内容审查器评审工具来测试内容审查器的基本功能，且无需编写任何代码。 若要更快速地将此服务集成到内容审核应用中，请参阅[后续步骤](#next-steps)部分中的其他快速入门。

## <a name="prerequisites"></a>先决条件

- Web 浏览器

## <a name="set-up-the-review-tool"></a>设置评审工具
内容审查器评审工具是一个基于 Web 的工具，可让评审人员协助认知服务做出决策。 在本指南中，你将执行一个简短的过程来设置评审工具，以便可以了解内容审查器服务的工作原理。 转到[内容审查器评审工具](https://contentmoderator.cognitive.microsoft.com/)站点并注册。

![内容审查器主页](images/homepage.PNG)

## <a name="create-a-review-team"></a>创建评审团队

接下来创建评审团队。 在工作场景中，此团队是要手动评审服务审查决策的一组人员。 若要创建团队，需要选择一个区域，并提供团队名称和团队 ID  。 若要邀请同事加入团队，可在此处输入他们的电子邮件地址。

> [!NOTE]
> 团队名称是评审团队的易记名称。 此名称将显示在 Azure 门户中。 团队 ID 用于程序化标识评审团队。

> [!div class="mx-imgBorder"]
> ![邀请团队成员](images/create-team.png)

如果选择使用客户管理的密钥 (CMK) 加密数据，系统将提示你输入 E0 定价层中内容审查器资源的资源 ID。 你提供的资源必须是此团队独有的资源。 

> [!div class="mx-imgBorder"]
> ![使用 CMK 邀请团队成员](images/create-team-cmk.png)

## <a name="upload-sample-content"></a>上传示例内容

现在，可以上传示例内容。 选择“尝试 > 图像”、“尝试 > 文本”或“尝试 > 视频”。  

> [!div class="mx-imgBorder"]
> ![请尝试图像或文本审核](images/tryimagesortext.png)

提交内容进行审查。 可使用以下示例文本内容：

```
Is this a grabage email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
Crap is the profanity here. Is this information PII? phone 4255550111
```

在内部，评审工具将调用审查 API 来扫描该内容。 扫描完成后，会出现一条消息，告知有结果等待你的评审。

> [!div class="mx-imgBorder"]
> ![审核文件](images/submitted.png)

## <a name="review-moderation-tags"></a>评审审查标记

评审已应用的审查标记。 可以查看哪些标记已应用到内容，以及每个类别的评分。 请参阅[图像](image-moderation-api.md)、[文本](text-moderation-api.md)和[视频](video-moderation-api.md)审核文章，详细了解不同内容标记指示的内容。

<!-- ![Review results](images/reviewresults_text.png) -->

在项目中，你或评审团队可以更改这些标记，或根据需要添加更多的标记。 单击“下一步”按钮提交这些更改。 当业务应用程序调用审查器 API 时，标记的内容将在此处排队，等待评审人员团队进行评审。 可以使用此方法快速评审大量内容。

现在，你已使用内容审查器评审工具来通过示例了解了内容审查器服务的功能。 接下来，可以详细了解评审工具，以及如何使用评审 API 将它集成到软件项目，或者，可以跳转到[后续步骤](#next-steps)部分来了解如何在应用中单独使用审查 API。

## <a name="learn-more-about-the-review-tool"></a>详细了解评审工具

若要详细了解如何使用内容审查器评审工具，请查看[审核工具](Review-Tool-User-Guide/human-in-the-loop.md)指南，并参阅审核工具 API 以了解如何优化人工审核体验：
- [作业 API](try-review-api-job.md) 使用审核 API 审查你的内容，并在评审工具中生成评论。 
- [评审 API](try-review-api-review.md) 无需先扫描内容即可直接为人工审查器创建图像、文本或视频评论。 
- [工作流 API](try-review-api-workflow.md) 可创建、更新和获取有关团队创建的自定义工作流的详细信息。

或者继续执行后续步骤，在代码中开始使用审查 API。

## <a name="next-steps"></a>后续步骤

了解如何在应用中单独使用审查 API。
- 实施图像审查。 使用 [API 控制台](try-image-api.md)或遵循[快速入门](client-libraries.md)，使用标记、置信度评分和提取的其他信息扫描图像并检测潜在的成人和猥亵内容。
- 实施文本审查。 使用 [API 控制台](try-text-api.md)或遵循[快速入门](client-libraries.md)，扫描文本内容，查找潜在的粗俗语言、个人数据和其他不需要的文本。
- 实施视频审查。 请按照[适用于 C# 的视频审核操作指南](video-moderation-api.md)扫描视频并检测潜在的成人和猥亵内容。 
