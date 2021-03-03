---
title: Apache Hadoop 组件和版本-Azure HDInsight 4。0
description: 了解 Azure HDInsight 4.0 中的 Apache Hadoop 组件和版本。
ms.service: hdinsight
ms.topic: conceptual
ms.custom: deshriva
ms.date: 02/08/2021
ms.openlocfilehash: 2baa210a293c0351c079ba545fc46d51f4fd7251
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744337"
---
# <a name="hdinsight-40-component-versions"></a>HDInsight 4.0 组件版本

本文介绍 Azure HDInsight 4.0 中 Apache Hadoop 的环境组件和版本。

## <a name="apache-components-available-with-hdinsight-version-40"></a>HDInsight 版本4.0 中提供的 Apache 组件

下表列出了与 HDInsight 4.0 相关联的 OSS 组件版本。

| 组件              | HDInsight 4.0 |
|------------------------|---------------|
| Apache Hadoop 和 YARN | 3.1.1         |
| Apache Tez             | 0.9.1         |
| Apache Pig             | 0.16.1        |
| Apache Hive            | 3.1.0         |
| Apache Ranger          | 1.1.0         |
| Apache HBase           | 2.1.6         |
| Apache Sqoop           | 1.4.7         |
| Apache Oozie           | 4.3.1         |
| Apache Zookeeper       | 3.4.6         |
| Apache Phoenix         | 5             |
| Apache Spark           | 2.4.4         |
| Apache Livy            | 0.5           |
| Apache Kafka           | 2.1.1         |
| Apache Ambari          | 2.7.0         |
| Apache Zeppelin        | 0.8.0         |
| Mono                   | 4.2.1         |


此表列出了已停用的某些 HDInsight 4.0 群集类型。

| 群集类型                    | 框架版本 | 支持到期日期      | 停用日期 |
|---------------------------------|-------------------|------------------------------|-----------------|
| HDInsight 4.0 Spark             | 2.3               | 2020 年 6 月 30 日                | 2020 年 6 月 30 日   |
| HDInsight 4.0 Kafka             | 1.1               | 2020 年 12 月 31 日                 | 2020 年 12 月 31 日    |

## <a name="next-steps"></a>后续步骤

- [为 HDInsight 上的 Apache Hadoop、Spark 和其他组件设置群集](hdinsight-hadoop-provision-linux-clusters.md)
- [企业安全性套餐](./enterprise-security-package.md)
- [使用 Windows 电脑在 HDInsight 上的 Apache Hadoop 中工作](hdinsight-hadoop-windows-tools.md)