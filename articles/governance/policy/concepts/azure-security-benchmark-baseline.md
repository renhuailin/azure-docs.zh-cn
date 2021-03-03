---
title: Azure 策略的 azure 安全基线
description: Azure Policy 安全基准为实现 Azure 安全基准中指定的安全建议提供过程指南和资源。
author: msmbaldwin
ms.service: azure-policy
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 9916ad6d3b6cb1a63d34004915666226b7836490
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101740337"
---
# <a name="azure-security-baseline-for-azure-policy"></a>Azure 策略的 azure 安全基线

此安全基准将指南从 [Azure 安全基准](../../../security/benchmarks/overview.md)应用到 Azure Policy。 Azure 安全基准提供有关如何在 Azure 上保护云解决方案的建议。 内容按“合规性域”进行分组，“安全控件”由 Azure 安全基准定义，且相关指南适用于 Azure Policy 。 排除了不适用于 Azure Policy 的“控件”。 若要查看 Azure Policy 如何完全映射到 Azure 安全基准，请参阅[完整的 Azure Policy 安全基准映射文件](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)。

有关通过内置计划将 Azure 安全基准控制映射到内置策略定义的内容，请参阅合规 [性： Azure 安全基准](../samples/azure-security-benchmark.md)。

Azure Policy 使用术语“所有权”来代替“责任” 。 如需了解“所有权”的详细信息，请参阅 [Azure Policy 策略定义](./definition-structure.md#type)和[云中责任共享](../../../security/fundamentals/shared-responsibility.md)。

## <a name="logging-and-monitoring"></a>日志记录和监视

[有关详细信息，请参阅 *Azure 安全基线：* 日志记录和监视](../../../security/benchmarks/security-control-logging-monitoring.md)。

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：为 Azure 资源启用审核日志记录

**指导**：Azure Policy 使用自动启用的活动日志来包括事件源、日期、用户、时间戳、源地址、目标地址和其他有用的元素。

- [如何使用 Azure Monitor 收集平台日志和指标](/azure/azure-monitor/platform/diagnostic-settings)

- [了解 Azure 中的日志记录和不同的日志类型](/azure/azure-monitor/platform/platform-logs-overview)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="identity-and-access-control"></a>标识和访问控制

[有关详细信息，请参阅 *Azure 安全基线：* 标识和访问控制](../../../security/benchmarks/security-control-identity-access-control.md)。

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用专用管理帐户

**指南**：围绕专用管理帐户的使用创建标准操作程序。 使用 Azure 安全中心标识和访问管理来监视管理帐户的数量。 你还可以使用 [Azure Active Directory (Azure AD) Privileged Identity Management](../../../active-directory/privileged-identity-management/pim-configure.md) 特权角色或 [Azure 资源管理器](../../../azure-resource-manager/management/overview.md)来启用实时/刚好足够的访问解决方案。

**责任**：客户

**Azure 安全中心监视**： [azure 安全基准](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md) 是安全中心的默认策略计划，是 [安全中心建议](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)的基础。 安全中心会自动启用与此控件相关的 Azure 策略定义。 与此控件相关的警报可能需要 [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) 计划相关服务。

**Azure 策略内置定义-GuestConfiguration**：

[!INCLUDE [Resource Policy for Microsoft.GuestConfiguration 3.3](../../../../includes/policy/standards/asb/rp-controls/microsoft.guestconfiguration-3-3.md)]

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6：使用由 Azure 管理的安全工作站执行管理任务

**指南**：通过配置为登录和配置 Azure 资源的多重身份验证，使用特权访问工作站 (paw) 。

- [了解特权访问工作站](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [如何在 Azure 中启用多重身份验证](../../../active-directory/authentication/howto-mfa-getstarted.md)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="data-protection"></a>数据保护

[有关详细信息，请参阅 *Azure 安全基线：* 数据保护](../../../security/benchmarks/security-control-data-protection.md)。

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6：使用 Azure RBAC 控制对资源的访问 

**指导**：使用 Azure 基于角色的访问控制 (Azure RBAC) 来控制对 Azure Policy 的访问。

- [Azure Policy 中的 Azure RBAC 权限](https://docs.microsoft.com/azure/governance/policy/overview#azure-rbac-permissions-in-azure-policy)

- [如何配置 Azure RBAC](../../../role-based-access-control/role-assignments-portal.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：记录对关键 Azure 资源的更改并对此类更改发出警报

**指导**：Azure Policy 发生更改时使用 Azure Monitor 与活动日志创建警报。

- [如何针对 Azure 活动日志事件创建警报](/azure/azure-monitor/platform/alerts-activity-log)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="inventory-and-asset-management"></a>清单和资产管理

[有关详细信息，请参阅 *Azure 安全基线：* 清单和资产管理](../../../security/benchmarks/security-control-inventory-asset-management.md)。

### <a name="62-maintain-asset-metadata"></a>6.2：维护资产元数据

**指导**：将标记应用到 Azure资源，以便有条理地将元数据组织成某种分类。 使用 Azure Policy“修改”效果来报告并强制实施合规性和一致性标记管理。

- [教程：创建和管理策略](../tutorials/create-and-manage.md)

- [教程：管理标记治理](../tutorials/govern-tags.md)

**责任**：客户

**Azure 安全中心监视**：无

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4：定义并维护已批准的 Azure 资源的清单

**指导**：根据组织需要创建已批准的策略定义和策略分配的清单。

**责任**：客户

**Azure 安全中心监视**：无

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：监视未批准的 Azure 资源

**指导**：使用 Azure Policy 对可以在订阅中创建的资源类型施加限制。

- [如何配置和管理 Azure Policy](../tutorials/create-and-manage.md)

**责任**：客户

**Azure 安全中心监视**：无

## <a name="next-steps"></a>后续步骤

- 参阅 [Azure 安全基准 V2 概述](/azure/security/benchmarks/overview)
- 详细了解 [Azure 安全基线](/azure/security/benchmarks/security-baselines-overview)
