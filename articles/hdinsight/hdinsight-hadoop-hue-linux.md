---
title: 在基于 HDInsight Linux 的群集上将 Hue 与 Hadoop 配合使用 - Azure
description: 了解如何在 HDInsight 群集上安装 Hue，并使用隧道将请求路由至 Hue。 使用 Hue 浏览存储和运行 Hive 或 Pig。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 03/31/2020
ms.openlocfilehash: 8f0346a3e9a2d21dc5959df51d894daef8a5a23f
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112284196"
---
# <a name="install-and-use-hue-on-hdinsight-hadoop-clusters"></a>在 HDInsight Hadoop 群集上安装并使用 Hue

了解如何在 HDInsight 群集上安装 Hue，并使用隧道将请求路由至 Hue。

## <a name="what-is-hue"></a>什么是 Hue？

Hue 是一组 Web 应用程序，用来与 Apache Hadoop 群集交互。 可以使用 Hue 浏览与 Hadoop 群集关联的存储（在 HDInsight 群集的案例中为 WASB）、运行 Hive 作业和 Pig 脚本等等。 HDInsight Hadoop 群集上的 Hue 安装提供以下组件。

* Beeswax Hive 编辑器
* Apache Pig
* 元存储管理器
* Apache Oozie
* FileBrowser（与 WASB 默认容器进行通信）
* 作业浏览器

> [!WARNING]  
> 完全支持通过 HDInsight 群集提供的组件，Microsoft 支持部门将帮助你找出并解决与这些组件相关的问题。
>
> 自定义组件可获得合理范围的支持，以帮助你进一步排查问题。 这可能导致问题解决，或要求参与可用的开放源代码技术渠道，在该处可找到该技术的深入专业知识。 例如，有许多可以使用的社区站点，例如：[关于 HDInsight 的 Microsoft Q&A 问题页](/answers/topics/azure-hdinsight.html)、[https://stackoverflow.com](https://stackoverflow.com)。 此外，Apache 项目在 [https://apache.org](https://apache.org) 上提供了项目站点，例如：[Hadoop](https://hadoop.apache.org/)。

## <a name="install-hue-using-script-actions"></a>使用脚本操作安装 Hue

请参照下表中的信息执行脚本操作。 有关如何使用脚本操作的具体说明，请参阅[使用脚本操作自定义 HDInsight 群集](hdinsight-hadoop-customize-cluster-linux.md)。

> [!NOTE]  
> 若要在 HDInsight 群集上安装 Hue，建议的头节点大小为至少 A4（8 核、14 GB 内存）。

|properties |值 |
|---|---|
|脚本类型：|- Custom|
|名称|安装 Hue|
|Bash 脚本 URI|`https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh`|
|节点类型：|Head|

## <a name="use-hue-with-hdinsight-clusters"></a>将 Hue 与 HDInsight 群集搭配使用

在常规群集上，只能有一个含 Hue 的用户帐户。 对于多用户访问，请在群集上启用[企业安全性套餐](./domain-joined/hdinsight-security-overview.md)。 SSH 隧道是在群集运行后访问 Hue 的唯一方式。 通过 SSH 的隧道允许流量直接流向运行 Hue 的群集的头节点。 在群集完成预配后，请按照以下步骤操作，以在 HDInsight 群集上使用 Hue。

> [!NOTE]  
> 建议使用 Firefox Web 浏览器按照下面的说明操作。

1. 利用[使用 SSH 隧道来访问 Apache Ambari Web UI、ResourceManager、JobHistory、NameNode、Oozie 及其他 Web UI](hdinsight-linux-ambari-ssh-tunnel.md) 中的信息，创建从客户端系统到 HDInsight 群集的 SSH 隧道，并将 Web 浏览器配置为使用 SSH 隧道作为代理。

1. 使用 [ssh 命令](./hdinsight-hadoop-linux-use-ssh-unix.md)连接到群集。 编辑以下命令，将 CLUSTERNAME 替换为群集的名称，然后输入该命令：

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. 连接后，使用以下命令获取主头节点的完全限定域名：

    ```bash
    hostname -f
    ```

    此命令将返回类似于下面的名称：

    ```output
    myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net
    ```

    这是 Hue 网站所在的主头节点的主机名。

1. 使用浏览器打开 Hue 门户 ( `http://HOSTNAME:8888` )。 将 HOSTNAME 替换为在上一步骤中获取的名称。

   > [!NOTE]  
   > 第一次登录时，系统会提示创建帐户来登录 Hue 门户。 在此处指定的凭据只能用于该门户，并且与预配群集时指定的管理员或 SSH 用户凭据不相关。

    :::image type="content" source="./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-login.png" alt-text="HDInsight Hue 门户登录窗口":::

### <a name="run-a-hive-query"></a>运行 Hive 查询

1. 在 Hue 门户中，依次选择“查询编辑器”和“Hive”，以打开 Hive 编辑器。

    :::image type="content" source="./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-use-hive.png" alt-text="在 HDInsight Hue 门户中使用 Hive 编辑器":::

2. 在“帮助”选项卡上的“数据库”下面，应会看到 **hivesampletable**。 这是 HDInsight 上的所有 Hadoop 群集随附的示例表。 在右窗格中输入示例查询，并在下方窗格的“结果”选项卡中查看输出，如屏幕截图所示。

    :::image type="content" source="./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-hive-query.png" alt-text="HDInsight Hue 门户 Hive 查询":::

    也可以使用“图表”选项卡查看结果的视觉表示形式。

### <a name="browse-the-cluster-storage"></a>浏览群集存储

1. 在 Hue 门户中，选择菜单栏右上角的“文件浏览器”。
2. 默认情况下，文件浏览器在 **/user/myuser** 目录中打开。 选择路径中紧接在用户目录前面的正斜杠，以转到与群集关联的 Azure 存储容器的根目录。

    :::image type="content" source="./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-file-browser.png" alt-text="HDInsight Hue 门户文件浏览器":::

3. 右键单击某个文件或文件夹，以查看可用的操作。 使用右侧的“上传”按钮，将文件上传到当前目录。 使用“新建”按钮创建新的文件或目录。

> [!NOTE]  
> Hue 文件浏览器只能显示与 HDInsight 群集关联的默认容器的内容。 与群集关联的任何其他存储帐户/容器将无法使用文件浏览器访问。 不过，与群集关联的其他容器始终可供 Hive 作业访问。 例如，如果在 Hive 编辑器中输入 `dfs -ls wasbs://newcontainer@mystore.blob.core.windows.net` 命令，也可以看到其他容器的内容。 在此命令中，**newcontainer** 不是与群集关联的默认容器。

## <a name="important-considerations"></a>重要注意事项

1. 用于安装 Hue 的脚本只会在群集的主头节点上安装它。

1. 在安装期间，系统会重启多个 Hadoop 服务（HDFS、YARN、MR2、Oozie），以更新配置。 在脚本安装完 Hue 之后，可能需要一些时间让其他 Hadoop 服务启动。 一开始可能会影响 Hue 的性能。 等所有服务都启动之后，Hue 就可以完全正常运行。

1. Hue 不了解 Apache Tez 作业，这是当前的 Hive 默认值。 如果想使用 MapReduce 作为 Hive 执行引擎，请更新脚本，以在脚本中使用以下命令：

   `set hive.execution.engine=mr;`

1. 使用 Linux 群集时，可能会出现这种情况：服务在主头节点上运行，而 Resource Manager 可能在辅助头节点上运行。 使用 Hue 查看群集上正在运行的作业的详细信息时，这种情况可能会导致错误（如下所示）。 不过，可以在作业完成后查看作业详细信息。

   :::image type="content" source="./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-error.png" alt-text="Hue 门户错误示例消息":::

   这是由已知问题造成的。 解决方法如下：修改 Ambari，使活动 Resource Manager 也在主头节点上运行。

1. 当 HDInsight 群集使用 Azure 存储（使用 `wasbs://`）时，Hue 能识别 WebHDFS。 因此，搭配脚本操作使用的自定义脚本会安装 WebWasb，这是用来与 WASB 通信的 WebHDFS 兼容服务。 因此，即使 Hue 门户中显示 HDFS（例如，将鼠标移到“文件浏览器”上时），也应该将它解释为 WASB。

## <a name="next-steps"></a>后续步骤

[使用脚本操作自定义 HDInsight 群集](hdinsight-hadoop-customize-cluster-linux.md) 