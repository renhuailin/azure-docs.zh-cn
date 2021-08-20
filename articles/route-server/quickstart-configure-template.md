---
title: 快速入门：使用 Azure 资源管理器模板（ARM 模板）创建 Azure 路由服务器
description: 本快速入门介绍如何使用 Azure 资源管理器模板（ARM 模板）创建 Azure 路由服务器。
services: route-server
author: duongau
ms.service: route-server
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurepowershell
ms.date: 04/05/2021
ms.author: duau
ms.openlocfilehash: 79ff9d8464aa15cac8928a9f5ed4d8863f27400d
ms.sourcegitcommit: e0ef8440877c65e7f92adf7729d25c459f1b7549
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2021
ms.locfileid: "113567396"
---
# <a name="quickstart-create-an-azure-route-server-using-an-arm-template"></a>快速入门：使用 ARM 模板创建 Azure 路由服务器

本快速入门介绍如何使用 Azure 资源管理器模板（ARM 模板）将 Azure 路由服务器部署到新的或现有的虚拟网络中。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请选择“部署到 Azure”按钮。 Azure 门户中会打开模板。

[![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.network%2Froute-server%2Fazuredeploy.json)

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/route-server)。

在本快速入门中，你要将 Azure 路由服务器部署到新的或现有的虚拟网络中。 系统创建一个名为 `RouteServerSubnet` 的专用子网，用于托管路由服务器。 路由服务器还将配置为使用对等 ASN 和对等 IP 以建立 BGP 对等互连。

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.network/route-server/azuredeploy.json" range="001-145" highlight="105-142":::

该模板中已定义了多个 Azure 资源：

* [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualNetworks)
* [Microsoft.Network/virtualNetworks/subnets](/azure/templates/microsoft.network/virtualNetworks/subnets)（两个子网，其中一个是 `routeserversubnet`）。
* [Microsoft.Network/virtualHubs](/azure/templates/microsoft.network/virtualhubs)（路由服务器部署）
* [Microsoft.Network/virtualHubs/ipConfigurations](/azure/templates/microsoft.network/virtualhubs/ipConfigurations)
* [Microsoft.Network/virtualHubs/bgpConnections](/azure/templates/microsoft.network/virtualhubs/bgpconnections)（对等 ASN 和对等 IP 配置）


若要查找与 ExpressRoute 相关的更多模板，请参阅 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular)。

## <a name="deploy-the-template"></a>部署模板

1. 从以下代码块中选择“试用”，以打开 Azure Cloud Shell，然后按照相关说明登录到 Azure。

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.network/route-server/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    等到控制台中显示提示。

1. 从上一个代码块中选择“复制”，以复制 PowerShell 脚本。

1. 右键单击 shell 控制台窗格，然后选择“粘贴”。

1. 输入相应的值。

    资源组名称是追加了 **rg** 的项目名称。

    部署模板大约需要 20 分钟。 完成后，输出类似于：

    :::image type="content" source="./media/quickstart-configure-template/powershell-output.png" alt-text="路由服务器资源管理器模板 PowerShell 部署输出。":::

使用 Azure PowerShell 部署模板。 除了 Azure PowerShell，还可以使用 Azure 门户、Azure CLI 和 REST API。 若要了解其他部署方法，请参阅[部署模板](../azure-resource-manager/templates/deploy-portal.md)。

## <a name="validate-the-deployment"></a>验证部署

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 从左侧窗格中选择“资源组”。

1. 选择你在上一部分中创建的资源组。 默认资源组名称是追加了 **rg** 的项目名称。

1. 资源组应只包含虚拟网络：

     :::image type="content" source="./media/quickstart-configure-template/resource-group.png" alt-text="通过虚拟网络的路由服务器部署资源组。":::

1. 转到  https://aka.ms/routeserver 。

1. 选择名为 **routeserver** 的路由服务器，以验证是否成功部署。

    :::image type="content" source="./media/quickstart-configure-template/deployment.png" alt-text="路由服务器“概述”页的屏幕截图。":::

## <a name="clean-up-resources"></a>清理资源

如果不再需要使用路由服务器创建的资源，请删除资源组。 这会删除 路由服务器和所有相关资源。

若要删除资源组，请调用 `Remove-AzResourceGroup` cmdlet：

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，我们创建了：

* 路由服务器
* 虚拟网络
* 子网

在创建 Azure 路由服务器后，请继续了解 Azure 路由服务器如何与 ExpressRoute 和 VPN 网关进行交互： 

> [!div class="nextstepaction"]
> [Azure ExpressRoute 和 Azure VPN 支持](expressroute-vpn-support.md)
