---
title: 快速入门：创建内部负载均衡器 - Azure CLI
titleSuffix: Azure Load Balancer
description: 本快速入门介绍如何使用 Azure CLI 创建内部负载均衡器
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a load balancer so that I can load balance internal traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/19/2020
ms.author: allensu
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 15060a367bba2d50d7054730321f7f20d4c25e46
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/06/2021
ms.locfileid: "97916671"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-using-azure-cli"></a>快速入门：使用 Azure CLI 创建内部负载均衡器以对 VM 进行负载均衡

使用 Azure CLI 创建内部负载均衡器和三个虚拟机，通过这种方式开始使用 Azure 负载均衡器。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)] 

- 本快速入门需要 Azure CLI 2.0.28 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。

## <a name="create-a-resource-group"></a>创建资源组

Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。

使用 [az group create](/cli/azure/group#az_group_create) 创建资源组：

* 命名为“CreateIntLBQS-rg”。 
* 在位置“eastus”中。

```azurecli-interactive
  az group create \
    --name CreateIntLBQS-rg \
    --location eastus

```
---

# <a name="standard-sku"></a>[**标准 SKU**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>对于生产型工作负载，建议使用标准 SKU 负载均衡器。 有关 SKU 的详细信息，请参阅 [Azure 负载均衡器 SKU](skus.md)。

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal.png" alt-text="在快速入门中创建的标准负载均衡器资源。" border="false":::

## <a name="configure-virtual-network---standard"></a>配置虚拟网络 - 标准

需要先创建支持的虚拟网络资源，然后才能部署 VM 和负载均衡器。

### <a name="create-a-virtual-network"></a>创建虚拟网络

使用 [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) 创建虚拟网络：

* 命名为“myVNet”。
* 地址前缀为 10.1.0.0/16。
* 子网命名为“myBackendSubnet”。
* 子网前缀为 10.1.0.0/24。
* 在“CreateIntLBQS-rg”资源组中。
* eastus 的位置。

```azurecli-interactive
  az network vnet create \
    --resource-group CreateIntLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

### <a name="create-a-public-ip-address"></a>创建公共 IP 地址

使用 [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) 为堡垒主机创建公共 IP：

* 创建名为“myBastionIP”的标准区域冗余公共 IP 地址。
* 在“CreateIntLBQS-rg”中。

```azurecli-interactive
az network public-ip create \
    --resource-group CreateIntLBQS-rg  \
    --name myBastionIP \
    --sku Standard
```
### <a name="create-a-bastion-subnet"></a>创建堡垒子网

使用 [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) 创建堡垒子网：

* 命名为 AzureBastionSubnet。
* 地址前缀为 10.1.1.0/24。
* 在虚拟网络“myVNet”中。
* 在“CreateIntLBQS-rg”资源组中。

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateIntLBQS-rg  \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

### <a name="create-bastion-host"></a>创建堡垒主机

使用 [az network bastion create](/cli/azure/network/bastion#az-network-bastion-create) 创建堡垒主机：

* 命名为 myBastionHost。
* 在“CreateIntLBQS-rg”中。
* 与公共 IP myBastionIP 相关联。
* 与虚拟网络 myVNet 相关联。
* 在位置“eastus”中。

```azurecli-interactive
az network bastion create \
    --resource-group CreateIntLBQS-rg  \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

部署 Azure Bastion 主机需要几分钟时间。


### <a name="create-a-network-security-group"></a>创建网络安全组

对于标准负载均衡器，后端地址中的 VM 需要具有属于网络安全组的网络接口。 

使用 [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create) 创建网络安全组：

* 命名为“myNSG”。
* 在“CreateIntLBQS-rg”资源组中。

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>创建网络安全组规则

使用 [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) 创建网络安全组规则：

* 命名为“myNSGRuleHTTP”。
* 在上一步创建的网络安全组“myNSG”中。
* 在“CreateIntLBQS-rg”资源组中。
* 协议为“(*)”。
* 方向为“入站”。
* 源为“(*)”。
* 目标为“(*)”。
* 目标端口为“端口 80”。
* 访问为“允许”。
* 优先级为“200”。

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreateIntLBQS-rg \
    --nsg-name myNSG \
    --name myNSGRuleHTTP \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

## <a name="create-backend-servers---standard"></a>创建后端服务器 - 标准

在本节中，创建以下项：

* 虚拟机的三个网络接口。
* 三个要用作负载均衡器后端服务器的虚拟机。

### <a name="create-network-interfaces-for-the-virtual-machines"></a>为虚拟机创建网络接口

使用 [az network nic create](/cli/azure/network/nic#az-network-nic-create) 创建三个网络接口：

* 名为 myNicVM1、myNicVM2 和 myNicVM3  。
* 在“CreateIntLBQS-rg”资源组中。
* 在虚拟网络“myVNet”中。
* 在子网“myBackendSubnet”中。
* 在网络安全组“myNSG”中。

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic create \
        --resource-group CreateIntLBQS-rg \
        --name $vmnic \
        --vnet-name myVNet \
        --subnet myBackEndSubnet \
        --network-security-group myNSG
  done
```

### <a name="create-virtual-machines"></a>创建虚拟机

使用 [az vm create](/cli/azure/vm#az-vm-create) 创建虚拟机：

* 命名为“myVM1”、“myVM2”和“myVM3”  。
* 在“CreateIntLBQS-rg”资源组中。
* 附加到网络接口“myNicVM1”、“myNicVM2”和“myNicVM3”  。
* 虚拟机映像 win2019datacenter。
* 位于区域 1、区域 2 和区域 3  。

```azurecli-interactive
  array=(1 2 3)
  for n in "${array[@]}"
  do
    az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM$n \
    --nics myNicVM$n \
    --image win2019datacenter \
    --admin-username azureuser \
    --zone $n \
    --no-wait
  done
```

可能需要花费几分钟时间才能部署 VM。

## <a name="create-standard-load-balancer"></a>创建标准负载均衡器

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
* 与后端子网 myBackendSubnet 相关联。

```azurecli-interactive
  az network lb create \
    --resource-group CreateIntLBQS-rg \
    --name myLoadBalancer \
    --sku Standard \
    --vnet-name myVnet \
    --subnet myBackendSubnet \
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
    --resource-group CreateIntLBQS-rg \
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
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --disable-outbound-snat true \
    --idle-timeout 15 \
    --enable-tcp-reset true
```
>[!NOTE]
>后端池中的虚拟机将不含具有此配置的出站 Internet 连接。 </br> 有关提供出站连接的详细信息，请参阅： </br> **[Azure 中的出站连接](load-balancer-outbound-connections.md)**</br> 用于提供连接的选项： </br> **[仅出站的负载均衡器配置](egress-only.md)** </br> **[什么是虚拟网络 NAT？](../virtual-network/nat-overview.md)**

### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>将虚拟机添加到负载均衡器后端池

使用 [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#az-network-nic-ip-config-address-pool-add) 将虚拟机添加到后端池：

* 在后端地址池“myBackEndPool”中。
* 在“CreateIntLBQS-rg”资源组中。
* 与网络接口 myNicVM1、myNicVM2 和 myNicVM3 关联  。
* 与负载均衡器 myLoadBalancer 关联。

```azurecli-interactive
  array=(VM1 VM2 VM3)
  for vm in "${array[@]}"
  do
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNic$vm \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
  done

```

# <a name="basic-sku"></a>[**基本 SKU**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>对于生产型工作负载，建议使用标准 SKU 负载均衡器。 有关 SKU 的详细信息，请参阅 [Azure 负载均衡器 SKU](skus.md)。

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal-basic.png" alt-text="在快速入门中创建的基本负载均衡器资源。" border="false":::

## <a name="configure-virtual-network---basic"></a>配置虚拟网络 - 基本

需要先创建支持的虚拟网络资源，然后才能部署 VM 和负载均衡器。

### <a name="create-a-virtual-network"></a>创建虚拟网络

使用 [az network vnet create](/cli/azure/network/vnet#az-network-vnet-createt) 创建虚拟网络：

* 命名为“myVNet”。
* 地址前缀为 10.1.0.0/16。
* 子网命名为“myBackendSubnet”。
* 子网前缀为 10.1.0.0/24。
* 在“CreateIntLBQS-rg”资源组中。
* eastus 的位置。

```azurecli-interactive
  az network vnet create \
    --resource-group CreateIntLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

### <a name="create-a-public-ip-address"></a>创建公共 IP 地址

使用 [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) 为堡垒主机创建公共 IP：

* 创建名为“myBastionIP”的标准区域冗余公共 IP 地址。
* 在“CreateIntLBQS-rg”中。

```azurecli-interactive
az network public-ip create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionIP \
    --sku Standard
```
### <a name="create-a-bastion-subnet"></a>创建堡垒子网

使用 [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) 创建堡垒子网：

* 命名为 AzureBastionSubnet。
* 地址前缀为 10.1.1.0/24。
* 在虚拟网络“myVNet”中。
* 在“CreateIntLBQS-rg”资源组中。

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateIntLBQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

### <a name="create-bastion-host"></a>创建堡垒主机

使用 [az network bastion create](/cli/azure/network/bastion#az-network-bastion-create) 创建堡垒主机：

* 命名为 myBastionHost。
* 在“CreateIntLBQS-rg”中。
* 与公共 IP myBastionIP 相关联。
* 与虚拟网络 myVNet 相关联。
* 在位置“eastus”中。

```azurecli-interactive
az network bastion create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

部署 Azure Bastion 主机需要几分钟时间。

### <a name="create-a-network-security-group"></a>创建网络安全组

对于标准负载均衡器，后端地址中的 VM 需要具有属于网络安全组的网络接口。 

使用 [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create) 创建网络安全组：

* 命名为“myNSG”。
* 在“CreateIntLBQS-rg”资源组中。

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>创建网络安全组规则

使用 [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) 创建网络安全组规则：

* 命名为“myNSGRuleHTTP”。
* 在上一步创建的网络安全组“myNSG”中。
* 在“CreateIntLBQS-rg”资源组中。
* 协议为“(*)”。
* 方向为“入站”。
* 源为“(*)”。
* 目标为“(*)”。
* 目标端口为“端口 80”。
* 访问为“允许”。
* 优先级为“200”。

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreateIntLBQS-rg \
    --nsg-name myNSG \
    --name myNSGRuleHTTP \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

## <a name="create-backend-servers---basic"></a>创建后端服务器 - 基本

在本节中，创建以下项：

* 虚拟机的三个网络接口。
* 虚拟机的可用性集
* 三个要用作负载均衡器后端服务器的虚拟机。

### <a name="create-network-interfaces-for-the-virtual-machines"></a>为虚拟机创建网络接口

使用 [az network nic create](/cli/azure/network/nic#az-network-nic-create) 创建三个网络接口：

* 名为 myNicVM1、myNicVM2 和 myNicVM3  。
* 在“CreateIntLBQS-rg”资源组中。
* 在虚拟网络“myVNet”中。
* 在子网“myBackendSubnet”中。
* 在网络安全组“myNSG”中。

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic create \
        --resource-group CreateIntLBQS-rg \
        --name $vmnic \
        --vnet-name myVNet \
        --subnet myBackEndSubnet \
        --network-security-group myNSG
  done
```

### <a name="create-availability-set-for-virtual-machines"></a>创建虚拟机的可用性集

使用 [az vm availability-set create](/cli/azure/vm/availability-set#az-vm-availability-set-create) 创建可用性集：

* 命名为“myAvailabilitySet”。
* 在“CreateIntLBQS-rg”资源组中。
* 位置 eastus。

```azurecli-interactive
  az vm availability-set create \
    --name myAvailabilitySet \
    --resource-group CreateIntLBQS-rg \
    --location eastus 
    
```

### <a name="create-virtual-machines"></a>创建虚拟机

使用 [az vm create](/cli/azure/vm#az-vm-create) 创建虚拟机：

* 命名为“myVM1”、“myVM2”和“myVM3”  。
* 在“CreateIntLBQS-rg”资源组中。
* 附加到网络接口“myNicVM1”、“myNicVM2”和“myNicVM3”  。
* 虚拟机映像 win2019datacenter。
* 位于 myAvailabilitySet。


```azurecli-interactive
  array=(1 2 3)
  for n in "${array[@]}"
  do
    az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM$n \
    --nics myNicVM$n \
    --image win2019datacenter \
    --admin-username azureuser \
    --availability-set myAvailabilitySet \
    --no-wait
  done
```
可能需要花费几分钟时间才能部署 VM。

## <a name="create-basic-load-balancer"></a>创建基本负载均衡器

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
* 与后端子网 myBackendSubnet 相关联。

```azurecli-interactive
  az network lb create \
    --resource-group CreateIntLBQS-rg \
    --name myLoadBalancer \
    --sku Basic \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
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
    --resource-group CreateIntLBQS-rg \
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

```azurecli-interactive
  az network lb rule create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --idle-timeout 15 
```
### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>将虚拟机添加到负载均衡器后端池

使用 [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#az-network-nic-ip-config-address-pool-add) 将虚拟机添加到后端池：

* 在后端地址池“myBackEndPool”中。
* 在“CreateIntLBQS-rg”资源组中。
* 与网络接口 myNicVM1、myNicVM2 和 myNicVM3 关联  。
* 与负载均衡器 myLoadBalancer 关联。

```azurecli-interactive
  array=(VM1 VM2 VM3)
  for vm in "${array[@]}"
  do
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNic$vm \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
  done

```
---

## <a name="test-the-load-balancer"></a>测试负载均衡器

### <a name="create-test-virtual-machine"></a>创建测试虚拟机

使用 [az network nic create](/cli/azure/network/nic#az-network-nic-create) 创建网络接口：

* 命名为 myNicTestVM。
* 在“CreateIntLBQS-rg”资源组中。
* 在虚拟网络“myVNet”中。
* 在子网“myBackendSubnet”中。
* 在网络安全组“myNSG”中。

```azurecli-interactive
  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicTestVM \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
使用 [az vm create](/cli/azure/vm#az-vm-create) 创建虚拟机：

* 命名为 myTestVM。
* 在“CreateIntLBQS-rg”资源组中。
* 附加到网络接口“myNicTestVM”。
* 虚拟机映像 Win2019Datacenter。

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myTestVM \
    --nics myNicTestVM \
    --image Win2019Datacenter \
    --admin-username azureuser \
    --no-wait
```
部署虚拟机需要几分钟时间。

## <a name="install-iis"></a>安装 IIS

使用 [az vm extension set](/cli/azure/vm/extension#az_vm_extension_set) 将 IIS 安装在虚拟机上，并将默认网站设置为计算机名。

```azurecli-interactive
  array=(myVM1 myVM2 myVM3)
    for vm in "${array[@]}"
    do
     az vm extension set \
       --publisher Microsoft.Compute \
       --version 1.8 \
       --name CustomScriptExtension \
       --vm-name $vm \
       --resource-group CreateIntLBQS-rg \
       --settings '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
  done

```

### <a name="test"></a>测试

1. [登录](https://portal.azure.com) Azure 门户。

2. 在“概述”屏幕上找到负载均衡器的专用 IP 地址。 在左侧菜单中选择“所有服务”，选择“所有资源”，然后选择“myLoadBalancer”  。

3. 在 myLoadBalancer 的“概览”中，记下或复制专用 IP 地址旁边的地址  。

4. 在左侧菜单中选择“所有服务”，选择“所有资源”，然后在资源列表中，选择“CreateIntLBQS-rg”资源组中的“myTestVM”   。

5. 在“概述”页上，选择“连接”，然后选择“Bastion”  。

6. 输入在 VM 创建过程中输入的用户名和密码。

7. 在 myTestVM 中打开 Internet Explorer 。

8. 将上一步骤的 IP 地址输入到浏览器的地址栏。 IIS Web 服务器的默认页会显示在浏览器上。

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="创建标准的内部负载均衡器" border="true":::
   
要查看所有三个 VM 中的负载均衡器分配流量，可以自定义每个 VM 的 IIS Web 服务器的默认页面，然后从客户端计算机强制刷新 Web 浏览器。

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组、负载均衡器和所有相关的资源，使用 [az group delete](/cli/azure/group#az-group-delete) 命令将它们删除。

```azurecli-interactive
  az group delete \
    --name CreateIntLBQS-rg
```

## <a name="next-steps"></a>后续步骤

在本快速入门：

* 你创建了一个标准或公共负载均衡器
* 附加了虚拟机。 
* 配置了负载均衡器流量规则和运行状况探测。
* 测试了负载均衡器。

若要详细了解 Azure 负载均衡器，请继续学习：
> [!div class="nextstepaction"]
> [什么是 Azure 负载均衡器？](load-balancer-overview.md)