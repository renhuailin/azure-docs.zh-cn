---
title: Sqoop 导入/导出命令对 ESP 群集中的某些用户失败 - Azure HDInsight
description: '对于 Azure HDInsight ESP 群集中的某些用户，Apache Sqoop 导入/导出命令失败并出现“导入失败: java.io.IOException: 对分段目录 /user/yourusername/.staging 的所有权不符合预期”错误'
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/01/2021
ms.openlocfilehash: 79aae099e30bae7d5201fae3384f7b89b6c30bcf
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2021
ms.locfileid: "106387130"
---
# <a name="scenario-sqoop-importexport-command-fails-for-usernames-greater-than-20-characters-in-azure-hdinsight-esp-clusters"></a>场景：对于 Azure HDInsight ESP 群集中超过 20 个字符的用户名，Sqoop 导入/导出命令失败

本文描述了在通过 ADLS Gen2 (ABFS) 存储帐户使用已启用 Azure HDInsight ESP (Enterprise Security Pack) 的群集时存在的一个已知问题及其解决方法。

## <a name="issue"></a>问题

某些用户在运行 sqoop 导入/导出命令时，该命令失败并出现以下错误：

```
ERROR tool.ImportTool: Import failed: java.io.IOException:
The ownership on the staging directory /user/yourlongdomainuserna/.staging is not as expected. 
It is owned by yourlongdomainusername.
The directory must be owned by the submitter yourlongdomainuserna or yourlongdomainuserna@AADDS.CONTOSO.COM
```

在以上示例中，`/user/yourlongdomainuserna/.staging` 显示了用户名 `yourlongdomainusername` 在截断后包含 20 个字符的用户名。

## <a name="cause"></a>原因

用户名长度超过 20 个字符。 

有关更多详细信息，请参阅[如何在 Azure Active Directory 域服务托管域中同步对象和凭据](../active-directory-domain-services/synchronization.md)。

## <a name="workaround"></a>解决方法

使用小于或等于 20 个字符的用户名。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]
