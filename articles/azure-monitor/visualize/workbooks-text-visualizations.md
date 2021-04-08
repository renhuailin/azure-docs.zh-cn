---
title: Azure Monitor 工作簿文本可视化效果
description: 了解 Azure Monitor 工作簿的所有文本可视化效果。
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: e8f2d9495484b781b26962c2946b5bada6c38b4c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "100606259"
---
# <a name="text-visualizations"></a>文本可视化效果

工作簿允许作者在其工作簿中包含文本块。 文本可以是遥测数据的人工分析、用于帮助用户解释数据的信息、部分标题，等等。

[![Apdex 文本表的屏幕截图](./media/workbooks-text-visualizations/apdex.png)](./media/workbooks-text-visualizations/apdex.png#lightbox)

文本是通过 Markdown 控件添加的，该控件提供全面的格式控制。 其中包括不同的标题和字体样式、超链接、表等。

编辑模式：

![处于编辑模式的文本步骤的屏幕截图。](./media/workbooks-text-visualizations/text-edit-mode.png)

预览模式：

![“预览”选项卡上处于编辑模式的文本步骤的屏幕截图。](./media/workbooks-text-visualizations/text-edit-mode-preview.png)

## <a name="add-a-text-control"></a>添加文本控件

1. 单击“编辑”工具栏项，将工作簿切换到编辑模式。
2. 使用“添加文本”链接将一个文本控件添加到工作簿。
3. 在编辑器字段中添加 Markdown。
4. 使用“文本样式”选项可在纯 markdown 与使用 Azure 门户的标准 info/warning/success/error 样式包装的 markdown 之间切换。
5. 使用“预览”选项卡可查看内容的外观。 编辑时，预览将在滚动条区域内显示内容以限制其大小；但在运行时，markdown 内容将扩展以填充所需的任何空间，无滚动条。
6. 选择“完成编辑”按钮，完成编辑步骤。

> [!TIP]
> 使用此 [Markdown 速查表](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet)了解不同的格式设置选项。

## <a name="text-styles"></a>文本样式

以下文本样式可用于文本步骤：

| Style     | 说明                                                                               |
|-----------|-------------------------------------------------------------------------------------------|
| `plain`   | 不应用额外的格式设置。                                                      |
| `info`    | 门户的“info”样式，具有 `ℹ` 或类似图标，一般为蓝色背景。      |
| `error`   | 门户的“error”样式，具有 `❌` 或类似图标，一般为红色背景。     |
| `success` | 门户的“success”样式，具有 `✔` 或类似图标，一般为绿色背景。  |
| `upsell`  | 门户的“upsell”样式，具有 `🚀` 或类似图标，一般为紫色背景。 |
| `warning` | 门户的“warning”样式，具有 `⚠` 或类似图标，一般为蓝色背景。   |

除了选择特定样式，你还可选择文本参数作为样式源。 参数值必须是上述文本值之一。 缺少值的情况或任何无法识别的值都将被视为 `plain` 样式。

info 样式示例：

![info 样式外观的屏幕截图。](./media/workbooks-text-visualizations/text-preview-info-style.png)

warning 样式示例：

![warning 样式外观的屏幕截图。](./media/workbooks-text-visualizations/text-warning-style.png)

## <a name="next-steps"></a>后续步骤

* 了解如何[在工作簿中创建图表](workbooks-chart-visualizations.md)。
* 了解如何[在工作簿中创建网格](workbooks-grid-visualizations.md)。
