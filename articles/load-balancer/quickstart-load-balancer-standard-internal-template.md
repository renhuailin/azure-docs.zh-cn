---
title: 快速入门：使用模板创建内部负载均衡器
description: 本快速入门介绍如何使用 Azure 资源管理器模板（ARM 模板）创建内部 Azure 负载均衡器。
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: allensu
ms.date: 09/14/2020
ms.openlocfilehash: a1fee5dd57568a77355315dc0a0ff89bd05775aa
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2021
ms.locfileid: "112467556"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-by-using-an-arm-template"></a>快速入门：使用 ARM 模板创建内部负载均衡器，以对 VM 进行负载均衡

本快速入门介绍如何使用 Azure 资源管理器模板（ARM 模板）来创建内部 Azure 负载均衡器。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请选择“部署到 Azure”按钮。 Azure 门户中会打开模板。

[![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.compute%2F2-vms-internal-load-balancer%2Fazuredeploy.json)

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/2-vms-internal-load-balancer/)。

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.compute/2-vms-internal-load-balancer/azuredeploy.json":::

该模板中已定义了多个 Azure 资源：

- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageaccounts)：用于启动诊断的虚拟机存储帐户。
- [**Microsoft.Compute/availabilitySets**](/azure/templates/microsoft.compute/availabilitySets)：虚拟机的可用性集。
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualNetworks)：负载均衡器和虚拟机的虚拟网络。
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkInterfaces)：虚拟机的网络接口。
- [**Microsoft.Network/loadBalancers**](/azure/templates/microsoft.network/loadBalancers)：内部负载均衡器。
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualMachines)：虚拟机。

若要查找与 Azure 负载均衡器相关的更多模板，请参阅 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular)。

## <a name="deploy-the-template"></a>部署模板

**Azure CLI**

```azurecli-interactive
read -p "Enter the location (i.e. westcentralus): " location
resourceGroupName="myResourceGroupLB"
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.compute/2-vms-internal-load-balancer/azuredeploy.json"

az group create \
--name $resourceGroupName \
--location $location

az deployment group create \
--resource-group $resourceGroupName \
--template-uri  $templateUri
```

## <a name="review-deployed-resources"></a>查看已部署的资源

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 从左侧窗格中选择“资源组”。

1. 选择你在上一部分中创建的资源组。 默认资源组名称是“myResourceGroupLB”

1. 验证是否在资源组中创建了以下资源：

:::image type="content" source="media/quickstart-load-balancer-standard-internal-template/verify-creation.png" alt-text="用于验证资源创建的用户 Azure 门户。" border="true":::

## <a name="clean-up-resources"></a>清理资源

如果不再需要上述资源组及其包含的所有资源，可以使用 [az group delete](/cli/azure/group#az_group_delete) 命令将其删除。

```azurecli-interactive
  az group delete \
    --name myResourceGroupLB
```

## <a name="next-steps"></a>后续步骤

有关引导你完成模板创建过程的分步教程，请参阅：

> [!div class="nextstepaction"]
> [教程：创建和部署你的第一个 ARM 模板](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
