---
title: Azure HDInsight 中的“hbase hbck”命令超时
description: 修复区域分配时出现“hbase hbck”命令超时问题
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/16/2019
ms.openlocfilehash: acf4d1237841f8c20c014598e00a5e8961e2a012
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98936701"
---
# <a name="scenario-timeouts-with-hbase-hbck-command-in-azure-hdinsight"></a>场景：Azure HDInsight 中的“hbase hbck”命令超时

本文介绍在与 Azure HDInsight 群集交互时出现的问题的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

修复区域分配时遇到 `hbase hbck` 命令超时。

## <a name="cause"></a>原因

使用 `hbck` 命令时出现超时问题的可能原因是多个区域长时间处于“正在转换”状态。 在 HBase Master UI 中可以看到这些区域显示为脱机。 由于有大量区域正在尝试进行转换，因此，HBase Master 可能会超时并且无法使那些区域恢复联机。

## <a name="resolution"></a>解决方法

1. 使用 SSH 登录到 HDInsight HBase 群集。

1. 运行 `hbase zkcli` 命令连接 Apache ZooKeeper shell。

1. 运行 `rmr /hbase/regions-in-transition` 或 `rmr /hbase-unsecure/regions-in-transition` 命令。

1. 使用 `exit` 命令从 `hbase zkcli` shell 退出。

1. 在 Apache Ambari UI 中，重启 Active HBase Master 服务。

1. 运行 `hbase hbck -fixAssignments` 命令。

1. 监视 HBase Master UI 的“转换中的区域”部分，以确保没有区域停滞。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

- 通过 [Azure 社区支持](https://azure.microsoft.com/support/community/)获取 Azure 专家的解答。

- 联系 [@AzureSupport](https://twitter.com/azuresupport)，这是用于改进客户体验的官方 Microsoft Azure 帐户。 它可以将 Azure 社区成员连接到适当的资源，为他们提供解答、支持和专家建议。

- 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”  ，或打开“帮助 + 支持”  中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](../../azure-portal/supportability/how-to-create-azure-support-request.md)。 Microsoft Azure 订阅中带有对订阅管理和计费支持的访问权限，技术支持通过 [Azure 支持计划](https://azure.microsoft.com/support/plans/)之一提供。