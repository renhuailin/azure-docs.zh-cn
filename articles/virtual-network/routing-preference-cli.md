---
title: 使用 Azure CLI 配置公共 IP 地址的路由首选项
titlesuffix: Azure Virtual Network
description: 了解如何使用 Azure CLI 通过 Internet 流量路由首选项创建公共 IP。
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
ms.openlocfilehash: 2291767c162953f8339fb8cc27e55b96290ef795
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101665961"
---
# <a name="configure-routing-preference-for-a-public-ip-address-using-azure-cli"></a>使用 Azure CLI 配置公共 IP 地址的路由首选项

本文介绍如何使用 Azure CLI 通过 ISP 网络（“Internet”选项）配置公共 IP 地址的路由首选项。 创建公共 IP 地址后，可将其与以下 Azure 资源关联，以获得 Internet 的入站和出站流量：

* 虚拟机
* 虚拟机规模集
* Azure Kubernetes 服务 (AKS)
* 面向 Internet 的负载均衡器
* 应用程序网关
* Azure 防火墙

默认情况下，对于所有 Azure 服务，公共 IP 地址的路由首选项设置为“Microsoft 全球网络”，并且可以与任何 Azure 服务相关联。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 本文要求使用 2.0.49 版或更高版本的 Azure CLI。 如果使用 Azure Cloud Shell，则最新版本已安装。

## <a name="create-a-resource-group"></a>创建资源组
使用“[az group create](/cli/azure/group#az-group-create)”命令创建资源组。 以下示例在美国东部 Azure 区域创建一个资源组：

```azurecli
  az group create --name myResourceGroup --location eastus
```
## <a name="create-a-public-ip-address"></a>创建公共 IP 地址

使用命令 [az network 公共 ip create](/cli/azure/network/public-ip#az-network-public-ip-create)创建包含 **Internet** 类型首选项的公共 IP 地址，格式如下所示。

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

可以将上面创建的公共 IP 地址与 [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 虚拟机相关联。 请参阅“教程”页上的“CLI”部分：[将公共 IP 地址与虚拟机相关联](associate-public-ip-address-vm.md#azure-cli)，以将公共 IP 关联到 VM。 也可以将上面创建的公共 IP 地址与 [Azure 负载均衡器](../load-balancer/load-balancer-overview.md)相关联，具体方法为将它分配到负载均衡器前端配置。 此公共 IP 地址充当负载均衡型虚拟 IP 地址 (VIP)。

## <a name="next-steps"></a>后续步骤

- 详细了解[公共 IP 地址中的路由首选项](routing-preference-overview.md)。 
- [使用 Azure CLI 为 VM 配置路由首选项](configure-routing-preference-virtual-machine-cli.md)。

