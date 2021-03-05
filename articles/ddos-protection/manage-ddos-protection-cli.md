---
title: 使用 Azure CLI 创建和配置 Azure DDoS 防护计划
description: 了解如何使用 Azure CLI 创建 DDoS 保护计划
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: yitoh
ms.openlocfilehash: 59c5ca9ce9e95319b36e002da0b5d1438ef3fdd1
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102203770"
---
# <a name="quickstart-create-and-configure-azure-ddos-protection-standard-using-azure-cli"></a>快速入门：使用 Azure CLI 创建和配置 Azure DDoS 保护标准

使用 Azure CLI 开始使用 Azure DDoS 保护标准版。 

DDoS 防护计划在订阅中定义一组已启用 DDoS 防护标准的虚拟网络。 可以为组织配置一个 DDoS 防护计划，然后从多个订阅将虚拟网络链接到相同计划。 

在本快速入门中，你将创建一个 DDoS 保护计划并将其链接到虚拟网络。 

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 本地安装的 Azure CLI 或 Azure Cloud Shell

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本快速入门要求 Azure CLI 2.0.28 或更高版本。 若要查找版本，请运行 `az --version`。 如需进行安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="create-a-ddos-protection-plan"></a>创建 DDoS 保护计划

在 Azure 中，可将相关的资源分配到资源组。 可以使用现有资源组，也可以创建新组。

若要创建资源组，请使用 [az group create](/cli/azure/group#az-group-create)。 在此示例中，我们将命名资源组 _MyResourceGroup_ ，并使用 _美国东部_ 位置：

```azurecli-interactive
az group create \
    --name MyResourceGroup \
    --location eastus
```

现在，创建名为 _MyDdosProtectionPlan_ 的 DDoS 保护计划：

```azurecli-interactive
az network ddos-protection create \
    --resource-group MyResourceGroup \
    --name MyDdosProtectionPlan
```

## <a name="enable-ddos-protection-for-a-virtual-network"></a>为虚拟网络启用 DDoS 保护

### <a name="enable-ddos-protection-for-a-new-virtual-network"></a>为新的虚拟网络启用 DDoS 保护

创建虚拟网络时，可以启用 DDoS 保护。 在此示例中，我们将命名为虚拟网络 _MyVnet_： 

```azurecli-interactive
az network vnet create \
    --resource-group MyResourceGroup \
    --name MyVnet \
    --location eastus \
    --ddos-protection true
    --ddos-protection-plan MyDdosProtectionPlan
```

如果已为虚拟网络启用 DDoS 防护，无法将虚拟网络移到其他资源组或订阅。 如果需要移动已启用 DDoS 标准的虚拟网络，请先禁用该标准并移动虚拟网络，然后启用 DDoS 标准。 移动后，适用于虚拟网络所有受保护的公共 IP 地址的自动优化策略阈值都将重置。

### <a name="enable-ddos-protection-for-an-existing-virtual-network"></a>为现有虚拟网络启用 DDoS 保护

[创建 DDoS 保护计划](#create-a-ddos-protection-plan)时，可以将一个或多个虚拟网络关联到计划。 要添加多个虚拟网络，只需列出以空格分隔的名称或 Id。 在此示例中，我们将添加 _MyVnet_：

```azurecli-interactive
az group create \
    --name MyResourceGroup \
    --location eastus

az network ddos-protection create \
    --resource-group MyResourceGroup \
    --name MyDdosProtectionPlan
    --vnets MyVnet
```

或者，可以为给定的虚拟网络启用 DDoS 保护：

```azurecli-interactive
az network vnet update \
    --resource-group MyResourceGroup \
    --name MyVnet \
    --ddos-protection true
    --ddos-protection-plan MyDdosProtectionPlan
```

## <a name="validate-and-test"></a>验证并测试

首先，请查看 DDoS 保护计划的详细信息：

```azurecli-interactive
az network ddos-protection show \
    --resource-group MyResourceGroup \
    --name MyDdosProtectionPlan
```

验证该命令是否返回正确的 DDoS 防护计划详细信息。

## <a name="clean-up-resources"></a>清理资源

可在下一教程中保留资源。 如果不再需要，请删除 _MyResourceGroup_ 资源组。 删除资源组时，还会删除 DDoS 保护计划及其所有相关资源。 

若要删除资源组，请使用 [az group delete](/cli/azure/group#az_group_delete)：

```azurecli-interactive
az group delete \
--name MyResourceGroup 
```

更新给定虚拟网络以禁用 DDoS 保护：

```azurecli-interactive
az network vnet update \
    --resource-group MyResourceGroup \
    --name MyVnet \
    --ddos-protection false
    --ddos-protection-plan ""
```

如果要删除 DDoS 保护计划，必须先取消关联所有虚拟网络的关联。 

## <a name="next-steps"></a>后续步骤

要了解如何查看和配置 DDoS 防护计划的遥测，请继续阅读教程。

> [!div class="nextstepaction"]
> [查看和配置 DDoS 防护遥测](telemetry.md)
