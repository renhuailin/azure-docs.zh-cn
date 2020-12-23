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
ms.openlocfilehash: 8818d4db489cef8203ae515c18c61e215d577033
ms.sourcegitcommit: ea17e3a6219f0f01330cf7610e54f033a394b459
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/14/2020
ms.locfileid: "97387609"
---
# <a name="data-integration-in-azure-synapse-analytics-versus-azure-data-factory"></a>Azure Synapse Analytics 与 Azure 数据工厂中的数据集成

在 Azure Synapse 分析中，数据集成功能（如 Synapse 管道和数据流）基于 Azure 数据工厂的功能。 有关详细信息，请参阅 [什么是 Azure 数据工厂](../../data-factory/introduction.md)。


## <a name="available-features-in-adf--azure-synapse-analytics"></a>ADF 中的可用功能 & Azure Synapse Analytics

请查看下表了解功能可用性：

| Category                 | 功能    |  Azure 数据工厂  | Azure Synapse Analytics |
| ------------------------ | ---------- | :------------------: | :---------------------: |
| **Integration Runtime**  | 使用 SSIS 和 SSIS Integration Runtime | ✓ | ✗ |
|                          | 支持跨区域 Integration Runtime (数据流)  | ✓ | ✗ |
|                          | Integration Runtime 共享 | ✓<br><small>*可在不同的数据工厂之间共享* | ✗ |
|                          | 生存时间 | ✓ | ✗ |
| **管道活动** | SSIS 包活动 | ✓ | ✗ |
|                          | 支持 Power Query 活动 | ✓ | ✓ |
| **模板库和知识中心** | 解决方案模板 | ✓<br><small>*Azure 数据工厂模板库* | ✓<br><small>*Synapse 工作区知识中心* |
| **GIT 存储库集成** | GIT 集成 | ✓ | ✓ |
| **Monitoring**           | 监视数据流的 Spark 作业 | ✗ | ✓<br><small>*利用 Synapse Spark 池* |
|                          | 与 Azure Monitor 的集成 | ✓ | ✗ |

> [!Note]
> **生存时间** 是 Azure Integration Runtime 的设置，使 Spark 群集可以在数据流执行后的一段时间内 *保持预热* 。
>


## <a name="next-steps"></a>后续步骤

通过了解如何将 [数据引入 Azure Data Lake Storage gen2 帐户](data-integration-data-lake.md)，开始在 Synapse 工作区中进行数据集成。
