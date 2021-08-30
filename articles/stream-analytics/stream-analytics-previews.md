---
title: Azure 流分析预览功能
description: 本文列出了当前以预览版提供的 Azure 流分析功能
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/16/2021
ms.openlocfilehash: 3f6b46425954d8befaef396c66b023565310ec36
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121741842"
---
# <a name="azure-stream-analytics-preview-features"></a>Azure 流分析预览功能

本文汇总了当前以预览版提供的 Azure 流分析的所有功能。 建议不要在生产环境中使用预览功能。

## <a name="authenticate-to-sql-database-output-with-managed-identities-preview"></a>通过托管标识对 SQL 数据库输出进行身份验证（预览）

Azure 流分析支持对 Azure SQL 数据库输出接收器进行[托管标识身份验证](../active-directory/managed-identities-azure-resources/overview.md)。 托管标识消除了基于用户的身份验证方法的限制，例如发生密码更改时需要重新进行身份验证。 

## <a name="real-time-high-performance-scoring-with-custom-ml-models-managed-by-azure-machine-learning"></a>通过 Azure 机器学习管理的自定义 ML 模型进行实时高性能评分

Azure 流分析通过利用自定义预先训练的机器学习模型（由 Azure 机器学习管理，并在 Azure Kubernetes 服务 (AKS) 或 Azure 容器实例 (ACI) 中托管），并使用不需要编写代码的工作流，来支持高性能实时评分。 [注册](https://aka.ms/asapreview1)即可获取预览版

## <a name="c-custom-de-serializers"></a>C# 自定义反序列化程序
开发人员可以利用 Azure 流分析的强大功能来处理 Protobuf、XML 或任何自定义格式的数据。 可以在 C# 中实现[自定义反序列化程序](custom-deserializer-examples.md)，然后可以使用该程序对 Azure 流分析接收的事件进行反序列化处理。

## <a name="extensibility-with-c-custom-code"></a>使用 C# 自定义代码的可扩展性

在云中或 IoT Edge 上创建流分析模块的开发人员可以编写或重复使用自定义 C# 函数，并在查询中通过[用户定义的函数](stream-analytics-edge-csharp-udf-methods.md)直接调用这些函数。

## <a name="debug-queries-locally-using-job-diagram-in-visual-studio-code"></a>在 Visual Studio Code 中使用作业关系图在本地调试查询

在本地测试查询时，可以使用作业关系图检查每个步骤的中间结果集和指标。

## <a name="explore-jobs-in-visual-studio-code"></a>在 Visual Studio Code 中探索作业

Visual Studio Code 中的流分析资源管理器扩展为开发人员提供了管理其流分析作业的轻量级体验。 在流分析资源管理器中，可以在作业监视器中轻松管理作业、查看作业关系图和调试。

## <a name="debug-query-steps-in-visual-studio"></a>在 Visual Studio 中调试查询步骤

在适用于 Visual Studio 的 Azure 流分析工具中执行本地测试时，可以轻松预览数据关系图上的中间行集。 


## <a name="live-data-testing-in-visual-studio"></a>Visual Studio 中的实时数据测试

适用于 Azure 流分析的 Visual Studio 工具增强了本地测试功能，通过该功能可针对来自云源（如事件中心或 IoT 中心）的实时事件流测试查询。 了解如何[使用适用于 Visual Studio 的 Azure 流分析工具在本地测试实时数据](stream-analytics-live-data-local-testing.md)。


