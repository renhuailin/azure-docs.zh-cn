---
title: 通过 Azure HDInsight 中的 Apache Ambari Hive 视图运行查询时出现异常
description: 使用 Azure HDInsight 中的 Apache Ambari Hive 视图运行 Apache Hive 查询时的故障排除步骤。
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 12/23/2019
ms.openlocfilehash: 40ef380cd2bd4743b92daf44a0a5b70ade1cbb35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98933013"
---
# <a name="exception-when-running-queries-from-apache-ambari-hive-view-in-azure-hdinsight"></a>通过 Azure HDInsight 中的 Apache Ambari Hive 视图运行查询时出现异常

本文介绍在与 Azure HDInsight 群集交互时出现的问题的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

通过 Apache Ambari Hive 视图运行 Apache Hive 查询时，会间歇收到以下错误消息：

```error
Cannot create property 'errors' on string '<!DOCTYPE html PUBLIC '-//W3C//DTD XHTML 1.0 Strict//EN' 'http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd'>
<html xmlns='http://www.w3.org/1999/xhtml'>
<head>
<title>IIS 8.5 Detailed Error - 502.3 - Bad Gateway</title>
```

## <a name="cause"></a>原因

网关超时。

网关超时时间值为 2 分钟。 使用 Ambari Hive 视图运行的查询将通过网关提交给 `/hive2` 终结点。 成功编译和接受查询后，HiveServer 将返回 `queryid`。 客户端将轮询查询的状态。 在此过程中，如果 HiveServer 在 2 分钟内未返回 HTTP 响应，则 HDI 网关会向调用方发出 502.3 网关超时错误。 提交查询进行处理时以及在获取状态调用时，都可能发生错误，前者出现错误的可能性更大。 用户可以查看其中任何一种情况。

Http 处理程序线程应快速响应：准备作业并返回 `queryid`。 然而，由于某些原因，所有处理程序线程可能十分繁忙，导致新查询和获取状态调用超时。

### <a name="responsibilities-of-the-http-handler-thread"></a>HTTP 处理程序线程的责任

当客户端将查询提交到 HiveServer 时，会在前台线程中执行以下操作：

* 分析请求，执行语义验证
* 获取锁定
* 元存储查找（如有必要）
* 编译查询（DDL 或 DML）
* 准备查询计划
* 执行授权（在安全群集中运行所有适用的 ranger 策略）

## <a name="resolution"></a>解决方法

以下提供一些常规建议改进此类情况：

* 如果使用外部 hive 元存储，请检查数据库指标，确保数据库未超载。 考虑缩放元存储数据库层。

* 确保并行 ops 处于开启状态（这使 HTTP 处理程序线程能够并行运行）。 要验证该值，请启动 [Apache Ambari](../hdinsight-hadoop-manage-ambari.md) 并导航到“Hive  > 配置 > 高级 > 自定义 Hive 站点”   。 `hive.server2.parallel.ops.in.session` 的值应为 `true`。

* 确保群集的 VM SKU 大小要适合负载。 考虑为多个群集拆分工作。 有关详细信息，请参阅[选择群集类型](../hdinsight-capacity-planning.md#choose-a-cluster-type)。

* 如果群集上安装了 Ranger，请检查是否有过多的 Ranger 策略需要为每个查询进行评估。 查找重复或不需要的策略。

* 在 Ambari 中检查“HiveServer2 堆大小”值。 导航到“Hive  > 配置 > 设置 > 优化”。    请确保该值大于 10 GB。 根据需要进行调整以优化性能。

* 确保 Hive 查询经过适当的优化。 有关详细信息，请参阅[优化 Azure HDInsight 中的 Apache Hive 查询](../hdinsight-hadoop-optimize-hive-query.md)。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 社区支持](https://azure.microsoft.com/support/community/)获取 Azure 专家的解答。

* 联系 [@AzureSupport](https://twitter.com/azuresupport)，这是用于改进客户体验的官方 Microsoft Azure 帐户。 它可以将 Azure 社区成员连接到适当的资源，为他们提供解答、支持和专家建议。

* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”  ，或打开“帮助 + 支持”  中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](../../azure-portal/supportability/how-to-create-azure-support-request.md)。 Microsoft Azure 订阅中带有对订阅管理和计费支持的访问权限，技术支持通过 [Azure 支持计划](https://azure.microsoft.com/support/plans/)之一提供。