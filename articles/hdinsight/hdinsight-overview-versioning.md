---
title: 版本控制简介-Azure HDInsight
description: 了解 Azure HDInsight 中的版本控制工作原理。
ms.service: hdinsight
ms.topic: conceptual
ms.custom: deshriva
ms.date: 02/08/2021
ms.openlocfilehash: c4dddeef5daf167eeef92532b61ed986861896e8
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744396"
---
# <a name="how-versioning-works-in-hdinsight"></a>HDInsight 中的版本控制的工作原理

HDInsight 服务包含两个主要组件：一个资源提供程序和一个在群集上部署 Apache Hadoop 组件。 

## <a name="hdinsight-resource-provider"></a>HDInsight 资源提供程序

Azure 中的资源由资源提供程序提供。 HDInsight 资源提供程序负责创建、管理和删除群集。

## <a name="hdinsight-images"></a>HDInsight 映像

HDInsight 使用映像将开源软件 (OSS 组合在一起，可在群集上部署) 组件。 这些映像包含基本 Ubuntu 操作系统和核心组件，例如 Spark、Hadoop、Kafka、HBase 或 Hive。

## <a name="versioning-in-hdinsight"></a>HDInsight 中的版本控制

Microsoft 定期升级映像和 HDInsight 资源提供程序，以包含新的改进和功能。

如果满足下列一项或多项条件，可能会创建新的 HDInsight 版本：

- HDInsight 资源提供程序功能的重大更改或更新
- OSS 组件的主要版本
- Ubuntu 操作系统的重大更改

如果满足下列一项或多项条件，则会创建新的映像版本：

- OSS 组件的主要或次要版本和更新
- 映像中组件的修补程序或修补程序

## <a name="next-steps"></a>后续步骤

- [HDInsight 中的 Apache 组件和版本](./hdinsight-component-versioning.md)
