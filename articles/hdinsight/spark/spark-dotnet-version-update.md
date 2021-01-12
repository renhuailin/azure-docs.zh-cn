---
title: 将 Apache Spark 的 .NET 更新到 HDI 中的版本1。0
description: 了解如何在 HDI 中将 Apache Spark 版本的 .NET 更新为1.0，以及如何影响你的现有代码和群集。
author: Niharikadutta
ms.author: nidutta
ms.service: hdinsight
ms.topic: how-to
ms.date: 01/05/2021
ms.openlocfilehash: 4b95e4f164eb2c1d0a881cf9ab14696c3cbed4a7
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2021
ms.locfileid: "98122064"
---
# <a name="updating-net-for-apache-spark-to-version-v10--in-hdinsight"></a>在 HDInsight 中将 Apache Spark 的 .NET 更新为版本1.0 版

本文档介绍 .Net 的第一个主要版本的 [Apache Spark](https://github.com/dotnet/spark)，以及它可能如何影响 HDInsight 群集中的当前生产管道。

## <a name="about-net-for-apache-spark-version-100"></a>关于 Apache Spark 1.0.0 版的 .NET

这是 .NET for Apache Spark 的第一个 [主要版本](https://github.com/dotnet/spark/releases/tag/v1.0.0) ，它提供 spark 2.4. x 和 spark 3.0. x 以及其他功能的数据帧 API 完整性。 有关所有功能、改进和 bug 修复的完整列表，请参阅官方 [1.0.0 发行说明](https://github.com/dotnet/spark/blob/master/docs/release-notes/1.0.0/release-1.0.0.md)。
需要注意的另一个重要事项是，此版本与和的以前版本 **不** 兼容 `Microsoft.Spark` `Microsoft.Spark.Worker` 。 如果你计划升级 .NET 以便 Apache Spark 应用程序与1.0.0 版兼容，请查看 [迁移指南](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10) 。

## <a name="using-net-for-apache-spark-v10-in-hdinsight"></a>使用适用于 HDInsight 中 Apache Spark v1.0 的 .NET

尽管当前 HDI 群集不会受到影响 (也就是说，它们仍将具有与) 之前相同的版本，而新创建的 HDI 群集将为 Apache Spark 提供此最新的1.0.0 版 .NET。 这意味着：

- **你有较旧的 HDI 群集**：如果想要将 Spark .net 应用程序升级到 (建议) ，则必须更新 `Microsoft.Spark.Worker` HDI 群集上的版本。 有关详细信息，请参阅 [HDI 群集上 Apache Spark 的更改 .net 版本部分](#changing-net-for-apache-spark-version-on-hdinsight)。
如果你不想更新应用程序中 Apache Spark 的当前版本的 .NET，则无需执行其他步骤。  

- **你有一个新的 HDI 群集**：如果想要将 Spark .net 应用程序升级到)  (建议的应用程序，则无需执行任何步骤来更改 HDI 上的辅助角色，但必须参阅 [迁移指南](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10) 以了解更新代码和管道所需的步骤。
如果你不希望在应用程序中更改 Apache Spark 的 .NET 当前版本，则必须将新群集上的 HDI 群集上的版本从1.0 版更改 () 到使用的任何版本。 有关详细信息，请参阅 [HDI 群集上 Apache Spark 的更改 .net 版本部分](spark-dotnet-version-update.md#changing-net-for-apache-spark-version-on-hdinsight)。  

## <a name="changing-net-for-apache-spark-version-on-hdinsight"></a>更改 HDInsight 上 Apache Spark 版本的 .NET

### <a name="deploy-microsoftsparkworker"></a>部署 Microsoft.Spark.Worker

`Microsoft.Spark.Worker` 是位于 Spark 群集的单个辅助角色节点上的后端组件。 若要执行 c # UDF (用户定义函数) ，Spark 需要了解如何启动 .NET CLR 来执行此 UDF。 `Microsoft.Spark.Worker` 向 Spark 提供启用此功能的类的集合。 根据要在 HDI 群集上部署的 Apache Spark 的 .NET 版本，选择辅助角色版本。

1. 下载特定版本的 "Microsoft Spark" Linux 版本。 例如，如果需要 `.NET for Apache Spark v1.0.0` ，可以下载 [netcoreapp 3.1. linux-x64-1.0.0. gz.](https://github.com/dotnet/spark/releases/tag/v1.0.0)。  

2. 下载 [install-worker.sh](https://github.com/dotnet/spark/blob/master/deployment/install-worker.sh) 脚本，将在步骤1中下载的辅助角色二进制文件安装到 HDI 群集的所有辅助角色节点。  

3. 将上述文件上传到群集可以访问的 Azure 存储帐户。 有关更多详细信息，请参阅 [.net for APACHE SPARK HDI deployment](https://docs.microsoft.com/dotnet/spark/tutorials/hdinsight-deployment#upload-files-to-azure) 。

4. `install-worker.sh`使用脚本操作在群集的所有辅助角色节点上运行该脚本。 有关详细信息，请参阅 [.net for APACHE SPARK HDI deployment](https://docs.microsoft.com/dotnet/spark/tutorials/hdinsight-deployment#run-the-hdinsight-script-action) 。

### <a name="update-your-application-to-use-specific-version"></a>更新应用程序以使用特定版本

可以通过在项目中选择所需版本的 [Microsoft Spark NuGet 包](https://www.nuget.org/packages/Microsoft.Spark/) ，将 Apache Spark 应用程序的 .net 更新为使用特定版本。 如果选择将应用程序更新为1.0.0 版，请务必查看上面提到的特定版本和 [迁移指南](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10) 的发行说明。

## <a name="faqs"></a>常见问题解答

### <a name="will-my-existing-hdi-cluster-with-version--100-start-failing-with-the-new-release"></a>现有的 HDI 群集版本 < 1.0.0 是否开始出现新版本？

现有的 HDI 群集将继续 Apache Spark 适用于 .NET 的早期版本， (具有早期版本的 Spark .NET) 的现有应用程序将不受影响。

## <a name="next-steps"></a>后续步骤

[在 HDInsight 上部署适用于 Apache Spark 应用程序的 .NET](https://docs.microsoft.com/dotnet/spark/tutorials/hdinsight-deployment)
