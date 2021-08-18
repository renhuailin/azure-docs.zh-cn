---
title: 将数据字段映射到 Azure Sentinel 实体 | Microsoft Docs
description: 将表中的数据字段映射到分析规则中的 Azure Sentinel 实体，以获得更好的事件信息
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/10/2021
ms.author: yelevin
ms.openlocfilehash: 755d6ff3e4b7e82390f6bc2c961883de976f5459
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121734791"
---
# <a name="map-data-fields-to-entities-in-azure-sentinel"></a>将数据字段映射到 Azure Sentinel 中的实体 

> [!IMPORTANT]
>
> - 新版本的实体映射功能处于预览阶段。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，了解适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

> [!IMPORTANT]
>
> - 请参阅本文档末尾的[新版本说明](#notes-on-the-new-version)，了解有关实体映射向后兼容性的重要信息以及新旧版本之间的差异。

## <a name="introduction"></a>简介

实体映射是[计划的查询分析规则](detect-threats-custom.md)配置不可或缺的一部分。 它可使用重要信息来丰富规则的输出（警报和事件），这些信息是任何调查过程和后续补救操作的构建基块。

下面详细介绍的过程是分析规则创建向导的一部分。 这里单独应用该过程来处理在现有分析规则中添加或更改实体映射的方案。

## <a name="how-to-map-entities"></a>如何映射实体

1. 从 Azure Sentinel 导航菜单中，选择“分析”。

1. 选择计划的查询规则，再单击“编辑”。 或者单击屏幕顶部的“创建”>“计划的查询规则”，新建规则。

1. 单击“设置规则逻辑”选项卡。 

1. 在“警报扩充（预览）”部分中，展开“实体映射”。

    :::image type="content" source="media/map-data-fields-to-entities/alert-enrichment.png" alt-text="展开实体映射":::

1. 在当前展开的“实体映射”部分，从“实体类型”下拉列表中选择一个实体类型。

    :::image type="content" source="media/map-data-fields-to-entities/choose-entity-type.png" alt-text="选择实体类型":::

1. 选择实体的“标识符”。 标识符是可充分标识实体的实体属性。 从“标识符”下拉列表中选择一个标识符，然后从“值”下拉列表中选择一个与标识符相对应的数据字段 。 除了一些例外情况，“值”列表中填充的是定义为规则查询主题的表中的数据字段。

    最多可为给定实体定义三个标识符。 有些标识符是必需的；而有些是可选的。 必须至少选择一个必需标识符。 否则，会出现一条警告消息，指示需要哪些标识符。 为了获得最佳结果（为了获得最佳唯一标识），应尽可能使用“强标识符”，使用多个强标识符可使数据源之间的关联度更高。 查看可用[实体和标识符](entities-reference.md)的完整列表。

    :::image type="content" source="media/map-data-fields-to-entities/map-entities.png" alt-text="将字段映射到实体":::

1. 单击“添加新实体”以映射多个实体。 你最多可以在一个分析规则中映射五个实体。 还可以映射多个相同类型的实体。 例如，可以映射两个 IP 实体，一个来自“源 IP 地址”字段，另一个来自“目标 IP 地址”字段 。 这样就可以同时跟踪两者。

    如果改变了主意或出错，可以通过单击实体下拉列表旁边的 “垃圾桶”图标来删除实体映射。

1. 完成实体映射后，请单击“查看并创建”选项卡。成功验证规则后，单击“保存”。

## <a name="notes-on-the-new-version"></a>新版本的说明

- 如果以前使用旧版本为此分析规则定义了实体映射，则这些映射会显示在查询代码中。 在新版本下定义的实体映射不会出现在查询代码中。 分析规则一次只能支持一个版本的实体映射，新版本优先。 因此，你在此处定义的任何单个映射都将导致在查询运行时忽略查询代码中定义的所有映射 。 

- 如果仍需要使用旧版本的实体映射（只要新版本仍处于预览阶段），就仍可使用 URL 中的功能标志访问该版本。 将光标置于 `https://portal.azure.com/` 和 `#blade` 之间，并插入文本 `?feature.EntityMapping=false`。

  - 旧版本的限制将继续适用。 只能映射用户、主机、IP 地址、URL 和文件哈希实体，并且每个实体只能映射一个。

  - 在复原到旧版本之前，必须删除使用新版本创建的任何实体映射，否则任何使用旧版本的实体映射都将不起作用  。

- 实体映射的新版本正式发布后，将无法再使用旧版本。 强烈建议将旧实体映射迁移到新版本。


## <a name="next-steps"></a>后续步骤

本文档介绍了如何将数据字段映射到 Azure Sentinel 分析规则中的实体。 要详细了解 Azure Sentinel，请参阅以下文章：
- 全面了解[计划的查询分析规则](detect-threats-custom.md)。
- 有关详细信息，请参阅 [Azure Sentinel 中的实体](entities-in-azure-sentinel.md)。
