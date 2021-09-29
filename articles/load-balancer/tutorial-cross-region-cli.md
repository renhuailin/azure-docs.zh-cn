---
title: 教程：使用 Azure CLI 创建跨区域负载均衡器
titleSuffix: Azure Load Balancer
description: 利用本教程开始使用 Azure CLI 部署跨区域 Azure 负载均衡器。
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 03/04/2021
ms.openlocfilehash: b9c96a6434712ed3179fe25c1014dc1fdc6e062d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124736572"
---
# <a name="tutorial-create-a-cross-region-azure-load-balancer-using-azure-cli"></a>教程：使用 Azure CLI 创建跨区域 Azure 负载均衡器

跨区域负载均衡器可确保服务在多个 Azure 区域中全局可用。 如果一个区域出现故障，则会将流量路由到下一个最近的正常运行的区域负载均衡器。  

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建跨区域负载均衡器。
> * 创建负载均衡器规则。
> * 创建包含两个区域负载均衡器的后端池。
> * 测试负载均衡器。

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

- Azure 订阅。
- 两个标准 SKU Azure 负载均衡器，其后端池部署在两个不同的 Azure 区域中。
    - 有关为后端池创建区域标准负载均衡器和虚拟机的信息，请参阅[快速入门：使用 Azure CLI 创建公共负载均衡器以对 VM 进行负载均衡](quickstart-load-balancer-standard-public-cli.md)。
        - 为每个区域中的负载均衡器和虚拟机的名称追加 -R1 和 -R2 。 
- 本地安装的 Azure CLI，或 Azure Cloud Shell。

如果选择在本地安装并使用 CLI，本快速入门要求 Azure CLI 2.0.28 或更高版本。 若要查找版本，请运行 `az --version`。 如需进行安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="sign-in-to-azure-cli"></a>登录 Azure CLI

登录到 Azure CLI：

```azurecli-interactive
az login
```

## <a name="create-cross-region-load-balancer"></a>创建跨区域负载均衡器

在本部分，你将创建跨区域负载均衡器、公共 IP 地址和负载均衡规则。

### <a name="create-a-resource-group"></a>创建资源组

Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。

使用 [az group create](/cli/azure/group#az_group_create) 创建资源组：

* 命名为 **myResourceGroupLB-CR**。
* 在 **westus** 位置。

```azurecli-interactive
  az group create \
    --name myResourceGroupLB-CR \
    --location westus
```

### <a name="create-the-load-balancer-resource"></a>创建负载均衡器资源

使用 [az network cross-region-lb create](/cli/azure/network/cross-region-lb#az_network_cross_region_lb_create) 创建跨区域负载均衡器：

* 命名为 **myLoadBalancer-CR**。
* 前端池名为 **myFrontEnd-CR**。
* 后端池名为 **myBackEndPool-CR**。

```azurecli-interactive
  az network cross-region-lb create \
    --name myLoadBalancer-CR \
    --resource-group myResourceGroupLB-CR \
    --frontend-ip-name myFrontEnd-CR \
    --backend-pool-name myBackEndPool-CR     
```

### <a name="create-the-load-balancer-rule"></a>创建负载均衡器规则

负载均衡器规则定义：

* 针对传入流量的前端 IP 配置。
* 用于接收流量的后端 IP 池。
* 所需的源和目标端口。 

使用 [az network cross-region-lb rule create](/cli/azure/network/cross-region-lb/rule#az_network_cross_region_lb_rule_create) 创建负载均衡器规则：

* 命名为 **myHTTPRule-CR**
* 侦听前端池 **myFrontEnd-CR** 中的 **端口 80**。
* 使用 **端口 80** 将负载均衡的网络流量发送到后端地址池 **myBackEndPool-CR**。 
* 协议为“TCP”。

```azurecli-interactive
  az network cross-region-lb rule create \
    --backend-port 80 \
    --frontend-port 80 \
    --lb-name myLoadBalancer-CR \
    --name myHTTPRule-CR \
    --protocol tcp \
    --resource-group myResourceGroupLB-CR \
    --backend-pool-name myBackEndPool-CR \
    --frontend-ip-name myFrontEnd-CR
```

## <a name="create-backend-pool"></a>创建后端池

在本部分中，你将向跨区域负载均衡器的后端池添加两个区域标准负载均衡器。

> [!IMPORTANT]
> 若要完成这些步骤，请确保已在订阅中部署了两个具有后端池的区域负载均衡器。  有关详细信息，请参阅 **[快速入门：使用 Azure CLI 创建公共负载均衡器以对 VM 进行负载均衡](quickstart-load-balancer-standard-public-cli.md)** 。

### <a name="add-the-regional-frontends-to-load-balancer"></a>将区域前端添加到负载均衡器

在本部分，你要将两个区域负载均衡器前端的资源 ID 置于变量中。  然后，使用这些变量将前端添加到跨区域负载均衡器的后端地址池。

使用 [az network lb frontend-ip show](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_show) 检索资源 ID。

使用 [az network cross-region-lb address-pool address add](/cli/azure/network/cross-region-lb/address-pool/address#az_network_cross_region_lb_address_pool_address_add) 将置于变量中的前端添加到跨区域负载均衡器后端池中：

```azurecli-interactive
  region1id=$(az network lb frontend-ip show \
    --lb-name myLoadBalancer-R1 \
    --name myFrontEnd-R1 \
    --resource-group CreatePubLBQS-rg-r1 \
    --query id \
    --output tsv)

  az network cross-region-lb address-pool address add \
    --frontend-ip-address $region1id \
    --lb-name myLoadBalancer-CR \
    --name myFrontEnd-R1 \
    --pool-name myBackEndPool-CR \
    --resource-group myResourceGroupLB-CR

  region2id=$(az network lb frontend-ip show \
    --lb-name myLoadBalancer-R2 \
    --name myFrontEnd-R2 \
    --resource-group CreatePubLBQS-rg-r2 \
    --query id \
    --output tsv)
  
  az network cross-region-lb address-pool address add \
    --frontend-ip-address $region2id \
    --lb-name myLoadBalancer-CR \
    --name myFrontEnd-R2 \
    --pool-name myBackEndPool-CR \
    --resource-group myResourceGroupLB-CR
```

## <a name="test-the-load-balancer"></a>测试负载均衡器

在本部分中，你将测试跨区域负载均衡器。 你需要在 Web 浏览器中连接到公共 IP 地址。  你将停止其中一个区域负载均衡器后端池中的虚拟机并观察故障转移。

1. 若要获取负载均衡器的公共 IP 地址，请使用 [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show)：

    ```azurecli-interactive
      az network public-ip show \
        --resource-group myResourceGroupLB-CR \
        --name PublicIPmyLoadBalancer-CR \
        --query ipAddress \
        --output tsv
    ```
2. 复制该公共 IP 地址，并将其粘贴到浏览器的地址栏。 IIS Web 服务器的默认页会显示在浏览器上。

3. 停止其中一个区域负载均衡器的后端池中的虚拟机。

4. 刷新 Web 浏览器，观察与其他区域负载均衡器的连接的故障转移。

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组、负载均衡器和所有相关的资源，使用 [az group delete](/cli/azure/group#az_group_delete) 命令将它们删除。

```azurecli-interactive
  az group delete \
    --name myResourceGroupLB-CR
```

## <a name="next-steps"></a>后续步骤

在本教程中，你将了解：

* 创建了跨区域负载均衡器。
* 创建了负载均衡规则。
* 向跨区域负载均衡器的后端池添加了区域负载均衡器。
* 测试了负载均衡器。

转到下一篇文章，了解如何…
> [!div class="nextstepaction"]
> [跨可用性区域对 VM 进行负载均衡](./quickstart-load-balancer-standard-public-portal.md)