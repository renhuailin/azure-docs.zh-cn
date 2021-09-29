---
title: 使用 Azure 视频分析器媒体版（以前称为视频索引器）搜索视频中的确切时刻
description: 了解如何使用 Azure 视频分析器媒体版（以前称为视频索引器）搜索视频中的确切时刻。
ms.topic: how-to
ms.date: 11/23/2019
ms.author: juliako
ms.openlocfilehash: 454c5a8033e30a10183d03a1689b6e606bcb77ba
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128607513"
---
# <a name="search-for-exact-moments-in-videos-with-video-analyzer-for-media"></a>使用视频分析器媒体版搜索视频中的确切时刻

本主题介绍如何使用 Azure 视频分析器媒体版（以前称为视频索引器）网站搜索视频中的确切时刻。

1. 转到[视频分析器媒体版](https://www.videoindexer.ai/)网站并登录。
1. 指定搜索关键字，系统将在帐户库中的所有视频中执行搜索。 

    可以通过选择“筛选”来筛选搜索内容。 在以下示例中，我们搜索仅显示为屏幕文本的“Microsoft”(OCR)。

    :::image type="content" source="./media/video-indexer-search/filter.png" alt-text="筛选，仅文本":::
1. 按“搜索”查看结果。

    :::image type="content" source="./media/video-indexer-search/results.png" alt-text="视频搜索结果":::

    如果选择某个结果，播放机会将你带到视频中的相应时刻。
1. 通过单击视频上的“播放”或选择一个原始搜索结果来查看和搜索视频的汇总见解。 

    可以查看、搜索和编辑见解。 如果选择某个见解，播放机会将你带到视频中的相应时刻。  

    :::image type="content" source="./media/video-indexer-search/insights.png" alt-text="查看、搜索和编辑视频见解":::

    如果通过视频分析器媒体版小组件嵌入视频，则可以在应用中实现播放机/见解视图和同步。 有关详细信息，请参阅[在应用中嵌入视频分析器媒体版小组件](video-indexer-embed-widgets.md)。
1. 可以通过单击“时间线”选项卡，来查看、搜索和编辑脚本。 

    :::image type="content" source="./media/video-indexer-search/timeline.png" alt-text="查看、搜索和编辑视频脚本":::

    若要编辑文本，请选择右上角的“编辑”，并根据需要更改文本。 

    还可以通过从右上角选择适当的选项来转换和下载脚本。 

## <a name="embed-download-create-projects"></a>嵌入、下载、创建项目

可以通过选择视频下的“</>嵌入”来嵌入视频。 有关详细信息，请参阅[将视觉小组件嵌入应用程序](video-indexer-embed-widgets.md)。

可以通过单击视频下的“下载”，来下载源视频、视频见解和脚本。

可以通过单击“在编辑器中打开”，根据视频创建特定行和时刻的剪辑。 然后编辑视频并保存项目。 有关详细信息，请参阅[使用视频的深入见解](use-editor-create-project.md)。

:::image type="content" source="./media/video-indexer-search/embed-download-create-projects.png" alt-text="嵌入、下载、创建视频项目":::

## <a name="next-steps"></a>后续步骤

[使用视频分析器媒体版 REST API 处理内容](video-indexer-use-apis.md)
