---
title: 创建应用服务环境
description: 了解如何创建应用服务环境。
author: ccompy
ms.assetid: 7690d846-8da3-4692-8647-0bf5adfd862a
ms.topic: article
ms.date: 09/07/2021
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: fdf74a88a34f9d497bc23b9fed00c8fa55b6875a
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129236079"
---
# <a name="create-an-app-service-environment"></a>创建应用服务环境
> [!NOTE]
> 本文介绍与独立 v2 应用服务计划一起使用的应用服务环境 v3
> 


[应用服务环境 (ASE)][Intro] 是在 Azure 虚拟网络 (VNet) 中注入的应用服务的单一租户部署。 ASE 的部署需要使用一个子网。 该子网不能用于 ASE 以外的任何其他对象。 

## <a name="before-you-create-your-ase"></a>创建 ASE 前的准备工作

创建 ASE 后，无法更改以下项：

- 位置
- 订阅
- 资源组
- 使用的 Azure 虚拟网络 (VNet)
- 使用的子网
- 子网大小
- 你的 ASE 的名称

该子网必须足够大，能够容纳 ASE 将缩放到的最大大小。 请选取一个足够大的子网，以支持最大缩放需求，因为在创建后不能更改它。 建议大小是 /24，包含 256 个地址。

## <a name="deployment-considerations"></a>部署注意事项

在部署 ASE 之前，需要考虑两个重要事项。 

- VIP 类型
- 部署类型

有两种不同的 VIP 类型：内部和外部。 使用内部 VIP 时，你的应用将在 ASE 子网的一个地址上被访问，应用不在公共 DNS 上。 在门户中创建过程中，可以选择为 ASE 创建 Azure 专用 DNS 区域。 使用外部 VIP 时，你的应用将位于面向公共 Internet 的地址上，并且应用在公共 DNS 中。 

有 3 种不同的部署类型； 

- 单个区域 
- 区域冗余
- 主机组

单区域 ASE 适用于 ASEv3 可用的所有区域。 当你有单个区域 ASE 时，将产生一个 Windows 独立 v2 实例的最低应用服务计划实例费用。 一旦有一个或多个实例，该费用就会消失。 这不是累加费用。 

在区域冗余 ASE 中，应用分布于同一区域中的三个区域。 区域冗余 ASE 可在部分支持 ASE 的区域中提供，主要受支持可用性区域的区域限制。 具有区域冗余 ASE 时，应用服务计划的最小大小为三个实例。 这可确保每个可用性区域中都有一个实例。 应用服务计划一次可扩展一个或多个实例。 扩展不一定以 3 个为单位，但只有当实例总数为 3 的倍数时，应用才会在所有可用性区域之间实现平衡。 区域冗余 ASE 具有三倍基础结构，并使用区域冗余组件，因此，如果三个区域中的两个由于某种原因而停机，你的工作负载仍然可用。 由于系统需求增加，区域冗余 ASE 的最低费用为 9 个实例。 如果 ASEv3 中的应用服务计划实例总数少于 9 个，则差额将按 Windows I1v2 计算。 如果有 9 个或更多实例，则无需额外付费即可使用区域冗余 ASE。 若要详细了解区域冗余，请参阅[区域和可用性区域][AZoverview]。

在主机组部署中，应用将部署到专用主机组。 专用主机组不是区域冗余的。 专用主机组部署使 ASE 可以部署在专用硬件上。 在专用主机组上使用 ASE 没有最低实例费用，但在预配 ASE 时，必须为主机组付费。 除此之外，当你创建计划和横向扩展时，你需要支付折后应用服务计划费率。可同时由应用服务计划和基础结构角色使用的专用主机部署的内核数有限。 ASE 的专用主机部署不能达到 ASE 中通常可用的 200 个实例总数。 可能的总实例数与应用服务计划实例总数加上基于负载的基础结构角色数相关。 

## <a name="creating-an-ase-in-the-portal"></a>在门户中创建 ASE

1. 若要创建 ASE，请在市场中搜索“应用服务环境 v3”。  

2. 基本信息：选择“订阅”，选择或创建“资源组”，并输入你的 ASE 的名称。  选择“虚拟 IP”类型。 如果选择“内部”，则入站 ASE 地址将是 ASE 子网中的地址。 如果选择“外部”，则入站 ASE 地址将是面向公共 Internet 的地址。 ASE 名称还将用于 ASE 的域后缀。 如果 ASE 名称是“contoso”并且你有一个内部 VIP ASE，则域后缀将为“contoso.appserviceenvironment.net”。 如果 ASE 名称是“contoso”并且你有一个外部 VIP，则域后缀将为“contoso.p.azurewebsites.net”。 

    ![应用服务环境的“创建基本信息”选项卡](./media/creation/creation-basics.png)

3. 托管：为“主机组部署”选择“启用”或“禁用”。 主机组部署用来选择专用硬件。 如果选择“启用”，则 ASE 会部署到专用硬件上。 部署到专用硬件上时，你需要在 ASE 创建期间为整个专用主机付费，然后按一个降低的价格为应用服务计划实例付费。 

    ![应用服务环境托管选择](./media/creation/creation-hosting.png)

4. 网络：选择或创建你的虚拟网络，选择或创建你的子网。 如果创建内部 VIP ASE，可以配置 Azure DNS 专用区域，以将域后缀指向你的 ASE。 [使用应用服务环境][UsingASE]中的“DNS”部分详细说明了如何手动配置 DNS。

    ![应用服务环境网络选择](./media/creation/creation-networking.png)

5. 查看和创建：检查你的配置是否正确，然后选择“创建”。 创建 ASE 最长可能需要两个小时。 

ASE 创建完成后，在创建应用时可以选择它作为位置。 若要详细了解如何在新 ASE 中创建应用或管理 ASE，请阅读[使用应用服务环境][UsingASE]

## <a name="dedicated-hosts"></a>专用主机

ASE 通常部署在那些在多租户虚拟机监控程序上预配的 VM 上。 如果你需要在专用系统（包括硬件）上进行部署，可以将 ASE 预配到专用主机上。 专用主机成对提供，以确保冗余。 基于专用主机的 ASE 部署的价格不同于普通部署。 专用主机需要付费，并且每个应用服务计划实例也需要付费。 主机组上的部署不是区域冗余。 若要部署到专用主机，请在“托管”选项卡上为“主机组部署”选择“启用”。

<!--Links-->
[Intro]: ./overview.md
[MakeASE]: ./creation.md
[ASENetwork]: ./networking.md
[UsingASE]: ./using.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[logalerts]: ../../azure-monitor/alerts/alerts-log.md
[AZoverview]: ../../availability-zones/az-overview.md
