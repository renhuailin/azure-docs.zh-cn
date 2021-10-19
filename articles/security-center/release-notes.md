---
title: Azure 安全中心的发行说明
description: 介绍 Azure 安全中心的新增功能和已更改的功能
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: reference
ms.date: 10/06/2021
ms.author: memildin
ms.openlocfilehash: bd8ebca221041684a47bb66bb01c176fd1b65ba6
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2021
ms.locfileid: "129729513"
---
# <a name="whats-new-in-azure-security-center"></a>Azure 安全中心的新增功能

安全中心正在积极开发中，并不断得到改进。 为及时了解最新开发成果，此页提供了有关新功能、bug 修复和已弃用功能的信息。

本页面会频繁更新，请经常回来查看。 

若要了解即将在安全中心推出的计划性更改，请参阅[即将推出的对 Azure 安全中心的重要更改](upcoming-changes.md)。 

> [!TIP]
> 如果要查找 6 个月之前的项目，可查看 [Azure 安全中心的新增功能存档](release-notes-archive.md)。


## <a name="october-2021"></a>2021 年 10 月

10月更新包括：

- [添加了 Microsoft 威胁和漏洞管理作为漏洞评估解决方案（预览版）](#microsoft-threat-and-vulnerability-management-added-as-vulnerability-assessment-solution-in-preview)
- [现在可以自动启用漏洞评估解决方案（预览版）](#vulnerability-assessment-solutions-can-now-be-auto-enabled-in-preview)
- [资产清单中新增的软件清单过滤器（预览版）](#software-inventory-filters-added-to-asset-inventory-in-preview)
- [将某些警报类型的前缀从“ARM_”更改为“VM_”](#changed-prefix-of-some-alert-types-from-arm_-to-vm_)


### <a name="microsoft-threat-and-vulnerability-management-added-as-vulnerability-assessment-solution-in-preview"></a>添加了 Microsoft 威胁和漏洞管理作为漏洞评估解决方案（预览版）

我们扩展了 [Azure Defender for servers](defender-for-servers-introduction.md)和 Microsoft Defender for Endpoint 之间的集成，以支持为你的计算机提供的新漏洞评估：[Microsoft 威胁和漏洞管理](/microsoft-365/security/defender-endpoint/next-gen-threat-and-vuln-mgt)。 

在集成了 [Microsoft Defender for Endpoint](security-center-wdatp.md) 的情况下使用 **威胁和漏洞管理**，可以近乎实时地发现漏洞和错误配置，而无需额外的代理或定期扫描。 威胁和漏洞管理会根据组织中的威胁形势和检测情况对漏洞进行优先级排序。

使用安全建议“[应在虚拟机上启用漏洞评估解决方案](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/ffff0522-1e88-47fc-8382-2a80ba848f5d)”来手动发现威胁和漏洞管理针对[支持的计算机](/microsoft-365/security/defender-endpoint/tvm-supported-os?view=o365-worldwide&preserve-view=true)检测到的漏洞。 

若要在现有和新计算机上自动发现漏洞，而无需手动修正建议，请参阅[现在可以自动启用漏洞评估解决方案（预览版）](#vulnerability-assessment-solutions-can-now-be-auto-enabled-in-preview)。

若要了解详细信息，请参阅[通过 Microsoft Defender for Endpoint 威胁和漏洞管理调查弱点](deploy-vulnerability-assessment-tvm.md)。

### <a name="vulnerability-assessment-solutions-can-now-be-auto-enabled-in-preview"></a>现在可以自动启用漏洞评估解决方案（预览版）

安全中心的自动预配页面现在包括一个选项，可在受 [Azure Defender for Server](defender-for-servers-introduction.md) 保护的订阅上自动为 Azure 虚拟机和 Azure Arc 计算机启用漏洞评估解决方案。

此外，如果启用了[与 Microsoft Defender for Endpoint 的集成](security-center-wdatp.md)，则将可以选择如下漏洞评估解决方案：

- （新）Microsoft Defender for Endpoint 的 Microsoft 威胁和漏洞管理模块（请参阅[发行说明](#microsoft-threat-and-vulnerability-management-added-as-vulnerability-assessment-solution-in-preview)）
- 集成 Qualys 代理

:::image type="content" source="media/deploy-vulnerability-assessment-tvm/auto-provision-vulnerability-assessment-agent.png" alt-text="通过 Azure 安全中心配置 Microsoft 的威胁和漏洞管理的自动预配。":::

所选解决方案将在受支持的计算机上自动启用。

请参阅[为计算机自动配置漏洞评估](auto-deploy-vulnerability-assessment.md)以了解详细信息。

### <a name="software-inventory-filters-added-to-asset-inventory-in-preview"></a>资产清单中新增的软件清单过滤器（预览版）

[资产清单](asset-inventory.md)页面现在包含一个过滤器，用于选择运行特定软件的计算机 - 甚至指定感兴趣的版本。 

此外，还可以在 **Azure Resource Graph Explorer** 中查询软件清单数据。

若要使用这些新功能，需要启用[与 Microsoft Defender for Endpoint 的集成](security-center-wdatp.md)。 

有关完整的详细信息，包括 Azure Resource Graph 的示例 Kusto 查询，请参阅[访问软件清单](asset-inventory.md#access-a-software-inventory)。

:::image type="content" source="media/deploy-vulnerability-assessment-tvm/software-inventory.png" alt-text="如果已启用威胁和漏洞解决方案，安全中心的资产清单会提供一个筛选器，用于按已安装的软件选择资源。":::

### <a name="changed-prefix-of-some-alert-types-from-arm_-to-vm_"></a>将某些警报类型的前缀从“ARM_”更改为“VM_” 

2021 年 7 月，我们发布了[适用于资源管理器的 Azure Defender 的警报的逻辑重组](release-notes.md#logical-reorganization-of-azure-defender-for-resource-manager-alerts) 

在对某些 Azure Defender 计划进行逻辑重组的过程中，我们已将 21 条警报从[适用于资源管理器的 Azure Defender](defender-for-resource-manager-introduction.md) 移至[适用于服务器的 Azure Defender](defender-for-servers-introduction.md)。

通过此更新，我们更改了这些警报的前缀以匹配此重新分配，将“ARM_”替换为“VM_”，如下表所示：

| 原始名称                                  | 从此更改中                              |
|------------------------------------------------|-----------------------------------------------|
| ARM_AmBroadFilesExclusion                      | VM_AmBroadFilesExclusion                      |
| ARM_AmDisablementAndCodeExecution              | VM_AmDisablementAndCodeExecution              |
| ARM_AmDisablement                              | VM_AmDisablement                              |
| ARM_AmFileExclusionAndCodeExecution            | VM_AmFileExclusionAndCodeExecution            |
| ARM_AmTempFileExclusionAndCodeExecution        | VM_AmTempFileExclusionAndCodeExecution        |
| ARM_AmTempFileExclusion                        | VM_AmTempFileExclusion                        |
| ARM_AmRealtimeProtectionDisabled               | VM_AmRealtimeProtectionDisabled               |
| ARM_AmTempRealtimeProtectionDisablement        | VM_AmTempRealtimeProtectionDisablement        |
| ARM_AmRealtimeProtectionDisablementAndCodeExec | VM_AmRealtimeProtectionDisablementAndCodeExec |
| ARM_AmMalwareCampaignRelatedExclusion          | VM_AmMalwareCampaignRelatedExclusion          |
| ARM_AmTemporarilyDisablement                   | VM_AmTemporarilyDisablement                   |
| ARM_UnusualAmFileExclusion                     | VM_UnusualAmFileExclusion                     |
| ARM_CustomScriptExtensionSuspiciousCmd         | VM_CustomScriptExtensionSuspiciousCmd         |
| ARM_CustomScriptExtensionSuspiciousEntryPoint  | VM_CustomScriptExtensionSuspiciousEntryPoint  |
| ARM_CustomScriptExtensionSuspiciousPayload     | VM_CustomScriptExtensionSuspiciousPayload     |
| ARM_CustomScriptExtensionSuspiciousFailure     | VM_CustomScriptExtensionSuspiciousFailure     |
| ARM_CustomScriptExtensionUnusualDeletion       | VM_CustomScriptExtensionUnusualDeletion       |
| ARM_CustomScriptExtensionUnusualExecution      | VM_CustomScriptExtensionUnusualExecution      |
| ARM_VMAccessUnusualConfigReset                 | VM_VMAccessUnusualConfigReset                 |
| ARM_VMAccessUnusualPasswordReset               | VM_VMAccessUnusualPasswordReset               |
| ARM_VMAccessUnusualSSHReset                    | VM_VMAccessUnusualSSHReset                    |
|||

详细了解[适用于资源管理器的 Azure Defender](defender-for-resource-manager-introduction.md) 和[适用于服务器的 Azure Defender](defender-for-servers-introduction.md) 计划。

## <a name="september-2021"></a>2021 年 9 月

9 月发布了以下更新：

### <a name="two-new-recommendations-to-audit-os-configurations-for-azure-security-baseline-compliance-in-preview"></a>用于审核 Azure 安全基线符合性的 OS 配置的两个新建议（预览版）

发布了以下两个建议来评估计算机是否符合 [Windows 安全基线](../governance/policy/samples/guest-configuration-baseline-windows.md)和 [Linux 安全基线](../governance/policy/samples/guest-configuration-baseline-linux.md)：

- 对于 Windows 计算机，[应修正 Windows 计算机上的安全配置漏洞（由来宾配置提供支持）](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/1f655fb7-63ca-4980-91a3-56dbc2b715c6)
- 对于 Linux 计算机，[应修正 Linux 计算机上的安全配置漏洞（由来宾配置提供支持）](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/8c3d9ad0-3639-4686-9cd2-2b2ab2609bda)

这些建议使用 Azure Policy 的来宾配置功能，将计算机的 OS 配置与 [Azure 安全基准](/security/benchmark/azure/overview)中定义的基线进行比较。

若要详细了解如何使用这些建议，请参阅[使用来宾配置强化计算机的 OS 配置](apply-security-baseline.md)。

## <a name="august-2021"></a>2021 年 8 月

8 月的更新包括：

- [适用于 Linux 的 Microsoft Defender for Endpoint 现在由适用于服务器的 Azure Defender 提供支持（预览版）](#microsoft-defender-for-endpoint-for-linux-now-supported-by-azure-defender-for-servers-in-preview)
- [关于管理 Endpoint Protection 解决方案的两项新建议（预览版）](#two-new-recommendations-for-managing-endpoint-protection-solutions-in-preview)
- [用于解决常见问题的内置故障排除和指南](#built-in-troubleshooting-and-guidance-for-solving-common-issues)
- [法规合规性仪表板的 Azure 审核报告正式发布 (GA)](#regulatory-compliance-dashboards-azure-audit-reports-released-for-general-availability-ga)
- [已弃用建议“应在计算机上解决 Log Analytics 代理运行状况问题”](#deprecated-recommendation-log-analytics-agent-health-issues-should-be-resolved-on-your-machines)
- [适用于容器注册表的 Azure Defender 现在扫描受 Azure 专用链接保护的注册表中的漏洞](#azure-defender-for-container-registries-now-scans-for-vulnerabilities-in-registries-protected-with-azure-private-link)
- [安全中心现在可以自动预配 Azure Policy 的来宾配置扩展（预览版）](#security-center-can-now-auto-provision-the-azure-policys-guest-configuration-extension-in-preview)
- [关于启用 Azure Defender 计划的建议现在支持“强制执行”](#recommendations-to-enable-azure-defender-plans-now-support-enforce)
- [建议数据的 CSV 导出现在限制为 20 MB](#csv-exports-of-recommendation-data-now-limited-to-20-mb)
- [建议页面现在包含多个视图](#recommendations-page-now-includes-multiple-views)

### <a name="microsoft-defender-for-endpoint-for-linux-now-supported-by-azure-defender-for-servers-in-preview"></a>适用于 Linux 的 Microsoft Defender for Endpoint 现在由适用于服务器的 Azure Defender 提供支持（预览版）

[适用于服务器的 Azure Defender](defender-for-servers-introduction.md) 包含 [Microsoft Defender for Endpoint](https://www.microsoft.com/microsoft-365/security/endpoint-defender) 的集成许可证。 两者共同提供全面的终结点检测和响应 (EDR) 功能。

用于终结点的 Defender 在检测到威胁时会触发警报。 警报在安全中心显示。 在安全中心，还可以透视用于终结点的 Defender 控制台，并执行详细调查来发现攻击范围。

在预览期间，你将通过以下两种方式之一将 [适用于 Linux 的 Defender for Endpoint](/microsoft-365/security/defender-endpoint/microsoft-defender-endpoint-linux) 传感器部署到受支持的 Linux 计算机上，具体取决于你是否已将其部署到 Windows 计算机上：

- [Azure Defender 和适用于 Windows 的 Microsoft Defender for Endpoint 的现有用户](security-center-wdatp.md?tabs=linux#existing-users-of-azure-defender-and-microsoft-defender-for-endpoint-for-windows)
- [从未与适用于 Windows 的 Microsoft Defender for Endpoint 集成的新用户](security-center-wdatp.md?tabs=linux#new-users-whove-never-enabled-the-integration-with-microsoft-defender-for-endpoint-for-windows)

若要了解详细信息，请参阅[通过安全中心的集成式 EDR 解决方案保护终结点：Microsoft Defender for Endpoint](security-center-wdatp.md)。

### <a name="two-new-recommendations-for-managing-endpoint-protection-solutions-in-preview"></a>关于管理 Endpoint Protection 解决方案的两项新建议（预览版）

我们就如何在计算机上部署和维护 Endpoint Protection 解决方案，增加了两项预览建议。 这两项建议均包括对 Azure 虚拟机和连接到已启用 Azure Arc 的服务器的计算机的支持。

|建议 |说明 |严重性 |
|---|---|---|
|[应在计算机上安装 Endpoint Protection](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/4fb67663-9ab9-475d-b026-8c544cced439) |若要保护计算机免受威胁和漏洞的侵害，请安装受支持的 Endpoint Protection 解决方案。  <br> <a href="/azure/security-center/security-center-endpoint-protection">详细了解如何评估计算机的 Endpoint Protection。</a><br />（相关策略：[监视 Azure 安全中心 Endpoint Protection 的缺失情况](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2faf6cd1bd-1635-48cb-bde7-5b15693900b9)） |高 |
|应在计算机上解决 Endpoint Protection 运行状况问题 |解决虚拟机上的 Endpoint Protection 运行状况问题，以保护其免受最新威胁和漏洞的侵害。 [此处](./security-center-services.md?tabs=features-windows)介绍了 Azure 安全中心支持的 Endpoint Protection 解决方案。 <a href='/azure/security-center/security-center-endpoint-protection'>此处</a>介绍了 Endpoint Protection 评估。<br />（相关策略：[监视 Azure 安全中心 Endpoint Protection 的缺失情况](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2faf6cd1bd-1635-48cb-bde7-5b15693900b9)） |中型 |
|||

> [!NOTE]
> 建议显示其刷新间隔为 8 小时，但在某些情况下，这可能需要更长的时间。 例如，当本地计算机被删除时，安全中心需要 24 小时来识别删除。 之后，评估最多需要 8 小时才能返回信息。 因此，在这种特定情况下，可能需要 32 小时才能将计算机从受影响资源列表中删除。
>
> :::image type="content" source="media/release-notes/freshness-interval.png" alt-text="这两项新安全中心建议的刷新间隔指示器":::

### <a name="built-in-troubleshooting-and-guidance-for-solving-common-issues"></a>用于解决常见问题的内置故障排除和指南

Azure 门户中安全中心页面的一个新专用区域提供了一套经过整理的、不断扩充的自助材料，用于解决安全中心和 Azure Defender 面临的常见挑战。

当你遇到问题，或向我们的支持团队寻求建议时，“诊断并解决问题”是另一种帮助你找到解决方案的工具：

:::image type="content" source="media/release-notes/solve-problems.png" alt-text="安全中心的“诊断并解决问题”页面":::
 

### <a name="regulatory-compliance-dashboards-azure-audit-reports-released-for-general-availability-ga"></a>法规合规性仪表板的 Azure 审核报告正式发布 (GA)

法规合规性仪表板的工具栏提供适用于订阅的标准的 Azure 和 Dynamics 认证报告。 

:::image type="content" source="media/release-notes/audit-reports-regulatory-compliance-dashboard.png" alt-text="法规合规性仪表板的工具栏，显示用于生成审核报告的按钮。":::

可以选择用于相关报告类型（PCI、SOC 和 ISO 等）的选项卡，然后使用筛选器来查找所需的特定报告。

有关详细信息，请参阅[生成合规性状态报告和证书](security-center-compliance-dashboard.md#generate-compliance-status-reports-and-certificates)。

:::image type="content" source="media/release-notes/audit-reports-list-regulatory-compliance-dashboard-ga.png" alt-text="可用 Azure 审核报告的选项卡式列表。显示了 ISO 报告、SOC 报告、PCI 等选项卡。":::

### <a name="deprecated-recommendation-log-analytics-agent-health-issues-should-be-resolved-on-your-machines"></a>已弃用建议“应在计算机上解决 Log Analytics 代理运行状况问题”

我们发现，建议“应在计算机上解决 Log Analytics 代理运行状况问题”会因与安全中心的云安全态势管理 (CSPM) 不一致而影响安全分数。 通常，CSPM 与识别安全错误配置有关。 代理运行状况问题不适用于此类问题。

此外，和与安全中心相关的其他代理相比时，该建议也是异常的：这是唯一一个具有与运行状况问题相关的建议的代理。

已应用该建议。

由于此弃用，我们还对安装 Log Analytics 代理的建议进行了少许更改（Log Analytics 代理应安装在...）。

此更改可能会影响安全分数。 对于大多数订阅，此更改应会导致分数增加，但在某些情况下，对安装建议的更新可能会导致分数降低。

> [!TIP]
> [资产清单](asset-inventory.md)页也受到此更改的影响，因为该页面显示计算机的受监视状态（被监视、未被监视或被部分监视 - 指存在运行状况问题的代理的状态）。


### <a name="azure-defender-for-container-registries-now-scans-for-vulnerabilities-in-registries-protected-with-azure-private-link"></a>适用于容器注册表的 Azure Defender 现在扫描受 Azure 专用链接保护的注册表中的漏洞
适用于容器注册表的 Azure Defender 包含漏洞扫描程序，用于扫描 Azure 容器注册表中的映像。 若要了解如何扫描注册表并修复发现的漏洞，请参阅[使用适用于容器注册表的 Azure Defender 来扫描映像是否存在漏洞](defender-for-container-registries-usage.md)。

若要限制对 Azure 容器注册表中托管的注册表的访问，请将虚拟网络专用 IP 地址分配给注册表终结点并使用 Azure 专用链接，如[使用 Azure 专用链接以私密方式连接到 Azure 容器注册表](../container-registry/container-registry-private-link.md)中所述。

在我们为更多环境和用例提供支持的不懈努力下，Azure Defender 现在还扫描受 [Azure 专用链接](../private-link/private-link-overview.md)保护的容器注册表。


### <a name="security-center-can-now-auto-provision-the-azure-policys-guest-configuration-extension-in-preview"></a>安全中心现在可以自动预配 Azure Policy 的来宾配置扩展（预览版）
Azure Policy 可以审核计算机内部的设置，包括在 Azure 中运行的计算机和 Arc 连接的计算机。 验证由来宾配置扩展和客户端执行。 详细[了解 Azure Policy 的来宾配置](../governance/policy/concepts/guest-configuration.md)。

通过此更新，你现在可以将安全中心设置为自动将此扩展预配到所有受支持的计算机。 

:::image type="content" source="media/release-notes/auto-provisioning-guest-configuration.png" alt-text="启用来宾配置扩展的自动部署。":::

若要详细了解自动预配的工作原理，请参阅[为代理和扩展配置自动预配](security-center-enable-data-collection.md)。

### <a name="recommendations-to-enable-azure-defender-plans-now-support-enforce"></a>关于启用 Azure Defender 计划的建议现在支持“强制执行”
安全中心包括以下两项功能，可帮助确保以安全的方式预配新创建的资源：强制执行和拒绝。 当某项建议提供这些选项时，你可以确保每当有人试图创建资源时，你的安全要求都能得到满足：

- **拒绝**：停止创建运行不正常的资源
- **强制执行**：在创建不合规的资源时自动修正它们

通过此更新，关于启用 Azure Defender 计划的建议中现在提供强制执行选项（例如“应启用适用于应用服务的 Azure Defender”、“应启用适用于 Key Vault 的 Azure Defender”、“应启用适用于存储的 Azure Defender”）。

若要详细了解这些选项，请参阅[使用“强制执行/拒绝”建议防止错误配置](prevent-misconfigurations.md)。

### <a name="csv-exports-of-recommendation-data-now-limited-to-20-mb"></a>建议数据的 CSV 导出现在限制为 20 MB

导出安全中心建议数据时，我们规定了 20 MB 的限制。

:::image type="content" source="media/upcoming-changes/download-csv-report.png" alt-text="安全中心的“下载 CSV 报表”按钮，用于导出建议数据。":::

如果需要导出大量数据，请在选择数据前使用可用的筛选器，或选择订阅的子集并批量下载数据。

:::image type="content" source="media/upcoming-changes/filter-subscriptions.png" alt-text="在 Azure 门户中筛选“订阅”。":::

详细了解如何[执行安全建议的 CSV 导出](continuous-export.md#manual-one-time-export-of-alerts-and-recommendations)。



### <a name="recommendations-page-now-includes-multiple-views"></a>建议页面现在包含多个视图

建议页面现在包含两个选项卡，可提供替代方法来查看与资源相关的建议：

- **安全功能分数建议** - 使用此选项卡查看按安全控制分组的建议列表。 若要详细了解这些控制，请参阅[安全控制及其建议](secure-score-security-controls.md#security-controls-and-their-recommendations)。
- **所有建议** - 使用此选项卡以简单列表的形式查看建议列表。 此选项卡还有助于了解是哪个计划（包括法规合规性标准）生成了建议。 若要详细了解计划及其与建议的关系，请参阅[什么是安全策略、计划和建议？](security-policy-concept.md)

:::image type="content" source="media/release-notes/recommendations-tabs.png" alt-text="用于更改 Azure 安全中心的建议列表视图的选项卡。":::

## <a name="july-2021"></a>2021 年 7 月

7 月的更新包括：

- [Azure Sentinel 连接器现在包含可选的双向警报同步（预览版）](#azure-sentinel-connector-now-includes-optional-bi-directional-alert-synchronization-in-preview)
- [资源管理器警报的 Azure Defender 的逻辑重组](#logical-reorganization-of-azure-defender-for-resource-manager-alerts)                                           
- [增强了关于启用 Azure 磁盘加密 (ADE) 的建议](#enhancements-to-recommendation-to-enable-azure-disk-encryption-ade)                                     
- [安全功能分数和法规合规性数据的连续导出功能正式发布 (GA)](#continuous-export-of-secure-score-and-regulatory-compliance-data-released-for-general-availability-ga)
- [对法规合规性评估的更改可以触发工作流自动化 (GA)](#workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-ga)
- [评估 API 字段“FirstEvaluationDate”和“StatusChangeDate”现在可用于工作区架构和逻辑应用](#assessments-api-field-firstevaluationdate-and-statuschangedate-now-available-in-workspace-schemas-and-logic-apps)
- [向 Azure Monitor 工作簿库添加了“一段时间内的合规性”工作簿模板](#compliance-over-time-workbook-template-added-to-azure-monitor-workbooks-gallery)

### <a name="azure-sentinel-connector-now-includes-optional-bi-directional-alert-synchronization-in-preview"></a>Azure Sentinel 连接器现在包含可选的双向警报同步（预览版）

安全中心以原生方式与 [Azure Sentinel](../sentinel/index.yml)（Azure 的云原生 SIEM 和 SOAR 解决方案）集成。 

Azure Sentinel 包括 Azure 安全中心在订阅和租户级别的内置连接器。 若要了解详细信息，请参阅[将警报流式传输到 Azure Sentinel](export-to-siem.md#stream-alerts-to-azure-sentinel)。

将 Azure Defender 连接到 Azure Sentinel 时，会在这两项服务之间同步已引入到 Azure Sentinel 中的 Azure Defender 警报的状态。 例如，当某个警报在 Azure Defender 中处于已关闭状态时，该警报在 Azure Sentinel 中也会显示为已关闭。 在 Azure Defender 中更改警报的状态不会影响包含同步 Azure Sentinel 警报的任何 Azure Sentinel 事件的状态，只会影响同步警报自身的状态。

启用此预览功能（即“双向警报同步”）后，系统会自动将原始 Azure Defender 警报的状态与包含这些 Azure Defender 警报副本的 Azure Sentinel 事件的状态同步。 例如，当包含 Azure Defender 警报的 Azure Sentinel 事件被关闭时，Azure Defender 将自动关闭相应的原始警报。

若要了解详细信息，请参阅[从 Azure 安全中心连接 Azure Defender 警报](../sentinel/connect-azure-security-center.md)。

### <a name="logical-reorganization-of-azure-defender-for-resource-manager-alerts"></a>资源管理器警报的 Azure Defender 的逻辑重组

[适用于资源管理器的 Azure Defender](defender-for-resource-manager-introduction.md) 计划中提供了下列警报。

在对某些 Azure Defender 计划进行逻辑重组的过程中，我们已将一些警报从 **适用于资源管理器的 Azure Defender** 移至 **适用于服务器的 Azure Defender**。

警报根据如下两个主要原则进行组织：

- 跨许多 Azure 资源类型提供控制平面保护的警报属于适用于资源管理器的 Azure Defender
- 保护特定工作负载的警报位于与相应工作负载相关的 Azure Defender 计划中

以下警报以前属于适用于资源管理器的 Azure Defender，经过本次更改后，现在属于适用于服务器的 Azure Defender：

- ARM_AmBroadFilesExclusion
- ARM_AmDisablementAndCodeExecution
- ARM_AmDisablement
- ARM_AmFileExclusionAndCodeExecution
- ARM_AmTempFileExclusionAndCodeExecution
- ARM_AmTempFileExclusion
- ARM_AmRealtimeProtectionDisabled
- ARM_AmTempRealtimeProtectionDisablement
- ARM_AmRealtimeProtectionDisablementAndCodeExec
- ARM_AmMalwareCampaignRelatedExclusion
- ARM_AmTemporarilyDisablement
- ARM_UnusualAmFileExclusion
- ARM_CustomScriptExtensionSuspiciousCmd
- ARM_CustomScriptExtensionSuspiciousEntryPoint
- ARM_CustomScriptExtensionSuspiciousPayload
- ARM_CustomScriptExtensionSuspiciousFailure
- ARM_CustomScriptExtensionUnusualDeletion
- ARM_CustomScriptExtensionUnusualExecution
- ARM_VMAccessUnusualConfigReset
- ARM_VMAccessUnusualPasswordReset
- ARM_VMAccessUnusualSSHReset

详细了解[适用于资源管理器的 Azure Defender](defender-for-resource-manager-introduction.md) 和[适用于服务器的 Azure Defender](defender-for-servers-introduction.md) 计划。


### <a name="enhancements-to-recommendation-to-enable-azure-disk-encryption-ade"></a>增强了关于启用 Azure 磁盘加密 (ADE) 的建议

根据用户反馈，我们已重命名建议“应对虚拟机应用磁盘加密”。

新建议使用相同的评估 ID，名为“虚拟机应该加密计算和存储资源之间的临时磁盘、缓存和数据流”。

说明也已更新，以更好地解释此强化建议的目的：

| 建议                                                                                               | 说明                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             | 严重性 |
|--------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:--------:|
| **虚拟机应加密临时磁盘、缓存以及计算资源和存储资源之间的数据流** | 默认情况下，虚拟机的 OS 和数据磁盘使用平台管理的密钥进行静态加密；临时磁盘和数据缓存不加密，数据在计算和存储资源之间流动时也不加密。 有关 Azure 中不同磁盘加密技术的比较，请参阅 https://aka.ms/diskencryptioncomparison 。<br>使用 Azure 磁盘加密来加密所有这些数据。 如果出现以下情况，请忽略此建议：(1) 你正在使用主机加密功能，或者 (2) 托管磁盘上的服务器端加密满足你的安全要求。 若要了解详细信息，请参阅“Azure 磁盘存储的服务器端加密”。 | 高     |
|                                                                                                              |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |          |


### <a name="continuous-export-of-secure-score-and-regulatory-compliance-data-released-for-general-availability-ga"></a>安全功能分数和法规合规性数据的连续导出功能正式发布 (GA)

[连续导出](continuous-export.md)提供了导出安全警报和建议的机制，以便使用环境中的其他监视工具进行跟踪。

设置连续导出时，可以配置导出的内容以及导出的位置。 若要了解详细信息，请参阅[连续导出概述](continuous-export.md)。

随着时间的推移，我们对此功能进行了增强和扩展：

- 2020 年 11 月，我们添加了一个预览选项，用于流式传输对安全功能分数的更改。<br/>有关完整的详细信息，请参阅[现可在连续导出中使用安全功能分数（预览版）](release-notes-archive.md#secure-score-is-now-available-in-continuous-export-preview)。

- 2020 年 12 月，我们添加了一个预览选项，用于流式传输对法规合规性评估数据的更改。<br/>有关完整的详细信息，请参阅[连续导出获取新数据类型（预览版）](release-notes-archive.md#continuous-export-gets-new-data-types-and-improved-deployifnotexist-policies)。

在此更新中，这两个选项正式发布 (GA)。 


### <a name="workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-ga"></a>对法规合规性评估的更改可以触发工作流自动化 (GA)

2021 年 2 月，我们向工作流自动化的触发器选项添加了第三种预览数据类型：对法规合规性评估的更改。 若要了解详细信息，请参阅[对法规合规性评估的更改可以触发工作流自动化](release-notes-archive.md#workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-in-preview)。

在此更新中，此触发器选项正式发布 (GA)。

了解如何使用[自动响应安全中心触发器](workflow-automation.md)中的工作流自动化工具。

:::image type="content" source="media/release-notes/regulatory-compliance-triggers-workflow-automation.png" alt-text="使用对监管合规性评估的更改来触发工作流自动化。" lightbox="media/release-notes/regulatory-compliance-triggers-workflow-automation.png":::

### <a name="assessments-api-field-firstevaluationdate-and-statuschangedate-now-available-in-workspace-schemas-and-logic-apps"></a>评估 API 字段“FirstEvaluationDate”和“StatusChangeDate”现在可用于工作区架构和逻辑应用

2021 年 5 月，我们使用两个新字段更新了评估 API，即“FirstEvaluationDate”和“StatusChangeDate”。 有关完整的详细信息，请参阅[评估 API 扩充了两个新字段](#assessments-api-expanded-with-two-new-fields)。

这些字段可通过 REST API、Azure Resource Graph、连续导出和 CSV 导出访问。

通过此次更改，我们现在在 Log Analytics 工作区架构和逻辑应用中提供这些信息。


### <a name="compliance-over-time-workbook-template-added-to-azure-monitor-workbooks-gallery"></a>向 Azure Monitor 工作簿库添加了“一段时间内的合规性”工作簿模板

3 月，我们在安全中心发布了集成式 Azure Monitor 工作簿体验（请参阅[集成到安全中心的 Azure Monitor 工作簿以及提供的三个模板](release-notes-archive.md#azure-monitor-workbooks-integrated-into-security-center-and-three-templates-provided)）。

初始版本包括三个模板，用于生成有关组织安全状况的动态和视觉报告。

我们现在添加了一个工作簿，专门用于跟踪订阅是否符合适用的法规或行业标准。 

如需了解如何使用这些报告或生成自己的报告，请参阅[创建丰富的交互式安全中心数据报表](custom-dashboards-azure-workbooks.md)。

:::image type="content" source="media/custom-dashboards-azure-workbooks/compliance-over-time-details.png" alt-text="Azure 安全中心的“一段时间内的合规性”工作簿":::


## <a name="june-2021"></a>2021 年 6 月

6 月的更新包括以下内容：

- [适用于 Key Vault 的 Azure Defender 的新警报](#new-alert-for-azure-defender-for-key-vault)
- [默认禁用使用客户管理的密钥 (CMK) 进行加密的建议](#recommendations-to-encrypt-with-customer-managed-keys-cmks-disabled-by-default)
- [Kubernetes 警报的前缀已从“AKS_”更改为“K8S_”](#prefix-for-kubernetes-alerts-changed-from-aks_-to-k8s_)
- [弃用了“应用系统更新”安全控制中的两个建议](#deprecated-two-recommendations-from-apply-system-updates-security-control)


### <a name="new-alert-for-azure-defender-for-key-vault"></a>适用于 Key Vault 的 Azure Defender 的新警报

为了扩展 Azure Defender 为 Key Vault 提供的威胁保护，我们添加了以下警报：

| 警报（警报类型）                                                                 | 说明                                                                                                                                                                                                                                                                                                                                                      | MITRE 技巧 | 严重性 |
|------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------:|----------|
| 可疑 IP 地址对 Key Vault 的访问<br>(KV_SuspiciousIPAccess)  | 被 Microsoft 威胁情报标识为可疑 IP 地址的 IP 已经成功访问了 key vault。 这可能表示基础结构已遭到入侵。 建议进一步调查。 详细了解 [Microsoft 威胁智能功能](https://go.microsoft.com/fwlink/?linkid=2128684)。 | 凭据访问                            | 中型   |
|||

有关详细信息，请参阅：
- [用于 Key Vault 的 Azure Defender 简介](defender-for-resource-manager-introduction.md)
- [响应适用于 Key Vault 的 Azure Defender 警报](defender-for-key-vault-usage.md)
- [适用于 Key Vault 的 Azure Defender 提供的警报列表](alerts-reference.md#alerts-azurekv)


### <a name="recommendations-to-encrypt-with-customer-managed-keys-cmks-disabled-by-default"></a>默认禁用使用客户管理的密钥 (CMK) 进行加密的建议

安全中心包含多个建议，它们建议使用客户管理的密钥对静态数据进行加密，例如：

- 容器注册表应使用客户管理的密钥 (CMK) 进行加密
- Azure Cosmos DB 帐户应使用客户管理的密钥来加密静态数据
- Azure 机器学习工作区应使用客户管理的密钥 (CMK) 进行加密

使用平台管理的密钥自动对 Azure 中的数据进行加密，因此，只有在要求符合组织选择执行的特定政策时，才使用客户管理的密钥。

如此更改后，现已 **默认禁用** 使用 CMK 的建议。 当与你的组织相关时，可以通过将相应安全策略的 *效果* 参数更改为 **AuditIfNotExists** 或 **Enforce** 来启用它们。 有关详细信息，请参阅[启用安全策略](tutorial-security-policy.md#enable-a-security-policy)。

此更改将反映在包含新前缀的建议名称中， **[Enable if required]** ，如以下示例所示：

- [Enable if required] 存储帐户应使用客户管理的密钥来加密静态数据
- [Enable if required] 应使用客户管理的密钥 (CMK) 来加密容器寄存器
- [Enable if required] Azure Cosmos DB 帐户应使用客户管理的密钥来加密静态数据

:::image type="content" source="media/upcoming-changes/customer-managed-keys-disabled.png" alt-text="默认禁用安全中心的 CMK 建议。" lightbox="media/upcoming-changes/customer-managed-keys-disabled.png":::


### <a name="prefix-for-kubernetes-alerts-changed-from-aks_-to-k8s_"></a>Kubernetes 警报的前缀已从“AKS_”更改为“K8S_”

Azure Defender for Kubernetes 最近已扩展，可保护托管在本地和多云环境中的 Kubernetes 群集。 参阅[使用 Azure Defender for Kubernetes 保护混合和多云 Kubernetes 部署（预览版）](release-notes-archive.md#use-azure-defender-for-kubernetes-to-protect-hybrid-and-multi-cloud-kubernetes-deployments-in-preview)，了解更多信息。

为了反映 Azure Defender for Kubernetes 提供的安全警报不再限于 Azure Kubernetes 服务上的群集，我们已将警报类型的前缀从 “AKS_”更改为“K8S_”。 必要时，还会更新名称和说明。 例如，以下警报：

|警报（警报类型）|说明|
|----|----|
|检测到 Kubernetes 渗透测试工具<br>(**AKS** _PenTestToolsKubeHunter)|Kubernetes 审核日志分析检测到在 **AKS** 群集中使用了 Kubernetes 渗透测试工具。 此行为可能是合法的，不过，攻击者可能会出于恶意使用此类公用工具。
|||

已更改为：

|警报（警报类型）|说明|
|----|----|
|检测到 Kubernetes 渗透测试工具<br>(**K8S** _PenTestToolsKubeHunter)|Kubernetes 审核日志分析检测到在 **Kubernetes** 群集中使用了 Kubernetes 渗透测试工具。 此行为可能是合法的，不过，攻击者可能会出于恶意使用此类公用工具。|
|||

将自动转换提到以“AKS_”开始的警报的所有抑制规则。 如果已设置 SIEM 导出或按警报类型引用 Kubernetes 警报的自定义自动化脚本，则需要使用新的警报类型对其进行更新。

有关 Kubernetes 警报的完整列表，请参阅[适用于 Kubernetes 群集的警报](alerts-reference.md#alerts-k8scluster)。

### <a name="deprecated-two-recommendations-from-apply-system-updates-security-control"></a>弃用了“应用系统更新”安全控制中的两个建议

以下两项建议已被弃用：

- **应为你的云服务角色更新 OS 版本** - 默认情况下，Azure 会定期将来宾 OS 更新为 OS 系列（在服务配置 (.cscfg) 中指定）中支持的最新映像，例如 Windows Server 2016。
- **Kubernetes Services 应升级到不容易受到攻击的 Kubernetes 版本** - 这项建议的评估覆盖面并不像我们所希望的那样广泛。 我们计划使用增强版本替换建议，以更好地满足你的安全需求。


## <a name="may-2021"></a>2021 年 5 月

5 月的更新包括以下内容：

- [适用于 DNS 的 Azure Defender 和适用于资源管理器的 Azure Defender 正式发布 (GA)](#azure-defender-for-dns-and-azure-defender-for-resource-manager-released-for-general-availability-ga)
- [适用于开放源代码关系数据库的 Azure Defender 正式发布 (GA)](#azure-defender-for-open-source-relational-databases-released-for-general-availability-ga)
- [适用于资源管理器的 Azure Defender 的新警报](#new-alerts-for-azure-defender-for-resource-manager)
- [通过 GitHub 工作流和 Azure Defender (预览版）扫描容器映像的 CI/CD 漏洞](#cicd-vulnerability-scanning-of-container-images-with-github-workflows-and-azure-defender-preview)
- [可用于某些建议的更多 Resource Graph 查询](#more-resource-graph-queries-available-for-some-recommendations)
- [更改了 SQL 数据分类建议严重性](#sql-data-classification-recommendation-severity-changed)
- [用于启用受信任启动功能（预览版）的新建议](#new-recommendations-to-enable-trusted-launch-capabilities-in-preview)
- [用于强化 Kubernetes 群集（预览版）的新建议](#new-recommendations-for-hardening-kubernetes-clusters-in-preview)
- [评估 API 扩充了两个新字段](#assessments-api-expanded-with-two-new-fields)
- [资产盘存增加了云环境筛选器](#asset-inventory-gets-a-cloud-environment-filter)


### <a name="azure-defender-for-dns-and-azure-defender-for-resource-manager-released-for-general-availability-ga"></a>适用于 DNS 的 Azure Defender 和适用于资源管理器的 Azure Defender 正式发布 (GA)

这两个云原生广度的威胁保护计划现已正式发布。

这些新的防护极大增强了你在遭到威胁行为体攻击后的复原能力，还大大增加了受 Azure Defender 保护的 Azure 资源数量。

- **适用于资源管理器的 Azure Defender** - 自动监视在你的组织中执行的所有资源管理操作。 有关详细信息，请参阅：
    - [适用于资源管理器的 Azure Defender 简介](defender-for-resource-manager-introduction.md)
    - [响应适用于资源管理器的 Azure Defender 警报](defender-for-resource-manager-usage.md)
    - [适用于资源管理器的 Azure Defender 提供的警报列表](alerts-reference.md#alerts-resourcemanager)

- **适用于 DNS 的 Azure Defender** - 持续监视来自你的 Azure 资源的所有 DNS 查询。 有关详细信息，请参阅：
    - [适用于 DNS 的 Azure Defender 简介](defender-for-dns-introduction.md)
    - [响应适用于 DNS 的 Azure Defender 警报](defender-for-dns-usage.md)
    - [适用于 DNS 的 Azure Defender 提供的警报列表](alerts-reference.md#alerts-dns)

若要简化启用这些计划的过程，请使用建议：

- **应启用 Azure Defender for Resource Manager**
- **应启用 Azure Defender for DNS**

> [!NOTE]
> 启用 Azure Defender 计划会产生费用。 若要了解各区域的定价详细信息，请访问安全中心的定价页： https://aka.ms/pricing-security-center 。


### <a name="azure-defender-for-open-source-relational-databases-released-for-general-availability-ga"></a>适用于开放源代码关系数据库的 Azure Defender 正式发布 (GA)

Azure 安全中心使用新的捆绑包扩展其 SQL 保护产品/服务，以涵盖开放源代码关系数据库：

- 适用于 Azure SQL 数据库服务器的 Azure Defender - 保护 Azure 原生 SQL 服务器
- 计算机上的 Azure Defender for SQL 服务器：将相同的保护扩展到混合、多云和本地环境中的 SQL 服务器
- **适用于开放源代码关系数据库的 Azure Defender** - 保护你的Azure Database for MySQL、PostgreSQL 和 MariaDB 单一服务器

适用于开放源代码关系数据库的 Azure Defender 持续监视服务器是否受到安全威胁，并检测异常的数据库活动，指出对 Azure Database for MySQL、PostgreSQL 和 MariaDB 的潜在威胁。 下面是一些示例：

- **精确检测暴力攻击** - 适用于开放源代码关系数据库的 Azure Defender 提供有关尝试的和成功的暴力攻击的详细信息。 让你可以更全面地了解你的环境中的攻击的性质和状态，据此展开调查和进行响应。
- **行为警报检测** - 适用于开放源代码关系数据库的 Azure Defender 会提醒你服务器上存在的可疑和意外行为，例如对数据库的访问模式的更改。
- **基于威胁情报的检测** -Azure Defender 应用 Microsoft 的威胁情报和巨大的知识库来发出威胁警报，以便你进行应对。

阅读[适用于开放源代码关系数据库的 Azure Defender 的简介](defender-for-databases-introduction.md)，了解更多信息。

### <a name="new-alerts-for-azure-defender-for-resource-manager"></a>适用于资源管理器的 Azure Defender 的新警报

为了扩展适用于资源管理器的 Azure Defender 提供的威胁保护，我们添加以下提醒：

| 警报（警报类型）                                                                                                                                                | 说明                                                                                                                                                                                                                                                                                                                                                                                                                              | MITRE 技巧 | 严重性 |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------------:|----------|
|**针对你的 Azure 环境（预览版），以异常方式授予 RBAC 角色的权限**<br>(ARM_AnomalousRBACRoleAssignment)|比较同一个分配者执行的其他分配、同一分配者为相同被分派人执行的其他分配以及在租户中执行的其他分配时，适用于资源管理器的 Azure Defender 检测到异常的 RBAC 角色分配，原因是存在以下异常：分配时间、分配者的地点、分配者、身份验证方法、分配的实体、使用的客户端软件、分配范围。 此操作可能是由你的组织中的合法用户执行的。 或者，这可能表示组织中的某个帐户被入侵，威胁方正在尝试向其拥有的其他用户帐户授予权限。|横向移动，防御规避|中等|
|**以可疑方式为订阅创建的特权自定义角色（预览）**<br>(ARM_PrivilegedRoleDefinitionCreation)|适用于资源管理器的 Azure Defender 检测到你的订阅中发生了可疑的特权自定义角色定义创建行为。 此操作可能是由你的组织中的合法用户执行的。 或者，这可能表明你的组织中的帐户被入侵，并且威胁方正在尝试创建特权角色，以在将来用于规避检测。|横向移动，防御规避|低|
|**来自可疑 IP 地址的 Azure 资源管理器操作（预览版）**<br>(ARM_OperationFromSuspiciousIP)|适用于资源管理器的 Azure Defender 检测到来自某个 IP 地址的操作，这个 IP 地址在威胁情报源中被标记为可疑。|执行|中型|
|**Azure 资源管理器对可疑代理 IP 地址的操作（预览）**<br>(ARM_OperationFromSuspiciousProxyIP)|适用于资源管理器的 Azure Defender 检测到来自某个 IP 地址的资源管理操作，而这个 IP 地址与代理服务（如 TOR）相关联。 虽然这种行为可能是合法的，但经常出现在恶意活动中，在这些恶意活动中，攻击者会试图隐藏其源 IP。|防御规避|中|
||||

有关详细信息，请参阅：
- [适用于资源管理器的 Azure Defender 简介](defender-for-resource-manager-introduction.md)
- [响应适用于资源管理器的 Azure Defender 警报](defender-for-resource-manager-usage.md)
- [适用于资源管理器的 Azure Defender 提供的警报列表](alerts-reference.md#alerts-resourcemanager)


### <a name="cicd-vulnerability-scanning-of-container-images-with-github-workflows-and-azure-defender-preview"></a>通过 GitHub 工作流和 Azure Defender (预览版）扫描容器映像的 CI/CD 漏洞

适用于容器注册表的 Azure Defender 现在让 DevSecOps 团队可以观察 GitHub 操作工作流。

针对容器映像的新漏洞扫描功能利用 Trivy 来帮助开发人员在将映像推送到容器注册表 *之前*， 扫描其容器映像中的常见漏洞。

容器扫描报告在 Azure 安全中心中进行汇总，使安全团队能够更好地理解和了解易受攻击的容器映像来源，以及是从哪里生成工作流和存储库。

有关详细信息，请参阅[识别 CI/CD 工作流中有漏洞的容器映像](defender-for-container-registries-cicd.md)。

### <a name="more-resource-graph-queries-available-for-some-recommendations"></a>可用于某些建议的更多资源图查询

安全中心的所有建议都可以使用 **打开查询** 中的“Azure 资源图”查看有关受影响的资源的状态信息。 有关此强大功能的完整详细信息，请参阅[在 Azure Resource Graph 资源管理器 (ARG) 中查看建议数据](security-center-recommendations.md#review-recommendation-data-in-azure-resource-graph-explorer-arg)。

安全中心包含内置的漏洞扫描程序，用于扫描 VM、SQL 服务器及其主机，以及容器注册表，以发现安全漏洞。 这些结果将作为建议返回，其中关于每个资源类型的所有单个发现都将集中到一个视图中。 这三个建议是：

- 应修正 Azure 容器注册表映像中的漏洞（由 Qualys 提供技术支持）
- 应修正虚拟机中的漏洞
- SQL 数据库应已解决漏洞结果
- 计算机上的 SQL 服务器应已解决漏洞结果

进行此更改后，也可以使用 **打开查询** 按钮打开显示安全发现的查询。

:::image type="content" source="media/release-notes/open-query-menu-security-findings.png" alt-text="“打开查询”按钮现提供更详细的查询选项，显示漏洞扫描程序相关建议的安全发现。":::

**打开查询** 按钮提供了一些其他相关建议的附加选项。

了解关于安全中心漏洞扫描程序的更多信息：

- [适用于 Azure 和混合计算机的 Azure Defender 集成式 Qualys 漏洞扫描程序](deploy-vulnerability-assessment-vm.md)
- [适用于 SQL 服务器的集成式漏洞评估扫描程序](defender-for-sql-on-machines-vulnerability-assessment.md)
- [适用于容器寄存表的 Azure Defender 集成式漏洞评估扫描程序](defender-for-container-registries-usage.md)

### <a name="sql-data-classification-recommendation-severity-changed"></a>更改了 SQL 数据分类建议严重性

已将 **“应对 SQL 数据库中的敏感数据进行分类”** 建议的严重性从 **高** 更改为 **低**。

这是对此建议的持续更改的一部分，此建议是在[针对关于对 SQL 数据库中的敏感数据进行分类的建议进行了增强](upcoming-changes.md#enhancements-to-recommendation-to-classify-sensitive-data-in-sql-databases)中提出的。

### <a name="new-recommendations-to-enable-trusted-launch-capabilities-in-preview"></a>用于启用受信任启动功能（预览版）的新建议

Azure 提供受信任启动作为一种无缝提高[第 2 代](../virtual-machines/generation-2.md) VM 安全性的方式。 受信任启动能够防范具有持续性的高级攻击手法。 受信任启动由多种可单独启用的协调式基础结构技术组成。 每种技术都针对错综复杂的威胁提供另一层防御。 在 [Azure 虚拟机的受信任启动](../virtual-machines/trusted-launch.md)中了解详细信息。

> [!IMPORTANT]
> 受信任启动要求创建新的虚拟机。 如果现有的虚拟机在最初创建时未配置受信任启动，则无法在其上启用受信任启动。
> 
> 受信任启动目前为公共预览版。 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。

安全中心的建议 **应在受支持的虚拟机上启用 vTPM** 确保你的 Azure VM 会使用 vTPM。 硬件受信任的平台模块的这个虚拟化版本通过测量 VM（UEFI、OS、系统和驱动程序）的整个启动链来实现证明。

启用 vTPM 后， **来宾证明扩展名** 可以远程验证安全启动。 以下建议可确保部署此扩展名：

- **应在受支持的 Windows 虚拟机上启用安全启动**
- **应在受支持的 Windows 虚拟机上安装来宾证明扩展名**
- **应在受支持的 Windows 虚拟机规模集上安装来宾证明扩展名**
- **应在受支持的 Linux 虚拟机上安装来宾证明扩展名**
- **应在受支持的 Linux 虚拟机规模集上安装来宾证明扩展名**

在 [Azure 虚拟机的受信任启动](../virtual-machines/trusted-launch.md)中了解详细信息。 

### <a name="new-recommendations-for-hardening-kubernetes-clusters-in-preview"></a>用于强化 Kubernetes 群集（预览版）的新建议

以下建议可用于进一步强化 Kubernetes 群集

- **Kubernetes 群集不得使用默认命名空间** - 防止在 Kubernetes 群集中使用默认命名空间，以防止对 ConfigMap、Pod、Secret、Service 和 ServiceAccount 资源类型进行未经授权的访问。
- **Kubernetes 群集应禁用自动装载 API 凭据** - 若要防止可能泄露的 Pod 资源对 Kubernetes 群集运行 API 命令，请禁用自动装载 API 凭据。
- **Kubernetes 群集不应授予 CAPSYSADMIN 安全功能**

如需了解安全中心如何保护容器化环境，请查阅[安全中心的容器安全性](container-security.md)。

### <a name="assessments-api-expanded-with-two-new-fields"></a>评估扩充了两个新字段的  API

我们已将以下两个字段添加到[评估 REST API](/rest/api/securitycenter/assessments)中：

- **FirstEvaluationDate** – 创建和首次评估建议的时间。 返回为 ISO 8601 格式的 UTC 时间。
- **StatusChangeDate** – 建议状态上次更改的时间。 返回为 ISO 8601 格式的 UTC 时间。

这些字段的初始默认值（对于所有建议）为 `2021-03-14T00:00:00+0000000Z`。

若要访问此信息，可以使用下表中的任意方法。

| 工具                 | 详细信息                                                                                                                                                                |
|----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| REST API 调用        | 获取 https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/providers/Microsoft.Security/assessments?api-version=2019-01-01-preview& $expand=statusEvaluationDates |
| Azure Resource Graph | `securityresources`<br>`where type == "microsoft.security/assessments"`                                                                                                |
| 连续导出    | 这两个专用字段将可用于 Log Analytics 工作区数据                                                                                            |
| [CSV 导出](continuous-export.md#manual-one-time-export-of-alerts-and-recommendations) | CSV 文件中包含这两个字段                                                        |
|                      |                                                                                                                                                                        |


详细了解[评估 REST API](/rest/api/securitycenter/assessments)。


### <a name="asset-inventory-gets-a-cloud-environment-filter"></a>资产盘存增加了云环境筛选器

安全中心的资产清单页提供多个筛选器，可快速优化显示的资源列表。 有关详细信息，请参阅[利用资产清单浏览和管理资源](asset-inventory.md)。

新筛选器提供根据使用安全中心的多云功能连接的云帐户来优化列表的选项：

:::image type="content" source="media/asset-inventory/filter-environment.png" alt-text="盘存环境筛选器":::

详细了解多云功能：

- [将 AWS 帐户连接到 Azure 安全中心](quickstart-onboard-aws.md)
- [将 GCP 帐户连接到 Azure 安全中心](quickstart-onboard-gcp.md)
