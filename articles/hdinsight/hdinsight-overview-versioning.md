---
title: 版本控制简介 - Azure HDInsight
description: 了解 Azure HDInsight 中的版本控制工作原理。
ms.service: hdinsight
ms.topic: conceptual
author: deshriva
ms.author: deshriva
ms.date: 02/08/2021
ms.openlocfilehash: 6db4c7856ebdf75d5bf94de1e3110bb25bc93e69
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "103493860"
---
# <a name="how-versioning-works-in-hdinsight"></a>HDInsight 中的版本控制工作原理

HDInsight 服务包含两个主要组件：资源提供程序和在群集上部署的 Apache Hadoop 组件。 

## <a name="hdinsight-resource-provider"></a>HDInsight 资源提供程序

Azure 中的资源由资源提供程序提供。 HDInsight 资源提供程序负责创建、管理和删除群集。

## <a name="hdinsight-images"></a>HDInsight 映像

HDInsight 使用映像将可在群集上部署的开源软件 (OSS) 组件组合在一起。 这些映像包含基本 Ubuntu 操作系统和核心组件，例如 Spark、Hadoop、Kafka、HBase 或 Hive。

## <a name="versioning-in-hdinsight"></a>HDInsight 中的版本控制

Microsoft 定期升级映像和 HDInsight 资源提供程序，以包含新的改进和功能。

如果满足下列一项或多项条件，可能会创建新的 HDInsight 版本：

- HDInsight 资源提供程序功能出现重大更改或更新
- OSS 组件发布主要版本
- Ubuntu 操作系统出现重大更改

如果满足下列一项或多项条件，则会创建新的映像版本：

- OSS 组件发布主要或次要版本和更新
- 映像中组件提供补丁或修补程序

## <a name="next-steps"></a>后续步骤

- [HDInsight 中的 Apache 组件和版本](./hdinsight-component-versioning.md)
