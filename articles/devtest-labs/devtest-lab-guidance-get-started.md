---
title: 适合使用 Azure 开发测试实验室的常见方案
description: 本文提供了使用 Azure 开发测试实验室的主要方案以及开始在你的组织中使用该服务的两条常规路径。
ms.topic: conceptual
ms.date: 06/20/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: fc4c7ca1cdbc8e503ad6c0d94fcec2d9697baae4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128657474"
---
# <a name="popular-scenarios-for-using-azure-devtest-labs"></a>适合使用 Azure 开发测试实验室的常见方案
根据企业的需要，可以配置开发测试实验室来满足不同的要求。  本文介绍常见方案。 每个方案都涵盖使用开发测试实验室和资源来实现这些方案所带来的好处。  

- 开发人员桌面
- 测试环境
- 培训会话、动手实验和黑客松
- 沙盒调查
- 教室实验室

## <a name="developer-desktops"></a>开发人员桌面
开发人员针对不同的项目对开发计算机有不同的要求。 使用开发测试实验室，开发人员可以访问配置的按需虚拟机来满足他们最常用的方案。 开发测试实验室具有以下优点：

- 组织可以提供在各个团队之间确保一致性的常用开发计算机。
- 开发人员可以快速按需预配其开发计算机，也可以[声明预配置的现有计算机](devtest-lab-add-claimable-vm.md)。
- 开发人员能够以自助方式预配资源，不需要具有订阅级权限。
- IT 或管理员可以[预先定义网络拓扑](devtest-lab-configure-vnet.md)，并且开发人员能够直接以简单直观的方式使用它，不需要任何特殊访问。
- 开发人员可以根据需要轻松[自定义](devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm)其开发计算机。
- 管理员可以控制成本，只需确保：
    - 开发人员[可获取的 VM 数量不能超过](devtest-lab-set-lab-policy.md#set-virtual-machines-per-user)开发所需的 VM 数量
    - [VM 在不使用时关闭](devtest-lab-set-lab-policy.md#set-auto-shutdown)
    - 仅[允许为特定的实验室使用一部分 VM 实例大小](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes)
    - 管理每个实验室的[成本目标和通知](devtest-lab-configure-cost-management.md)。

如需深入阅读，请参阅[使用面向开发人员的 Azure 开发测试实验室](devtest-lab-developer-lab.md)。 

## <a name="test-environments"></a>测试环境
在整个企业中创建和管理测试环境可能需要做大量工作。 使用开发测试实验室，可以轻松创建、更新或复制测试环境。 这使得团队在需要时可以访问完全配置的环境。 在此方案中，开发测试实验室具有以下优势：

- 组织可以提供在各个团队之间确保一致性的常用测试环境。
- 测试人员可使用可重用模板快速预配 Windows 和 Linux 环境，从而测试最新版应用程序。
- 管理员可以将实验室连接到 Azure DevOps 来实现 DevOps 方案
- 实验室所有者可以控制成本，只需确保：
    - [环境中的 VM 在不使用时关闭](devtest-lab-set-lab-policy.md#set-auto-shutdown)
    - 仅[允许为特定的实验室使用一部分 VM 实例大小](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes)
    - 管理每个实验室的[成本目标和通知](devtest-lab-configure-cost-management.md)。

有关详细信息，请参阅[将 Azure 开发测试实验室用于 VM 和 PaaS 测试环境](devtest-lab-test-env.md)。

## <a name="sandboxed-investigations"></a>沙盒调查
开发人员经常调查不同的技术或基础结构设计。 默认情况下，在其自己的资源组中创建使用开发测试实验室创建的所有环境。 开发测试实验室用户仅拥有对这些资源的读取访问权限。 但是，对于需要更多控制的开发人员，可以更新实验室范围内的设置，以便为其创建的每个环境的发起开发测试实验室用户提供[参与者权限](https://azure.microsoft.com/updates/azure-devtest-labs-view-and-set-access-rights-to-an-environment-rg/)。  使用开发测试实验室，可向开发人员提供对在实验室中创建的环境的参与者权限。  此方案允许开发人员根据其开发或测试环境的需要添加和/或更改 Azure 资源。 使用 [按资源划分的成本](devtest-lab-configure-cost-management.md#view-cost-by-resource)页，实验室所有者可以跟踪用于调查的每个环境的成本。

有关详细信息，请参阅[设置对环境资源组的访问权限](https://aka.ms/dtl-sandbox)。

## <a name="trainings-hands-on-labs-and-hackathons"></a>培训、动手实验和黑客松 
Azure 开发测试实验室中的实验室是很棒的容器，用于容纳暂时性的活动，例如研讨会、动手实验室、培训和黑客松。  此服务允许你创建实验室，可在其中提供自定义模板，每个学员都可使用该模板创建相同和隔绝的培训环境。 在此方案中，开发测试实验室具有以下优势：

- [策略](devtest-lab-set-lab-policy.md)可确保学员仅获取所需的资源（如虚拟机）数。
- 预配置和创建的计算机是通过学员中的单个操作来[声明](devtest-lab-add-claimable-vm.md)的。
- 实验室通过访问[实验室的 URL](/azure/devtest-labs/devtest-lab-faq#how-do-i-share-a-direct-link-to-my-lab) 与学员共享。
- 虚拟机上的[过期日期](devtest-lab-add-vm.md#steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs)可确保在不再需要计算机时将其删除。
- 在课程结束后，可以轻松[删除实验室](devtest-lab-delete-lab-vm.md#delete-a-lab)和所有[相关的资源](/azure/devtest-labs/devtest-lab-faq#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab)。

有关详细信息，请参阅[使用 Azure 开发测试实验室进行培训](devtest-lab-training-lab.md)。  

## <a name="proof-of-concept-vs-scaled-deployment"></a>概念证明与按比例部署
在你决定探究开发测试实验室后，可以沿两条常规路径继续前进 – 概念验证与按比例部署。  

**规模化部署** 包括数周/数月的审查和规划，其目的是将开发测试实验室部署到具有数百或数千个开发人员的整个企业。

概念验证部署专注于集中单个团队的力量来实现组织价值。 虽然想想规模化部署比较诱人，但是该方法通常比概念验证选项更容易失败。 因此，我们建议从小型部署开始，从第一个团队那里学习经验，对两到三个另外的团队重复相同的方法，然后根据所获得的知识规划规模化部署。 为实现成功的概念验证，我们建议你挑选一两个团队，识别其方案（开发环境与测试环境），记录其当前的使用案例，并部署开发测试实验室。

## <a name="next-steps"></a>后续步骤
请阅读以下文章：

- [开发测试实验室概念](devtest-lab-concepts.md)
- [开发测试实验室常见问题解答](devtest-lab-faq.yml)