---
title: Azure 自动化新增功能存档
description: 此内容集的“概述”部分中的新增功能发行说明包含 6 个月的活动。 6 个月过后，这些项目将从主文章中删除并放入此文章中。
services: automation
ms.topic: overview
ms.date: 08/27/2021
ms.custom: references_regions
ms.openlocfilehash: 459bfea7504bb9d9078dadabf7443e995d5c90bc
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123117268"
---
# <a name="archive-for-whats-new-in-azure-automation"></a>Azure 自动化新增功能存档？

[Azure 自动化新增功能](whats-new.md)这篇主要文章包含过去六个月的更新，而本文包含所有较早的信息。

“Azure 自动化的新增功能”提供以下相关信息：

- 最新版本
- 已知问题
- Bug 修复

---

## <a name="january-2021"></a>2021 年 1 月

### <a name="support-for-automation-and-state-configuration-declared-ga-in-switzerland-west"></a>已在瑞士西部正式发布对自动化和 State Configuration 的支持声明

**类型：** 新功能

现可在瑞士西部区域使用自动化帐户和 State Configuration。 有关详细信息，请阅读[公告](https://azure.microsoft.com/updates/azure-automation-in-switzerland-west-region/)。

### <a name="added-python-3-script-to-import-module-with-multiple-dependencies"></a>添加了 Python 3 脚本以导入具有多个依赖项的模块

**类型：** 新功能

可从我们的 [GitHub 存储库](https://github.com/azureautomation/runbooks/blob/master/Utility/Python/import_py3package_from_pypi.py)下载该脚本。 
 
### <a name="hybrid-runbook-worker-role-support-for-centos-8xrhel-8xsles-15"></a>混合 Runbook 辅助角色支持 Centos 8.x/RHEL 8.x/SLES 15

类型。 新功能

混合 Runbook 辅助角色功能支持 CentOS 8.x、REHL 8.x 和 SLES 15 分发版，仅用于对混合 Runbook 辅助角色的进程自动化。 请参阅[受支持的操作系统](automation-linux-hrw-install.md#supported-linux-operating-systems)，获取反映这些更改的文档更新。

### <a name="update-management-and-change-tracking-availability-in-australia-east-east-asia-west-us-and-central-us-regions"></a>在澳大利亚东部、东亚、美国西部和美国中部区域可以使用更新管理和更改跟踪

**类型：** 新功能

在澳大利亚东部、东亚、美国西部和美国中部区域可以使用自动化帐户、更改跟踪和清单，以及更新管理。 

### <a name="introduced-public-preview-of-python-3-runbooks-in-us-government-cloud"></a>在美国政府云中引入了 Python 3 runbook 公共预览版

类型：新功能 Azure 自动化在美国政府云区域中引入了对 Python 3 云和混合 runbook 执行的公共预览版支持。 有关详细信息，请参阅[公告](https://azure.microsoft.com/updates/azure-automation-python-3-public-preview/)。

### <a name="azure-automation-runbooks-moved-from-technet-script-center-to-github"></a>Azure 自动化 runbook 从 TechNet 脚本中心移到了 GitHub

**类型：** 更改计划

TechNet 脚本中心即将停用，并且 runbook 库中托管的所有 runbook 都已移至我们的[自动化 GitHub 组织](https://github.com/azureautomation)。 有关详细信息，请参阅[将 Azure 自动化 runbook 移动到 GitHub](https://techcommunity.microsoft.com/t5/azure-governance-and-management/azure-automation-runbooks-moving-to-github/ba-p/2039337)。

## <a name="december-2020"></a>2020 年 12 月

### <a name="azure-automation-and-update-management-private-link-ga"></a>Azure 自动化和更新管理专用链接已正式发布

**类型：** 新功能

已宣布对 Azure 全球云和政府云正式发布 Azure 自动化和更新管理支持。 Azure 自动化已实现专用链接支持，通过使用更新管理在计算机上安装补丁、通过 webhook 调用 runbook，并通过 State Configuration 服务使计算机合规，来确保在混合辅助角色上安全地执行 runbook。 有关详细信息，请参阅 [Azure 自动化专用链接支持](https://azure.microsoft.com/updates/azure-automation-private-link)

### <a name="azure-automation-classified-as-grade-c-certified-on-accessibility"></a>Azure 自动化分类为 C 级认证的辅助功能

**类型：** 新功能

Microsoft 产品的辅助功能可帮助机构满足全球辅助功能要求。 在[博客公告](https://cloudblogs.microsoft.com/industry-blog/government/2018/09/11/accessibility-conformance-reports/)页面上搜索“Azure 自动化”，阅读有关自动化服务的辅助功能一致性报告。

### <a name="support-for-automation-and-state-configuration-ga-in-uae-north"></a>已在阿拉伯联合酋长国北部正式发布对自动化和 State Configuration 的支持

**类型：** 新功能

现可在阿拉伯联合酋长国北部区域使用自动化帐户和 State Configuration。 有关详细信息，请阅读[公告](https://azure.microsoft.com/updates/azure-automation-in-uae-north-region/)。

### <a name="support-for-automation-and-state-configuration-ga-in-germany-west-central"></a>已在德国中西部正式发布对自动化和 State Configuration 的支持

**类型：** 新功能

现可在德国西部区域使用自动化帐户和 State Configuration。 有关详细信息，请阅读[公告](https://azure.microsoft.com/updates/azure-automation-in-germany-west-central-region/)。

### <a name="dsc-support-for-oracle-6-and-7"></a>针对 Oracle 6 和 7 的 DSC 支持

**类型：** 新功能

使用自动化 State Configuration 管理 Oracle Linux 6 和 7 计算机。 请参阅[受支持的 Linux 发行版](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions)，获取反映这些更改的文档更新。

### <a name="public-preview-for-python3-runbooks-in-automation"></a>自动化中 Python3 runbook 的公共预览版

**类型：** 新功能

Azure 自动化现支持在 Azure 全球云的所有区域中执行 Python 3 云和混合 runbook - 此功能现为公共预览版。 有关详细信息，请参阅 [公告]((https://azure.microsoft.com/updates/azure-automation-python-3-public-preview/) 。

## <a name="november-2020"></a>2020 年 11 月

### <a name="dsc-support-for-ubuntu-1804"></a>针对 Ubuntu 18.04 的 DSC 支持

**类型：** 新功能

请参阅[受支持的 Linux 发行版](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions)，获取反映这些更改的文档更新。

## <a name="october-2020"></a>2020 年 10 月

### <a name="support-for-automation-and-state-configuration-ga-in-switzerland-north"></a>已在瑞士北部正式发布对自动化和 State Configuration 的支持

**类型：** 新功能

现可在瑞士北部使用自动化帐户和 State Configuration。 有关详细信息，请阅读[公告](https://azure.microsoft.com/updates/azure-automation-in-switzerland-north-region/)。

### <a name="support-for-automation-and-state-configuration-ga-in-brazil-south-east"></a>已在巴西东南部正式发布对自动化和 State Configuration 的支持

**类型：** 新功能

现可在巴西东南部使用自动化帐户和 State Configuration。 有关详细信息，请阅读[公告](https://azure.microsoft.com/updates/azure-automation-in-brazil-southeast-region/)。

### <a name="update-management-availability-in-south-central-us"></a>现可在美国中南部使用更新管理功能

**类型：** 新功能

Azure 自动化区域映射已经更新，现支持在美国中南部区域使用更新管理功能。 请参阅[受支持的区域映射](how-to/region-mappings.md#supported-mappings)，获取反映此更改的文档更新。

## <a name="september-2020"></a>2020 年 9 月

### <a name="startstop-vms-during-off-hours-runbooks-updated-to-use-azure-az-modules"></a>在空闲时间启动/停止 VM runbook 已更新为使用 Azure Az 模块

**类型：** 新功能

启动/停止 VM runbook 已更新为用 Az 模块取代 Azure 资源管理器模块。 请参阅[在空闲时间启动/停止 VM](automation-solution-vm-management.md) 概述，获取反映这些更改的文档更新。

## <a name="august-2020"></a>2020 年 8 月

### <a name="published-the-dsc-extension-to-support-azure-arc"></a>已发布 DSC 扩展来支持 Azure Arc

**类型：** 新功能

使用 Azure 自动化 State Configuration 集中存储配置，并维护通过已启用 Azure Arc 的服务器 DSC VM 扩展启用的混合连接计算机的所需状态。 有关详细信息，请阅读[已启用 Arc 的服务器 VM 扩展概述](../azure-arc/servers/manage-vm-extensions.md)。

### <a name="july-2020"></a>2020 年 7 月

### <a name="introduced-public-preview-of-private-link-support-in-automation"></a>在自动化服务中引入了专用链接支持的公共预览版

**类型：** 新功能

借助 Azure 专用链接，可通过专用终结点将虚拟网络安全地连接到 Azure 自动化。 有关详细信息，请阅读[公告](https://azure.microsoft.com/updates/public-preview-private-link-azure-automation-is-now-available/)。

### <a name="hybrid-runbook-worker-support-for-windows-server-2008-r2"></a>针对 Windows Server 2008 R2 的混合 Runbook 辅助角色支持

**类型：** 新功能

自动化混合 Runbook 辅助角色支持 Windows Server 2008 R2 操作系统。 请参阅[受支持的操作系统](automation-windows-hrw-install.md#supported-windows-operating-system)，获取反映这些更改的文档更新。

### <a name="update-management-support-for-windows-server-2008-r2"></a>针对 Windows Server 2008 R2 的更新管理支持

**类型：** 新功能

更新管理功能支持评估和修补 Windows Server 2008 R2 操作系统。 请参阅[受支持的操作系统](update-management/operating-system-requirements.md)，获取反映这些更改的文档更新。

### <a name="automation-diagnostic-logs-schema-update"></a>自动化诊断日志架构更新

**类型：** 新功能

更改了 Log Analytics 服务中的 Azure 自动化日志数据的架构。 若要了解详细信息，请参阅[将 Azure 自动化作业数据转发到 Azure Monitor 日志](automation-manage-send-joblogs-log-analytics.md#filter-job-status-output-converted-into-a-json-object)。

### <a name="azure-lighthouse-supports-automation-update-management"></a>Azure Lighthouse 支持自动化更新管理

**类型：** 新功能

服务提供商和客户可通过 Azure Lighthouse 来使用更新管理功能实现委托资源管理。 在[此处](https://azure.microsoft.com/blog/how-azure-lighthouse-enables-management-at-scale-for-service-providers/)了解详细信息。

## <a name="june-2020"></a>2020 年 6 月

### <a name="automation-and-update-management-availability-in-the-us-gov-arizona-region"></a>现可在 US Gov 亚利桑那州区域使用自动化服务和更新管理功能

**类型：** 新功能

现可在 US Gov 亚利桑那州使用自动化帐户和更新管理功能。 有关详细信息，请参阅[公告](https://azure.microsoft.com/updates/azure-automation-generally-available-in-usgov-arizona-region/)。

### <a name="hybrid-runbook-worker-onboarding-script-updated-to-use-az-modules"></a>混合 Runbook 辅助角色加入脚本已经更新，现使用 Az 模块

**类型：** 新功能

New-OnPremiseHybridWorker runbook 已经更新，现支持 Az 模块。 有关详细信息，请查看 [PowerShell 库](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker/1.7)中的包。

### <a name="update-management-availability-in-china-east-2"></a>现可在中国东部 2 区域使用更新管理功能

**类型：** 新功能

Azure 自动化区域映射已经更新，现支持在中国东部 2 区域使用更新管理功能。 请参阅[受支持的区域映射](how-to/region-mappings.md#supported-mappings)，获取反映此更改的文档更新。

## <a name="may-2020"></a>2020 年 5 月

### <a name="updated-automation-service-dns-records-from-region-specific-to-automation-account-specific-urls"></a>已将自动化服务 DNS 记录从区域特定的 URL 更新为自动化帐户特定的 URL

**类型：** 新功能

Azure 自动化 DNS 记录已经更新，现支持专用链接。 有关详细信息，请阅读[公告](https://azure.microsoft.com/updates/azure-automation-updateddns-records/)。

### <a name="added-capability-to-keep-automation-runbooks-and-dsc-scripts-encrypted-by-default"></a>已添加使自动化 runbook 和 DSC 脚本默认加密的功能

**类型：** 新功能

除了提高资产的安全性以外，还会加密 Runbook 和 DSC 脚本以增强 Azure 自动化的安全性。

## <a name="april-2020"></a>2020 年 4 月

### <a name="retirement-of-the-automation-watcher-task"></a>停用自动化观察程序任务

**类型：** 更改计划

现推荐且支持使用 Azure 逻辑应用来监视事件、计划定期任务和触发操作。 我们将不再对观察程序任务功能进行任何投入。 若要了解详细信息，请参阅[使用逻辑应用计划和运行定期自动化任务](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)。

## <a name="march-2020"></a>2020 年 3 月

### <a name="support-for-impact-level-5-il5-compute-isolation-in-azure-commercial-and-government-cloud"></a>Azure 商用和政府云中对影响级别 5 (IL5) 计算隔离的支持

**类型：**

可在 Azure 政府中使用 Azure 自动化混合 Runbook 辅助角色来支持影响级别为 5 的工作负载。 若要了解详细信息，请参阅我们的[文档](automation-hybrid-runbook-worker.md#support-for-impact-level-5-il5)。

## <a name="february-2020"></a>2020 年 2 月

### <a name="introduced-support-for-azure-virtual-network-service-tags"></a>引入了对 Azure 虚拟网络服务标记的支持

**类型：** 新功能

在部分方案中，服务标记的自动化支持会就自动化服务的流量做出决定（允许或拒绝）。 若要了解详细信息，请参阅[文档](automation-hybrid-runbook-worker.md#service-tags)。

### <a name="enable-tls-12-support-for-azure-automation-service"></a>启用对 Azure 自动化服务的 TLS 1.2 支持

**类型：** 更改计划

Azure 自动化完全支持 TLS 1.2 和所有客户端调用（通过 webhook、DSC 节点和混合辅助角色）。 在客户实现标准化并完全迁移到 TLS 1.2 之前，仍支持 TLS 1.1 和 TLS 1.0 以实现与旧客户端的向后兼容性。

## <a name="january-2020"></a>2020 年 1 月

### <a name="introduced-public-preview-of-customer-managed-keys-for-azure-automation"></a>向 Azure 自动化引入了客户管理的密钥公共预览版

**类型：** 新功能

客户可使用自己管理的密钥来管理和保护 Azure 自动化资产的加密。 有关详细信息，请参阅[使用客户管理的密钥](automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)。

### <a name="retirement-of-azure-service-management-asm-rest-apis-for-azure-automation"></a>停用 Azure 自动化的 Azure 服务管理 (ASM) REST API

**类型：** 停用

Azure 自动化的 Azure 服务管理 (ASM) REST API 将在 2020 年 1 月 30 日之后停用，届时将不再受支持。 若要了解详细信息，请参阅[公告](https://azure.microsoft.com/updates/azure-automation-service-management-rest-apis-are-being-retired-april-30-2019/)。

## <a name="next-steps"></a>后续步骤

若要参与 Azure 自动化文档，请查看 [Docs 参与者指南](/contribute/)。
