---
title: 将 .NET for Apache Spark 更新到 HDI 中的版本 v1.0
description: 了解如何将 .NET for Apache Spark 版本更新到 HDI 中的 1.0，以及如何影响现有代码和群集。
author: Niharikadutta
ms.author: nidutta
ms.service: hdinsight
ms.topic: how-to
ms.date: 01/05/2021
ms.openlocfilehash: a1602f29a6d0066ec3c99e990532411621652c47
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98788123"
---
# <a name="updating-net-for-apache-spark-to-version-v10--in-hdinsight"></a>将 .NET for Apache Spark 更新到 HDInsight 中的版本 v1.0

本文档介绍 [.NET for Apache Spark](https://github.com/dotnet/spark) 的第一个主要版本，以及它可能如何影响 HDInsight 群集中的当前生产管道。

## <a name="about-net-for-apache-spark-version-100"></a>关于 .NET for Apache Spark 版本 1.0.0

这是 .NET for Apache Spark 的第一个[主要正式版本](https://github.com/dotnet/spark/releases/tag/v1.0.0)，为 Spark 2.4.x 和 Spark 3.0.x 提供数据帧 API 完整性以及其他功能。 有关所有功能、改进和 bug 修复的完整列表，请参阅正式 [v1.0.0 发行说明](https://github.com/dotnet/spark/blob/master/docs/release-notes/1.0.0/release-1.0.0.md)。
需要注意的另一个重要事项是，此版本与以前版本的 `Microsoft.Spark` 和 `Microsoft.Spark.Worker` 不兼容。 如果你计划升级 .NET for Apache Spark 应用程序以便与 v1.0.0 兼容，请查看[迁移指南](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10)。

## <a name="using-net-for-apache-spark-v10-in-hdinsight"></a>使用 HDInsight 中的 .NET for Apache Spark v1.0

尽管当前 HDI 群集不会受到影响（也就是说，它们仍将具有与之前相同的版本），但新创建的 HDI 群集将提供最新 v1.0.0 版本的 .NET for Apache Spark。 这意味着：

- 你有较旧的 HDI 群集：如果想要将 Spark .NET 应用程序升级到 v1.0.0（建议），则必须更新 HDI 群集上的 `Microsoft.Spark.Worker` 版本。 有关详细信息，请参阅[更改 HDI 群集上的 .NET for Apache Spark 版本部分](#changing-net-for-apache-spark-version-on-hdinsight)。
如果不想更新应用程序中 .NET for Apache Spark 的当前版本，则无需执行进一步的步骤。  

- 你有较新的 HDI 群集：如果想要将 Spark .NET 应用程序升级到 v1.0.0（建议），则无需执行任何步骤即可更改 HDI 上的辅助角色，但必须参阅[迁移指南](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10)来了解更新代码和管道所需的步骤。
如果不想更改应用程序中 .NET for Apache Spark 的当前版本，则必须将 HDI 群集上的版本从 v1.0（新群集上的默认版本）更改为所使用的任何版本。 有关详细信息，请参阅[更改 HDI 群集上的 .NET for Apache Spark 版本部分](spark-dotnet-version-update.md#changing-net-for-apache-spark-version-on-hdinsight)。  

## <a name="changing-net-for-apache-spark-version-on-hdinsight"></a>更改 HDInsight 上的 .NET for Apache Spark 版本

### <a name="deploy-microsoftsparkworker"></a>部署 Microsoft.Spark.Worker

Microsoft.Spark.Worker 是后端组件，位于 Spark 群集的单个工作器节点上`Microsoft.Spark.Worker`。 想要执行 C# UDF（用户定义的函数）时，Spark 需要了解如何启动 .NET CLR 以执行此 UDF。 Microsoft.Spark.Worker 向 Spark 提供启用此功能的类集合`Microsoft.Spark.Worker`。 根据要在 HDI 群集上部署的 .NET for Apache Spark 版本，选择辅助角色版本。

1. 下载特定版本的 Microsoft.Spark.Worker Linux 版本。 例如，如果需要 `.NET for Apache Spark v1.0.0`，则需要下载 [Microsoft.Spark.Worker.netcoreapp3.1.linux-x64-1.0.0.tar.gz](https://github.com/dotnet/spark/releases/tag/v1.0.0)。  

2. 下载 [install-worker.sh](https://github.com/dotnet/spark/blob/master/deployment/install-worker.sh) 脚本以将步骤 1 中下载的辅助角色二进制文件安装到 HDI 群集的所有辅助角色节点。  

3. 将上述文件上传到群集有权访问的 Azure 存储帐户。 有关更多详细信息，请参阅 [.NET for Apache Spark HDI 部署一文](/dotnet/spark/tutorials/hdinsight-deployment#upload-files-to-azure)。

4. 使用脚本操作在群集的所有辅助角色节点上运行 `install-worker.sh` 脚本。 有关详细信息，请参阅 [.NET for Apache Spark HDI 部署一文](/dotnet/spark/tutorials/hdinsight-deployment#run-the-hdinsight-script-action)。

### <a name="update-your-application-to-use-specific-version"></a>更新应用程序以使用特定版本

可以通过在项目中选择所需版本的 [Microsoft.Spark NuGet 包](https://www.nuget.org/packages/Microsoft.Spark/)，将 .NET for Apache Spark 应用程序更新为使用特定版本。 如果选择将应用程序更新到 v1.0.0，请务必查看特定版本的发行说明和如上所述的[迁移指南](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10)。

## <a name="faqs"></a>常见问题

### <a name="will-my-existing-hdi-cluster-with-version--100-start-failing-with-the-new-release"></a>版本低于 1.0.0 的现有 HDI 群集是否开始失败并出现新版本？

现有 HDI 群集将继续具有相同的 .NET for Apache Spark 的早期版本，现有应用程序（具有 Spark .NET 的早期版本）不受影响。

## <a name="next-steps"></a>后续步骤

[部署 HDInsight 上的 .NET for Apache Spark 应用程序](/dotnet/spark/tutorials/hdinsight-deployment)