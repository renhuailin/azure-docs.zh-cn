---
title: Azure 安全中心就绪情况路线图 | Microsoft Docs
description: 本文档提供了 Azure 安全中心的就绪情况路线图。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: fece670cc-df70-445d-9773-b32cbaba8d4a
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/03/2018
ms.author: memildin
ms.openlocfilehash: 42ca1bf538ff7a8a7479f8db041d35d4ea68692e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121731091"
---
# <a name="azure-security-center-readiness-roadmap"></a>Azure 安全中心就绪情况路线图
本文档提供的就绪情况路线图有助于 Azure 安全中心入门。

## <a name="understanding-security-center"></a>了解安全中心
Azure 安全中心为 Azure、本地和其他云中运行的工作负载提供统一的安全管理和高级威胁防护功能。 

使用以下资源，开始使用安全中心。

项目
- [Azure 安全中心简介](security-center-introduction.md)
- [Azure 安全中心快速入门指南](security-center-get-started.md)

视频
- [简介视频](https://azure.microsoft.com/resources/videos/introduction-to-azure-security-center/)
- [安全中心的防护、检测和响应功能概述](https://azure.microsoft.com/resources/videos/azurecon-2015-new-azure-security-center-helps-you-prevent-detect-and-respond-to-threats/)

## <a name="planning-and-operations"></a>规划和操作

若要充分利用安全中心，必须了解在需要进行安全操作、监视、管理和事件响应的情况下，组织中的不同个人或团队是如何使用服务的。

在规划和操作过程中，请参考以下资源。

- [Azure 安全中心规划和操作指南](security-center-planning-and-operations-guide.md)


### <a name="onboarding-computers-to-security-center"></a>将计算机载入到安全中心
安全中心会自动检测未受 Azure Defender 保护的任何 Azure 订阅或工作区。 其中包括使用安全中心免费层的 Azure 订阅和未启用安全解决方案的工作区。

在载入过程中，请参考以下资源。

- [载入非 Azure 计算机](quickstart-onboard-machines.md)
- [Azure Security Center Hybrid - Overview](https://youtu.be/NMa4L_M597k)（Azure 安全中心混合层 - 概述）

## <a name="mitigating-security-issues-using-security-center"></a>使用安全中心缓解安全问题
安全中心会自动收集、分析以及整合 Azure 资源、网络和所连合作伙伴解决方案（如，防火墙和终结点保护解决方案）的日志数据，检测真正的威胁并减少误报。

若要管理安全警报和保护资源，请参考以下资源。

项目    
- [保护 Azure 安全中心中的网络](./security-center-network-recommendations.md)
- [在 Azure 安全中心保护 Azure SQL 服务和数据](./security-center-remediate-recommendations.md)


视频    
- [Mitigating Security Issues using Azure Security Center](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Mitigating-Security-Issues-using-Azure-Security-Center)（使用 Azure 安全中心缓解安全问题）

### <a name="security-center-for-incident-response"></a>负责事件响应的安全中心
为降低成本并减少破坏，必须在攻击发生之前制定事件响应计划。 可在事件响应的不同阶段使用 Azure 安全中心。

若要了解如何将安全中心纳入事件响应流程中，请参考以下资源。

视频    
* [Azure Security Center in Incident Response](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Security-Center-in-Incident-Response)（Azure 安全中心在事件响应中的作业）
* [Respond quickly to threats with next-generation security operation, and investigation](https://youtu.be/e8iFCz5RM4g)（通过下一代安全操作和调查对威胁进行快速响应）

项目    
* [利用 Azure 安全中心进行事件响应](./tutorial-security-incident.md)
* [使用自动化响应安全中心触发器](workflow-automation.md)

## <a name="advanced-cloud-defense"></a>高级云防御

Azure VM 可以充分利用安全中心的高级云防御功能。 这些功能包括实时虚拟机 (VM) 访问和自适应应用程序控制。

若要了解如何在安全中心使用这些功能，请参考以下资源。

视频    
* [Azure 安全中心 – 实时 VM 访问](https://youtu.be/UOQb2FcdQnU)
* [Azure Security Center - Adaptive Application Controls](https://youtu.be/wWWekI1Y9ck)（Azure 安全中心 - 自适应应用程序控制）

项目    
* [使用实时功能管理虚拟机访问](./security-center-just-in-time.md)
* [Azure 安全中心的自适应应用程序控制](./security-center-adaptive-application.md)

## <a name="hands-on-activities"></a>动手活动

* [安全中心动手实验](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=78871abf-6f35-4aa0-840f-d801f5cdbd72)
* [安全中心的 Web 应用程序防火墙 (WAF) 建议攻略](https://gallery.technet.microsoft.com/ASC-Playbook-Protect-38bd47ff)
* [Azure 安全中心 Playbook：安全警报](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046)

## <a name="additional-resources"></a>其他资源
* [安全中心文档页](./index.yml)
* [安全中心 REST API 文档页](/previous-versions/azure/reference/mt704034(v=azure.100))
* [Azure 安全中心常见问题 (FAQ)](./faq-general.yml)
* [安全中心定价页](https://azure.microsoft.com/pricing/details/security-center/)
* [标识安全最佳做法](../security/fundamentals/identity-management-best-practices.md)
* [网络安全最佳实践](../security/fundamentals/network-best-practices.md)
* [PaaS 建议](../security/fundamentals/paas-deployments.md)
* [遵从性](https://www.microsoft.com/trustcenter/compliance/due-diligence-checklist)
* [Log Analytics 客户现在可以使用 Azure 安全中心来保护其混合云工作负荷](/archive/blogs/msoms/oms-customers-can-now-use-azure-security-center-to-protect-their-hybrid-cloud-workloads)