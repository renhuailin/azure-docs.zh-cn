---
title: 将 Azure 分析资源从 Azure 德国迁移到全球 Azure
description: 本文介绍如何将 Azure 分析资源从 Azure 德国迁移到全球 Azure。
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfmigrate
ms.openlocfilehash: 93630f536127971e922ddceaf5d1e5fa82ea3ad4
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2021
ms.locfileid: "117029089"
---
# <a name="migrate-analytics-resources-to-global-azure"></a>将分析资源迁移到全球 Azure

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]


本文中的信息可帮助你将 Azure 分析资源从 Azure 德国迁移到全球 Azure。
  
## <a name="event-hubs"></a>事件中心

不能直接将 Azure 事件中心资源从 Azure 德国迁移到全球 Azure。 事件中心服务没有数据导出或导入功能。 但是，可以将事件中心资源[作为模板](../azure-resource-manager/templates/export-template-portal.md)导出。 然后，针对全球 Azure 调整导出的模板，然后重新创建资源。

> [!NOTE]
> 导出事件中心模板不会复制数据（例如，消息）。 导出模板只会重新创建事件中心元数据。

> [!IMPORTANT]
> 更改位置、Azure 密钥保管库机密、证书和其他 GUID 以与新区域保持一致。

### <a name="event-hubs-metadata"></a>事件中心元数据

导出事件中心模板时，将重新创建以下元数据元素：

- 命名空间
- 事件中心
- 使用者组
- 授权规则

参考信息：

- 请查看 [Azure 事件中心概述](../event-hubs/event-hubs-about.md)。
- 通过完成[事件中心教程](../event-hubs/index.yml)，学习更多新知识。
- 查看 [Azure 服务总线](./germany-migration-integration.md#service-bus)的迁移步骤。
- 熟悉如何[导出 Azure 资源管理器模板](../azure-resource-manager/templates/export-template-portal.md)或阅读 [Azure 资源管理器](../azure-resource-manager/management/overview.md)概述。

## <a name="hdinsight"></a>HDInsight

要将 Azure HDInsight 群集从 Azure 德国迁移到全球 Azure：

1. 停止 HDInsight 群集。
2. 使用 AzCopy 或类似工具将 Azure 存储帐户中的数据迁移到新区域。
3. 在全球 Azure 中创建新的计算资源，然后将迁移的存储资源附加为主附加存储。

对于更专业的、长时间运行的群集（Kafka、Spark 流、Storm 或 HBase），我们建议你安排工作负载向新区域的过渡。

参考信息：

- 查看 [Azure HDInsight 文档](../hdinsight/index.yml)。
- 通过完成[HDInsight 教程](../hdinsight/index.yml)，学习更多新知识。
- 有获得有关[扩展 HDInsight 群集](../hdinsight/hdinsight-administer-use-powershell.md#scale-clusters)的帮助，请参阅[使用 PowerShell 管理 HDInsight](../hdinsight/hdinsight-administer-use-powershell.md)。
- 了解如何使用 [AzCopy](../storage/common/storage-use-azcopy-v10.md)。

## <a name="stream-analytics"></a>流分析

要将 Azure 流分析服务从 Azure 德国迁移到全球 Azure，请使用 Azure 门户或使用 PowerShell 在全球 Azure 区域中手动重新创建整个设置。 流分析作业的入口和出口源可以位于任何区域。

参考信息：

- 通过完成[流分析教程](../stream-analytics/stream-analytics-real-time-fraud-detection.md)，学习更多新知识。
- 查看[流分析概述](../stream-analytics/stream-analytics-introduction.md)。
- 了解如何[使用 PowerShell 创建流分析作业](../stream-analytics/stream-analytics-quick-create-powershell.md)。

## <a name="sql-database"></a>SQL 数据库

若要迁移较小的 Azure SQL 数据库工作负载，请使用导出功能创建 BACPAC 文件。 BACPAC 文件是一个压缩 (zip) 文件，包含 SQL Server 数据库中的元数据和数据。 创建 BACPAC 文件后，您可以将该文件复制到目标环境（例如，使用 AzCopy）并使用导入功能重建数据库。 请注意以下事项：

- 为使导出的内容在事务上保持一致，请确保符合以下条件之一：
  - 在导出过程中不会发生任何写入活动。
  - 从 SQL 数据库的事务一致性副本导出。
- 若要导出到 Azure Blob 存储，BACPAC 文件大小限制为 200 GB。 对于更大的 BACPAC 文件，请导出到本地存储。
- 如果从 SQL 数据库导出内容的操作所需的时间超过 20 小时，可能会取消该操作。 请查看以下文章，获取有关如何提高性能的提示。

> [!NOTE]
> 由于服务器的 DNS 名称在导出过程中会更改，完成导出操作后，连接字符串也会更改。

参考信息：

- 了解如何[将数据库导出到 BACPAC 文件](../azure-sql/database/database-export.md)。
- 了解如何[将 BACPAC 文件导入数据库](../azure-sql/database/database-import.md)。
- 查看 [Azure SQL 数据库文档](/azure/sql-database/)。

## <a name="analysis-services"></a>Analysis Services

要将 Azure 分析服务模型从 Azure 德国迁移到全球 Azure，请使用[备份和还原操作](../analysis-services/analysis-services-backup.md)。

如果只想迁移模型元数据而不迁移数据，另一种方法是[通过 SQL Server Data Tools 重新部署模型](../analysis-services/analysis-services-deploy.md)。

参考信息：

- 了解[分析服务备份和还原](../analysis-services/analysis-services-backup.md)。
- 查看[分析服务概述](../analysis-services/analysis-services-overview.md)。

## <a name="next-steps"></a>后续步骤

了解用于在以下服务类别中迁移资源的工具、方法和建议：

- [计算](./germany-migration-compute.md)
- [联网](./germany-migration-networking.md)
- [存储](./germany-migration-storage.md)
- [Web](./germany-migration-web.md)
- [数据库](./germany-migration-databases.md)
- [IoT](./germany-migration-iot.md)
- [集成](./germany-migration-integration.md)
- [标识](./germany-migration-identity.md)
- [安全性](./germany-migration-security.md)
- [管理工具](./germany-migration-management-tools.md)
- [介质](./germany-migration-media.md)