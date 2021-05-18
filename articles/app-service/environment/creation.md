---
title: 创建应用服务环境
description: 了解如何创建应用服务环境。
author: ccompy
ms.assetid: 7690d846-8da3-4692-8647-0bf5adfd862a
ms.topic: article
ms.date: 11/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 021c85037ea105ff7d8ff642e3a9f28ed3ebe991
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "100594099"
---
# <a name="create-an-app-service-environment"></a>创建应用服务环境

> [!NOTE]
> 本文介绍应用服务环境 v3（预览版）
> 

[应用服务环境 (ASE)][Intro] 是在 Azure 虚拟网络 (VNet) 中注入的应用服务的单一租户部署。  ASEv3 仅支持在 VNet 中的专用地址上公开应用。 在预览期间创建 ASEv3 时，会将这些资源添加到你的订阅。

- 应用服务环境
- 专用终结点

ASE 的部署需要使用两个子网。  一个子网将容纳专用终结点。  此子网可用于 VM 之类的其他对象。  另一个子网用于从 ASE 进行的出站调用。  该子网不能用于 ASE 以外的任何其他对象。 

## <a name="before-you-create-your-ase"></a>创建 ASE 前的准备工作

创建 ASE 后，无法更改以下项：

- 位置
- 订阅
- 资源组
- 使用的 Azure 虚拟网络 (VNet)
- 使用的子网
- 子网大小
- 你的 ASE 的名称

出站子网必须足够大，能够容纳 ASE 将缩放到的最大大小。 请选取一个足够大的子网，以支持最大缩放需求，因为在创建后不能更改它。 建议大小是 /24，包含 256 个地址。

创建 ASE 后，可以向其中添加应用。 如果 ASEv3 中没有应用服务计划，则会产生费用，就像 ASE 中有 I1v2 应用服务计划的一个实例一样。  

ASEv3 仅在特选区域提供。 在预览版向正式版过渡的过程中，会添加更多区域。 

## <a name="creating-an-ase-in-the-portal"></a>在门户中创建 ASE

1. 若要创建 ASEv3，请在市场中搜索“应用服务环境(预览版)”。  
2. 基本信息：选择“订阅”，选择或创建“资源组”，并输入你的 ASE 的名称。  ASE 名称还将用于 ASE 的域后缀。  如果 ASE 名称为 contoso，则域后缀为 contoso.appserviceenvironment.net。  此名称将在 ASE 部署到的 VNet 所使用的 Azure DNS 专用区域中自动设置。 

    ![应用服务环境的“创建基本信息”选项卡](./media/creation/creation-basics.png)

3. 托管：选择 OS 首选项、主机组部署。 OS 首选项指示在此 ASE 中你最初将为应用使用的操作系统。 可以在创建 ASE 后添加其他 OS 的应用。 主机组部署用来选择专用硬件。 对于 ASEv3，你可以选择“启用”，然后在专用硬件上部署。 将对创建 ASE 的整个专用主机收取费用，然后对你的应用服务计划实例降价。 

    ![应用服务环境托管选择](./media/creation/creation-hosting.png)

4. 网络：选择或创建你的虚拟网络、入站子网、出站子网。 用于出站的任何子网必须为空，并且必须委托给 Microsoft.Web/hostingEnvironments。 如果你通过门户创建子网，系统会自动为你委托子网。

    ![应用服务环境网络选择](./media/creation/creation-networking.png)

5. 查看和创建：检查你的配置是否正确，然后选择“创建”。 创建 ASE 需要大约一小时。 

    ![应用服务环境的“查看和创建”](./media/creation/creation-review.png)

ASE 创建完成后，在创建应用时可以选择它作为位置。 若要详细了解如何在新 ASE 中创建应用，请阅读[使用应用服务环境][UsingASE]

## <a name="os-preference"></a>OS 首选项
在 ASE 中，可以存在 Windows 应用、Linux 应用或同时存在这两者。 在 ASEv2 中，在创建过程中选择的初始首选项会在 ASE 创建过程中为该 OS 添加高可用性基础结构。 若要添加其他 OS 的应用，只需照常创建应用，然后选择所需 OS 即可。 在 ASEv3 中，这不会影响后端行为，这一点令人欣慰。  

## <a name="dedicated-hosts"></a>专用主机
ASE 通常部署在那些在多租户虚拟机监控程序上预配的 VM 上。 如果你需要在专用系统（包括硬件）上进行部署，可以将 ASE 预配到专用主机上。 在最初的 ASEv3 预览版中，专用主机成对出现。 如果区域允许，每台专用主机都会位于单独的可用性区域中。 基于专用主机的 ASE 部署的价格不同于普通部署。 专用主机需要付费，并且每个应用服务计划实例也需要付费。  

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
