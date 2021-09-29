---
title: 快速入门 - 使用 CLI 创建 Azure Managed Instance for Apache Cassandra 群集
description: 按照本快速入门使用 Azure CLI 创建 Azure Managed Instance for Apache Cassandra 群集。
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 09/08/2021
ms.openlocfilehash: b2d00a30a1b53ecb6c854e84c3202e10a92abcea
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124736314"
---
# <a name="quickstart-create-an-azure-managed-instance-for-apache-cassandra-cluster-using-azure-cli-preview"></a>快速入门：使用 Azure CLI 创建 Azure Managed Instance for Apache Cassandra 群集（预览版）

Azure Managed Instance for Apache Cassandra 为托管的开源 Apache Cassandra 数据中心提供自动部署和缩放操作。 此服务有助于加速混合方案的部署，并减少日常维护。

> [!IMPORTANT]
> Azure Managed Instance for Apache Cassandra 目前以公共预览版形式提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本快速入门演示如何使用 Azure CLI 命令创建 Azure Managed Instance for Apache Cassandra 群集。 它还介绍了如何创建数据中心，以及如何在数据中心内纵向扩展或缩减节点。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* 与自承载环境或本地环境连接的 [Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)。 若要详细了解如何将本地环境连接到 Azure，请参阅[将本地网络连接到 Azure](/azure/architecture/reference-architectures/hybrid-networking/) 一文。

* 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

> [!IMPORTANT]
> 本文需要 Azure CLI 2.17.1 或更高版本。 如果你使用的是 Azure Cloud Shell，则表示已安装最新版本。

## <a name="create-a-managed-instance-cluster"></a><a id="create-cluster"></a>创建托管实例群集

1. 登录到 [Azure 门户](https://portal.azure.com/)

1. 在 Azure CLI 中设置订阅 ID：

   ```azurecli-interactive
   az account set -s <Subscription_ID>
   ```

1. 接下来，在资源组中创建包含专用子网的虚拟网络：

   ```azurecli-interactive
   az network vnet create -n <VNet_Name> -l eastus2 -g <Resource_Group_Name> --subnet-name <Subnet Name>
   ```

   > [!NOTE]
   > 部署 Azure Managed Instance for Apache Cassandra 需要 Internet 访问权限。 在限制 Internet 访问权限的环境中，部署将失败。 请确保未在 VNet 中阻止对以下关键 Azure 服务（托管 Cassandra 需要这些服务才能正常工作）的访问权限：
   > - Azure 存储
   > - Azure KeyVault
   > - Azure 虚拟机规模集
   > - Azure 监视
   > - Azure Active Directory
   > - Azure 安全性

1. 向虚拟网络应用托管实例需要的一些特殊权限。 使用 `az role assignment create` 命令，将 `<subscriptionID>`、`<resourceGroupName>` 和 `<vnetName>` 替换为适当的值：

   ```azurecli-interactive
   az role assignment create \
     --assignee a232010e-820c-4083-83bb-3ace5fc29d0b \
     --role 4d97b98b-1d4f-4787-a291-c67834d212e7 \
     --scope /subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Network/virtualNetworks/<vnetName>
   ```

   > [!NOTE]
   > 上一命令中的 `assignee` 和 `role` 值是固定值，请完全按照命令中的说明输入这些值。 如果不这样做，则会在创建群集时出错。 如果在执行此命令时遇到任何错误，则你可能没有运行此命令的权限，请与管理员联系以获取权限。

1. 接下来，使用 [az managed-cassandra cluster create](/cli/azure/managed-cassandra/cluster?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_cluster_create) 命令在新创建的虚拟网络中创建群集。 运行以下命令，`delegatedManagementSubnetId` 变量的值：

   > [!NOTE]
   > 你将在下面提供的 `delegatedManagementSubnetId` 变量的值与你在上面的命令中提供的 `--scope` 的值完全相同：

   ```azurecli-interactive
   resourceGroupName='<Resource_Group_Name>'
   clusterName='<Cluster_Name>'
   location='eastus2'
   delegatedManagementSubnetId='/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>/subnets/<subnet name>'
   initialCassandraAdminPassword='myPassword'
    
   az managed-cassandra cluster create \
     --cluster-name $clusterName \
     --resource-group $resourceGroupName \
     --location $location \
     --delegated-management-subnet-id $delegatedManagementSubnetId \
     --initial-cassandra-admin-password $initialCassandraAdminPassword \
     --debug
   ```

1. 最后，使用 [az managed-cassandra datacenter create](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_create) 命令为群集创建一个数据中心，其中包含三个节点：

   ```azurecli-interactive
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
    
   az managed-cassandra datacenter create \
     --resource-group $resourceGroupName \
     --cluster-name $clusterName \
     --data-center-name $dataCenterName \
     --data-center-location $dataCenterLocation \
     --delegated-subnet-id $delegatedManagementSubnetId \
     --node-count 3 
   ```

1. 创建数据中心后，如果想要纵向扩展或缩减数据中心内的节点，请运行 [az managed-cassandra datacenter update](/cli/azure/managed-cassandra/datacenter?view=azure-cli-latest&preserve-view=true#az_managed_cassandra_datacenter_update) 命令。 将 `node-count` 参数的值更改为所需的值：

   ```azurecli-interactive
   resourceGroupName='<Resource_Group_Name>'
   clusterName='<Cluster Name>'
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
    
   az managed-cassandra datacenter update \
     --resource-group $resourceGroupName \
     --cluster-name $clusterName \
     --data-center-name $dataCenterName \
     --node-count 9 
   ```

## <a name="connect-to-your-cluster"></a>连接到群集

Azure Managed Instance for Apache Cassandra 不会创建具有公共 IP 地址的节点。 若要连接到新创建的 Cassandra 群集，必须在虚拟网络中另外创建一个资源。 此资源可以是应用程序，也可以是安装了 Apache 开源查询工具 [CQLSH](https://cassandra.apache.org/doc/latest/cassandra/tools/cqlsh.html) 的虚拟机。 你可使用[资源管理器模板](https://azure.microsoft.com/resources/templates/vm-simple-linux/)来部署 Ubuntu 虚拟机。 部署后，使用 SSH 连接到该虚拟机并安装 CQLSH，如以下命令中所示：

```bash
# Install default-jre and default-jdk
sudo apt update
sudo apt install openjdk-8-jdk openjdk-8-jre

# Install the Cassandra libraries in order to get CQLSH:
echo "deb http://www.apache.org/dist/cassandra/debian 311x main" | sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
curl https://downloads.apache.org/cassandra/KEYS | sudo apt-key add -
sudo apt-get update
sudo apt-get install cassandra

# Export the SSL variables:
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false

# Connect to CQLSH (replace <IP> with the private IP addresses of the nodes in your Datacenter):
host=("<IP>" "<IP>" "<IP>")
cqlsh $host 9042 -u cassandra -p cassandra --ssl
```

## <a name="troubleshooting"></a>疑难解答

如果在将权限应用到虚拟网络时遇到错误，如 *无法在e5007d2c-4b13-4a74-9b6a-605d99f03501”的图形数据库中找到用户或服务主体*，则可以在 Azure 门户中手动应用相同的权限。 要在门户中应用权限，请访问现有虚拟网络的“访问控制 (IAM)”窗格，并将“Azure Cosmos DB”的角色分配添加到“网络管理员”角色。 如果搜索“Azure Cosmos DB”时出现两个条目，请同时添加这两个条目，如下图所示： 

   :::image type="content" source="./media/create-cluster-cli/apply-permissions.png" alt-text="应用权限" lightbox="./media/create-cluster-cli/apply-permissions.png" border="true":::

> [!NOTE] 
> Azure Cosmos DB 角色分配仅用于部署目的。 Azure Managed Instanced for Apache Cassandra 对于 Azure Cosmos DB 不存在后端依赖关系。  

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组、托管实例及所有相关资源，可使用 `az group delete` 命令将其删除：

```azurecli-interactive
az group delete --name <Resource_Group_Name>
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你了解了如何使用 Azure CLI 创建 Azure Managed Instance for Apache Cassandra 群集。 现在你可开始使用该群集了：

> [!div class="nextstepaction"]
> [使用 Azure Databricks 部署托管的 Apache Spark 群集](deploy-cluster-databricks.md)
