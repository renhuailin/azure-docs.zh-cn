---
title: 无法登录到 Azure HDInsight 群集
description: 排查无法登录到 Azure HDInsight 中 Apache Hadoop 群集的原因
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/31/2019
ms.openlocfilehash: eab5097ef27865b2d5d46370f60aebdac3739d24
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112299674"
---
# <a name="scenario-unable-to-log-into-azure-hdinsight-cluster"></a>场景：无法登录到 Azure HDInsight 群集

本文介绍在与 Azure HDInsight 群集交互时出现的问题的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

无法登录到 Azure HDInsight 群集。

## <a name="cause"></a>原因

原因可能有所不同。 请记住，在登录到群集或应用仪表板时，请使用“群集登录名”或 HTTP 凭据。 从远程连接时，请使用安全外壳 (SSH) 或远程桌面凭据。

## <a name="resolution"></a>解决方法

若要解决常见问题，请尝试下面的一个或多个步骤。

* 尝试在新的浏览器选项卡中以隐私模式打开群集仪表板。

* 如果忘记了 SSH 凭据，可以[在 Ambari UI 中重置凭据](../hdinsight-administer-use-portal-linux.md#change-passwords)。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]