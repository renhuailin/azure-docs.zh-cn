---
title: 创建 NAT 网关 - 资源管理器模板
titleSuffix: Azure Virtual Network NAT
description: 本快速入门介绍如何使用 Azure 资源管理器模板（ARM 模板）创建 NAT 网关。
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/27/2020
ms.author: allensu
ms.custom: subject-armqs, devx-track-azurepowershell
ms.openlocfilehash: 728c382d68c739cba927db985e16d3679f69f0d5
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113438992"
---
# <a name="quickstart-create-a-nat-gateway---arm-template"></a>快速入门：创建 NAT 网关 - ARM 模板

通过 Azure 资源管理器模板（ARM 模板）完成虚拟网络 NAT 入门。 此模板部署虚拟网络、NAT 网关资源和 Ubuntu 虚拟机。 Ubuntu 虚拟机将部署到与 NAT 网关资源关联的子网。

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请选择“部署到 Azure”按钮。 Azure 门户中会打开模板。

[![部署到 Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.network%2Fnat-gateway-1-vm%2Fazuredeploy.json)

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/nat-gateway-1-vm)。

此模板配置为创建：

* 虚拟网络
* NAT 网关资源
* Ubuntu 虚拟机

Ubuntu VM 部署到与 NAT 网关资源关联的子网。

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.network/nat-gateway-1-vm/azuredeploy.json":::

该模板中定义了九个 Azure 资源：

* **[Microsoft.Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups)** ：创建网络安全组。
* **[Microsoft.Network/networkSecurityGroups/securityRules](/azure/templates/microsoft.network/networksecuritygroups/securityrules)** ：创建安全规则。
* **[Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)** ：创建公共 IP 地址。
* **[Microsoft.Network/publicIPPrefixes](/azure/templates/microsoft.network/publicipprefixes)** ：创建公共 IP 前缀。
* **[Microsoft.Compute/virtualMachines](/azure/templates/Microsoft.Compute/virtualMachines)** ：创建虚拟机。
* **[Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)** ：创建虚拟网络。
* **[Microsoft.Network/natGateways](/azure/templates/microsoft.network/natgateways)** ：创建 NAT 网关资源。
* **[Microsoft.Network/virtualNetworks/subnets](/azure/templates/microsoft.network/virtualnetworks/subnets)** ：创建虚拟网络子网。
* **[Microsoft.Network/networkinterfaces](/azure/templates/microsoft.network/networkinterfaces)** ：创建网络接口。

## <a name="deploy-the-template"></a>部署模板

**Azure CLI**

```azurecli-interactive
read -p "Enter the location (i.e. westcentralus): " location
resourceGroupName="myResourceGroupNAT"
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.network/nat-gateway-1-vm/azuredeploy.json"

az group create \
--name $resourceGroupName \
--location $location

az deployment group create \
--resource-group $resourceGroupName \
--template-uri  $templateUri
```

**Azure PowerShell**

```azurepowershell-interactive
$location = Read-Host -Prompt "Enter the location (i.e. westcentralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.network/nat-gateway-1-vm/azuredeploy.json"

$resourceGroupName = "myResourceGroupNAT"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri
```

**Azure 门户**

[![部署到 Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.network%2Fnat-gateway-1-vm%2Fazuredeploy.json)

## <a name="review-deployed-resources"></a>查看已部署的资源

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 从左侧窗格中选择“资源组”。

1. 选择你在上一部分中创建的资源组。 默认资源组名称是 **myResourceGroupNAT**

1. 验证是否在资源组中创建了以下资源：

    ![虚拟网络 NAT 资源组](./media/quick-create-template/nat-gateway-template-rg.png)

## <a name="clean-up-resources"></a>清理资源

**Azure CLI**

如果不再需要上述资源组及其包含的所有资源，可以使用 [az group delete](/cli/azure/group#az_group_delete) 命令将其删除。

```azurecli-interactive
  az group delete \
    --name myResourceGroupNAT
```

**Azure PowerShell**

如果不再需要上述资源组及其包含的所有资源，可以使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 命令将其删除。

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupNAT
```

**Azure 门户**

如果不再需要上述资源组、NAT 网关和所有相关资源，请将其删除。 选择包含 NAT 网关的资源组 **myResourceGroupNAT**，然后选择“删除”。

## <a name="next-steps"></a>后续步骤

在本快速入门中，我们创建了：

* NAT 网关资源
* 虚拟网络
* Ubuntu 虚拟机

虚拟机部署到与 NAT 网关关联的虚拟网络子网。

若要详细了解虚拟网络 NAT 和 Azure 资源管理器，请继续阅读以下文章。

* 阅读[虚拟网络 NAT 概述](nat-overview.md)
* 了解 [NAT 网关资源](nat-gateway-resource.md)
* 了解有关 [Azure 资源管理器](../../azure-resource-manager/management/overview.md)的详细信息
