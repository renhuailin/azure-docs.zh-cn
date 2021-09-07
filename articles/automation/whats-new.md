---
title: Azure 自动化中的新增功能
description: Azure 自动化每月都有重大更新。
services: automation
ms.subservice: ''
ms.topic: overview
ms.date: 08/27/2021
ms.custom: references_regions
ms.openlocfilehash: 84644759a3f2f887662faae686814f032410266b
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123107926"
---
# <a name="whats-new-in-azure-automation"></a>Azure 自动化有哪些新增功能？

Azure 自动化会不断改进。 为了让大家随时了解最新的开发成果，本文将提供以下方面的信息：

- 最新版本
- 已知问题
- Bug 修复

本页面每月更新，请不时回来查看。 若要查找 6 个月之前的项目，可以在 [Azure 自动化新增功能存档](whats-new-archive.md)中找到它们。

## <a name="august-2021"></a>2021 年 8 月

### <a name="azure-policy-guest-configuration"></a>Azure Policy 来宾配置

**类型：** 更改计划

客户应评估并计划从 Azure 自动化 State Configuration 迁移到 Azure Policy 来宾配置。 有关详细信息，请参阅 [Azure Policy 的来宾配置](../governance/policy/concepts/guest-configuration.md)。

## <a name="july-2021"></a>2021 年 7 月

### <a name="preview-support-for-user-assigned-managed-identity"></a>支持预览由用户分配的托管标识

**类型：** 新功能

Azure 自动化现在支持对 Azure 全球、Azure 政府和 Azure 中国区域中的云作业使用[用户分配的托管标识](automation-secure-asset-encryption.md)。 有关详细信息，请参阅[公告](https://azure.microsoft.com/updates/azure-automation-user-assigned-identities/)。

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

区域映射已更新，现支持挪威东部、阿联酋北部、美国中北部、巴西南部和韩国中部区域的更新管理与更改跟踪。 有关详细信息，请参阅[支持的映射](./how-to/region-mappings.md#supported-mappings)。

### <a name="support-for-system-assigned-managed-identities"></a>支持系统分配的托管标识

**类型：** 新功能

Azure 自动化现在支持对 Azure 全球与 Azure 政府区域中的云作业和混合作业使用[系统分配的托管标识](./automation-security-overview.md#managed-identities-preview)。 有关详细信息，请参阅[公告](https://azure.microsoft.com/updates/azure-automation-system-assigned-managed-identities/)。

## <a name="march-2021"></a>2021 年 3 月

### <a name="new-azure-automation-built-in-policy-definitions"></a>新的 Azure 自动化内置策略定义

**类型：** 新功能

Azure 自动化新增了五个内置策略：

- 自动化帐户应禁用公用网络访问，
- Azure 自动化帐户应使用客户管理的密钥来加密静态数据
- 将 Azure 自动化帐户配置为禁用公用网络访问
- 在 Azure 自动化帐户上配置专用终结点连接
- 应启用自动化帐户上的专用终结点连接。

有关详细信息，请参阅 [Azure Policy 策略参考](./policy-reference.md)。

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

## <a name="next-steps"></a>后续步骤

若要参与 Azure 自动化文档，请查看 [Docs 参与者指南](/contribute/)。
