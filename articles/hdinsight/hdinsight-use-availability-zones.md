---
title: 创建使用可用性区域的 Azure HDInsight 群集
description: 了解如何创建使用可用性区域的 Azure HDInsight 群集。
ms.service: hdinsight
ms.topic: how-to
ms.custom: references_regions
ms.date: 09/01/2021
ms.openlocfilehash: 1b516db029c5769526f8345d5f6ecdb96c5fad6c
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123439944"
---
# <a name="create-an-hdinsight-cluster-that-uses-availability-zones-preview"></a>创建使用可用性区域的 HDInsight 群集（预览版）

Azure HDInsight 群集由多个节点（头节点、工作器节点、网关节点和 zookeeper 节点）组成。 默认情况下，在支持可用性区域的区域中，用户无法控制在哪个可用性区域中预配哪些群集节点。 

借助此新的可用性区域功能，用户现在可以指定哪个可用性区域应托管 HDInsight 群集的所有节点。 群集节点在物理上与另一个可用性区域隔离，并与同一区域中其他可用性区域故障隔离。 此部署模型还在群集中提供经济实惠、低延迟的网络连接。 

将此部署模型复制到多个可用性区域可以提供更高级别的可用性，以防止出现硬件失败。

本文介绍如何在可用性区域中创建 HDInsight 群集，以及如何使用此功能实现更高的可用性。 

## <a name="before-you-begin"></a>开始之前
只有 6 月 15 日之后创建的群集才支持可用性区域功能。 创建群集后，无法更新可用性区域设置。 也不能更新现有非可用性区域群集来使用可用性区域。

## <a name="prerequisites-and-region-availability"></a>先决条件和区域可用性
先决条件：

 - 必须在自定义 VNet 下创建群集。 
 - 需要自带用于 Ambari DB 和外部元存储（如 Hive 元存储）的 SQL DB，以便可以在同一可用性区域中配置这些数据库。 

目前可以在以下地区使用可用性区域创建 HDInsight 群集：

 - 澳大利亚东部
 - 巴西南部
 - 加拿大中部
 - 美国中部
 - 美国东部
 - 美国东部 2
 - 法国中部
 - 德国中西部
 - 日本东部
 - 北欧
 - 东南亚
 - 美国中南部
 - 英国南部
 - US Gov 弗吉尼亚州
 - 西欧
 - 美国西部 2

## <a name="overview-of-availability-zones-for-hdinsight-clusters"></a>HDInsight 群集的可用性区域概述

可用性区域是某个区域内的非重复物理位置。 每个区域由一个或多个数据中心组成，这些数据中心配置了独立电源、冷却和网络。 在 Azure 中，一个区域包含一个或多个可用性区域。 数据中心发生故障时，区域中的可用性区域的物理隔离可保护应用程序和数据。 有关详细信息，请参阅 [Azure 中的可用性区域是什么](../availability-zones/az-overview.md)。

可将 Azure HDInsight 群集配置为部署在一个可用性区域中。 此 HDInsight 群集中的所有节点（包括两个头节点、三个 zookeeper 节点、两个网关节点和工作器节点）都将放置在指定的可用性区域中。  例如，美国东部有三个可用性区域。 可以使用可用性区域 1 中的所有节点创建美国东部的 HDInsight 群集。 

通过这种方式将可用性区域与 HDInsight 群集一起使用可提供性能和成本优势： 

 - 由于网络连接延迟较低，因此性能更佳
 - 成本更低：同一可用性区域中的数据传输是免费的。 跨可用性区域数据传输会产生额外的网络成本。 

如果应用程序需要跨多个可用性区域实现高可用性，可以在一个可用性区域中创建一个主要 HDInsight 群集，并在另一个可用性区域中创建一个最小的辅助 HDInsight 群集，以节省成本。 使用此设计，如果其他可用性区域之一出现故障，此 HDInsight 群集不会受到影响。 如果此可用性区域出现故障，客户需要将不同可用性区域中的辅助群集切换到主要群集，将工作负载路由到此新的主要群集，并快速纵向扩展群集规模以进行数据处理。   

## <a name="create-an-hdinsight-cluster-using-availability-zone"></a>使用可用性区域创建 HDInsight 群集
可以使用 Azure 资源管理器 (ARM) 模板将 HDInsight 群集部署到指定的可用性区域。 

在资源部分，需要添加一个“区域”部分，并提供要将此群集部署到的可用性区域。 

```json
   "resources": [
        {
            "type": "Microsoft.HDInsight/clusters",
            "apiVersion": "2018-06-01-preview",
            "name": "[parameters('cluster name')]",
            "location": "East US 2",
            "zones": [
                "1"
            ],
```
 
## <a name="verify-nodes-within-one-availability-zone-across-zones"></a>跨区域验证一个可用性区域中的节点
HDInsight 群集准备就绪后，可以检查位置，以查看它们所部署到的可用性区域。

:::image type="content" source="./media/hdinsight-use-availability-zones/cluster-availability-zone-info.png" alt-text="显示群集概述中可用性区域信息的屏幕截图" border="true":::

**获取 API 响应**： 

```json
 [
        {
            "location": "East US 2",
            "zones": [
                "1"
            ],
```

## <a name="scale-up-the-cluster"></a>纵向扩展群集
可以纵向扩展具有很多工作器节点的 HDInsight 群集。 新添加的工作器节点将放置在此群集的同一可用性区域中。 

**限制**： 

 - HDInsight 中的可用性区域功能不支持需要托管磁盘的群集（Kafka 群集和启用了加速写入功能的 HBase 群集） 

## <a name="best-practices"></a>最佳实践

 - 定期备份 Ambari DB 中的配置。 
 - 实现逻辑以轻松地将工作负载路由到辅助群集。

## <a name="when-az-goes-down-what-to-expect"></a>AZ 关闭时，会发生什么
 - 无法通过 SSH 访问此群集
 - 无法删除或纵向扩展或缩减此群集
 - 无法提交作业或查看作业历史记录
 - 仍然可以在不同的区域提交新的群集创建请求