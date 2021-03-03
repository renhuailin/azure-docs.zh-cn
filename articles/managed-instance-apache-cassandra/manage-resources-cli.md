---
title: 使用 Azure CLI 管理 Apache Cassandra 资源的 Azure 托管实例
description: 了解使用 Azure CLI 自动管理 Azure 托管实例适用于 Apache Cassandra 的常见命令。
author: TheovanKraay
ms.service: managed-instance-apache-cassandra
ms.topic: how-to
ms.date: 03/02/2021
ms.author: thvankra
ms.openlocfilehash: 3cd5fdbf6cdc504a1290c8fbd80cf89cf85ce714
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744476"
---
# <a name="manage-azure-managed-instance-for-apache-cassandra-resources-using-azure-cli-preview"></a>使用 Azure CLI (预览版管理 Apache Cassandra 资源的 Azure 托管实例) 

本文介绍了使用 Azure CLI 自动管理 Azure 托管实例适用于 Apache Cassandra 群集的常见命令。

> [!IMPORTANT]
> 适用于 Apache Cassandra 的 Azure 托管实例当前提供公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* 本文要求 Azure CLI 版本2.12.1 或更高版本。 如果使用 Azure Cloud Shell，则已安装最新版本。

> [!IMPORTANT]
> 不能重命名 Apache Cassandra 资源的 Azure 托管实例，因为这违反了 Azure 资源管理器使用资源 Uri 的方式。

## <a name="azure-managed-instance-for-apache-cassandra-clusters"></a>适用于 Apache Cassandra 群集的 Azure 托管实例

以下部分演示了如何管理 Apache Cassandra 群集的 Azure 托管实例，包括：

* [创建托管实例群集](#create-cluster)
* [删除托管实例群集](#delete-cluster)
* [获取群集详细信息](#get-cluster-details)
* [获取群集节点状态](#get-cluster-status)
* [按资源组列出群集](#list-clusters-resource-group)
* [按订阅 ID 列出群集](#list-clusters-subscription)

### <a name="create-a-managed-instance-cluster"></a><a id="create-cluster"></a>创建托管实例群集

为 Apache Cassandra 群集创建 Azure 托管实例：

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
location='West US'
delegatedManagementSubnetId='/subscriptions/<subscription id>/resourceGroups/customer-vnet-rg/providers/Microsoft.Network/virtualNetworks/customer-vnet/subnets/management'
initialCassandraAdminPassword='myPassword'

# You can override the cluster name if the original name is not legal for an Azure resource.
# overrideClusterName='ClusterNameIllegalForAzureResource'
# the default Cassandra version is v3.11

az managed-cassandra cluster create \
    --cluster-name $clusterName \
    --resource-group $resourceGroupName \
    --location $location \
    --delegated-management-subnet-id $delegatedManagementSubnetId \
    --initial-cassandra-admin-password $initialCassandraAdminPassword \
```

### <a name="delete-a-managed-instance-cluster"></a><a id="delete-cluster"></a>删除托管实例群集

删除群集：

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'

az managed-cassandra cluster delete \
    --cluster-name $clusterName \
    --resource-group $resourceGroupName
```

### <a name="get-the-cluster-details"></a><a id="get-cluster-details"></a>获取群集详细信息

获取群集详细信息：

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'

az managed-cassandra cluster show \
    --cluster-name $clusterName \
    --resource-group $resourceGroupName
```

### <a name="get-the-cluster-node-status"></a><a id="get-cluster-status"></a>获取群集节点状态

获取群集详细信息：

```azurecli-interactive
clusterName='cassandra-hybrid-cluster'
resourceGroupName='MyResourceGroup'

az managed-cassandra cluster node-status \
    --cluster-name $clusterName \
    --resource-group $resourceGroupName
```

### <a name="list-the-clusters-by-resource-group"></a><a id="list-clusters-resource-group"></a>按资源组列出群集

按资源组列出群集：

```azurecli-interactive
subscriptionId='MySubscriptionId'
resourceGroupName='MyResourceGroup'

az managed-cassandra cluster list\
    --resource-group $resourceGroupName
```

### <a name="list-clusters-by-subscription-id"></a><a id="list-clusters-subscription"></a>按订阅 ID 列出群集

按订阅 ID 列出群集：

```azurecli-interactive
# set your subscription id
az account set -s <subscription id>

az managed-cassandra cluster list
```

## <a name="the-managed-instance-datacenters"></a><a id="managed-instance-datacenter"></a>托管实例数据中心

以下部分演示了如何为 Apache Cassandra 数据中心管理 Azure 托管实例，包括：

* [创建数据中心](#create-datacenter)
* [删除数据中心](#delete-datacenter)
* [获取数据中心详细信息](#get-datacenter-details)
* [更新或缩放数据中心](#update-datacenter)
* [获取群集中的数据中心](#get-datacenters-cluster)

### <a name="create-a-datacenter"></a><a id="create-datacenter"></a>创建数据中心

创建数据中心：

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
dataCenterName='dc1'
dataCenterLocation='eastus2'
delegatedSubnetId='/subscriptions/<Subscription_ID>/resourceGroups/customer-vnet-rg/providers/Microsoft.Network/virtualNetworks/customer-vnet/subnets/dc1-subnet'

az managed-cassandra datacenter create \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --data-center-name $dataCenterName \
    --data-center-location $dataCenterLocation \
    --delegated-subnet-id $delegatedSubnetId \
    --node-count 3 
```

### <a name="delete-a-datacenter"></a><a id="delete-datacenter"></a>删除数据中心

删除数据中心：

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
dataCenterName='dc1'

az managed-cassandra datacenter delete \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --data-center-name $dataCenterName 
```

### <a name="get-datacenter-details"></a><a id="get-datacenter-details"></a>获取数据中心详细信息

获取数据中心详细信息：

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
dataCenterName='dc1'

az managed-cassandra datacenter show \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --data-center-name $dataCenterName 
```

### <a name="update-or-scale-a-datacenter"></a><a id="update-datacenter"></a>更新或缩放数据中心

更新或缩放数据中心 (缩放更改 nodeCount 值) ：

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
dataCenterName='dc1'
dataCenterLocation='eastus'
delegatedSubnetId= '/subscriptions/<Subscription_ID>/resourceGroups/customer-vnet-rg/providers/Microsoft.Network/virtualNetworks/customer-vnet/subnets/dc1-subnet'

az managed-cassandra datacenter update \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --data-center-name $dataCenterName \
    --node-count 13 
```

### <a name="get-the-datacenters-in-a-cluster"></a><a id="get-datacenters-cluster"></a>获取群集中的数据中心

获取群集中的数据中心：

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'

az managed-cassandra datacenter list \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName
```

## <a name="next-steps"></a>后续步骤

* [从 Azure 门户创建托管实例群集](create-cluster-portal.md)
* [使用 Azure Databricks 部署托管 Apache Spark 群集](deploy-cluster-databricks.md)