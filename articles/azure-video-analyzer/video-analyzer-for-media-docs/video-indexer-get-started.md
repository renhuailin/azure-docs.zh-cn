---
title: 注册 Azure 视频分析器媒体版（原视频索引器）并上传第一个视频 - Azure
description: 了解如何使用 Azure 视频分析器媒体版（原视频索引器）门户注册并上传第一个视频。
ms.topic: quickstart
ms.subservice: azure-video-analyzer-media
ms.date: 01/25/2021
ms.author: juliako
ms.openlocfilehash: bf8b8f640fbcb6155092a50fdbd6e1af66060e07
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128636147"
---
# <a name="quickstart-how-to-sign-up-and-upload-your-first-video"></a>快速入门：如何注册并上传第一个视频

本快速入门介绍如何登录到 Azure 视频分析器媒体版（原视频索引器）网站，以及如何上传第一个视频。

创建视频分析器媒体版帐户时，可以选择免费试用帐户（提供特定分钟数的免费索引时间）或付费选项（不受配额的限制）。 使用免费试用版时，视频分析器媒体版为网站用户提供最多 600 分钟的免费索引，为 API 用户提供最多 2400 分钟的免费索引。 使用付费选项时，可以[创建连接到 Azure 订阅和 Azure 媒体服务帐户的视频分析器媒体版帐户](connect-to-azure.md)。 你需要为索引时间付费，有关详细信息，请参阅[媒体服务定价](https://azure.microsoft.com/pricing/details/media-services/)。 

## <a name="sign-up-for-video-analyzer-for-media"></a>注册视频分析器媒体版

若要开始通过视频分析器媒体版进行开发，请浏览到[视频分析器媒体版](https://www.videoindexer.ai/)网站并进行注册。

开始使用视频分析器媒体版后，所有存储的数据和上传的内容都将使用 Microsoft 托管密钥进行静态加密。

> [!NOTE]
> 请查看[计划的视频分析器媒体版网站身份验证更改](./release-notes.md#planned-video-analyzer-for-media-website-authenticatication-changes)。

## <a name="upload-a-video-using-the-video-analyzer-for-media-website"></a>使用视频分析器媒体版网站上传视频

### <a name="supported-file-formats-for-video-analyzer-for-media"></a>视频分析器媒体版支持的文件格式

有关可用于视频分析器媒体版的文件格式列表，请参阅[输入容器/文件格式](../../media-services/latest/encode-media-encoder-standard-formats-reference.md)一文。

### <a name="upload-a-video"></a>上传视频

1. 登录到[视频分析器媒体版](https://www.videoindexer.ai/)网站。
1. 若要上传视频，请按“上传”按钮或链接。 

    > [!NOTE]
    > 视频的名称长度不得超过 80 个字符。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/video-indexer-upload.png" alt-text="上载":::
1. 视频上传以后，视频分析器媒体版就会开始对视频进行索引编制和分析。 你会看到进度。 

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/progress.png" alt-text="上传进度":::
1. 当视频分析器媒体版分析完成后，你会收到一封电子邮件，其中包含视频链接以及对视频中找到的内容的简短说明。 例如：人员、口语和书面语、主题和命名实体。
1. 稍后可以在库列表中找到视频并执行不同的操作。 例如：搜索、重新索引、编辑。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/uploaded.png" alt-text="已上传内容":::

## <a name="supported-browsers"></a>支持的浏览器

有关详细信息，请参阅[支持的浏览器](video-indexer-overview.md#supported-browsers)。

## <a name="see-also"></a>另请参阅

有关更多详细信息，请参阅[上传视频并为其编制索引](upload-index-videos.md)。

上传视频并为其编制索引后，可以开始使用[视频分析器媒体版网站](video-indexer-view-edit.md)或[视频分析器媒体版开发人员门户](video-indexer-use-apis.md)查看视频见解。 

[开始使用 API](video-indexer-use-apis.md)

## <a name="next-steps"></a>后续步骤

有关详细介绍，请访问我们的[简介实验室](https://github.com/Azure-Samples/media-services-video-indexer/blob/master/IntroToVideoIndexer.md)。 

在研讨会结束时，你将对可以从视频和音频内容中提取的信息类型有一个很好的了解，你将做好更充分的准备来识别与内容智能相关的机会、在 Azure 上推介视频 AI，并在视频分析器媒体版上演示几个场景。

