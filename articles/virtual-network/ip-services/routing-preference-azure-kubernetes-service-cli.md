---
title: 教程：为 Azure Kubernetes 服务配置路由首选项 - Azure CLI
titlesuffix: Azure virtual network
description: 使用本教程来了解如何为 Azure Kubernetes 服务配置路由首选项。
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: tutorial
ms.date: 10/01/2021
ms.custom: template-tutorial
ms.openlocfilehash: 277f861b0816b0c7eaf0267fc80f1b15d12509d5
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129368793"
---
# <a name="tutorial-configure-routing-preference-for-an-azure-kubernetes-service-using-the-azure-cli"></a>教程：使用 Azure CLI 为 Azure Kubernetes 服务配置路由首选项

本文介绍如何使用 Azure CLI 通过 ISP 网络（“Internet”选项）为 Kubernetes 群集配置路由首选项。 设置路由的首选项方法是：创建路由首选项类型为“Internet”的公共 IP，然后在创建 AKS 群集时使用该 IP 地址。

在本教程中，你将了解：

> [!div class="checklist"]
> * 创建具有 Internet 路由首选项的公共 IP 地址。
> * 创建具有 Internet 路由首选项公共 IP 的 Azure Kubernetes 群集。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- 本文要求使用 2.0.49 版或更高版本的 Azure CLI。 如果使用 Azure Cloud Shell，则最新版本已安装。

## <a name="create-a-resource-group"></a>创建资源组

使用“[az group create](/cli/azure/group#az_group_create)”命令创建资源组。 以下示例在美国东部 Azure 区域创建一个资源组：

```azurecli-interactive
  az group create \
    --name TutorAKSRP-rg \
    --location eastus

```

## <a name="create-public-ip-with-internet-routing-preference"></a>创建具有 Internet 路由首选项的公共 IP

使用 [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) 命令创建路由首选项为“Internet”类型的公共 IP 地址。

以下命令在美国东部 Azure 区域创建具有“Internet”路由首选项的新公共 IP。

```azurecli-interactive
  az network public-ip create \
    --resource-group TutorAKSRP-rg \
    --name myPublicIP-IR \
    --version IPv4 \
    --ip-tags 'RoutingPreference=Internet' \
    --sku Standard \
    --zone 1 2 3
```
> [!NOTE]
>  路由首选项目前仅支持 IPv4 公共 IP 地址。

## <a name="create-kubernetes-cluster-with-public-ip"></a>创建具有公共 IP 的 Kubernetes 群集

将之前创建的公共 IP 的 ID 置于变量中，以供以后使用。 使用 [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) 检索公共 IP ID。

以下命令检索公共 IP ID，请将其置于变量中，以在下一个命令中使用。

```azurecli-interactive
  export resourceid=$(az network public-ip show \
    --resource-group TutorAKSRP-rg \
    --name myPublicIP-IR \
    --query id \
    --output tsv)
```

使用 [az aks create](/cli/azure/aks#az_aks_create) 创建 Kubernetes 群集。

以下命令创建 Kubernetes 群集，并使用在上一步中创建的公共 IP 的变量。

```azurecli-interactive
  az aks create \
    --resource-group TutorAKSRP-rg \
    --name MyAKSCluster \
    --load-balancer-outbound-ips $resourceid \
    --generate-ssh-key
```

>[!NOTE]
>部署 AKS 群集需要几分钟时间。

若要进行验证，请在 Azure 门户中搜索在前面步骤中创建的公共 IP。 公共 IP 与负载均衡器相关联。 负载均衡器与 Kubernetes 群集相关联，如下所示：

  :::image type="content" source="./media/routing-preference-azure-kubernetes-service-cli/verify-aks-ip.png" alt-text="AKS 群集公共 IP 地址的屏幕截图。":::

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组、公共 IP、AKS 群集和所有相关的资源，请使用 [az group delete](/cli/azure/group#az_group_delete) 命令将其删除。

```azurecli-interactive
  az group delete \
    --name TutorAKSRP-rg
```

## <a name="next-steps"></a>后续步骤

请继续学习下一篇文章，了解如何创建具有混合路由首选项的虚拟机：
> [!div class="nextstepaction"]
> [为虚拟机配置两个路由首选项选项](routing-preference-mixed-network-adapter-portal.md)