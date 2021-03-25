---
title: 快速入门：使用 Azure CLI 创建 Azure 专用链接服务
description: 在本快速入门教程中，了解如何使用 Azure CLI 创建 Azure 专用链接服务
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 01/22/2021
ms.author: allensu
ms.openlocfilehash: 76fd959c28203132be4695031d96315f258cf53f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102563024"
---
# <a name="quickstart-create-a-private-link-service-using-azure-cli"></a>快速入门：使用 Azure CLI 创建专用链接服务

开始创建引用你的服务的专用链接服务。  向专用链接授予对 Azure 标准负载均衡器后面部署的服务或资源的访问权限。  服务的用户具有从其虚拟网络进行专用访问的权限。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)] 

- 本快速入门需要 Azure CLI 2.0.28 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。

## <a name="create-a-resource-group"></a>创建资源组

Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。

使用 [az group create](/cli/azure/group#az_group_create) 创建资源组：

* 名为“CreatePrivLinkService-rg”。 
* 在位置“eastus”中。

```azurecli-interactive
  az group create \
    --name CreatePrivLinkService-rg \
    --location eastus2

```

## <a name="create-an-internal-load-balancer"></a>创建内部负载均衡器

在本部分，创建一个虚拟网络和一个内部 Azure 负载均衡器。

### <a name="virtual-network"></a>虚拟网络

在本部分，创建虚拟网络和子网来托管访问专用链接服务的负载均衡器。

使用 [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) 创建虚拟网络：

* 命名为“myVNet”。
* 地址前缀为 10.1.0.0/16。
* 名为“mySubnet”的子网。
* 子网前缀为 10.1.0.0/24。
* 在“CreatePrivLinkService-rg”资源组中。
* eastus2 的位置。
* 在子网上禁用专用链接服务的网络策略。

```azurecli-interactive
  az network vnet create \
    --resource-group CreatePrivLinkService-rg \
    --location eastus2 \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefixes 10.1.0.0/24

```

若要更新子网以禁用专用链接服务网络策略，请使用 [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update)：

```azurecli-interactive
az network vnet subnet update \
    --name mySubnet \
    --resource-group CreatePrivLinkService-rg \
    --vnet-name myVNet \
    --disable-private-link-service-network-policies true
```

### <a name="create-standard-load-balancer"></a>创建标准负载均衡器

本部分详细介绍如何创建和配置负载均衡器的以下组件：

  * 前端 IP 池，用于在负载均衡器上接收传入网络流量。
  * 后端 IP 池，前端池将负载均衡的网络流量发送到此处。
  * 运行状况探测，用于确定后端 VM 实例的运行状况。
  * 负载均衡器规则，用于定义如何将流量分配给 VM。

### <a name="create-the-load-balancer-resource"></a>创建负载均衡器资源

使用 [az network lb create](/cli/azure/network/lb#az-network-lb-create) 创建公共负载均衡器：

* 命名为 myLoadBalancer。
* 前端池命名为 myFrontEnd。
* 后端池命名为 myBackEndPool。
* 与虚拟网络 myVNet 相关联。
* 与后端子网 mySubnet 相关联。

```azurecli-interactive
  az network lb create \
    --resource-group CreatePrivLinkService-rg \
    --name myLoadBalancer \
    --sku Standard \
    --vnet-name myVnet \
    --subnet mySubnet \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool
```

### <a name="create-the-health-probe"></a>创建运行状况探测

运行状况探测会检查所有虚拟机实例，以确保它们可以发送网络流量。 

从负载均衡器中删除未通过探测检查的虚拟机。 解决故障后，虚拟机将重新添加到负载均衡器中。

使用 [az network lb probe create](/cli/azure/network/lb/probe#az-network-lb-probe-create) 创建运行状况探测：

* 监视虚拟机的运行状况。
* 命名为“myHealthProbe”。
* 协议为“TCP”。
* 监视“端口 80”。

```azurecli-interactive
  az network lb probe create \
    --resource-group CreatePrivLinkService-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

### <a name="create-the-load-balancer-rule"></a>创建负载均衡器规则

负载均衡器规则定义：

* 针对传入流量的前端 IP 配置。
* 用于接收流量的后端 IP 池。
* 所需的源和目标端口。 

使用 [az network lb rule create](/cli/azure/network/lb/rule#az-network-lb-rule-create) 创建负载均衡器规则：

* 命名为“myHTTPRule”
* 对前端池“myFrontEnd”中的“端口 80”进行侦听 。
* 使用“端口 80”将负载均衡的网络流量发送到后端地址池“myBackEndPool” 。 
* 使用运行状况探测“myHealthProbe”。
* 协议为“TCP”。
* 空闲超时 15 分钟。
* 启用 TCP 重置。

```azurecli-interactive
  az network lb rule create \
    --resource-group CreatePrivLinkService-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --idle-timeout 15 \
    --enable-tcp-reset true
```

## <a name="create-a-private-link-service"></a>创建专用链接服务

在本部分中，创建使用上一步中创建的 Azure 负载均衡器的专用链接服务。

使用 [az network private-link-service create](/cli/azure/network/private-link-service#az-network-private-link-service-create) 通过标准负载均衡器前端 IP 配置创建专用链接服务：

* 名为 myPrivateLinkService。
* 在虚拟网络“myVNet”中。
* 与标准负载均衡器 myLoadBalancer 和前端配置 myFrontEnd 相关联 。
* 在 eastus2 位置。
 
```azurecli-interactive
az network private-link-service create \
    --resource-group CreatePrivLinkService-rg \
    --name myPrivateLinkService \
    --vnet-name myVNet \
    --subnet mySubnet \
    --lb-name myLoadBalancer \
    --lb-frontend-ip-configs myFrontEnd \
    --location eastus2
```

专用链接服务已创建，可接收流量。 若要查看流量流，请在标准负载均衡器后面配置应用程序。


## <a name="create-private-endpoint"></a>创建专用终结点

在本部分，将专用链接服务映射到专用终结点。 虚拟网络包含用于专用链接服务的专用终结点。 此虚拟网络包含将访问专用链接服务的资源。

### <a name="create-private-endpoint-virtual-network"></a>创建专用终结点虚拟网络

使用 [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) 创建虚拟网络：

* 名为“myVNetPE”。
* 地址前缀为 11.1.0.0/16。
* 名为“mySubnetPE”的子网。
* 子网前缀为 11.1.0.0/24。
* 在“CreatePrivLinkService-rg”资源组中。
* eastus2 的位置。

```azurecli-interactive
  az network vnet create \
    --resource-group CreatePrivLinkService-rg \
    --location eastus2 \
    --name myVNetPE \
    --address-prefixes 11.1.0.0/16 \
    --subnet-name mySubnetPE \
    --subnet-prefixes 11.1.0.0/24
```

若要更新子网以禁用专用终结点网络策略，请使用 [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update)：

```azurecli-interactive
az network vnet subnet update \
    --name mySubnetPE \
    --resource-group CreatePrivLinkService-rg \
    --vnet-name myVNetPE \
    --disable-private-endpoint-network-policies true
```

### <a name="create-endpoint-and-connection"></a>创建终结点和连接

* 使用 [az network private-link-service show](/cli/azure/network/private-link-service#az_network_private_link_service_show) 获取专用链接服务的资源 ID。 此命令将资源 ID 置于变量中以供以后使用。

* 使用 [az network private-endpoint create](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) 在之前创建的虚拟网络中创建专用终结点。

* 名为“MyPrivateEndpoint”。
* 在“CreatePrivLinkService-rg”资源组中。
* 连接名称“myPEconnectiontoPLS”。
* eastus2 的位置。
* 在虚拟网络“myVNetPE”和子网“mySubnetPE”中 。

```azurecli-interactive
  export resourceid=$(az network private-link-service show \
    --name myPrivateLinkService \
    --resource-group CreatePrivLinkService-rg \
    --query id \
    --output tsv)

  az network private-endpoint create \
    --connection-name myPEconnectiontoPLS \
    --name myPrivateEndpoint \
    --private-connection-resource-id $resourceid \
    --resource-group CreatePrivLinkService-rg \
    --subnet mySubnetPE \
    --manual-request false \
    --vnet-name myVNetPE 

```

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组、专用链接服务、负载均衡器和所有相关的资源，使用 [az group delete](/cli/azure/group#az-group-delete) 命令将它们删除。

```azurecli-interactive
  az group delete \
    --name CreatePrivLinkService-rg 
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，请执行以下操作：

* 创建了虚拟网络和内部 Azure 负载均衡器。
* 创建了专用链接服务

若要详细了解 Azure 专用终结点，请继续学习：
> [!div class="nextstepaction"]
> [快速入门：使用 Azure CLI 创建专用终结点](create-private-endpoint-cli.md)
