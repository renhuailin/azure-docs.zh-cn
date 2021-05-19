---
title: Azure Functions 诊断概述
description: 了解如何通过 Azure Functions 诊断来排查函数应用的问题。
author: yunjchoi
ms.topic: article
ms.date: 11/01/2019
ms.author: yunjchoi
ms.custom: na
ms.openlocfilehash: fb329273d442c023233fee2e7e01aabe5f5bff8c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "83122353"
---
# <a name="azure-functions-diagnostics-overview"></a>Azure Functions 诊断概述

运行函数应用时，你希望能够准备好应对可能出现的任何问题，例如 4xx 错误或触发器失败。 Azure Functions 诊断是一种智能的交互式体验，无需进行配置即可帮助你排查函数应用的问题，也没有额外费用。 当你在函数应用中遇到问题时，Azure Functions 诊断会指出问题所在。 它将引导你获得正确的信息，以便更轻松、快速地排除故障并解决问题。 本文介绍如何使用 Azure Functions 诊断的基础知识，以更快速地诊断和解决常见函数应用问题。

## <a name="start-azure-functions-diagnostics"></a>启动 Azure Functions 诊断

启动 Azure Functions 诊断：

1. 在 [Azure 门户](https://portal.azure.com)中导航到函数应用。
1. 选择“诊断并解决问题”可打开 Azure Functions 诊断。
1. 使用主页磁贴中的关键字，选择最能描述函数应用问题的类别。 还可以在搜索栏中键入最能描述你的问题的关键字。 例如，可键入 `execution` 以查看与函数应用执行相关的诊断报告列表，并直接从主页打开它们。

   :::image type="content" source="./media/functions-diagnostics/functions-app-search-azure-functions-diagnostics.png" alt-text="搜索 Azure Functions 诊断。" border="true":::

## <a name="use-the-interactive-interface"></a>使用交互式接口

选择最适合函数应用问题的主页类别后，Azure Functions 诊断的交互式接口（名为精灵）可以指导你诊断和解决应用的问题。 对于你感兴趣的问题类别，可以使用精灵提供的磁贴快捷方式来查看其完整诊断报告。 磁贴快捷方式为你提供了一种访问诊断指标的直接方式。

:::image type="content" source="./media/functions-diagnostics/genie.png" alt-text="精灵是 Azure Functions 诊断的接口。" border="false":::

选择磁贴后，可以看到与磁贴中所述问题相关的主题列表。 这些主题会提供完整报告的重要信息片段。 选择以上任一主题，进一步调查问题。 此外，还可以选择“查看完整报告”来浏览单个页面上的所有主题。

:::image type="content" source="./media/functions-diagnostics/preview-of-diagnostic-report.png" alt-text="诊断报告预览" border="false":::

## <a name="view-a-diagnostic-report"></a>查看诊断报告

选择某个主题后，可以查看特定于函数应用的诊断报告。 诊断报告使用状态图标来指示应用是否存在任何特定问题。 你会看到问题的详细描述、建议的操作、相关指标和有用的文档。自定义诊断报告是通过在函数应用上运行的一系列检查生成的。 诊断报告是一个有用的工具，可用于在函数应用中查明问题并指导你解决问题。

## <a name="find-the-problem-code"></a>查找问题代码

对于基于脚本的函数，可以使用“函数应用关闭或报告错误”下的“函数执行和错误”来缩小导致异常或错误的代码行范围 。 你可以使用此工具找出源于特定代码行的根本原因并解决问题。 此选项不适用于预编译的 C# 和 Java 函数。

:::image type="content" source="./media/functions-diagnostics/diagnostic-report-on-function-execution-errors.png" alt-text="有关函数执行错误的诊断报告" border="false":::

:::image type="content" source="./media/functions-diagnostics/function-exception.png" alt-text="异常详细信息的视图。" border="false":::

## <a name="next-steps"></a>后续步骤

你可以在 [UserVoice](https://feedback.azure.com/forums/355860-azure-functions) 上提问或提供有关 Azure Functions 诊断的反馈。 在反馈的标题中包含 `[Diag]`。

> [!div class="nextstepaction"]
> [监视函数应用](functions-monitoring.md)
