---
title: Azure 安全基准 V2 - 备份和恢复
description: Azure 安全基准 V2 备份和恢复
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 089cf521a7c5428833be340001c88b870c568a8f
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/13/2020
ms.locfileid: "97368879"
---
# <a name="security-control-v2-backup-and-recovery"></a>安全控制 V2：备份和恢复

备份和恢复包括用于确保在不同服务层执行、验证和保护数据和配置备份的控制措施。

## <a name="br-1-ensure-regular-automated-backups"></a>BR-1：确保定期执行自动备份

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID (s)  |
|--|--|--|--|
| BR-1 | 10.1 | CP-2、CP4、CP-6、CP-9 |

确保在发生意外事件后，备份系统和数据以保持业务连续性。 这应该由恢复点目标 (RPO) 和恢复时间目标 (RTO) 的任何目标定义。

启用 Azure 备份，配置备份源（例如 Azure VM、SQL Server、HANA 数据库或文件共享）以及所需的频率和保持期。  

为了提高保护级别，可启用异地冗余存储选项，将备份数据复制到次要区域，并使用跨区域还原进行恢复。

- [企业规模业务连续性和灾难恢复](/azure/cloud-adoption-framework/ready/enterprise-scale/business-continuity-and-disaster-recovery)

- [如何启用 Azure 备份](../../backup/index.yml)

- [如何启用跨区域还原](../../backup/backup-azure-arm-restore-vms.md#cross-region-restore)

**责任**：客户

客户安全利益干系人（[了解详细信息](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)）：

- [策略和标准](/azure/cloud-adoption-framework/organize/cloud-security-policy-standards)

- [安全体系结构](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [基础结构和终结点安全性](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [事件准备](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

## <a name="br-2-encrypt-backup-data"></a>BR-2：加密备份数据

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID (s)  |
|--|--|--|--|
| BR-2 | 10.2 | CP-9 |

确保备份不受攻击。 这应包括对备份进行加密，以防止丧失机密性。   

对于使用 Azure 备份的本地备份，请使用所提供的密码提供静态加密。 对于常规 Azure 服务备份，系统会使用 Azure 平台管理的密钥自动加密备份数据。 你可选择使用客户管理的密钥对备份进行加密。 在这种情况下，请确保 Key Vault 中客户管理的密钥也在备份范围内。 

在 Azure 备份中使用 Azure 基于角色的访问控制、Azure Key Vault 或其他资源，以保护备份和客户管理的密钥。 此外，可启用高级安全功能，要求在更改或删除备份之前进行 MFA。

- [Azure 备份中的安全功能概述](../../backup/security-overview.md)

- [使用客户托管密钥加密备份数据](../../backup/encryption-at-rest-with-cmk.md) 

- [如何在 Azure 中备份 Key Vault 密钥](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

- [可帮助保护混合备份免受攻击的安全功能](../../backup/backup-azure-security-feature.md#prevent-attacks)

**责任**：客户

客户安全利益干系人（[了解详细信息](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)）：

- [安全体系结构](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [基础结构和终结点安全性](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [事件准备](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

## <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3：验证所有备份，包括客户管理的密钥

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID (s)  |
|--|--|--|--|
| BR-3 | 10.3 | CP-4、CP-9 |

请定期在备份中执行数据还原。 请确保可以还原已备份的客户管理的密钥。

- [如何从 Azure 虚拟机备份恢复文件](../../backup/backup-azure-restore-files-from-vm.md)

- [如何在 Azure 中还原 Key Vault 密钥](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**责任**：客户

客户安全利益干系人（[了解详细信息](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)）：

- [事件准备](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [安全合规性管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4：减少密钥丢失风险

| Azure ID | CIS Controls v7.1 ID | NIST SP 800-53 r4 ID (s)  |
|--|--|--|--|
| BR-4 | 10.4 | CP-9 |

确保你有适当的措施来防止和恢复丢失的密钥。 在 Azure Key Vault 中启用软删除和清除保护，以防止意外删除或恶意删除密钥。  

- [如何在 Key Vault 中启用软删除和清除保护](../../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**责任**：客户

客户安全利益干系人（[了解详细信息](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)）：

- [安全体系结构](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [事件准备](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [数据安全性](/azure/cloud-adoption-framework/organize/cloud-security-data-security)