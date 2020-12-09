---
title: 与 Azure 数据工厂的区别
description: 了解 Azure Synapse Analytics 的数据集成功能与 Azure 数据工厂的不同之处
services: synapse-analytics
author: shirleywangmsft
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/06/2020
ms.author: shwang
ms.reviewer: jrasnick
ms.openlocfilehash: db5d05e1a211ce14926ee4031054669fff5110d9
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2020
ms.locfileid: "96930205"
---
# <a name="data-integration-in-azure-synapse-analytics-versus-azure-data-factory"></a>Azure Synapse Analytics 与 Azure 数据工厂中的数据集成

在 Azure Synapse 分析中，数据集成功能（如 Synapse 管道和数据流）基于 Azure 数据工厂的功能。 有关详细信息，请参阅 [什么是 Azure 数据工厂](../../data-factory/introduction.md)。 几乎所有功能完全相同或类似，并且文档在两个服务之间共享。 本文重点介绍 Azure 数据工厂与 Azure Synapse 之间的当前差异。

若要查看 Azure 数据工厂功能或文章是否适用于 Azure Synapse，请查看文章顶部的名字对象。

![适用于名字对象](../media/concepts-data-factory-differences/applies-to-moniker.png "适用于名字对象")

## <a name="features-in-azure-data-factory-not-planned-for-azure-synapse"></a>Azure 数据工厂中的功能未计划用于 Azure Synapse

以下功能在 Azure 数据工厂中提供，但未计划用于 Azure Synapse。

* **生命周期和班次 SSIS 包：** 在 Azure 数据工厂中，可以使用 SSIS 集成运行时提升和转移 SSIS 包。 SSIS 集成运行时和 "执行 SSIS 包" 活动在 Synapse 工作区中不可用。 
* **生存时间：** 生存时间是 Azure 集成运行时中的一项设置，它允许 spark 群集在数据流完成后的一段时间 *内保持不* 变。 此功能在 Synapse 工作区中不可用。

## <a name="azure-synapse-features-not-supported-in-azure-data-factory"></a>Azure 数据工厂不支持 azure Synapse 功能

以下功能在 Azure Synapse 中可用，但不计划用于 Azure 数据工厂。

* 用于 **监视地图数据流的 Spark 作业：** 在 Synapse 中，Spark 引擎包含在用户的订阅中，因此用户可以查看详细的 Spark 日志。 在 Azure 数据工厂中，作业执行发生在 Azure 数据工厂托管的 Spark 群集上，此信息不可用。 

## <a name="azure-data-factory-features-that-behave-differently-in-synapse"></a>Synapse 中行为不同的 Azure 数据工厂功能

以下功能的行为方式不同，或当前在 Azure Synapse 中不存在。 

* **整理数据流：** 整理数据流活动目前仅适用于 Azure 数据工厂。
* **解决方案模板库：** 在 Azure 数据工厂中，用户可以在解决方案模板库中找到管道模板。 在 Synapse 工作区中，知识中心包含一组不同的模板以及其他数据集和 SQL 脚本。 
* **与 Azure Monitor 集成：** 与 Azure 数据工厂一样，Synapse 工作区不会与 Azure Monitor 集成。
* **混合集成运行时配置：** 在 Synapse 工作区中，用户不能同时具有托管 VNet IR 和 Azure IR。 Azure 数据工厂支持此功能。
* **Integration runtime 共享：** 不能在 Synapse 工作区之间共享自承载集成运行时。 Azure 数据工厂支持此功能。
* **用于数据流的跨区域集成运行时：** 数据流无法在 Synapse 工作区之外的其他区域中的集成运行时上运行。 Azure 数据工厂支持此功能。

## <a name="next-steps"></a>后续步骤

通过了解如何将 [数据引入 Azure Data Lake Storage gen2 帐户](data-integration-data-lake.md)，开始在 Synapse 工作区中进行数据集成。
