---
title: 使用 Azure CLI 为 Azure Kubernetes 服务配置路由首选项
titlesuffix: Azure Virtual Network
description: 了解如何使用 Azure CLI 为 AKS 群集配置路由首选项。
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
ms.openlocfilehash: 491572ceaac3e4bc401d5bd6c3a5401966d0c528
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114463602"
---
# <a name="configure-routing-preference-for-a-kubernetes-cluster-using-azure-cli"></a>使用 Azure CLI 为 Kubernetes 群集配置路由首选项

本文介绍如何使用 Azure CLI 通过 ISP 网络（“Internet”选项）为 Kubernetes 群集配置路由首选项。 设置路由的首选项方法是：创建路由首选项类型为“Internet”的公共 IP，然后在创建 AKS 群集时使用该 IP 地址。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 本文要求使用 2.0.49 版或更高版本的 Azure CLI。 如果使用 Azure Cloud Shell，则最新版本已安装。

## <a name="create-a-resource-group"></a>创建资源组
使用“[az group create](/cli/azure/group#az_group_create)”命令创建资源组。 以下示例在美国东部 Azure 区域创建一个资源组：

```azurecli
  az group create --name myResourceGroup --location eastus
```
## <a name="create-a-public-ip-address"></a>创建公共 IP 地址

使用 [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) 命令创建路由首选项为“Internet”类型的公共 IP 地址。

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

以下命令返回在上一部分创建的公共 IP 地址 ID：
```azurecli
az network public-ip show \
--resource-group myResourceGroup \
--name myRoutingPrefIP \
--query id
```
## <a name="create-kubernetes-cluster-with-the-public-ip"></a>创建具有公共 IP 的 Kubernetes 群集

以下命令使用上一部分创建的公共 IP 创建 AKS 群集：

```azurecli
az aks create \
--resource-group MyResourceGroup \
--name MyAKSCluster \
--load-balancer-outbound-ips "Enter the public IP ID from previous step" --generate-ssh-key
```

>[!NOTE]
>部署 AKS 群集需要几分钟时间。

若要进行验证，请在 Azure 门户中搜索在前面步骤中创建的公共 IP，你将看到该 IP 与 Kubernetes 群集所关联的负载均衡器相关联，如下所示：

 ![Kubernetes 的路由首选公共 IP](./media/routing-preference-azure-kubernetes-service-cli/routing-preference-azure-kubernetes-service.png)


## <a name="next-steps"></a>后续步骤

- 详细了解[公共 IP 地址中的路由首选项](routing-preference-overview.md)。 
- [使用 Azure CLI 为 VM 配置路由首选项](configure-routing-preference-virtual-machine-cli.md)。
