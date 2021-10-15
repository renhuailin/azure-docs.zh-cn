---
title: 将 Azure ExpressRoute 与 Oracle 云基础结构进行连接 | Microsoft Docs
description: 将 Azure ExpressRoute 与 Oracle 云基础结构 (OCI) FastConnect 进行连接来启用跨云 Oracle 应用程序解决方案
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 03/16/2020
ms.author: rogardle
ms.openlocfilehash: c8c35abc680a37c3226872698d85be8853dd892d
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129456397"
---
# <a name="set-up-a-direct-interconnection-between-azure-and-oracle-cloud-infrastructure"></a>在 Azure 与 Oracle 云基础结构之间建立直接互连  

**适用于：** :heavy_check_mark: Linux VM 

为了创建[集成的多云体验](oracle-oci-overview.md)，Microsoft 和 Oracle 通过 [ExpressRoute](../../../expressroute/expressroute-introduction.md) 和 [FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm) 在 Azure 与 Oracle 云基础结构 (OCI) 之间提供直接互连。 通过 ExpressRoute 和 FastConnect 互连，客户可以在两个云之间体验具有低延迟和高吞吐量特点的专用直接连接。

> [!IMPORTANT]
> 在 2020 年 5 月之前，Oracle 将认证，在使用 Azure/Oracle 云互连解决方案时，这些应用程序可以在 Azure 中运行。
> * E-Business Suite
> * JD Edwards EnterpriseOne
> * PeopleSoft
> * Oracle Retail 应用程序
> * Oracle Hyperion 财务管理

下图概要显示了互连：

![跨云网络连接](https://user-images.githubusercontent.com/37556655/115093592-bced0180-9ecf-11eb-976d-9d4c7a1be2a8.png)

> [!NOTE]
> 关系图中所示的 ExpressRoute 连接是一种常规 [ExpressRoute 线路](../../../expressroute/expressroute-introduction.md)，支持所有功能，如 Global Reach。
> 

## <a name="prerequisites"></a>先决条件

* 若要在 Azure 与 OCI 之间建立连接，你必须有活动的 Azure 订阅和活动的 OCI 租户。

* 只有当 Azure ExpressRoute 的对等互连位置与 OCI FastConnect 的相邻或者二者在同一对等互连位置时，才能建立此连接。 请参阅[区域可用性](oracle-oci-overview.md#region-availability)。

## <a name="configure-direct-connectivity-between-expressroute-and-fastconnect"></a>在 ExpressRoute 与 FastConnect 之间配置直接连接

1. 在 Azure 订阅中的资源组下创建一个标准 ExpressRoute 线路。 
    * 创建 ExpressRoute 时，选择“Oracle Cloud FastConnect”作为服务提供商。 若要创建 ExpressRoute 线路，请参阅[分步指南](../../../expressroute/expressroute-howto-circuit-portal-resource-manager.md)。
    * Azure ExpressRoute 线路提供了细致的带宽选项，而 FastConnect 支持 1、2、5 或 10 Gbps。 因此，建议在 ExpressRoute 下选择其中一个匹配的带宽选项。

    ![创建 ExpressRoute 线路](media/configure-azure-oci-networking/exr-create-new.png)
1. 记下你的 ExpressRoute 服务密钥。 在配置 FastConnect 线路时你需要提供该密钥。

    ![ExpressRoute 服务密钥](media/configure-azure-oci-networking/exr-service-key.png)

    > [!IMPORTANT]
    > 一旦预配了 ExpressRoute 线路（即使“提供商状态”为“未预配” ），系统便会向你收取 ExpressRoute 费用。

1. 划分出两个专用 IP 地址空间，每个空间为 /30，不与 Azure 虚拟网络或 OCI 虚拟云网络 IP 地址空间重叠。 我们会将第一个 IP 地址空间称为主地址空间，将第二个 IP 地址空间称为辅助地址空间。 记下配置 FastConnect 线路时需要提供的地址。
1. 创建动态路由网关 (DRG)。 创建 FastConnect 线路时需要使用此网关。 有关详细信息，请参阅[动态路由网关](https://docs.cloud.oracle.com/iaas/Content/Network/Tasks/managingDRGs.htm)文档。
1. 在你的 Oracle 租户下创建一个 FastConnect 线路。 有关详细信息，请参阅 [Oracle 文档](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm)。
  
    * 在 FastConnect 配置下，选择“Microsoft Azure: ExpressRoute”作为提供商。
    * 选择在上一步预配的动态路由网关。
    * 选择要预配的带宽。 为了获得最佳性能，此带宽必须与创建 ExpressRoute 线路时选择的带宽匹配。
    * 在“提供商服务密钥”中，粘贴 ExpressRoute 服务密钥。
    * 使用在前面的一个步骤中划分出的第一个 /30 专用 IP 地址空间作为“主 BGP IP 地址”，使用第二个 /30 专用 IP 地址空间作为“辅助 BGP IP 地址”。
        * 将两个范围中的第一个可用地址分配给 Oracle BGP IP 地址（主地址和辅助地址），将第二个地址分配给客户 BGP IP 地址（从 FastConnect 的角度来看）。 第一个可用 IP 地址是 /30 地址空间中的第二个 IP 地址（第一个 IP 地址由 Microsoft 保留）。
    * 单击“创建”。
1. 使用路由表，通过动态路由网关将 FastConnect 链接到 Oracle 租户下的虚拟云网络。
1. 导航到 Azure，确保你的 ExpressRoute 线路的“提供商状态”已变为“已预配”，并确保已预配“Azure 专用”类型的对等互连。 这是以下步骤的先决条件。

    ![ExpressRoute 提供商状态](media/configure-azure-oci-networking/exr-provider-status.png)
1. 单击“Azure 专用”对等互连。 你会看到已根据你在设置 FastConnect 线路时输入的信息自动配置了对等互连详细信息。

    ![专用对等互连设置](media/configure-azure-oci-networking/exr-private-peering.png)

## <a name="connect-virtual-network-to-expressroute"></a>将虚拟网络连接到 ExpressRoute

1. 创建一个虚拟网络和虚拟网络网关（如果尚未这样做）。 有关详细信息，请参阅[分步指南](../../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)。
1. 通过执行 [Terraform 脚本](https://github.com/microsoft/azure-oracle/tree/master/InterConnect-2)或 PowerShell 命令来[配置 ExpressRoute FastPath](../../../expressroute/expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath)，以便设置虚拟网络网关与 ExpressRoute 线路之间的连接。

完成网络配置后，你可以通过以下方式验证配置的有效性：在 Azure 门户中，单击“ExpressRoute 专用对等互连”边栏选项卡下的“获取 ARP 记录”和“获取路由表”。

## <a name="automation"></a>自动化

Microsoft 创建了 Terraform 脚本来自动部署网络互连。 Terraform 脚本在执行之前需要通过 Azure 的身份验证，因为这些脚本需要拥有对 Azure 订阅的足够权限。 可以使用 [Azure Active Directory 服务主体](../../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)或 Azure CLI 来执行身份验证。 有关详细信息，请参阅 [Terraform 文档](https://www.terraform.io/docs/providers/azurerm/auth/azure_cli.html)。

可以在此 [GitHub 存储库](https://aka.ms/azureociinterconnecttf)中找到用于部署互连的 Terraform 脚本和相关文档。

## <a name="monitoring"></a>监视

在这两个云上安装代理，你可以利用 Azure [网络性能监视器 (NPM)](../../../expressroute/how-to-npm.md) 来监视端到端网络的性能。 NPM 可帮助你轻松查明并解决网络问题。

## <a name="delete-the-interconnect-link"></a>删除互连链接

若要删除互连，必须按照给定的具体顺序执行以下步骤。 否则，会导致 ExpressRoute 线路出现“故障状态”。

1. 删除 ExpressRoute 连接。 通过在你的连接的页面上单击“删除”图标来删除连接。 有关详细信息，请参阅 [ExpressRoute 文档](../../../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#clean-up-resources)。
1. 从 Oracle 云控制台中删除 Oracle FastConnect。
1. 删除 Oracle FastConnect 线路后，可以删除 Azure ExpressRoute 线路。

此时，删除和解除预配过程已完成。

## <a name="next-steps"></a>后续步骤

* 有关 OCI 与 Azure 之间的跨云连接的详细信息，请参阅 [Oracle 文档](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/azure.htm)。
* 使用 [Terraform 脚本](https://aka.ms/azureociinterconnecttf)通过 Azure 为目标 Oracle 应用程序部署基础结构，并配置网络互连。 
