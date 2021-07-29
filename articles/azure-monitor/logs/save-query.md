---
title: 在 Azure Monitor Log Analytics 中保存查询（预览版）
description: 介绍如何在 Log Analytics 中保存查询。
ms.subservice: logs
ms.topic: article
author: bwren
ms.author: bwren
ms.date: 05/20/2021
ms.openlocfilehash: 0334fa67849acdf628ec77a55fcf7d7f5435cbf6
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2021
ms.locfileid: "110481986"
---
# <a name="save-a-query-in-azure-monitor-log-analytics-preview"></a>在 Azure Monitor Log Analytics 中保存查询（预览版）
[日志查询](log-query-overview.md)是 Azure Monitor 中的请求，允许在 Log Analytics 工作区中处理和检索数据。 保存日志查询后，可以：

- 在所有 Log Analytics 上下文（包括工作区和资源中心）中使用查询。
- 允许其他用户运行相同的查询。
- 为组织创建常见查询库。

## <a name="save-options"></a>保存选项
保存查询时，该查询将存储在查询包中，此方法与先前在工作区中存储查询的方法相比，具有以下优势。 保存到查询包是首选方法，具有以下优势：

- 能够按不同属性对查询进行筛选和分组，更易于发现。
- 在 Log Analytics 中使用资源范围时可获取查询。
- 使查询在整个订阅中可用。
- 更多数据可用于描述和分类查询。


## <a name="save-a-query"></a>保存查询
若要将查询保存到查询包，请从 Log Analytics 中的“保存”下拉列表中选择“另存为 Log Analytics 查询”。

[![保存查询菜单](media/save-query/save-query.png)](media/save-query/save-query.png#lightbox)

将查询保存到查询包时，将显示以下对话框，可在其中提供查询属性的值。 查询属性用于对相似的查询进行筛选和分组，以帮助你查找所需的查询。 有关每个属性的详细说明，请参阅[查询属性](queries.md#query-properties)。

大多数用户应该保留“保存到默认查询包”选项，此选项会将查询保存到[默认查询包](query-packs.md#default-query-pack)中。 如果订阅中还有其他查询包，请取消选中此框。 有关创建新查询包的详细信息，请参阅 [Azure Monitor 日志（预览版）中的查询包](query-packs.md)。

[![保存查询对话框](media/save-query/save-query-dialog.png)](media/save-query/save-query-dialog.png#lightbox)

## <a name="edit-a-query"></a>编辑查询
可能需要编辑已保存的查询。 可能是更改查询本身或修改其任何属性。 在 Log Analytics 中打开现有查询后，通过从“保存”下拉列表中选择“编辑查询详细信息”可编辑它。 这样，可以使用相同的属性保存已编辑的查询或在保存之前修改任何属性。

如果要使用不同的名称保存查询，请选择“另存为 Log Analytics 查询”，就像创建新查询一样。 


## <a name="save-as-a-legacy-query"></a>另存为旧查询
由于上面列出的查询包的优势，建议不要另存为旧查询。 但是，可以将查询保存到工作区，将其与发布查询包之前保存到工作区的其他查询合并。 

若要保存旧查询，请从 Log Analytics 中的“保存”下拉列表中选择“另存为 Log Analytics 查询”。 选择“另存为旧查询”选项。 唯一可用的选项将是旧类别。


## <a name="next-steps"></a>后续步骤

[KQL 查询入门](get-started-queries.md)
