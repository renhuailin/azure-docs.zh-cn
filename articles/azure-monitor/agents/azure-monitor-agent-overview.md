---
title: Azure Monitor 代理概述
description: Azure Monitor 代理 (AMA) 概述，该代理从虚拟机的来宾操作系统中收集监视数据。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/22/2021
ms.custom: references_regions
ms.openlocfilehash: 7c12fff502d8c4b68470370e0a5eede1dd3866a9
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2021
ms.locfileid: "114605374"
---
# <a name="azure-monitor-agent-overview"></a>Azure Monitor 代理概述
Azure Monitor 代理 (AMA) 从 Azure 虚拟机的来宾操作系统中收集监视数据，并将数据交付给 Azure Monitor。 本文概述了 Azure Monitor 代理，包括如何安装它以及如何配置数据收集。

## <a name="relationship-to-other-agents"></a>与其他代理之间的关系
Azure Monitor 代理会替换 Azure Monitor 当前使用的以下旧版代理以从虚拟机收集来宾数据（[查看已知差异](/azure/azure-monitor/faq#is-the-new-azure-monitor-agent-at-parity-with-existing-agents)）：

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

- Log Analytics 代理从 Log Analytics 工作区获取其配置。 集中配置很容易，但难以为不同的虚拟机定义独立定义。 这种方法只能将数据发送到 Log Analytics 工作区。

- 诊断扩展具有每种虚拟机的配置。 为不同的虚拟机定义独立的定义很容易，但难以集中管理。 这种方法只能将数据发送到 Azure Monitor 指标、Azure 事件中心或 Azure 存储。 对于 Linux 代理，需要开源 Telegraf 代理才能将数据发送到 Azure Monitor 指标。

Azure Monitor 代理使用[数据收集规则 (DCR)](data-collection-rule-overview.md) 来配置要从每个代理收集的数据。 数据收集规则实现了大规模的集合设置的可管理性，同时还为计算机的子集启用了唯一的、有作用域的配置。 这些规则独立于工作区，并且独立于虚拟机，允许定义一次这些规则，并可在计算机和环境中重复使用。 请参阅[为 Azure Monitor 代理配置数据收集](data-collection-rule-azure-monitor-agent.md)。

## <a name="should-i-switch-to-azure-monitor-agent"></a>我应该切换到 Azure Monitor 代理吗？
Azure Monitor 代理替换 [Azure Monitor 的旧版代理](agents-overview.md)，且你可以根据以下因素开始将 VM 从当前代理转换到新代理：

- **环境要求。** Azure Monitor 代理当前支持[这些操作系统](./agents-overview.md#supported-operating-systems)。 在此新代理中，最有可能会提供对未来的操作系统版本、环境支持和网络要求的支持。 应评估你的环境是否受 Azure Monitor 代理支持。 如果不支持，则可能需要留在当前代理。 如果 Azure Monitor 代理支持你当前的环境，则应考虑转换到 Azure Monitor 代理。
- **当前功能和新功能的要求。** Azure Monitor 代理引入了几种新功能，如筛选、限定范围和多宿主，但在自定义日志收集以及与所有解决方案（[查看预览版解决方案](/azure/azure-monitor/faq#which-log-analytics-solutions-are-supported-on-the-new-azure-monitor-agent)）集成等其他功能方面，目前还无法与当前代理相提并论。 Azure Monitor 中的大多数新功能仅适用于 Azure Monitor 代理，因此，越来越多的功能将逐渐仅在新代理中可用。 考虑 Azure Monitor 代理是否具有你需要的功能，以及是否可以在不获取新代理中的其他重要功能的情况下临时执行的某些功能。 如果 Azure Monitor 代理具有你需要的所有核心功能，则考虑转换为 Azure Monitor 代理。 如果有一些你需要的关键功能，则继续使用当前代理，直到 Azure Monitor 代理能与之媲美。
- **改编的容忍度。** 如果要使用部署脚本和加入模板等资源设置新环境，请评估所涉及的工作。 如果这需要大量工作，则考虑使用新代理来设置新环境，因为新代理现已正式发布。 Log Analytics 代理的弃用日期将于 2021 年 8 月发布。 一旦开始弃用，将在几年内为最新的代理提供支持。

## <a name="supported-resource-types"></a>支持的资源类型
目前支持 Azure 虚拟机、虚拟机规模集和启用了 Azure Arc 的服务器。 目前不支持 Azure Kubernetes 服务和其他计算资源类型。


## <a name="supported-regions"></a>支持的区域
Azure Monitor 代理在支持 Log Analytics 的所有公共区域中可用。 目前不支持政府区域和云。
## <a name="supported-services-and-features"></a>支持的服务和功能
下表显示了其他 Azure 服务对 Azure Monitor 代理的当前支持。

| Azure 服务 | 当前支持 | 详细信息 |
|:---|:---|:---|
| [Azure 安全中心](../../security-center/security-center-introduction.md) | 个人预览版 | [注册链接](https://aka.ms/AMAgent) |
| [Azure Sentinel](../../sentinel/overview.md) | 个人预览版 | [注册链接](https://aka.ms/AMAgent) |


下表显示了 Azure Monitor 功能对 Azure Monitor 代理的当前支持。

| Azure Monitor 功能 | 当前支持 | 详细信息 |
|:---|:---|:---|
| [VM 见解](../vm/vminsights-overview.md) | 个人预览版  | [注册链接](https://forms.office.com/r/jmyE821tTy) |
| [VM 见解来宾运行状况](../vm/vminsights-health-overview.md) | 公共预览版 | 仅在新代理上可用 |
| [SQL 见解](../insights/sql-insights-overview.md) | 公共预览版 | 仅在新代理上可用 |

下表显示了 Azure 解决方案对 Azure Monitor 代理的当前支持。

| 解决方案 | 当前支持 | 详细信息 |
|:---|:---|:---|
| [更改跟踪](../../automation/change-tracking/overview.md) | 支持作为 Azure 安全中心个人预览版中的文件完整性监视 (FIM)。  | [注册链接](https://aka.ms/AMAgent) |
| [更新管理](../../automation/update-management/overview.md) | 使用不需要代理的更新管理 v2（个人预览版）。 | [注册链接](https://www.yammer.com/azureadvisors/threads/1064001355087872) |



## <a name="coexistence-with-other-agents"></a>与其他代理共存
Azure Monitor 代理可以与现有代理共存，因此你可以在评估或迁移期间继续使用其现有功能。 由于支持现有解决方案的限制，这一点尤为重要。 但在收集重复数据时应该小心，因为这可能会使查询结果扭曲，并导致数据提取和保留的额外费用。 

例如，VM Insights 使用 Log Analytics 代理将性能数据发送到 Log Analytics 工作区。 你还可以将工作区配置为从代理收集 Windows 事件和 Syslog 事件。 如果安装 Azure Monitor 代理，并为这些相同的事件和性能数据创建了数据收集规则，则会导致重复数据。

因此，请确保未从两个代理收集相同的数据。 如果收集的数据相同，请确保这些数据前往不同的目标。


## <a name="costs"></a>成本
Azure Monitor 代理不收取任何费用，但引入的数据可能产生费用。 有关 Log Analytics 数据收集和保留以及客户指标的详细信息，请参阅 [Azure Monitor 定价](https://azure.microsoft.com/pricing/details/monitor/)。

## <a name="data-sources-and-destinations"></a>数据源和目标
下表列出了当前可以使用数据收集规则通过 Azure Monitor 代理收集的数据类型，以及可以发送该数据的位置。 请参阅 [Azure Monitor 监视的内容是什么？](../monitor-reference.md)，获取使用 Azure Monitor 代理收集其他类型的数据的见解、解决方案和其他解决方案的列表。


Azure Monitor 代理会将数据发送到 Azure Monitor 指标或发送到支持 Azure Monitor 日志的 Log Analytics 工作区。

| 数据源 | Destinations | 说明 |
|:---|:---|:---|
| 性能        | Azure Monitor 指标<sup>1</sup><br>Log Analytics 工作区 | 测量操作系统和工作负载不同方面性能的数值。 |
| Windows 事件日志 | Log Analytics 工作区 | 发送到 Windows 事件日志记录系统的信息。 |
| Syslog             | Log Analytics 工作区 | 发送到 Linux 事件日志记录系统的信息。 |

<sup>1</sup> 目前，适用于 Linux 的 Azure Monitor 代理存在一个限制，即不支持将 Azure Monitor 指标用作唯一目标。 将其与 Azure Monitor 日志一起使用是可行的。 此限制将在下一扩展更新中解决。

## <a name="supported-operating-systems"></a>支持的操作系统
有关 Azure Monitor 代理目前支持的 Windows 和 Linux 操作系统版本的列表，请参阅[支持的操作系统](agents-overview.md#supported-operating-systems)。



## <a name="security"></a>安全性
Azure Monitor 代理无需任何密钥，而是需要[系统分配的托管标识](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity)。 部署代理之前，必须在每个虚拟机上启用系统分配的托管标识。

## <a name="networking"></a>网络
Azure Monitor 代理支持 Azure 服务标签（需要 AzureMonitor 和 AzureResourceManager 标签），但尚不适用于 Azure Monitor 专用链接范围。 如果计算机通过代理服务器建立连接，以便通过 Internet 进行通信，请查看下面的要求以了解所需的网络配置。

### <a name="proxy-configuration"></a>代理配置

适用于 Windows 和 Linux 的 Azure Monitor 代理扩展可使用 HTTPS 协议通过代理服务器或 Log Analytics 网关与 Azure Monitor 通信（对于 Azure 虚拟机、Azure 虚拟机扩展集和 Azure Arc for servers）。 这使用如下所述的扩展设置进行配置，并且支持匿名身份验证和基本身份验证（用户名/密码）。  

1. 使用此简单的流程图，首先确定“setting”和“protectedSetting”参数的值 ：

   ![启用扩展时用于确定 setting 和 protectedSetting 参数的值的流程图](media/azure-monitor-agent-overview/proxy-flowchart.png)


2. 确定“setting”和“protectedSetting”参数的值后，在使用 PowerShell 命令部署 Azure Monitor 代理时，提供这些附加参数（以下示例适用于 Azure 虚拟机） ：

    | 参数 | 值 |
    |:---|:---|
    | SettingString | 上述流程图中的 JSON 对象，已转换为字符串；如果不适用，则跳过。 示例：{"proxy":{"mode":"application","address":"http://[address]:[port]","auth": false}} |
    | ProtectedSettingString | 上述流程图中的 JSON 对象，已转换为字符串；如果不适用，则跳过。 示例：{"proxy":{"username": "[username]","password": "[password]"}} |


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
