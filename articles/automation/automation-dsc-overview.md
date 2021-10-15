---
title: Azure Automation State Configuration 概述
description: 本文概括性介绍了 Azure Automation State Configuration。
keywords: powershell dsc, 所需状态配置, powershell dsc azure
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 08/17/2021
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 898fc7b2574040ef6095178eae9cd8e7ee747ef9
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129352893"
---
# <a name="azure-automation-state-configuration-overview"></a>Azure Automation State Configuration 概述

Azure Automation State Configuration 是一种 Azure 配置管理服务，允许为任何云或本地数据中心内的节点编写、管理和编译 PowerShell 所需状态配置 (DSC) [配置](/powershell/scripting/dsc/configurations/configurations)。 该服务还导入 [DSC 资源](/powershell/scripting/dsc/resources/resources)，并将配置分配给目标节点，一切操作均在云中完成。 可以通过选择“配置管理”下的“状态配置 (DSC)”在 Azure 门户中访问 Azure Automation State Configuration 。

> [!NOTE]
> 在启用 Automation State Configuration 之前，我们希望你知道 DSC 的较新版本目前以预览版提供，由名为[来宾配置](../governance/policy/concepts/guest-configuration.md)的 Azure Policy 功能管理。 来宾配置服务结合了 DSC 扩展、Azure Automation State Configuration 以及客户反馈中最常请求的功能。 来宾配置还包括通过[启用了 Arc 的服务器](../azure-arc/servers/overview.md)提供的混合计算机支持。

可以使用 Azure Automation State Configuration 管理各种不同的计算机：

- Azure 虚拟机
- Azure 虚拟机（经典）
- 位于本地或 Azure以外的云中的物理/虚拟 Windows 计算机（包括 AWS EC2 实例）
- 位于本地、Azure 或 Azure 以外的云中的物理/虚拟 Linux 计算机

如果未准备好从云中管理计算机配置，也可使用 Azure Automation State Configuration 作为仅限报告的终结点。 此功能允许你通过 DSC 设置（推送）配置，并在 Azure 自动化中查看报表详细信息。

> [!NOTE]
> 如果已安装的 Azure VM Desired State Configuration 扩展版本大于 2.70，则其中包含了可通过 Azure Automation State Configuration 来管理 Azure VM 的功能，且不收取额外费用。 有关详细信息，请参阅 [Automation 定价页](https://azure.microsoft.com/pricing/details/automation/)。

## <a name="why-use-azure-automation-state-configuration"></a>为何使用 Azure Automation State Configuration

与在 Azure 之外使用 DSC 相比，Azure Automation State Configuration 具有多项优势。 该服务可让你从中心的安全位置快速轻松地扩展到数千台计算机。 可以轻松启用计算机、为其分配声明性配置并查看显示每台计算机是否符合指定的所需状态的报告。

Azure Automation State Configuration 服务对于 DSC 而言如同 Azure 自动化 Runbook 对于 PowerShell 脚本而言。 换句话说，Azure 自动化以帮助你管理 PowerShell 脚本的相同方式帮助你管理 DSC 配置。

### <a name="built-in-pull-server"></a>内置拉取服务器

Azure Automation State Configuration 提供类似于 [Windows 功能 DSC 服务](/powershell/scripting/dsc/pull-server/pullserver)的 DSC 拉取服务器。 目标节点可自动接收配置、符合所需状态，以及报告其合规性。 Azure 自动化中的内置拉取服务器消除了设置和维护你自己的拉取服务器的需要。 Azure 自动化的目标可以是云中或本地的虚拟机，或物理 Windows 或 Linux 计算机。

### <a name="management-of-all-your-dsc-artifacts"></a>管理所有 DSC 项目

Azure Automation State Configuration 向 [PowerShell Desired State Configuration](/powershell/scripting/dsc/overview/overview) 提供的管理层与它为 PowerShell 脚本提供的相同。 从 Azure 门户，或从 PowerShell，你可以管理所有的 DSC 配置、资源和目标节点。

![“Azure 自动化”页的屏幕截图](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-of-reporting-data-into-azure-monitor-logs"></a>将报表数据导入 Azure Monitor 日志

使用 Azure Automation State Configuration 进行管理的节点将详细的报表状态数据发送到内置拉取服务器。 可以将 Azure Automation State Configuration 配置为将此数据发送到 Log Analytics 工作区。 请参阅[将 Azure Automation State Configuration 报告数据转发到 Azure Monitor 日志](automation-dsc-diagnostics.md)。

## <a name="prerequisites"></a>先决条件

使用 Azure Automation State Configuration 时，请想一想本节中的要求。

### <a name="operating-system-requirements"></a>操作系统要求

对于运行 Windows 的节点，支持以下版本：

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012R2
- Windows Server 2012
- Windows Server 2008 R2 SP1
- Windows 10
- Windows 8.1
- Windows 7

>[!NOTE]
>[Microsoft Hyper-V Server](/windows-server/virtualization/hyper-v/hyper-v-server-2016) 独立产品 SKU 不包含 DSC 的实现。 因此，它不能由 PowerShell DSC 或 Azure Automation State Configuration 进行管理。

对于运行 Linux 的节点，DSC Linux 扩展支持 [PowerShell DSC 文档](/powershell/scripting/dsc/getting-started/lnxgettingstarted)中列出的所有 Linux 发行版。

### <a name="dsc-requirements"></a>DSC 要求

对于在 Azure 中运行的所有 Windows 节点，在启用计算机时会安装 [WMF 5.1](/powershell/scripting/wmf/setup/install-configure)。 对于运行 Windows 服务器 2012 和 Windows 7 的节点，会启用 [WinRM](/powershell/scripting/dsc/troubleshooting/troubleshooting#winrm-dependency)。

对于在 Azure 中运行的所有 Linux 节点，将在启用计算机时安装 [PowerShell DSC for Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux)。

### <a name="configuration-of-private-networks"></a><a name="network-planning"></a>私有网络配置

查看 [Azure 自动化网络配置](automation-network-configuration.md#hybrid-runbook-worker-and-state-configuration)，以详细了解专用网络上节点所需的端口、URL 和其他网络详细信息。

#### <a name="proxy-support"></a>代理支持

Windows 版本 1809 及更高版本中提供了对 DSC 代理的代理支持。 此选项的启用方法是：在用于注册节点的[元配置脚本](automation-dsc-onboarding.md#generate-dsc-metaconfigurations)中设置 `ProxyURL` 和 `ProxyCredential` 属性的值。

>[!NOTE]
>Azure Automation State Configuration 不为早期版本的 Windows 提供 DSC 代理支持。

对于 Linux 节点，DSC 代理支持代理并使用 `http_proxy` 变量来确定 URL。 若要详细了解代理支持，请参阅[生成 DSC 元配置](automation-dsc-onboarding.md#generate-dsc-metaconfigurations)。

#### <a name="dns-records-per-region"></a>每个区域的 DNS 记录数

建议在定义异常时使用[每个区域的 DNS 记录数](how-to/automation-region-dns-records.md)表中列出的地址。

## <a name="next-steps"></a>后续步骤

- 有关入门信息，请参阅 [Azure Automation State Configuration 入门](automation-dsc-getting-started.md)。
- 要了解如何启用节点，请参阅[启用 Azure Automation State Configuration](automation-dsc-onboarding.md)。
- 若要了解如何编译 DSC 配置，以便将它们分配给目标节点，请参阅[在 Azure Automation State Configuration 中编译 DSC 配置](automation-dsc-compile.md)。
- 若要查看在持续部署管道中使用 Azure Automation State Configuration 的示例，请参阅[使用 Chocolatey 设置持续部署](automation-dsc-cd-chocolatey.md)。
- 有关定价信息，请参阅 [Azure Automation State Configuration 定价](https://azure.microsoft.com/pricing/details/automation/)。
- 有关 PowerShell cmdlet 参考，请参阅 [Az.Automation](/powershell/module/az.automation)。
