---
title: 提供概念证明
description: 了解如何提供概念证明，以使 Azure 开发测试实验室成功纳入企业环境中。
ms.topic: how-to
ms.date: 06/2/2020
ms.openlocfilehash: 03c8cc087f33ea6385fc0690f5634f33a3cbf0fd
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128589960"
---
# <a name="deliver-a-proof-of-concept"></a>提供概念证明 

Azure 开发测试实验室的重要方案之一是在云中启用开发和测试环境。 示例包括：

* 在云中创建开发人员桌面。
* 配置测试环境。
* 启用对虚拟机及其他 Azure 资源的访问权限。
* 设置沙盒区域以便开发人员学习和试验。

开发测试实验室还提供策略护栏和成本控制，使企业能够向遵守公司安全、法规和合规性策略的开发人员提供“自助服务 Azure”。 

每个企业对于如何将 Azure 开发测试实验室成功纳入企业环境有不同要求。 本文介绍企业为确保概念证明取得成功需要完成的最常见步骤。 概念证明是成功完成端到端部署的第一步。 

## <a name="getting-started"></a>入门 

概念证明提供入门。 花些时间来了解 Azure 和开发测试实验室很重要。  下面是一些入门资源： 

* [理解 Azure 门户](https://azure.microsoft.com/features/azure-portal/)
* [开发测试实验室基础知识](devtest-lab-overview.md)
* [开发测试实验室支持的方案](devtest-lab-guidance-get-started.md)
* [开发测试实验室企业文档](devtest-lab-guidance-prescriptive-adoption.md)
* [Azure 网络简介](../virtual-network/virtual-networks-overview.md)

## <a name="prerequisites"></a>先决条件 

若要使用开发测试实验室成功完成试点或概念证明，有几个前提条件： 

* “Azure 订阅”：企业通常有现有 [企业协议](https://azure.microsoft.com/pricing/purchase-options/enterprise-agreement/) 来启用对 Azure 的访问权限，且可以使用现有或新的开发测试实验室订阅。 或者，企业可以在试点期间使用 [Visual Studio 订阅](https://azure.microsoft.com/pricing/member-offers/visual-studio-subscriptions/)（利用免费的 Azure 额度）。 如果两个选项都不可用，则企业可以创建和使用[免费的 Azure 帐户](https://azure.microsoft.com/free/search/?&OCID=AID719825_SEM_g4lyBqgB&lnkd=Bing_Azure_Brand&msclkid=ecc4275a31b61375749e7a5322c20de8&dclid=CMGW5-m78-ICFaLt4QodmUwGtQ)。 如果有企业协议，则使用[企业开发/测试订阅](https://azure.microsoft.com/offers/ms-azr-0148p/)是获取 Windows 10/Windows 8.1 客户端操作系统访问权限及开发和测试工作负荷折扣率的极佳选择。 
* “Azure Active Directory 租户”：若要启用用户管理（例如，添加用户或添加实验室所有者），则这些用户必须是 Azure 订阅用于试点的 [Azure Active Directory 租户](https://azure.microsoft.com/services/active-directory/)的一部分。 企业通常会设置[混合标识](../active-directory/hybrid/whatis-hybrid-identity.md)，使用户能在云端使用本地标识，但这对于开发测试实验室试点项目而言非必需。 

## <a name="scoping-of-the-pilot"></a>试点范围 

开始实施之前，请务必规划试点。 事先知道资源不会无限持续会为试点用户设置适当期望。 

> [!IMPORTANT]
> 界定试点范围和预先设置期望值至关重要。

启动试点之前，请先回答以下重要问题： 

* 你想学习什么内容，试点成功后应该是什么样？ 
* 试点会涵盖哪些工作负荷或方案？ 必须只定义一个小集，以确保可以快速确定试点范围并完成。 
* 实验室中必须有哪些资源？ 例如：自定义映像、市场映像、策略和网络拓扑。 
* 哪些用户和团队参与到试点中来验证体验？  
* 试点的持续时间有多长？ 选择与计划范围相符的时间范围，如两周或一个月。 
* 试点完成后，试点期间使用的分配资源怎么办？ 你是否打算放弃试点资源？ 你可能会认为：
   
   “如果我们计划在试点结束时丢弃虚拟机和实验室，那么我们可以为试验设置单个订阅，并在其中完成所有工作，同时并行解决大规模推出问题。” 

有一种普遍的趋势是追求“完美”试点，以便使其完全代表服务在公司推出后的最终状态。 这是一种错误的假设。 越接近“完美”，越需要在就试点开始之前完成更多工作。 而试点的目的是便于在扩展和推出最终服务方面做出正确决定。 

试点应着重于选择最少的必要工作负荷和依赖项，以便回答 Azure 开发测试实验室服务是否是适合你的企业这一问题。 建议选择具有最少依赖项的最简工作负荷，以确保快速利落地取得成功。 如果无法做到这一点，请选择最具代表性的能公开潜在复杂性的工作负荷，以便在横向扩展阶段复制试点阶段的成功。 

下面的示例演示了一种范围设置良好的概念证明。 

## <a name="example-proof-of-concept-plan"></a>示例：概念验证计划 

本部分演示了一个示例，用于界定用于开发实验室的概念验证试点的范围。 

> [!TIP]
> 为了尽量减少项目设置失败的可能性，强烈建议不要跳过此部分中所述的示例。 

### <a name="overview"></a>概述 

我们计划基于开发测试实验室在 Azure 中开发新环境，使供应商将其用作独立于企业网络的隔离环境。 若要确定解决方案是否满足要求，我们将开发概念证明来验证端到端解决方案。 我们找到了几个供应商来试用并验证体验。 

### <a name="outcomes"></a>结果 

构建概念证明时，我们的首要关注点在于（试图实现的）的结果。 在概念证明结束时，我们期待： 

* 一款适用于供应商的端到端解决方案，能使用 Azure Active Directory (Azure AD) 中的来宾帐户访问 Azure 中的隔离环境。 环境具有保证其高工作效率所需的资源。 
* 对影响广泛使用和采用的潜在阻碍性问题进行了枚举和理解。
* 开发概念证明所涉及的人员对所有代码都有充分的了解。 这些人员还了解其中包括的附属品，对于广泛采用非常有信心。

### <a name="open-questions-and-prerequisites"></a>开放性问题和先决条件 

* 我们是否创建了可用于此项目的订阅？ 
* 我们是否确定了 Azure AD 租户和 Azure AD 全局管理员，来为与 Azure AD 相关的问题提供帮助和指导？ 
* 我们是否为从事该项目的人员提供了合作场所？ 

   * 源代码和脚本（如 Azure Repos） 
   * 文档（如 Microsoft 团队或 SharePoint）  
   * 会话（如 Microsoft 团队） 
   * 工作项（如 Azure Boards） 
* 供应商需要哪些资源？ 这包括网络上可用的应用程序，这些应用程序在本地虚拟机和其他所需服务器上都可用。 
* 虚拟机是否会加入 Azure 中的域？ 如果是，加入的域是 Azure Active Directory 域服务 (Azure AD DS) 还是其他？ 
* 是否确定了概念证明目标的团队或供应商？ 谁会是该环境的客户？
* 我们会为概念证明使用哪个 Azure 区域？ 
* 除了 IaaS (VM) ，我们是否有供应商可以通过开发测试实验室使用的服务列表？ 
* 我们计划如何对开发供应商/用户使用实验室进行培训？ 

### <a name="components-of-the-proof-of-concept-solution"></a>概念证明解决方案的组件 

我们希望解决方案具有以下组件： 

* 各种供应商团队将在 Azure 中使用一组实验室。
* 实验室连接到支持要求的网络基础结构。
* 供应商可以通过 Azure AD 和角色分配来访问实验室。
* 供应商可以成功连接到其资源。 具体而言，站点到站点 VPN 可在没有公共 IP 地址的情况下直接访问虚拟机。
* 一组项目包括供应商在虚拟机上所需的软件。

## <a name="additional-planning-and-design-decisions"></a>其他规划和设计决策 

在发布完整的开发测试实验室解决方案之前，必须做出一些重要的规划和设计决策。 在概念证明方面的工作经验有助于做出这些决策。 其他注意事项包括： 

* “订阅拓扑”：Azure 资源的企业级要求可能会超过[单个订阅中的可用配额](../azure-resource-manager/management/azure-subscription-service-limits.md)。 这就需要多个 Azure 订阅和/或服务请求以增加初始订阅限制。 提前决定如何将资源分布到多个订阅十分重要。 [订阅决策指南](/azure/architecture/cloud-adoption/decision-guides/subscriptions/)是有价值的资源，因为以后很难将资源移到另一个订阅。 例如，在创建实验室后，无法将其移动到另一个订阅。  
* “网络拓扑”：开发测试实验室自动创建的 [默认网络基础结构](../app-service/networking-features.md) 可能不足以满足企业用户的要求和限制。 常见情况是，可以查看 [Azure ExpressRoute 连接的虚拟网络](/azure/architecture/reference-architectures/hybrid-networking/)、跨订阅连接的[中心分支](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)，甚至[强制路由](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md)以确保只有本地连接。 开发测试实验室允许将现有虚拟网络连接到实验室，以便在实验室中创建新的虚拟机时使用。 
* “虚拟机的远程访问”：有许多选项可用于远程访问位于开发测试实验室中的虚拟机。 最简单的方法是使用公共 IP 或共享公共 IP。 这些是[实验室中的可用设置](devtest-lab-shared-ip.md)。 如果这些选项不够，还可以选择使用远程访问网关。 此选项显示在[开发测试实验室企业参考体系结构](devtest-lab-reference-architecture.md)上，并在[开发测试实验室远程桌面网关文档](configure-lab-remote-desktop-gateway.md)中进行了进一步说明。 企业还可以使用 ExpressRoute 或站点到站点 VPN 将其实验室连接到本地网络。 此选项可根据虚拟机的专用 IP 地址，实现直接到这些虚拟机的远程桌面或 SSH 连接，而不会向 Internet 公开。 
* “处理权限”：开发测试实验室中通常使用的两个主要权限是[所有者和实验室用户](devtest-lab-add-devtest-user.md)。 在广泛推出开发测试实验室之前，决定委托实验室中每个级别访问权限的人员很重要。 常见的模型是预算所有者（例如，团队主管）作为实验室所有者，团队成员作为实验室用户。 此模型使负责预算的人员（团队主管）能调整策略设置并将团队花费保持在预算内。  

## <a name="completing-the-proof-of-concept"></a>完成概念证明 

涵盖预期的学习后，就可以完成试点。 然后应该收集用户反馈，确定试点是否成功，并决定组织是否会在企业中的开发测试实验室大规模推出后继续进行。 这也是考虑自动部署开发测试实验室和关联资源的好时机，以确保大规模推出过程中的一致性。 

## <a name="next-steps"></a>后续步骤 

* [开发测试实验室企业文档](devtest-lab-guidance-prescriptive-adoption.md)
* [企业的参考体系结构](devtest-lab-reference-architecture.md)
* [纵向扩展开发测试实验室部署](devtest-lab-guidance-orchestrate-implementation.md)
* [协调 Azure 开发测试实验室的实施](devtest-lab-guidance-orchestrate-implementation.md)
