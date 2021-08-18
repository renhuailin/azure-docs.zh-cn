---
title: 导入和导出 Azure Sentinel 分析规则 | Microsoft Docs
description: 在 ARM 模板中导出和导入分析规则以帮助部署
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
ms.date: 05/30/2021
ms.author: yelevin
ms.openlocfilehash: 60d72bcc687659f27e309942040f46a20aab5d3f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121730986"
---
# <a name="export-and-import-analytics-rules-to-and-from-arm-templates"></a>在 ARM 模板中导出和导入分析规则

> [!IMPORTANT]
>
> - 导出和导入规则处于预览阶段。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，了解适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

## <a name="introduction"></a>简介

你现在可以将分析规则导出到 Azure 资源管理器 (ARM) 模板文件并从这些文件导入规则，作为以代码形式管理和控制 Azure Sentinel 部署的一部分。 导出操作将在浏览器的下载位置的中创建一个 JSON 文件（名为 Azure_Sentinel_analytic_rule.json），然后可以像处理任何其他文件一样对其进行重命名、移动和其他处理。

导出的 JSON 文件是独立于工作区的，因此可以导入到其他工作区，甚至其他租户。 作为代码，它也可以在托管 CI/CD 框架中进行版本控制、更新和部署。

此文件包含分析规则中定义的所有参数，因此对于计划的规则，它包括基础查询及其相关的计划设置、严重性、事件创建、事件和警报分组设置、已分配的 MITRE ATT&CK 策略等。 任何类型的分析规则（不只是计划的规则）都可以导出到 JSON 文件。

## <a name="export-rules"></a>导出规则

1. 从 Azure Sentinel 导航菜单中，选择“分析”。

1. 选择要导出的规则，然后单击屏幕顶部栏中的“导出”。

    :::image type="content" source="./media/import-export-analytics-rules/export-rule.png" alt-text="导出分析规则" lightbox="./media/import-export-analytics-rules/export-rule.png":::

    > [!NOTE]
    > - 通过标记规则旁边的复选框并点击最后的“导出”，可以一次性选择多个分析规则进行导出。
    >
    > - 在单击“导出”之前标记标头行（“严重性”旁边）中的复选框，可以一次性导出显示网格的单个页面上的所有规则 。 不过，一次不能导出多个页面的规则。
    >
    > - 请注意，在此方案中，将创建一个文件（名为 Azure_Sentinel_analytic_rules.json），该文件将包含所有导出规则的 JSON 代码。

## <a name="import-rules"></a>导入规则

1. 已准备好分析规则 ARM 模板 JSON 文件。

1. 从 Azure Sentinel 导航菜单中，选择“分析”。

1. 单击屏幕顶部栏中的“导入”。 在生成的对话框中，导航到并选择表示要导入的规则的 JSON 文件，然后选择“打开”。

    :::image type="content" source="./media/import-export-analytics-rules/import-rule.png" alt-text="导入分析规则" lightbox="./media/import-export-analytics-rules/import-rule.png":::

    > [!NOTE]
    > 最多可以从一个 ARM 模板文件导入 50 个分析规则。

## <a name="next-steps"></a>后续步骤

本文档介绍了如何在 ARM 模板中导出和导入分析规则。
- 了解有关[分析规则](detect-threats-built-in.md)的详细信息，包括[自定义计划规则](detect-threats-custom.md)。
- 了解有关 [ARM 模板](../azure-resource-manager/templates/overview.md)的详细信息。
