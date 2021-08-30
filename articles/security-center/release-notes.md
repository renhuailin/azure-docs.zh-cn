---
title: Azure 安全中心的发行说明
description: 介绍 Azure 安全中心的新增功能和已更改的功能
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: reference
ms.date: 08/15/2021
ms.author: memildin
ms.openlocfilehash: 04cff79878c3110c6da8b40e7a207693e693e2ba
ms.sourcegitcommit: da9335cf42321b180757521e62c28f917f1b9a07
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2021
ms.locfileid: "122229037"
---
# <a name="whats-new-in-azure-security-center"></a>Azure 安全中心的新增功能

安全中心正在积极开发中，并不断得到改进。 为及时了解最新开发成果，此页提供了有关新功能、bug 修复和已弃用功能的信息。

本页面会频繁更新，请经常回来查看。 

若要了解即将在安全中心推出的计划性更改，请参阅[即将推出的对 Azure 安全中心的重要更改](upcoming-changes.md)。 

> [!TIP]
> 如果要查找 6 个月之前的项目，可查看 [Azure 安全中心的新增功能存档](release-notes-archive.md)。

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
- [适用于资源管理器的 Azure Defender 的警报的逻辑重组](#logical-reorganization-of-azure-defender-for-resource-manager-alerts)                                           
- [增强了关于启用 Azure 磁盘加密 (ADE) 的建议](#enhancements-to-recommendation-to-enable-azure-disk-encryption-ade)                                     
- [安全功能分数和法规合规性数据的连续导出功能正式发布 (GA)](#continuous-export-of-secure-score-and-regulatory-compliance-data-released-for-general-availability-ga)
- [对法规合规性评估的更改可以触发工作流自动化 (GA)](#workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-ga)
- [评估 API 字段“FirstEvaluationDate”和“StatusChangeDate”现在可用于工作区架构和逻辑应用](#assessments-api-field-firstevaluationdate-and-statuschangedate-now-available-in-workspace-schemas-and-logic-apps)
- [向 Azure Monitor 工作簿库添加了“一段时间内的合规性”工作簿模板](#compliance-over-time-workbook-template-added-to-azure-monitor-workbooks-gallery)

### <a name="azure-sentinel-connector-now-includes-optional-bi-directional-alert-synchronization-in-preview"></a>Azure Sentinel 连接器现在包含可选的双向警报同步（预览版）

安全中心以原生方式与 [Azure Sentinel](../sentinel/index.yml)（Azure 的云原生 SIEM 和 SOAR 解决方案）集成。 

Azure Sentinel 包括 Azure 安全中心在订阅和租户级别的内置连接器。 若要了解详细信息，请参阅[将警报流式传输到 Azure Sentinel](export-to-siem.md#stream-alerts-to-azure-sentinel)。

将 Azure Defender 连接到 Azure Sentinel 时，系统会在这两项服务之间同步已引入到 Azure Sentinel 中的 Azure Defender 警报的状态。 例如，当某个警报在 Azure Defender 中处于已关闭状态时，该警报在 Azure Sentinel 中也会显示为已关闭。 在 Azure Defender 中更改警报的状态不会影响包含同步 Azure Sentinel 警报的任何 Azure Sentinel 事件的状态，只会影响同步警报自身的状态。

启用此预览功能（即“双向警报同步”）后，系统会自动将原始 Azure Defender 警报的状态与包含这些 Azure Defender 警报副本的 Azure Sentinel 事件的状态同步。 例如，当包含 Azure Defender 警报的 Azure Sentinel 事件被关闭时，Azure Defender 将自动关闭相应的原始警报。

若要了解详细信息，请参阅[从 Azure 安全中心连接 Azure Defender 警报](../sentinel/connect-azure-security-center.md)。

### <a name="logical-reorganization-of-azure-defender-for-resource-manager-alerts"></a>适用于资源管理器的 Azure Defender 的警报的逻辑重组

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

3 月，我们在安全中心发布了集成式 Azure Monitor 工作簿体验（请参阅[集成到安全中心的 Azure Monitor 工作簿以及提供的三个模板](#azure-monitor-workbooks-integrated-into-security-center-and-three-templates-provided)）。

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

Azure Defender for Kubernetes 最近已扩展，可保护托管在本地和多云环境中的 Kubernetes 群集。 参阅[使用 Azure Defender for Kubernetes 保护混合和多云 Kubernetes 部署（预览版）](release-notes.md#use-azure-defender-for-kubernetes-to-protect-hybrid-and-multi-cloud-kubernetes-deployments-in-preview)，了解更多信息。

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

**打开查询** 按钮还提供了一些其他相关建议的附加选项。

了解关于安全中心漏洞扫描程序的更多信息：

- [适用于 Azure 和混合计算机的 Azure Defender 集成式漏洞评估扫描程序](deploy-vulnerability-assessment-vm.md)
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


## <a name="april-2021"></a>2021 年 4 月

4 月的更新包括：
- [刷新了资源运行状况页（预览版）](#refreshed-resource-health-page-in-preview)
- [现在会每周重新扫描最近拉取的容器注册表映像（正式发布 (GA)）](#container-registry-images-that-have-been-recently-pulled-are-now-rescanned-weekly-released-for-general-availability-ga)
- [使用 Azure Defender for Kubernetes 保护混合和多云 Kubernetes 部署（预览版）](#use-azure-defender-for-kubernetes-to-protect-hybrid-and-multi-cloud-kubernetes-deployments-in-preview)
- [Microsoft Defender for Endpoint 与 Azure Defender 的集成现在支持 Windows Server 2019 和 Windows 10 虚拟桌面 (WVD)（正式发布 (GA)）](#microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-released-for-general-availability-ga)
- [关于启用适用于 DNS 和资源管理器的 Azure Defender 的建议（预览版）](#recommendations-to-enable-azure-defender-for-dns-and-resource-manager-in-preview)
- [添加了三个监管合规性标准：Azure CIS 1.3.0、CMMC 级别 3 和限制性的新西兰 ISM](#three-regulatory-compliance-standards-added-azure-cis-130-cmmc-level-3-and-new-zealand-ism-restricted)
- [与来宾配置相关的四个新建议（预览版）](#four-new-recommendations-related-to-guest-configuration-in-preview)
- [CMK 建议移动到最佳做法安全控制](#cmk-recommendations-moved-to-best-practices-security-control)
- [11 Azure Defender 警报已弃用](#11-azure-defender-alerts-deprecated)
- [“应用系统更新”安全控制中的两个建议已弃用](#two-recommendations-from-apply-system-updates-security-control-were-deprecated)
- [从 Azure Defender 仪表板中删除了适用于计算机上的 SQL 的 Azure Defender 磁贴](#azure-defender-for-sql-on-machine-tile-removed-from-azure-defender-dashboard)
- [21 条建议在不同安全控件之间进行了调动](#21-recommendations-moved-between-security-controls)

### <a name="refreshed-resource-health-page-in-preview"></a>刷新了资源运行状况页（预览版）

安全中心的资源运行状况已展开、增强和改进，提供单个资源总体运行状况的快照视图。 

可以查看有关该资源的详细信息以及适用于该资源的所有建议。 如果使用 [Azure Defender](azure-defender.md)，还可查看该特定资源的未解决的安全警报。

若要打开资源的资源运行状况页，请从[资产清单页](asset-inventory.md)中选择任何资源。

安全中心门户页面中的此预览页显示：

1. **资源信息**：资源组及其附加到的订阅，以及地理位置等。
1. **应用的安全功能**：是否为资源启用 Azure Defender。
1. **未完成的建议和警报数**：未完成的安全建议和 Azure Defender 警报的数量。
1. **可操作的建议和警报**：两个选项卡列出适用于资源的建议和警报。

:::image type="content" source="media/investigate-resource-health/resource-health-page-virtual-machine.gif" alt-text="显示虚拟机运行状况信息的 Azure 安全中心的资源运行状况页":::

参阅[教程：调查资源的运行状况](investigate-resource-health.md)，了解更多信息。


### <a name="container-registry-images-that-have-been-recently-pulled-are-now-rescanned-weekly-released-for-general-availability-ga"></a>现在会每周重新扫描最近拉取的容器注册表映像（正式发布 (GA)）

适用于容器注册表的 Azure Defender 包含内置漏洞扫描程序。 此扫描程序会立即扫描推送到注册表中的任何映像以及在过去 30 天内拉取的任何映像。

每天都会发现新漏洞。 通过此更新，将每周重新扫描一次过去 30 天内从注册表拉取的容器映像。 这可确保在映像中识别新发现的漏洞。

扫描按映像收费，因此重新扫描不产生额外费用。

可在[使用适用于容器注册表的 Azure Defender 来扫描映像是否存在漏洞](defender-for-container-registries-usage.md)中详细了解此扫描程序。


### <a name="use-azure-defender-for-kubernetes-to-protect-hybrid-and-multi-cloud-kubernetes-deployments-in-preview"></a>使用 Azure Defender for Kubernetes 保护混合和多云 Kubernetes 部署（预览版）

Azure Defender for Kubernetes 正在扩展其威胁防护功能，以便在部署时保护其群集。 这已通过集成[已启用 Azure Arc 的 Kubernetes](../azure-arc/kubernetes/overview.md) 及其新的[扩展功能](../azure-arc/kubernetes/extensions.md)来实现。 

在非 Azure Kubernetes 群集上启用 Azure Arc 后，根据 Azure 安全中心提供的新建议，仅需单击几下，即可将 Azure Defender 扩展部署到其中。

使用建议（启用 Azure Arc 的 Kubernetes 群集应已安装 Azure Defender 的扩展）和扩展保护在其他云提供程序中部署的 Kubernetes 群集，但不支持其托管的 Kubernetes 服务。

Azure 安全中心、Azure Defender 和启用了 Azure Arc 的 Kubernetes 之间的集成将带来以下影响：

- 轻松地将 Azure Defender 扩展预配到未受保护的 Azure Arc 启用 Kubernetes 群集（手动和大规模）
- 从 Azure Arc 门户监视 Azure Defender 扩展及其预配状态
- 请转到 Azure Arc 门户的新“安全性”页中，查看所报告的安全中心安全建议
- 请转到 Azure Arc 门户的新“安全性”页中，查看所报告的 Azure Defender 标识安全威胁
- 启用 Azure Arc 的 Kubernetes 群集可以集成到 Azure 安全中心平台和体验

有关详细信息，[请参阅通过本地和多云 Kubernetes 群集使用 Azure Defender for Kubernetes](defender-for-kubernetes-azure-arc.md)。

:::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-recommendation.png" alt-text="有关为已启用 Azure Arc 的 Kubernetes 群集部署 Azure Defender 扩展的 Azure 安全中心建议。" lightbox="media/defender-for-kubernetes-azure-arc/extension-recommendation.png":::


### <a name="microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-released-for-general-availability-ga"></a>Microsoft Defender for Endpoint 与 Azure Defender 的集成现在支持 Windows Server 2019 和 Windows 10 虚拟桌面 (WVD)（正式发布 (GA)）

Microsoft Defender for Endpoint 是一种整体的、云交付的终结点安全解决方案。 它提供基于风险的漏洞管理和评估以及终结点检测和响应 (EDR)。 有关将 Defender for Endpoint 与 Azure 安全中心一起使用的好处的完整列表，请参阅[使用安全中心的集成式 EDR 解决方案 (Microsoft Defender for Endpoint) 保护终结点](security-center-wdatp.md)。

为 Windows Server 上的服务器启用 Azure Defender 时，Defender for Endpoint 的许可证会包含在计划在中。 如果已为服务器启用 Azure Defender 并且订阅中有 Windows 2019 服务器，则它们会自动接收包含此更新的 Defender for Endpoint。 无需手动操作。 

支持现在已扩展为包括 Windows Server 2019 和 [Windows 虚拟桌面 (WVD)](../virtual-desktop/overview.md)。

> [!NOTE]
> 如果要在 Windows Server 2019 计算机上启用 Defender for Endpoint，请确保它满足[启用 Microsoft Defender for Endpoint 集成](security-center-wdatp.md#enable-the-microsoft-defender-for-endpoint-integration)中所述的先决条件。


### <a name="recommendations-to-enable-azure-defender-for-dns-and-resource-manager-in-preview"></a>关于启用适用于 DNS 和资源管理器的 Azure Defender 的建议（预览版）

添加了两项新建议，以简化启用[适用于资源管理器的 Azure Defender](defender-for-resource-manager-introduction.md) 和[适用于 DNS 的 Azure Defender](defender-for-dns-introduction.md)：

- **应启用适用于资源管理器的 Azure Defender** - 适用于资源管理器的 Defender 会自动监视组织中的资源管理操作。 Azure Defender 会检测威胁，并向你发出有关可疑活动的警报。
- **应启用适用于 DNS 的 Azure Defender** - 适用于 DNS 的 Defender 通过持续监视所有来自 Azure 资源的 DNS 查询为云资源额外提供保护层。 Azure Defender 会在 DNS 层向你发出有关可疑活动的警报。

启用 Azure Defender 计划会产生费用。 若要了解各区域的定价详细信息，请访问安全中心的定价页： https://aka.ms/pricing-security-center 。

> [!TIP]
> 预览版建议不会显示资源运行不正常，并且在计算安全功能分数时不会包含这些建议。 请尽量修正这些建议，以便在预览期结束之后，借助这些建议提高安全功能分数。 如需详细了解如何响应这些建议，请参阅[修正 Azure 安全中心的建议](security-center-remediate-recommendations.md)。


### <a name="three-regulatory-compliance-standards-added-azure-cis-130-cmmc-level-3-and-new-zealand-ism-restricted"></a>添加了三个监管合规性标准：Azure CIS 1.3.0、CMMC 级别 3 和限制性的新西兰 ISM

我们添加了适用于 Azure 安全中心的三个标准。 使用监管合规性仪表板，现在可以根据以下标准来跟踪合规性：

- [CIS Microsoft Azure 基础基准检验 1.3.0](../governance/policy/samples/cis-azure-1-3-0.md)
- [CMMC 级别 3](../governance/policy/samples/cmmc-l3.md)
- [受限于新西兰 ISM](../governance/policy/samples/new-zealand-ism.md)

可以按照[在监管合规性仪表板中自定义标准集](update-regulatory-compliance-packages.md)中所述，将这些标准分配给订阅。

:::image type="content" source="media/release-notes/additional-regulatory-compliance-standards.png" alt-text="添加了三个标准，用于 Azure 安全中心的监管合规性仪表板。" lightbox="media/release-notes/additional-regulatory-compliance-standards.png":::

了解详细信息，请参阅：
- [在监管合规仪表板中自定义标准集](update-regulatory-compliance-packages.md)
- [教程：提高合规性](security-center-compliance-dashboard.md)
- [常见问题解答 - 法规合规性仪表板](security-center-compliance-dashboard.md#faq---regulatory-compliance-dashboard)

### <a name="four-new-recommendations-related-to-guest-configuration-in-preview"></a>与来宾配置相关的四个新建议（预览版）

Azure 的[来宾配置扩展](../governance/policy/concepts/guest-configuration.md) 向安全中心报告，以帮助确保强化虚拟机的来宾内设置。 已启用 Arc 的服务器不需要该扩展，因为已连接 Arc 的计算机代理中包含该扩展。 扩展需要计算机上系统托管标识。

我们已将四个新建议添加到安全中心，以便充分利用此扩展。

- 这两个建议会提示安装该扩展及其所需的系统托管标识：
    - **应在计算机上安装来宾配置扩展**
    - **应使用系统分配的托管标识来部署虚拟机的来宾配置扩展**

- 当扩展已安装并正在运行时，它将开始审核你的计算机，此时系统将提示你强化设置，例如配置操作系统和环境设置。 这两个建议将提示你按如下所述强化 Windows 和 Linux 计算机：
    - **应在计算机上启用 Windows Defender 攻击防护**
    - **对 Linux 虚拟机进行身份验证需要 SSH 密钥**

详细[了解 Azure Policy 的来宾配置](../governance/policy/concepts/guest-configuration.md)。

### <a name="cmk-recommendations-moved-to-best-practices-security-control"></a>CMK 建议移动到最佳做法安全控制

每个组织的安全计划都包含数据加密要求。 默认情况下，通过服务托管密钥对 Azure 客户的数据进行静态加密。 但是，客户管理的密钥 (CMK) 通常需要满足法规符合性标准。 通过 CMK 能够使用自己创建并拥有的 [Azure Key Vault](../key-vault/general/overview.md) 密钥对数据进行加密。 你可以完全控制并负责关键生命周期，包括轮换和管理。

Azure 安全中心的安全控制是相关安全建议的逻辑组，反映了你易受攻击的攻击面。 对于每个控制，可以看到为所有资源修正该控制中列出的所有建议后，安全评分可以增加的最高分数。 “实现安全最佳做法”这一安全控制得分为零。 因此，此控制中的建议不会影响安全评分。

下面列出的建议将移到“实现安全最佳做法”这一安全控制，以更好地反应它们的可选性质。 这一移动确保了这些建议都处于最适当的控制之下，以满足其目标。

- Azure Cosmos DB 帐户应使用客户管理的密钥来加密静态数据
- Azure 机器学习工作区应使用客户管理的密钥 (CMK) 进行加密
- 认知服务帐户应启用使用客户管理的密钥 (CMK) 进行数据加密
- 容器注册表应使用客户管理的密钥 (CMK) 进行加密
- SQL 托管实例应使用客户管理的密钥进行静态数据加密
- SQL Server 应使用客户管理的密钥进行静态数据加密
- 存储帐户应使用客户管理的密钥 (CMK) 进行加密

请在[安全控件及其建议](secure-score-security-controls.md#security-controls-and-their-recommendations)中了解每个安全控件中的建议。


### <a name="11-azure-defender-alerts-deprecated"></a>11 Azure Defender 警报已弃用

下面列出的 11 个 Azure Defender 警报已弃用。

- 新警报将取代下面两种警报并扩大覆盖范围：

    | AlertType                | AlertDisplayName                                                         |
    |--------------------------|--------------------------------------------------------------------------|
    | ARM_MicroBurstDomainInfo | 预览版 - 检测到 MicroBurst 工具包“Get-AzureDomainInfo”函数运行 |
    | ARM_MicroBurstRunbook    | 预览版 - 检测到 MicroBurst 工具包“Get-AzurePasswords”函数运行  |
    |                          |                                                                          |

- 下面 9 种警报与已弃用的 Azure Active Directory 标识保护连接器 (IPC) 相关：

    | AlertType           | AlertDisplayName              |
    |---------------------|-------------------------------|
    | UnfamiliarLocation  | 不熟悉的登录属性 |
    | AnonymousLogin      | 匿名 IP 地址          |
    | InfectedDeviceLogin | 受恶意软件感染的 IP 地址     |
    | ImpossibleTravel    | 异常位置登录               |
    | MaliciousIP         | 恶意 IP 地址          |
    | LeakedCredentials   | 凭据泄露            |
    | PasswordSpray       | 密码喷射                |
    | LeakedCredentials   | Azure AD 威胁智能  |
    | AADAI               | Azure AD AI                   |
    |                     |                               |
 
    > [!TIP]
    > 这 9 种 IPC 警报绝不是安全中心警报。 它们是 Azure Active Directory (AAD) 标识保护连接器 (IPC) 的一部分，此连接器将这些警警报发送给安全中心。 在过去 2 年中，只看到这些警报的客户就是在 2019 或更早版本中配置了导出（从连接器到 ASC）。 AAD IPC 继续将其显示其专属的警报系统中，并在 Azure Sentinel 中继续提供。 唯一的变化是它们不再出现在安全中心。

### <a name="two-recommendations-from-apply-system-updates-security-control-were-deprecated"></a>“应用系统更新”安全控制中的两项建议已弃用 

下面这两个建议已弃用，这些更改可能会对安全分数产生轻微影响：

- 应重启计算机来应用系统更新
- **应在计算机上安装监视代理**。 此建议仅与本地计算机相关，其中某些逻辑将转移到另一条建议，即“应在计算机上解决 Log Analytics 代理运行状况问题”

建议检查连续导出和工作流自动化配置，以查看这些建议是否包括在其中。 此外，任何仪表板或其他可能使用它们的监视工具都应该相应地进行更新。

有关这些建议的详细信息，请参阅[安全建议参考页面](recommendations-reference.md)。

### <a name="azure-defender-for-sql-on-machine-tile-removed-from-azure-defender-dashboard"></a>从 Azure Defender 仪表板中删除了适用于计算机上的 SQL 的 Azure Defender 磁贴

Azure Defender 仪表板的覆盖区包括对应于环境相关 Azure Defender 计划的磁贴。 由于报告受保护和未受保护资源数量时出现问题，我们决定在解决此问题前暂时删除适用于计算机上的 SQL 的 Azure Defender 的资源覆盖状态。


### <a name="21-recommendations-moved-between-security-controls"></a>21 条建议在不同安全控件之间进行了调动 

以下建议已移动到其他的安全控件。 安全控件是相关安全建议的逻辑组，反映了你易受攻击的攻击面。 这一调动确保了每个建议都处于最适当的控制之下，以满足其目标。

请在[安全控件及其建议](secure-score-security-controls.md#security-controls-and-their-recommendations)中了解每个安全控件中的建议。

|建议 |更改和影响  |
|---------|---------|
|应对 SQL Server 启用漏洞评估<br>应对 SQL 托管实例启用漏洞评估<br>应立即修正 SQL 数据库的漏洞<br>应修正 VM 中的 SQL 数据库漏洞     |从“修正漏洞”（得 6 分）<br>变为“修正安全配置“（得 4 分）。<br>根据你的环境，这些建议会减少对评分的影响。|
|应该为你的订阅分配了多个所有者<br>自动化帐户变量应进行加密<br> IoT 设备 - 经审核的进程已停止发送事件<br> IoT 设备 - 操作系统基线验证失败<br> IoT 设备 - 需要进行 TLS 加密套件升级<br> IoT 设备 - 打开设备上的端口<br> IoT 设备 - 在其中一个链中找到了宽容防火墙策略<br> IoT 设备 - 在输入链中找到了宽容防火墙规则<br> IoT 设备 - 在输出链中找到了宽容防火墙规则<br>应启用 IoT 中心的诊断日志<br> IoT 设备 - 代理正在发送未充分利用的消息<br>IoT 设备 - 默认 IP 筛选策略应为“拒绝”<br>IoT 设备 - IP 筛选器规则为“大范围 IP”<br>IoT 设备 - 应调整代理消息间隔和大小<br>IoT 设备 - 完全相同的身份验证凭据<br>IoT 设备 - 经审核的进程停止发送事件<br>IoT 设备 - 应修复操作系统 (OS) 基线配置|移到 **实施安全最佳做法**。<br>如果某条建议移到“实施安全最佳做法”控制（不得分），则这条建议不再影响安全分数。|
|||


## <a name="march-2021"></a>2021 年 3 月

3 月的更新包括：

- [集成到安全中心的 Azure 防火墙管理](#azure-firewall-management-integrated-into-security-center)
- [SQL 漏洞评估现在包含“禁用规则”体验（预览）](#sql-vulnerability-assessment-now-includes-the-disable-rule-experience-preview)
- [集成到安全中心的 Azure Monitor 工作簿以及提供的三个模板](#azure-monitor-workbooks-integrated-into-security-center-and-three-templates-provided)
- [法规合规性仪表板现在包含 Azure 审核报告（预览）](#regulatory-compliance-dashboard-now-includes-azure-audit-reports-preview)
- [可以在 Azure Resource Graph 中通过“在 ARG 中浏览”查看建议数据](#recommendation-data-can-be-viewed-in-azure-resource-graph-with-explore-in-arg)
- [更新部署工作流自动化的的策略](#updates-to-the-policies-for-deploying-workflow-automation)
- [两条旧版建议不再将数据直接写入 Azure 活动日志](#two-legacy-recommendations-no-longer-write-data-directly-to-azure-activity-log)
- [建议页面功能增强](#recommendations-page-enhancements)


### <a name="azure-firewall-management-integrated-into-security-center"></a>集成到安全中心的 Azure 防火墙管理

打开 Azure 安全中心时，首先显示的是概述页面。 

此交互式仪表板提供了混合云工作负载安全状况的统一视图。 此外，它还显示安全警报、覆盖范围信息等等。

在帮助你通过中心体验查看安全状态的过程中，我们已将 Azure 防火墙管理器集成到此仪表板中。 你现在可以检查所有网络的防火墙覆盖状态，并从安全中心开始集中管理 Azure 防火墙策略。

有关此仪表板的详细信息，请参阅 [Azure 安全中心的概述页面](overview-page.md)。

:::image type="content" source="media/release-notes/overview-dashboard-firewall-manager.png" alt-text="安全中心的概述仪表板，其中包含 Azure 防火墙的磁贴":::


### <a name="sql-vulnerability-assessment-now-includes-the-disable-rule-experience-preview"></a>SQL 漏洞评估现在包含“禁用规则”体验（预览）

安全中心包含内置漏洞扫描仪，有助于发现、跟踪和修正潜在的数据库漏洞。 评估扫描结果概述了 SQL 计算机的安全状态以及任何安全发现结果的详细信息。

如果组织需要忽略发现结果，而不是修正漏洞，则可以选择禁用发现结果。 禁用发现结果不会影响安全分数，也不会产生有害的噪音。

有关详细信息，请参阅[禁用特定发现结果](defender-for-sql-on-machines-vulnerability-assessment.md#disable-specific-findings-preview)。



### <a name="azure-monitor-workbooks-integrated-into-security-center-and-three-templates-provided"></a>集成到安全中心的 Azure Monitor 工作簿以及提供的三个模板

在 Ignite Spring 2021 召开过程中，我们宣布在安全中心中推出集成式 Azure Monitor 工作簿。

借助新集成，可以开始使用安全中心的库中的现成模板。 通过使用工作簿模板，可以访问并生成动态、可视化报告，以跟踪组织的安全状况。 此外，还可以根据安全中心数据或任何其他受支持的数据类型创建新的工作簿，并且快速部署来自安全中心 GitHub 社区的社区工作簿。

提供了三个模板报告：

- 一段时间内的安全功能分数 - 跟踪订阅的分数以及对资源建议的更改
- 系统更新 - 按资源、OS 和严重性等查看缺失的系统更新
- 漏洞评估发现结果 - 查看对 Azure 资源进行漏洞扫描的发现结果

如需了解如何使用这些报告或生成自己的报告，请参阅[创建丰富的交互式安全中心数据报表](custom-dashboards-azure-workbooks.md)。

:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-snip.png" alt-text="一段时间内的安全功能分数报告。":::


### <a name="regulatory-compliance-dashboard-now-includes-azure-audit-reports-preview"></a>法规合规性仪表板现在包含 Azure 审核报告（预览）

现在可以从法规合规性仪表板的工具栏中下载 Azure 和 Dynamics 认证报告。 

:::image type="content" source="media/release-notes/audit-reports-regulatory-compliance-dashboard.png" alt-text="法规合规性仪表板的工具栏":::

可以选择用于相关报告类型（PCI、SOC 和 ISO 等）的选项卡，然后使用筛选器来查找所需的特定报告。

有关详细信息，请参阅[管理法规合规性仪表板中的标准](update-regulatory-compliance-packages.md)。

:::image type="content" source="media/release-notes/audit-reports-list-regulatory-compliance-dashboard.png" alt-text="筛选可用 Azure 审核报告的列表。":::



### <a name="recommendation-data-can-be-viewed-in-azure-resource-graph-with-explore-in-arg"></a>可以在 Azure Resource Graph 中通过“在 ARG 中浏览”查看建议数据

建议详细信息页现在包含“在 ARG 中浏览”工具栏按钮。 请使用此按钮来打开 Azure Resource Graph 查询，并浏览、导出和共享建议的数据。

使用 Azure Resource Graph (ARG)，可以通过可靠的筛选、分组和排序功能，快速访问你的云环境中的资源信息。 这是以编程方式或从 Azure 门户中查询 Azure 订阅中的信息的一种快速且有效的方式。

详细了解 [Azure Resource Graph (ARG)](../governance/resource-graph/index.yml)。

:::image type="content" source="media/release-notes/explore-in-resource-graph.png" alt-text="在 Azure Resource Graph 中浏览建议数据。":::


### <a name="updates-to-the-policies-for-deploying-workflow-automation"></a>对部署工作流自动化的的策略的更新

自动执行组织的监视和事件响应流程可以显著缩短调查和缓解安全事件所需的时间。

我们提供三个 Azure Policy“DeployIfNotExist”策略，这些策略可以创建并配置工作流自动化过程，以便你可以在组织内部署自动化：

|目标  |策略  |策略 ID  |
|---------|---------|---------|
|安全警报的工作流自动化|[为 Azure 安全中心警报部署工作流自动化](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
|安全建议的工作流自动化|[为 Azure 安全中心建议部署工作流自动化](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
|用于法规合规性的工作流自动化发生更改|[部署 Azure 安全中心合规工作流自动化](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-509122b9-ddd9-47ba-a5f1-d0dac20be63c)|509122b9-ddd9-47ba-a5f1-d0dac20be63c|
||||

以下是对这些策略的功能进行的两项更新：

- 分配后，它们将通过强制执行保持启用状态。
- 现在可以自定义这些策略，并更新任意参数，即使它们已部署，也是如此。 例如，如果用户想添加另一个评估密钥或编辑现有的评估密钥，则可以执行此操作。

开始使用[工作流自动化模板](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation)。

了解如何[自动响应安全中心触发器](workflow-automation.md)。


### <a name="two-legacy-recommendations-no-longer-write-data-directly-to-azure-activity-log"></a>两条旧版建议不再将数据直接写入 Azure 活动日志 

安全中心会将几乎所有安全建议的数据都传递到 Azure 顾问，而后者又会将这些数据写入 [Azure 活动日志](../azure-monitor/essentials/activity-log.md)。

对于其中两条建议，数据将同时直接写入 Azure 活动日志。 通过这项更改，安全中心会停止将这些旧版安全建议的数据直接写入活动日志。 而我们要将数据导出到 Azure 顾问，就像我们针对所有其他建议所做的那样。

这两条旧版建议为：
- 应在计算机上解决 Endpoint Protection 运行状况问题
- 应该修复计算机上安全配置中的漏洞

如果你一直是在活动日志的“TaskDiscovery 类型的建议”类别中访问这两条建议的信息，现在不再可以这样操作。


### <a name="recommendations-page-enhancements"></a>建议页面功能增强 

我们发布了改进版本的建议列表，以便直观地显示更多信息。

此页面现在显示：

1. 每个安全控制措施的最高分数和当前分数。
1. 图标（代替标记），如“修复”和“预览” 。
1. 一个新列，显示与每条建议相关的[策略计划](security-policy-concept.md)，在禁用“按控制措施分组”时显示。

:::image type="content" source="media/release-notes/recommendations-grid-enhancements.png" alt-text="Azure 安全中心的建议页的功能增强 - 2021 年 3 月" lightbox="media/release-notes/recommendations-grid-enhancements.png":::

:::image type="content" source="media/release-notes/recommendations-grid-enhancements-initiatives.png" alt-text="Azure 安全中心的建议“平面”列表的功能增强 - 2021 年 3 月" lightbox="media/release-notes/recommendations-grid-enhancements-initiatives.png":::

可在 [Azure 安全中心内的安全建议](security-center-recommendations.md)中了解详细信息。