---
title: Azure Monitor 代理概述
description: Azure Monitor 代理 (AMA) 概述，该代理从虚拟机的来宾操作系统中收集监视数据。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/16/2021
ms.custom: references_regions
ms.openlocfilehash: 248f070c37e32cb0d90c3e31eebddc6245446828
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2021
ms.locfileid: "108765658"
---
# <a name="azure-monitor-agent-overview-preview"></a>Azure Monitor 代理概述（预览版）
Azure Monitor 代理 (AMA) 从 Azure 虚拟机的来宾操作系统中收集监视数据，并将数据交付给 Azure Monitor。 本文概述了 Azure Monitor 代理，包括如何安装以及如何配置数据收集。

## <a name="relationship-to-other-agents"></a>与其他代理之间的关系
Azure Monitor 代理会替换 Azure Monitor 当前使用的以下代理以从虚拟机收集来宾数据：

- [Log Analytics 代理](./log-analytics-agent.md) - 将数据发送到 Log Analytics 工作区，并支持 VM Insights 和监视解决方案。
- [诊断扩展](./diagnostics-extension-overview.md) - 将数据发送到 Azure Monitor 指标（仅Windows）、Azure 事件中心和 Azure 存储。
- [Telegraf 代理](../essentials/collect-custom-metrics-linux-telegraf.md) - 将数据发送到 Azure Monitor 指标（仅 Linux）。

除了将此功能合并到单个代理外，相对于现有代理，Azure Monitor 代理还具有以下优势：

- 监视范围。 集中配置来自不同虚拟机集的不同数据集的收集。  
- Linux 多主页：将数据从 Linux VM 发送到多个工作区。
- Windows 事件筛选：使用 XPATH 查询来筛选要收集的 Windows 事件。
- 改进的扩展管理：与当前 Log Analytics 代理中的管理包和 Linux 插件相比，Azure Monitor 代理使用一种新的方法来处理扩展性，新方法更加透明和可控。

### <a name="changes-in-data-collection"></a>数据收集方面的变化
用于定义现有代理的数据收集的方法各有不同，并且每种方法都需要通过 Azure Monitor 代理解决各自的难题。

- Log Analytics 代理从 Log Analytics 工作区获取其配置。 这易于集中配置，但难以为不同的虚拟机定义独立定义。 这种方法只能将数据发送到 Log Analytics 工作区。

- 诊断扩展具有每种虚拟机的配置。 这可以轻松定义不同虚拟机的独立定义，但难以集中管理。 这种方法只能将数据发送到 Azure Monitor 指标、Azure 事件中心或 Azure 存储。 对于 Linux 代理，需要开源 Telegraf 代理才能将数据发送到 Azure Monitor 指标。

Azure Monitor 代理使用[数据收集规则 (DCR)](data-collection-rule-overview.md) 来配置要从每个代理收集的数据。 数据收集规则实现了大规模的集合设置的可管理性，同时还为计算机的子集启用了唯一的、有作用域的配置。 这些规则独立于工作区，并且独立于虚拟机，允许定义一次这些规则，并可在计算机和环境中重复使用。 请参阅[为 Azure Monitor 代理（预览版）配置数据收集](data-collection-rule-azure-monitor-agent.md)。

## <a name="should-i-switch-to-azure-monitor-agent"></a>我应该切换到 Azure Monitor 代理吗？
Azure Monitor 代理与 [Azure Monitor 的正式发布代理](agents-overview.md)共存，但是你可以考虑在 Azure Monitor 代理的公共预览期间将 VM 从当前代理过渡。 做出此决定时，请考虑以下因素。

- **环境要求。** 与当前代理相比，Azure Monitor 代理对于支持的操作系统、环境和网络要求集合具有更多限制。 将来的环境支持（例如新的操作系统版本和网络要求类型）很有可能只在 Azure Monitor 代理中提供。 应评估你的环境是否受 Azure Monitor 代理支持。 如果不支持，那么需要留在当前代理。 如果 Azure Monitor 代理支持你当前的环境，则应考虑转换到 Azure Monitor 代理。
- **公共预览版风险容忍度。** 虽然已针对当前支持的方案全面测试了 Azure Monitor 代理，但该代理仍处于公共预览版。 将频繁进行版本更新和功能改进，并可能引入 bug。 你应该评估 VM 上代理出现以下 bug 的风险：导致数据收集停止。 如果数据收集的间隙不会对服务造成重大影响，请继续使用 Azure Monitor 代理。 如果对任何不稳定的容忍度较低，则应该一直使用正式发布的代理，直至 Azure Monitor 代理达到此状态。
- **当前功能和新功能的要求。** Azure Monitor 代理引入了几种新功能，如筛选、限定作用域和多宿主，但目前还无法与其他功能（例如自定义日志收集以及与解决方案集成）的当前代理相提并论。 Azure Monitor 中的大多数新功能仅适用于 Azure Monitor 代理，因此，越来越多的功能将逐渐仅在新代理中可用。 你应考虑 Azure Monitor 代理是否具有你需要的功能，以及是否可以在不获取新代理中的其他重要功能的情况下临时执行的某些功能。 如果 Azure Monitor 代理具有你需要的所有核心功能，请考虑转换为 Azure Monitor 代理。 如果有一些你需要的关键功能，则继续使用当前代理，直到 Azure Monitor 代理能与之媲美。
- **改编的容忍度。** 如果你正在为新环境设置资源（如部署脚本和载入模板），你应考虑在 Azure Monitor 代理正式发布后是否能够对其进行改编。 如果这种改编的工作量很小，请暂时继续使用当前代理。 如果改变需要大量工作，请考虑使用新的代理设置新环境。 Azure Monitor 代理预计将于 2021 正式发布，届时将弃用 Log Analytics 代理。 一旦开始弃用，将在几年内为最新的代理提供支持。



## <a name="current-limitations"></a>当前限制
在 Azure Monitor 代理的公共预览期间，有以下限制：

- Azure Monitor 代理不支持 VM Insights 和 Azure 安全中心等解决方案和见解。 目前支持的唯一方案是使用你配置的数据收集规则来收集数据。 
- 数据收集规则必须与用作目标的任何 Log Analytics 工作区在同一区域中创建。
- 目前支持 Azure 虚拟机、虚拟机规模集和启用了 Azure Arc 的服务器。 目前不支持 Azure Kubernetes 服务和其他计算资源类型。
- 虚拟机必须具有以下 HTTPS 终结点的访问权限：
  - *.ods.opinsights.azure.com
  - *.ingest.monitor.azure.com
  - *.control.monitor.azure.com


## <a name="supported-regions"></a>支持的区域
Azure Monitor 代理目前支持以下区域中的资源：

- 东亚
- 东南亚
- 澳大利亚中部
- 澳大利亚东部
- 澳大利亚东南部
- 加拿大中部
- 北欧
- 西欧
- 法国中部
- 德国中西部
- 印度中部
- Japan East
- 韩国中部
- 南非北部
- 瑞士北部
- 英国南部
- 英国西部
- 美国中部
- 美国东部
- 美国东部 2
- 美国中北部
- 美国中南部
- 美国西部
- 美国西部 2
- 美国中西部

## <a name="coexistence-with-other-agents"></a>与其他代理共存
Azure Monitor 代理可以与现有代理共存，因此你可以在评估或迁移期间继续使用其现有功能。 这一点特别重要，因为在支持现有解决方案时公共预览版中有一些限制。 但在收集重复数据时应该小心，因为这可能会使查询结果扭曲，并导致数据提取和保留的额外费用。

例如，VM Insights 使用 Log Analytics 代理将性能数据发送到 Log Analytics 工作区。 你还可以将工作区配置为从代理收集 Windows 事件和 Syslog 事件。 如果安装 Azure Monitor 代理，并为这些相同的事件和性能数据创建了数据收集规则，则会导致重复数据。


## <a name="costs"></a>成本
Azure Monitor 代理不收取任何费用，但引入的数据可能产生费用。 有关 Log Analytics 数据收集和保留以及客户指标的详细信息，请参阅 [Azure Monitor 定价](https://azure.microsoft.com/pricing/details/monitor/)。

## <a name="data-sources-and-destinations"></a>数据源和目标
下表列出了当前可以使用数据收集规则通过 Azure Monitor 代理收集的数据类型，以及可以发送该数据的位置。 请参阅 [Azure Monitor 监视的内容是什么？](../monitor-reference.md)，获取使用 Azure Monitor 代理收集其他类型的数据的见解、解决方案和其他解决方案的列表。


Azure Monitor 代理会将数据发送到 Azure Monitor 指标或发送到支持 Azure Monitor 日志的 Log Analytics 工作区。

| 数据源 | Destinations | 说明 |
|:---|:---|:---|
| 性能        | Azure Monitor 指标<br>Log Analytics 工作区 | 测量操作系统和工作负载不同方面性能的数值。 |
| Windows 事件日志 | Log Analytics 工作区 | 发送到 Windows 事件日志记录系统的信息。 |
| Syslog             | Log Analytics 工作区 | 发送到 Linux 事件日志记录系统的信息。 |


## <a name="supported-operating-systems"></a>支持的操作系统
有关 Azure Monitor 代理目前支持的 Windows 和 Linux 操作系统版本的列表，请参阅[支持的操作系统](agents-overview.md#supported-operating-systems)。



## <a name="security"></a>安全性
Azure Monitor 代理无需任何密钥，而是需要[系统分配的托管标识](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity)。 部署代理之前，必须在每个虚拟机上启用系统分配的托管标识。

## <a name="networking"></a>网络
Azure Monitor 代理支持 Azure 服务标签（需要 AzureMonitor 和 AzureResourceManager 标签），但尚不适用于 Azure Monitor 私有链接范围或直接代理。


## <a name="next-steps"></a>后续步骤

- 在 Windows 和 Linux 虚拟机上[安装 Azure Monitor 代理](azure-monitor-agent-install.md)。
- [创建数据收集规则](data-collection-rule-azure-monitor-agent.md)，通过代理收集数据并将其发送给 Azure Monitor。
