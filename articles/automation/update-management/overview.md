---
title: Azure 自动化更新管理概述
description: 本文概述了为 Windows 和 Linux 计算机实现更新的更新管理功能。
services: automation
ms.subservice: update-management
ms.date: 09/27/2021
ms.topic: conceptual
ms.openlocfilehash: fed1ce7f236b568458f1eb1b25ce5420c2ad5501
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2021
ms.locfileid: "129092144"
---
# <a name="update-management-overview"></a>更新管理概述

你可以使用 Azure 自动化中的更新管理来管理 Azure 中的 Windows 和 Linux 虚拟机、本地环境和其他云环境中的物理或虚拟机的操作系统更新。 你可以快速评估可用更新的状态，并对向更新管理报告的计算机安装所需更新的过程进行管理。 

作为服务提供商，你可能已经将多个客户租户载入了 [Azure Lighthouse](../../lighthouse/overview.md)。 更新管理可用于评估和安排同一 Azure Active Directory (Azure AD) 租户中的或使用 Azure Lighthouse 跨租户的多个订阅中的计算机的更新部署。

Microsoft 还有其他功能，有助于管理 Azure 虚拟机或 Azure 虚拟机规模集的更新，应当将其看作总体更新管理策略的一部分。 

- 如果有兴趣自动评估和更新 Azure 虚拟机，以保持安全性与每月发布的“关键”和“安全”更新相符合，请查看[自动 VM 来宾修补”](../../virtual-machines/automatic-vm-guest-patching.md)（预览）。  与通过 Azure 自动化中的管理更新对 Azure 虚拟机（包括可用性集内的虚拟机）的更新部署进行管理相比，这是为这些虚拟机提供的另一种更新管理解决方案，可以在非高峰期自动更新这些虚拟机。 

- 对于管理 Azure 虚拟机规模集，请查看如何执行“[自动 OS 映像升级](../../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)”，以安全、自动地升级规模集内所有实例的 OS 磁盘。 

在部署更新管理并启用计算机进行管理之前，请确保你了解以下部分中的信息。

## <a name="about-update-management"></a>关于更新管理

下图说明了更新管理如何评估安全更新并将其应用于所有连接的 Windows 服务器和 Linux 服务器。

![更新管理工作流](./media/overview/update-mgmt-workflow.png)

更新管理集成了 Azure Monitor 日志，以日志数据的形式存储已分配的 Azure 计算机和非 Azure 计算机中的更新评估和更新部署结果。 为了收集这些数据，系统会绑定自动化帐户和 Log Analytics 工作区，同时，计算机上需要适用于 Windows 和 Linux 的 Log Analytics 代理，且需要将其配置为向此工作区报告。 

更新管理支持从连接到工作区的 System Center Operations Manager 管理组中的代理收集有关系统更新的信息。 不支持在多个 Log Analytics 工作区（也称为多宿主）中对计算机注册更新管理。

下表汇总了具有更新管理的受支持的已连接源。

| 连接的源 | 支持 | 说明 |
| --- | --- | --- |
| Windows |是 |更新管理通过使用 Log Analytics 代理并安装所需的更新，从 Windows 计算机收集有关系统更新的信息。<br> 计算机需要向 Microsoft 更新或 Windows Server Update Services (WSUS) 报告。 |
| Linux |是 |更新管理通过使用 Log Analytics 代理并在受支持的发行版上安装所需的更新，从 Linux 计算机收集有关系统更新的信息。<br> 计算机需要向本地或远程存储库报告。 |
| Operations Manager 管理组 |是 |更新管理从已连接的管理组中的代理收集有关软件更新的信息。<br/><br/>从 Operations Manager 代理到 Azure Monitor 日志的直接连接不是必需的。 系统会将日志数据从管理组转发到 Log Analytics 工作区。 |

分配给更新管理的计算机根据将其配置为与之同步的源报告其最新状态。 Windows 计算机需要配置为向 [Windows Server Update Services](/windows-server/administration/windows-server-update-services/get-started/windows-server-update-services-wsus) 或 [Microsoft 更新](https://www.update.microsoft.com)报告，Linux 计算机需要配置为向本地或公共存储库报告。 此外，还可以将更新管理与 Microsoft Endpoint Configuration Manager 一起使用，相关详情请参阅[集成更新管理与 Windows Endpoint Configuration Manager](mecmintegration.md)。 

如果将 Windows 计算机上的 Windows Update Agent (WUA) 配置为向 WSUS 报告，则结果可能不同于 Microsoft 更新所显示的内容，具体取决于 WSUS 上次与 Microsoft 更新同步的时间。 对于配置为向本地存储库（而非公共存储库）报告的 Linux 计算机来说，情况亦是如此。 在 Windows 计算机上，符合性扫描默认情况下每 12 小时运行一次。 对于 Linux 计算机，符合性扫描默认情况下每小时执行一次。 如果 Log Analytics 代理重启，则会在 15 分钟内启动符合性扫描。 当计算机完成更新合规性扫描时，代理会将信息批量转发到 Azure Monitor 日志。 

可以创建计划的部署，在需要更新的计算机上部署和安装软件更新。 归类为“可选”的更新不包括在 Windows 计算机的部署范围内。 只有必需的更新会包括在部署范围内。

计划的部署定义哪些目标计算机接收适用的更新。 它通过以下某种方式来实现此目的：明确指定特定的计算机，或选择基于特定计算机集的日志搜索（或基于根据指定条件动态选择 Azure VM 的 [Azure 查询](query-logs.md)）的[计算机组](../../azure-monitor/logs/computer-groups.md)。 这些组与[范围配置](../../azure-monitor/insights/solution-targeting.md)不同，后者用于控制接收配置以启用更新管理的目标计算机。 这会阻止它们执行和报告更新符合性，并安装已批准的所需更新。

定义部署时，还可以指定要批准的计划，并设置可以安装更新的一个时段。 此时段称为维护时段。 假设需要重启，并选择了相应的重启选项，则会预留 20 分钟的维护时段进行重启。 如果修补时间比预期时间长且维护时段少于 20 分钟，则不会进行重启。

为部署安排更新包后，Linux 计算机需要 2-3 小时才会显示更新以供评估。 对于 Windows 计算机，发布后，需要 12-15 小时才会显示更新以供评估。 在更新安装之前和之后，会执行更新符合性扫描，日志数据结果将转发到工作区。

通过 Azure 自动化中的 runbook 安装更新。 无法查看这些 runbook，它们不需要任何配置。 创建更新部署时，会创建一个在指定的时间为所包含的计算机启动主更新 runbook 的计划。 主 Runbook 在每个代理上启动子 Runbook，该子 Runbook 使用 Windows 上的 Windows 更新代理或受支持的 Linux 发行版上的适用命令启动所需更新的安装。

目标计算机会按更新部署中指定的日期和时间，以并行方式执行部署。 在安装之前，会运行扫描来验证更新是否仍然是必需的。 对于 WSUS 客户端计算机，如果更新未在 WSUS 中获得批准，则更新部署会失败。

## <a name="limits"></a>限制

有关适用于更新管理的限制，请参阅 [Azure 自动化服务限制](../../azure-resource-manager/management/azure-subscription-service-limits.md#update-management)。

## <a name="permissions"></a>权限

若要创建和管理更新部署，需要特定的权限。 若要了解这些权限，请参阅[基于角色的访问 - 更新管理](../automation-role-based-access-control.md#update-management-permissions)。

## <a name="update-management-components"></a>更新管理组件

更新管理使用本部分中所述的资源。 启用更新管理时，这些资源会自动添加到自动化帐户。

### <a name="hybrid-runbook-worker-groups"></a>混合 Runbook 辅助角色组

启用更新管理以后，任何直接连接到 Log Analytics 工作区的 Windows 计算机都会自动配置为系统混合 Runbook 辅助角色，为支持更新管理的 runbook 提供支持。

更新管理托管的每个 Windows 计算机都会作为自动化帐户的一个“系统混合辅助角色组”列在“混合辅助角色组”窗格中。 这些组使用 `Hostname FQDN_GUID` 命名约定。 不能在帐户中通过 Runbook 将这些组作为目标进行操作。 如果尝试，则尝试会失败。 这些组仅用于为更新管理提供支持。 若要详细了解如何查看配置为混合 Runbook 辅助角色的 Windows 计算机的列表，请参阅[查看混合 Runbook 辅助角色](../automation-hybrid-runbook-worker.md#view-system-hybrid-runbook-workers)。

如果对更新管理和混合 Runbook 辅助角色组成员身份使用同一帐户，则可以将 Windows 计算机添加到自动化帐户中的用户混合 Runbook 辅助角色组，为自动化 runbook 提供支持。 此功能是在 7.2.12024.0 版本的混合 Runbook 辅助角色中添加的。

### <a name="external-dependencies"></a>外部依赖关系

Azure 自动化更新管理依赖于以下外部依赖项来提供软件更新。

* 在基于 Windows 的计算机上，软件更新包和软件更新适用性扫描需要 Windows Server Update Services (WSUS) 或 Microsoft 更新。
* 在基于 Windows 的计算机上，需要有 Windows 更新代理 (WUA) 客户端，这样这些计算机才能连接到 WSUS 服务器或 Microsoft 更新。
* 本地或远程存储库，用于检索 OS 更新并在基于 Linux 的计算机上安装这些更新。

### <a name="management-packs"></a>管理包

以下管理包安装在由更新管理进行管理的计算机上。 如果 Operations Manager 管理组[已连接到 Log Analytics 工作区](../../azure-monitor/agents/om-agents.md)，则管理包将安装在 Operations Manager 管理组中。 你不需要对这些管理包进行配置或管理。

* Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* 更新部署 MP

> [!NOTE]
> 如果已将 Operations Manager 1807 或 2019 管理组连接到 Log Analytics 工作区并且在管理组中将代理配置为收集日志数据，则需要重写参数 `IsAutoRegistrationEnabled` 并在 Microsoft.IntelligencePacks.AzureAutomation.HybridAgent.Init 规则中将其设置为 `True`。

有关管理包更新内容的详细信息，请参阅[将 Operations Manager 连接到 Azure Monitor 日志](../../azure-monitor/agents/om-agents.md)。

> [!NOTE]
> 若要更新管理通过 Log Analytics 代理完全管理计算机，必须更新为适用于 Windows 的 Log Analytics 代理或适用于 Linux 的 Log Analytics 代理。 若要了解如何更新代理，请参阅[如何升级 Operations Manager 代理](/system-center/scom/deploy-upgrade-agents)。 在使用 Operations Manager 的环境中，必须运行 System Center Operations Manager 2012 R2 UR 14 或更高版本。

## <a name="data-collection-frequency"></a>数据收集频率

更新管理使用以下规则扫描托管计算机中的数据。 可能需要 30 分钟到 6 小时，仪表板才会显示托管计算机提供的已更新数据。

* 每个 Windows 计算机 - 更新管理每天对每个计算机扫描两次。

* 每个 Linux 计算机 - 更新管理每小时执行一次扫描。

使用更新管理的计算机的每月平均 Azure Monitor 日志数据使用情况大约为 25 MB。 此值仅为近似值，且随时可能基于环境而更改。 建议监视环境，以跟踪实际使用情况。 有关分析 Azure Monitor 日志数据使用情况的详细信息，请参阅[管理使用情况和成本](../../azure-monitor/logs/manage-cost-storage.md)。

## <a name="update-classifications"></a>更新分类

下表定义了更新管理支持的 Windows 更新分类。

|分类  |说明  |
|---------|---------|
|关键更新     | 解决关键、非安全相关错误的特定问题的更新。        |
|安全更新     | 产品特定、安全相关问题的更新。        |
|更新汇总     | 一起打包以便于部署的一组累积修补程序。        |
|功能包     | 在产品版本以外发布的新产品功能。        |
|服务包     | 应用于应用程序的一组累积修补程序。        |
|定义更新     | 对病毒或其他定义文件的更新。        |
|工具     | 可帮助完成一个或多个任务的实用工具或功能。        |
|更新     | 对当前已安装的应用程序或文件的更新。        |

下表定义了受支持的 Linux 更新分类。

|分类  |说明  |
|---------|---------|
|关键和安全更新     | 特定问题或产品特定、安全相关问题的更新。         |
|其他更新     | 本质上不是关键更新或不是安全更新的所有其他更新。        |

> [!NOTE]
> 适用于 Linux 计算机的更新分类只适合在支持的 Azure 公有云区域中使用。 在以下国家/地区云区域中使用更新管理时，没有适用于 Linux 的更新分类：
>
>* Azure 美国政府
>* 中国世纪互联
>
> 更新没有被分类，而是在“其他更新”类别下报告。
>
> 更新管理使用受支持的分发版发布的数据，尤其是其发布的 [OVAL](https://oval.mitre.org/)（开放式漏洞与评估语言）文件。 由于 Internet 访问受到这些国家/地区云的限制，更新管理无法访问以上文件。

对于 Linux，更新管理可以区分云中类别“安全性”和“其他”下的关键更新和安全更新，同时显示因云中数据扩充而产生的评估数据 。 为了进行修补，更新管理依赖于计算机上提供的分类数据。 与其他发行版不同，CentOS 在 RTM 版本中未提供此信息。 如果已将 CentOS 计算机配置为返回以下命令的安全数据，则更新管理可以基于分类进行修补。

```bash
sudo yum -q --security check-update
```

当前没有受支持的方法可用来在 CentOS 上提供原生分类数据。 目前，仅向可能已自行启用此功能的客户提供有限的支持。

若要对 Red Hat Enterprise 版本 6 上的更新进行分类，需要安装 yum 安全插件。 在 Red Hat Enterprise Linux 7 上，yum 本身已包含该插件，无需安装任何内容。 有关详细信息，请参阅以下 Red Hat [知识文章](https://access.redhat.com/solutions/10021)。

当安排要在 Linux 计算机上运行的更新时（例如，配置为仅安装与“安全性”分类匹配的更新），安装的更新可能不同于与该分类匹配的更新，或者是与该分类匹配的更新的子集。 在评估待处理的 Linux 计算机 OS 更新时，“更新管理”使用 Linux 发行版供应商提供的[开放漏洞和评估语言](https://oval.mitre.org/) (OVAL) 文件进行分类。

基于 OVAL 文件将 Linux 更新分类为“安全性”或“其他”，其中包括用于解决安全问题或漏洞的更新 。 但是，当运行更新计划时，会在 Linux 计算机上使用适当的包管理器（例如 YUM、APT 或 ZYPPER）安装更新。 Linux 发行版的包管理器可能具有不同的机制来对更新进行分类，其结果可能与更新管理从 OVAL 文件获得的结果有所不同。 若要通过包管理器手动检查计算机并了解哪些更新与安全性有关，请参阅 [Linux 更新部署故障排除](../troubleshoot/update-management.md#updates-linux-installed-different)。

>[!NOTE]
> 对于“更新管理”支持的 Linux 发行版，可能无法正常执行按更新分类来部署更新的操作。 这是由 OVAL 文件的命名架构确定的问题导致的，会阻止更新管理根据筛选规则正确匹配分类。 由于在安全更新评估中使用的逻辑不同，结果可能与部署期间应用的安全更新不同。 如果将分类设置为“严重”和“安全性”，则更新部署会按预期工作。 在评估期间，只有“更新的分类”会受影响。
>
> Windows Server 计算机的更新管理不受影响；更新分类和部署保持不变。

## <a name="integrate-update-management-with-configuration-manager"></a>将更新管理与 Configuration Manager 集成

已经投资购买了 Microsoft Endpoint Configuration Manager 来管理电脑、服务器和移动设备的客户还依赖 Configuration Manager 的优势和成熟度来帮助管理软件更新。 若要了解如何将更新管理与 Configuration Manager 集成，请参阅[将更新管理与 Windows Endpoint Configuration Manager 集成](mecmintegration.md)。

## <a name="third-party-updates-on-windows"></a>Windows 上的第三方更新

更新管理依赖于本地配置的更新存储库来更新受支持的 Windows 系统（WSUS 或 Windows 更新）。 借助 [System Center Updates Publisher](/configmgr/sum/tools/updates-publisher) 等工具，可通过 WSUS 导入和发布自定义更新。 在这种情况下，允许更新管理借助第三方软件来更新使用 Configuration Manager 作为其更新存储库的计算机。 若要了解如何配置 Updates Publisher，请参阅[安装 Updates Publisher](/configmgr/sum/tools/install-updates-publisher)。

## <a name="next-steps"></a>后续步骤

* 在启用和使用更新管理之前，请查看[计划更新管理部署](plan-deployment.md)。

* 查看 [Azure 自动化常见问题解答](../automation-faq.md)中有关更新管理的常见问题。