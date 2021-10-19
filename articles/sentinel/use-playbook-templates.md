---
title: 从内置模板创建和自定义 Azure Sentinel playbook | Microsoft Docs
description: 本文介绍如何从 playbook 模板创建 playbook，以及如何使用这些模板，从而根据需求自定义这些 playbook。
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/11/2021
ms.author: yelevin
ms.openlocfilehash: a2d9bdce1294a42487529c959f26eeee497d5046
ms.sourcegitcommit: af303268d0396c0887a21ec34c9f49106bb0c9c2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2021
ms.locfileid: "129754723"
---
# <a name="create-and-customize-azure-sentinel-playbooks-from-built-in-templates"></a>从内置模板创建和自定义 Azure Sentinel playbook

> [!IMPORTANT]
>
> playbook 模板目前以预览版提供 。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，了解适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

playbook 模板是预生成的、已经过测试且随时可用的工作流，你可以根据自己的需求对其进行自定义。 在从头开发 playbook 时，还可以将这些模板作为最佳做法参考；或者，可以通过模板获得灵感以实现新的自动化方案。

除非从 playbook 模板创建了一个 playbook（模板的可编辑副本），否则这些模板本身并非活动 playbook。

Azure Sentinel 社区、独立软件供应商 (ISV) 和 Microsoft 内部专家已基于全球安全运营中心使用的热门自动化方案开发了许多 playbook 模板。

可以从以下来源获取 playbook 模板：

- “Playbook 模板”选项卡（在“自动化”下）显示了 Azure Sentinel 社区贡献的主流方案 。 可以从同一个模板创建多个活动 playbook。

    发布新版模板后，（在“Playbook”选项卡中）从该模板创建的活动 playbook 将标有一则通知，其中指出有可用的更新。

- playbook 模板还可以在特定产品的上下文中作为 [Azure Sentinel 解决方案](sentinel-solutions.md)的一部分获取。 部署该解决方案会生成活动 playbook。

- [Azure Sentinel GitHub 存储库](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks)包含许多 playbook 模板。 可以选择“部署到 Azure”按钮将这些模板部署到 Azure 订阅。 

从技术上讲，playbook 模板是一个 [Azure 资源管理器 (ARM) 模板](../azure-resource-manager/templates/index.yml)，它由多个资源组成：Azure 逻辑应用工作流，以及每个相关连接的 API 连接。 

本文重点介绍如何从“自动化”下的“Playbook 模板”选项卡部署 playbook 模板 。

本文将帮助你了解如何执行以下操作：

> [!div class="checklist"]
> * 浏览现成的 playbook 模板
> * 部署 playbook 模板

## <a name="explore-playbook-templates"></a>浏览 playbook 模板

从 Azure Sentinel 导航菜单中选择“自动化”，然后选择“Playbook 模板”选项卡 。

此处显示的 playbook 模板演示了 SOC 倾向于使用的或从中获得灵感的主要自动化方案。 其中的大部分 playbook 是由 Azure Sentinel 社区贡献的，最初位于 Azure Sentinel GitHub 存储库中。 其中一些 playbook 已集成到 Azure Sentinel 解决方案中。

:::image type="content" source="media/use-playbook-templates/gallery.png" alt-text="playbook 库的屏幕截图。" lightbox="media/use-playbook-templates/gallery.png":::

若要查找符合你要求的 playbook 模板，可按以下条件筛选列表：

- “触发器”指示该 playbook 由事件创建操作触发（因而可附加到自动化规则）、由警报创建操作触发（因而可附加到分析规则）或由其他某项操作触发。 [了解详细信息](playbook-triggers-actions.md#azure-sentinel-triggers-summary)

- “逻辑应用连接器”显示此 playbook 将与之交互的外部服务。 在部署过程中，每个连接器需要采用一个标识来向外部服务进行身份验证。

- “实体”显示某个 playbook 显式筛选和分析的实体类型，该 playbook 预期会在事件中找到这些实体类型。 例如，告知防火墙阻止某个 IP 地址的 playbook 应会针对由生成包含 IP 地址的警报的分析规则（例如暴力攻击检测规则）所创建的事件运行。

- “标记”显示应用于 playbook，以将其与特定方案相关联或指示特殊特征的标签。
 
    示例：

    - 扩充 - playbook 从另一个服务中获取信息，以便将信息添加到事件中。 此信息通常作为注释添加到事件或发送到 SOC。

    - 修正 - playbook 对受影响的实体采取措施，以消除潜在威胁。

    - 同步 - playbook 帮助通过事件的属性来使外部服务（例如事件管理服务）保持更新状态。

    - 通知 - playbook 发送电子邮件或消息。

    - Teams 响应 - playbook 允许分析师使用交互式卡从 Teams 采取手动措施。

:::image type="content" source="media/use-playbook-templates/filters.png" alt-text="筛选 playbook 模板列表":::

## <a name="customize-a-playbook-from-a-template"></a>从模板自定义 playbook

此过程说明如何部署 playbook 模板。

可以重复此过程以在同一模板上创建多个 playbook。 

1. 从“Playbook 模板”选项卡中选择一个 playbook 名称。

1. 如果该 playbook 有任何先决条件，请确保按照说明进行操作。

    - 某些 playbook 会将其他 playbook 称为操作。 这第二个 playbook 称为嵌套 playbook。 在这种情况下，先决条件之一是首先部署嵌套 playbook。

    - 某些 playbook 要求部署自定义逻辑应用连接器或 Azure 函数。 在这种情况下，会出现“部署到 Azure”链接， <!-- WHERE? --> 单击该链接会转到常规 ARM 模板部署过程。

1. 选择“创建 playbook”，以基于所选模板打开 playbook 创建向导。 该向导有四个选项卡：

    - 基础信息：找到你的新 playbook（逻辑应用资源）并为其命名（可使用默认值）。
        :::image type="content" source="media/use-playbook-templates/basics.png" alt-text="playbook 创建向导的“基本信息”选项卡":::

    - 参数：输入 playbook 将使用的特定于客户的值。 例如，如果此 playbook 要向 SOC 发送电子邮件，则你可以在此处定义收件人地址。 仅当 playbook 具有参数时，才会显示此选项卡。
    
        > [!NOTE]
        > 如果此 playbook 具有一个已在使用的自定义连接器，则该连接器应已部署在同一资源组中，这样，你就可以在此选项卡中插入其名称。

        :::image type="content" source="media/use-playbook-templates/parameters.png" alt-text="playbook 创建向导的“参数”选项卡":::

    - 连接：展开每个操作以查看你为前面的 playbook 创建的现有连接。 详细了解如何[为 playbook 创建连接](authenticate-playbooks-to-sentinel.md)。
    
        > [!NOTE]
        > 对于自定义连接器，将按照在“参数”选项卡中输入的自定义连接器名称显示连接。

        :::image type="content" source="media/use-playbook-templates/connections.png" alt-text="playbook 创建向导的“连接”选项卡":::

        如果没有任何连接，或者你要创建新连接，请选择“部署后创建新连接”。 这样，在部署过程完成后，你将转到逻辑应用设计器。

        对于支持[使用托管标识进行连接](authenticate-playbooks-to-sentinel.md#authenticate-with-managed-identity)的连接器（例如 Azure Sentinel），默认会选择这种连接方法。

    - 查看并创建：在创建 playbook 之前查看过程摘要，并等待系统验证你的输入。


1. 从头到尾完成 playbook 创建向导中的步骤后，你将转到逻辑应用设计器中新 playbook 的工作流设计。

    :::image type="content" source="media/use-playbook-templates/designer.png" alt-text="在逻辑应用设计器中查看 playbook":::

1. 对于你选择要在部署后为其创建新连接的每个连接器，请执行以下操作：
    1. 从导航菜单中，选择“API 连接”。
    1. 选择连接名称。
    1. 从导航菜单中选择“编辑 API 连接”。
    1. 填写所需的参数，然后单击“保存”。
        :::image type="content" source="media/use-playbook-templates/edit-api-connection.png" alt-text="显示如何编辑 API 连接的屏幕截图。":::
    
    或者，可以通过逻辑应用设计器中的相关步骤创建新连接：
    1. 对于出现了错误符号的每个步骤，请选择该步骤以将其展开。
    1. 选择“添加新订阅”。
    1. 按照相关说明进行身份验证。
    1. 如果还有其他步骤使用此连接器，请展开这些步骤对应的框。 从显示的连接列表中，选择刚刚创建的连接。

1. 如果已选择对 Azure Sentinel（或其他受支持的连接）使用托管标识连接，请向新 playbook 授予对 Azure Sentinel 工作区（或其他连接器的相关目标资源）的权限。

1. 保存 playbook。 现在你可以在“活动 Playbook”选项卡中看到该 playbook。

1. 若要运行此 playbook，请[设置自动响应](automate-responses-with-playbooks.md#set-an-automated-response)或[手动运行](automate-responses-with-playbooks.md#run-a-playbook-manually-on-an-alert)（仅限警报触发器）。

1. 大多数模板都可按原样使用，但我们建议进行任何必要的调整，使新 playbook 符合你的 SOC 需求。

## <a name="troubleshooting"></a>疑难解答

### <a name="issue-found-a-bug-in-the-playbook"></a>问题：在 playbook 中发现了 bug

若要报告 bug 或请求改进某个 playbook，请在该 playbook 的详细信息窗格中选择“支持者”链接。 如果该 playbook 的支持工作由社区负责，则单击该链接会转到一个可以提交 GitHub 问题的页面。 否则你将会转到支持者的页面。

## <a name="next-steps"></a>后续步骤

在本文中，你已了解如何使用 playbook 模板，根据自己的需求创建和自定义 playbook。 详细了解 Azure Sentinel 中的 playbook 和自动化：

- [Azure Sentinel 中的安全业务流程、自动化和响应 (SOAR)](automation-in-azure-sentinel.md)
- [在 Azure Sentinel 中使用 playbook 实现威胁响应自动化](automate-responses-with-playbooks.md)
- [教程：在 Azure Sentinel 中结合自动化规则使用 playbook](tutorial-respond-threats-playbook.md)
- [向 Azure Sentinel 验证 playbook](authenticate-playbooks-to-sentinel.md)
- [在 Azure Sentinel playbook 中使用触发器和操作](playbook-triggers-actions.md)
