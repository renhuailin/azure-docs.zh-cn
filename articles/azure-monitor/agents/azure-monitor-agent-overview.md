---
title: Azure Monitor 代理概述
description: Azure Monitor 代理概述，该代理从虚拟机的来宾操作系统中收集监视数据。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/21/2021
ms.custom: references_regions
ms.openlocfilehash: 8eb9a6ad285bea2a5ac6e69ebb5ebef48b135899
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129713131"
---
# <a name="azure-monitor-agent-overview"></a>Azure Monitor 代理概述
Azure Monitor 代理 (AMA) 从 Azure 虚拟机的来宾操作系统中收集监视数据，并将数据交付给 Azure Monitor。 本文概述了 Azure Monitor 代理，提供了有关如何安装它以及如何配置数据收集的信息。

## <a name="relationship-to-other-agents"></a>与其他代理之间的关系
Azure Monitor 代理替代了 Azure Monitor 当前使用的以下旧版代理来从虚拟机收集来宾数据（[查看已知差异](/azure/azure-monitor/faq#is-the-new-azure-monitor-agent-at-parity-with-existing-agents)）：

- [Log Analytics 代理](./log-analytics-agent.md)：将数据发送到 Log Analytics 工作区，并支持 VM 见解和监视解决方案。
- [诊断扩展](./diagnostics-extension-overview.md)：将数据发送到 Azure Monitor 指标（仅 Windows）、Azure 事件中心和 Azure 存储。
- [Telegraf 代理](../essentials/collect-custom-metrics-linux-telegraf.md)：将数据发送到 Azure Monitor 指标（仅 Linux）。

除了将此功能合并到单个代理外，相对于现有代理，Azure Monitor 代理还具有以下优势：

- 监视范围：集中配置来自不同虚拟机集的不同数据集的收集。
- Linux 多宿主：将数据从 Linux VM 发送到多个工作区。
- Windows 事件筛选：使用 XPATH 查询来筛选要收集的 Windows 事件。
- 改进的扩展管理：与当前的 Log Analytics 代理中的管理包和 Linux 插件相比，Azure Monitor 代理使用一种新的方法来处理扩展性，新方法更加透明和可控。

### <a name="current-limitations"></a>当前限制
与现有代理相比，此新代理尚没有完整的奇偶校验。
- 与 Log Analytics 代理 (MMA/OMS) 对比：
    - 目前并非所有 Log Analytics 解决方案都受支持。 请参阅[支持的内容](#supported-services-and-features)。
    - 不支持 Azure 专用链接。
    - 不支持收集基于文件的日志或 IIS 日志。
- 与 Azure 诊断扩展 (WAD/LAD) 对比：
    - 不支持将事件中心和存储帐户作为目标。
    - 不支持收集基于文件的日志、IIS 日志、ETW 事件、.NET 事件和故障转储。

### <a name="changes-in-data-collection"></a>数据收集方面的变化
用于定义现有代理的数据收集的方法各有不同， 每种方法都存在需要通过 Azure Monitor 代理解决的难题。

- Log Analytics 代理从 Log Analytics 工作区获取其配置。 集中配置很容易，但难以为不同的虚拟机定义独立定义。 这种方法只能将数据发送到 Log Analytics 工作区。
- 诊断扩展具有每种虚拟机的配置。 为不同的虚拟机定义独立的定义很容易，但难以集中管理。 这种方法只能将数据发送到 Azure Monitor 指标、Azure 事件中心或 Azure 存储。 对于 Linux 代理，需要使用开源 Telegraf 代理才能将数据发送到 Azure Monitor 指标。

Azure Monitor 代理使用[数据收集规则](data-collection-rule-overview.md)来配置要从每个代理收集的数据。 数据收集规则实现了大规模的集合设置的可管理性，同时还为计算机的子集启用了唯一的、有作用域的配置。 这些规则独立于工作区，并且独立于虚拟机，因此定义一次之后，就可以在计算机和环境中重复使用。 请参阅[为 Azure Monitor 代理配置数据收集](data-collection-rule-azure-monitor-agent.md)。

## <a name="should-i-switch-to-the-azure-monitor-agent"></a>我是否应该切换到 Azure Monitor 代理？
Azure Monitor 代理替代了 [Azure Monitor 的旧版代理](agents-overview.md)。 若要开始将 VM 的当前代理转换为新代理，请考虑以下因素：

- 环境要求：Azure Monitor 代理当前支持[这些操作系统](./agents-overview.md#supported-operating-systems)。 在此新代理中，最有可能会提供对未来的操作系统版本、环境支持和网络要求的支持。 
 
  评估 Azure Monitor 代理是否支持你的环境。 如果不支持，则可能需要保留当前代理。 如果 Azure Monitor 代理支持你的当前环境，则应考虑转换到该代理。
- 当前的和新的功能要求：Azure Monitor 代理引入了多项新功能，例如筛选、限定范围和多宿主。 但在其他功能方面，例如自定义日志收集以及与所有解决方案的集成方面，它还不能与当前的代理相提并论。 （[查看预览版解决方案](/azure/azure-monitor/faq#which-log-analytics-solutions-are-supported-on-the-new-azure-monitor-agent)。） 
 
  Azure Monitor 中的大多数新功能将只能通过 Azure Monitor 代理使用。 随着时间的推移，更多功能将只能在新代理中使用。 请考虑一下， Azure Monitor 代理是否有你需要的功能？你是否可以临时执行某些功能，而不必获取新代理中的其他重要功能？ 
  
  如果 Azure Monitor 代理具有你需要的所有核心功能，则考虑转换到 Azure Monitor 代理。 如果当前代理有你需要的关键功能，则继续使用当前代理，直到 Azure Monitor 代理能与之媲美。
- 返工容错性：如果要使用部署脚本和加入模板等资源设置新环境，请评估所涉及的工作。 如果此设置操作需要大量工作，请考虑使用新代理来设置新环境，因为新代理现已正式发布。 
 
  Log Analytics 代理的弃用日期将于 2021 年 8 月发布。 在弃用后，我们仍会在几年内继续为当前代理提供支持。

## <a name="supported-resource-types"></a>支持的资源类型
目前支持 Azure 虚拟机、虚拟机规模集和启用了 Azure Arc 的服务器。 目前不支持 Azure Kubernetes 服务和其他计算资源类型。

## <a name="supported-regions"></a>支持的区域
Azure Monitor 代理在所有支持 Log Analytics 的公共区域及 Azure 政府云和中国云中可用。 目前尚不支持气隙云。

## <a name="supported-operating-systems"></a>支持的操作系统
有关 Azure Monitor 代理目前支持的 Windows 和 Linux 操作系统版本的列表，请参阅[支持的操作系统](agents-overview.md#supported-operating-systems)。

## <a name="supported-services-and-features"></a>支持的服务和功能
下表显示了其他 Azure 服务对 Azure Monitor 代理的当前支持。

| Azure 服务 | 当前支持 | 详细信息 |
|:---|:---|:---|
| [Azure 安全中心](../../security-center/security-center-introduction.md) | 个人预览版 | [注册链接](https://aka.ms/AMAgent) |
| [Azure Sentinel](../../sentinel/overview.md) | <ul><li>Windows 事件转发 (WEF)：个人预览版</li><li>Windows 安全事件：[公共预览版](../../sentinel/connect-windows-security-events.md?tabs=AMA)</li></ul>  | <ul><li>[注册链接](https://aka.ms/AMAgent) </li><li>不需要注册</li></ul> |

下表显示了 Azure Monitor 功能对 Azure Monitor 代理的当前支持。

| Azure Monitor 功能 | 当前支持 | 详细信息 |
|:---|:---|:---|
| [VM 见解](../vm/vminsights-overview.md) | 个人预览版  | [注册链接](https://aka.ms/amadcr-privatepreviews) |
| [使用专用链接或 AMPLS 进行连接](../logs/private-link-security.md) | AMA 的个人预览版 | [注册链接](https://aka.ms/amadcr-privatepreviews) |
| [VM 见解来宾运行状况](../vm/vminsights-health-overview.md) | 公共预览版 | 仅在新代理上可用 |
| [SQL 见解](../insights/sql-insights-overview.md) | 公共预览版 | 仅在新代理上可用 |

下表显示了 Azure 解决方案对 Azure Monitor 代理的当前支持。

| 解决方案 | 当前支持 | 详细信息 |
|:---|:---|:---|
| [更改跟踪](../../automation/change-tracking/overview.md) | 在 Azure 安全中心个人预览版中通过文件完整性监视功能提供此支持。  | [注册链接](https://aka.ms/AMAgent) |
| [更新管理](../../automation/update-management/overview.md) | 使用不需要代理的更新管理 v2（个人预览版）。 | [注册链接](https://www.yammer.com/azureadvisors/threads/1064001355087872) |

## <a name="coexistence-with-other-agents"></a>与其他代理共存
Azure Monitor 代理可以与现有代理共存，因此你可以在评估或迁移期间继续使用其现有功能。 由于存在对现有解决方案的支持限制，此功能尤为重要。 收集重复数据时要小心，因为它可能会导致查询结果扭曲，并导致在引入和保留数据时产生更多的费用。 

例如，VM Insights 使用 Log Analytics 代理将性能数据发送到 Log Analytics 工作区。 你还可能已将工作区配置为从代理收集 Windows 事件和 Syslog 事件。 如果安装 Azure Monitor 代理，并为这些相同的事件和性能数据创建了数据收集规则，则会导致重复数据。

因此，请确保未从两个代理收集相同的数据。 如果收集的数据相同，请确保将这些数据发往不同的目标。

## <a name="costs"></a>成本
Azure Monitor 代理不收取任何费用，但引入的数据可能产生费用。 若要了解 Log Analytics 数据收集和保留的详细信息以及客户指标，请参阅 [Azure Monitor 定价](https://azure.microsoft.com/pricing/details/monitor/)。

## <a name="data-sources-and-destinations"></a>数据源和目标
下表列出了当前可以使用数据收集规则通过 Azure Monitor 代理收集的数据类型，以及可以将该数据发送到的位置。 若要获取见解、解决方案的列表以及使用 Azure Monitor 代理收集其他类型的数据的其他解决方案的列表，请参阅 [Azure Monitor 监视哪些内容？](../monitor-reference.md)。

Azure Monitor 代理会将数据发送到 Azure Monitor 指标（预览版）或发送到支持 Azure Monitor 日志的 Log Analytics 工作区。

| 数据源 | Destinations | 说明 |
|:---|:---|:---|
| 性能        | Azure Monitor 指标（预览版）<sup>1</sup><br>Log Analytics 工作区 | 度量操作系统和工作负荷的各方面性能的数值 |
| Windows 事件日志 | Log Analytics 工作区 | 发送到 Windows 事件日志记录系统的信息 |
| Syslog             | Log Analytics 工作区 | 发送到 Linux 事件日志记录系统的信息 |

<sup>1</sup> [单击此处](../essentials/metrics-custom-overview.md#quotas-and-limits)可查看使用 Azure Monitor 指标时的其他限制。 在 Linux 上，v.1.10.9.0 或更高版本支持使用 Azure Monitor 指标作为唯一目标。 

## <a name="security"></a>安全性
Azure Monitor 代理无需任何密钥，而是需要[系统分配的托管标识](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity)。 部署代理之前，必须在每个虚拟机上启用系统分配的托管标识。

## <a name="networking"></a>网络
Azure Monitor 代理支持 Azure 服务标记。 AzureMonitor 和 AzureResourceManager 标记都是必需的。 Azure Monitor 代理尚不能用于 Azure Monitor 专用链接范围。 如果计算机通过代理服务器建立连接，以便通过 Internet 进行通信，请查看以下要求来了解所需的网络配置。

### <a name="proxy-configuration"></a>代理配置

Windows 和 Linux 的 Azure Monitor 代理扩展可以使用 HTTPS 协议通过代理服务器或 Log Analytics 网关与 Azure Monitor 进行通信。 请将其用于 Azure 虚拟机、Azure 虚拟机规模集和 Azure Arc for servers。 将扩展设置用于配置，如以下步骤所述。 匿名身份验证和基本身份验证（使用用户名/密码）都受支持。

> [!IMPORTANT]
> [Azure Monitor 指标（预览版）](../essentials/metrics-custom-overview.md)不支持代理配置作为目标。 因此，如果将指标发送到此目标，则将使用公共互联网而没有任何代理。

1. 使用此流程图，首先确定 setting 和 protectedSetting 参数的值 。

   ![启用扩展时用于确定 setting 和 protectedSetting 参数的值的流程图。](media/azure-monitor-agent-overview/proxy-flowchart.png)


2. 确定 setting 和 protectedSetting 参数的值后，请在使用 PowerShell 命令部署 Azure Monitor 代理时，提供这些附加参数。  以下示例适用于 Azure 虚拟机。

    | 参数 | 值 |
    |:---|:---|
    | SettingString | 前面的流程图中的一个 JSON 对象，已转换为字符串。 如果不适用，则跳过。 示例：{"proxy":{"mode":"application","address":"http://[address]:[port]","auth": false}}。 |
    | ProtectedSettingString | 前面的流程图中的一个 JSON 对象，已转换为字符串。 如果不适用，则跳过。 示例：{"proxy":{"username": "[username]","password": "[password]"}}。 |


# <a name="windows"></a>[Windows](#tab/PowerShellWindows)
```powershell
Set-AzVMExtension -ExtensionName AzureMonitorWindowsAgent -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location> -TypeHandlerVersion 1.0 -SettingString <settingString> -ProtectedSettingString <protectedSettingString>
```

# <a name="linux"></a>[Linux](#tab/PowerShellLinux)
```powershell
Set-AzVMExtension -ExtensionName AzureMonitorLinuxAgent -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location> -TypeHandlerVersion 1.5 -SettingString <settingString> -ProtectedSettingString <protectedSettingString>
```

---

## <a name="next-steps"></a>后续步骤

- 在 Windows 和 Linux 虚拟机上[安装 Azure Monitor 代理](azure-monitor-agent-install.md)。
- [创建数据收集规则](data-collection-rule-azure-monitor-agent.md)，通过代理收集数据并将其发送给 Azure Monitor。
