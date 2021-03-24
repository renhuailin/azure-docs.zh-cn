---
title: Azure HDInsight 中的 Hive 的安全选项
description: 标准群集和 ESP 群集中的 Hive 的安全选项。
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/02/2020
ms.openlocfilehash: a608c34225641a3c7764d6c7dd3872c5f61fe3c8
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2021
ms.locfileid: "104869712"
---
# <a name="security-options-for-hive-in-azure-hdinsight"></a>Azure HDInsight 中的 Hive 的安全选项

本文档介绍了 HDInsight 中的 Hive 的推荐安全选项。 这些选项可以通过 Ambari 进行配置。

:::image type="content" source="./media/hdinsight-security-options-for-hive/security-options-hive.png " alt-text="`Security Options for Hive`" border="true":::

## <a name="hiveserver2-authentication"></a>HiveServer2 身份验证

对于标准群集，推荐的 HiveServer2 身份验证设置为默认值“无”。 若要启用身份验证，我们建议升级到 [ESP](../domain-joined/hdinsight-security-overview.md)（企业安全性套餐）群集。 

对于 ESP 群集，默认启用 [Kerberos](https://web.mit.edu/Kerberos/) 身份验证。 不支持插入式身份验证模块 (PAM) 和自定义身份验证方案。

## <a name="hiveserver2-authorization"></a>HiveServer2 授权

对于标准群集，默认设置为“无”。 可以启用 [SqlStdAuth（基于 SQL 标准的授权）](https://cwiki.apache.org/confluence/display/Hive/SQL+Standard+based+hive+authorization)。 标准群集不支持通过 [Apache Ranger](https://ranger.apache.org/) 进行授权。 建议升级到 ESP 群集以进行 Ranger 授权。 

对于 ESP 群集，默认启用通过 Ranger 授权。 


## <a name="ssl-encryption-for-hiveserver2"></a>HiveServer2 的 SSL 加密

不建议为标准群集或 ESP 群集启用 Hiveserver2 SSL。 而应在网关上启用 SSL。 可以启用[传输中加密](../domain-joined/encryption-in-transit.md)，以使用 [Internet 协议安全性 (IPSec)](https://en.wikipedia.org/wiki/IPsec) 对群集节点之间的通信进行加密。


## <a name="next-steps"></a>后续步骤
* [HiveServer2 身份验证概述](https://cwiki.apache.org/confluence/display/Hive/Setting+up+HiveServer2#SettingUpHiveServer2-Authentication/SecurityConfiguration)
* [HiveServer2 授权概述](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Authorization)
* [启用基于 SQL 标准的 Hive 授权](https://community.cloudera.com/t5/Community-Articles/Getting-started-with-SQLStdAuth/ta-p/244263)
* [Apache Ranger 与 Hive](../domain-joined/apache-domain-joined-run-hive.md)
