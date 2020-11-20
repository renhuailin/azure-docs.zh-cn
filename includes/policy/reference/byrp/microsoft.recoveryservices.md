---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 11/20/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: e491c3a1f0364626a1efb5b81d4680e83c027a4a
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2020
ms.locfileid: "94992245"
---
|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应为虚拟机启用 Azure 备份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |启用 Azure 备份，确保对 Azure 虚拟机提供保护。 Azure 备份是一种安全且经济高效的 Azure 数据保护解决方案。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |
|[使用默认策略将具有给定标记的 Vm 上的备份配置到新的恢复服务保管库](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83644c87-93dd-49fe-bf9f-6aff8fd0834e) |通过在与虚拟机相同的位置和资源组中部署恢复服务保管库，来强制实施所有虚拟机的备份。 当组织中的不同应用程序团队分配了单独的资源组并需要管理自己的备份和还原时，执行此操作非常有用。 你可以选择包含包含指定标记的虚拟机来控制分配的作用域。 请参阅 [https://aka.ms/AzureVMAppCentricBackupIncludeTag](https://aka.ms/AzureVMAppCentricBackupIncludeTag) |deployIfNotExists |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachineApplicationCentricBackup_Backup_Deploy_WithTag.json) |
|[在同一位置的现有恢复服务保管库中为具有给定标记的 Vm 配置备份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F345fa903-145c-4fe1-8bcd-93ec2adccde8) |通过备份到虚拟机所在的同一位置和订阅中的现有中央恢复服务保管库，强制实施所有虚拟机的备份。 当组织中有一个中心团队管理订阅中所有资源的备份时，执行此操作非常有用。 你可以选择包含包含指定标记的虚拟机来控制分配的作用域。 请参阅 [https://aka.ms/AzureVMCentralBackupIncludeTag](https://aka.ms/AzureVMCentralBackupIncludeTag) |deployIfNotExists、auditIfNotExists、disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachineWithTag_Backup_Deploy.json) |
|[将不带给定标记的 Vm 的备份配置为具有默认策略的新恢复服务保管库](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F98d0b9f8-fd90-49c9-88e2-d3baf3b0dd86) |通过在与虚拟机相同的位置和资源组中部署恢复服务保管库，来强制实施所有虚拟机的备份。 当组织中的不同应用程序团队分配了单独的资源组并需要管理自己的备份和还原时，执行此操作非常有用。 你可以选择排除包含指定标记的虚拟机来控制分配的作用域。 请参阅 [https://aka.ms/AzureVMAppCentricBackupExcludeTag](https://aka.ms/AzureVMAppCentricBackupExcludeTag) |deployIfNotExists |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachineApplicationCentricBackup_Backup_Deploy_WithOutTag.json) |
|[在同一位置的现有恢复服务保管库中，将无给定标记的 Vm 的备份配置为](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09ce66bc-1220-4153-8104-e3f51c936913) |通过备份到虚拟机所在的同一位置和订阅中的现有中央恢复服务保管库，强制实施所有虚拟机的备份。 当组织中有一个中心团队管理订阅中所有资源的备份时，执行此操作非常有用。 你可以选择排除包含指定标记的虚拟机来控制分配的作用域。 请参阅 [https://aka.ms/AzureVMCentralBackupExcludeTag](https://aka.ms/AzureVMCentralBackupExcludeTag) |deployIfNotExists、auditIfNotExists、disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachineBackup_Backup_DeployIfNotExists.json) |
|[将恢复服务保管库的诊断设置部署到资源专有类别的 Log Analytics 工作区。](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc717fb0c-d118-4c43-ab3d-ece30ac81fb3) |部署恢复服务保管库的诊断设置，以便将数据流式传输到资源专有类别的 Log Analytics 工作区。 如果未启用任何资源专有类别，则会新建诊断设置。 |deployIfNotExists |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/EnableRecoveryServiceVaultDiagnosticSetting_Backup_DeployIfNotExist.json) |
