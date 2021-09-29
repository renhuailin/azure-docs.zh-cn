---
title: 快速入门 - 使用 Azure Managed Instance for Apache Cassandra 配置多区域群集
description: 本快速入门介绍如何使用 Azure Managed Instance for Apache Cassandra 配置多区域群集。
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 09/08/2021
ms.openlocfilehash: b40a2a2a8aaff878fa514ddd0d5b56eb9d5e10a3
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124736234"
---
# <a name="quickstart-create-a-multi-region-cluster-with-azure-managed-instance-for-apache-cassandra-preview"></a>快速入门：使用 Azure Managed Instance for Apache Cassandra（预览版）创建多区域群集

Azure Managed Instance for Apache Cassandra 为托管的开源 Apache Cassandra 数据中心提供自动部署和缩放操作。 此服务有助于加速混合方案的部署，并减少日常维护。

> [!IMPORTANT]
> Azure Managed Instance for Apache Cassandra 目前以公共预览版形式提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本快速入门演示如何使用 Azure CLI 命令在 Azure 中配置多区域群集。  

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* 本文需要 Azure CLI 2.12.1 或更高版本。 如果你使用的是 Azure Cloud Shell，则表示已安装最新版本。

* 与自承载环境或本地环境连接的 [Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)。 若要详细了解如何将本地环境连接到 Azure，请参阅[将本地网络连接到 Azure](/azure/architecture/reference-architectures/hybrid-networking/) 一文。

## <a name="setting-up-the-network-environment"></a><a id="create-account"></a>设置网络环境

由于必须使用 VNet 注入将使用此服务预配的所有数据中心部署到专用子网中，因此需要在部署前配置合适的网络对等互连，以确保多区域群集正常运行。 我们将在单独的区域创建包含两个数据中心的群集：美国东部和美国东部 2。 首先，我们需要为每个区域创建虚拟网络。 

1. 登录 [Azure 门户](https://portal.azure.com/)。

1. 创建名为“cassandra-mi-multi-region”的资源组

   ```azurecli-interactive
   az group create -l eastus2 -n cassandra-mi-multi-region
   ```

1. 使用专用子网在美国东部 2 创建第一个 VNet：

   ```azurecli-interactive
   az network vnet create \
     -n vnetEastUs2 \
     -l eastus2 \
     -g cassandra-mi-multi-region \
     --address-prefix 10.0.0.0/16 \
     --subnet-name dedicated-subnet
   ```

1. 现在，同样使用专用子网在美国东部创建第二个 VNet：

   ```azurecli-interactive
    az network vnet create \
      -n vnetEastUs \
      -l eastus \
      -g cassandra-mi-multi-region \
      --address-prefix 192.168.0.0/16 \
      --subnet-name dedicated-subnet
   ```

   > [!NOTE]
   > 我们显式添加了不同的 IP 地址范围，以确保在对等互连时不会出现错误。 

1. 现在，我们需要将第一个 VNet 与第二个 VNet 对等互连：

   ```azurecli-interactive
   az network vnet peering create \
     -g cassandra-mi-multi-region \
     -n MyVnet1ToMyVnet2 \
     --vnet-name vnetEastUs2 \
     --remote-vnet vnetEastUs \
     --allow-vnet-access \
     --allow-forwarded-traffic
   ```

1. 若要连接两个 VNet，请在第二个 VNet 和第一个 VNet 之间创建另一个对等互连：

   ```azurecli-interactive
   az network vnet peering create \
     -g cassandra-mi-multi-region \
     -n MyVnet2ToMyVnet1 \
     --vnet-name vnetEastUs \
     --remote-vnet vnetEastUs2 \
     --allow-vnet-access \
     --allow-forwarded-traffic  
   ```

   > [!NOTE]
   > 如果要添加更多区域，则每个 VNet 都需要与所有其他 VNet 进行对等互连。 

1. 检查上一个命令的输出，并确保“peeringState”的值现在为“已连接”。 还可以通过运行以下命令来进行检查：

   ```azurecli-interactive
   az network vnet peering show \
     --name MyVnet1ToMyVnet2 \
     --resource-group cassandra-mi-multi-region \
     --vnet-name vnetEastUs2 \
     --query peeringState
   ``` 

1. 接下来，对这两个虚拟网络应用一些特殊权限，这是 Azure Managed Instance for Apache Cassandra 的需求。 运行以下内容并确保将 `<SubscriptionID>` 替换为你的订阅 ID：

   ```azurecli-interactive
   az role assignment create \
     --assignee a232010e-820c-4083-83bb-3ace5fc29d0b \
     --role 4d97b98b-1d4f-4787-a291-c67834d212e7 \
     --scope /subscriptions/<SubscriptionID>/resourceGroups/cassandra-mi-multi-region/providers/Microsoft.Network/virtualNetworks/vnetEastUs2

   az role assignment create     \
     --assignee a232010e-820c-4083-83bb-3ace5fc29d0b \
     --role 4d97b98b-1d4f-4787-a291-c67834d212e7 \
     --scope /subscriptions/<SubscriptionID>/resourceGroups/cassandra-mi-multi-region/providers/Microsoft.Network/virtualNetworks/vnetEastUs
    ```
   > [!NOTE]
   > 上一命令中的 `assignee` 和 `role` 值是固定值，请完全按照命令中的说明输入这些值。 如果不这样做，则会在创建群集时出错。 如果在执行此命令时遇到任何错误，则你可能没有运行此命令的权限，请与管理员联系以获取权限。

## <a name="create-a-multi-region-cluster"></a><a id="create-account"></a>创建多区域群集

1. 现在，我们已准备好适当的网络，接下来可以部署群集资源（将 `<Subscription ID>` 替换为你的订阅 ID）。 此过程可能需要 5-10 分钟：

   ```azurecli-interactive
   resourceGroupName='cassandra-mi-multi-region'
   clusterName='test-multi-region'
   location='eastus2'
   delegatedManagementSubnetId='/subscriptions/<SubscriptionID>/resourceGroups/cassandra-mi-multi-region/providers/Microsoft.Network/virtualNetworks/vnetEastUs2/subnets/dedicated-subnet'
   initialCassandraAdminPassword='myPassword'
        
    az managed-cassandra cluster create \
      --cluster-name $clusterName \
      --resource-group $resourceGroupName \
      --location $location \
      --delegated-management-subnet-id $delegatedManagementSubnetId \
      --initial-cassandra-admin-password $initialCassandraAdminPassword \
      --debug
   ```

1. 创建群集资源后，即可创建数据中心。 首先，在美国东部 2 创建一个数据中心（将 `<SubscriptionID>` 替换为你的订阅 ID）。 此过程最长需要 10 分钟：

   ```azurecli-interactive
   resourceGroupName='cassandra-mi-multi-region'
   clusterName='test-multi-region'
   dataCenterName='dc-eastus2'
   dataCenterLocation='eastus2'
   delegatedManagementSubnetId='/subscriptions/<SubscriptionID>/resourceGroups/cassandra-mi-multi-region/providers/Microsoft.Network/virtualNetworks/vnetEastUs2/subnets/dedicated-subnet'
        
    az managed-cassandra datacenter create \
       --resource-group $resourceGroupName \
       --cluster-name $clusterName \
       --data-center-name $dataCenterName \
       --data-center-location $dataCenterLocation \
       --delegated-subnet-id $delegatedManagementSubnetId \
       --node-count 3
   ```

1. 接下来，在美国东部创建一个数据中心（将 `<SubscriptionID>` 替换为你的订阅 ID）：

   ```azurecli-interactive
   resourceGroupName='cassandra-mi-multi-region'
   clusterName='test-multi-region'
   dataCenterName='dc-eastus'
   dataCenterLocation='eastus'
   delegatedManagementSubnetId='/subscriptions/<SubscriptionID>/resourceGroups/cassandra-mi-multi-region/providers/Microsoft.Network/virtualNetworks/vnetEastUs/subnets/dedicated-subnet'
        
    az managed-cassandra datacenter create \
      --resource-group $resourceGroupName \
      --cluster-name $clusterName \
      --data-center-name $dataCenterName \
      --data-center-location $dataCenterLocation \
      --delegated-subnet-id $delegatedManagementSubnetId \
      --node-count 3 
   ```

1. 创建第二个数据中心后，请获取节点状态，验证是否已成功显示所有 Cassandra 节点：

    ```azurecli-interactive
    resourceGroupName='cassandra-mi-multi-region'
    clusterName='test-multi-region'
    
    az managed-cassandra cluster node-status \
       --cluster-name $clusterName \
       --resource-group $resourceGroupName
    ```

1. 最后，使用 CQLSH [连接群集](create-cluster-cli.md#connect-to-your-cluster)，并使用以下 CQL 查询更新每个密钥空间中的复制策略，使其包含整个群集中的所有数据中心：

   ```bash
   ALTER KEYSPACE "ks" WITH REPLICATION = {'class': 'NetworkTopologyStrategy', 'dc-eastus2': 3, 'dc-eastus': 3};
   ```
   还需要更新密码表：

   ```bash
    ALTER KEYSPACE "system_auth" WITH REPLICATION = {'class': 'NetworkTopologyStrategy', 'dc-eastus2': 3, 'dc-eastus': 3}
   ```

## <a name="troubleshooting"></a>疑难解答

如果在将权限应用到虚拟网络时遇到错误，如“无法在‘e5007d2c-4b13-4a74-9b6a-605d99f03501’的图形数据库中找到用户或服务主体”，则可以在 Azure 门户中手动应用相同的权限。 

要在 Azure 门户中应用权限，请转到现有虚拟网络的“访问控制(IAM)”窗格，并将“Azure Cosmos DB”的角色分配添加到“网络管理员”角色。 如果搜索“Azure Cosmos DB”时出现两个条目，请同时添加这两个条目，如下图所示： 

   :::image type="content" source="./media/create-cluster-cli/apply-permissions.png" alt-text="应用权限" lightbox="./media/create-cluster-cli/apply-permissions.png" border="true":::

> [!NOTE] 
> Azure Cosmos DB 角色分配仅用于部署目的。 Azure Managed Instanced for Apache Cassandra 对于 Azure Cosmos DB 不存在后端依赖关系。  

## <a name="clean-up-resources"></a>清理资源

如果不打算继续使用此托管实例群集，请按照以下步骤删除它：

1. 从 Azure 门户的左侧菜单中选择“资源组”。
1. 从列表中选择为本快速入门创建的资源组。
1. 在资源组的“概述”窗格上，选择“删除资源组” 。
1. 在下一窗口中输入要删除的资源组的名称，然后选择“删除”  。

## <a name="next-steps"></a>后续步骤

在本快速入门中，你了解了如何使用 Azure CLI 和 Azure Managed Instance for Apache Cassandra 创建多区域群集。 现在你可开始使用该群集了。

> [!div class="nextstepaction"]
> [使用 Azure CLI 管理 Azure Managed Instance for Apache Cassandra 资源](manage-resources-cli.md)
