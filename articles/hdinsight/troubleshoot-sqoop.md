---
title: 对于 ESP 群集中的某些用户，Sqoop import/export 命令失败 - Azure HDInsight
description: '对于 Azure HDInsight ESP 群集中的某些用户，Apache Sqoop import/export 命令失败，并显示“导入失败: java.io.IOException: 分段目录 /user/yourusername/.staging 的所有权不符合预期”错误'
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/01/2021
ms.openlocfilehash: 2eba44d735b1b8ea9a88164ede0ca4860efcce8a
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112283926"
---
# <a name="scenario-sqoop-importexport-command-fails-for-usernames-greater-than-20-characters-in-azure-hdinsight-esp-clusters"></a>场景：对于 Azure HDInsight ESP 群集中超过 20 个字符的用户名，Sqoop import/export 命令失败

本文介绍了将启用 Azure HDInsight ESP（企业安全包）的群集与 ADLS Gen2 (ABFS) 存储帐户一起使用时的已知问题和解决方法。

## <a name="issue"></a>问题

对于某些用户，运行 sqoop import/export 命令时失败，并显示以下错误：

```
ERROR tool.ImportTool: Import failed: java.io.IOException:
The ownership on the staging directory /user/yourlongdomainuserna/.staging is not as expected. 
It is owned by yourlongdomainusername.
The directory must be owned by the submitter yourlongdomainuserna or yourlongdomainuserna@AADDS.CONTOSO.COM
```

在上面的示例中，对于用户名 `yourlongdomainusername`，`/user/yourlongdomainuserna/.staging` 显示被截断为 20 个字符的用户名。

## <a name="cause"></a>原因

用户名长度超过 20 个字符。 

请参阅[如何在 Azure Active Directory 域服务托管域中同步对象和凭据](../active-directory-domain-services/synchronization.md)，了解详细信息。

## <a name="workaround"></a>解决方法

使用小于或等于 20 个字符的用户名。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [troubleshooting next steps](includes/hdinsight-troubleshooting-next-steps.md)]
