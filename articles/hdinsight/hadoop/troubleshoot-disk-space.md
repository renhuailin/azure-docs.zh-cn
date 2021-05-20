---
title: 在 Azure HDInsight 中管理磁盘空间
description: 当与 Azure HDInsight 群集交互时，用于管理磁盘空间问题的故障排除步骤和可能的解决方案。
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/17/2020
ms.openlocfilehash: 7164494cb08c4b419b9e4d96075ace3e52187497
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98944826"
---
# <a name="manage-disk-space-in-azure-hdinsight"></a>在 Azure HDInsight 中管理磁盘空间

本文介绍在与 Azure HDInsight 群集交互时出现的问题的故障排除步骤和可能的解决方法。

## <a name="hive-log-configurations"></a>Hive 日志配置

1. 在 Web 浏览器中导航到 `https://CLUSTERNAME.azurehdinsight.net`，其中的 `CLUSTERNAME` 是群集的名称。

1. 依次转到“Hive” > “配置” > “高级” > “高级 hive-log4j”。 检查以下设置：

    * `hive.root.logger=DEBUG,RFA`. 此为默认值，将[“日志级别”](https://logging.apache.org/log4j/2.x/log4j-api/apidocs/org/apache/logging/log4j/Level.html)修改为“`INFO`”可以减少日志打印条目。

    * `log4jhive.log.maxfilesize=1024MB`. 此为默认值，可以根据需要修改。

    * `log4jhive.log.maxbackupindex=10`. 此为默认值，可以根据需要修改。 如果省略了参数，则生成的日志文件将是无穷无尽的。

## <a name="yarn-log-configurations"></a>Yarn 日志配置

检查以下配置：

* Apache Ambari

    1. 在 Web 浏览器中，导航到 `https://CLUSTERNAME.azurehdinsight.net`，其中 `CLUSTERNAME` 是群集的名称。

    1. 依次转到“Hive” > “配置” > “高级” > “资源管理器”。 请务必选中“启用日志聚合”。 如果禁用，则名称节点在本地保存日志，而不会在应用程序完成或终止时将它们聚合到远程存储中。

* 确保群集大小适合工作负荷。 工作负载可能最近发生了变化，或者群集可能已经调整了大小。 [纵向扩展](../hdinsight-scaling-best-practices.md)群集来匹配更高的工作负载。

* `/mnt/resource` 可能由孤立文件填充（如在资源管理器重启的情况下）。 如有必要，请手动清理 `/mnt/resource/hadoop/yarn/log` 和 `/mnt/resource/hadoop/yarn/local`。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 社区支持](https://azure.microsoft.com/support/community/)获取 Azure 专家的解答。

* 联系 [@AzureSupport](https://twitter.com/azuresupport)，这是用于改进客户体验的官方 Microsoft Azure 帐户。 它可以将 Azure 社区成员连接到适当的资源，为他们提供解答、支持和专家建议。

* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”  ，或打开“帮助 + 支持”  中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](../../azure-portal/supportability/how-to-create-azure-support-request.md)。 Microsoft Azure 订阅中带有对订阅管理和计费支持的访问权限，技术支持通过 [Azure 支持计划](https://azure.microsoft.com/support/plans/)之一提供。