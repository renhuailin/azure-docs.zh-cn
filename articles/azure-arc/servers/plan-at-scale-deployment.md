---
title: 如何规划和部署已启用 Azure Arc 的服务器
description: 了解如何在已启用 Azure Arc 的服务器中启用大量计算机，以简化 Azure 中不可或缺的安全、管理和监视功能的配置。
ms.date: 08/27/2021
ms.topic: conceptual
ms.openlocfilehash: 0a31a886d4eb687c92d73c39617a6993e4b3f835
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123104914"
---
# <a name="plan-and-deploy-arc-enabled-servers"></a>规划和部署已启用 Arc 的服务器

对于任何一家公司而言，部署 IT 基础结构服务或业务应用程序都充满了挑战。 若要顺利执行此任务并避免任何意外变故和计划外成本，需要进行全面的规划，确保做好尽量充分的准备。 若要规划已启用 Azure Arc 的服务器的任何规模的部署，应该考虑到需要满足的设计和部署条件，以便能够成功完成任务。

要使部署顺利进行，应在计划中明确以下方面的认知：

* 角色和职责。
* 清点物理服务器或虚拟机，以验证它们是否满足网络和系统要求。
* 实现成功部署和日常管理所需的技能集与培训。
* 验收条件以及如何跟踪其成功结果。
* 用于自动化部署的工具或方法。
* 已确定的风险和缓解计划，旨在避免延误、中断等问题。
* 在部署期间如何避免中断。
* 出现重大问题时的事务升级路径是什么？

本文旨在确保你充分准备好在环境中的多个生产物理服务器或虚拟机上成功部署已启用 Azure Arc 的服务器。

若要详细了解我们的大规模部署建议，还可以参考此视频。

> [!VIDEO https://www.youtube.com/embed/Cf1jUPOB_vs]

## <a name="prerequisites"></a>先决条件

* 计算机运行 Connected Machine Agent [支持的操作系统](agent-overview.md#supported-operating-systems)。
* 计算机可以直接或者通过代理服务器从本地网络或其他云环境连接到 Azure 中的资源。
* 若要安装并配置已启用 Arc 的服务器 Connected Machine Agent，需要创建一个在计算机上拥有提升特权的帐户（即管理员或 root 帐户）。
* 若要将计算机加入，你必须是 **Azure Connected Machine 加入** 角色的成员。
* 若要读取、修改和删除计算机，你需是“Azure Connected Machine 资源管理员”角色的成员。

## <a name="pilot"></a>试点

在部署到所有生产计算机之前，请先评估此部署过程，然后再在环境中广泛采用这种部署。 要进行试点，请确定几台有代表性的、对公司业务运营不很关键的计算机。 请务必留出足够的时间来运行试点并评估其影响：建议最少留出 30 天时间。

制定正式的计划用于描述试点范围和细节。 下面提供了计划中的示例内容，以帮助你着手制定自己的计划。

* 目标 - 描述哪些业务和技术驱动因素导致你认定试点是有必要的。
* 选择条件 - 指定通过试点展示解决方案的哪些方面时所要依据的条件。
* 范围 - 描述试点范围，包括但不限于解决方案组件、预计计划、试点持续时间和试点所针对的计算机数量。
* 成功条件和指标 - 定义试点的成功条件，以及用于确定成功程度的具体措施。
* 培训计划 - 描述在试点期间对 Azure 及其服务不太熟悉的系统工程师、管理员等人员的培训计划。
* 过渡计划 - 描述用于引导从试点环境过渡到生产环境的策略和条件。
* 回滚 - 描述从试点回滚到部署前状态的过程。
* 风险 - 列出所有已识别到的，与开展试点以及与生产部署相关的风险。

## <a name="phase-1-build-a-foundation"></a>第 1 阶段：构建基础

在此阶段，系统工程师或管理员将在其组织的 Azure 订阅中启用核心功能，以便在启用计算机（让已启用 Arc 的服务器和其他 Azure 服务进行管理）之前开始打下基础。

|任务 |详细信息 |持续时间 |
|-----|-------|---------|
| [创建资源组](../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) | 一个专用的资源组，仅包含已启用 Arc 的服务器，并对这些资源进行集中式管理和监视。 | 一小时 |
| 应用[标记](../../azure-resource-manager/management/tag-resources.md)来帮助对计算机进行组织。 | 评估并制定一个与 IT 部门相协调的[标记策略](/azure/cloud-adoption-framework/decision-guides/resource-tagging/)，以帮助降低管理已启用 Arc 的服务器的复杂性，并简化管理决策。 | 一天 |
| 设计并部署 [Azure Monitor 日志](../../azure-monitor/logs/data-platform-logs.md) | 评估[设计和部署注意事项](../../azure-monitor/logs/design-logs-deployment.md)，确定组织是应该使用现有的 Log Analytics 工作区还是实施另一个 Log Analytics 工作区来存储从混合服务器和计算机收集的日志数据。<sup>1</sup> | 一天 |
| [制定 Azure Policy](../../governance/policy/overview.md) 监管计划 | 确定如何使用 Azure Policy 在订阅或资源组范围实施混合服务器和计算机的监管。 | 一天 |
| 配置[基于角色的访问控制](../../role-based-access-control/overview.md) (RBAC) | 制定一个访问计划，用于控制谁有权管理已启用 Arc 的服务器并能够从其他 Azure 服务和解决方案查看这些服务器的数据。 | 一天 |
| 确定已装有 Log Analytics 代理的计算机 | 在 [Log Analytics](../../azure-monitor/logs/log-analytics-overview.md) 中运行以下日志查询，以支持将现有 Log Analytics 代理部署转换为扩展托管的代理：<br> 检测信号 <br> &#124; where TimeGenerated > ago(30d) <br> &#124; where ResourceType == "machines" and (ComputerEnvironment == "Non-Azure") <br> &#124; summarize by Computer, ResourceProvider, ResourceType, ComputerEnvironment | 一小时 |

<sup>1</sup> 在评估 Log Analytics 工作区设计的过程中，一个重要考虑因素是与 Azure 自动化（用于支持工作区的更新管理以及更改跟踪和清单功能）、Azure 安全中心和 Azure Sentinel 的集成。 如果你的组织已有一个自动化帐户并已启用其与 Log Analytics 工作区关联的管理功能，请评估是否可以集中化和简化管理操作，并利用这些现有资源来最大程度地降低成本，而不是创建重复的帐户、工作区等资源。

## <a name="phase-2-deploy-arc-enabled-servers"></a>第 2 阶段：部署已启用 Arc 的服务器

接下来，我们通过准备并部署已启用 Arc 的服务器 Connected Machine Agent，在第 1 阶段打下的基础上补充内容。

|任务 |详细信息 |持续时间 |
|-----|-------|---------|
| 下载预定义的安装脚本 | 查看并自定义用于大规模部署 Connected Machine Agent 来支持自动化部署要求的预定义安装脚本。<br><br> 大规模加入资源的示例：<br><br> <ul><li> [大规模基本部署脚本](onboard-service-principal.md)</ul></li> <ul><li>[大规模加入 VMware vSphere Windows Server VM](https://github.com/microsoft/azure_arc/blob/main/docs/azure_arc_jumpstart/azure_arc_servers/scaled_deployment/vmware_scaled_powercli_win/_index.md)</ul></li> <ul><li>[大规模加入 VMware vSphere Linux VM](https://github.com/microsoft/azure_arc/blob/main/docs/azure_arc_jumpstart/azure_arc_servers/scaled_deployment/vmware_scaled_powercli_linux/_index.md)</ul></li> <ul><li>[使用 Ansible 大规模加入 AWS EC2 实例](https://github.com/microsoft/azure_arc/blob/main/docs/azure_arc_jumpstart/azure_arc_servers/scaled_deployment/aws_scaled_ansible/_index.md)</ul></li> <ul><li>[使用 PowerShell 远程处理进行大规模部署](./onboard-powershell.md)（仅限 Windows）</ul></li>| 一天或多天，具体取决于要求、组织流程（例如，更改和发布管理）以及使用的自动化方法。 |
| [创建服务主体](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) |创建一个服务主体，以使用 Azure PowerShell 或从门户以非交互方式连接计算机。| 一小时 |
| 将 Connected Machine Agent 部署到目标服务器和计算机 |使用自动化工具将脚本部署到服务器，然后将服务器连接到 Azure。| 一天或多天，具体取决于发布计划，以及是否遵循分阶段实施方案。 |

## <a name="phase-3-manage-and-operate"></a>第 3 阶段：管理和操作

在第 3 阶段，管理员或系统工程师将使手动任务自动化，以便在 Connected Machine Agent 和计算机的整个生命周期内对其进行管理和操作。

|任务 |详细信息 |持续时间 |
|-----|-------|---------|
|创建资源运行状况警报 |如果某台服务器有 15 分钟以上停止向 Azure 发送检测信号，则可能表示该服务器处于脱机状态、网络连接已被阻止，或者代理未运行。 制定一个计划来规定如何应对和调查这些事件，并使用[资源运行状况警报](../..//service-health/resource-health-alert-monitor-guide.md)以便在这些事件开始发生时收到通知。<br><br> 配置警报时请指定以下设置：<br> **资源类型** = **已启用 Azure Arc 的服务器**<br> **当前资源状态** = **不可用**<br> **以前的资源状态** = **可用** | 一小时 |
|创建 Azure 顾问警报 | 为获得最佳体验和最新的安全修复和 bug 修复，我们建议将已启用 Azure Arc 的服务器代理保持使用最新版本。 将使用 [Azure 顾问警报](../../advisor/advisor-alerts-portal.md)来识别已过时的代理。<br><br> 配置警报时请指定以下设置：<br> **建议类型** = **升级到最新版本的 Azure Connected Machine Agent** | 一小时 |
|在订阅或资源组范围[分配 Azure 策略](../../governance/policy/assign-policy-portal.md) |在订阅或资源组范围分配“启用用于 VM 的 Azure Monitor”[策略](../../azure-monitor/vm/vminsights-enable-policy.md)（以及符合需求的其他策略）。 借助 Azure Policy，你可以分配策略定义，以便在整个环境中为 VM 见解安装所需的代理。| 多种多样 |
|[为已启用 Arc 的服务器启用更新管理](../../automation/update-management/enable-from-automation-account.md) |在 Azure 自动化中配置更新管理，以管理注册到了已启用 Arc 的服务器的 Windows 和 Linux 虚拟机的操作系统更新。 | 15 分钟 |

## <a name="next-steps"></a>后续步骤

* 在 [Connected Machine Agent 故障排除指南](troubleshoot-agent-onboard.md)中可以找到故障排除信息。

* 了解如何使用其他 Azure 服务（例如 Azure 自动化 [State Configuration](../../automation/automation-dsc-overview.md) 和其他受支持的 [Azure VM 扩展](manage-vm-extensions.md)）来简化部署。