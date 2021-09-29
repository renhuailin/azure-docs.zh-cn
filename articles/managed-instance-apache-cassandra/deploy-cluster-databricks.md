---
title: 快速入门 - 使用 Azure Databricks 部署托管的 Apache Spark 群集
description: 本快速入门演示如何在 Azure 门户中使用 Azure Databricks 部署托管的 Apache Spark 群集。
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 06/02/2021
ms.openlocfilehash: 7075b7efd871d3f226faa593e0730d22c8913fd1
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124797077"
---
# <a name="quickstart-deploy-a-managed-apache-spark-cluster-preview-with-azure-databricks"></a>快速入门：使用 Azure Databricks 部署托管的 Apache Spark 群集（预览版）

Azure Managed Instance for Apache Cassandra 为托管的开源 Apache Cassandra 数据中心提供自动部署和缩放操作，可加速混合方案的部署并减少日常维护。

> [!IMPORTANT]
> Azure Managed Instance for Apache Cassandra 目前以公共预览版形式提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本快速入门演示了如何使用 Azure 门户在 Azure Managed Instance for Apache Cassandra 群集的 Azure 虚拟网络中创建完全托管的 Apache Spark 群集。 你将在 Azure Databricks 中创建 Spark 群集。 稍后，你可创建笔记本或将其附加到群集、从不同的数据源读取数据，还可分析见解。

此外，你可通过有关[在 Azure 虚拟网络中部署 Azure Databricks（虚拟网络注入）](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)的详细说明了解详细信息。

## <a name="create-an-azure-databricks-cluster"></a>创建 Azure Databricks 群集

按照以下步骤在具有 Azure Managed Instance for Apache Cassandra 的虚拟网络中创建 Azure Databricks 群集：

1. 登录 [Azure 门户](https://portal.azure.com/)。

1. 在左侧导航栏中，找到“资源组”，然后导航到包含部署了托管实例的虚拟网络的资源组。

1. 打开“虚拟网络”资源，并记下地址空间 ：

   :::image type="content" source="./media/deploy-cluster-databricks/virtual-network-address-space.png" alt-text="获取虚拟网络的地址空间。" border="true":::

1. 从资源组中选择“添加”，并在搜索字段中搜索“Azure Databricks” ：

   :::image type="content" source="./media/deploy-cluster-databricks/databricks.png" alt-text="搜索 Azure Databricks。" border="true":::

1. 选择“创建”以创建 Azure Databricks 帐户：

   :::image type="content" source="./media/deploy-cluster-databricks/databricks-create.png" alt-text="创建 Azure Databricks 帐户。" border="true":::

1. 填充以下值：

   * **区域** - 提供 Databricks 工作区的名称。
   * **区域** - 务必选择你的虚拟网络所在的区域。
   * **定价层** - 在“标准”、“高级”和“试用”层之间进行选择。 有关这些层的详细信息，请参阅 [Databricks 价格页](https://azure.microsoft.com/pricing/details/databricks/)。

   :::image type="content" source="./media/deploy-cluster-databricks/select-name.png" alt-text="填写 Databricks 帐户的工作区名称、区域和定价层。" border="true":::

1. 接下来，选择“网络”选项卡并填写以下详细信息：

   * **在你的虚拟网络 (VNet) 中部署 Azure Databricks 工作区** - 选择“是”。
   * **虚拟网络** - 从下拉列表中，选择你的托管实例所在的虚拟网络。
   * **公共子网名称** - 输入公共子网的名称。
   * **公共子网 CIDR 范围** - 输入公共子网的 IP 范围。
   * **专用子网名称** - 输入专用子网的名称。
   * **专用子网 CIDR 范围** - 输入专用子网的 IP 范围。

   为避免范围冲突，请确保选择更高的范围。 如有必要，请使用[可视子网计算器](https://www.fryguy.net/wp-content/tools/subnets.html)来划分范围：

   :::image type="content" source="./media/deploy-cluster-databricks/subnet-calculator.png" alt-text="使用虚拟网络子网计算器。" border="true":::

   以下屏幕截图显示网络窗格中的示例详细信息：

   :::image type="content" source="./media/deploy-cluster-databricks/subnets.png" alt-text="指定公用和专用子网名称。" border="true":::

1. 选择“查看和创建”，然后选择“创建”以部署工作区 。

1. 创建工作区后启动工作区。

1. 随后将会重定向到 Azure Databricks 门户。 在门户中选择“新建群集”。

1. 在“新建群集”窗格中，对于除以下字段以外的所有其他字段，请接受默认值：

   * **群集名称** - 为群集输入一个名称。
   * Databricks Runtime 版本 - 我们建议选择 Databricks Runtime 7.5 或更高版本，以支持 Spark 3.x。 

   :::image type="content" source="../cosmos-db/cassandra/media/migrate-data-databricks/databricks-runtime.png" alt-text="选择 Databricks 运行时版本和 Spark 群集。" border="true":::

1. 展开“高级选项”，然后添加以下配置。 请务必替换节点 IP 和凭据：

   ```java
   spark.cassandra.connection.host <node1 IP>,<node 2 IP>, <node IP>
   spark.cassandra.auth.password cassandra
   spark.cassandra.connection.port 9042
   spark.cassandra.auth.username cassandra
   spark.cassandra.connection.ssl.enabled true
   ```

1. 将 Apache Spark Cassandra 连接器库添加到群集，以便连接到原生终结点和 Azure Cosmos DB Cassandra 终结点。 在群集中，选择“库” > “安装新库” > “Maven”，然后在 Maven 坐标中添加 `com.datastax.spark:spark-cassandra-connector-assembly_2.12:3.0.0`  。

:::image type="content" source="../cosmos-db/cassandra/media/migrate-data-databricks/databricks-search-packages.png" alt-text="屏幕截图显示在 Databricks 中搜索 Maven 包。":::

## <a name="clean-up-resources"></a>清理资源

如果不打算继续使用此托管实例群集，请按照以下步骤删除它：

1. 从 Azure 门户的左侧菜单中选择“资源组”。
1. 从列表中选择为本快速入门创建的资源组。
1. 在资源组的“概述”窗格上，选择“删除资源组” 。
1. 在下一窗口中输入要删除的资源组的名称，然后选择“删除”  。

## <a name="next-steps"></a>后续步骤

在本快速入门中，你学习了如何在 Azure Managed Instance for Apache Cassandra 群集的虚拟网络中创建完全托管的 Apache Spark 群集。 接下来，你可了解如何管理群集和数据中心资源：

> [!div class="nextstepaction"]
> [使用 Azure CLI 管理 Azure Managed Instance for Apache Cassandra 资源](manage-resources-cli.md)

