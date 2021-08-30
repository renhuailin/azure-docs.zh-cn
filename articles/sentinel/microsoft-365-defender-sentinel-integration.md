---
title: Microsoft 365 Defender 与 Azure Sentinel 集成 | Microsoft Docs
description: 了解如果将 Microsoft 365 Defender 与 Azure Sentinel 结合使用，可如何将 Azure Sentinel 用作通用事件队列，同时无缝应用 Microsoft 365 Defender 的优势协助调查 Microsoft 365 安全事件。 此外，还可了解如何将 Defender 组件的高级搜寻数据引入 Azure Sentinel。
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
ms.date: 04/21/2021
ms.author: yelevin
ms.openlocfilehash: 86a998e304755085a9ae1e15e011df3b242c4df8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121725150"
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

通过 Azure Sentinel 的 [Microsoft 365 Defender](/microsoft-365/security/mtp/microsoft-threat-protection) 事件集成，可将所有 Microsoft 365 Defender 事件都流式传输到 Azure Sentinel，并使这些事件在两个门户之间保持同步。 来自 Microsoft 365 Defender（旧称 Microsoft 威胁防护或 MTP）的事件包括所有关联警报、实体和相关信息，为你提供足够的上下文，以便在 Azure Sentinel 中执行会审和初步调查。 传输到 Sentinel 后，事件将与 Microsoft 365 Defender 保持双向同步，让你能够在事件调查中利用这两个门户的优势。

此集成使 Microsoft 365 安全事件在 Azure Sentinel 内部作为整个组织中主要事件队列的一部分进行管理变得可见，因此你可以查看 Microsoft 365 事件并将其与所有其他云和本地系统中的事件关联起来。 同时，此集成还可让你利用 Microsoft 365 Defender 的独特优势和功能进行深入调查，并在整个 Microsoft 365 生态系统中获得特定于 Microsoft 365 的体验。 Microsoft 365 Defender 将来自多个 Microsoft 365 产品的警报进行了扩充和分组，既减小了 SOC 事件队列的大小，又缩短了解决时间。 属于 Microsoft 365 Defender 堆栈的组件服务包括：

- Microsoft Defender for Endpoint（前身为 Microsoft Defender ATP）
- Microsoft Defender for Identity（前身为 Azure ATP）
- Microsoft Defender for Office 365（前身为 Office 365 ATP）
- **Microsoft Cloud App Security**

除了从这些组件收集警报外，Microsoft 365 Defender 还会生成自己的警报。 它从所有这些警报创建事件，并将其发送到 Azure Sentinel。

### <a name="common-use-cases-and-scenarios"></a>常见用例和场景

- 将 Microsoft 365 Defender 事件（包括来自 Microsoft 365 Defender 组件的所有警报和实体）一键式连接到 Azure Sentinel。

- Sentinel 和 Microsoft 365 Defender 事件之间在状态、所有者和关闭原因方面保持双向同步。

- 利用 Azure Sentinel 中的 Microsoft 365 Defender 警报分组和扩充功能，缩短解决时间。

- 在 Azure Sentinel 事件及其并行的 Microsoft 365 Defender 事件之间进行上下文深层链接，以便跨两个门户进行调查。

### <a name="connecting-to-microsoft-365-defender"></a>连接到 Microsoft 365 Defender

启用 Microsoft 365 Defender 数据连接器以[收集事件和警报](connect-microsoft-365-defender.md)时，在 Microsoft 365 Defender 中生成 Microsoft 365 Defender 事件后不久，这些事件将出现在 Azure Sentinel 事件队列中，同时 Microsoft 365 Defender 将出现在“产品名称”字段中。
- 从在 Microsoft 365 Defender 中生成事件到事件出现在 Azure Sentinel 中，最多可能需要 10 分钟。

- 事件将引入并同步，而不会产生额外费用。

连接 Microsoft 365 Defender 集成后，所有组件警报连接器（Defender for Endpoint、Defender for Identity、Defender for Office 365、Cloud App Security）都将在后台自动连接（如果尚未连接）。 如果在连接 Microsoft 365 Defender 之后购买了任何组件许可证，则来自新产品的警报和事件仍将流向 Azure Sentinel，无需额外配置或收费。

### <a name="microsoft-365-defender-incidents-and-microsoft-incident-creation-rules"></a>Microsoft 365 Defender 事件和 Microsoft 事件创建规则

- Microsoft 365 Defender 根据来自 Microsoft 365 安全产品的警报生成的事件是使用自定义 Microsoft 365 Defender 逻辑创建而来。

- Azure Sentinel 中的 Microsoft 事件创建规则还使用（不同的）自定义 Azure Sentinel 逻辑从相同的警报创建事件。

- 这两种机制可完全一起使用，且可用于促进向新的 Microsoft 365 Defender 事件创建逻辑过渡。 但是，这种做法会为相同的警报创建重复事件。

- 为了避免对同一警报创建重复事件，建议客户在连接 Microsoft 365 Defender 时，关闭 Microsoft 365 产品（Defender for Endpoint、Defender for Identity、Defender for Office 365 和 Cloud App Security）的所有 Microsoft 事件创建规则。 这可以通过在连接器页面中禁用事件创建来完成。 请记住，如果执行此操作，事件创建规则应用的所有筛选器都不会应用于 Microsoft 365 Defender 事件集成。

    > [!NOTE]
    > 所有 Microsoft Cloud App Security 警报类型现都已加入到 Microsoft 365 Defender 中。

### <a name="working-with-microsoft-365-defender-incidents-in-azure-sentinel-and-bi-directional-sync"></a>在 Azure Sentinel 和双向同步中使用 Microsoft 365 Defender 事件

Microsoft 365 Defender 事件将出现在 Azure Sentinel 事件队列中，其产品名称为 Microsoft 365 Defender，并且具有与任何其他 Sentinel 事件类似的详细信息和功能。 每个事件都包含一个返回 Microsoft 365 Defender 门户中并行事件的链接。

随着事件在 Microsoft 365 Defender 中发展，且更多警报或实体添加到其中，Azure Sentinel 事件也将相应地进行更新。

在 Microsoft 365 Defender 或 Azure Sentinel 中对 Microsoft 365 事件的状态、关闭原因或分配所进行的更改同样会在另一个事件队列中相应地进行更新。 在应用对事件所做的更改后，将立即在两个门户中进行同步，没有延迟。 可能需要刷新才能查看最新更改。

在 Microsoft 365 Defender 中，来自一个事件的所有警报都可以传输到另一个事件，从而实现事件的合并。 发生合并时，Azure Sentinel 事件将反映所进行的更改。 一个事件将包含来自两个原始事件的所有警报，另一个事件将自动关闭，并添加“重定向”标记。

> [!NOTE]
> Azure Sentinel 中的事件最多可以包含 150 个警报。 Microsoft 365 Defender 事件所包含的警报超过这个数量。 如果将具有超过 150 个警报的 Microsoft 365 Defender 事件同步到 Azure Sentinel，则 Sentinel 事件将显示为具有“150+”个警报，并将提供指向 Microsoft 365 Defender 中并行事件的链接，可在其中看到完整的警报集合。

## <a name="advanced-hunting-event-collection"></a>高级搜寻事件收集

利用 Microsoft 365 Defender 连接器，还可以将高级搜寻事件（原始事件数据的一种类型）从 Microsoft 365 Defender 及其组件服务流式传输到 Azure Sentinel。 你目前可以收集 [Microsoft Defender For Endpoint (MDATP)](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) [高级搜寻](/windows/security/threat-protection/microsoft-defender-atp/advanced-hunting-overview)事件，并将它们直接流式传输到 Azure Sentinel 工作区中专门构建的表中。 这些表是在 Microsoft 365 Defender 门户中使用的相同架构上构建的，让你可以完全访问一组完整的高级搜寻事件，还可以执行以下操作：

- 将现有 Microsoft Defender for Endpoint 高级搜寻查询轻松复制到 Azure Sentinel。

- 使用原始事件日志提供有关警报、搜寻和调查的进一步见解，并将这些事件与 Azure Sentinel 中其他数据源的事件关联起来。

- 存储保留期更长的日志，其保留期超出了 Microsoft Defender for Endpoint 或 Microsoft 365 Defender 默认的 30 天。 为此，可配置工作区的保留期，或在 Log Analytics 中配置每个表的保留期。

## <a name="next-steps"></a>后续步骤

在本文档中，你学习了如何从通过 Microsoft 365 Defender 连接器将 Microsoft 365 Defender 与 Azure Sentinel 一起使用中获益。

- 获取有关如何[启用 Microsoft 365 Defender 连接器](connect-microsoft-365-defender.md)的说明。
- 创建[自定义警报](detect-threats-custom.md)并[调查事件](investigate-cases.md)。
