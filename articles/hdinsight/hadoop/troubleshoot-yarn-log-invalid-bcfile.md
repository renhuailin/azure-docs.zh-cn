---
title: 在 Azure HDInsight 中无法读取 Apache Yarn 日志
description: 与 Azure HDInsight 群集交互时出现的问题的故障排除步骤和可能的解决方法。
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: 02a79de8aee169f5f702d5fae67194c62363e8c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98943052"
---
# <a name="scenario-unable-to-read-apache-yarn-log-in-azure-hdinsight"></a>场景：在 Azure HDInsight 中无法读取 Apache Yarn 日志

本文介绍在与 Azure HDInsight 群集交互时出现的问题的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

从存储帐户找到的 Apache Yarn 日志无法人工读取。 文件分析程序不起作用，并产成以下错误消息：

```
java.io.IOException: Not a valid BCFile.
```

## <a name="cause"></a>原因

Apache Yarn 日志被聚合为 `IndexFile` 格式，但文件分析程序不支持该格式。

## <a name="resolution"></a>解决方法

1. 在 Web 浏览器中导航到 `https://CLUSTERNAME.azurehdinsight.net`，其中的 `CLUSTERNAME` 是群集的名称。

1. 在 Ambari UI 中，导航到“YARN” > “配置” > “高级” > “高级 Yarn 站点”。   

1. 对于 WASB 存储：`yarn.log-aggregation.file-formats` 的默认值为 `IndexedFormat,TFile`。 将值更改为 `TFile`。

1. 对于 ADLS 存储：`yarn.nodemanager.log-aggregation.compression-type` 的默认值为 `gz`。 将值更改为 `none`。

1. 保存更改并重启所有受影响的服务。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 社区支持](https://azure.microsoft.com/support/community/)获取 Azure 专家的解答。

* 联系 [@AzureSupport](https://twitter.com/azuresupport)，这是用于改进客户体验的官方 Microsoft Azure 帐户。 它可以将 Azure 社区成员连接到适当的资源，为他们提供解答、支持和专家建议。

* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”  ，或打开“帮助 + 支持”  中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](../../azure-portal/supportability/how-to-create-azure-support-request.md)。 Microsoft Azure 订阅中带有对订阅管理和计费支持的访问权限，技术支持通过 [Azure 支持计划](https://azure.microsoft.com/support/plans/)之一提供。