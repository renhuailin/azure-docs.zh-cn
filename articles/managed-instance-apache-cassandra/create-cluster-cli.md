---
title: 快速入门 - 使用 CLI 创建 Azure Managed Instance for Apache Cassandra 群集
description: 按照本快速入门使用 Azure CLI 创建 Azure Managed Instance for Apache Cassandra 群集。
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.openlocfilehash: 510fcf48091266af255c15aced80651619133aab
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101747427"
---
# <a name="quickstart-create-an-azure-managed-instance-for-apache-cassandra-cluster-using-azure-cli-preview"></a>快速入门：使用 Azure CLI 创建 Azure Managed Instance for Apache Cassandra 群集（预览版）

Azure Managed Instance for Apache Cassandra 为托管的开源 Apache Cassandra 数据中心提供自动部署和缩放操作。 此服务有助于加速混合方案的部署，并减少日常维护。

> [!IMPORTANT]
> Azure Managed Instance for Apache Cassandra 目前以公共预览版形式提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本快速入门演示如何使用 Azure CLI 命令创建 Azure Managed Instance for Apache Cassandra 群集。 它还介绍了如何创建数据中心，以及如何在数据中心内纵向扩展或缩减节点。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* 本文需要 Azure CLI 2.12.1 或更高版本。 如果你使用的是 Azure Cloud Shell，则表示已安装最新版本。

* 与自承载环境或本地环境连接的 [Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)。 若要详细了解如何将本地环境连接到 Azure，请参阅[将本地网络连接到 Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/) 一文。

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

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

1. 向虚拟网络和子网应用一些特殊权限 - 托管实例要求具备这些权限。 为此，你必须获取现有虚拟网络的资源 ID。 运行以下命令并复制 `Resource ID` 参数的值：

   ```azurecli-interactive
   # get the resource ID of the Virtual Network
   az network vnet show -n <VNet_name> -g <Resource_Group_Name> --query "id" --output tsv

1. Now apply the special permissions by using the `az role assignment create` command. Use the `Resource ID` parameter from the output of previous command to the `scope` parameter:

   ```azurecli-interactive
   az role assignment create --assignee e5007d2c-4b13-4a74-9b6a-605d99f03501 --role 4d97b98b-1d4f-4787-a291-c67834d212e7 --scope <Resource ID>
   ```

   > [!NOTE]
   > 上一命令中的 `assignee` 和 `role` 值分别是固定的服务主体和角色标识符。

1. 接下来，在新创建的虚拟网络中创建群集。 运行以下命令，并确保将在上一个命令中检索到的 `Resource ID` 值用作 `delegatedManagementSubnetId` 变量的值：

   ```azurecli-interactive
   resourceGroupName='<Resource_Group_Name>'
   clusterName='<Cluster_Name>'
   location='eastus2'
   delegatedManagementSubnetId='<Resource_ID>'
   initialCassandraAdminPassword='myPassword'
    
   az managed-cassandra cluster create \
      --cluster-name $clusterName \
      --resource-group $resourceGroupName \
      --location $location \
      --delegated-management-subnet-id $delegatedManagementSubnetId \
      --initial-cassandra-admin-password $initialCassandraAdminPassword \
      --debug
   ```

1. 最后，创建一个数据中心供群集使用，该中心包含 3 个节点：

   ```azurecli-interactive
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
   delegatedSubnetId='<Resource_ID>'
    
   az managed-cassandra datacenter create \
      --resource-group $resourceGroupName \
      --cluster-name $clusterName \
      --data-center-name $dataCenterName \
      --data-center-location $dataCenterLocation \
      --delegated-subnet-id $delegatedSubnetId \
      --node-count 3 
   ```

1. 创建数据中心后，如果想要纵向扩展或缩减数据中心内的节点，请运行以下命令。 将 `node-count` 参数的值更改为所需的值：

   ```azurecli-interactive
   resourceGroupName='<Resource_Group_Name>'
   clusterName='<Cluster Name>'
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
   delegatedSubnetId= '<Resource_ID>'
    
   az managed-cassandra datacenter update \
      --resource-group $resourceGroupName \
      --cluster-name $clusterName \
      --data-center-name $dataCenterName \
      --node-count 9 
   ```

## <a name="connect-to-your-cluster"></a>连接到群集

Azure Managed Instance for Apache Cassandra 不会创建具有公共 IP 地址的节点。 若要连接到新创建的 Cassandra 群集，必须在虚拟网络中另外创建一个资源。 此资源可以是应用程序，也可以是安装了 Apache 开源查询工具 [CQLSH](https://cassandra.apache.org/doc/latest/tools/cqlsh.html) 的虚拟机。 你可使用[资源管理器模板](https://azure.microsoft.com/resources/templates/101-vm-simple-linux/)来部署 Ubuntu 虚拟机。 部署后，使用 SSH 连接到该虚拟机并安装 CQLSH，如以下命令中所示：

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

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组、托管实例及所有相关资源，可使用 `az group delete` 命令将其删除：

```azurecli-interactive
az group delete --name <Resource_Group_Name>
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你了解了如何使用 Azure CLI 创建 Azure Managed Instance for Apache Cassandra 群集。 现在你可开始使用该群集了：

> [!div class="nextstepaction"]
> [使用 Azure Databricks 部署托管的 Apache Spark 群集](deploy-cluster-databricks.md)
