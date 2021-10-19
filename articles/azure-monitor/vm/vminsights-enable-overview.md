---
title: 启用 VM 见解概述
description: 了解如何部署和配置 VM 见解。 了解系统要求。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/22/2020
ms.custom: references_regions
ms.openlocfilehash: 1a4c872e5df50e2874053ad460cb50ecfabfeed9
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129713379"
---
# <a name="enable-vm-insights-overview"></a>启用 VM 见解概述

本文概述了可用于启用 VM 见解以监视以下各项的运行状况和性能的选项：

- Azure 虚拟机 
- Azure 虚拟机规模集
- 与 Azure Arc 连接的混合虚拟机
- 本地虚拟机
- 托管在其他云环境中的虚拟机。  

设置 VM 见解：

* 通过直接从 Azure 门户的相应菜单中选择“见解”，启用单个 Azure 虚拟机、Azure 虚拟机规模集或 Azure Arc 计算机。
* 使用 Azure Policy 启用多个 Azure 虚拟机、Azure 虚拟机规模集或 Azure Arc 计算机。 此方法可确保在现有和新的 VM 与规模集上安装并正确配置所需的依赖项。 系统会报告不合规的虚拟机和规模集，因此你可以决定是否要启用和修正它们。
* 使用 PowerShell 跨指定的订阅或资源组启用多个 Azure 虚拟机、Azure Arc 虚拟机、Azure 虚拟机规模集或 Azure Arc 计算机。
* 启用 VM 见解，以监视企业网络或其他云环境中托管的 VM 或物理计算机。

## <a name="supported-machines"></a>支持的虚拟机
VM 见解支持以下虚拟机：

- Azure 虚拟机
- Azure 虚拟机规模集
- 与 Azure Arc 连接的混合虚拟机

> [!IMPORTANT]
> 如果虚拟机的以太网设备拥有 9 个以上字符，则不会被 VM insights 识别，也不会将数据发送到 InsightsMetrics 表。 代理将从[其他源](../agents/agent-data-sources.md)收集数据。

## <a name="supported-azure-arc-machines"></a>支持的 Azure Arc 计算机
VM insights 见解可在提供 Arc 扩展服务的区域中用于启用了 Azure Arc 的服务器。 必须运行 0.9 版或更高版本的 Arc 代理。

| 连接的源 | 支持 | 说明 |
|:--|:--|:--|
| Windows 代理 | 是 | 除[适用于 Windows 的 Log Analytics 代理](../agents/log-analytics-agent.md)以外，Windows 代理还需要依赖项代理。 有关详细信息，请参阅[支持的操作系统](../agents/agents-overview.md#supported-operating-systems)。 |
| Linux 代理 | 是 | 除[适用于 Linux 的 Log Analytics 代理](../agents/log-analytics-agent.md)以外，Linux 代理还需要依赖项代理。 有关详细信息，请参阅[支持的操作系统](#supported-operating-systems)。 |
| System Center Operations Manager 管理组 | 否 | |

## <a name="supported-operating-systems"></a>支持的操作系统

VM 见解支持所有支持 Log Analytics 代理和 Dependency Agent 的操作系统。 有关完整列表，请参阅 [Azure Monitor 代理概述](../agents/agents-overview.md#supported-operating-systems)。

> [!IMPORTANT]
> 在公共预览版中，VM 见解来宾运行状况功能支持的操作系统更加有限。 有关详细列表，请参阅[启用 VM 见解来宾运行状况（预览版）](../vm/vminsights-health-enable.md)。

### <a name="linux-considerations"></a>Linux 注意事项
请参阅下列支持 VM 见解的 Dependency Agent 的相关 Linux 支持注意事项：

- 仅默认版本和 SMP Linux 内核版本受支持。
- 任何 Linux 发行版都不支持非标准内核版本（例如物理地址扩展 [PAE] 和 Xen）。 例如，不支持版本字符串为 *2.6.16.21-0.8-xen* 的系统。
- 不支持自定义内核（包括标准内核的重新编译）。
- 除版本 9.4 以外的 Debian 发行版不支持地图功能，并且只能通过 Azure Monitor 菜单来使用性能功能。 无法直接在 Azure VM 的左窗格中使用此功能。
- 支持 CentOSPlus 内核。

必须为 Spectre 和 Meltdown 漏洞修补 Linux 内核。 有关更多详细信息，请咨询 Linux 发行版供应商。 运行以下命令，检查 Spectre/Meltdown 是否已缓解：

```
$ grep . /sys/devices/system/cpu/vulnerabilities/*
```

此命令的输出如下所示，并指定计算机是否容易出现任一问题。 如果缺少这些文件，则表明未修补计算机。

```
/sys/devices/system/cpu/vulnerabilities/meltdown:Mitigation: PTI
/sys/devices/system/cpu/vulnerabilities/spectre_v1:Vulnerable
/sys/devices/system/cpu/vulnerabilities/spectre_v2:Vulnerable: Minimal generic ASM retpoline
```


## <a name="log-analytics-workspace"></a>Log Analytics 工作区
VM 见解需要 Log Analytics 工作区。 有关此工作区的详细信息和要求，请参阅[为 VM 见解配置 Log Analytics 工作区](vminsights-configure-workspace.md)。
## <a name="agents"></a>代理
VM 见解要求在要监视的每个虚拟机或虚拟机规模集上安装以下两个代理。 若要载入资源，请安装这些代理并将其连接到工作区。  有关这些代理的网络要求，请参阅[网络要求](../agents/log-analytics-agent.md#network-requirements)。

- [Log Analytics 代理](../agents/log-analytics-agent.md)。 从虚拟机或虚拟机规模集中收集事件和性能数据，并将其传递到 Log Analytics 工作区。 用于 Azure 资源上的 Log Analytics 代理的部署方法使用适用于 [Windows](../../virtual-machines/extensions/oms-windows.md) 和 [Linux](../../virtual-machines/extensions/oms-linux.md) 的 VM 扩展。
- Dependency Agent。 收集有关在虚拟机上运行的进程和外部进程依赖关系的已发现数据，[VM 见解中的地图功能](../vm/vminsights-maps.md)会使用这些数据。 Dependency Agent 依赖于 Log Analytics 代理将其数据传递到 Azure Monitor。 用于 Azure 资源上的 Dependency Agent 的部署方法使用适用于 [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) 和 [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) 的 VM 扩展。

> [!NOTE]
> Log Analytics 代理是 System Center Operations Manager 使用的同一代理。 如果 VM 见解与 Operations Manager 直接连接，并且它们都安装了 Dependency Agent，则 VM 见解可以监视受 Operations Manager 监视的代理。 VM 见解无法监视通过[管理组连接](../tform/../agents/om-agents.md)连接到 Azure Monitor 的代理。

下面列出了多种适用于部署这些代理的方法。 

| 方法 | 说明 |
|:---|:---|
| [Azure 门户](../vm/vminsights-enable-portal.md) | 在单个虚拟机、虚拟机规模集或与 Azure Arc 连接的混合虚拟机上安装这两个代理。 |
| [资源管理器模板](../vm/vminsights-enable-resource-manager.md) | 使用任意受支持的方法安装两个代理，以部署资源管理器模板（包括 CLI 和 PowerShell）。 |
| [Azure Policy](../vm/vminsights-enable-policy.md) | 分配 Azure Policy 计划，以在创建虚拟机或虚拟机规模集时自动安装代理。 |
| [手动安装](../vm/vminsights-enable-hybrid.md) | 在 Azure 外部（包括在数据中心或其他云环境中）托管的计算机上的来宾操作系统中安装代理。 |


## <a name="network-requirements"></a>网络要求

- 有关 Log Analytics 代理的网络要求，请参阅[网络要求](../agents/log-analytics-agent.md#network-requirements)。
- Dependency Agent 需要从虚拟机连接到地址 169.254.169.254。 这是 Azure 元数据服务终结点。 确保防火墙设置允许连接到此终结点。


## <a name="management-packs"></a>管理包
为 VM 见解配置 Log Analytics 工作区后，系统会将两个管理包转发给连接到该工作区的所有 Windows 计算机。 这两个管理包分别名为 Microsoft.IntelligencePacks.ApplicationDependencyMonitor 和 Microsoft.IntelligencePacks.VMInsights，并写入 %Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs  。 

管理包 ApplicationDependencyMonitor 使用的数据源是 *%Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll 。 管理包 VMInsights 使用的数据源是 %Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\ Microsoft.VirtualMachineMonitoringModule.dll 。

## <a name="diagnostic-and-usage-data"></a>诊断和使用情况数据

Microsoft 使用 Azure Monitor 服务自动收集使用情况和性能数据。 Microsoft 使用此数据来改进服务的质量、安全性和完整性。 

为了提供准确高效的故障排除功能，映射功能包含有关软件配置的信息。 这些数据提供操作系统和版本、IP 地址、DNS 名称及工作站名称等信息。 Microsoft 不收集姓名、地址或其他联系信息。

有关数据收集和使用的详细信息，请参阅 [Microsoft Online Services 隐私声明](https://go.microsoft.com/fwlink/?LinkId=512132)。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>后续步骤

要了解如何使用性能监视功能，请参阅[查看VM 见解性能](../vm/vminsights-performance.md)。 要查看已发现的应用程序依赖关系，请参阅[查看 VM 见解地图](../vm/vminsights-maps.md)。
