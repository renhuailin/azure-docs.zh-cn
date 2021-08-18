---
title: 对 Azure HDInsight 中的 Apache Oozie 进行故障排查
description: 在 Azure HDInsight 中排查特定 Apache Oozie 错误。
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/27/2020
ms.openlocfilehash: 4dfc85b566f464a6c0fd49f7859f2c62aad1d8c4
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112283872"
---
# <a name="troubleshoot-apache-oozie-in-azure-hdinsight"></a>对 Azure HDInsight 中的 Apache Oozie 进行故障排查

使用 Apache Oozie UI 可查看 Oozie 日志。 Oozie UI 还包含一些链接，指向工作流启动的 MapReduce 任务的 JobTracker 日志。 故障排除的模式应为：

1. 在 Oozie Web UI 中查看作业。

2. 如果特定的操作出错或失败，请选择该操作，以查看“错误消息”字段是否提供了有关失败的详细信息。

3. 如果已提供，请使用操作中的 URL 查看该操作的更多详细信息（例如 JobTracker 日志）。

下面是可能会遇到的特定错误及其解决方法。

## <a name="ja009-cant-initialize-cluster"></a>JA009:无法初始化群集

### <a name="issue"></a>问题

作业状态变为“SUSPENDED”。 作业详细信息中的 `RunHiveScript` 状态显示为“START_MANUAL”。 选择该操作会显示以下错误消息：

```output
JA009: Cannot initialize Cluster. Please check your configuration for map
```

### <a name="cause"></a>原因

**job.xml** 文件中使用的 Azure Blob 存储地址不包含存储容器或存储帐户名。 Blob 存储地址格式必须是 `wasbs://containername@storageaccountname.blob.core.windows.net`。

### <a name="resolution"></a>解决方法

更改作业使用的 Blob 存储地址。

---

## <a name="ja002-oozie-isnt-allowed-to-impersonate-ltusergt"></a>JA002:不允许 Oozie 模拟 &lt;USER&gt;

### <a name="issue"></a>问题

作业状态变为“SUSPENDED”。 作业详细信息中的 `RunHiveScript` 状态显示为“START_MANUAL”。 选择操作会显示以下错误消息：

```output
JA002: User: oozie is not allowed to impersonate <USER>
```

### <a name="cause"></a>原因

当前的权限设置不允许 Oozie 模拟指定的用户帐户。

### <a name="resolution"></a>解决方法

允许 Oozie 模拟 `users` 组中的用户。 使用 `groups USERNAME` 查看用户帐户所属的组。 如果该用户不是 `users` 组的成员，请使用以下命令将该用户添加到该组：

```bash
sudo adduser USERNAME users
```

> [!NOTE]  
> 可能需要几分钟，HDInsight 才能识别用户已添加到该组。

---

## <a name="launcher-error-sqoop"></a>启动器错误 (Sqoop)

### <a name="issue"></a>问题

作业状态变为“KILLED”。 作业详细信息中的 `RunSqoopExport` 状态显示为“ERROR”。 选择操作会显示以下错误消息：

```output
Launcher ERROR, reason: Main class [org.apache.oozie.action.hadoop.SqoopMain], exit code [1]
```

### <a name="cause"></a>原因

Sqoop 无法加载访问数据库时所需的数据库驱动程序。

### <a name="resolution"></a>解决方法

从 Oozie 作业使用 Sqoop 时，必须同时包含数据库驱动程序和作业使用的其他资源（例如 workflow.xml）。 此外，通过 workflow.xml 的 `<sqoop>...</sqoop>` 节引用包含数据库驱动程序的存档。

例如，对于[使用 Hadoop Oozie 工作流](hdinsight-use-oozie-linux-mac.md)中的作业示例，使用以下步骤：

1. 将 `mssql-jdbc-7.0.0.jre8.jar` 文件复制到 **/tutorials/useoozie** 目录：

    ```bash
    hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc-7.0.0.jre8.jar /tutorials/useoozie/mssql-jdbc-7.0.0.jre8.jar
    ```

2. 修改 `workflow.xml`，在 `</sqoop>` 上方的新行中添加以下 XML：

    ```xml
    <archive>mssql-jdbc-7.0.0.jre8.jar</archive>
    ```

## <a name="next-steps"></a>后续步骤

[!INCLUDE [troubleshooting next steps](includes/hdinsight-troubleshooting-next-steps.md)]