---
title: 了解审阅工具的概念 - 内容审查器
titleSuffix: Azure Cognitive Services
description: 了解内容审查器审阅工具，该工具是一个网站，可协调 AI 和人工审阅的审查工作。
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.date: 03/15/2019
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.author: pafarley
ms.openlocfilehash: ddf4d17cd2631f5bc299e392c46bd9065bb13744
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "93146601"
---
# <a name="content-moderator-review-tool"></a>内容审查器审阅工具

Azure 内容审查器提供将机器学习内容审核与人工审阅相结合的服务。 [审阅工具](https://contentmoderator.cognitive.microsoft.com)网站是一个用户友好的前端，提供对这些服务的精细访问权限。

## <a name="what-it-does"></a>作用

当[审阅工具](https://contentmoderator.cognitive.microsoft.com)与计算机辅助的审查 API 一起使用时，可以完成内容审查过程中涉及的以下任务：

- 使用一组工具审查多种格式（文本、图像和视频）的内容。
- 收到审查 API 结果时，自动创建人工[审阅](../review-api.md#reviews)。
- 将内容审阅分配给或呈报给根据内容类别或经验级别组织的多个审阅团队。
- 使用默认或自定义逻辑筛选器（[工作流](../review-api.md#workflows)）在不编写任何代码的情况下对内容进行排序和跟踪。
- 除内容审查器 API 以外，还可以使用 Microsoft PhotoDNA、Text Analytics 和 Face 服务来配合[连接器](./configure.md#connectors)处理内容。
- 在内容审核过程中获得关键性能指标。

## <a name="review-tool-dashboard"></a>审阅工具仪表板

在“仪表板”选项卡上，可以查看在该工具中完成的内容审阅的关键指标。 查看图像、文本和视频内容的总审阅数、已审阅数和待审阅数。 

“待审阅项”表显示了尚待审阅或已完成审阅的用户和子团队的明细，以及剩余的 SLA 时间。 你可以选择表中的项以转到其审阅数。 使用表上方的搜索框可按团队名称筛选结果，使用“筛选器”图标可按照其他指标进行筛选。

切换到“已完成的审阅”选项卡，可显示用户和子团队已处理或完成的总项数。 可以像筛选待处理的审阅一样筛选此数据。

单击仪表板右上角的文本将显示每日个人指标，该指标报告针对每种内容类型完成的审阅数。

> [!div class="mx-imgBorder"]
> ![浏览器中的“审阅工具”仪表板](images/0-dashboard.png)

## <a name="review-tool-credentials"></a>审阅工具凭据

注册[审阅工具](https://contentmoderator.cognitive.microsoft.com)时，系统将提示你为帐户选择 Azure 区域。 这是因为[审阅工具](https://contentmoderator.cognitive.microsoft.com)会为 Azure 内容审查器服务生成一个免费试用密钥。 需要此密钥才能从 REST 调用或客户端 SDK 访问其中任何服务。 可以通过选择“管理员” > “凭据”，查看密钥和 API 终结点 URL 。

> [!div class="mx-imgBorder"]
> ![内容审查器“凭据”设置](images/settings-6-credentials.png)

## <a name="next-steps"></a>后续步骤

请参阅[配置审阅工具](./configure.md)，了解如何访问审阅工具资源和更改设置。