---
title: 创建应用服务环境
description: 了解如何创建应用服务环境。
author: ccompy
ms.assetid: 7690d846-8da3-4692-8647-0bf5adfd862a
ms.topic: article
ms.date: 11/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 12c9fd43933f9f54e013752c5fe482ce31650536
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2020
ms.locfileid: "95238388"
---
# <a name="create-an-app-service-environment"></a>创建应用服务环境

> [!NOTE]
> 本文介绍应用服务环境 v3 (预览版) 
> 

[应用服务环境 (ASE) ][Intro]是应用服务的单租户部署，它将插入到 Azure 虚拟网络 (VNet) 中。  ASEv3 仅支持在 Vnet 中公开专用地址上的应用。 预览期间创建 ASEv3 时，会向订阅添加三个资源。

- 应用服务环境
- Azure DNS 专用区域
- 专用终结点

ASE 的部署需要使用两个子网。  一个子网将持有专用终结点。  此子网可用于 Vm 等其他功能。  另一个子网用于从 ASE 发出的出站调用。  此子网不能用于 ASE 以外的任何其他内容。 

## <a name="before-you-create-your-ase"></a>创建 ASE 前的准备工作

创建 ASE 后，无法更改：

- 位置
- 订阅
- 资源组
- Azure 虚拟网络 (VNet) 使用
- 使用的子网
- 子网大小
- ASE 的名称

出站子网必须足够大，以容纳可缩放 ASE 的最大大小。 选择足够大的子网，以支持最大的缩放需求，因为在创建后不能更改它。 建议大小为/24，其中包含256地址。

创建 ASE 后，可以向其添加应用。 如果 ASEv3 中没有应用服务计划，则会产生费用，因为 ASE 中有一个 I1v2 应用服务计划实例。  

ASEv3 仅在选择区域中提供。 随着预览版的发展，将添加更多区域。 

## <a name="creating-an-ase-in-the-portal"></a>在门户中创建 ASE

1. 若要创建 ASEv3，请在 marketplace **(预览版)** 中搜索应用服务环境。  
2. 基础知识：选择订阅，选择或创建资源组，并输入 ASE 的名称。  ASE 名称还将用于 ASE 的域后缀。  如果 ASE 名称为 *contoso* ，则域后缀将为 *contoso.appserviceenvironment.net*。  此名称将在 ASE 部署到的 Vnet 所使用的 Azure DNS 专用区域中自动设置。 

    ![应用服务环境创建基本信息选项卡](./media/creation/creation-basics.png)

3. 托管：选择 OS 首选项、主机组部署。 OS 首选项指示最初将用于此 ASE 中的应用的操作系统。 可以在创建 ASE 后添加其他 OS 的应用。 主机组部署用于选择专用硬件。 对于 ASEv3，可以选择 "已启用"，然后选择 "在专用硬件上土地"。 需要为使用 ASE 创建的整个专用主机计费，并为应用服务计划实例支付较小的价格。 

    ![应用服务环境承载选定内容](./media/creation/creation-hosting.png)

4. 网络：选择或创建虚拟网络，选择或创建入站子网，选择或创建出站子网。 用于出站的任何子网都必须为空，并且必须委托给 hostingEnvironments/。 如果通过门户创建子网，子网会自动委托给你。

    ![应用服务环境网络选择](./media/creation/creation-networking.png)

5. 查看和创建：检查您的配置是否正确，然后选择 "创建"。 需要大约一小时才能创建 ASE。 

    ![应用服务环境查看和创建](./media/creation/creation-review.png)

ASE 创建完成后，可以在创建应用时将其选作位置。 若要详细了解如何在新 ASE 中创建应用，请阅读 [使用应用服务环境][UsingASE]

## <a name="os-preference"></a>OS 首选项
在 ASE 中，可以有 Windows 应用、Linux 应用或两者。 在 ASEv2 中，在创建过程中选择的初始首选项会在 ASE 创建过程中为该操作系统添加高可用性基础结构。 若要添加其他 OS 的应用，只需按常规方式创建应用，然后选择所需的 OS。 在 ASEv3 中，这不会影响后端行为 appreciatively。  

## <a name="dedicated-hosts"></a>专用主机
ASE 通常部署在多租户虚拟机监控程序上预配的 Vm 上。 如果需要在专用系统（包括硬件）上部署，可以将 ASE 预配到专用主机上。 在最初的 ASEv3 预览版中，专用主机会成对出现。 如果区域允许，每个专用主机都在单独的可用性区域中。 基于主机的专用 ASE 部署价格不同于正常。 专用主机需要支付费用，然后对每个应用服务计划实例收费。  

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
[logalerts]: ../../azure-monitor/platform/alerts-log.md
