---
title: 与 Azure 数据工厂的区别
description: 了解 Azure Synapse Analytics 的数据集成功能与 Azure 数据工厂的数据集成功能的区别
services: synapse-analytics
author: kromerm
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: conceptual
ms.date: 12/10/2020
ms.author: makromer
ms.reviewer: jrasnick
ms.openlocfilehash: 314c52384910728c0250101e72501de0ae0b89a1
ms.sourcegitcommit: 832e92d3b81435c0aeb3d4edbe8f2c1f0aa8a46d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2021
ms.locfileid: "111554995"
---
# <a name="data-integration-in-azure-synapse-analytics-versus-azure-data-factory"></a>Azure Synapse Analytics 与 Azure 数据工厂中的数据集成比较

在 Azure Synapse Analytics 中，数据集成功能（如 Synapse 管道和数据流）基于 Azure 数据工厂的功能。 有关详细信息，请参阅[什么是 Azure 数据工厂](../../data-factory/introduction.md)。


## <a name="available-features-in-adf--azure-synapse-analytics"></a>ADF 和 Azure Synapse Analytics 中的可用功能

请查看下表以了解功能可用性：

| 类别                 | 功能    |  Azure 数据工厂  | Azure Synapse Analytics |
| ------------------------ | ---------- | :------------------: | :---------------------: |
| **集成运行时**  | 使用 SSIS 和 SSIS Integration Runtime | ✓ | ✗ |
|                          | 支持跨区域的集成运行时（数据流） | ✓ | ✗ |
|                          | 集成运行时共享 | ✓<br><small>可在不同的数据工厂之间共享 | ✗ |
|                          | 生存时间 | ✓ | ✗ |
| **管道和活动** | SSIS 包活动 | ✓ | ✗ |
|                          | 支持 Power Query 活动 | ✓ | ✗ |
| 模板库和知识中心 | 解决方案模板 | ✓<br><small>Azure 数据工厂模板库 | ✓<br><small>Synapse 工作区知识中心 |
| **GIT 存储库集成** | GIT 集成 | ✓ | ✓ |
| **Monitoring**           | 监视 Spark 作业的数据流 | ✗ | ✓<br><small>利用 Synapse Spark 池 |
|                          | 与 Azure Monitor 集成 | ✓ | ✗ |
| **沿袭** | 支持将管道沿袭数据发布到 Purview  | ✓ | ✗ |  

> [!Note]
> 生存时间是一种 Azure Integration Runtime 设置，使 Spark 群集可以在执行数据流之后的一段时间内保持活跃状态。
>


## <a name="next-steps"></a>后续步骤

通过了解如何[将数据引入 Azure Data Lake Storage gen2 帐户](data-integration-data-lake.md)，开始在 Synapse 工作区中进行数据集成。
