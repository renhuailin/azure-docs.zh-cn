---
title: 使用 Azure CLI 配置 Azure Kubernetes 服务的路由首选项
titlesuffix: Azure Virtual Network
description: 了解如何使用 Azure CLI 按路由首选项配置 AKS 群集。
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2021
ms.author: mnayak
ms.openlocfilehash: ac70f48a3c484f8865c54e09c59662a14a259e74
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679172"
---
# <a name="configure-routing-preference-for-a-kubernetes-cluster-using-azure-cli"></a>使用 Azure CLI 配置 Kubernetes 群集的路由首选项

本文介绍如何使用 Azure CLI 为 Kubernetes 群集) 的 ISP 网络 (**Internet** 选项配置路由首选项。 路由首选项是通过创建路由首选项类型 * * Internet * * * 的公共 IP 地址设置的，并在创建 AKS 群集时使用。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 本文要求使用 2.0.49 版或更高版本的 Azure CLI。 如果使用 Azure Cloud Shell，则最新版本已安装。

## <a name="create-a-resource-group"></a>创建资源组
使用“[az group create](/cli/azure/group#az-group-create)”命令创建资源组。 以下示例在美国东部 Azure 区域创建一个资源组：

```azurecli
  az group create --name myResourceGroup --location eastus
```
## <a name="create-a-public-ip-address"></a>创建公共 IP 地址

使用命令 [az network 公共 ip create](/cli/azure/network/public-ip#az-network-public-ip-create)创建包含 **Internet** 类型首选项的公共 IP 地址。

以下命令在美国东部 Azure 区域创建具有“Internet”路由首选项的新公共 IP。

```azurecli
az network public-ip create \
--name MyRoutingPrefIP \
--resource-group MyResourceGroup \
--location eastus \
--ip-tags 'RoutingPreference=Internet' \
--sku STANDARD \
--allocation-method static \
--version IPv4
```
> [!NOTE]
>  路由首选项目前仅支持 IPv4 公共 IP 地址。

## <a name="get-the-id-of-public-ip-address"></a>获取公共 IP 地址的 ID

以下命令返回在上一节中创建的公共 IP 地址 ID：
```azurecli
az network public-ip show \
--resource-group myResourceGroup \
--name myRoutingPrefIP \
--query id
```
## <a name="create-kubernetes-cluster-with-the-public-ip"></a>创建具有公共 ip 的 Kubernetes 群集

以下命令将创建 AKS 群集，其中包含在上一部分中创建的公共 IP：

```azurecli
az aks create \
--resource-group MyResourceGroup \
--name MyAKSCluster \
--load-balancer-outbound-ips "Enter the public IP ID from previous step" --generate-ssh-key
```

>[!NOTE]
>部署 AKS 群集需要几分钟时间。

若要验证，请在 Azure 门户中搜索在前面的步骤中创建的公共 IP，你会看到 IP 与 Kubernetes 群集关联的负载均衡器相关联，如下所示：

 ![Kubernetes 的路由首选项公共 IP](./media/routing-preference-azure-kubernetes-service-cli/routing-preference-azure-kubernetes-service.png)


## <a name="next-steps"></a>后续步骤

- 详细了解[公共 IP 地址中的路由首选项](routing-preference-overview.md)。 
- [使用 Azure CLI 为 VM 配置路由首选项](configure-routing-preference-virtual-machine-cli.md)。

