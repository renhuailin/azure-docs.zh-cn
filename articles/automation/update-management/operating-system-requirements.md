---
title: Azure 自动化更新管理支持的客户端
description: 本文介绍 Azure 自动化更新管理支持的 Windows 和 Linux 操作系统。
services: automation
ms.subservice: update-management
ms.date: 07/14/2021
ms.topic: conceptual
ms.openlocfilehash: 888123d1e1b2b87313f44f8b266d969cbff5bcde
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114460483"
---
# <a name="operating-systems-supported-by-update-management"></a>更新管理支持的操作系统

本文详细介绍了更新管理支持的 Windows 和 Linux 操作系统，以及更新管理管理的计算机或服务器的系统要求。

## <a name="supported-operating-systems"></a>支持的操作系统

下表列出了适用于更新评估和修补的支持的操作系统。 修补需要一个系统混合 Runbook 辅助角色（在你通过更新管理启用虚拟机或服务器进行管理时自动安装）。 有关混合 Runbook 辅助角色系统需求的信息，请参阅[部署 Windows 混合 Runbook 辅助角色](../automation-windows-hrw-install.md#prerequisites)和[部署 Linux 混合 Runbook 辅助角色](../automation-linux-hrw-install.md#prerequisites)。

所有操作系统都假定为 x64。 任何操作系统均不支持 x86。

> [!NOTE]
> 仅自动化帐户和 Log Analytics 工作区[映射表](../how-to/region-mappings.md#supported-mappings)中列出的特定区域支持 Linux 计算机的更新评估。

|操作系统  |说明  |
|---------|---------|
|Windows Server 2019（包括 Server 核心的数据中心/标准）<br><br>Windows Server 2016（不包括 Server 核心的数据中心/标准）<br><br>Windows Server 2012 R2(Datacenter/Standard)<br><br>Windows Server 2012 | |
|Windows Server 2008 R2（RTM 和 SP1 Standard）| 更新管理仅支持对此操作系统进行评估和修补。 Windows Server 2008 R2 不支持[混合 Runbook 辅助角色](../automation-windows-hrw-install.md)。 |
|CentOS 6、7 和 8       | Linux 代理需要具有访问更新存储库的权限。 基于分类的修补需要借助 `yum` 来返回 CentOS 的 RTM 版本中没有的安全数据。 有关 CentOS 上基于分类的修补的详细信息，请参阅 [Linux 上的更新分类](view-update-assessments.md#linux)。          |
|Oracle Linux 6.x、7.x、8x | Linux 代理需要具有访问更新存储库的权限。        |
|Red Hat Enterprise 6、7 和 8      | Linux 代理需要具有访问更新存储库的权限。        |
|SUSE Linux Enterprise Server 12、15、15.1 和 15.2      | Linux 代理需要具有访问更新存储库的权限。     |
|Ubuntu 14.04 LTS、16.04 LTS、18.04 LTS 和 20.04 LTS       |Linux 代理需要具有访问更新存储库的权限。         |

> [!NOTE]
> 更新管理不支持对 Azure 虚拟机规模集中的所有实例安全地自动执行更新管理。 建议使用[自动 OS 映像升级](../../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)来管理规模集的 OS 映像升级。

## <a name="unsupported-operating-systems"></a>不支持的操作系统

下表列出了更新管理不支持的操作系统：

|操作系统  |说明  |
|---------|---------|
|Windows 客户端     | 不支持客户端操作系统（例如 Windows 7 和 Windows 10）。<br> 对于 Azure Windows 虚拟桌面 (WVD)，管理更新<br> 的推荐方法是使用 [Microsoft Endpoint Configuration Manager](../../virtual-desktop/configure-automatic-updates.md) 实现 Windows 10 客户端计算机修补程序管理。 |
|Windows Server 2016 Nano Server     | 不支持。       |
|Azure Kubernetes 服务节点 | 不支持。 使用[对 Azure Kubernetes 服务 (AKS) 中的 Linux 节点应用安全和内核更新](../../aks/node-updates-kured.md)中所述的修补过程|

## <a name="system-requirements"></a>系统要求

以下信息介绍操作系统特定的要求。 有关其他指南，请参阅[网络规划](plan-deployment.md#ports)。 若要了解 TLS 1.2 的要求，请参阅[为 Azure 自动化使用 TLS 1.2](../automation-managing-data.md#tls-12-for-azure-automation)。

### <a name="windows"></a>Windows

软件要求：

- 需要 .NET Framework 4.6 或更高版本。 （[下载 .NET Framework](/dotnet/framework/install/guide-for-developers)。
- 需要 Windows PowerShell 5.1（[下载 Windows Management Framework 5.1](https://www.microsoft.com/download/details.aspx?id=54616)。）
- 更新管理功能取决于系统混合 Runbook 辅助角色，你应确认其[系统要求](../automation-windows-hrw-install.md#prerequisites)。

Windows 更新代理也必须配置为与 Windows Server Update Services (WSUS) 服务器通信或需要有权访问 Microsoft 更新。 对于混合计算机，我们建议通过首先将计算机连接到[启用了 Azure Arc 的服务器](../../azure-arc/servers/overview.md)来安装适用于 Windows 的 Log Analytics 代理，然后使用 Azure Policy 分配[将 Log Analytics 代理部署到 Windows Azure Arc 计算机](../../governance/policy/samples/built-in-policies.md#monitoring)内置策略。 或者，如果计划使用 VM Insights 监视计算机，请改用[启用 Enable VM Insights](../../governance/policy/samples/built-in-initiatives.md#monitoring) 计划。

可以将更新管理与 Microsoft Endpoint Configuration Manager 配合使用。 若要了解有关集成方案的详细信息，请参阅[将更新管理与 Microsoft Endpoint Configuration Manager](mecmintegration.md)。 对于由 Configuration Manager 环境中的站点托管的 Windows 服务器，需要[适用于 Windows 的 Log Analytics 代理](../../azure-monitor/agents/agent-windows.md)。

默认情况下，从 Azure 市场部署的 Windows VM 设置为从 Windows 更新服务接收自动更新。 将 Windows VM 添加到工作区时，此行为不会更改。 如果不主动使用更新管理来管理更新，则会应用默认行为（即自动应用更新）。

> [!NOTE]
> 可以修改组策略，以便仅由用户而非系统来执行计算机重启。 如果在用户不进行手动交互的情况下，更新管理无权重启计算机，则托管计算机可能会停滞。 有关详细信息，请参阅[配置自动更新的组策略设置](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates)。

### <a name="linux"></a>Linux

软件要求：

- 计算机需要有权访问专用或公共的更新存储库。
- 需要 TLS 1.1 或 TLS 1.2 才能与更新管理进行交互。
- 更新管理功能取决于系统混合 Runbook 辅助角色，你应确认其[系统要求](../automation-linux-hrw-install.md#prerequisites)。 由于更新管理使用自动化 Runbook 来启动计算机的评估和更新，因此请查看支持的 Linux 发行版[所需的 Python 版本](../automation-linux-hrw-install.md#supported-runbook-types)。

> [!NOTE]
> 仅特定区域支持 Linux 计算机的更新评估。 请参阅自动化帐户和 Log Analytics 工作区[映射表](../how-to/region-mappings.md#supported-mappings)。

对于混合计算机，我们建议通过首先将计算机连接到[启用了 Azure Arc 的服务器](../../azure-arc/servers/overview.md)来安装适用于 Linux 的 Log Analytics 代理，然后使用 Azure Policy 分配[将 Log Analytics 代理部署到 Linux Azure Arc 计算机](../../governance/policy/samples/built-in-policies.md#monitoring)内置策略。 或者，如果计划使用用于 VM 的 Azure Monitor 来监视计算机，请改用[启用用于 VM 的 Azure Monitor](../../governance/policy/samples/built-in-initiatives.md#monitoring) 计划。

## <a name="next-steps"></a>后续步骤

在启用和使用更新管理之前，请查看[计划更新管理部署](plan-deployment.md)。