---
title: 使用 HDInsight .NET SDK 运行 Apache Hive 查询 - Azure
description: 了解如何使用 HDInsight .NET SDK 将 Apache Hadoop 作业提交到 Azure HDInsight Apache Hadoop。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 12/24/2019
ms.openlocfilehash: 8c2dc7cef889a0488352ace443771d118b0d0613
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "122653531"
---
# <a name="run-apache-hive-queries-using-hdinsight-net-sdk"></a>使用 HDInsight .NET SDK 运行 Apache Hive 查询

[!INCLUDE [hive-selector](../includes/hdinsight-selector-use-hive.md)]

了解如何使用 HDInsight .NET SDK 提交 Apache Hive 查询。 编写 C# 程序来提交 Hive 查询以列出 Hive 表，并显示结果。

> [!NOTE]  
> 必须从 Windows 客户端执行本文中的步骤。 有关使用 Linux、OS X 或 Unix 客户端来使用 Hive 的信息，请使用本文顶部显示的选项卡选择器。

## <a name="prerequisites"></a>先决条件

在开始阅读本文前，必须具有以下项：

* HDInsight 中的 Apache Hadoop 群集。 请参阅[在 HDInsight 中使用基于 Linux 的 Hadoop](apache-hadoop-linux-tutorial-get-started.md)。

    > [!IMPORTANT]  
    > 自 2017 年 9 月 15 日起，HDInsight .NET SDK 仅支持从 Azure 存储帐户返回 Hive 查询结果。 如果将此示例用于使用 Azure Data Lake Storage 作为主存储的 HDInsight 群集，则无法使用 .NET SDK 检索搜索结果。

* [Visual Studio](https://visualstudio.microsoft.com/vs/community/) 2013 及更高版本。 至少应安装工作负载 .NET 桌面开发。

## <a name="run-a-hive-query"></a>运行 Hive 查询

HDInsight .NET SDK 提供 .NET 客户端库，可简化从 .NET 中使用 HDInsight 群集的操作。

1. 在 Visual Studio 中创建 C# 控制台应用程序。

1. 在 Nuget 包管理器控制台运行以下命令：

    ```console
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```

1. 编辑以下代码以初始化变量的值：`ExistingClusterName, ExistingClusterUsername, ExistingClusterPassword,DefaultStorageAccountName,DefaultStorageAccountKey,DefaultStorageContainerName`。 然后，在 Visual Studio 中使用修改后的代码作为 Program.cs 的全部内容。

    ```csharp
    using System.Collections.Generic;
    using System.IO;
    using System.Text;
    using System.Threading;
    using Microsoft.Azure.Management.HDInsight.Job;
    using Microsoft.Azure.Management.HDInsight.Job.Models;
    using Hyak.Common;

    namespace SubmitHDInsightJobDotNet
    {
        class Program
        {
            private static HDInsightJobManagementClient _hdiJobManagementClient;

            private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
            private const string ExistingClusterUsername = "<Cluster Username>";
            private const string ExistingClusterPassword = "<Cluster User Password>";

            // Only Azure Storage accounts are supported by the SDK
            private const string DefaultStorageAccountName = "<Default Storage Account Name>";
            private const string DefaultStorageAccountKey = "<Default Storage Account Key>";
            private const string DefaultStorageContainerName = "<Default Blob Container Name>";

            private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";

            static void Main(string[] args)
            {
                System.Console.WriteLine("The application is running ...");

                var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);

                SubmitHiveJob();

                System.Console.WriteLine("Press ENTER to continue ...");
                System.Console.ReadLine();
            }

            private static void SubmitHiveJob()
            {
                Dictionary<string, string> defines = new Dictionary<string, string> { { "hive.execution.engine", "tez" }, { "hive.exec.reducers.max", "1" } };
                List<string> args = new List<string> { { "argA" }, { "argB" } };
                var parameters = new HiveJobSubmissionParameters
                {
                    Query = "SHOW TABLES",
                    Defines = defines,
                    Arguments = args
                };

                System.Console.WriteLine("Submitting the Hive job to the cluster...");
                var jobResponse = _hdiJobManagementClient.JobManagement.SubmitHiveJob(parameters);
                var jobId = jobResponse.JobSubmissionJsonResponse.Id;
                System.Console.WriteLine("Response status code is " + jobResponse.StatusCode);
                System.Console.WriteLine("JobId is " + jobId);

                System.Console.WriteLine("Waiting for the job completion ...");

                // Wait for job completion
                var jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                while (!jobDetail.Status.JobComplete)
                {
                    Thread.Sleep(1000);
                    jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                }

                // Get job output
                var storageAccess = new AzureStorageAccess(DefaultStorageAccountName, DefaultStorageAccountKey,
                    DefaultStorageContainerName);
                var output = (jobDetail.ExitValue == 0)
                    ? _hdiJobManagementClient.JobManagement.GetJobOutput(jobId, storageAccess) // fetch stdout output in case of success
                    : _hdiJobManagementClient.JobManagement.GetJobErrorLogs(jobId, storageAccess); // fetch stderr output in case of failure

                System.Console.WriteLine("Job output is: ");

                using (var reader = new StreamReader(output, Encoding.UTF8))
                {
                    string value = reader.ReadToEnd();
                    System.Console.WriteLine(value);
                }
            }
        }
    }
    ```

1. 按 **F5** 运行该应用程序。

应用程序的输出应类似于：

:::image type="content" source="./media/apache-hadoop-use-hive-dotnet-sdk/hdinsight-hadoop-use-hive-net-sdk-output.png" alt-text="HDInsight Hadoop Hive 作业输出" border="true":::

## <a name="next-steps"></a>后续步骤

本文介绍了如何使用 HDInsight .NET SDK 提交 Apache Hive 查询。 若要了解更多信息，请参阅下列文章：

* [Azure HDInsight 入门](apache-hadoop-linux-tutorial-get-started.md)
* [在 HDInsight 中创建 Apache Hadoop 群集](../hdinsight-hadoop-provision-linux-clusters.md)
* [HDInsight .NET SDK 参考](/dotnet/api/overview/azure/hdinsight)
* [将 Apache Sqoop 与 HDInsight 配合使用](apache-hadoop-use-sqoop-mac-linux.md)
* [创建非交互式身份验证 .NET HDInsight 应用程序](../hdinsight-create-non-interactive-authentication-dotnet-applications.md)
