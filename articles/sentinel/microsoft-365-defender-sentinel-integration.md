---
title: Microsoft 365 Defender 与 Azure Sentinel 集成 | Microsoft Docs
description: 了解如何通过 Microsoft 365 Defender 与 Azure Sentinel 的集成将 Azure Sentinel 用作通用事件队列，同时保留 M365D 的优势来协助调查 M365 安全事件，以及如何将 Defender 组件的高级搜寻数据引入 Azure Sentinel。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/02/2021
ms.author: yelevin
ms.openlocfilehash: abace18db51a7a571ecc66d50253277fbd2296d3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "101744529"
---
# <a name="microsoft-365-defender-integration-with-azure-sentinel"></a>Microsoft 365 Defender 与 Azure Sentinel 集成

> [!IMPORTANT]
> Microsoft 365 Defender 连接器目前提供预览版。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，了解适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

> [!IMPORTANT]
>
> Microsoft 365 Defender 以前被称为 Microsoft 威胁防护 (MTP)。  
>
> Microsoft Defender for Endpoint 以前被称为 Microsoft Defender 高级威胁防护 (MDATP)。  
>
> 你可能会看到旧名称在一段时间内仍在使用。

## <a name="incident-integration"></a>事件集成

通过 Azure Sentinel 的 [Microsoft 365 Defender (M365D)](/microsoft-365/security/mtp/microsoft-threat-protection) 事件集成，可将所有 M365D 事件都流式传输到 Azure Sentinel，并使它们在这两个门户之间保持同步。 M365D 之前称为 Microsoft 威胁防护 (MTP)，来自它的事件包括所有关联警报、实体和相关信息，其中提供了足够的上下文来在 Azure Sentinel 中执行会审和初步调查。 传输到 Sentinel 后，事件将与 M365D 保持双向同步，让你能够在事件调查中利用这两个门户的优势。

此集成使 Microsoft 365 安全事件在 Azure Sentinel 内部作为整个组织中主要事件队列的一部分进行管理变得可见，因此你可以查看 M365 事件并将其与所有其他云和本地系统中的事件关联起来。 同时，它还使你可以利用 M365D 的独特优势和功能来进行深入的调查，并在整个 M365 生态系统中享受特定于 M365 的体验。 M365 Defender 将来自多个 M365 产品的警报进行了扩充和分组，由此减小了 SOC 事件队列的大小并缩短了解决时间。 属于 M365 Defender 堆栈的组件服务包括：

- 用于终结点的 Microsoft Defender（MDE，前身为 MDATP）
- Microsoft Defender for Identity（MDI，前身为 AATP）
- Microsoft Defender for Office 365（MDO，前身为 O365ATP）
- Microsoft Cloud App Security (MCAS)

除了从这些组件收集警报外，M365 Defender 还会生成自己的警报。 它从所有这些警报创建事件，并将其发送到 Azure Sentinel。

### <a name="common-use-cases-and-scenarios"></a>常见用例和方案

- 一键式连接 M365 Defender 事件，将 M365 Defender 组件中的所有警报和实体都包含到 Azure Sentinel 中。

- Sentinel 和 M365D 事件之间在状态、所有者和关闭原因方面进行双向同步。

- 利用 Azure Sentinel 中的 M365 Defender 警报分组和扩充功能，缩短解决时间。

- 在 Azure Sentinel 事件与其并行 M365 Defender 事件之间进行上下文中深度链接，以便跨两个门户进行调查。

### <a name="connecting-to-microsoft-365-defender"></a>连接到 Microsoft 365 Defender

一旦启用 Microsoft 365 Defender 数据连接器以[收集事件和警报](connect-microsoft-365-defender.md)，M365D 事件在 M365 Defender 生成后不久，将出现在 Azure Sentinel 事件队列中，同时 Microsoft 365 Defender 将出现在“产品名称”字段中。 
- 从在 M365 Defender 中生成事件的时间到它出现在 Azure Sentinel 中的时间最多可能需要 10 分钟。

- 事件将引入并同步，而不会产生额外费用。

一旦连接了 M365 Defender 集成，所有尚未连接的组件警报连接器（MDE、MDI、MDO、MCAS）将在后台自动连接。 如果在连接 M365 Defender 之后购买了任何组件许可证，则来自新产品的警报和事件仍将流向 Azure Sentinel，而无需进行额外配置或产生额外费用。

### <a name="m365-defender-incidents-and-microsoft-incident-creation-rules"></a>M365 Defender 事件和 Microsoft 事件创建规则

- M365 Defender 基于来自 M365 安全产品的警报生成的事件是使用自定义 M365 逻辑创建的。

- Azure Sentinel 中的 Microsoft 事件创建规则还使用（不同的）自定义 Azure Sentinel 逻辑从相同的警报创建事件。

- 完全支持一起使用两种机制，并且此配置可用于促进向新的 M365 Defender 事件创建逻辑过渡。 但是，这将为相同的警报创建重复事件。

- 为避免为相同的警报创建重复的事件，建议用户在连接 M365 Defender 时关闭 M365 产品（MDE、MDI 和 MDO - 请参阅下面的 MCAS）的所有 Microsoft 事件创建规则。 此操作可以通过在连接器页上标记相关复选框来完成。 请记住，如果执行此操作，事件创建规则应用的所有筛选器都不会应用于 M365 Defender 事件集成。

- 对于 Microsoft Cloud App Security (MCAS) 警报，当前并非所有警报类型都已载入 M365 Defender。 为确保仍能收到所有 MCAS 警报的事件，必须为未载入 M365D 的警报类型保留或创建 Microsoft事件创建规则。

### <a name="working-with-m365-defender-incidents-in-azure-sentinel-and-bi-directional-sync"></a>在 Azure Sentinel 和双向同步中使用 M365 Defender 事件

M365 Defender 事件将出现在 Azure Sentinel 事件队列中，其产品名称为 Microsoft 365 Defender，并且具有与任何其他 Sentinel 事件类似的详细信息和功能。 每个事件都包含一个返回 M365 Defender 门户中并行事件的链接。

随着事件在 M365 Defender 中演变，更多警报或实体被添加到其中，Azure Sentinel 事件也将相应地进行更新。

在 M365D 或 Azure Sentinel 中，对状态、关闭原因或 M365 事件的分配所做的更改也将在另一个事件队列中相应地进行更新。 在应用对事件所做的更改后，将立即在两个门户中进行同步，没有延迟。 可能需要刷新才能查看最新更改。

在 M365 Defender 中，来自一个事件的所有警报都可以传输到另一个事件，从而实现事件的合并。 发生这种情况时，Azure Sentinel 事件将反映所做的更改。 一个事件将包含来自两个原始事件的所有警报，另一个事件将自动关闭，并添加“重定向”标记。

> [!NOTE]
> Azure Sentinel 中的事件最多可以包含 150 个警报。 M365D 事件可具有超过此数目的警报。 如果将具有超过 150 个警报的 M365D 事件同步到 Azure Sentinel，则 Sentinel 事件将显示为具有“150+”个警报，并将提供指向 M365D 中的并行事件的链接，你可以在其中看到完整的警报集合。

## <a name="advanced-hunting-event-collection"></a>高级搜寻事件收集

利用 Microsoft 365 Defender 连接器，还可以将高级搜寻事件（原始事件数据的一种类型）从 Microsoft 365 Defender 及其组件服务流式传输到 Azure Sentinel。 你目前可以收集 [Microsoft Defender For Endpoint (MDATP)](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) [高级搜寻](/windows/security/threat-protection/microsoft-defender-atp/advanced-hunting-overview)事件，并将它们直接流式传输到 Azure Sentinel 工作区中专门构建的表中。 这些表是在 Microsoft 365 Defender 门户中使用的相同架构上构建的，让你可以完全访问一组完整的高级搜寻事件，还可以执行以下操作：

- 将现有 Microsoft Defender for Endpoint 高级搜寻查询轻松复制到 Azure Sentinel。

- 使用原始事件日志提供警报、搜寻和调查的额外见解，并将这些事件与 Azure Sentinel 中来自其他数据源的其他事件关联起来。

- 以延长的保留期，超出了 Microsoft Defender for Endpoint 或 Microsoft 365 Defender 默认 30 天的保留期，存储日志。 为此，可以配置工作区的保留期，或在 Log Analytics 中配置每个表的保留期。

## <a name="next-steps"></a>后续步骤

在本文档中，你学习了如何从通过 Microsoft 365 Defender 连接器将 Microsoft 365 Defender 与 Azure Sentinel 一起使用中获益。

- 获取有关如何[启用 Microsoft 365 Defender 连接器](connect-microsoft-365-defender.md)的说明。
- 创建[自定义警报](tutorial-detect-threats-custom.md)并[调查事件](tutorial-investigate-cases.md)。
