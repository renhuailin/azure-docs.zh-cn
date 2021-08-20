---
title: Azure 自动化中的新增功能
description: Azure 自动化每月都有重大更新。
services: automation
ms.subservice: ''
ms.topic: overview
ms.date: 07/27/2021
ms.custom: references_regions
ms.openlocfilehash: 6b91b9bb4e1bc871ecd8cb7909718329f5c5c014
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2021
ms.locfileid: "114729561"
---
# <a name="whats-new-in-azure-automation"></a>Azure 自动化有哪些新增功能？

Azure 自动化会不断改进。 为了让大家随时了解最新的开发成果，本文将提供以下方面的信息：

- 最新版本
- 已知问题
- Bug 修复

本页面每月更新，请不时回来查看。

## <a name="july-2021"></a>2021 年 7 月

### <a name="preview-support-for-user-assigned-managed-identities"></a>支持预览由用户分配的托管标识

**类型：** 新功能

Azure 自动化现在支持对 Azure 公有云、政府云和中国区域中的云作业和混合作业使用[用户分配的托管标识](automation-secure-asset-encryption.md)。 有关详细信息，请参阅[公告](https://azure.microsoft.com/updates/azure-automation-user-assigned-identities/)。

### <a name="general-availability-of-customer-managed-keys-for-azure-automation"></a>Azure 自动化的客户管理密钥的正式发布

**类型：** 新功能

客户可使用自己管理的密钥来管理和保护 Azure 自动化资产的加密。 由于引入了客户管理的密钥，你可以使用在 Azure Key Vault 中创建和管理的密钥，通过一个额外的加密层来补充默认加密。 这一额外的加密应有助于满足组织的法规或合规性需求。

有关详细信息，请参阅[使用客户管理的密钥](automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)。

## <a name="june-2021"></a>2021 年 6 月

### <a name="security-update-for-log-analytics-contributor-role"></a>Log Analytics 参与者角色的安全更新

**类型：** 更改计划

Microsoft 打算从 Log Analytics 参与者角色中删除自动化帐户权限。 目前，内置的 [Log Analytics 参与者](./automation-role-based-access-control.md#log-analytics-contributor)角色可以将权限提升为订阅[参与者](./../role-based-access-control/built-in-roles.md#contributor)角色。 由于自动化帐户运行方式帐户最初在订阅上配置了“参与者”权限，因此攻击者可以使用该帐户创建新的 runbook，并以订阅参与者身份执行代码。

由于存在这种安全风险，我们建议不要使用 Log Analytics 参与者角色来执行自动化作业。 请改为创建 Azure 自动化参与者自定义角色，并使用它执行与自动化帐户相关的操作。 有关实现步骤，请参阅[自定义 Azure 自动化参与者角色](./automation-role-based-access-control.md#custom-azure-automation-contributor-role)。

### <a name="support-for-automation-and-state-configuration-available-in-west-us-3"></a>在美国西部 3 区域中推出了对自动化和 State Configuration 的支持

**类型：** 新功能

有关详细信息，请参阅 [Azure 中的数据驻留](https://azure.microsoft.com/global-infrastructure/data-residency/)，并从下拉列表中选择你的地理位置。

## <a name="may-2021"></a>2021 年 5 月

### <a name="startstop-vms-during-off-hours-v1"></a>在空闲时间启动/停止 VM (v1)

**类型：** 更改计划

在空闲时间启动/停止 VM (v1) 的功能将于 2022 年 5 月 21 日弃用。 客户应该评估并规划迁移到启动/停止 VM v2（预览版）。 有关详细信息，请参阅[启动/停止 v2 概述](../azure-functions/start-stop-vms/overview.md)（预览版）。

## <a name="april-2021"></a>2021 年 4 月

### <a name="support-for-update-management-and-change-tracking"></a>支持更新管理和更改跟踪

**类型：** 新功能

已更新区域映射，以支持挪威东部、阿联酋北部、美国中北部、巴西南部和韩国中部区域的更新管理与更改跟踪。 有关详细信息，请参阅[支持的映射](./how-to/region-mappings.md#supported-mappings)。

### <a name="support-for-system-assigned-managed-identities"></a>支持系统分配的托管标识

**类型：** 新功能

Azure 自动化现在支持对 Azure 公有云与政府云区域中的云作业和混合作业使用[系统分配的托管标识](./automation-security-overview.md#managed-identities-preview)。 有关详细信息，请参阅[公告](https://azure.microsoft.com/updates/azure-automation-system-assigned-managed-identities/)。

## <a name="march-2021"></a>2021 年 3 月

### <a name="new-azure-automation-built-in-policies"></a>新的 Azure 自动化内置策略

**类型：** 新功能

Azure 自动化添加了五个新的内置策略：

- 自动化帐户应禁用公用网络访问，
- Azure 自动化帐户应使用客户管理的密钥来加密静态数据
- 将 Azure 自动化帐户配置为禁用公用网络访问
- 在 Azure 自动化帐户上配置专用终结点连接
- 应启用自动化帐户上的专用终结点连接。

有关详细信息，请参阅[策略参考](./policy-reference.md)。

### <a name="support-for-automation-and-state-configuration-declared-ga-in-south-india"></a>已在印度南部正式发布对自动化和 State Configuration 的支持声明

**类型：** 新功能

可在印度南部使用过程自动化和 State Configuration 功能。 有关详细信息，请参阅[公告](https://azure.microsoft.com/updates/azure-automation-in-south-india-region/)。

### <a name="support-for-automation-and-state-configuration-declared-ga-in-uk-west"></a>已在英国西部正式发布对自动化和 State Configuration 的支持声明

**类型：** 新功能

可在英国西部使用过程自动化和 State Configuration 功能。 有关详细信息，请阅读[公告](https://azure.microsoft.com/updates/azure-automation-in-uk-west-region/)。

### <a name="support-for-automation-and-state-configuration-declared-ga-in-uae-central"></a>已在阿联酋中部正式发布对自动化和 State Configuration 的支持声明

**类型：** 新功能

可在阿联酋中部使用过程自动化和 State Configuration 功能。 有关详细信息，请参阅[公告](https://azure.microsoft.com/updates/azure-automation-in-uae-central-region/)。

### <a name="support-for-automation-and-state-configuration-available-in-australia-central-2-norway-west-and-france-south"></a>在澳大利亚中部 2、挪威西部和法国南部区域推出了对自动化和 State Configuration 的支持

**类型：** 新功能

在[“数据驻留”页](https://azure.microsoft.com/global-infrastructure/data-residency/)上选择每个区域的地理位置以查看详细信息。

### <a name="new-scripts-added-for-installing-hybrid-worker-on-windows-and-linux"></a>添加了新的脚本，用于在 Windows 和 Linux 上安装混合辅助角色

**类型：** 新功能

在 Azure 自动化 [GitHub 存储库](https://github.com/azureautomation)中添加了两个新脚本，用于应对在 Windows 或 Linux 计算机上设置混合 Runbook 辅助角色的 Azure 自动化关键方案之一。 此脚本将创建一个新的 VM 或使用现有 VM，根据需要创建 Log Analytics 工作区，安装适用于 Windows 的 Log Analytics 代理或适用于 Linux 的 Log Analytics 代理，并将计算机注册到 Log Analytics 工作区。 Windows 脚本名为 Create Automation Windows HybridWorker，Linux 脚本名为 Create Automation Linux HybridWorker。 

### <a name="invoke-runbook-through-an-azure-resource-manager-template-webhook"></a>通过 Azure 资源管理器模板 webhook 调用 runbook

**类型：** 新功能

有关详细信息，请参阅[通过 ARM 模板使用 Webhook](./automation-webhooks.md#create-runbook-and-webhook-with-arm-template)。

### <a name="azure-update-management-now-supports-centos-8x-red-hat-enterprise-linux-server-8x-and-suse-linux-enterprise-server-15"></a>Azure 更新管理现在支持Centos 8.x、Red Hat Enterprise Linux Server 8.x 和 SUSE Linux Enterprise Server 15

**类型：** 新功能

有关更多详细信息，请参阅支持的 Linux 操作系统的[完整列表](./update-management/operating-system-requirements.md)。

### <a name="in-region-data-residency-support-for-brazil-south-and-south-east-asia"></a>巴西南部和东南亚的区域内数据驻留支持 

**类型：** 新功能

在除巴西南部和东南亚以外的所有区域中，Azure 自动化数据都存储在其他区域（Azure 配对区域），以提供业务连续性和灾难恢复 (BCDR)。 对于巴西和东南亚区域，我们现在将 Azure 自动化数据存储在同一区域中，以适应这些区域的数据驻留要求。 有关详细信息，请参阅 [Azure 自动化中的异地复制](./automation-managing-data.md#geo-replication-in-azure-automation)。

## <a name="february-2021"></a>2021 年 2 月

### <a name="support-for-automation-and-state-configuration-declared-ga-in-japan-west"></a>已在日本西部正式发布对自动化和 State Configuration 的支持声明

**类型：** 新功能

现可在日本西部区域使用自动化帐户和 State Configuration。 有关详细信息，请阅读[公告](https://azure.microsoft.com/updates/azure-automation-in-japan-west-region/)。

### <a name="introduced-custom-azure-policy-compliance-to-enforce-runbook-execution-on-hybrid-worker"></a>引入了自定义 Azure Policy 合规性，以对混合辅助角色强制执行 runbook

类型：新功能

可以使用新的 Azure Policy 合规性规则来允许创建作业、Webhook 和作业计划，使其仅在混合辅助角色组上运行。

### <a name="update-management-availability-in-east-us-france-central-and-north-europe-regions"></a>现可在美国东部、法国中部和北欧地区使用更新管理

**类型：** 新功能

在美国东部、法国中部和北欧地区提供自动化更新管理功能。 请参阅[受支持的区域映射](how-to/region-mappings.md)，获取反映此更改的文档更新。

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
