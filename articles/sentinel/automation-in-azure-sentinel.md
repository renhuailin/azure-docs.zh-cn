---
title: Azure Sentinel 中的自动化简介 | Microsoft Docs
description: 本文介绍了 Azure Sentinel 的安全业务流程、自动化和响应 (SOAR) 功能，并围绕 SOAR 的组件（即自动化规则和 playbook）展开说明。
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
ms.topic: conceptual
ms.date: 03/14/2021
ms.author: yelevin
ms.openlocfilehash: 54d7c997ce17c927a692e84f6094e3a08f707af7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104608508"
---
# <a name="security-orchestration-automation-and-response-soar-in-azure-sentinel"></a>Azure Sentinel 中的安全业务流程、自动化和响应 (SOAR)

本文介绍了 Azure Sentinel 中的安全业务流程、自动化和响应 (SOAR) 功能，并演示了如何使用自动化规则和 playbook 以响应安全威胁，从而提高 SOC 的有效性并节约时间和资源。

## <a name="azure-sentinel-as-a-soar-solution"></a>Azure Sentinel 作为 SOAR 解决方案

### <a name="the-problem"></a>问题

SIEM/SOC 团队通常会定期收到大量的安全警报和事件，数量之多甚至超出了可用人员的处理能力。 因此频繁出现许多警报被忽视、许多事件未调查的窘境，导致组织很容易在未注意到的情况下受到攻击。

### <a name="the-solution"></a>解决方案

除了作为安全信息和事件管理 (SIEM) 系统以外，Azure Sentinel 还是用于安全业务流程、自动化和响应 (SOAR) 的平台。 其主要目的之一就是自动执行任何定期的、可预测的扩充、响应和修正任务，减轻了原本负责这些任务的安全运营中心和人员 (SOC/SecOps) 的负担，以便将更多时间和资源用在更深入地调查和搜寻高级威胁方面。 Azure Sentinel 中的自动化分为若干种不同的形式，从集中管理自动化事件处理和响应的自动化规则到运行预先确定的操作序列，以对威胁响应任务提供强大而灵活的高级自动化的 playbook。

## <a name="automation-rules"></a>自动化规则

自动化规则是 Azure Sentinel 中的一个新概念。 此功能允许用户集中管理自动化事件处理。 除了允许向事件（而不是之前的只有警报）分配 playbook 之外，自动化规则还允许一次性自动执行多个分析规则的响应，自动标记、分配或关闭事件而无需 playbook，并控制操作的执行顺序。 自动化规则有助于精简 Azure Sentinel 中的自动化使用，并能简化事件业务流程进程的复杂工作流。

通过此处[有关自动化规则的完整说明](automate-incident-handling-with-automation-rules.md)，了解更多详细信息。

> [!IMPORTANT]
>
> - **自动化规则** 功能目前处于 **预览阶段**。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，了解适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

## <a name="playbooks"></a>攻略

playbook 是可以作为例程从 Azure Sentinel 运行的响应和修正操作以及逻辑的集合。 playbook 可以帮助自动执行和协调威胁响应，可以与内部和外部的其他系统相集成，并且可以设置为自动运行以响应特定警报或事件（当由分析规则或自动化规则触发时）。 为了响应警报，还可以从事件页中按需手动运行。

Azure Sentinel 中的 playbook 基于在 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)中构建的工作流，这是一项云服务，可帮助计划、自动执行和协调整个企业范围内系统中的任务和工作流。 这意味着 playbook 不仅可以利用逻辑应用的集成和业务流程功能以及易于使用的设计工具所具备的所有强大功能和可定制性，还具有第 1 层 Azure 服务的可伸缩性、可靠性和服务级别。

通过此处[有关 playbook 的完整说明](automate-responses-with-playbooks.md)，了解更多详细信息。

## <a name="next-steps"></a>后续步骤

本文档介绍了 Azure Sentinel 如何使用自动化来帮助提高 SOC 运行的有效性和高效性。

- 若要了解有关自动化事件处理的信息，请参阅 [Azure Sentinel 中的自动事件处理](automate-incident-handling-with-automation-rules.md)。
- 若要了解有关高级自动化选项的详细信息，请参阅[在 Azure Sentinel 中利用 playbook 自动执行威胁响应](automate-responses-with-playbooks.md)。
- 有关实施自动化规则和 playbook 方面的帮助，请参阅[教程：在 Azure Sentinel 中使用 playbook 自动完成威胁响应](tutorial-respond-threats-playbook.md)。
