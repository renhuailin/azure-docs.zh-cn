---
title: 了解 Azure Service Fabric 支持选项
description: 支持的 Azure Service Fabric 群集版本，以及文件支持票证的链接
author: pkcsf
ms.topic: troubleshooting
ms.date: 8/24/2018
ms.author: pkc
ms.openlocfilehash: 977cd79de629670cef526f072340f8897fa6446e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "92316503"
---
# <a name="azure-service-fabric-support-options"></a>Azure Service Fabric 支持选项

我们创建了许多支持请求选项来满足管理 Service Fabric 群集和应用程序工作负载的需求。 可以根据所需支持的紧急性和问题的严重性，选择适合你的选项。

## <a name="report-production-issues-or-request-paid-support-for-azure"></a>报告生产问题，或者请求 Azure 付费支持

若要报告与在 Azure 上运行的 Service Fabric 群集相关的问题，请在 [Azure 门户](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)或 [Microsoft 支持门户](https://support.microsoft.com/oas/default.aspx?prid=16146)上开具支持票证。

了解有关以下方面的详细信息：
 
- [Microsoft 提供的 Azure 支持](https://azure.microsoft.com/support/plans/?b=16.44)。
- [Microsoft 顶级支持](https://support.microsoft.com/en-us/premier)。

> [!Note]
> 在青铜级可靠性层级或单节点群集上运行的群集只能用来运行测试性工作负荷。 如果遇到在青铜级可靠性层上运行的群集或单节点群集方面的问题，Microsoft 支持团队将帮助你缓解问题，但不会执行根本原因分析。 如需了解详细信息，请参阅[群集的可靠性特征](./service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster)。
>
> 有关生产就绪群集所需满足的要求的详细信息，请参阅[生产就绪情况核对清单](./service-fabric-production-readiness-checklist.md)。

<a id="getlivesitesupportonprem"></a>

## <a name="report-production-issues-or-request-paid-support-for-standalone-service-fabric-clusters"></a>报告生产问题，或者请求独立 Service Fabric 群集的付费支持

若要报告与在本地或其他云上运行的 Service Fabric 群集相关的问题，可通过 [Microsoft 支持门户](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)开具专业支持票证。

了解有关以下方面的详细信息：

- [Microsoft 提供的本地专业支持](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0)。
- [Microsoft 顶级支持](https://support.microsoft.com/en-us/premier)。

## <a name="report-azure-service-fabric-issues"></a>报告 Azure Service Fabric 问题

我们已设置 GitHub 存储库，用于报告 Service Fabric 问题。  我们还积极监视以下论坛。

### <a name="github-repo"></a>GitHub 存储库 

在 [Service Fabric GitHub](https://github.com/microsoft/service-fabric/issues) 上报告 Azure Service Fabric 问题。 此存储库用于报告和跟踪与 Azure Service Fabric 相关的问题并提出与 Azure Service Fabric 相关的小型功能请求。 **请勿使用此方式报告实时站点问题**。

### <a name="stackoverflow-and-msdn-forums"></a>StackOverflow 和 MSDN 论坛

[StackOverflow 上的 Service Fabric 标记][stackoverflow]和 [MSDN 上的 Service Fabric 论坛][msdn-forum]最适合提问有关平台工作方式以及如何通过该平台完成某些任务的一般性问题。

### <a name="azure-feedback-forum"></a>Azure 反馈论坛

[有关 Service Fabric 的 Azure 反馈论坛][uservoice-forum]是提交重要的产品功能想法的最佳场所。 我们会评审最热门的请求，并将其作为我们的中长期规划的考虑因素。 我们鼓励你在社区内争取大家对建议的支持。

## <a name="service-fabric-preview-versions---unsupported-for-production-use"></a>Service Fabric 预览版本 - 不支持在生产环境中使用

有时我们会推出特殊的预览版本，其中包含我们希望通过调查获得早期反馈的重要功能更改。 应该只在不提供生产工作负载的隔离测试环境中使用预览版。 生产群集应始终运行支持的稳定 Service Fabric 版本。 我们不提供这些预览版本的付费支持选项。

预览版本始终以主版本号和次版本号 255 开头。 例如，如果你看到 Service Fabric 版本255.255.5703.949，则此版本处于预览阶段，并且仅用于测试群集。 这些预览版本也在 [Service Fabric 团队博客](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)上公布，并将提供有关包含的功能的详细信息。 使用[报告 Azure Service Fabric 问题](#report-azure-service-fabric-issues)下列出的选项之一提出问题或提供反馈。

## <a name="next-steps"></a>后续步骤

[支持的 Service Fabric 版本](service-fabric-versions.md)

<!--references-->
[Microsoft Q&A question page]: /answers/topics/azure-service-fabric.html
[stackoverflow]: https://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: ./index.yml
[sample-repos]: /samples/browse/?products=azure
[msdn-forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?category=windowsazureplatform