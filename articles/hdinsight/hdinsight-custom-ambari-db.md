---
title: Azure HDInsight 上的自定义 Apache Ambari 数据库
description: 了解如何创建包含自己的自定义 Apache Ambari 数据库的 HDInsight 群集。
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 01/12/2021
ms.openlocfilehash: d2ab2609338daff846797834be7694a8b1f220e6
ms.sourcegitcommit: 9caa850a2b26773e238f8ba6f4ca151c47260915
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2021
ms.locfileid: "113600932"
---
# <a name="set-up-hdinsight-clusters-with-a-custom-ambari-db"></a>设置包含自定义 Ambari DB 的 HDInsight 群集

Apache Ambari 简化了 Apache Hadoop 群集的管理和监视。 Ambari 提供易于使用的 Web UI 和 REST API。 Ambari 包括在 HDInsight 群集中，用于监视群集和进行配置更改。

在一般的群集创建过程中，如其他文章（例如[在 HDInsight 中设置群集](hdinsight-hadoop-provision-linux-clusters.md)）中所述，Ambari 将部署在由 HDInsight 管理的、不可供用户访问的 [S0 Azure SQL 数据库](../azure-sql/database/resource-limits-dtu-single-databases.md#standard-service-tier)中。

使用自定义 Ambari DB 功能，可以在由你管理的外部数据库中部署新群集和设置 Ambari。 部署是使用 Azure 资源管理器模板完成的。 此功能提供以下优势：

- 自定义 - 可以选择数据库的大小和处理容量。 如果大型群集需要处理密集型工作负荷，规格较低的 Ambari 数据库可能会成为管理操作的瓶颈。
- 灵活性 - 可根据要求按需缩放数据库。
- 控制 - 可以根据组织的要求管理数据库的备份和安全性。

本文的余下内容将讨论以下几点：

- 使用自定义 Ambari DB 功能的要求
- 使用自己的外部数据库为 Apache Ambari 预配 HDInsight 群集所要执行的步骤

## <a name="custom-ambari-db-requirements"></a>自定义 Ambari DB 的要求

可以使用所有群集类型和版本来部署自定义的 Ambari DB。 多个群集不能使用同一个 Ambari DB。

自定义 Ambari DB 具有以下附加要求：

- 数据库名称不能包含连字符或空格
- 必须有现有的 Azure SQL DB 服务器和数据库。
- 为 Ambari 设置提供的数据库必须是空的。 没有任何表采用默认的 dbo 架构。
- 用于连接到数据库的用户应该对该数据库拥有 SELECT、CREATE TABLE 和 INSERT 权限。
- 在托管 Ambari 的服务器上启用“[允许访问的 Azure 服务](../azure-sql/database/vnet-service-endpoint-rule-overview.md#azure-portal-steps)”选项。
- 需要在防火墙规则中允许来自 HDInsight 服务的管理 IP 地址。 有关必须添加到服务器级别防火墙规则的 IP 地址列表，请参阅 [HDInsight 管理 IP 地址](hdinsight-management-ip-addresses.md)。

在外部数据库中托管 Apache Ambari DB 时，请记住以下几点：

- 你需要负责支付用于保存 Ambari 的 Azure SQL DB 的额外费用。
- 定期备份自定义 Ambari DB。 Azure SQL 数据库会自动生成备份，但备份保留时间范围有所不同。 有关详细信息，请参阅[了解 SQL 数据库自动备份](../azure-sql/database/automated-backups-overview.md)。

## <a name="deploy-clusters-with-a-custom-ambari-db"></a>部署包含自定义 Ambari DB 的群集

若要创建使用你自己的外部 Ambari 数据库的 HDInsight 群集，请使用[自定义 Ambari DB 快速入门模板](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.hdinsight/hdinsight-custom-ambari-db)。

编辑 `azuredeploy.parameters.json` 中的参数，指定有关新群集以及用于保存 Ambari 的数据库的信息。

可以使用 Azure CLI 开始部署。 请将 `<RESOURCEGROUPNAME>` 替换为要在其中部署群集的资源组。

```azurecli
az deployment group create --name HDInsightAmbariDBDeployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file azuredeploy.json \
    --parameters azuredeploy.parameters.json
```


> [!WARNING]
> 请对 HDInsight 群集使用以下建议的 SQL DB 和头节点 VM。 请勿将默认的 Ambari DB (S0) 用于任何生产环境。 
>


## <a name="database-and-headnode-sizing"></a>数据库和头节点调整大小

下表提供了有关基于 HDInsight 群集大小选择 Azure SQL DB 层的指南。

| 工作器节点数 | 必需的 DB 层 | 必需的头节点 VM |
|---|---|---|
| <=4 | S0 | 4 核/28 GB RAM 或更高配置 |
| >4 && <=8 | S1 | 4 核/28 GB RAM 或更高配置 |
| >8 && <=16 | S2 | 4 核/28 GB RAM 或更高配置 |
| >16 && <=32 | S3 | 8 核/56 GB RAM 或更高配置 |
| >32 && <=64 | S4 | 8 核/56 GB RAM 或更高配置 |
| >64 && <=128 | P2 | 16 核/112 GB RAM 或更高配置 |
| >128 | 联系支持人员 | 联系支持人员 |

## <a name="next-steps"></a>后续步骤

- [使用外部元数据存储 - Azure HDInsight](hdinsight-use-external-metadata-stores.md)
