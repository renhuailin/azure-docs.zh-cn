---
title: 在视频中搜索视频索引器的确切时间
titleSuffix: Azure Media Services
description: 了解如何使用视频索引器在视频中搜索确切的时间。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/23/2019
ms.author: juliako
ms.openlocfilehash: a1ca7c677e5f1eb55d74cca45e757676674f303c
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "96030492"
---
# <a name="search-for-exact-moments-in-videos-with-video-indexer"></a>在视频中搜索视频索引器的确切时间

本主题说明如何使用视频索引器网站在视频中搜索确切的时间。

1. 请参阅 [视频索引器](https://www.videoindexer.ai/) 网站并登录。
1. 指定搜索关键字，搜索将在帐户的库中的所有视频之间执行。 

    可以通过选择 " **筛选** 器" 来筛选搜索。 在下面的示例中，我们搜索仅 (OCR) 显示为屏幕文本的 "Microsoft"。

    :::image type="content" source="./media/video-indexer-search/filter.png" alt-text="筛选器，仅文本":::
1. 按 " **搜索** " 查看结果。

    :::image type="content" source="./media/video-indexer-search/results.png" alt-text="视频搜索结果":::

    如果选择其中一个结果，播放机将进入视频。
1. 通过单击视频上的 " **播放** " 或选择一个原始搜索结果来查看和搜索视频的汇总见解。 

    您可以查看、搜索和编辑 **见解**。 选择其中一个见解后，播放机将进入视频。  

    :::image type="content" source="./media/video-indexer-search/insights.png" alt-text="查看、搜索和编辑视频见解":::

    如果通过视频索引器小组件嵌入视频，则可以在应用程序中实现播放机/见解视图和同步。 有关详细信息，请参阅 [将视频索引器小组件嵌入应用](video-indexer-embed-widgets.md)。
1. 通过单击 " **时间线** " 选项卡，可以查看、搜索和编辑这些脚本。 

    :::image type="content" source="./media/video-indexer-search/timeline.png" alt-text="查看、搜索和编辑视频的脚本":::

    若要编辑文本，请从右上角选择 " **编辑** "，并根据需要更改文本。 

    你还可以通过从右上角选择适当的选项来翻译和下载这些脚本。 

## <a name="embed-download-create-projects"></a>嵌入、下载、创建项目

可以通过选择视频下的 **</>嵌入** 视频来嵌入视频。 有关详细信息，请参阅 [在应用程序中嵌入可视小组件](video-indexer-embed-widgets.md)。

可以通过单击视频下的 " **下载** "，下载源视频、视频见解、脚本。

可以通过单击 " **在编辑器中打开**"，基于特定线路和时间的视频创建剪辑。 然后编辑视频并保存该项目。 有关详细信息，请参阅 [使用视频深入见解](use-editor-create-project.md)。

:::image type="content" source="./media/video-indexer-search/embed-download-create-projects.png" alt-text="嵌入、下载、创建视频项目":::

## <a name="next-steps"></a>后续步骤

[用视频索引器处理内容 REST API](video-indexer-use-apis.md)
