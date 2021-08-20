---
title: 关于 Azure 网络功能管理器
description: 了解 Azure 网络功能管理器，它是一种完全托管的云原生业务流程服务，让你可以在 Azure Stack Edge Pro GPU 上部署和预配网络功能，使用 Azure 门户获得一致的混合体验。
author: cherylmc
ms.service: network-function-manager
ms.topic: overview
ms.date: 06/16/2021
ms.author: cherylmc
ms.custom: references_regions
ms.openlocfilehash: df063a5b19e9287e1275683719d2d41a13200b5e
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2021
ms.locfileid: "113128923"
---
# <a name="what-is-azure-network-function-manager-preview"></a>什么是 Azure 网络功能管理器？ （预览版）


Azure 网络功能管理器提供 [Azure 市场](https://azure.microsoft.com/marketplace/)体验，将网络功能（如移动数据包核心、SD-WAN Edge 和 VPN 服务）部署到在本地环境中运行的 [Azure Stack Edge 设备](https://azure.microsoft.com/products/azure-stack/edge/)。 现在可以直接从 Azure 管理门户在边缘设备上快速部署专用移动网络服务或 SD-WAN 解决方案。 网络功能管理器从不断增长的[合作伙伴](#partners)生态系统中引入网络功能。 [Azure Stack Edge Pro GPU](../databox-online/azure-stack-edge-gpu-overview.md) 支持此预览版。 

## <a name="preview-features"></a><a name="preview"></a>预览功能

* 一致的管理体验 - 网络功能管理器为在企业边缘部署的不同合作伙伴的网络功能提供一致的 Azure 管理体验。 这让你可以简化治理和管理。 你可以使用熟悉的 Azure 工具和 SDK，通过声明性模板自动部署网络功能。 还可以将 Azure 基于角色的访问控制应用于 [Azure RBAC](../role-based-access-control/overview.md)，从而在 Azure Stack Edge 设备上全局部署网络功能。

* 5G 网络功能的 Azure 市场体验 - 通过从 Azure 市场直接选择 LTE 和 5G 移动数据包核心网络功能，在 Azure Stack Edge 设备上加速部署专用移动网络解决方案。

* SD-WAN 和 VPN 解决方案的云到边缘无缝体验 - 网络功能管理器将公有云中你所熟悉的市场网络功能的 Azure 管理体验扩展到边缘设备。 这样，当你选择部署在 Azure 公有云或 Azure Stack Edge 设备上的 SD-WAN 和 VPN 合作伙伴网络功能时，就可以利用一致的部署体验。

* 企业边缘的网络功能的 Azure 托管应用程序体验 - 网络功能管理器可为 Azure Stack Edge 设备上的专用网络功能（如移动数据包核心）提供简化的部署体验。 我们已经对合作伙伴提供的已批准的网络功能映像的生命周期管理进行了预先验证。 你可以确信，你的网络功能资源在整个系列的设备中有一致的部署状态。 有关详细信息，请参阅 [Azure 托管应用程序](../azure-resource-manager/managed-applications/overview.md)。

* 网络加速和为网络功能选择动态或静态 IP 分配 - 网络功能管理器和 [Azure Stack Edge Pro](../databox-online/azure-stack-edge-gpu-overview.md) 支持提高网络功能工作负载的网络性能。 现在，在接入点网络和用户平面网络上，可以使用更快的数据路径性能在 Azure Stack Edge 设备上部署专用网络功能，如移动数据包核心。 还可以为网络功能部署的不同虚拟接口选择静态或动态 IP 分配。 请与你的网络功能合作伙伴确认是否支持这些网络功能。  

## <a name="azure-managed-applications-for-network-functions"></a><a name="managed"></a>用于网络功能的 Azure 托管应用程序

可使用网络功能管理器进行部署的网络功能专门设计为在 Azure Stack Edge 设备上运行。 网络功能产品/服务作为 [Azure 托管应用程序](../azure-resource-manager/managed-applications/overview.md)发布到 Azure 市场。 客户可以直接从 [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/)部署产品/服务，也可以通过 Azure 门户从网络功能管理器设备资源进行部署。 

:::image type="content" source="./media/overview/managed-app-workflow.png" alt-text="托管应用程序工作流的示意图。":::

所有可使用网络功能管理器进行部署的网络功能产品/服务都有一个在 Azure 市场中可用的托管应用程序。 借助托管应用程序，合作伙伴可以：

* 使用 Azure 门户体验为其网络功能构建自定义部署体验。 

* 提供专用资源管理器模板，使其可以直接在 Azure Stack Edge 设备中创建网络功能。 

* 直接或通过 Azure 市场对软件许可成本计费。 

* 公开自定义属性和资源计量。 

根据设备部署、配置许可和管理需求不同，网络功能合作伙伴可能会创建不同的资源。 与所有 Azure 托管应用程序一样，当客户在 Azure Stack Edge 设备中创建网络功能时，会在其订阅中创建两个资源组： 

* 客户资源组 - 此资源组将包含一个针对托管应用程序的应用程序占位符。 合作伙伴可以在该组中公开其在此处选择的任何客户属性。 

* 受管理资源组 - 你不能直接在此资源组中配置或更改资源，因为这是由托管应用程序的发布服务器控制的。 此资源组将包含网络功能管理器网络功能 资源。

:::image type="content" source="./media/overview/managed-app-resource-groups.png" alt-text="托管应用程序资源组的示意图。":::

## <a name="network-function-configuration-process"></a><a name="configuration"></a>网络功能配置过程 

通过网络功能管理器提供其 Azure 托管应用程序的网络功能合作伙伴提供了一种体验：在部署过程中自动配置网络功能。 在托管应用程序部署成功并且网络功能实例已预配到 Azure Stack Edge 后，网络功能可能需要的任何其他配置都必须通过网络功能合作伙伴管理门户完成。 请咨询你的网络功能合作伙伴，了解 Azure Stack Edge 设备上部署的网络功能的端到端管理体验。 

## <a name="prerequisites"></a><a name="prereq"></a>先决条件

### <a name="azure-stack-edge-pro-with-gpu-installed-and-activated"></a><a name="edge-pro"></a>已安装并激活 Azure Stack Edge Pro GPU

在 Azure Stack Edge Pro 设备上启用了 Azure 网络功能管理器服务。 在部署网络功能之前，请确认已安装并激活 Azure Stack Edge Pro。 Azure Stack Edge 资源必须部署在网络功能管理器资源支持的区域中。 有关详细信息，请参阅[区域可用性](#regions)。 请务必按照 Azure Stack Edge Pro [快速入门](../databox-online/azure-stack-edge-gpu-quickstart.md)和[教程](../databox-online/azure-stack-edge-gpu-deploy-checklist.md)中的所有步骤进行操作。

此外，还应验证位于 Azure 管理门户中 Azure Stack Edge 资源的“属性”部分中的设备“状态”是否为“联机”。

:::image type="content" source="./media/overview/properties.png" alt-text="属性的屏幕截图。" lightbox="./media/overview/properties.png":::

### <a name="partner-prerequisites"></a><a name="partner-prereq"></a>合作伙伴先决条件 

客户可以选择一个或多个网络功能管理器[合作伙伴](#partners)，在 Azure Stack Edge 设备上部署其网络功能。 对于将网络功能部署到 Azure Stack Edge 设备，每个合作伙伴都有自己的网络要求。 请参阅网络功能合作伙伴提供的产品文档，完成以下配置任务：

* [在不同端口上配置网络](../databox-online/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)。
* [在 Azure Stack Edge 设备上启用计算网络](../databox-online/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#enable-compute-network)。 


### <a name="azure-account"></a><a name="account"></a>Azure 帐户

Azure 网络功能管理器服务由网络功能管理器设备和网络功能管理器网络功能资源组成。 设备和网络功能资源均包含在 Azure 订阅中。 用于激活 Azure Stack Edge Pro 设备和网络功能管理器资源的 Azure 订阅 ID 应该是相同的。 

通过填写 [Azure 功能管理器预览表单](https://go.microsoft.com/fwlink/?linkid=2163583)，在网络功能管理器预览版中载入 Azure 订阅 ID。 对于预览版，Azure 网络功能管理器合作伙伴必须启用相同的 Azure 订阅 ID，才能从 Azure 市场部署其网络功能。 确保 Azure 订阅 ID 在这两个位置载入以用于预览版。 

## <a name="resource-provider-registration-and-permissions"></a><a name="permissions"></a>资源提供程序注册和权限

Azure 网络功能管理器资源位于 Microsoft.HybridNetwork 资源提供程序中。 载入 Azure 订阅 ID 以用于网络功能管理器服务预览版后，请将订阅 ID 注册到 Microsoft.HybridNetwork 资源提供程序。 有关如何注册的详细信息，请参阅 [Azure 资源提供程序和类型](../azure-resource-manager/management/resource-providers-and-types.md)。

必须将用于创建网络功能管理器设备资源的帐户分配给一个自定义角色，该角色被分配了下表中的必要操作。 有关详细信息，请参阅[自定义角色](../role-based-access-control/custom-roles.md)。

| 名称 | 操作|
|---|---|
| Microsoft.DataBoxEdge/dataBoxEdgeDevices/read|需要读取将在其上部署网络功能的 Azure Stack Edge 资源。 |
|Microsoft.DataBoxEdge/dataBoxEdgeDevices/getExtendedInformation/action |需要读取 Azure Stack Edge 资源的“属性”部分。 |
|Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/write |需要在 Azure Stack Edge 资源上创建网络功能管理器设备资源。|
| Microsoft.HybridNetwork/devices/* | 需要创建、更新、删除网络功能管理器设备资源。 |

必须向用于创建 Azure 托管应用程序资源的帐户分配[自定义角色](../role-based-access-control/custom-roles.md)，该角色被分配了下表中的必要操作： 

|名称 |操作 |
|---|---|
|[托管应用程序参与者角色](../role-based-access-control/built-in-roles.md#managed-application-contributor-role)|需要创建托管应用资源。|

## <a name="managed-identity"></a><a name="managed-identity"></a>托管标识 

通过网络功能管理器提供其 Azure 托管应用程序的网络功能合作伙伴提供了一种体验，让你可以部署被附加到现有网络功能管理器设备资源的托管应用程序。 当你在 Azure 门户中部署合作伙伴托管的应用程序时，需要提供一个可访问网络功能管理器设备资源的 Azure 用户分配的托管标识资源。 用户分配的托管标识允许托管应用程序资源提供程序和网络功能发布服务器对在受管理资源组外部部署的网络功能管理器设备资源拥有适当的权限。 有关详细信息，请参阅[在 Azure 门户中管理用户分配的托管标识](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)。

若要创建用户分配的托管标识以部署网络功能，请执行以下操作：

1. 创建用户分配的托管标识，并将其分配给具有 Microsoft.HybridNetwork/devices/join/action 权限的自定义角色。 有关详细信息，请参阅[在 Azure 门户中管理用户分配的托管标识](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)。

1. 在 Azure 门户中创建合作伙伴的托管应用程序时，提供此托管标识。 有关详细信息，请参阅[使用 Azure 门户为托管标识分配对资源的访问权限](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md)。


## <a name="port-requirements-and-firewall-rules"></a><a name="port-firewall"></a>端口要求和防火墙规则

在 Azure Stack Edge 上运行的网络功能管理器 (NFM) 服务需要出站连接到 NFM 云服务进行流量管理，以部署网络功能。 NFM 与 Azure Stack Edge 服务完全集成。 查看 [Azure Stack Edge](../databox-online/azure-stack-edge-gpu-system-requirements.md#networking-port-requirements) 设备的网络端口要求和防火墙规则。  

网络功能合作伙伴对于防火墙和端口配置规则具有不同的要求，用于管理到合作伙伴管理门户的流量。 请咨询网络功能合作伙伴，了解具体的要求。  

## <a name="region-availability"></a><a name="regions"></a>上市区域

Azure Stack Edge 资源、Azure 网络功能管理器设备以及用于网络功能的 Azure 托管应用程序应位于同一 Azure 区域中。 Azure Stack Edge Pro GPU 物理设备不需要在同一区域中。 

* 资源可用性 - 对于预览版，网络功能管理器资源在以下区域中提供：

   [!INCLUDE [Preview- available regions](../../includes/network-function-manager-regions-include.md)]

* 设备可用性 - 有关提供 Azure Stack Edge Pro GPU 设备的所有国家/地区列表，请转到 [Azure Stack Edge Pro GPU 定价](https://azure.microsoft.com/pricing/details/azure-stack/edge/#azureStackEdgePro)页。 在“Azure Stack Edge Pro”选项卡上，查看“可用性” ****  部分。

对于当前版本，网络功能管理器是一项区域服务。 对于区域范围的中断，网络功能管理器资源的管理操作将受到影响，但在 Azure Stack Edge 设备上运行的网络功能不会受到区域范围内中断的影响。 

## <a name="partner-solutions"></a><a name="partners"></a>合作伙伴解决方案

请参阅网络功能管理器的[合作伙伴页](partners.md)，了解为专用移动网络、SD-WAN 和 VPN 解决方案提供其市场托管应用程序的不断发展的合作伙伴生态系统。

## <a name="faq"></a><a name="faq"></a>常见问题解答

有关常见问题解答，请参阅[网络功能管理器常见问题解答](faq.md)。

## <a name="next-steps"></a>后续步骤

[创建设备资源](create-device.md)。
