---
title: 教程：为 VM 配置路由首选项 - Azure CLI
description: 本教程介绍如何使用 Azure CLI 来创建具有已设置所选路由首选项的公共 IP 地址的 VM。
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: tutorial
ms.date: 10/01/2021
ms.custom: template-tutorial
ms.openlocfilehash: 9428d28eabde40dec7ed2805217fb655d8b0f693
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129369053"
---
# <a name="tutorial-configure-routing-preference-for-a-vm-using-the-azure-cli"></a>教程：使用 Azure CLI 为 VM 配置路由首选项
本教程介绍如何为虚拟机配置路由首选项。 如果你选择“Internet”作为路由首选项，则从 VM 发往 Internet 的流量会通过 ISP 网络进行路由。 默认是通过 Microsoft 全球网络路由。

在本教程中，你将了解：

> [!div class="checklist"]
> * 创建为 Internet 路由首选项配置的公共 IP 地址。
> * 创建虚拟机。
> * 验证公共 IP 地址是否设置为 Internet 路由首选项。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 本教程需要 Azure CLI 版本 2.0.28 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。

## <a name="create-a-resource-group"></a>创建资源组

Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。

使用 [az group create](/cli/azure/group#az_group_create) 在“westus2”位置创建名为“TutorVMRoutePref-rg”的资源组 。

```azurecli-interactive
  az group create \
    --name TutorVMRoutePref-rg \
    --location westus2
```

## <a name="create-a-public-ip-address"></a>创建公共 IP 地址

在 TutorVMRoutePref-rg 中，使用 [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) 创建一个名为 myPublicIP 的标准区域冗余公共 IPv4 地址 。 Internet 标记作为 CLI 命令中的一个参数应用于公共 IP 地址，启用 Internet 路由首选项  。

```azurecli-interactive
az network public-ip create \
    --resource-group TutorVMRoutePref-rg \
    --name myPublicIP \
    --version IPv4 \
    --ip-tags 'RoutingPreference=Internet' \
    --sku Standard \
    --zone 1 2 3
```

## <a name="create-virtual-machine"></a>创建虚拟机

使用 [az vm create](/cli/azure/vm#az_vm_create) 创建虚拟机。 上一部分创建的公共 IP 地址作为 CLI 命令的一部分添加，在创建过程中附加到 VM。

```azurecli-interactive
az vm create \
--name myVM \
--resource-group TutorVMRoutePref-rg \
--public-ip-address myPublicIP \
--size Standard_A2 \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest \
--admin-username azureuser
```

## <a name="verify-internet-routing-preference"></a>验证 Internet 路由首选项

使用 [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) 验证是否为公共 IP 地址配置了 Internet 路由首选项。

```azurecli-interactive
az network public-ip show \
    --resource-group TutorVMRoutePref-rg \
    --name myPublicIP \
    --query ipTags \
    --output tsv
```

## <a name="clean-up-resources"></a>清理资源

用完虚拟机和公共 IP 地址后，请使用 [az group delete](/cli/azure/group#az_group_delete) 删除资源组及其包含的所有资源。

```azurecli-interactive
  az group delete \
    --name TutorVMRoutePref-rg
```

## <a name="next-steps"></a>后续步骤

请继续学习下一篇文章，了解如何创建具有混合路由首选项的虚拟机：
> [!div class="nextstepaction"]
> [为虚拟机配置两个路由首选项选项](routing-preference-mixed-network-adapter-portal.md)

