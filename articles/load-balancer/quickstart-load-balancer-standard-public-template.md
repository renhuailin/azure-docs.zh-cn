---
title: 快速入门：创建公共负载均衡器 - Azure 模板
titleSuffix: Azure Load Balancer
description: 本快速入门介绍如何使用 Azure 资源管理器模板创建负载均衡器。
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/09/2020
ms.author: allensu
ms.custom: mvc,subject-armqs
ms.openlocfilehash: a5cb80924daf3328da6a3a9fd7fc1c7c7212bed6
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2021
ms.locfileid: "129090925"
---
# <a name="quickstart-create-a-public-load-balancer-to-load-balance-vms-by-using-an-arm-template"></a>快速入门：使用 ARM 模板创建公共负载均衡器，以对 VM 进行负载均衡

负载均衡将传入请求分布到多个虚拟机 (VM)，从而提供更高级别的可用性和可伸缩性。

本快速入门演示如何部署标准负载均衡器，以对虚拟机进行负载均衡。

与其他部署方法相比，使用 ARM 模板需要的步骤更少。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请选择“部署到 Azure”按钮。 Azure 门户中会打开模板。

[![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.network%2Fload-balancer-standard-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/load-balancer-standard-create/)。

负载均衡器和公共 IP SKU 必须匹配。 创建标准负载均衡器时，还必须创建一个作为该标准负载均衡器的前端配置的新标准公共 IP 地址。 若要创建基本负载均衡器，请使用[此模板](https://azure.microsoft.com/resources/templates/2-vms-loadbalancer-natrules/)。 Microsoft 建议将标准 SKU 用于生产工作负载。

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.network/load-balancer-standard-create/azuredeploy.json":::

该模板中已定义了多个 Azure 资源：

- [**Microsoft.Network/loadBalancers**](/azure/templates/microsoft.network/loadbalancers)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)：用于负载均衡器、堡垒主机以及三个虚拟机中的每个虚拟机。
- [**Microsoft.Network/bastionHosts**](/azure/templates/microsoft.network/bastionhosts)
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines) (3)。
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)（3 个）。
- [**Microsoft.Compute/virtualMachine/extensions**](/azure/templates/microsoft.compute/virtualmachines/extensions) (3)：用于配置 Internet Information Server (IIS) 和网页。

若要查找与 Azure 负载均衡器相关的更多模板，请参阅 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular)。

## <a name="deploy-the-template"></a>部署模板

1. 从以下代码块中选择“试用”，以打开 Azure Cloud Shell，然后按照相关说明登录到 Azure。

   ```azurepowershell-interactive
   $projectName = Read-Host -Prompt "Enter a project name with 12 or less letters or numbers that is used to generate Azure resource names"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
   $adminUserName = Read-Host -Prompt "Enter the virtual machine administrator account name"
   $adminPassword = Read-Host -Prompt "Enter the virtual machine administrator password" -AsSecureString

   $resourceGroupName = "${projectName}rg"
   $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.network/load-balancer-standard-create/azuredeploy.json"

   New-AzResourceGroup -Name $resourceGroupName -Location $location
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName -location $location -adminUsername $adminUsername -adminPassword $adminPassword

   Write-Host "Press [ENTER] to continue."
   ```

   等到控制台中显示提示。

1. 从上一个代码块中选择“复制”，以复制 PowerShell 脚本。

1. 右键单击 shell 控制台窗格，然后选择“粘贴”。

1. 输入相应的值。

   模板部署会创建三个可用性区域。 可用性区域仅在[某些地区](../availability-zones/az-overview.md)受到支持。 请使用受支持的地区之一。 如果不确定，请输入 **centralus**。

   资源组名称是追加了 **rg** 的项目名称。 在下一部分中将用到资源组名称。

部署模板大约需要 10 分钟的时间。 完成后，输出类似于：

![Azure 标准负载均衡器资源管理器模板 PowerShell 部署输出](./media/quickstart-load-balancer-standard-public-template/azure-standard-load-balancer-resource-manager-template-powershell-output.png)

使用 Azure PowerShell 部署模板。 还可以使用 Azure 门户、Azure CLI 和 REST API。 若要了解其他部署方法，请参阅[部署模板](../azure-resource-manager/templates/deploy-portal.md)。

## <a name="review-deployed-resources"></a>查看已部署的资源

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 从左侧窗格中选择“资源组”。

1. 选择你在上一部分中创建的资源组。 默认资源组名称是追加了 **rg** 的项目名称。

1. 选择负载均衡器。 其默认名称是追加了 **-lb** 的项目名称。

1. 仅复制公共 IP 地址的 IP 地址部分，然后将其粘贴到浏览器的地址栏中。

   ![Azure 标准负载均衡器资源管理器模板公共 IP](./media/quickstart-load-balancer-standard-public-template/azure-standard-load-balancer-resource-manager-template-deployment-public-ip.png)

    浏览器将显示 Internet Information Services (IIS) Web 服务器的默认页。

   ![IIS Web 服务器](./media/quickstart-load-balancer-standard-public-template/load-balancer-test-web-page.png)

若要查看负载均衡器如何在所有 3 个 VM 之间分配流量，可从客户端计算机强制刷新 Web 浏览器。

## <a name="clean-up-resources"></a>清理资源

不再需要它们时，请删除：

* 资源组
* 负载均衡器
* 相关资源

请访问 Azure 门户，选择包含负载均衡器的资源组，然后选择“删除资源组”。

## <a name="next-steps"></a>后续步骤

在本快速入门中，请执行以下操作：

* 为负载均衡器和虚拟机创建了虚拟网络。
* 创建了用于管理的 Azure Bastion 主机。
* 已创建标准负载均衡器并已将 VM 附加到它。
* 配置了负载均衡器流量规则和运行状况探测。
* 测试了负载均衡器。

若要了解详细信息，请继续学习与 Azure 负载均衡器相关的教程。

> [!div class="nextstepaction"]
> [Azure 负载均衡器教程](./quickstart-load-balancer-standard-public-portal.md)
