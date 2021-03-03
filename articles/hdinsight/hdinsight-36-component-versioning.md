---
title: Apache Hadoop 组件和版本-Azure HDInsight 3。6
description: 了解 Azure HDInsight 3.6 中的 Apache Hadoop 组件和版本。
ms.service: hdinsight
ms.topic: conceptual
ms.custom: deshriva
ms.date: 02/08/2021
ms.openlocfilehash: ee4ae5e0b92fb0627143f2d2f998747f7e956b6d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744340"
---
# <a name="hdinsight-36-component-versions"></a>HDInsight 3.6 组件版本

本文介绍 Azure HDInsight 3.6 中 Apache Hadoop 的环境组件和版本。

## <a name="support-for-hdinsight-36"></a>支持 HDInsight 3。6

下表列出了 HDInsight 3.6 群集类型的支持时间范围。

| 群集类型                    | 框架版本 | 当前支持到期日期        | 新的支持到期日期 |
|---------------------------------|-------------------|-----------------------------------|-----------------------------|
| HDInsight 3.6 Hadoop            | 2.7.3             | 2020 年 12 月 31 日                      | 2021 年 6 月 30 日               |
| HDInsight 3.6 Spark             | 2.3               | 2020 年 12 月 31 日                      | 2021 年 6 月 30 日               |
| HDInsight 3.6 Spark             | 2.2               | 2020 年 6 月 30 日停用          |                             |
| HDInsight 3.6 Spark             | 2.1               | 2020 年 6 月 30 日停用          |                             |
| HDInsight 3.6 Kafka             | 1.1               | 2020 年 12 月 31 日                      | 2021 年 6 月 30 日               |
| HDInsight 3.6 Kafka             | 1.0               | 2020 年 6 月 30 日停用。         |                             |
| HDInsight 3.6 HBase             | 1.1               | 2020 年 12 月 31 日                      | 2021 年 6 月 30 日               |
| HDInsight 3.6 Interactive Query | 2.1               | 2020 年 12 月 31 日                      | 2021 年 6 月 30 日               |
| HDInsight 3.6 Storm             | 1.1               | 2020 年 12 月 31 日                      | 2021 年 6 月 30 日               |
| HDInsight 3.6 ML 服务      | 9.3               | 2020 年 12 月 31 日                      | 2020 年 12 月 31 日                |
## <a name="apache-components-available-with-hdinsight-version-36"></a>HDInsight 版本3.6 中提供的 Apache 组件

下表列出了与 HDInsight 3.6 相关联的 OSS 组件版本。

| 组件              | HDInsight 3.6（默认）     |
|------------------------|-----------------------------|
| Apache Hadoop 和 YARN | 2.7.3                       |
| Apache Tez             | 0.7.0                       |
| Apache Pig             | 0.16.0                      |
| Apache Hive            |  (ESP 交互式查询) 上的2.1。0 |
| Apache Tez Hive2       | 0.8.4                       |
| Apache Ranger          | 0.7.0                       |
| Apache HBase           | 1.1.2                       |
| Apache Sqoop           | 1.4.6                       |
| Apache Oozie           | 4.2.0                       |
| Apache Zookeeper       | 3.4.6                       |
| Apache Storm           | 1.1.0                       |
| Apache Mahout          | 0.9.0+                      |
| Apache Phoenix         | 4.7.0                       |
| Apache Spark           | 2.3.2.                      |
| Apache Livy            | 0.4.                        |
| Apache Kafka           | 1.1                         |
| Apache Ambari          | 2.6.0                       |
| Apache Zeppelin        | 0.7.3                       |
| Mono                   | 4.2.1                       |

## <a name="next-steps"></a>后续步骤

- [为 HDInsight 上的 Apache Hadoop、Spark 和其他组件设置群集](hdinsight-hadoop-provision-linux-clusters.md)
- [企业安全性套餐](./enterprise-security-package.md)
- [使用 Windows 电脑在 HDInsight 上的 Apache Hadoop 中工作](hdinsight-hadoop-windows-tools.md)