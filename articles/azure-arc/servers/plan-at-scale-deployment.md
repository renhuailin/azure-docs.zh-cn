---
title: 如何规划启用了 Azure Arc 的服务器的大规模部署
description: 了解如何为启用了 Azure Arc 的服务器启用大量计算机，以简化 Azure 中的基本安全、管理和监视功能的配置。
ms.date: 02/23/2021
ms.topic: conceptual
ms.openlocfilehash: fd02e7c0b4d65efde13fbc428a15d60adab174d4
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101692980"
---
# <a name="planing-for-an-at-scale-deployment-of-azure-arc-enabled-servers"></a>适用于支持 Azure Arc 的服务器的大规模部署的 Planing

IT 基础结构服务或业务应用程序的部署对于任何公司都是一项挑战。 若要良好地执行此方法并避免任何不受欢迎的意外成本，你需要对其进行全面规划，以确保尽可能做好准备。 若要计划大规模部署启用了 Azure Arc 的服务器，它应涵盖需要满足的设计和部署标准，才能成功完成任务以支持大规模部署。

为了顺利进行部署，你的计划应明确了解以下内容：

* 角色和职责。
* 对物理服务器或虚拟机进行清点，以验证它们是否满足网络和系统要求。
* 启用成功的部署和日常管理所需的技能集和培训。
* 验收条件以及您跟踪其成功的方式。
* 用于自动执行部署的工具或方法。
* 确定了风险和缓解计划，以避免延迟、中断等。
* 如何在部署过程中避免中断。
* 出现重大问题时，升级路径是什么？

本文旨在确保你已准备好在你的环境中的多个生产物理服务器或虚拟机上成功部署支持 Azure Arc 的服务器。

## <a name="prerequisites"></a>先决条件

* 计算机为连接的计算机代理运行 [受支持的操作系统](agent-overview.md#supported-operating-systems) 。
* 你的计算机直接或通过代理服务器连接到 Azure 中的本地网络或其他云环境中的资源。
* 安装和配置启用了 Arc 的服务器连接的计算机代理，具有提升 (的帐户，该帐户是计算机上的管理员或根) 特权。
* 若要将计算机加入，你必须是 **Azure Connected Machine 加入** 角色的成员。
* 若要读取、修改和删除计算机，你是 **Azure 连接的计算机资源管理员** 角色的成员。

## <a name="pilot"></a>试点

在部署到所有生产计算机之前，首先评估此部署过程，然后在环境中广泛采用它。 对于试验，确定对公司开展业务不重要的计算机的代表性采样。 你需要确保留出足够的时间来运行试验并评估其影响：我们建议最少30天。

建立说明试验范围和详细信息的正式计划。 下面的示例显示了计划在帮助入门时应包括的内容。

* 目标-介绍导致需要试点的业务和技术驱动因素。
* 选择条件-指定用于选择将通过试验演示的解决方案的各个方面的条件。
* 作用域-介绍试验范围，其中包括但不限于解决方案组件、预计计划、试验持续时间和目标计算机的数量。
* "成功条件" 和 "指标"-定义试验的成功条件和用于确定成功级别的具体措施。
* 培训计划-介绍培训系统工程师、管理员等的计划，这些计划是在试验期间对 Azure 和 it 服务的新用户。
* 过渡计划-介绍用于引导从试点转换到生产的策略和标准。
* Rollback-介绍将试点回滚到预先部署状态的过程。
* 风险-列出了执行试验并与生产部署相关联的所有已确定的风险。

## <a name="phase-1-build-a-foundation"></a>阶段1：构建基础

在此阶段中，系统工程师或管理员可在其组织的 Azure 订阅中启用核心功能，在启用启用了 Arc 的服务器和其他 Azure 服务的管理之前，启动基础。

|任务 |详细信息 |持续时间 |
|-----|-------|---------|
| [创建资源组](../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) | 专用资源组，仅包括启用了 Arc 的服务器，并对这些资源进行集中管理和监视。 | 一小时 |
| 应用 [标记](../../azure-resource-manager/management/tag-resources.md) 以帮助组织计算机。 | 评估和开发一个 IT 协调的 [标记策略](/cloud-adoption-framework/decision-guides/resource-tagging/) ，有助于降低管理启用 Arc 的服务器的复杂性，并简化管理决策。 | 一天 |
| 设计和部署 [Azure Monitor 日志](../../azure-monitor/logs/data-platform-logs.md) | 评估 [设计和部署注意事项](../../azure-monitor/logs/design-logs-deployment.md) ，确定你的组织是否应使用现有的或实现另一个 Log Analytics 工作区来存储混合服务器和计算机的收集的日志数据。<sup>1</sup> | 一天 |
| [制定 Azure 策略](../../governance/policy/overview.md) 调控计划 | 确定如何在 Azure 策略的订阅或资源组范围内实现混合服务器和计算机的管理。 | 一天 |
| 配置 [基于角色的访问控制](../../role-based-access-control/overview.md) (RBAC)  | 开发访问计划以控制谁有权管理启用了 Arc 的服务器，以及如何从其他 Azure 服务和解决方案查看其数据。 | 一天 |
| 识别已安装 Log Analytics 代理的计算机 | 在 [Log Analytics](../../azure-monitor/logs/log-analytics-overview.md) 中运行以下日志查询，以支持将现有 Log Analytics 代理部署转换为扩展托管代理：<br> 检测信号 <br> &#124;，其中 TimeGenerated > 前 (30d)  <br> &#124;，ResourceType = = "计算机" 和 (ComputerEnvironment = = "非 Azure" )  <br> &#124; 按计算机、ResourceProvider、ResourceType、ComputerEnvironment 汇总 | 一小时 |

<sup>1</sup> 在评估 Log Analytics 工作区设计的过程中，需要考虑的一个重要事项是，与 azure 自动化集成，以支持其更新管理、更改跟踪和清单功能，以及 Azure 安全中心和 azure Sentinel。 如果你的组织已有一个自动化帐户，并且已启用其管理功能与 Log Analytics 工作区链接，请评估你是否可以集中和简化管理操作，并使用这些现有资源来最大程度地降低成本，并创建重复帐户、工作区等。

## <a name="phase-2-deploy-arc-enabled-servers"></a>阶段2：部署启用 Arc 的服务器

接下来，通过准备部署和执行代理安装，将添加到阶段1中的基础。

|任务 |详细信息 |持续时间 |
|-----|-------|---------|
| 下载预定义的安装脚本 | 查看并自定义用于已连接计算机代理的大规模部署的预定义安装脚本，以支持自动部署要求。<br><br> 大规模载入资源示例：<br><br> * [大规模基本部署脚本](servers/onboard-service-principal.md)<br><br> * [Windows Server Vm VMware vSphere 的大规模载入](https://github.com/microsoft/azure_arc/blob/master/azure_arc_servers_jumpstart/docs/vmware_scaled_powercli_win.md)<br><br> * [Linux Vm VMware vSphere 的大规模载入](https://github.com/microsoft/azure_arc/blob/master/azure_arc_servers_jumpstart/docs/vmware_scaled_powercli_linux.md)<br><br> * [使用 Ansible 的大规模载入 AWS EC2 实例](https://github.com/microsoft/azure_arc/blob/master/azure_arc_servers_jumpstart/docs/aws_scale_ansible.md)<br><br> * [使用 PowerShell 远程处理进行大规模部署](https://docs.microsoft.com/azure/azure-arc/servers/onboard-powershell) (仅限 Windows) | 一天或多天，具体取决于要求、组织过程 (例如，更改和 Release Management) 以及使用的自动化方法。 |
| [创建服务主体](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) |创建一个服务主体，以便使用 Azure PowerShell 或从门户以非交互方式连接计算机。| 一小时 |
| 将连接的计算机代理部署到目标服务器和计算机 |使用自动化工具将脚本部署到服务器，并将其连接到 Azure。| 一天或多天，具体取决于你的发布计划，以及在分阶段推出的情况下。 |

## <a name="phase-3-manage-and-operate"></a>阶段3：管理和操作

阶段3查看管理员或系统工程师，使手动任务自动执行，在其生命周期中管理和操作连接的计算机代理和计算机。

|任务 |详细信息 |持续时间 |
|-----|-------|---------|
|创建资源运行状况警报 |如果服务器停止向 Azure 发送检测信号的时间超过15分钟，则可能表示它处于脱机状态，网络连接已被阻止，或者代理未在运行。 制定一套计划，说明你将如何做出响应并调查这些事件，并使用 [资源运行状况警报](../..//service-health/resource-health-alert-monitor-guide.md) 在开始时获得通知。<br><br> 配置警报时指定以下内容：<br> **资源类型**  = **启用了 Azure Arc 的服务器**<br> **当前资源状态**  = **不可用**<br> **以前的资源状态**  = **可用** | 一小时 |
|创建 Azure 顾问警报 | 为了获得最佳体验和最新的安全性和 bug 修复，建议将 Azure Arc enabled 服务器代理保持为最新。 将使用 [Azure Advisor 警报](../../advisor/advisor-alerts-portal.md)确定过期的代理。<br><br> 配置警报时指定以下内容：<br> **建议类型**  = **升级到最新版本的 Azure 连接计算机代理** | 一小时 |
|[将 Azure 策略分配](../../governance/policy/assign-policy-portal.md) 到订阅或资源组作用域 |将 " **启用用于 VM 的 Azure Monitor** 策略" 和 "满足你的需求的其他人" 作用域分配给订阅或资源组范围，以确保自动配置所有启用了 Arc 的服务器，以便使用用于 VM 的 Azure Monitor 进行监视。| 多种多样 |
|[启用启用了 Arc 的服务器的更新管理](../../automation/update-management/enable-from-automation-account.md) |在 Azure 自动化中配置更新管理，以便为在启用 Arc 的服务器上注册的 Windows 和 Linux 虚拟机管理操作系统更新。 | 15 分钟 |

## <a name="next-steps"></a>后续步骤

* 有关疑难解答信息，请参阅 [连接计算机代理疑难解答指南](troubleshoot-agent-onboard.md)。

* 了解如何通过其他 Azure 服务（如 Azure 自动化 [状态配置](../../automation/automation-dsc-overview.md) 和其他受支持的 [azure VM 扩展](manage-vm-extensions.md)）简化部署。