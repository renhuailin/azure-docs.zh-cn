---
title: 与 Azure 数据工厂的区别
description: 了解 Azure Synapse Analytics 的数据集成功能与 Azure 数据工厂的不同之处
services: synapse-analytics
author: kromerm
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 12/10/2020
ms.author: makromer
ms.reviewer: jrasnick
ms.openlocfilehash: a8fd0ef006b246e30c02cfb321c72b4e070f54de
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109141"
---
# <a name="data-integration-in-azure-synapse-analytics-versus-azure-data-factory"></a>Azure Synapse Analytics 与 Azure 数据工厂中的数据集成

在 Azure Synapse 分析中，数据集成功能（如 Synapse 管道和数据流）基于 Azure 数据工厂的功能。 有关详细信息，请参阅 [什么是 Azure 数据工厂](../../data-factory/introduction.md)。


## <a name="available-features-in-azure-data-factory-and-azure-synapse-analytics"></a>Azure 数据工厂和 Azure Synapse 分析中的可用功能

请查看下表了解功能可用性：

| 类别                 | 功能    |  Azure 数据工厂  | Azure Synapse Analytics |
| ------------------------ | ---------- | :------------------: | :---------------------: |
| **Integration Runtime**  | 使用 SSIS 和 SSIS Integration Runtime | ✓ | ✗ |
|                          | 支持跨区域 Integration Runtime (数据流)  | ✓ | ✗ |
|                          | Integration Runtime 共享 | ✓<br><small>*可在不同的数据工厂之间共享* | ✗ |
|                          | 生存时间 | ✓ | ✗ |
| **管道活动** | SSIS 包活动 | ✓ | ✗ |
|                          | 支持 Power Query 活动 | ✓ | ✓ |
| **模板库和知识中心** | 解决方案模板 | ✓<br><small>*Azure 数据工厂模板库* | ✓<br><small>*Synapse 工作区知识中心* |
| **GIT 存储库集成** | GIT 集成 | ✓ | ✓ |
| **监视**           | 监视数据流的 Spark 作业 | ✗ | ✓<br><small>*利用 Synapse Spark 池* |
|                          | 与 Azure Monitor 的集成 | ✓ | ✗ |

> [!Note]
> **生存时间** 是 Azure Integration Runtime 的设置，使 Spark 群集可以在数据流执行后的一段时间内 *保持预热* 。
>


## <a name="next-steps"></a>后续步骤

通过了解如何将 [数据引入 Azure Data Lake Storage gen2 帐户](data-integration-data-lake.md)，开始在 Synapse 工作区中进行数据集成。
