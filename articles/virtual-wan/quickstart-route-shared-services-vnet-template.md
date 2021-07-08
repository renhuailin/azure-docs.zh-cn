---
title: 快速入门：使用 ARM 模板路由到共享服务
titleSuffix: Azure Virtual WAN
description: 了解如何使用 Azure 资源管理器模板（ARM 模板）设置路由，以访问具有每个 VNet 和分支都要访问的工作负载的共享服务 VNet。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: quickstart
ms.date: 03/05/2021
ms.author: cherylmc
ms.custom: subject-armqs
ms.openlocfilehash: ff23bf626fe252547d3449cfe318c073e9cc9b6c
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2021
ms.locfileid: "112026302"
---
# <a name="quickstart-route-to-shared-services-vnets-using-an-arm-template"></a>快速入门：使用 ARM 模板路由到共享服务 VNet

本快速入门介绍如何使用 Azure 资源管理器模板（ARM 模板）设置路由，以访问具有每个 VNet 和分支 (VPN/ER/P2S) 都要访问的工作负载的共享服务 VNet。 这些共享工作负载的示例可能包括具有域控制器或文件共享等服务的虚拟机，或通过 [Azure 专用终结点](../private-link/private-endpoint-overview.md)在内部公开的 Azure 服务。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请选择“部署到 Azure”按钮。 Azure 门户中会打开模板。

[![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2fquickstarts%2fmicrosoft.network%2fvirtual-wan-with-route-tables%2fazuredeploy.json)

## <a name="prerequisites"></a>先决条件

* 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 此配置要求提供公钥证书数据。 本文提供了示例数据。 不过，提供示例数据只是为了满足创建 P2S 网关的模板要求。 完成模板并部署资源后，必须使用自己的证书数据更新此字段，以使配置正常工作。 请参阅[用户 VPN 证书](certificates-point-to-site.md#cer)。

## <a name="review-the-template"></a><a name="review"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/virtual-wan-with-route-tables)。 本文的模板太长，无法在此处显示。 若要查看模板，请参阅 [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.network/virtual-wan-with-route-tables/azuredeploy.json)。

在此快速入门中，你将创建一个 Azure 虚拟 WAN 多中心部署，包括所有网关和 VNet 连接。 输入参数列表已有意保持在最小值。 可通过修改模板内的变量来更改 IP 寻址方案。 [方案：共享服务 VNet](scenario-shared-services-vnet.md) 一文中进一步说明了该方案。

:::image type="content" source="./media/routing-scenarios/shared-service-vnet/shared-services.png" alt-text="部署体系结构" lightbox="./media/routing-scenarios/shared-service-vnet/shared-services.png":::

此模板使用以下资源创建功能齐全的 Azure 虚拟 WAN 环境：

* 2 个不同的中心（分处不同区域）。
* 4 个 Azure 虚拟网络 (VNet)。
* 每个 VWan 中心有 2 个 VNet 连接。
* 每个中心有 1 个点到站点 (P2S) VPN 网关。
* 每个中心有 1 个站点到站点 (S2S) VPN 网关。
* 每个中心有 1 个 ExpressRoute 网关。
* 每个中心的自定义路由表 RT_SHARED。
* 用于对 RT_SHARED 路由表进行分组的标签LBL_RT_SHARED。

模板中定义了多个 Azure 资源：

* [**Microsoft.Network/virtualwans**](/azure/templates/microsoft.network/virtualwans)
* [**Microsoft.Network/virtualhubs**](/azure/templates/microsoft.network/virtualhubs)
* [**Microsoft.Network/virtualnetworks**](/azure/templates/microsoft.network/virtualnetworks)
* [**Microsoft.Network/hubvirtualnetworkconnections**](/azure/templates/microsoft.network/virtualhubs/hubvirtualnetworkconnections)
* [**Microsoft.Network/hubroutetables**](/azure/templates/microsoft.network/virtualhubs/hubRouteTables)
* [**Microsoft.Network/p2svpngateways**](/azure/templates/microsoft.network/p2svpngateways)
* [**Microsoft.Network/vpngateways**](/azure/templates/microsoft.network/vpngateways)
* [**Microsoft.Network/expressroutegateways**](/azure/templates/microsoft.network/expressroutegateways)
* [**Microsoft.Network/vpnserverconfigurations**](/azure/templates/microsoft.network/vpnServerConfigurations)

>[!NOTE]
> 此 ARM 模板不会创建混合连接所需的客户端资源。 部署模板后，仍然需要创建和配置 P2S VPN 客户端和 VPN 分支（本地站点），并连接 ExpressRoute 线路。
>

若要查找更多模板，请参阅 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular)。

## <a name="deploy-the-template"></a><a name="deploy"></a>部署模板

若要正确部署此模板，必须使用“部署到 Azure”按钮和 Azure 门户，而不是其他方法，原因如下：

* 若要创建 P2S 配置，你需要上传根证书数据。 使用 PowerShell 或 CLI 时，数据字段不接受证书数据。
* 由于证书数据上传，使用 Cloud Shell 时此模板无法正常工作。
* 此外，你可在门户中轻松修改模板和参数，以适应 IP 地址范围和其他值。

1. 单击 **“部署到 Azure”** 。

   [![“部署到 Azure”](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2fquickstarts%2fmicrosoft.network%2fvirtual-wan-with-route-tables%2fazuredeploy.json)
1. 若要查看模板，请单击“编辑模板”。 在此页上，可调整某些值，例如地址空间或某些资源的名称。 选择“保存”来保存更改，或者选择“放弃” 。
1. 在模板页面上输入值。 对于此模板，需要提供 P2S 公共证书数据。 若要用本文进行练习，可使用此 .cer 文件中的以下数据作为两个中心的示例数据。 在模板运行且部署完成后，必须为自己的部署将此信息替换为公钥[证书数据](certificates-point-to-site.md#cer)，才能使用 P2S 配置。

   ```certificate-data
   MIIC5zCCAc+gAwIBAgIQGxd3Av1q6LJDZ71e3TzqcTANBgkqhkiG9w0BAQsFADAW
   MRQwEgYDVQQDDAtQMlNSb290Q2VydDAeFw0yMDExMDkyMjMxNTVaFw0yMTExMDky
   MjUxNTVaMBYxFDASBgNVBAMMC1AyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEF
   AAOCAQ8AMIIBCgKCAQEA33fFra/E0YmGuXLKmYcdvjsYpKwQmw8DjjDkbwhE9jcc
   Dp50e7F1P6Rxo1T6Hm3dIhEji+0QkP4Ie0XPpw0eW77+RWUiG9XJxGqtJ3Q4tyRy
   vBfsHORcqMlpV3VZOXIxrk+L/1sSm2xAc2QGuOqKaDNNoKmjrSGNVAeQHigxbTQg
   zCcyeuhFxHxAaxpW0bslK2hEZ9PhuAe22c2SHht6fOIDeXkadzqTFeV8wEZdltLr
   6Per0krxf7N2hFo5Cfz0KgWlvgdKLL7dUc9cjHo6b6BL2pNbLh8YofwHQOQbwt6H
   miAkEnx1EJ5N8AWuruUTByR2jcWyCnEAUSH41+nk4QIDAQABozEwLzAOBgNVHQ8B
   Af8EBAMCAgQwHQYDVR0OBBYEFJMgnJSYHH5AJ+9XB11usKRwjbjNMA0GCSqGSIb3
   DQEBCwUAA4IBAQBOy8Z5FBd/nvgDcjvAwNCw9h5RHzgtgQqDP0qUjEqeQv3ALeC+
   k/F2Tz0OWiPEzX5N+MMrf/jiYsL2exXuaPWCF5U9fu8bvs89GabHma8MGU3Qua2x
   Imvt0whWExQMjoyU8SNUi2S13fnRie9ZlSwNh8B/OIUUEtVhQsd4OfuZZFVH4xGp
   ibJMSMe5JBbZJC2tCdSdTLYfYJqrLkVuTjynXOjmz2JXfwnDNqEMdIMMjXzlNavR
   J8SNtAoptMOK5vAvlySg4LYtFyXkl0W0vLKIbbHf+2UszuSCijTUa3o/Y1FoYSfi
   eJH431YTnVLuwdd6fXkXFBrXDhjNsU866+hE
   ```

1. 输入值后，选择“查看 + 创建”。
1. 在“查看 + 创建”页面上，验证通过后选择“创建” 。
1. 需要大约 75 分钟才能完成部署。 可在模板概述页面上查看进度。 即使关闭门户，部署也将继续。

   :::image type="content" source="./media/quickstart-route-shared-services-template/template.png" alt-text="部署完成示例":::

## <a name="validate-the-deployment"></a><a name="validate"></a>验证部署

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 从左侧窗格中选择“资源组”。
1. 选择你在上一部分中创建的资源组。 在概述页面上，你将看到如下例所示的内容：:::image type="content" source="./media/quickstart-route-shared-services-template/resources.png" alt-text="资源示例" lightbox="./media/quickstart-route-shared-services-template/resources.png":::

1. 单击虚拟 WAN 以查看中心。 在虚拟 WAN 页面上，单击每个中心以查看连接和其他中心信息。
   :::image type="content" source="./media/quickstart-route-shared-services-template/hub.png" alt-text="中心示例" lightbox="./media/quickstart-route-shared-services-template/hub.png":::

## <a name="complete-the-hybrid-configuration"></a><a name="complete"></a>完成混合配置

该模板不会配置混合网络所需的部分设置。 根据要求，你需要完成以下配置和设置。

* [配置 VPN 分支 - 本地站点](virtual-wan-site-to-site-portal.md#site)
* [完成 P2S VPN 配置](virtual-wan-point-to-site-portal.md)
* [连接 ExpressRoute 线路](virtual-wan-expressroute-portal.md)

## <a name="clean-up-resources"></a>清理资源

当不再需要所创建的资源时，请将其删除。 由于存在依赖关系，必须按特定顺序删除某些虚拟 WAN 资源。 大约需要 30 分钟才能完成删除。

[!INCLUDE [Delete resources](../../includes/virtual-wan-resource-cleanup.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [完成 P2S VPN 配置](virtual-wan-point-to-site-portal.md)

> [!div class="nextstepaction"]
> [配置 VPN 分支 - 本地站点](virtual-wan-site-to-site-portal.md#site)

> [!div class="nextstepaction"]
> [连接 ExpressRoute 线路](virtual-wan-expressroute-portal.md)
