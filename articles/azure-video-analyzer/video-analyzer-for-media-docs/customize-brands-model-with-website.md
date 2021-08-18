---
title: 使用 Azure 视频分析器媒体版（以前称为视频索引器）网站自定义品牌模型
titleSuffix: Azure Video Analyzer for Media
description: 了解如何使用 Azure 视频分析器媒体版（以前称为视频索引器）网站自定义品牌模型。
services: azure-video-analyzer
author: anikaz
manager: johndeu
ms.topic: article
ms.subservice: azure-video-analyzer-media
ms.date: 12/15/2019
ms.author: kumud
ms.openlocfilehash: 5bb1a6317ad946da4e7fc00068a7fac40c45d772
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/15/2021
ms.locfileid: "112121446"
---
# <a name="customize-a-brands-model-with-the-video-analyzer-for-media-website"></a>使用视频分析器媒体版网站自定义品牌模型

在对视频和音频内容进行索引和重新索引的过程中，可以使用 Azure 视频分析器媒体版（以前为视频索引器）从语音和视觉文本中检测品牌。 品牌检测功能可以确定内容中是否提到必应品牌数据库建议的产品、服务和公司。 例如，如果某个视频或音频内容中提到了 Microsoft，或者 Microsoft 出现在某个视频的视觉文本中，视频分析器媒体版就会将其作为内容中的品牌检测出来。

使用自定义品牌模型，你可以：

- 选择是否想要视频分析器媒体版从必应品牌数据库中检测品牌。
- 选择是否想要视频分析器媒体版将某些品牌排除在检测范围之外（实质上是创建一个包含品牌的拒绝列表）。
- 选择是否想要视频分析器媒体版包括应属于你的模型但可能不在必应的品牌数据库中的品牌（实质上是创建一个包含品牌的接受列表）。

如需详细的概述，请参阅[此概述](customize-brands-model-overview.md)。

可以使用视频分析器媒体版网站来创建、使用和编辑视频中检测到的自定义品牌模型，如本主题所述。 也可使用 API，如[使用 API 自定义品牌模型](customize-brands-model-with-api.md)中所述。

> [!NOTE]
> 如果在添加品牌之前已为视频编制了索引，则需要对其重新编制索引。 你会在与视频关联的下拉菜单中找到“重新索引”项。 选择“高级选项” -> “品牌类别”，并选中“所有品牌”。

## <a name="edit-brands-model-settings"></a>编辑品牌模型设置

可以选择设置是否需要检测必应品牌数据库中的品牌。 若要设置此选项，需要编辑品牌模型的设置。 执行以下步骤:

1. 转到[视频分析器媒体版](https://www.videoindexer.ai/)网站并登录。
1. 若要自定义帐户中的模型，请选择页面左侧的“内容模型自定义”按钮。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/content-model-customization/content-model-customization.png" alt-text="在视频分析器媒体版中自定义内容模型":::
1. 若要编辑品牌，请选择“品牌”选项卡。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/customize-brand-model/customize-brand-model.png" alt-text="此屏幕截图显示了“内容模型自定义”对话框的“品牌”选项卡":::
1. 如果想要视频分析器媒体版检测必应建议的品牌，请选中“显示必应建议的品牌”选项；如果不想，请取消选中该选项。

## <a name="include-brands-in-the-model"></a>在模型中包括品牌

“包括品牌”部分表示你希望视频分析器媒体版检测的自定义品牌，即使这些品牌不是必应建议的。  

### <a name="add-a-brand-to-include-list"></a>将品牌添加到包括列表

1. 选择“+ 创建新品牌”。

    提供名称（必填）、类别（可选）、说明（可选）和引用 URL（可选）。
    类别字段用于对品牌进行标记。 在使用视频分析器媒体版 API 时，此字段显示为品牌的标记。 例如，品牌“Azure”可以标记或归类为“云”。

    引用 URL 字段可以是品牌的任何引用网站（例如其维基百科页的链接）。

2. 选择“保存”，你会看到该品牌已添加到“包括品牌”列表中。

### <a name="edit-a-brand-on-the-include-list"></a>编辑包括列表中的品牌

1. 选择要编辑的品牌旁边的铅笔图标。

    可以更新品牌的类别、说明或引用 URL。 不能更改品牌的名称，因为品牌的名称是唯一的。 如果需要更改品牌名称，请删除整个品牌（参见下一部分），然后使用新名称创建一个新品牌。

2. 选择“更新”按钮，使用新信息更新品牌。

### <a name="delete-a-brand-on-the-include-list"></a>删除包括列表中的品牌

1. 选择要删除的品牌旁边的回收站图标。
2. 选择“删除”，该品牌将不再出现在“包括品牌”列表中。

## <a name="exclude-brands-from-the-model"></a>从模型中排除品牌

“排除品牌”部分表示你不希望视频分析器媒体版检测的品牌。

### <a name="add-a-brand-to-exclude-list"></a>向排除列表添加品牌

1. 选择“+ 创建新品牌”。

    提供名称（必填）、类别（可选）。

2. 选择“保存”，你会看到该品牌已添加到“排除品牌”列表中。

### <a name="edit-a-brand-on-the-exclude-list"></a>编辑排除列表中的品牌

1. 选择要编辑的品牌旁边的铅笔图标。

    只能更新品牌的类别。 不能更改品牌的名称，因为品牌的名称是唯一的。 如果需要更改品牌名称，请删除整个品牌（参见下一部分），然后使用新名称创建一个新品牌。

2. 选择“更新”按钮，使用新信息更新品牌。

### <a name="delete-a-brand-on-the-exclude-list"></a>删除排除列表中的品牌

1. 选择要删除的品牌旁边的回收站图标。
2. 选择“删除”，该品牌将不再出现在“排除品牌”列表中。

## <a name="next-steps"></a>后续步骤

[使用 API 自定义品牌模型](customize-brands-model-with-api.md)
