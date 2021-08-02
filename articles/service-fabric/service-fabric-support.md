---
title: 了解 Azure Service Fabric 支持选项
description: 支持的 Azure Service Fabric 群集版本，以及文件支持票证的链接
author: erikadoyle
ms.topic: troubleshooting
ms.date: 5/17/2021
ms.author: edoyle
ms.custom: support-help-page
ms.openlocfilehash: a4f83d6ddf09ca449da74f163a82baf2b12adc91
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111950075"
---
# <a name="azure-service-fabric-support-options"></a>Azure Service Fabric 支持选项

我们创建了许多支持请求选项来满足管理 Service Fabric 群集和应用程序工作负载的需求，具体取决于所需支持的紧迫性和问题的严重性。

## <a name="self-help-troubleshooting"></a>自助式故障排除
<div class='icon is-large'>
    <img alt='Self help content' src='./media/logos/doc-logo.png'>
</div>

这是客户支持服务在创建工单时、Service Fabric 站点可靠性工程师响应事件时以及用户发现活动系统问题的解决方案时引用的材料。

有关自助式故障排除内容的完整列表，请参阅 [Service Fabric 故障排除指南](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)

## <a name="create-an-azure-support-request"></a>创建 Azure 支持请求
<div class='icon is-large'>
    <img alt='Azure support' src='./media/logos/azure-logo.png'>
</div>

若要报告与在 Azure 上运行的 Service Fabric 群集相关的问题，请在 [Azure 门户](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)或 [Microsoft 支持门户](https://support.microsoft.com/oas/default.aspx?prid=16146)上开具支持票证。

了解有关以下方面的详细信息：

- [Azure 支持选项](https://azure.microsoft.com/support/plans/?b=16.44)。
- [Microsoft 顶级支持](https://support.microsoft.com/premier)。

> [!Note]
> 在青铜级可靠性层级或单节点群集上运行的群集只能用来运行测试性工作负荷。 如果遇到在青铜级可靠性层上运行的群集或单节点群集方面的问题，Microsoft 支持团队将帮助你缓解问题，但不会执行根本原因分析。 如需了解详细信息，请参阅[群集的可靠性特征](./service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster)。
>
> 有关生产就绪群集所需满足的要求的详细信息，请参阅[生产就绪情况核对清单](./service-fabric-production-readiness-checklist.md)。

<a id="getlivesitesupportonprem"></a>

## <a name="create-a-support-request-for-standalone-service-fabric-clusters"></a>为独立 Service Fabric 群集创建支持请求
<div class='icon is-large'>
    <img alt='Azure support' src='./media/logos/azure-logo.png'>
</div>

若要报告与在本地或其他云上运行的 Service Fabric 群集相关的问题，可通过 [Microsoft 支持门户](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)开具专业支持票证。

了解有关以下方面的详细信息：

- [Microsoft 提供的本地专业支持](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0)。
- [Microsoft 顶级支持](https://support.microsoft.com/en-us/premier)。

## <a name="post-a-question-to-microsoft-qa"></a>在 Microsoft Q&A 上发布问题
<div class='icon is-large'>
    <img alt='Microsoft Q&A' src='./media/logos/microsoft-logo.png'>
</div>   

直接从 Microsoft 工程师、Azure 最有价值专家 (MVP) 和我们的专家社区成员那里获取 Service Fabric 问题的解答。

[Microsoft Q&A](/answers/topics/azure-service-fabric.html) 是 Azure 建议的社区支持来源。

如果通过搜索 Microsoft Q&A 找不到问题的解答，可以提交新问题。 请务必使用 [azure-service-fabric](/answers/topics/azure-service-fabric.html) 标记发布问题。 下面是一些 Microsoft Q&A 技巧，可以帮助你写出[高质量的问题](/answers/articles/24951/how-to-write-a-quality-question.html)。

## <a name="open-a-github-issue"></a>创建 GitHub 问题
<div class='icon is-large'>
    <img alt='GitHub-image' src='./media/logos/github-logo.png'>
</div>

在 [Service Fabric GitHub](https://github.com/microsoft/service-fabric/issues) 上报告 Azure Service Fabric 问题。 此存储库用于报告和跟踪与 Azure Service Fabric 相关的问题并提出与 Azure Service Fabric 相关的小型功能请求。 **请勿使用此方式报告实时站点问题**。

## <a name="check-the-stackoverflow-forum"></a>查看 StackOverflow 论坛
<div class='icon is-large'>
    <img alt='Stack Overflow' src='./media/logos/stack-overflow-logo.png'>
</div>

[StackOverflow][stackoverflow] 上的 `azure-service-fabric` 标记用于询问有关平台如何工作以及如何使用它来完成某些任务的一般问题。

## <a name="submit-feedback-on-azure-feedback"></a>在 Azure 反馈中提交反馈
<div class='icon is-large'>
    <img alt='UserVoice' src='./media/logos/azure-feedback-logo.png'>
</div>

[有关 Service Fabric 的 Azure 反馈论坛][uservoice-forum]是提交重要的产品功能想法的最佳场所。 我们会评审最热门的请求，并将其作为我们的中长期规划的考虑因素。 我们鼓励你在社区内争取大家对建议的支持。


> [!Note]
> Service Fabric 预览版不支持用于生产。 有时我们会推出特殊的预览版本，其中包含我们希望通过调查获得早期反馈的重要功能更改。 应该只在不提供生产工作负载的隔离测试环境中使用预览版。 生产群集应始终运行支持的稳定 Service Fabric 版本。 我们不提供这些预览版本的付费支持选项。
>
> 预览版本始终以主版本号和次版本号 255 开头。 例如，如果你看到 Service Fabric 版本255.255.5703.949，则此版本处于预览阶段，并且仅用于测试群集。 这些预览版本也在 [Service Fabric 团队博客](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)上公布，并将提供有关包含的功能的详细信息。 请使用上面列出的选项之一提供反馈。


## <a name="stay-informed-of-updates-and-new-releases"></a>随时了解更新和新的版本

<div class='icon is-large'>
    <img alt='Stay informed' src='./media/logos/updates-logo.png'>
</div>

在 [Azure 更新](https://azure.microsoft.com/updates/?product=service-fabric)中了解重要产品更新、路线图和公告。

有关 Service Fabric 运行时和 SDK 的最新版本和更新，请参阅 [Service Fabric 版本](release-notes.md)



## <a name="next-steps"></a>后续步骤

[支持的 Service Fabric 版本](service-fabric-versions.md)

<!--references-->
[Microsoft Q&A question page]: /answers/topics/azure-service-fabric.html
[stackoverflow]: https://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: ./index.yml
[sample-repos]: /samples/browse/?products=azure