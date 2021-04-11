---
title: Apache Spark 池的概念
description: Azure Synapse Analytics 中的 Apache Spark 池大小和配置简介。
services: synapse-analytics
author: mlee3gsd
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 12/2/2020
ms.author: martinle
ms.reviewer: euang
ms.openlocfilehash: 6422c33f17879aa8ec4844cc6de63411528a388b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104606151"
---
# <a name="apache-spark-pool-configurations-in-azure-synapse-analytics"></a>Azure Synapse Analytics 中的 Apache Spark 池配置

Spark 池是一组元数据，用于定义实例化 Spark 实例时的计算资源要求和关联的行为特征。 这些特征包括但不限于名称、节点数、节点大小、缩放行为和生存时间。 Spark 池本身不会消耗任何资源。 创建 Spark 池不会产生任何费用。 仅当在目标 Spark 池上执行 Spark 作业以及按需实例化 Spark 实例后，才会产生费用。

可在 [Synapse Analytics 中的 Spark 池入门](../quickstart-create-apache-spark-pool-portal.md)中了解如何创建 Spark 池及查看其所有属性

## <a name="nodes"></a>节点

Apache Spark 池实例由一个头节点和两个或更多个工作器节点组成，一个 Spark 实例中至少有三个节点。  头节点运行其他管理服务，例如 Livy、Yarn 资源管理器、Zookeeper 和 Spark 驱动程序。  所有节点都运行节点代理和 Yarn 节点管理器等服务。 所有工作器节点都运行 Spark 执行程序服务。

## <a name="node-sizes"></a>节点大小

可以使用不同的节点大小来定义 Spark 池，大小范围从具有 8 个 vCore 和 64 GB 内存的小型计算节点，到每个节点具有 64 个 vCore 和 432 GB 内存的 XXLarge 计算节点。 可以在创建池后更改节点大小，不过，这可能需要重启实例。

|大小 | vCore | 内存|
|-----|------|-------|
|小|4|32 GB|
|中型|8|64 GB|
|大|16|128 GB|
|XLarge|32|256 GB|
|XXLarge|64|432 GB|

## <a name="autoscale"></a>自动缩放

Apache Spark 池提供根据活动量自动纵向扩展和缩减计算资源的功能。  启用自动缩放功能后，你可以设置要缩放的最小和最大节点数。
禁用自动缩放功能后，设置的节点数将固定不变。  可以在创建池后更改此设置，不过，这可能需要重启实例。

## <a name="automatic-pause"></a>自动暂停

自动暂停功能会在设置的空闲时段后释放资源，从而减少 Apache Spark 池的总体成本。  启用此功能后，可以设置空闲时间的分钟数。  自动暂停功能与自动缩放功能互不相关。 无论是启用还是禁用了自动缩放，都可以暂停资源。  可以在创建池后更改此设置，不过，这可能需要重启实例。

## <a name="next-steps"></a>后续步骤

* [Azure Synapse Analytics](../index.yml)
* [Apache Spark 文档](https://spark.apache.org/docs/2.4.5/)