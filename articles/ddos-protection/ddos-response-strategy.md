---
title: DDoS 响应策略的组件
description: 了解如何使用 Azure DDoS 保护标准来响应 DDoS 攻击。
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 2b31a8aa8b126c228ac7e9c3ca182300c710b098
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/30/2020
ms.locfileid: "97814051"
---
# <a name="components-of-a-ddos-response-strategy"></a>DDoS 响应策略的组件

针对 Azure 资源的 DDoS 攻击通常只需用户做出极少量的干预。 在事件响应策略中整合 DDoS 缓解措施有助于进一步减少对业务连续性产生的影响。

## <a name="microsoft-threat-intelligence"></a>Microsoft 威胁情报

Microsoft 部署了广泛的威胁情报网络。 此网络利用了为 Microsoft 联机服务和 Microsoft 合作伙伴提供服务的扩展安全社区的集体知识，以及与 Internet 安全社区的内部关系。 

作为一家重要的基础结构提供商，Microsoft 会提前收到有关威胁的警告。 Microsoft 从其 Microsoft 联机服务和全球客户群体收集威胁情报。 Microsoft 将所有威胁情报融入到 Azure DDoS 防护产品中。

此外，Microsoft 的反数字犯罪部门 (DCU) 还会针对僵尸网络执行打击策略。 僵尸网络是 DDoS 攻击的常见指挥源头。

## <a name="risk-evaluation-of-your-azure-resources"></a>针对 Azure 资源的风险评估

必须不断了解 DDoS 攻击的风险范围。 定期询问自己：

- 哪些新公开发布的 Azure 资源需要保护？

- 服务中是否存在单一故障点？ 

- 如何隔离服务，以限制某项攻击造成的影响，同时使服务仍可供合法客户使用？

- 是否有虚拟网络应启用标准 DDoS 防护，但却没有启用？ 

- 我的服务在跨多个区域故障转移后是否保持主动/主动状态？

务必了解应用程序的正常行为，并在应用程序在 DDoS 攻击期间不按预期方式执行操作。 为模拟客户端行为的业务关键应用程序配置监视器，并在检测到相关异常时通知你。 若要深入了解应用程序的运行状况，请参阅 [监视和诊断最佳实践](/azure/architecture/best-practices/monitoring#monitoring-and-diagnostics-scenarios) 。

[Azure Application Insights](../azure-monitor/app/app-insights-overview.md) 是多个平台上面向 Web 开发人员的可扩展应用程序性能管理 (APM) 服务。 使用 Application Insights 来监视实时 Web 应用程序。 它会自动检测性能异常。 它包含分析工具，可帮助你诊断问题并了解用户对应用执行的操作。 Application Insights 有助于持续提高性能与可用性。

## <a name="customer-ddos-response-team"></a>客户 DDoS 响应团队

建立 DDoS 响应团队是快速有效地对攻击做出响应的关键一步。 在组织中确定负责监督规划和执行的各个联系人。 此 DDoS 响应团队应该全面了解标准 Azure DDoS 防护服务。 确保该团队能够与内部和外部客户（包括 Microsoft 支持团队）协作，以识别和缓解攻击。 

建议在服务可用性和连续性规划中使用模拟练习，这些练习应包括缩放测试。 请参阅 [测试模拟](test-through-simulations.md) ，了解如何针对 Azure 公共终结点模拟 DDoS 测试流量。

## <a name="alerts-during-an-attack"></a>攻击期间的警报

标准 Azure DDoS 防护将识别并缓解 DDoS 攻击，而无需任何用户干预。 若要在受保护的公共 IP 存在活动缓解时获得通知，你可以 [配置警报](alerts.md)。

### <a name="when-to-contact-microsoft-support"></a>何时与 Microsoft 支持部门联系

Azure DDoS 保护标准客户有权访问 DDoS 快速响应 (DRR) 团队，他们可以在攻击期间帮助攻击调查，还可以进行攻击后的分析。 有关更多详细信息，请参阅 [DDoS 快速响应](ddos-rapid-response.md) ，包括应参与 DRR 团队。

## <a name="post-attack-steps"></a>攻击后的措施

在发生攻击后执行事后剖析并按需重新调整 DDoS 响应策略，始终是一个良好的策略。 注意事项：

- 服务或用户的体验是否因缺少可缩放的体系结构而受到任何干扰？

- 哪些应用程序或服务受到的影响最大？

- DDoS 响应策略的效果如何，如何对它做出改进？

如果你怀疑自己受到 DDoS 攻击，请通过正常的 Azure 支持渠道上报。

## <a name="next-steps"></a>后续步骤

- 了解如何 [创建 DDoS 保护计划](manage-ddos-protection.md)。