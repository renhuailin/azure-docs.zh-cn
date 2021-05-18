---
title: Azure HDInsight 中的 Apache Ambari 过时警报
description: 讨论和分析 HDInsight 中导致 Apache Ambari 警报过时的可能原因和解决方法。
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: 22985f295e504a2d73015f1dc96064f89be47bd7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104866924"
---
# <a name="scenario-apache-ambari-stale-alerts-in-azure-hdinsight"></a>方案：Azure HDInsight 中的 Apache Ambari 过时警报

本文介绍在与 Azure HDInsight 群集交互时出现的问题的故障排除步骤和可能的解决方案。

## <a name="issue"></a>问题

在 Apache Ambari UI 中，你可能会看到如下所示的警报：

:::image type="content" source="./media/apache-ambari-troubleshoot-stale-alerts/ambari-stale-alerts-example.png" alt-text="Apache Ambari 过时警报示例" border="true":::

## <a name="cause"></a>原因

Ambari 代理会持续监视许多资源的运行状况。 可以将警报配置为通知你特定群集属性是否在预先确定的阈值范围内。 每次运行资源检查后，如果满足警报条件，Ambari 代理会将状态报告回 Ambari 服务器，并触发警报。 如果未根据警报配置文件中的间隔检查警报，则服务器将触发“Ambari 服务器警报过时”警报。

运行状况检查不按定义的间隔运行的原因有多种：

* 主机的使用量较高（CPU 使用率高），因此 Ambari 代理无法获得足够的系统资源来按时运行警报。

* 在负载繁重的期间内，群集正忙于执行许多作业或服务。

* 群集中的少量主机承载许多组件，因此需要运行许多警报。 如果组件数量很大，警报作业可能会错过其计划的间隔。

## <a name="resolution"></a>解决方法

请尝试以下方法来解决 Ambari 警报过时的问题。

### <a name="increase-the-alert-interval-time"></a>增大警报间隔时间

可以根据群集的响应时间及其负载，增大单个警报间隔的值：

1. 在 Apache Ambari UI 中，选择“警报”选项卡。
1. 选择所需的警报定义名称。
1. 在定义中选择“编辑”。
1. 增大“检查间隔”值，然后选择“保存”。 

### <a name="increase-the-alert-interval-time-for-ambari-server-alerts"></a>增大 Ambari 服务器警报的警报间隔时间

1. 在 Apache Ambari UI 中，选择“警报”选项卡。
1. 在“组”下拉列表中，选择“AMBARI 默认值”。 
1. 选择警报“Ambari 服务器警报”。
1. 在定义中选择“编辑”。
1. 增大“检查间隔”值。
1. 增大“间隔乘数”值，然后选择“保存”。 

### <a name="disable-and-reenable-the-alert"></a>禁用警报，然后重新启用警报

若要放弃过时警报，请禁用它，然后重新启用它：

1. 在 Apache Ambari UI 中，选择“警报”选项卡。
1. 选择所需的警报定义名称。
1. 在定义中，选择位于 UI 最右侧的“已启用”。
1. 在“确认”弹出窗口中，选择“确认禁用”。 
1. 等待几秒，以便页面上显示的所有警报“实例”均被清除。
1. 在定义中，选择位于 UI 最右侧的“已禁用”。
1. 在“确认”弹出窗口中，选择“确认启用”。 

### <a name="increase-the-alert-grace-period"></a>增大警报宽限期

在 Ambari 代理报告已配置的警报错过了其计划之前，会有一个宽限期。 如果警报错过了其计划时间，但在宽限期内运行，则不会产生过时警报。

默认的 `alert_grace_period` 值为 5 秒。 可以在 /etc/ambari-agent/conf/ambari-agent.ini 中配置此设置。 对于定期发生过时警报的主机，请尝试将该值增大到 10。 然后，重启 Ambari 代理。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者你无法解决问题，请访问以下渠道之一，以获取更多支持：

* 通过 [Azure 社区支持](https://azure.microsoft.com/support/community/)获取 Azure 专家的解答。

* 在 Twitter 上联系 [@AzureSupport](https://twitter.com/azuresupport)。 这是用于改进客户体验的官方 Microsoft Azure 帐户。 它可以将 Azure 社区成员引导至适当的资源，为他们提供解答、支持和专家建议。

* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 为此，请从门户菜单中选择“帮助 ( **?** )”，或打开“帮助 + 支持”窗格。 有关详细信息，请参阅[如何创建 Azure 支持请求](../../azure-portal/supportability/how-to-create-azure-support-request.md)。 

  Microsoft Azure 订阅中包含对订阅管理和计费的支持。 可通过 [Azure 支持计划](https://azure.microsoft.com/support/plans/)获取技术支持。