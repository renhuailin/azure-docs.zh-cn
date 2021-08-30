---
title: 用于数据科学项目的平台和工具 - Team Data Science Process
description: 列举并讨论基于 Team Data Science Process 实现标准化的企业可用的数据和分析资源。
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 06/18/2021
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 326d435544057af946a186ef246c74847fc61a11
ms.sourcegitcommit: 351279883100285f935d3ca9562e9a99d3744cbd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2021
ms.locfileid: "112378243"
---
# <a name="platforms-and-tools-for-data-science-projects"></a>用于数据科学项目的平台和工具

Microsoft 为云或本地平台提供了整套分析资源。 部署这些服务和资源可让数据科学项目的执行变得有效且可缩放。 [Team Data Science Process](overview.md) (TDSP) 为团队以可跟踪、版本受控和协作的方式实施数据科学项目提供指导。  有关致力于标准化此过程的数据科学团队处理的人员角色及其相关任务的概述，请参阅 [Team Data Science Process 角色和任务](roles-tasks.md)。

使用 TDSP 的数据科学团队可用的分析资源包括：

- Azure 机器学习 
- Data Science Virtual Machine（Windows 和 Linux Ubuntu）
- HDInsight Spark 群集
- Azure Synapse Analytics
- Azure Data Lake
- HDInsight Hive 群集
- Azure 文件存储
- SQL Server 2019 R 和 Python 服务
- Azure Databricks

本文档简要介绍上述资源，并提供 TDSP 团队发布的教程和演练的链接。 可以借助这些参考材料了解如何逐步使用这些资源，并开始使用它们来生成智能应用程序。 这些资源的产品页上提供了其详细信息。 


## <a name="azure-machine-learning"></a>Azure 机器学习 

Azure 机器学习是我们推荐的主要数据科学开发平台。  此 PaaS（平台即服务）提供独立操作，也可与本网页中提到的任何其他平台和工具集成。 [Azure 机器学习](../overview-what-is-azure-ml.md) (AzureML) 是一个端到端平台，其中包含：

+ 完全托管计算
  + 计算实例
  + 用于分布式 ML 任务的计算群集
  + 用于实时评分的推理群集
+ 数据存储（例如 Blob、ADLS Gen2、SQL DB）
+ 试验跟踪
+ 模型管理
+ 笔记本
+ 环境（管理 conda 和 R 依赖项）
+ 标记
+ 管道（自动化端到端数据科学工作流）


## <a name="data-science-virtual-machine-dsvm"></a>数据科学虚拟机 (DSVM)

Microsoft 在 Windows 和 Linux 上提供的数据科学虚拟机包含用于数据科学建模与开发活动的热门工具。 

Data Science Virtual Machine 是在云中浏览数据和进行机器学习的一种简单方法。 Data Science Virtual Machine 已预先配置了完整的操作系统、安全修补程序、驱动程序和常用的数据科学和开发软件。 可以选择硬件环境，选择范围包括低成本的以 CPU 为中心的计算机，以及具有多个 GPU、NVMe 存储和大量内存的极强大计算机。 对于具有 GPU 的计算机，我们安装了所有驱动程序，对所有机器学习框架进行了版本匹配以确保 GPU 兼容性，并且在支持 GPU 的所有应用程序软件中启用了加速。

Data Science Virtual Machine 预装有最实用的数据科学工具。  有关工具和版本的最新列表，请参阅 [Data Science Virtual Machine 中包含的工具](/azure/machine-learning/data-science-virtual-machine/tools-included)。

DSVM 目前可在 Windows 和 Linux Ubuntu 操作系统中使用 。 根据计划在其上执行的数据科学项目的需求，选择 DSVM 的大小（CPU 核心数和内存量）。 

有关 Windows 版 DSVM 的详细信息，请参阅 Azure 市场中的 [Microsoft Data Science Virtual Machine](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019)。 有关 Linux 版 DSVM，请参阅 [Linux 数据科学虚拟机](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804)。

若要了解如何在 DSVM 上有效执行某些常见的数据科学任务，请参阅 [Data Science Virtual Machine 的十大功能](../data-science-virtual-machine/vm-do-ten-things.md)


## <a name="azure-hdinsight-spark-clusters"></a>Azure HDInsight Spark 群集

Apache Spark 是一种开源并行处理框架，支持使用内存中处理来提升大数据分析应用程序的性能。 Spark 处理引擎是专为速度、易用性和复杂分析打造的产品。 Spark 的内存中计算功能使其成为机器学习和图形计算中的迭代算法的最佳选择。 Spark 也能与 Azure Blob 存储 (WASB) 兼容，因此可以轻松使用 Spark 处理存储在 Azure 中的现有数据。

在 HDInsight 中创建 Spark 群集时，即会创建已安装并配置了 Spark 的 Azure 计算资源。 在 HDInsight 中创建 Spark 群集需要约 10 分钟。 将要处理的数据存储在 Azure Blob 存储中。 有关在群集中使用 Azure Blob 存储的信息，请参阅[将 HDFS 兼容的 Azure Blob 存储与 HDInsight 中的 Hadoop 配合使用](../../hdinsight/hdinsight-hadoop-use-blob-storage.md)。

Microsoft 的 TDSP 团队发布了两篇端到端演练，介绍如何使用 Azure HDInsight Spark 群集生成数据科学解决方案，其中一个解决方案使用 Python，另一个使用 Scala。 有关 Azure HDInsight“Spark 群集”的详细信息，请参阅[概述：HDInsight Linux 上的 Apache Spark](../../hdinsight/spark/apache-spark-overview.md)。 若要了解如何在 Azure HDInsight Spark 群集上使用 **Python** 生成数据科学解决方案，请参阅 [有关在 Azure HDInsight 上使用 Spark 展开数据科学的概述](spark-overview.md)。 若要了解如何在 Azure HDInsight Spark 群集上使用 **Scala** 生成数据科学解决方案，请参阅 [在 Azure 上使用 Scala 和 Spark 展开数据科学](scala-walkthrough.md)。 


##  <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

使用 Azure Synapse Analytics 可以在片刻之间轻松缩放计算资源，无需过度预配或过度付费。 此外，SQL 数据仓库提供暂停使用计算资源的独特选项，为我们赋予更大的自由以更好地管理云成本。 部署可缩放计算资源的能力使我们能够将所有数据放入 Azure Synapse Analytics。 存储成本低廉，只需针对想要分析的数据集部分运行计算资源。 

有关 Azure Synapse Analytics 的详细信息，请参阅 [Azure Synapse Analytics](https://azure.microsoft.com/services/sql-data-warehouse) 网站。 若要了解如何使用 Azure Synapse Analytics 生成端到端高级分析解决方案，请参阅 [Team Data Science Process 实务：使用 Azure Synapse Analytics](sqldw-walkthrough.md)。


## <a name="azure-data-lake"></a>Azure Data Lake

Azure Data Lake 是在施加任何正式要求或架构之前，在单个位置收集的每种数据的企业级存储库。 这种灵活性允许将每种数据保留在 Data Lake 中，而不管数据的大小或结构或引入速度如何。 然后，组织可以使用 Hadoop 或高级分析功能在这些 Data Lake 中查找模式。 在策划数据并其转移到数据仓库之前，Data Lake 还可以充当较低成本的数据准备工作的存储库。

有关 Azure Data Lake 的详细信息，请参阅 [Azure Data Lake 简介](https://azure.microsoft.com/blog/introducing-azure-data-lake/)。 要了解如何使用 Azure Data Lake 生成可缩放的端到端数据科学解决方案，请参阅 [Azure Data Lake 中可缩放的数据科学：端到端演练](data-lake-walkthrough.md)


## <a name="azure-hdinsight-hive-hadoop-clusters"></a>Azure HDInsight Hive (Hadoop) 群集

Apache Hive 是适用于 Hadoop 的数据仓库系统，可让你使用 HiveQL（类似于 SQL 的查询语言）来进行数据汇总、查询和数据分析。 使用 Hive 能够以交互方式浏览数据，或者创建可重用的批处理作业。

Hive 可以实现将结构投影到很大程度上未结构化的数据上。 定义结构后，可以使用 Hive 在 Hadoop 群集中查询这些数据，而无需使用甚至无需了解 Java 或 MapReduce。 借助 HiveQL（Hive 查询语言）可以使用类似于 T-SQL 的语句编写查询。

对于数据科学家而言，Hive 可以在 Hive 查询中运行 Python 用户定义的函数 (UDF) 来处理记录。 这种能力大大扩展了数据分析中的 Hive 查询功能。 具体而言，Hive 可让数据科学家使用他们最熟悉的语言（包括类似于 SQL 的 HiveQL 和 Python）开展可缩放的特征工程。 

有关 Azure HDInsight Hive 群集的详细信息，请参阅[在 HDInsight 中将 Hive 和 HiveQL 与 Hadoop 配合使用](../../hdinsight/hadoop/hdinsight-use-hive.md)。 若要了解如何使用 Azure HDInsight Hive 群集生成可缩放的端到端数据科学解决方案，请参阅[运行中的 Team Data Science Process：使用 HDInsight Hadoop 群集](hive-walkthrough.md)。


## <a name="azure-file-storage"></a>Azure 文件存储 

Azure 文件存储是一种使用标准服务器消息块 (SMB) 协议在云中提供文件共享的服务。 支持 SMB 2.1 和 SMB 3.0。 通过 Azure 文件存储，可以将依赖文件共享的旧版应用程序快速迁移到 Azure 且无成本高昂的重写。 在 Azure 虚拟机或云服务中或者从本地客户端运行的应用程序可以在云中装载文件共享，就像桌面应用程序装载典型的 SMB 共享一样。 之后，任意数量的应用程序组件可以装载并同时访问文件存储共享。

能够创建一个 Azure 文件存储作为与项目团队成员共享项目数据的位置，对于数据科学项目特别有用。 然后，每个成员可以访问 Azure 文件存储中的相同数据副本。 他们还可使用此文件存储来共享执行项目期间生成的特征集。 如果项目是客户参与项目，则客户可以在其自己的 Azure 订阅下创建一个 Azure 文件存储，用来与你共享项目数据和特征。 这样，客户便可以完全控制项目数据资产。 有关 Azure 文件存储的详细信息，请参阅 [在 Windows 上开始使用 Azure 文件存储](../../storage/files/storage-dotnet-how-to-use-files.md)和[如何通过 Linux 使用 Azure 文件存储](../../storage/files/storage-how-to-use-files-linux.md)。


## <a name="sql-server-2019-r-and-python-services"></a>SQL Server 2019 R 和 Python 服务

R 服务（数据库内部）提供一个平台，用于开发和部署可以发现新见解的智能应用程序。 可以使用丰富强大的 R 语言（包括 R 社区提供的许多包）来创建模型，基于 SQL Server 数据生成预测。 由于 R 服务（数据库内部）可将 R 语言与 SQL Server 集成，因此可以保持与数据接近的分析结果，同时消除与数据移动相关的成本和安全风险。

R 服务（数据库内部）凭借一套综合性的 SQL Server 工具和技术来支持开放源代码 R 语言。 它们提供优异的性能、安全性、可靠性和可管理性。 可以使用便捷、熟悉的工具部署 R 解决方案。 生产应用程序可以使用 Transact-SQL 调用 R 运行时以及检索预测数据和视觉对象。 还可以使用 ScaleR 库来改善 R 解决方案的缩放性和性能。 有关详细信息，请参阅 [SQL Server R Services](/sql/advanced-analytics/r/sql-server-r-services)。

Microsoft 的 TDSP 团队发布了两篇端到端演练，介绍如何在 SQL Server 2016 R 服务中生成数据科学解决方案：一篇面向 R 程序员，另一篇面向 SQL 开发人员。 **R 程序员** 可参阅 [数据科学端到端演练](/sql/advanced-analytics/tutorials/walkthrough-data-science-end-to-end-walkthrough)。 **SQL 开发人员** 可参阅 [面向 SQL 开发人员的数据库内部高级分析（教程）](/sql/advanced-analytics/tutorials/sqldev-in-database-r-for-sql-developers)。


## <a name="appendix-tools-to-set-up-data-science-projects"></a><a name="appendix"></a>附录：用于设置数据科学项目的工具

### <a name="install-git-credential-manager-on-windows"></a>在 Windows 上安装 Git 凭据管理器

如果在 **Windows** 上遵循 TDSP，需要安装 **Git 凭据管理器 (GCM)** 来与 Git 存储库通信。 若要安装 GCM，首先需要安装 Chocolatey。 若要安装 Chocolatey 和 GCM，请在 Windows PowerShell 中以管理员身份运行以下命令：  

```powershell
iwr https://chocolatey.org/install.ps1 -UseBasicParsing | iex
choco install git-credential-manager-for-windows -y
```  

### <a name="install-git-on-linux-centos-machines"></a>在 Linux (CentOS) 计算机上安装 Git

运行以下 bash 命令，在 Linux (CentOS) 计算机上安装 Git：

```powershell
sudo yum install git
```

### <a name="generate-public-ssh-key-on-linux-centos-machines"></a>在 Linux (CentOS) 计算机上生成公共 SSH 密钥

如果使用 Linux (CentOS) 计算机运行 git 命令，需要将计算机的公共 SSH 密钥添加到 Azure DevOps Services，使 Azure DevOps Services 能够识别此计算机。 首先，需要生成公共 SSH 密钥，并在 Azure DevOps Services 安全设置页中将该密钥添加到 SSH 公钥。 

1. 若要生成 SSH 密钥，请运行以下两条命令： 

   ```
   ssh-keygen
   cat .ssh/id_rsa.pub
   ```
   
   ![生成 SSH 密钥的命令](./media/platforms-and-tools/resources-1-generate_ssh.png)

1. 复制整个 SSH 密钥，包括 *ssh-rsa*。 
1. 登录到 Azure DevOps Services。 
1. 单击页面右上角的“<你的姓名\>”，再单击“安全性” 。 
    
   ![单击你的姓名，再单击“安全性”](./media/platforms-and-tools/resources-2-user-setting.png)

1. 依次单击“SSH 公钥”、“+添加”。  

   ![单击 SSH 公钥，再单击“+ 添加”](./media/platforms-and-tools/resources-3-add-ssh.png)

1. 将复制的 SSH 密钥粘贴到文本框中并保存。


## <a name="next-steps"></a>后续步骤

还提供了完整的、端到端的演练，演示 **特定方案** 处理过程中的所有步骤。 在[示例演练](walkthroughs.md)主题中，列出了相关步骤并链接了缩略图说明。 这些演练演示如何将云、本地工具和服务合并到工作流或管道中，以创建智能应用程序。 

有关如何使用 Azure 机器学习工作室（经典）执行 Team Data Science Process 中的步骤的示例，请参见[使用 Azure ML](./index.yml) 学习路径。
