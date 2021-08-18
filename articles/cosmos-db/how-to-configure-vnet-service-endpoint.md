---
title: 为 Azure Cosmos 帐户配置基于虚拟网络的访问
description: 本文档介绍为 Azure Cosmos DB 设置虚拟网络服务终结点所要执行的步骤。
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/07/2021
ms.author: thweiss
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 42e38162409c5762f43807f483b091fbb88638af
ms.sourcegitcommit: bc29cf4472118c8e33e20b420d3adb17226bee3f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2021
ms.locfileid: "113493272"
---
# <a name="configure-access-to-azure-cosmos-db-from-virtual-networks-vnet"></a>配置从虚拟网络 (VNet) 到 Azure Cosmos DB 的访问
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

可将 Azure Cosmos 帐户配置为仅允许从虚拟网络 (VNet) 的特定子网进行访问。 启用[服务终结点](../virtual-network/virtual-network-service-endpoints-overview.md)来访问虚拟网络中子网上的 Azure Cosmos DB 后，来自该子网的流量将发送到具有该子网和虚拟网络的标识的 Azure Cosmos DB。 启用 Azure Cosmos DB 服务终结点后，可以通过将子网添加到 Azure Cosmos 帐户来限制对该子网的访问。

默认情况下，如果请求附带有效的授权令牌，则可从任何源访问 Azure Cosmos 帐户。 在 VNet 中添加一个或多个子网后，只有源自这些子网的请求才能获取有效响应。 源自其他任何源的请求将收到 403（禁止访问）响应。 

可将 Azure Cosmos DB 帐户配置为仅允许从 Azure 虚拟网络的特定子网进行访问。 限制使用虚拟网络中子网的连接对 Azure Cosmos DB 帐户的访问：

1. 让该子网将其标识和虚拟网络标识发送到 Azure Cosmos DB。 在特定的子网中为 Azure Cosmos DB 启用服务终结点可实现此目的。

1. 在 Azure Cosmos DB 帐户中添加一个规则，以便将此子网指定为可从中访问帐户的源。

> [!NOTE]
> 在子网中为 Azure Cosmos DB 帐户启用服务终结点之后，抵达 Azure Cosmos DB 的流量的源将从公共 IP 切换到虚拟网络和子网。 流量切换适用于从此子网访问的任何 Azure Cosmos DB 帐户。 如果 Azure Cosmos DB 帐户包含允许此子网的基于 IP 的防火墙，则已启用服务的子网发出的请求将不再与 IP 防火墙规则相匹配，因此会遭到拒绝。
>
> 有关详细信息，请参阅本文的[从 IP 防火墙规则迁移到虚拟网络访问控制列表](#migrate-from-firewall-to-vnet)部分所述的步骤。

以下部分介绍如何为 Azure Cosmos DB 帐户配置虚拟网络服务终结点。

## <a name="configure-a-service-endpoint-by-using-the-azure-portal"></a><a id="configure-using-portal"></a>使用 Azure 门户配置服务终结点

### <a name="configure-a-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>为现有的 Azure 虚拟网络和子网配置服务终结点

1. 在“所有资源”边栏选项卡中，找到想要保护的 Azure Cosmos DB 帐户。

1. 从设置菜单中选择“防火墙和虚拟网络”，然后选择允许从“选定的网络”进行访问。 

1. 若要授予对现有虚拟网络子网的访问权限，请在“虚拟网络”下面选择“添加现有的 Azure 虚拟网络”。 

1. 选择要从中添加 Azure 虚拟网络的 **订阅**。 选择要向其提供 Azure Cosmos DB 帐户访问权限的 Azure **虚拟网络** 和 **子网**。 接下来选择“启用”，以便为“Microsoft.AzureCosmosDB”启用包含服务终结点的选定网络。 完成后，选择“添加”。

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet.png" alt-text="选择虚拟网络和子网":::

   > [!NOTE]
   > 配置 VNET 服务终结点可能需要长达 15 分钟的时间来传播，终结点在此期间可能会表现出不一致的行为。

1. 允许从虚拟网络访问 Azure Cosmos DB 帐户之后，只允许来自此所选子网的流量。 添加的虚拟网络和子网应会显示，如以下屏幕截图所示：

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/vnet-and-subnet-configured-successfully.png" alt-text="已成功配置虚拟网络和子网":::

> [!NOTE]
> 若要启用虚拟网络服务终结点，需要以下订阅权限：
>   * 使用虚拟网络的订阅：网络参与者
>   * 使用 Azure Cosmos DB 帐户的订阅：DocumentDB 帐户参与者
>   * 如果虚拟网络与 Azure Cosmos DB 帐户位于不同的订阅中，请确保包含虚拟网络的订阅还注册了 `Microsoft.DocumentDB` 资源提供程序。 若要注册资源提供程序，请参阅 [Azure 资源提供程序和类型](../azure-resource-manager/management/resource-providers-and-types.md)一文。

以下是向资源提供程序注册订阅的说明。

### <a name="configure-a-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>为新的 Azure 虚拟网络和子网配置服务终结点

1. 在“所有资源”边栏选项卡中，找到想要保护的 Azure Cosmos DB 帐户。  

1. 从设置菜单中选择“防火墙和 Azure 虚拟网络”，然后选择允许从“选定的网络”进行访问。   

1. 若要授予对新 Azure 虚拟网络的访问权限，请在“虚拟网络”下面选择“添加新虚拟网络”。   

1. 提供创建新虚拟网络所需的详细信息，然后选择“创建”。 随后将为启用的“Microsoft.AzureCosmosDB”创建包含服务终结点的子网。

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png" alt-text="为新虚拟网络选择虚拟网络和子网":::

如果你的 Azure Cosmos DB 帐户被 Azure 认知搜索等其他 Azure 服务使用，或者可以通过流分析或 Power BI 访问，则可以通过选择“接受来自全局 Azure 数据中心内部的连接”来允许访问。

为确保能够从门户访问 Azure Cosmos DB 指标，需要启用“允许从 Azure 门户访问”选项。 有关这些选项的详细信息，请参阅[配置 IP 防火墙](how-to-configure-firewall.md)一文。 启用访问后，选择“保存”以保存设置。

## <a name="remove-a-virtual-network-or-subnet"></a><a id="remove-vnet-or-subnet"></a>删除虚拟网络或子网

1. 在“所有资源”边栏选项卡中，找到为其分配了服务终结点的 Azure Cosmos DB 帐户。  

1. 从设置菜单中选择“防火墙和虚拟网络”。  

1. 若要删除某个虚拟网络或子网规则，请选择虚拟网络或子网旁边的“...”，然后选择“删除”。 

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png" alt-text="删除虚拟网络":::

1. 单击“保存”应用所做的更改。

## <a name="configure-a-service-endpoint-by-using-azure-powershell"></a><a id="configure-using-powershell"></a>使用 Azure PowerShell 配置服务终结点

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

在 Azure PowerShell 中使用以下步骤配置 Azure Cosmos DB 帐户的服务终结点：  

1. 安装 [Azure PowerShell](/powershell/azure/install-Az-ps) 并[登录](/powershell/azure/authenticate-azureps)。  

1. 为虚拟网络的现有子网启用服务终结点。  

   ```powershell
   $resourceGroupName = "<Resource group name>"
   $vnetName = "<Virtual network name>"
   $subnetName = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"
   $serviceEndpoint = "Microsoft.AzureCosmosDB"

   Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName | Set-AzVirtualNetworkSubnetConfig `
      -Name $subnetName `
      -AddressPrefix $subnetPrefix `
      -ServiceEndpoint $serviceEndpoint | Set-AzVirtualNetwork
   ```

   > [!NOTE]
   > 使用 PowerShell 或 Azure CLI 时，请务必在参数中指定 IP 筛选器和虚拟网络 ACL 的完整列表，而不仅仅是需要添加的项。

1. 获取虚拟网络信息。

   ```powershell
   $vnet = Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName

   $subnetId = $vnet.Id + "/subnets/" + $subnetName
   ```

1. 准备 Cosmos DB 虚拟网络规则

   ```powershell
   $vnetRule = New-AzCosmosDBVirtualNetworkRule `
      -Id $subnetId
   ```

1. 使用新的虚拟网络终结点配置更新 Azure Cosmos DB 帐户属性： 

   ```powershell
   $accountName = "<Cosmos DB account name>"

   Update-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName `
      -EnableVirtualNetwork $true `
      -VirtualNetworkRuleObject @($vnetRule)
   ```

1. 运行以下命令，验证是否已使用上一步骤中配置的虚拟网络服务终结点更新 Azure Cosmos DB 帐户：

   ```powershell
   $account = Get-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName

   $account.IsVirtualNetworkFilterEnabled
   $account.VirtualNetworkRules
   ```

## <a name="configure-a-service-endpoint-by-using-the-azure-cli"></a><a id="configure-using-cli"></a>使用 Azure CLI 配置服务终结点

Azure Cosmos 帐户可以在以后创建或更新服务终结点时针对服务终结点进行配置（如果已经为其配置了子网）。 还可以在尚未为其配置子网的 Cosmos 帐户上启用服务终结点，然后这些服务终结点在以后配置子网时开始工作。 这种灵活性允许无法同时访问 Cosmos 帐户和虚拟网络资源的管理员将其配置设为彼此独立。

### <a name="create-a-new-cosmos-account-and-connect-it-to-a-back-end-subnet-for-a-new-virtual-network"></a>创建新的 Cosmos 帐户并将其连接到新虚拟网络的后端子网

在此示例中，创建虚拟网络和子网时会同时为它们启用服务终结点。

```azurecli-interactive
# Create an Azure Cosmos Account with a service endpoint connected to a backend subnet

# Resource group and Cosmos account variables
resourceGroupName='MyResourceGroup'
location='West US 2'
accountName='mycosmosaccount'

# Variables for a new Virtual Network with two subnets
vnetName='myVnet'
frontEnd='FrontEnd'
backEnd='BackEnd'

# Create a resource group
az group create -n $resourceGroupName -l $location

# Create a virtual network with a front-end subnet
az network vnet create \
   -n $vnetName \
   -g $resourceGroupName \
   --address-prefix 10.0.0.0/16 \
   --subnet-name $frontEnd \
   --subnet-prefix 10.0.1.0/24

# Create a back-end subnet with service endpoints enabled for Cosmos DB
az network vnet subnet create \
   -n $backEnd \
   -g $resourceGroupName \
   --address-prefix 10.0.2.0/24 \
   --vnet-name $vnetName \
   --service-endpoints Microsoft.AzureCosmosDB

svcEndpoint=$(az network vnet subnet show -g $resourceGroupName -n $backEnd --vnet-name $vnetName --query 'id' -o tsv)

# Create a Cosmos DB account with default values and service endpoints
az cosmosdb create \
   -n $accountName \
   -g $resourceGroupName \
   --enable-virtual-network true \
   --virtual-network-rules $svcEndpoint
```

### <a name="connect-and-configure-a-cosmos-account-to-a-back-end-subnet-independently"></a>将 Cosmos 帐户独立连接并配置到后端子网

此示例旨在说明如何将 Azure Cosmos 帐户连接到尚未为服务终结点配置子网的现有新虚拟网络。 这是通过使用 `--ignore-missing-vnet-service-endpoint` 参数完成的。 这样就可以在完成对虚拟网络的子网的配置之前，完成 Cosmos 帐户的配置而不会出现错误。 子网配置完成后，便可通过配置的子网访问 Cosmos 帐户。

```azurecli-interactive
# Create an Azure Cosmos Account with a service endpoint connected to a backend subnet
# that is not yet enabled for service endpoints.

# Resource group and Cosmos account variables
resourceGroupName='MyResourceGroup'
location='West US 2'
accountName='mycosmosaccount'

# Variables for a new Virtual Network with two subnets
vnetName='myVnet'
frontEnd='FrontEnd'
backEnd='BackEnd'

# Create a resource group
az group create -n $resourceGroupName -l $location

# Create a virtual network with a front-end subnet
az network vnet create \
   -n $vnetName \
   -g $resourceGroupName \
   --address-prefix 10.0.0.0/16 \
   --subnet-name $frontEnd \
   --subnet-prefix 10.0.1.0/24

# Create a back-end subnet but without configuring service endpoints (--service-endpoints Microsoft.AzureCosmosDB)
az network vnet subnet create \
   -n $backEnd \
   -g $resourceGroupName \
   --address-prefix 10.0.2.0/24 \
   --vnet-name $vnetName

svcEndpoint=$(az network vnet subnet show -g $resourceGroupName -n $backEnd --vnet-name $vnetName --query 'id' -o tsv)

# Create a Cosmos DB account with default values
az cosmosdb create -n $accountName -g $resourceGroupName

# Add the virtual network rule but ignore the missing service endpoint on the subnet
az cosmosdb network-rule add \
   -n $accountName \
   -g $resourceGroupName \
   --virtual-network $vnetName \
   --subnet svcEndpoint \
   --ignore-missing-vnet-service-endpoint true

read -p'Press any key to now configure the subnet for service endpoints'

az network vnet subnet update \
   -n $backEnd \
   -g $resourceGroupName \
   --vnet-name $vnetName \
   --service-endpoints Microsoft.AzureCosmosDB
```

## <a name="port-range-when-using-direct-mode"></a>使用直接模式时的端口范围

通过直接模式连接结合使用服务终结点和 Azure Cosmos 帐户时，需要确保启用的 TCP 端口范围为 10000 到 20000。

## <a name="migrating-from-an-ip-firewall-rule-to-a-virtual-network-acl"></a><a id="migrate-from-firewall-to-vnet"></a>从 IP 防火墙规则迁移到虚拟网络 ACL

若要将 Azure Cosmos DB 帐户从使用 IP 防火墙规则转换为使用虚拟网络服务终结点，请使用以下步骤。

为子网的服务终结点配置 Azure Cosmos DB 帐户后，来自该子网的请求将使用虚拟网络和子网源信息（而不是源公共 IP 地址）发送到 Azure Cosmos DB。 这些请求将不再匹配 Azure Cosmos DB 帐户上配置的 IP 筛选器，所以需要执行以下步骤以避免停机。

继续之前，请使用上面“为虚拟网络的现有子网启用服务终结点”中显示的步骤在虚拟网络和子网上启用 Azure Cosmos DB 服务终结点。

1. 获取虚拟网络和子网信息：

   ```powershell
   $resourceGroupName = "myResourceGroup"
   $accountName = "mycosmosaccount"
   $vnetName = "myVnet"
   $subnetName = "mySubnet"

   $vnet = Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName

   $subnetId = $vnet.Id + "/subnets/" + $subnetName
   ```

1. 为 Azure Cosmos DB 帐户准备新的虚拟网络规则对象：

   ```powershell
   $vnetRule = New-AzCosmosDBVirtualNetworkRule `
      -Id $subnetId
   ```

1. 更新 Azure Cosmos DB 帐户以实现从子网访问服务终结点：

   ```powershell
   Update-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName `
      -EnableVirtualNetwork $true `
      -VirtualNetworkRuleObject @($vnetRule)
   ```

1. 对从子网访问的所有 Azure Cosmos DB 帐户重复前面的步骤。

1. 从 Azure Cosmos DB 帐户的防火墙规则中删除子网的 IP 防火墙规则。

## <a name="frequently-asked-questions"></a>常见问题

下面是有关配置从虚拟网络进行访问的一些常见问题：

### <a name="are-notebooks-and-mongocassandra-shell-currently-compatible-with-virtual-network-enabled-accounts"></a>Notebooks 和 Mongo/Cassandra Shell 目前是否与已启用虚拟网络的帐户兼容？

目前，不支持通过 VNET 访问 Cosmos DB 数据资源管理器中的 [Mongo Shell](https://devblogs.microsoft.com/cosmosdb/preview-native-mongo-shell/) 和 [Cassandra Shell](https://devblogs.microsoft.com/cosmosdb/announcing-native-cassandra-shell-preview/) 集成以及 [Jupyter Notebooks 服务](./cosmosdb-jupyter-notebooks.md)。 此功能目前正在积极开发中。

### <a name="can-i-specify-both-virtual-network-service-endpoint-and-ip-access-control-policy-on-an-azure-cosmos-account"></a>是否可以在 Azure Cosmos 帐户中同时指定虚拟网络服务终结点和 IP 访问控制策略？ 

可以在 Azure Cosmos 帐户中同时启用虚拟网络服务终结点和 IP 访问控制策略（也称为防火墙）。 这两个功能是互补的，共同确保 Azure Cosmos 帐户的隔离性和安全性。 使用 IP 防火墙可确保静态 IP 能够访问你的帐户。 

### <a name="how-do-i-limit-access-to-subnet-within-a-virtual-network"></a>如何限制对虚拟网络中子网的访问？ 

需要执行两个步骤来限制从子网对 Azure Cosmos 帐户的访问。 首先，允许来自子网的流量将其子网和虚拟网络标识传递给 Azure Cosmos DB。 在子网中为 Azure Cosmos DB 启用服务终结点可实现此目的。 接下来，在 Azure Cosmos 帐户中添加一个规则，以便将此子网指定为可从中访问帐户的源。

### <a name="will-virtual-network-acls-and-ip-firewall-reject-requests-or-connections"></a>虚拟网络 ACL 和 IP 防火墙是否会拒绝请求或连接？ 

添加 IP 防火墙或虚拟网络访问规则后，只有来自受允许源的请求才能获取有效响应。 将拒绝其他请求并返回 403（禁止访问）错误。 必须将 Azure Cosmos 帐户的防火墙与连接级别的防火墙区分开来。 源仍可连接到服务，连接本身不会遭到拒绝。

### <a name="my-requests-started-getting-blocked-when-i-enabled-service-endpoint-to-azure-cosmos-db-on-the-subnet-what-happened"></a>在子网中为 Azure Cosmos DB 启用服务终结点后，我的请求开始遭到阻止。 发生了什么情况？

在子网中为 Azure Cosmos DB 启用服务终结点后，抵达帐户的流量源将从公共 IP 切换到虚拟网络和子网。 如果 Azure Cosmos 帐户仅包含基于 IP 的防火墙，则已启用服务的子网发出的流量将不再与 IP 防火墙规则相匹配，因此遭到拒绝。 请重温有关从基于 IP 的防火墙无缝迁移到基于虚拟网络的访问控制的步骤。

### <a name="are-additional-azure-rbac-permissions-needed-for-azure-cosmos-accounts-with-vnet-service-endpoints"></a>对于使用 VNET 服务终结点的 Azure Cosmos 帐户，是否需要其他 Azure RBAC 权限？

在将 VNet 服务终结点添加到 Azure Cosmos 帐户后，若要对帐户设置进行任何更改，需要访问 Azure Cosmos 帐户上配置的所有 VNET 的 `Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action` 操作。 此权限是必需的，因为授权过程会先验证对资源（例如数据库和虚拟网络资源）的访问权限，然后再对所有属性进行评估。
 
即使用户没有使用 Azure CLI 指定 VNET ACL，授权也会验证对 VNet 资源操作的权限。 目前，Azure Cosmos 帐户的控制平面支持设置 Azure Cosmos 帐户的完整状态。 控制平面调用的其中一个参数是 `virtualNetworkRules`。 如果未指定此参数，Azure CLI 将执行 get database 调用来检索 `virtualNetworkRules`，并在更新调用中使用此值。

### <a name="do-the-peered-virtual-networks-also-have-access-to-azure-cosmos-account"></a>对等互连的虚拟网络是否也有权访问 Azure Cosmos 帐户？ 
只有已添加到 Azure Cosmos 帐户的虚拟网络及其子网才拥有此访问权限。 将对等互连的虚拟网络中的子网添加到帐户之后，对等互连的 VNet 才可以访问该帐户。

### <a name="what-is-the-maximum-number-of-subnets-allowed-to-access-a-single-cosmos-account"></a>最多允许多少个子网访问单个 Cosmos 帐户？ 
目前，一个 Azure Cosmos 帐户最多允许 256 个子网。

### <a name="can-i-enable-access-from-vpn-and-express-route"></a>是否可以启用从 VPN 和 Express Route 进行访问？ 
若要在本地通过 ExpressRoute 访问 Azure Cosmos 帐户，需要启用 Microsoft 对等互连。 创建 IP 防火墙或虚拟网络访问规则后，可以在 Azure Cosmos 帐户 IP 防火墙中添加用于 Microsoft 对等互连的公共 IP 地址，以允许本地服务访问 Azure Cosmos 帐户。 

### <a name="do-i-need-to-update-the-network-security-groups-nsg-rules"></a>是否需要更新网络安全组 (NSG) 规则？ 
NSG 规则用于限制与虚拟网络中子网之间的连接。 将 Azure Cosmos DB 的服务终结点添加到子网时，无需在 NSG 中为 Azure Cosmos 帐户打开出站连接。 

### <a name="are-service-endpoints-available-for-all-vnets"></a>服务终结点是否适用于所有 VNet？
否，只能为 Azure 资源管理器虚拟网络启用服务终结点。 经典虚拟网络不支持服务终结点。

### <a name="can-i-accept-connections-from-within-public-azure-datacenters-when-service-endpoint-access-is-enabled-for-azure-cosmos-db"></a>为 Azure Cosmos DB 启用了服务终结点访问时，我能否“接受从公用 Azure 数据中心内连接”？  
仅当你希望自己的 Azure Cosmos DB 帐户可供其他 Azure 第一方服务（例如 Azure 数据工厂和 Azure 认知搜索）或给定 Azure 区域中部署的任何服务访问时，才需要这样做。

## <a name="next-steps"></a>后续步骤

* 若要为 Azure Cosmos DB 配置防火墙，请参阅[防火墙支持](how-to-configure-firewall.md)一文。
