---
title: Microsoft 365 Defender 与 Azure Sentinel 集成 |Microsoft Docs
description: 了解 Microsoft 365 Defender 与 Azure Sentinel 集成的功能，即如何使用 Azure Sentinel 作为通用事件队列，同时保留 M365D's 优势来帮助调查 M365 的安全事件，以及如何将 Defender 组件的高级搜寻数据引入到 Azure Sentinel。
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
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744529"
---
# <a name="microsoft-365-defender-integration-with-azure-sentinel"></a>Microsoft 365 Defender 与 Azure Sentinel 集成

> [!IMPORTANT]
> Microsoft 365 Defender 连接器目前为 **预览版**。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) ，了解适用于 Azure 功能的其他法律条款，这些功能适用于 beta 版、预览版或其他情况下尚未公开上市。

> [!IMPORTANT]
>
> **Microsoft 365 Defender** 以前称为 **Microsoft 威胁防护** 或 **MTP**。
>
> **Microsoft defender For Endpoint** 以前称为 " **Microsoft Defender 高级威胁防护** " 或 **MDATP**。
>
> 你可能会看到旧名称在一段时间内仍在使用。

## <a name="incident-integration"></a>事件集成

Azure Sentinel [Microsoft 365 Defender (M365D) ](/microsoft-365/security/mtp/microsoft-threat-protection) 事件集成允许将所有 M365D 事件流式传输到 Azure Sentinel，并使其在两个门户之间保持同步。 来自 (M365D 的事件（以前称为 Microsoft 威胁防护或 MTP) ）包括所有关联的警报、实体和相关信息，为你提供了在 Azure Sentinel 中执行会审和初步调查所需的足够上下文。 进入 Sentinel 后，事件仍会与 M365D 保持同步，从而使你能够充分利用事件调查中这两个门户的优势。

此集成为 Microsoft 365 安全事件提供了从 Azure Sentinel 内管理的可见性，作为整个组织中主事件队列的一部分，因此你可以查看–并将 M365 事件与所有其他云和本地系统中的事件关联起来。 同时，它还允许充分利用 M365D 的独特优势和功能，实现深入调查，并跨 M365 生态系统提供 M365 特定体验。 M365 Defender 丰富并将来自多个 M365 产品的警报分组，同时减小 SOC 事件队列的大小并缩短解决时间。 属于 M365 Defender 堆栈的组件服务包括：

- **Microsoft Defender For Endpoint** (MDE，以前为 MDATP) 
- **Microsoft Defender For Identity** (MDI，以前为 AATP) 
- **Microsoft Defender For Office 365** (MDO，以前称为 O365ATP) 
- **Microsoft Cloud App Security** (MCAS) 

除了从这些组件收集警报外，M365 Defender 还会生成自己的警报。 它从所有这些警报创建事件，并将其发送到 Azure Sentinel。

### <a name="common-use-cases-and-scenarios"></a>常见的用例和方案

- 一键式连接 M365 Defender 事件，包括 M365 Defender 组件中的所有警报和实体。

- 状态、所有者和关闭原因上的 M365D 事件之间的双向同步。

- 利用 Azure Sentinel 中的 M365 Defender 警报分组和扩充功能，从而缩短解决时间。

- 在 Azure Sentinel 事件与其并行 M365 Defender 事件之间进行上下文深层链接，以便于跨两个门户进行调查。

### <a name="connecting-to-microsoft-365-defender"></a>正在连接到 Microsoft 365 Defender

启用 Microsoft 365 Defender 数据连接器以 [收集事件和警报](connect-microsoft-365-defender.md)后，M365D 事件会显示在 "Azure Sentinel 事件 **Microsoft 365** 队列" 中，在 " **产品名称** " 字段中显示 "M365"。
- 可能需要长达10分钟的时间，从 M365 Defender 生成事件到它在 Azure Sentinel 中出现的时间。

- 事件将引入并同步，无需额外付费。

连接 M365 Defender 集成后，所有组件警报连接器 (MDE、MDI、MDO、MCAS) 在后台自动连接（如果尚未这样做）。 如果在连接 M365 Defender 后购买了任何组件许可证，则新产品中的警报和事件仍将流向 Azure Sentinel，无额外配置或收费。

### <a name="m365-defender-incidents-and-microsoft-incident-creation-rules"></a>M365 Defender 事件和 Microsoft 事件创建规则

- M365 Defender 基于 M365 安全产品的警报生成的事件是使用自定义 M365 逻辑创建的。

- Microsoft 事件-Azure Sentinel 中的创建规则还会使用 (不同) 自定义 Azure Sentinel 逻辑来创建相同警报的事件。

- 完全支持结合使用这两种机制，此配置可用于促进过渡到新的 M365 Defender 事件创建逻辑。 但这会为相同警报创建 **重复事件** 。

- 若要避免为同一警报创建重复事件，建议客户关闭 M365 products (MDE、MDI 和 MDO 的所有 **Microsoft 事件创建规则** -连接 M365 Defender 时，请参阅以下 MCAS) 。 可以通过在 "连接器" 页中标记相关复选框来完成此操作。 请记住，如果这样做，事件创建规则应用的任何筛选器都不会应用于 M365 Defender 事件集成。

- 对于 Microsoft Cloud App Security (MCAS) 警报，并非所有警报类型当前都载入 M365 Defender。 若要确保仍会获得所有 MCAS 警报的事件，必须为 *不载入* 到 M365D 的警报类型保留或创建 **Microsoft 事件创建规则**。

### <a name="working-with-m365-defender-incidents-in-azure-sentinel-and-bi-directional-sync"></a>在 Azure Sentinel 和双向同步中使用 M365 Defender 事件

M365 Defender 事件会在 Azure Sentinel 事件队列中显示，其产品名称为 **Microsoft 365 Defender**，并对任何其他 Sentinel 事件具有类似的详细信息和功能。 每个事件在 M365 Defender 门户中都包含一个返回到并行事件的链接。

随着事件在 M365 Defender 中的发展，并向其添加了更多警报或实体，Azure Sentinel 事件会相应地进行更新。

在 M365D 或 Azure Sentinel 中，对状态、关闭原因或 M365 事件的分配所做的更改将在另一个事件队列中相应地进行更新。 在应用事件的更改后，将立即在两个门户中进行同步，而无需延迟。 可能需要刷新才能查看最新更改。

在 M365 Defender 中，来自一个事件的所有警报都可以传输到另一个事件，从而导致事件被合并。 发生这种情况时，Azure Sentinel 事件将反映所做的更改。 一个事件将包含来自两个原始事件的所有警报，另一个事件将自动关闭，并添加标记 "重定向"。

> [!NOTE]
> Azure Sentinel 中的事件最多可包含150个警报。 M365D 事件可具有超过此数目。 如果将超过150个警报的 M365D 事件同步到 Azure Sentinel，则 Sentinel 事件将显示为 "150 +" 个警报，并将在 M365D 中提供与并行事件的链接，你可以在其中看到完整的警报集。

## <a name="advanced-hunting-event-collection"></a>高级搜寻事件收集

使用 Microsoft 365 Defender 连接器，还可以流式传输 **高级搜寻** 事件，一种类型的原始事件数据（从 Microsoft 365 Defender 及其组件服务到 Azure Sentinel）。 你目前可以收集 [Microsoft Defender For Endpoint (MDATP) ](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) [高级搜寻](/windows/security/threat-protection/microsoft-defender-atp/advanced-hunting-overview) 事件，并将它们直接流式传输到 Azure Sentinel 工作区中专门构建的表中。 这些表是在 Microsoft 365 Defender 门户中使用的相同架构上构建的，可让你完全访问一组完整的高级搜寻事件，并允许你执行以下操作：

- 轻松将现有 Microsoft Defender for Endpoint 高级搜寻查询复制到 Azure Sentinel。

- 使用原始事件日志提供警报、搜寻和调查的其他见解，并将这些事件与 Azure Sentinel 中其他数据源的其他事件关联起来。

- 存储保留的保留期，超过 Microsoft Defender for Endpoint 或 Microsoft 365 Defender 的30天的保留期。 为此，可以配置工作区的保留期，或在 Log Analytics 中配置每个表的保留期。

## <a name="next-steps"></a>后续步骤

本文档介绍了如何使用 Microsoft 365 Defender 连接器将 Microsoft 365 Defender 与 Azure Sentinel 一起使用。

- 获取有关 [启用 Microsoft 365 Defender 连接器](connect-microsoft-365-defender.md)的说明。
- 创建 [自定义警报](tutorial-detect-threats-custom.md) 并 [调查事件](tutorial-investigate-cases.md)。
