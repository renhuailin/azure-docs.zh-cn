---
title: Azure 自动化更新管理部署计划
description: 本文介绍了准备部署 Azure 自动化更新管理的注意事项和决策。
services: automation
ms.subservice: update-management
ms.date: 09/28/2021
ms.topic: conceptual
ms.openlocfilehash: bac463aa01a997122d86e32e140b135938d589da
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2021
ms.locfileid: "129093281"
---
# <a name="plan-your-update-management-deployment"></a>计划更新管理部署

## <a name="step-1---automation-account"></a>步骤 1 - 自动化帐户

更新管理是一项 Azure 自动化功能，因此需要一个自动化帐户。 你可以使用订阅中现有的自动化帐户，也可以新建一个帐户，专用于更新管理，不用于其他自动化功能。

## <a name="step-2---azure-monitor-logs"></a>步骤 2 - Azure Monitor 日志

更新管理依赖于 Azure Monitor 中的 Log Analytics 工作区来存储从托管计算机收集的评估和更新状态日志数据。 与 Log Analytics 的集成还支持在 Azure Monitor 中进行详细分析和发送警报。 你可以使用订阅中的现有工作区，也可以新建一个工作区专用于更新管理。

如果你不熟悉 Azure Monitor 日志和 Log Analytics 工作区，应查看[设计 Log Analytics 工作区](../../azure-monitor/logs/design-logs-deployment.md)部署指南。 

## <a name="step-3---supported-operating-systems"></a>步骤 3 - 支持的操作系统

更新管理支持特定版本的 Windows Server 和 Linux 操作系统。 在启用更新管理之前，请确认目标计算机满足[操作系统要求](operating-system-requirements.md)。 

## <a name="step-4---log-analytics-agent"></a>步骤 4 - Log Analytics 代理

需要适用于 Windows 和 Linux 的 [Log Analytics 代理](../../azure-monitor/agents/log-analytics-agent.md)来支持更新管理。 该代理既用于数据收集，也用于自动化系统混合 Runbook 辅助角色，以支持用于管理计算机上的评估和更新部署的更新管理 runbook。 

在 Azure VM 上，如果还没有安装 Log Analytics 代理，当你为 VM 启用更新管理时，会使用适用于 [Windows](../../virtual-machines/extensions/oms-windows.md) 或 [Linux](../../virtual-machines/extensions/oms-linux.md) 的 Log Analytics VM 扩展自动安装。 该代理被配置为向链接到自动化帐户（已启用更新管理）的 Log Analytics 工作区报告。

非 Azure VM 或服务器需要安装适用于 Windows 或 Linux 的 Log Analytics 代理，并向链接的工作区报告。 建议通过先将计算机连接到[已启用 Azure Arc 的服务器](../../azure-arc/servers/overview.md)来安装适用于 Windows 或 Linux 的 Log Analytics 代理，然后使用 Azure Policy 来分配[将 Log Analytics 代理部署到 Linux 或 Windows Azure Arc 计算机](../../governance/policy/samples/built-in-policies.md#monitoring)内置策略定义。 或者，如果计划使用 [VM 见解](../../azure-monitor/vm/vminsights-overview.md)来监视计算机，请改用[启用用于 VM 的 Azure Monitor](../../governance/policy/samples/built-in-initiatives.md#monitoring) 计划。

如果你启用当前由 Operations Manager 管理的计算机，则不需要新代理。 将管理组连接到 Log Analytics 工作区时，工作区信息会添加到代理配置中。

不支持在多个 Log Analytics 工作区（也称为多宿主）中对计算机注册更新管理。

## <a name="step-5---network-planning"></a><a name="ports"></a> 步骤 5 - 网络规划

为了准备网络以支持更新管理，可能需要配置一些基础结构组件。 例如，打开防火墙端口以传递更新管理和 Azure Monitor 使用的通信。

查看 [Azure 自动化网络配置](../automation-network-configuration.md)，了解有关更新管理所需的端口、URL 和其他网络的详细信息，包括混合 Runbook 辅助角色。 若要安全且私密地从 Azure VM 连接到自动化服务，请查看[使用 Azure 专用链接](../how-to/private-link-security.md)。 

对于 Windows 计算机，还必须允许流量发送到 Windows 更新代理所需的任何终结点。 可以在[与 HTTP/Proxy 相关的问题](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy)中找到所需终结点的更新列表。 如果拥有本地 [Windows Server Update Services](/windows-server/administration/windows-server-update-services/plan/plan-your-wsus-deployment) (WSUS) 部署，则还必须允许流量发送到 [WSUS 密钥](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry)中指定的服务器。

对于 Red Hat Linux 计算机，请参阅[适用于 RHUI 内容分发服务器的 IP](../../virtual-machines/workloads/redhat/redhat-rhui.md#the-ips-for-the-rhui-content-delivery-servers) 了解所需的终结点。 对于其他 Linux 发行版，请参阅提供程序文档。

如果 IT 安全策略不允许网络上的计算机连接到 Internet，则可以设置 [Log Analytics 网关](../../azure-monitor/agents/gateway.md)，然后将计算机配置为通过该网关连接到 Azure 自动化和 Azure Monitor。

## <a name="step-6---permissions"></a>步骤 6 - 权限

若要创建和管理更新部署，需要特定的权限。 若要了解这些权限，请参阅[基于角色的访问 - 更新管理](../automation-role-based-access-control.md#update-management-permissions)。

## <a name="step-7---windows-update-agent"></a>步骤 7 - Windows 更新代理

Azure 自动化更新管理依赖 Windows 更新代理来下载和安装 Windows 更新。 计算机上的 Windows 更新代理 (WUA) 使用特定的组策略设置来连接到 Windows Server Update Services (WSUS) 或 Microsoft 更新。 这些组策略设置还用于成功扫描软件更新的符合性，以及自动更新软件更新。 若要查看我们的建议，请参阅[为更新管理配置 Windows 更新设置](configure-wuagent.md)。

## <a name="step-8---linux-repository"></a>步骤 8 - Linux 存储库

基于 Azure 市场中提供的按需 Red Hat Enterprise Linux (RHEL) 映像创建的 VM 注册为访问 Azure 中部署的 Red Hat 更新基础结构 (RHUI)。 对于任何其他 Linux 发行版，必须使用该发行版支持的方法从发行版联机文件存储库对其进行更新。

若要对 Red Hat Enterprise 版本 6 上的更新进行分类，需要安装 yum 安全插件。 在 Red Hat Enterprise Linux 7 上，yum 本身已包含该插件，无需安装任何内容。 有关详细信息，请参阅以下 Red Hat [知识文章](https://access.redhat.com/solutions/10021)。

## <a name="step-9---plan-deployment-targets"></a>步骤 9 - 规划部署目标

通过更新管理，可以将更新定向到代表 Azure 或非 Azure 计算机的动态组，以便确保特定计算机始终在最方便的时间获得正确的更新。 动态组是在部署时解析的，并基于以下条件：

* 订阅
* 资源组
* 位置
* Tags 

对于非 Azure 计算机，动态组使用保存的搜索，也称为[计算机组](../../azure-monitor/logs/computer-groups.md)。 范围限定为一组计算机的更新部署仅在更新管理“部署计划”选项中的自动化帐户中可见，在特定的 Azure VM 中不可见。

另外，只能为选定的 Azure VM 管理更新。 范围限定为特定计算机的更新部署在计算机和更新管理“部署计划”选项中的自动化帐户中均可见。 

## <a name="next-steps"></a>后续步骤

启用更新管理并使用以下方法之一选择要管理的计算机：

- 使用 Azure [资源管理器模板](enable-from-template.md)将更新管理部署到订阅中新的或现有的自动化帐户和 Azure Monitor Log Analytics 工作区。 它不会配置应管理的计算机范围，而是在使用模板后在单独的步骤中执行此操作。

- 从一台或多台 Azure 和非 Azure 计算机（包括启用了 Arc 的服务器）的[自动化帐户](enable-from-automation-account.md)。

- 使用 Enable-AutomationSolution [runbook](enable-from-runbook.md) 自动加入 Azure VM。

- 从 Azure 门户中的“虚拟机”页为[所选 Azure VM](enable-from-vm.md) 启用。 此方案适用于 Linux 和 Windows VM。

- 可以从 Azure 门户中的“虚拟机”页选择启用[多个 Azure VM](enable-from-portal.md)。