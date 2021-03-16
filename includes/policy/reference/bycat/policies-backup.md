---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/05/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 705146d0d54753faf54aabc8293dd8e79373897f
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2021
ms.locfileid: "102428995"
---
|名称<br /><sub>（Azure 门户）</sub> |说明 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[应为虚拟机启用 Azure 备份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |启用 Azure 备份，确保对 Azure 虚拟机提供保护。 Azure 备份是一种安全且经济高效的 Azure 数据保护解决方案。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |
|[\[预览\]：使用默认策略将具有给定标记的 VM 上的备份配置到新的恢复服务保管库](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83644c87-93dd-49fe-bf9f-6aff8fd0834e) |通过在与虚拟机相同的位置和资源组中部署恢复服务保管库，为所有虚拟机强制实施备份。 当组织中的不同应用程序团队拥有单独的资源组并且需要管理自己的备份和还原时，这样做非常有用。 可以选择包括包含指定标记的虚拟机，以控制分配范围。 请参阅 [https://aka.ms/AzureVMAppCentricBackupIncludeTag](https://aka.ms/AzureVMAppCentricBackupIncludeTag) |deployIfNotExists |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachineApplicationCentricBackup_Backup_Deploy_WithTag.json) |
|[\[预览\]：将具有给定标记的 VM 上的备份配置到同一位置的现有恢复服务保管库](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F345fa903-145c-4fe1-8bcd-93ec2adccde8) |通过将虚拟机备份到与虚拟机相同的位置和订阅中的现有中央恢复服务保管库，为所有虚拟机强制实施备份。 当组织中的中央团队管理订阅中所有资源的备份时，这样做非常有用。 可以选择包括包含指定标记的虚拟机，以控制分配范围。 请参阅 [https://aka.ms/AzureVMCentralBackupIncludeTag](https://aka.ms/AzureVMCentralBackupIncludeTag) |deployIfNotExists、auditIfNotExists、disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachineWithTag_Backup_Deploy.json) |
|[\[预览\]：使用默认策略将不带给定标记的 VM 上的备份配置到新的恢复服务保管库](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F98d0b9f8-fd90-49c9-88e2-d3baf3b0dd86) |通过在与虚拟机相同的位置和资源组中部署恢复服务保管库，为所有虚拟机强制实施备份。 当组织中的不同应用程序团队拥有单独的资源组并且需要管理自己的备份和还原时，这样做非常有用。 可以选择排除包含指定标记的虚拟机，以控制分配范围。 请参阅 [https://aka.ms/AzureVMAppCentricBackupExcludeTag](https://aka.ms/AzureVMAppCentricBackupExcludeTag) |deployIfNotExists |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachineApplicationCentricBackup_Backup_Deploy_WithOutTag.json) |
|[将不带给定标记的 VM 上的备份配置到同一位置的现有恢复服务保管库](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09ce66bc-1220-4153-8104-e3f51c936913) |通过将虚拟机备份到与虚拟机相同的位置和订阅中的现有中央恢复服务保管库，为所有虚拟机强制实施备份。 当组织中的中央团队管理订阅中所有资源的备份时，这样做非常有用。 可以选择排除包含指定标记的虚拟机，以控制分配范围。 请参阅 [https://aka.ms/AzureVMCentralBackupExcludeTag](https://aka.ms/AzureVMCentralBackupExcludeTag) |deployIfNotExists、auditIfNotExists、disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachineBackup_Backup_DeployIfNotExists.json) |
|[将恢复服务保管库的诊断设置部署到资源专有类别的 Log Analytics 工作区。](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc717fb0c-d118-4c43-ab3d-ece30ac81fb3) |部署恢复服务保管库的诊断设置，以便将数据流式传输到资源专有类别的 Log Analytics 工作区。 如果未启用任何资源专有类别，则会新建诊断设置。 |deployIfNotExists |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/EnableRecoveryServiceVaultDiagnosticSetting_Backup_DeployIfNotExist.json) |
