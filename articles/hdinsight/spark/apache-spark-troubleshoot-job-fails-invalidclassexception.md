---
title: Apache Spark 中的 InvalidClassException 错误 - Azure HDInsight
description: 在 Azure HDInsight 中，Apache Spark 作业因 InvalidClassException（类版本不匹配）而失败
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/29/2019
ms.openlocfilehash: 936e0728e70ceba35fbde105d68d77b9fbace45a
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112288832"
---
# <a name="apache-spark-job-fails-with-invalidclassexception-class-version-mismatch-in-azure-hdinsight"></a>在 Azure HDInsight 中，Apache Spark 作业因 InvalidClassException（类版本不匹配）而失败

本文介绍在 Azure HDInsight 群集中使用 Apache Spark 组件时出现的问题的故障排除步骤和可能的解决方案。

## <a name="issue"></a>问题

尝试在 Spark 2.x 群集中创建 Apache Spark 作业。 它失败，出现类似于以下内容的错误：

```
18/09/18 09:32:26 WARN TaskSetManager: Lost task 0.0 in stage 1.0 (TID 1, wn7-dev-co.2zyfbddadfih0xdq0cdja4g.ax.internal.cloudapp.net, executor 4): java.io.InvalidClassException:
org.apache.commons.lang3.time.FastDateFormat; local class incompatible: stream classdesc serialVersionUID = 2, local class serialVersionUID = 1
        at java.io.ObjectStreamClass.initNonProxy(ObjectStreamClass.java:699)
        at java.io.ObjectInputStream.readNonProxyDesc(ObjectInputStream.java:1885)
        at java.io.ObjectInputStream.readClassDesc(ObjectInputStream.java:1751)
        at java.io.ObjectInputStream.readOrdinaryObject(ObjectInputStream.java:2042)
        at java.io.ObjectInputStream.readObject0(ObjectInputStream.java:1573)
```

## <a name="cause"></a>原因

此错误可能是由于向 `spark.yarn.jars` 配置中添加其他 JAR 导致的，具体来说是包含不同版本的 `commons-lang3` 包并引入类不匹配的阴影 JAR。 默认情况下，Spark 2.1/2/3 使用版本 3.5 的 `commons-lang3`。

> [!TIP]
> 为库添加阴影就是将其内容放入你自己的 jar 中，并更改其包。 这与打包库不同，后者是将库放入你自己的 jar 中，而不重新打包。

## <a name="resolution"></a>解决方案

删除 jar，或重新编译自定义 jar (AzureLogAppender)，并使用 [maven-shade-plugin](https://maven.apache.org/plugins/maven-shade-plugin/examples/class-relocation.html) 重新定位类。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]