---
title: 其他文档和资源
titleSuffix: Azure Australia
description: 有关在 Azure 中安全运营的澳大利亚政府机构的其他文档、教程或参考。
author: emilyre
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: yvettep
ms.openlocfilehash: d13ae772aaef25997365915465ed544ea4002964
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2021
ms.locfileid: "117029094"
---
# <a name="additional-documentation-and-resources-by-focus-area"></a>按照关注领域分类的其他文档和资源

此资源库包含其他链接和引用，这些链接和引用与在 Azure 澳大利亚中安全实现澳大利亚政府工作负荷相关。

## <a name="general-references-for-all-security-and-governance-in-azure-australia"></a>Azure 澳大利亚中所有安全和治理的一般参考

* [Microsoft 服务信任门户中的澳大利亚页面](https://aka.ms/au-irap)
* [Microsoft 信任中心 CCSL 页面](https://www.microsoft.com/trustcenter/compliance/ccsl)
* [受保护的 Azure 安全性与合规性蓝图](https://aka.ms/au-protected)
* [Microsoft Azure 中的租户隔离](../security/fundamentals/isolation-choices.md)
* [澳大利亚信息安全手册](https://www.cyber.gov.au/ism)
* [澳大利亚网络安全中心 (ACSC) 认证云列表](https://www.cyber.gov.au/irap/cloud-services)

## <a name="azure-key-vault"></a>Azure Key Vault

* [Azure 密钥保管库概述](../key-vault/general/overview.md)
* [关于键、密钥和证书](../key-vault/general/about-keys-secrets-certificates.md)
* [配置 Azure Key Vault 防火墙和虚拟网络](../key-vault/general/network-security.md)
* [保护对密钥保管库的访问](../key-vault/general/security-features.md)
* [Azure 静态数据加密](../security/fundamentals/encryption-atrest.md)
* [如何将 Azure 密钥保管库与通过 .NET 编写的 Azure Windows 虚拟机配合使用](../key-vault/general/tutorial-net-virtual-machine.md)
* [Azure Key Vault 托管存储帐户 - PowerShell](../key-vault/general/tutorial-net-virtual-machine.md)
* [设置密钥轮换和审核](../key-vault/secrets/tutorial-rotation-dual.md)

## <a name="identity-federation"></a>联合身份验证

* [Azure AD Connect - 安装指南](../active-directory/hybrid/how-to-connect-install-roadmap.md)
* [密码写回](../active-directory/authentication/concept-sspr-writeback.md)
* [安装和运行 IDFix 工具](/office365/enterprise/install-and-run-idfix)
* [Azure AD UPN 填充](../active-directory/hybrid/plan-connect-userprincipalname.md)
* [Azure AD Connect - 同步的属性](../active-directory/hybrid/reference-connect-sync-attributes-synchronized.md)
* [Azure AD Connect - 最佳实践配置指南](../active-directory/hybrid/how-to-connect-sync-best-practices-changing-default-configuration.md)
* [Azure AD Connect - 用户登录选项](../active-directory/hybrid/plan-connect-user-signin.md)
* [Azure AD Connect 和联合身份验证](../active-directory/hybrid/how-to-connect-fed-whatis.md)
* [直通身份验证文档](../active-directory/hybrid/how-to-connect-pta.md)
* [部署 Azure AD 多重身份验证](../active-directory/authentication/howto-mfa-getstarted.md)
* [Azure Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md)

## <a name="azure-backup-and-azure-site-recovery"></a>Azure 备份和 Azure Site Recovery

* [Azure 备份简介](../backup/backup-overview.md)
* [Azure 备份概述](../backup/backup-overview.md)
* [Azure Site Recovery 概述](../site-recovery/site-recovery-overview.md)
* [Azure 治理](../governance/index.yml)
* [Azure 配对区域](../best-practices-availability-paired-regions.md)
* [Azure Policy](../governance/policy/overview.md)
* [Azure 存储服务加密](../storage/common/storage-service-encryption.md)
* [Azure 备份教程](../backup/index.yml)
* [Azure Site Recovery 教程](../site-recovery/index.yml)

## <a name="azure-role-based-access-control-azure-rbac-and-privileged-identity-management-pim"></a>Azure 基于角色的访问控制 (Azure RBAC) 和 Privileged Identity Management (PIM)

* [Azure RBAC 概述](../role-based-access-control/overview.md)
* [Azure Privileged Identify Management 概述](../active-directory/privileged-identity-management/pim-configure.md)
* [Azure 管理组概述](../governance/management-groups/index.yml)
* [Azure 标识和访问控制最佳实践](../security/fundamentals/identity-management-best-practices.md)
* [管理 Azure AD 组](../active-directory/fundamentals/active-directory-manage-groups.md)
* [混合标识](../active-directory/hybrid/whatis-hybrid-identity.md)
* [Azure 自定义角色](../role-based-access-control/custom-roles.md)
* [Azure 内置角色](../role-based-access-control/built-in-roles.md)
* [在混合云环境中保护特权访问](../active-directory/roles/security-planning.md)
* [Azure 企业基架](/azure/architecture/cloud-adoption/appendix/azure-scaffold)

## <a name="system-monitoring-for-security"></a>系统监视安全性

* [Azure 治理](../governance/index.yml)
* [Azure 安全最佳实践](../security/fundamentals/best-practices-and-patterns.md)
* [Platforms and features supported by Azure Security Center](../security-center/security-center-os-coverage.md)（Azure 安全中心支持的平台和功能）
* [Azure 活动日志](../azure-monitor/essentials/platform-logs-overview.md)
* [Azure 诊断日志](../azure-monitor/essentials/platform-logs-overview.md)
* [Azure 安全中心警报](../security-center/security-center-managing-and-responding-alerts.md)
* [Azure 日志集成](/previous-versions/azure/security/fundamentals/azure-log-integration-overview)
* [在 Azure Monitor 中分析日志数据](../azure-monitor/logs/log-query-overview.md)
* [将 Azure Monitor 日志流式传输至事件中心](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md)
* [事件中心安全性和身份验证](../event-hubs/authenticate-shared-access-signature.md)

## <a name="azure-policy-and-azure-blueprints"></a>Azure Policy 和 Azure 蓝图

* [Azure Policy 概述](../governance/policy/overview.md)
* [Azure 蓝图概述](https://azure.microsoft.com/services/blueprints/)
* [Azure Policy 示例](../governance/policy/samples/index.md)
* [Azure Policy 示例存储库](https://github.com/Azure/azure-policy)
* [Azure Policy 定义结构](../governance/policy/concepts/definition-structure.md)
* [Azure Policy 效果](../governance/policy/concepts/effects.md)
* [Azure 治理](../governance/index.yml)
* [Azure 管理组](../governance/management-groups/index.yml)
* [Azure 基于角色的访问控制 (Azure RBAC)](../role-based-access-control/overview.md)

## <a name="next-steps"></a>后续步骤

登录到 [Azure 门户](https://portal.azure.com)并开始在 Azure 澳大利亚中安全地配置您的资源。