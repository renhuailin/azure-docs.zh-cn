---
title: 设置具有专用链接的专用终结点
description: 在容器注册表上设置专用终结点，并实现在本地虚拟网络中通过专用链接进行访问的功能。 专用链接访问是高级服务层级的一项功能。
ms.topic: article
ms.date: 07/14/2021
ms.openlocfilehash: 25a45f0e1f4115fce623deef919368ecdf479ead
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114471456"
---
# <a name="connect-privately-to-an-azure-container-registry-using-azure-private-link"></a>使用 Azure 专用链接以私密方式连接到 Azure 容器注册表

将虚拟网络专用 IP 地址分配给注册表终结点并使用[ Azure 专用链接](../private-link/private-link-overview.md)，从而限制对注册表的访问。 虚拟网络上的客户端与注册表专用终结点之间的网络流量将穿过虚拟网络以及 Azure 主干网络上的专用链接，因此不会从公共 internet 公开。 专用链接还允许通过 [Azure ExpressRoute](../expressroute/expressroute-introduction.MD) 专用对等互连或 [VPN 网关](../vpn-gateway/vpn-gateway-about-vpngateways.md)，从本地访问专用注册表。

可以为注册表专用终结点[配置 DNS 设置](../private-link/private-endpoint-overview.md#dns-configuration)，以便将这些设置解析为注册表的已分配的专用 IP 地址。 使用 DNS 配置时，网络中的客户端和服务可以继续按注册表的完全限定的域名（如 myregistry.azurecr.io）来访问注册表。 

本文介绍如何使用 Azure 门户（推荐）或 Azure CLI 为注册表配置专用终结点。 此功能在“高级”容器注册表服务层级中可用。 有关注册表服务层级和限制的信息，请参阅 [Azure 容器注册表层级](container-registry-skus.md)。

[!INCLUDE [container-registry-scanning-limitation](../../includes/container-registry-scanning-limitation.md)]

> [!NOTE]
> 目前，最多可以为注册表设置 10 个专用终结点。 

## <a name="prerequisites"></a>先决条件

* 要在其中设置专用终结点的虚拟网络和子网。 如果需要，可[创建新虚拟网络和子网](../virtual-network/quick-create-portal.md)。
* 为了进行测试，建议在虚拟网络中设置 VM。 有关创建测试虚拟机以访问注册表的步骤，请参阅[创建启用了 Docker 的虚拟机](container-registry-vnet.md#create-a-docker-enabled-virtual-machine)。 
* 若要使用本文中所述的 Azure CLI 步骤，建议安装 Azure CLI 版本 2.6.0 或更高版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli]。 或是在 [Azure Cloud Shell](../cloud-shell/quickstart.md) 中运行。
* 如果还没有容器注册表，请创建一个（需要的高级层级），并[导入](container-registry-import-images.md)示例公共映像，如来自 Microsoft 容器注册表的 `mcr.microsoft.com/hello-world`。 例如，使用 [Azure 门户][quickstart-portal]或 [Azure CLI][quickstart-cli] 创建注册表。

### <a name="register-container-registry-resource-provider"></a>注册容器注册表资源提供程序

若要使用其他 Azure 订阅或租户中的专用链接配置注册表访问，需要在该订阅中为 Azure 容器注册表注册[资源提供程序](../azure-resource-manager/management/resource-providers-and-types.md)。 使用 Azure 门户、Azure CLI 或其他工具。

示例：

```azurecli
az account set --subscription <Name or ID of subscription of private link>

az provider register --namespace Microsoft.ContainerRegistry
``` 

## <a name="set-up-private-endpoint---portal-recommended"></a>设置专用终结点 - 门户（推荐）

在创建注册表时设置专用终结点，或向现有注册表添加专用终结点。 

### <a name="create-a-private-endpoint---new-registry"></a>创建专用终结点 - 新注册表

1. 在门户中创建注册表时，请在“基本信息”选项卡上的“SKU”中，选择“高级”。
1. 选择“网络”选项卡。
1. 在“网络连接”中，选择“专用终结点” > “+ 添加”。
1. 输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | 订阅 | 选择订阅。 |
    | 资源组 | 输入现有组名称或创建一个新组。|
    | 名称 | 输入唯一名称。 |
    | 注册表子资源 |选择“注册表”|
    | **网络** | |
    | 虚拟网络| 选择专用终结点的虚拟网络。 例如：myDockerVMVNET。 |
    | 子网 | 选择专用终结点的子网。 例如：myDockerVMSubnet。 |
    |**专用 DNS 集成**||
    |与专用 DNS 区域集成 |请选择“是”。 |
    |专用 DNS 区域 |选择“(新) privatelink.azurecr.io” |
    |||
1. 配置其余注册表设置，然后选择“查看 + 创建”。
  
:::image type="content" source="media/container-registry-private-link/private-link-create-portal.png" alt-text="通过专用终结点创建注册表":::



专用链接现已配置，可供使用。

### <a name="create-a-private-endpoint---existing-registry"></a>创建专用终结点 - 现有注册表

1. 在门户中，导航到容器注册表。
1. 在“设置”下选择“网络” 。
1. 在“专用终结点”选项卡上，选择“+ 专用终结点”。
    :::image type="content" source="media/container-registry-private-link/private-endpoint-existing-registry.png" alt-text="向注册表添加专用终结点":::

1. 在“基本信息”中，输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    | **项目详细信息** | |
    | 订阅 | 选择订阅。 |
    | 资源组 | 输入现有组名称或创建一个新组。|
    | **实例详细信息** |  |
    | 名称 | 输入名称。 |
    |区域|选择区域。|
    |||
1. 在完成时选择“下一步:资源”。
1. 输入或选择以下信息：

    | 设置 | 值 |
    | ------- | ----- |
    |连接方法  | 对于本例，选择“连接到我的目录中的 Azure 资源”。|
    | 订阅| 选择订阅。 |
    | 资源类型 | 选择“Microsoft.ContainerRegistry/registries”。 |
    | 资源 |选择注册表的名称|
    |目标子资源 |选择“注册表”|
    |||
1. 在完成时选择“下一步:配置”。
1. 输入或选择信息：

    | 设置 | 值 |
    | ------- | ----- |
    |**联网**| |
    | 虚拟网络| 选择专用终结点的虚拟网络 |
    | 子网 | 选择专用终结点的子网 |
    |**专用 DNS 集成**||
    |与专用 DNS 区域集成 |请选择“是”。 |
    |专用 DNS 区域 |选择“(新) privatelink.azurecr.io” |
    |||

1. 选择“查看 + 创建”。 随后你会转到“查看 + 创建”页，Azure 将在此页面验证配置。 
1. 看到“验证通过”消息时，选择“创建” 。

### <a name="confirm-endpoint-configuration"></a>确认终结点配置

创建好专用终结点之后，专用区域中的 DNS 设置会随门户中的“专用终结点”设置一起显示：

1. 在门户中，导航到容器注册表，选择“设置”>“网络”。
1. 在“专用终结点”选项卡上，选择创建的专用这节点。 
1. 选择“DNS 配置”。
1. 查看链接设置和自定义 DNS 设置。

:::image type="content" source="media/container-registry-private-link/private-endpoint-overview.png" alt-text="门户中的终结点 DNS 设置":::
## <a name="set-up-private-endpoint---cli"></a>设置专用终结点连接 - CLI

本文中的 Azure CLI 示例使用以下环境变量。 需要现有容器注册表、虚拟网络和子网的名称才能设置专用终结点。 替换为适合于环境的值。 所有示例都针对 Bash shell 进行格式设置：

```bash
REGISTRY_NAME=<container-registry-name>
REGISTRY_LOCATION=<container-registry-location> # Azure region such as westeurope where registry created
RESOURCE_GROUP=<resource-group-name> # Resource group for your existing virtual network and subnet
NETWORK_NAME=<virtual-network-name>
SUBNET_NAME=<subnet-name>
```
### <a name="disable-network-policies-in-subnet"></a>在子网中禁用网络策略

[禁用网络策略](../private-link/disable-private-endpoint-network-policy.md)，如用于专用终结点的子网中的网络安全组。 使用 [az network vnet subnet update][az-network-vnet-subnet-update] 更新子网配置：

```azurecli
az network vnet subnet update \
 --name $SUBNET_NAME \
 --vnet-name $NETWORK_NAME \
 --resource-group $RESOURCE_GROUP \
 --disable-private-endpoint-network-policies
```

### <a name="configure-the-private-dns-zone"></a>配置专用 DNS 区域

为专用 Azure 容器注册表域创建[专用 Azure DNS 区域](../dns/private-dns-privatednszone.md)。 在后续步骤中，你将在此 DNS 区域中为你的注册表域创建 DNS 记录。 有关详细信息，请参阅本文后面的 [DNS 配置选项](#dns-configuration-options)。

若要使用专用区域替代 Azure 容器注册表的默认 DNS 解析，区域必须命名为 privatelink.azurecr.io。 运行以下 [az network private-dns zone create][az-network-private-dns-zone-create] 命令以创建专用区域：

```azurecli
az network private-dns zone create \
  --resource-group $RESOURCE_GROUP \
  --name "privatelink.azurecr.io"
```

### <a name="create-an-association-link"></a>创建关联链接

运行 [az network private-dns link vnet create][az-network-private-dns-link-vnet-create] 以将专用区域与虚拟网络相关联。 此示例会创建名为 myDNSLink的链接。

```azurecli
az network private-dns link vnet create \
  --resource-group $RESOURCE_GROUP \
  --zone-name "privatelink.azurecr.io" \
  --name MyDNSLink \
  --virtual-network $NETWORK_NAME \
  --registration-enabled false
```

### <a name="create-a-private-registry-endpoint"></a>创建专用注册表终结点

在此部分中，将在虚拟网络中创建注册表的专用终结点。 首先，获取注册表的资源 ID：

```azurecli
REGISTRY_ID=$(az acr show --name $REGISTRY_NAME \
  --query 'id' --output tsv)
```

运行 [az network private-endpoint create][az-network-private-endpoint-create] 命令以创建注册表的专用终结点。

下面的示例创建终结点 myPrivateEndpoint 和服务连接 myConnection。 若要为终结点指定容器注册表资源，请传递 `--group-ids registry`：

```azurecli
az network private-endpoint create \
    --name myPrivateEndpoint \
    --resource-group $RESOURCE_GROUP \
    --vnet-name $NETWORK_NAME \
    --subnet $SUBNET_NAME \
    --private-connection-resource-id $REGISTRY_ID \
    --group-ids registry \
    --connection-name myConnection
```

### <a name="get-endpoint-ip-configuration"></a>获取终结点 IP 配置

若要配置 DNS 记录，请获取专用终结点的 IP 配置。 在此示例中，与专用终结点的网络接口关联的是容器注册表的两个专用 IP 地址：一个用于注册表本身，另一个用于注册表的数据终结点。 如果注册表是异地复制的，每个副本将关联一个额外的 IP 地址。

首先，运行 [az network private-endpoint show][az-network-private-endpoint-show] 以查询网络接口 ID 的专用终结点：

```azurecli
NETWORK_INTERFACE_ID=$(az network private-endpoint show \
  --name myPrivateEndpoint \
  --resource-group $RESOURCE_GROUP \
  --query 'networkInterfaces[0].id' \
  --output tsv)
```

以下 [az network nic show][az-network-nic-show] 命令获取容器注册表和注册表数据终结点的专用 IP 地址和 FQDN：

```azurecli
REGISTRY_PRIVATE_IP=$(az network nic show \
  --ids $NETWORK_INTERFACE_ID \
  --query "ipConfigurations[?privateLinkConnectionProperties.requiredMemberName=='registry'].privateIpAddress" \
  --output tsv)

DATA_ENDPOINT_PRIVATE_IP=$(az network nic show \
  --ids $NETWORK_INTERFACE_ID \
  --query "ipConfigurations[?privateLinkConnectionProperties.requiredMemberName=='registry_data_$REGISTRY_LOCATION'].privateIpAddress" \
  --output tsv)

# An FQDN is associated with each IP address in the IP configurations

REGISTRY_FQDN=$(az network nic show \
  --ids $NETWORK_INTERFACE_ID \
  --query "ipConfigurations[?privateLinkConnectionProperties.requiredMemberName=='registry'].privateLinkConnectionProperties.fqdns" \
  --output tsv)

DATA_ENDPOINT_FQDN=$(az network nic show \
  --ids $NETWORK_INTERFACE_ID \
  --query "ipConfigurations[?privateLinkConnectionProperties.requiredMemberName=='registry_data_$REGISTRY_LOCATION'].privateLinkConnectionProperties.fqdns" \
  --output tsv)
```

#### <a name="additional-endpoints-for-geo-replicas"></a>异地副本的其他终结点

如果注册表是[异地复制](container-registry-geo-replication.md)，请查询每个注册表副本的附加数据终结点。 例如，在 eastus 区域： 

```azurecli
REPLICA_LOCATION=eastus
GEO_REPLICA_DATA_ENDPOINT_PRIVATE_IP=$(az network nic show \
  --ids $NETWORK_INTERFACE_ID \
  --query "ipConfigurations[?privateLinkConnectionProperties.requiredMemberName=='registry_data_$REPLICA_LOCATION'].privateIpAddress" \
  --output tsv) 

GEO_REPLICA_DATA_ENDPOINT_FQDN=$(az network nic show \
  --ids $NETWORK_INTERFACE_ID \
  --query "ipConfigurations[?privateLinkConnectionProperties.requiredMemberName=='registry_data_$REPLICA_LOCATION'].privateLinkConnectionProperties.fqdns" \
  --output tsv)
```
### <a name="create-dns-records-in-the-private-zone"></a>在专用区域中创建 DNS 记录

以下命令在专用区域中为注册表终结点及其数据终结点创建 DNS 记录。 例如，如果在 westeurope 区域中有一个名为 myregistry 的注册表，则终结点名称是 `myregistry.azurecr.io` 和 `myregistry.westeurope.data.azurecr.io`。 

首先运行 [az network private-dns record-set a create][az-network-private-dns-record-set-a-create]，为注册表终结点和数据终结点创建空的 A 记录集：

```azurecli
az network private-dns record-set a create \
  --name $REGISTRY_NAME \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP

# Specify registry region in data endpoint name
az network private-dns record-set a create \
  --name ${REGISTRY_NAME}.${REGISTRY_LOCATION}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP
```

运行 [az network private-dns record-set a add-record][az-network-private-dns-record-set-a-add-record] 命令，为注册表终结点和数据终结点创建 A 记录：

```azurecli
az network private-dns record-set a add-record \
  --record-set-name $REGISTRY_NAME \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP \
  --ipv4-address $REGISTRY_PRIVATE_IP

# Specify registry region in data endpoint name
az network private-dns record-set a add-record \
  --record-set-name ${REGISTRY_NAME}.${REGISTRY_LOCATION}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP \
  --ipv4-address $DATA_ENDPOINT_PRIVATE_IP
```

#### <a name="additional-records-for-geo-replicas"></a>异地副本的附加记录

如果注册表是异地复制的，请为每个副本创建附加 DNS 设置。 继续以 eastus 区域为例：

```azurecli
az network private-dns record-set a create \
  --name ${REGISTRY_NAME}.${REPLICA_LOCATION}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP

az network private-dns record-set a add-record \
  --record-set-name ${REGISTRY_NAME}.${REPLICA_LOCATION}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP \
  --ipv4-address $GEO_REPLICA_DATA_ENDPOINT_PRIVATE_IP
```

专用链接现已配置，可供使用。

## <a name="disable-public-access"></a>禁用公共访问

在许多情况下，禁用从公用网络进行的注册表访问。 此配置会阻止虚拟网络外部的客户端访问注册表终结点。 

### <a name="disable-public-access---portal"></a>禁用公共访问 - 门户

1. 在门户中，导航到容器注册表，选择“设置”>“网络”。
1. 在“公共访问”选项卡上的“允许公用网络访问”中，选择“禁用”  。 再选择“保存”。

### <a name="disable-public-access---cli"></a>禁用公共访问 - CLI

若要使用 Azure CLI 禁用公共访问，请运行 [az acr update][az-acr-update]，并将 `--public-network-enabled` 设置为 `false`。 

```azurecli
az acr update --name $REGISTRY_NAME --public-network-enabled false
```

## <a name="validate-private-link-connection"></a>验证专用链接连接

应该验证专用终结点的子网中的资源是否可以通过专用 IP 地址连接到注册表，以及它们是否具有正确的专用 DNS 区域集成。

若要验证专用链接连接，请连接到虚拟网络中设置的虚拟机。

运行 `nslookup` 或 `dig` 等实用工具，以便通过专用链接查找注册表的 IP 地址。 例如：

```bash
dig $REGISTRY_NAME.azurecr.io
```

示例输出演示子网的地址空间中的注册表 IP 地址：

```console
[...]
; <<>> DiG 9.11.3-1ubuntu1.13-Ubuntu <<>> myregistry.azurecr.io
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 52155
;; flags: qr rd ra; QUERY: 1, ANSWER: 2, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;myregistry.azurecr.io.         IN      A

;; ANSWER SECTION:
myregistry.azurecr.io.  1783    IN      CNAME   myregistry.privatelink.azurecr.io.
myregistry.privatelink.azurecr.io. 10 IN A      10.0.0.7

[...]
```

将此结果与公共终结点上相同注册表的 `dig` 输出中的公共 IP 地址进行比较：

```console
[...]
;; ANSWER SECTION:
myregistry.azurecr.io.  2881    IN  CNAME   myregistry.privatelink.azurecr.io.
myregistry.privatelink.azurecr.io. 2881 IN CNAME xxxx.xx.azcr.io.
xxxx.xx.azcr.io.    300 IN  CNAME   xxxx-xxx-reg.trafficmanager.net.
xxxx-xxx-reg.trafficmanager.net. 300 IN CNAME   xxxx.westeurope.cloudapp.azure.com.
xxxx.westeurope.cloudapp.azure.com. 10  IN A 20.45.122.144

[...]
```

### <a name="registry-operations-over-private-link"></a>针对专用链接的注册表操作

还应验证是否可从网络中的虚拟机执行注册表操作。 建立与虚拟机的 SSH 连接，并运行 [az acr login][az-acr-login] 以登录注册表。 根据 VM 配置，可能需要将 `sudo` 作为以下命令的前缀。

```bash
az acr login --name $REGISTRY_NAME
```

执行注册表操作（如 `docker pull`），以从注册表拉取示例映像。 将 `hello-world:v1` 替换为适用于注册表的映像和标记，并以注册表登录服务器名称（全部小写）作为前缀：

```bash
docker pull myregistry.azurecr.io/hello-world:v1
``` 

Docker 已成功将映像拉取到 VM。

## <a name="manage-private-endpoint-connections"></a>管理专用终结点连接

使用 Azure 门户或使用 [az acr private-endpoint-connection][az-acr-private-endpoint-connection] 命令组中的命令管理注册表的专用终结点连接。 操作包括批准、删除、列出、拒绝注册表专用终结点连接或显示其详细信息。

例如，若要列出注册表的专用终结点连接，请运行 [az acr private-endpoint-connection list][az-acr-private-endpoint-connection-list] 命令。 例如：

```azurecli
az acr private-endpoint-connection list \
  --registry-name $REGISTRY_NAME 
```

使用本文中的步骤设置专用终结点连接时，注册表会自动接受来自在注册表上拥有 Azure RBAC 权限的客户端和服务的连接。 可以设置终结点以要求手动批准连接。 有关如何批准和拒绝专用终结点连接的信息，请参阅[管理专用终结点连接](../private-link/manage-private-endpoint.md)。

> [!IMPORTANT]
> 目前，如果从注册表中删除专用终结点，则可能还需要删除从虚拟网络到专用区域的链接。 如果未删除该链接，你可能会看到类似于 `unresolvable host` 的错误。

## <a name="dns-configuration-options"></a>DNS 配置选项

本示例中的专用终结点会集成与基本虚拟网络关联的专用 DNS 区域。 此安装程序直接使用 Azure 提供的 DNS 服务将注册表的公共 FQDN 解析为其在虚拟网络中的专用 IP 地址。 

专用链接支持其他使用专用区域的 DNS 配置方案，包括使用自定义 DNS 解决方案。 例如，你可能有一个自定义 DNS 解决方案，该方案部署在虚拟网络中，或部署在使用 VPN 网关或 Azure ExpressRoute 连接到虚拟网络的本地网络中。 

若要在这些情况下将注册表的公共 FQDN 解析为专用 IP 地址，需要配置到 Azure DNS 服务 (168.63.129.16) 的服务器级别转发器。 确切的配置选项和步骤取决于现有的网络和 DNS。 有关示例，请参阅[了解 Azure 专用终结点 DNS 配置](../private-link/private-endpoint-dns.md)。

> [!IMPORTANT]
> 如果为了高可用性，你在多个区域中创建了专用终结点，我们建议你在每个区域中使用单独的资源组，并将虚拟网络和关联的专用 DNS 区域放置在其中。 此配置还可防止由于共享同一专用 DNS 区域而导致 DNS 解析不可预测的问题。

### <a name="manually-configure-dns-records"></a>手动配置 DNS 记录

在某些情况下，你可能需要在专用区域中手动配置 DNS 记录，而非使用 Azure 提供的专用区域。 务必创建对应于以下每个终结点的记录：注册表终结点、注册表的数据终结点，以及任何其他区域副本的数据终结点。 如果所有记录均未配置，则可能无法访问注册表。

> [!IMPORTANT]
> 如果以后添加新副本，则需要为该区域中的数据终结点手动添加新的 DNS 记录。 例如，如果在 northeurope 位置创建 myregistry 的副本，请为 `myregistry.northeurope.data.azurecr.io` 添加记录。

创建 DNS 记录所需的 FQDN 和专用 IP 地址与该专用终结点的网络接口相关联。 你可使用 Azure 门户或 Azure CLI 获取这些信息。

* 在门户中，导航到专用终结点，然后选择“DNS 配置”。 
* 使用 Azure CLI，运行 [az network nic show][az-network-nic-show] 命令。 有关示例命令，请参阅本文前面的[获取终结点 IP 配置](#get-endpoint-ip-configuration)。

创建 DNS 记录后，请确保注册表 FQDN 正确解析为各自的专用 IP 地址。

## <a name="clean-up-resources"></a>清理资源

若要在门户中清理资源，请导航到资源组。 加载资源组后，单击“删除资源组”以删除该资源组和其中存储的资源。

如果在同一资源组中创建了所有 Azure 资源，并且不再需要这些资源，则可以选择使用单个 [az group delete](/cli/azure/group) 命令删除资源：

```azurecli
az group delete --name $RESOURCE_GROUP
```

## <a name="next-steps"></a>后续步骤

* 若要了解有关专用链接的更多信息，请参阅 [Azure 专用链接](../private-link/private-link-overview.md)文档。

* 若要验证路由到专用终结点的虚拟网络中的 DNS 设置，请使用 `--vnet` 参数运行 [az acr check-health](/cli/azure/acr#az_acr_check_health) 命令。 有关详细信息，请参阅[检查 Azure 容器注册表的运行状况](container-registry-check-health.md) 

* 如需设置从客户端防火墙后访问注册表的规则，请参阅[配置从防火墙后访问 Azure 容器注册表的规则](container-registry-firewall-access-rules.md)。

* [排查 Azure 专用终结点连接问题](../private-link/troubleshoot-private-endpoint-connectivity.md)

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-acr-repository-show]: /cli/azure/acr/repository#az_acr_repository_show
[az-acr-repository-list]: /cli/azure/acr/repository#az_acr_repository_list
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-private-endpoint-connection]: /cli/azure/acr/private-endpoint-connection
[az-acr-private-endpoint-connection-list]: /cli/azure/acr/private-endpoint-connection#az_acr_private-endpoint-connection-list
[az-acr-private-endpoint-connection-approve]: /cli/azure/acr/private-endpoint-connection#az_acr_private_endpoint_connection_approve
[az-acr-update]: /cli/azure/acr#az_acr_update
[az-group-create]: /cli/azure/group
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-vm-create]: /cli/azure/vm#az_vm_create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az_network_vnet_subnet_show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az_network_vnet_subnet_update
[az-network-vnet-list]: /cli/azure/network/vnet/#az_network_vnet_list
[az-network-private-endpoint-create]: /cli/azure/network/private-endpoint#az_network_private_endpoint_create
[az-network-private-endpoint-show]: /cli/azure/network/private-endpoint#az_network_private_endpoint_show
[az-network-private-dns-zone-create]: /cli/azure/network/private-dns/zone#az_network_private_dns_zone_create
[az-network-private-dns-link-vnet-create]: /cli/azure/network/private-dns/link/vnet#az_network_private_dns_link_vnet_create
[az-network-private-dns-record-set-a-create]: /cli/azure/network/private-dns/record-set/a#az_network_private_dns_record_set_a_create
[az-network-private-dns-record-set-a-add-record]: /cli/azure/network/private-dns/record-set/a#az_network_private_dns_record_set_a_add_record
[az-network-nic-show]: /cli/azure/network/nic#az_network_nic_show
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
