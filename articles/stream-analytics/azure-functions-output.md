---
title: 从 Azure 流分析输出的 Azure Functions
description: 本文介绍作为 Azure 流分析输出的 Azure Functions。
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/28/2021
ms.openlocfilehash: ccedab6284fd5dac5a3d9f8d221a22803a3571f8
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2021
ms.locfileid: "110666664"
---
# <a name="azure-functions-output-from-azure-stream-analytics"></a>从 Azure 流分析输出的 Azure Functions

Azure Functions 是一个无服务器计算服务，可以使用它按需运行代码，而无需显式预配或管理基础结构。 它允许实现由 Azure 或合作伙伴服务中出现的事件所触发的代码。 Azure Functions 响应触发的这一功能使其成为 Azure 流分析的自然输出。 此输出适配器允许用户将流分析连接到 Azure Functions，并运行脚本或一段代码来响应各种事件。

流分析中的 Azure Functions 输出不可在 Azure 中国世纪互联和 Azure 德国 (T-Systems International) 区域中使用。 也不支持从多租户群集中运行的流分析作业连接到虚拟网络 (VNet) 内的 Azure Functions。

Azure 流分析通过 HTTP 触发器调用 Azure Functions。 提供具有以下可配置属性的 Azure Functions 输出适配器：

| 属性名称 | 说明 |
| --- | --- |
| 函数应用 |Azure Functions 应用的名称。 |
| 函数 |Azure Functions 应用中的函数的名称。 |
| 键 |若要使用其他订阅中的 Azure 函数，可提供用于访问该函数的密钥。 |
| 最大批大小 |此属性可用于设置将发送到 Azure 函数的每个输出批的最大大小。 输入单元以字节为单位。 默认情况下，此值为 262,144 字节 (256 KB)。 |
| 最大批数  |一个用于指定发送到 Azure Functions 的每个批中的最大事件数的属性。 默认值为 100。 |

对于已成功处理的批，Azure 流分析预期函数应用中的 HTTP 状态为 200。

当 Azure 流分析从 Azure 函数中收到 413（“http 请求实体过大”）异常时，它将减小发送到 Azure Functions 的批的大小。 在 Azure 函数代码中，使用此异常以确保 Azure 流分析不会发送过大的批。 此外，确保函数中使用的最大批数和最大批大小值与在流分析门户中输入的值一致。

> [!NOTE]
> 在测试连接过程中，流分析会将空批发送到 Azure Functions，以测试两者之间的连接是否正常。 确保 Functions 应用处理空批请求，以确保通过连接测试。

另外，如果时间窗口中没有任何事件登录，则不生成任何输出。 因此，不会调用 **computeResult** 函数。 此行为与内置窗口化聚合函数一致。

## <a name="partitioning"></a>分区

分区键基于查询中的 PARTITION BY 子句。 输出编写器的数量与[完全并行化的查询](stream-analytics-scale-jobs.md)的输入分区一致。

## <a name="output-batch-size"></a>输出批大小

默认批大小为 262,144 字节 (256 KB)。 每批的默认事件计数为 100。 批大小是可配置的，可在流分析输出选项中增大或减小。

## <a name="limitation"></a>限制

Azure Functions 应该在 100 秒内完成其请求，因为 HTTP 客户端会在 100 秒后超时。 如果 Azure Functions 处理一批数据的时间超过 100 秒，就会出现一个会触发重试的超时。 此重试可能导致数据重复，因为 Azure Functions 会再次处理数据并可能产生相同的输出（因为它可能已在前一个请求中部分输出）


## <a name="next-steps"></a>后续步骤

* [快速入门：使用 Azure 门户创建流分析作业](stream-analytics-quick-create-portal.md)
* [快速入门：使用 Azure CLI 创建 Azure 流分析作业](quick-create-azure-cli.md)
* [快速入门：使用 ARM 模板创建 Azure 流分析作业](quick-create-azure-resource-manager.md)
* [快速入门：使用 Azure PowerShell 创建流分析作业](stream-analytics-quick-create-powershell.md)
* [快速入门：使用 Visual Studio 创建 Azure 流分析作业](stream-analytics-quick-create-vs.md)
* [快速入门：在 Visual Studio Code 中创建 Azure 流分析作业](quick-create-visual-studio-code.md)
