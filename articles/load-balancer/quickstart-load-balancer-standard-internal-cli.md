---
title: 快速入门：创建内部负载均衡器 - Azure CLI
titleSuffix: Azure Load Balancer
description: 本快速入门介绍如何使用 Azure CLI 创建内部负载均衡器。
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/19/2020
ms.author: allensu
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 2db30024b26352bcc038d606bcdc760b6350d413
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788828"
---
# <a name="quickstart-create-an-internal-load-balancer-by-using-the-azure-cli"></a>快速入门：使用 Azure CLI 创建内部负载均衡器

使用 Azure CLI 创建内部负载均衡器和三个虚拟机，通过这种方式开始使用 Azure 负载均衡器。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)] 

本快速入门需要 Azure CLI 2.0.28 或更高版本。 如果你使用的是 Azure Cloud Shell，则表示已安装最新版本。

## <a name="create-a-resource-group"></a>创建资源组

Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。

使用 [az group create](/cli/azure/group#az_group_create) 创建资源组。 将资源组命名为 **CreateIntLBQS-rg**，将位置指定为 **eastus**。

```azurecli-interactive
  az group create \
    --name CreateIntLBQS-rg \
    --location eastus

```

---
# <a name="standard-sku"></a>[**标准 SKU**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>对于生产型工作负载，建议使用标准 SKU 负载均衡器。 有关 sku 的详细信息，请参阅 [Azure 负载均衡器 SKU](skus.md)。

在本部分，你将创建一个负载均衡器来对虚拟机进行负载均衡。 创建内部负载均衡器时，虚拟网络配置为负载均衡器的网络。 下图显示在本快速入门中创建的资源：

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal.png" alt-text="在快速入门中创建的标准负载均衡器资源。" border="false":::

### <a name="configure-the-virtual-network"></a>配置虚拟网络

需要先创建支持的虚拟网络资源，然后才能部署 VM 和负载均衡器。

#### <a name="create-a-virtual-network"></a>创建虚拟网络

使用 [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) 创建虚拟网络。 指定下列各项：

* 名称为 **myVNet**
* 地址前缀为 **10.1.0.0/16**
* 子网名为 **myBackendSubnet**
* 子网前缀为 **10.1.0.0/24**
* 在 **CreateIntLBQS-rg** 资源组中
* 位置为 **eastus**

```azurecli-interactive
  az network vnet create \
    --resource-group CreateIntLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

#### <a name="create-a-public-ip-address"></a>创建公共 IP 地址

使用 [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) 为 Azure Bastion 主机创建公共 IP 地址。 指定下列各项：

* 创建名为 **myBastionIP** 的标准区域冗余公共 IP 地址
* 在“CreateIntLBQS-rg”中

```azurecli-interactive
az network public-ip create \
    --resource-group CreateIntLBQS-rg  \
    --name myBastionIP \
    --sku Standard
```
#### <a name="create-an-azure-bastion-subnet"></a>创建 Azure Bastion 子网

使用 [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) 创建子网。 指定下列各项：

* 名称为 **AzureBastionSubnet**
* 地址前缀为 **10.1.1.0/24**
* 在虚拟网络 **myVNet** 中
* 在资源组 **CreateIntLBQS-rg** 中

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateIntLBQS-rg  \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

#### <a name="create-an-azure-bastion-host"></a>创建 Azure Bastion 主机

使用 [az network bastion create](/cli/azure/network/bastion#az_network_bastion_create) 创建主机。 指定下列各项：

* 命名为 myBastionHost
* 在“CreateIntLBQS-rg”中
* 与公共 IP **myBastionIP** 相关联
* 与虚拟网络 **myVNet** 相关联
* 在 **eastus** 位置

```azurecli-interactive
az network bastion create \
    --resource-group CreateIntLBQS-rg  \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

部署 Azure Bastion 主机需要几分钟时间。

#### <a name="create-a-network-security-group"></a>创建网络安全组

对于标准负载均衡器，请确保 VM 具有属于网络安全组的网络接口。 使用 [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create) 创建网络安全组。 指定下列各项：

* 名称为 **myNSG**
* 在资源组 **CreateIntLBQS-rg** 中

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

#### <a name="create-a-network-security-group-rule"></a>创建网络安全组规则

使用 [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) 创建网络安全组规则。 指定下列各项：

* 名称为 **myNSGRuleHTTP**
* 在上一步骤创建的网络安全组 **myNSG** 中
* 在资源组 **CreateIntLBQS-rg** 中
* 协议为 **(*)**
* 方向为“入站”
* 源为 **(*)**
* 目标为 **(*)**
* 目标端口为“端口 80”
* 访问为“允许”
* 优先级为 **200**

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

### <a name="create-back-end-servers"></a>创建后端服务器

在本节中，创建以下项：

* 虚拟机的三个网络接口。
* 三个用作负载均衡器服务器的虚拟机。

#### <a name="create-network-interfaces-for-the-virtual-machines"></a>为虚拟机创建网络接口

使用 [az network nic create](/cli/azure/network/nic#az_network_nic_create) 创建三个网络接口。 指定下列各项：

* 名称为 **myNicVM1**、**myNicVM2** 和 **myNicVM3**
* 在资源组 **CreateIntLBQS-rg** 中
* 在虚拟网络 **myVNet** 中
* 在子网 **myBackendSubnet** 中
* 在网络安全组 **myNSG** 中

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

#### <a name="create-the-virtual-machines"></a>创建虚拟机

使用 [az vm create](/cli/azure/vm#az_vm_create) 创建虚拟机。 指定下列各项：

* 名称为 **myVM1**、**myVM2** 和 **myVM3**
* 在资源组 **CreateIntLBQS-rg** 中
* 附加到网络接口 **myNicVM1**、**myNicVM2** 和 **myNicVM3**
* 虚拟机映像为 **win2019datacenter**
* 在 **区域 1**、**区域 2** 和 **区域 3** 中

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

部署 VM 可能需要花费几分钟时间。

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]


### <a name="create-the-load-balancer"></a>创建负载均衡器

本部分详细介绍如何创建和配置负载均衡器的以下组件：

* 一个 IP 池，用于在负载均衡器上接收传入网络流量。
* 另一个后端 IP 池，第一个池将负载均衡的网络流量发送到其中。
* 一个运行状况探测，用于确定 VM 实例的运行状况。
* 负载均衡器规则，用于定义如何将流量分配给 VM。

#### <a name="create-the-load-balancer-resource"></a>创建负载均衡器资源

使用 [az network lb create](/cli/azure/network/lb#az_network_lb_create) 创建公共负载均衡器。 指定下列各项：

* 命名为“myLoadBalancer”
* 一个名为 **myFrontEnd** 的池
* 一个名为 **myBackEndPool** 的池
* 与虚拟网络 **myVNet** 相关联
* 与子网 **myBackendSubnet** 相关联

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

#### <a name="create-the-health-probe"></a>创建运行状况探测

运行状况探测会检查所有虚拟机实例，以确保它们可以发送网络流量。 从负载均衡器中删除未通过探测检查的虚拟机。 解决故障后，虚拟机将重新添加到负载均衡器中。

使用 [az network lb probe create](/cli/azure/network/lb/probe#az_network_lb_probe_create) 创建运行状况探测器。 指定下列各项：

* 监视虚拟机的运行状况
* 名称为 **myHealthProbe**
* 协议为 **TCP**
* 监视 **端口 80**

```azurecli-interactive
  az network lb probe create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

#### <a name="create-a-load-balancer-rule"></a>创建负载均衡器规则

负载均衡器规则定义：

* 针对传入流量的 IP 配置。
* 用于接收流量的 IP 池。
* 所需的源和目标端口。 

使用 [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create) 创建负载均衡器规则。 指定下列各项：

* 命名为“myHTTPRule”
* 侦听池 **myFrontEnd** 中的 **端口 80**
* 使用 **端口 80** 将负载均衡的网络流量发送到地址池 **myBackEndPool** 
* 使用运行状况探测 **myHealthProbe**
* 协议为 **TCP**
* 空闲超时为 **15 分钟**
* 启用 TCP 重置

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
    --idle-timeout 15 \
    --enable-tcp-reset true
```

#### <a name="add-vms-to-the-load-balancer-pool"></a>将 VM 添加到负载均衡器池

使用 [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#az_network_nic_ip_config_address_pool_add) 将虚拟机添加到后端池。 指定下列各项：

* 在地址池 **myBackEndPool** 中
* 在资源组 **CreateIntLBQS-rg** 中
* 与网络接口 **myNicVM1**、**myNicVM2** 和 **myNicVM3** 相关联
* 与负载均衡器 myLoadBalancer 关联

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
>对于生产型工作负载，建议使用标准 SKU 负载均衡器。 有关 sku 的详细信息，请参阅 [Azure 负载均衡器 SKU](skus.md)。

在本部分，你将创建一个负载均衡器来对虚拟机进行负载均衡。 创建内部负载均衡器时，虚拟网络配置为负载均衡器的网络。 下图显示在本快速入门中创建的资源：

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal-basic.png" alt-text="在快速入门中创建的基本负载均衡器资源。" border="false":::

### <a name="configure-the-virtual-network"></a>配置虚拟网络

需要先创建支持的虚拟网络资源，然后才能部署 VM 和负载均衡器。

#### <a name="create-a-virtual-network"></a>创建虚拟网络

使用 [az network vnet create](/cli/azure/network/vnet#az_network_vnet_createt) 创建虚拟网络。 指定下列各项：

* 名称为 **myVNet**
* 地址前缀为 **10.1.0.0/16**
* 子网名为 **myBackendSubnet**
* 子网前缀为 **10.1.0.0/24**
* 在 **CreateIntLBQS-rg** 资源组中
* 位置为 **eastus**

```azurecli-interactive
  az network vnet create \
    --resource-group CreateIntLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

#### <a name="create-a-public-ip-address"></a>创建公共 IP 地址

使用 [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) 为 Azure Bastion 主机创建公共 IP 地址。 指定下列各项：

* 创建名为 **myBastionIP** 的标准区域冗余公共 IP 地址
* 在“CreateIntLBQS-rg”中

```azurecli-interactive
az network public-ip create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionIP \
    --sku Standard
```
#### <a name="create-an-azure-bastion-subnet"></a>创建 Azure Bastion 子网

使用 [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) 创建子网。 指定下列各项：

* 名称为 **AzureBastionSubnet**
* 地址前缀为 **10.1.1.0/24**
* 在虚拟网络 **myVNet** 中
* 在资源组 **CreateIntLBQS-rg** 中

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateIntLBQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

#### <a name="create-an-azure-bastion-host"></a>创建 Azure Bastion 主机

使用 [az network bastion create](/cli/azure/network/bastion#az_network_bastion_create) 创建主机。 指定下列各项：

* 命名为 myBastionHost
* 在“CreateIntLBQS-rg”中
* 与公共 IP **myBastionIP** 相关联
* 与虚拟网络 **myVNet** 相关联
* 在 **eastus** 位置

```azurecli-interactive
az network bastion create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

部署 Azure Bastion 主机需要几分钟时间。

#### <a name="create-a-network-security-group"></a>创建网络安全组

对于标准负载均衡器，请确保 VM 具有属于网络安全组的网络接口。 使用 [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create) 创建网络安全组。 指定下列各项：

* 名称为 **myNSG**
* 在资源组 **CreateIntLBQS-rg** 中

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

#### <a name="create-a-network-security-group-rule"></a>创建网络安全组规则

使用 [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) 创建网络安全组规则。 指定下列各项：

* 名称为 **myNSGRuleHTTP**
* 在上一步骤创建的网络安全组 **myNSG** 中
* 在资源组 **CreateIntLBQS-rg** 中
* 协议为 **(*)**
* 方向为“入站”
* 源为 **(*)**
* 目标为 **(*)**
* 目标端口为“端口 80”
* 访问为“允许”
* 优先级为 **200**

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

### <a name="create-back-end-servers"></a>创建后端服务器

在本节中，创建以下项：

* 虚拟机的三个网络接口。
* 虚拟机的可用性集。
* 三个用作负载均衡器服务器的虚拟机。

#### <a name="create-network-interfaces-for-the-virtual-machines"></a>为虚拟机创建网络接口

使用 [az network nic create](/cli/azure/network/nic#az_network_nic_create) 创建三个网络接口。 指定下列各项：

* 名称为 **myNicVM1**、**myNicVM2** 和 **myNicVM3**
* 在资源组 **CreateIntLBQS-rg** 中
* 在虚拟网络 **myVNet** 中
* 在子网 **myBackendSubnet** 中
* 在网络安全组 **myNSG** 中

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

#### <a name="create-the-availability-set-for-the-virtual-machines"></a>创建虚拟机的可用性集

使用 [az vm availability-set create](/cli/azure/vm/availability-set#az_vm_availability_set_create) 创建可用性集。 指定下列各项：

* 名称为 **myAvailabilitySet**
* 在资源组 **CreateIntLBQS-rg** 中
* 位置为 **eastus**

```azurecli-interactive
  az vm availability-set create \
    --name myAvailabilitySet \
    --resource-group CreateIntLBQS-rg \
    --location eastus 
    
```

#### <a name="create-the-virtual-machines"></a>创建虚拟机

使用 [az vm create](/cli/azure/vm#az_vm_create) 创建虚拟机。 指定下列各项：

* 名称为 **myVM1**、**myVM2** 和 **myVM3**
* 在资源组 **CreateIntLBQS-rg** 中
* 附加到网络接口 **myNicVM1**、**myNicVM2** 和 **myNicVM3**
* 虚拟机映像为 **win2019datacenter**
* 在 **myAvailabilitySet** 中


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
部署 VM 可能需要花费几分钟时间。

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]


### <a name="create-the-load-balancer"></a>创建负载均衡器

本部分详细介绍如何创建和配置负载均衡器的以下组件：

* 一个 IP 池，用于在负载均衡器上接收传入网络流量。
* 另一个后端 IP 池，第一个池将负载均衡的网络流量发送到其中。
* 一个运行状况探测，用于确定 VM 实例的运行状况。
* 负载均衡器规则，用于定义如何将流量分配给 VM。

#### <a name="create-the-load-balancer-resource"></a>创建负载均衡器资源

使用 [az network lb create](/cli/azure/network/lb#az_network_lb_create) 创建公共负载均衡器。 指定下列各项：

* 命名为“myLoadBalancer”
* 一个名为 **myFrontEnd** 的池
* 一个名为 **myBackEndPool** 的池
* 与虚拟网络 **myVNet** 相关联
* 与子网 **myBackendSubnet** 相关联

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

#### <a name="create-the-health-probe"></a>创建运行状况探测

运行状况探测会检查所有虚拟机实例，以确保它们可以发送网络流量。 从负载均衡器中删除未通过探测检查的虚拟机。 解决故障后，虚拟机将重新添加到负载均衡器中。

使用 [az network lb probe create](/cli/azure/network/lb/probe#az_network_lb_probe_create) 创建运行状况探测器。 指定下列各项：

* 监视虚拟机的运行状况
* 名称为 **myHealthProbe**
* 协议为 **TCP**
* 监视 **端口 80**

```azurecli-interactive
  az network lb probe create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

#### <a name="create-a-load-balancer-rule"></a>创建负载均衡器规则

负载均衡器规则定义：

* 针对传入流量的 IP 配置。
* 用于接收流量的 IP 池。
* 所需的源和目标端口。 

使用 [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create) 创建负载均衡器规则。 指定下列各项：

* 命名为“myHTTPRule”
* 侦听池 **myFrontEnd** 中的 **端口 80**
* 使用 **端口 80** 将负载均衡的网络流量发送到地址池 **myBackEndPool** 
* 使用运行状况探测 **myHealthProbe**
* 协议为 **TCP**
* 空闲超时为 **15 分钟**

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
#### <a name="add-vms-to-the-load-balancer-pool"></a>将 VM 添加到负载均衡器池

使用 [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#az_network_nic_ip_config_address_pool_add) 将虚拟机添加到后端池。 指定下列各项：

* 在地址池 **myBackEndPool** 中
* 在资源组 **CreateIntLBQS-rg** 中
* 与网络接口 **myNicVM1**、**myNicVM2** 和 **myNicVM3** 相关联
* 与负载均衡器 myLoadBalancer 关联

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

使用 [az network nic create](/cli/azure/network/nic#az_network_nic_create) 创建网络接口。 指定下列各项：

* 名称为 **myNicTestVM**
* 在资源组 **CreateIntLBQS-rg** 中
* 在虚拟网络 **myVNet** 中
* 在子网 **myBackendSubnet** 中
* 在网络安全组 **myNSG** 中

```azurecli-interactive
  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicTestVM \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
使用 [az vm create](/cli/azure/vm#az_vm_create) 创建虚拟机。 指定下列各项：

* 名称为 **myTestVM**
* 在资源组 **CreateIntLBQS-rg** 中
* 附加到网络接口 **myNicTestVM**
* 虚拟机映像为 **Win2019Datacenter**

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myTestVM \
    --nics myNicTestVM \
    --image Win2019Datacenter \
    --admin-username azureuser \
    --no-wait
```
可能需要等待几分钟时间来部署虚拟机。

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

2. 在“概述”页上找到负载均衡器的专用 IP 地址。 在左侧菜单中，选择“所有服务” > “所有资源” > “myLoadBalancer”。  

3. 在 **myLoadBalancer** 的概述中，复制“专用 IP 地址”旁边的地址。

4. 在左侧菜单中，选择“所有服务” > “所有资源”。  在 **CreateIntLBQS-rg** 资源组的资源列表中，选择“myTestVM”。

5. 在“概述”页上，选择“连接” > “Bastion”。  

6. 输入创建 VM 时所输入的用户名和密码。

7. 在 **myTestVM** 上打开“Internet Explorer”。

8. 将上一步骤的 IP 地址输入到浏览器的地址栏。 浏览器中会显示 IIS Web 服务器的默认页。

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="浏览器地址栏中 IP 地址屏幕截图。" border="true":::
   
若要查看所有三个 VM 中的负载均衡器分配流量，可以自定义每个 VM 的 IIS Web 服务器的默认页。 然后从客户端计算机手动刷新 Web 浏览器。

## <a name="clean-up-resources"></a>清理资源

如果你不再需要本教程中创建的资源，请使用 [az group delete](/cli/azure/group#az_group_delete) 命令删除资源组、负载均衡器和所有相关资源。

```azurecli-interactive
  az group delete \
    --name CreateIntLBQS-rg
```

## <a name="next-steps"></a>后续步骤

简要了解 Azure 负载均衡器。
> [!div class="nextstepaction"]
> [什么是 Azure 负载均衡器？](load-balancer-overview.md)
