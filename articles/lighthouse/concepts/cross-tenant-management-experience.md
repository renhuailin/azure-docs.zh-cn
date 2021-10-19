---
title: 跨租户管理体验
description: Azure Lighthouse 在许多 Azure 服务中启用和增强跨租户体验。
ms.date: 09/30/2021
ms.topic: conceptual
ms.openlocfilehash: 90ff0396a467f3137d6501ac34cf707577e43bdf
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129714782"
---
# <a name="cross-tenant-management-experiences"></a>跨租户管理体验

作为服务提供商，可以使用 [Azure Lighthouse](../overview.md) 从自己的 Azure Active Directory (Azure AD) 租户内管理多个客户的资源。 可以使用 [Azure 委托资源管理](../concepts/architecture.md)跨托管租户执行许多任务和服务。

> [!TIP]
> 还可在[具有多个其自己的 Azure AD 租户的企业中](enterprise.md)使用 Azure Lighthouse，以简化跨租户管理。

## <a name="understanding-tenants-and-delegation"></a>了解租户和委托

Azure AD 租户是组织的表示形式。 它是组织通过注册 Azure、Microsoft 365 或其他服务与 Microsoft 建立关系时接收的 Azure AD 的专用实例。 每个 Azure AD 租户都是独特的，独立于其他 Azure AD 租户，且具有其自己的租户 ID (GUID)。 有关详细信息，请参阅[什么是 Azure Active Directory？](../../active-directory/fundamentals/active-directory-whatis.md)

通常，为管理客户的 Azure 资源，服务提供商必须使用与该客户的租户相关联的帐户登录 Azure 门户，要求客户租户中的管理员为该服务提供商创建和管理用户帐户。

使用 Azure Lighthouse，加入过程会指定服务提供商租户中能够处理客户租户中的委托订阅和资源组的用户。 然后，这些用户可以使用自己的凭据登录到 Azure 门户。 在 Microsoft Azure 门户中，这些用户可以管理其有权访问的所有客户的资源。 为此，可以访问 Microsoft Azure 门户中的[我的客户](../how-to/view-manage-customers.md)页，或直接在该客户订阅的上下文中工作（在 Azure 门户中或通过 API）。

Azure Lighthouse 可更灵活地管理多个客户的资源，而无需登录到不同租户中的不同帐户。 例如，服务提供商可能有两个客户，各自有不同的职责和访问级别。 使用 Azure Lighthouse，授权用户可以登录到服务提供商的租户来访问这些资源。

![图中显示通过一个服务提供商租户管理的客户资源。](../media/azure-delegated-resource-management-service-provider-tenant.jpg)

## <a name="apis-and-management-tool-support"></a>API 和管理工具支持

可以直接在门户中对委派资源执行管理任务，也可以使用 API 和管理工具（如 Azure CLI 和 Azure PowerShell）对委派资源执行管理任务。 在处理委托的资源时，只要跨租户管理支持该功能，并且用户具有相应的权限，可以使用所有现有 API。

默认情况下，Azure PowerShell [Get-AzSubscription cmdlet](/powershell/module/Az.Accounts/Get-AzSubscription) 将显示管理租户的 `TenantId`。 可以对每个订阅使用 `HomeTenantId` 和 `ManagedByTenantIds` 属性，从而确定返回的订阅是属于托管租户还是属于管理租户。

同样，Azure CLI 命令（例如 [az account list](/cli/azure/account#az_account_list)）会显示 `homeTenantId` 和 `managedByTenants` 属性。 如果在使用 Azure CLI 时看不到这些值，请尝试通过先运行 `az account clear` 再运行 `az login --identity` 来清除缓存。

在 Azure REST API 中，[Subscriptions - Get](/rest/api/resources/subscriptions/get) 和 [Subscriptions - List](/rest/api/resources/subscriptions/list) 命令包括 `ManagedByTenant`。

> [!NOTE]
> 除了与 Azure Lighthouse 相关的租户信息之外，这些 API 显示的租户还可能反映 Azure Databricks 或 Azure 托管应用程序的合作伙伴租户。

我们还提供了专用于执行 Azure Lighthouse 任务的 API。 有关详细信息，请参阅“参考”部分。

## <a name="enhanced-services-and-scenarios"></a>增强的服务和方案

大多数任务和服务都可对跨托管租户的委托资源执行。 下面是可特别有效进行跨租户管理的一些关键方案。

[Azure Arc](../../azure-arc/index.yml)：

- 大规模管理混合服务器 - [启用了 Azure Arc 的服务器](../../azure-arc/servers/overview.md)：
  - [管理 Azure 外部连接到 Azure 中的委托订阅和/或资源组的 Windows Server 或 Linux 计算机](../../azure-arc/servers/onboard-portal.md)
  - 使用 Azure 构造（如Azure Policy 和标记）管理连接的计算机
  - 确保在客户的混合环境上应用一组相同的策略
  - 使用 Azure 安全中心监视客户混合环境的符合性
- 大规模管理混合 Kubernetes 群集 - [启用了 Azure Arc 的 Kubernetes（预览版）](../../azure-arc/kubernetes/overview.md)：
  - [管理连接到 Azure 中的委托订阅和/或资源组的 Kubernetes 群集](../../azure-arc/kubernetes/quickstart-connect-cluster.md)
  - 对连接的群集[使用 GitOps](../../azure-arc/kubernetes/tutorial-use-gitops-connected-cluster.md)
  - 跨连接的群集强制实施策略

[Azure 自动化](../../automation/index.yml)：

- 使用自动化帐户来访问和使用委托的资源

[Azure 备份](../../backup/index.yml)：

- 从[本地工作负荷、Azure VM、Azure 文件共享等](../..//backup/backup-overview.md#what-can-i-back-up)备份和还原客户数据
- 查看[备份中心](../../backup/backup-center-overview.md)中所有委托的客户资源的数据
- 使用[备份资源管理器](../../backup/monitor-azure-backup-with-backup-explorer.md)可以查看备份项（包括尚未配置用于备份的 Azure 资源）的操作信息以及委托订阅的监视信息（作业和警报）。 备份资源管理器当前仅可用于 Azure VM 数据。
- 跨委托订阅使用[备份报告](../../backup/configure-reports.md)来跟踪历史趋势、分析备份存储消耗，以及审核备份和还原。

[Azure 蓝图](../../governance/blueprints/index.yml)：

- 使用 Azure 蓝图协调资源模板和其他项目的部署（需要[其他访问权限](https://www.wesleyhaakman.org/preparing-azure-lighthouse-customer-subscriptions-for-azure-blueprints/)才能准备客户订阅）

[Azure 成本管理 + 计费](../../cost-management-billing/index.yml)：

- 通过管理租户，CSP 合作伙伴可以查看、管理和分析 Azure 计划下的客户的税前消耗成本（不包含购买）。 该成本将基于零售价和合作伙伴对客户订阅所拥有的 Azure 基于角色的访问控制 (Azure RBAC) 访问权限。 目前，可以根据 Azure RBAC 访问权限，查看每个单独客户订阅的零售费率消耗成本。

[Azure Key Vault](../../key-vault/general/index.yml)：

- 在客户租户中创建 Key Vault
- 使用托管标识在客户租户中创建 Key Vault

[Azure Kubernetes 服务 (AKS)](../../aks/index.yml)：

- 管理托管的 Kubernetes 环境并部署和管理客户租户中的容器化应用程序
- 部署和管理客户租户中的群集
- 使用用于容器的 Azure Monitor 跨客户租户监视性能

[Azure Migrate](../../migrate/index.yml)：

- 在客户租户中创建迁移项目并迁移 VM

[Azure Monitor](../../azure-monitor/index.yml)：

- 查看委托订阅的警报，并能够查看并刷新所有订阅的警报
- 查看委派订阅的活动日志详细信息
- [日志分析](../../azure-monitor/logs/service-providers.md)：从多个租户中的远程工作区查询数据（请注意，必须在同一租户中创建用于访问客户租户中工作区数据的自动化帐户）
- 在客户租户中创建、查看和管理[指标警报](../../azure-monitor/alerts/alerts-metric.md)、[日志警报](../../azure-monitor/alerts/alerts-log.md)和[活动日志警报](../../azure-monitor/alerts/alerts-activity-log.md)
- 在客户租户中创建警报，通过 Webhook 在管理租户中触发自动化，例如 Azure Automation Runbook 或 Azure Functions
- 在客户租户中创建的工作区中创建[诊断设置](../..//azure-monitor/essentials/diagnostic-settings.md)，以便将资源日志发送到管理租户中的工作区
- 对于 SAP 工作负荷，[使用跨客户租户的聚合视图监视 SAP 解决方案指标](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/using-azure-lighthouse-and-azure-monitor-for-sap-solutions-to/ba-p/1537293)

[Azure 网络](../../networking/fundamentals/networking-overview.md)：

- 在托管租户中部署和管理 [Azure 虚拟网络](../../virtual-network/index.yml)和虚拟网络接口卡 (vNIC)
- 部署和配置 [Azure 防火墙](../../firewall/overview.md)，以保护客户的虚拟网络资源
- 管理 [Azure 虚拟 WAN](../../virtual-wan/virtual-wan-about.md)、[ExpressRoute](../../expressroute/expressroute-introduction.md) 和 [VPN 网关](../../vpn-gateway/vpn-gateway-about-vpngateways.md)等连接服务
- 使用 Azure Lighthouse 支持 [Azure 网络 MSP 计划](../../networking/networking-partners-msp.md)的关键方案

[Azure Policy](../../governance/policy/index.yml)：

- 在委托订阅中创建和编辑策略定义
- 跨多个租户部署策略定义和策略分配
- 在委托订阅中分配客户定义的策略定义
- 客户将看到由服务提供商和自己创建的策略
- 可以[修正 deployIfNotExists 或修改托管租户内的分配](../how-to/deploy-policy-remediation.md)
- 请注意，当前不支持查看客户租户中不符合资源的符合性详细信息

[Azure Resource Graph](../../governance/resource-graph/index.yml)：

- 现在，在返回的查询结果中包含租户 ID，以便确定订阅是否属于托管租户

[Azure 安全中心](../../security-center/index.yml)：

- 跨租户可见性
  - 对是否符合安全策略进行监视，确保安全措施涵盖所有租户的资源
  - 单个视图中跨多个租户的连续合规性监视
  - 通过安全分数计算监视、会审可操作安全建议，并设置其优先级
- 跨租户安全状况管理
  - 管理安全策略
  - 对不符合可操作安全建议的资源执行操作
  - 收集并存储安全相关数据
- 跨租户威胁检测和保护
  - 跨租户资源检测威胁
  - 应用高级威胁防护控制（如实时 (JIT) VM 访问）
  - 通过自适应网络强化来强化网络安全组配置
  - 通过自适应应用程序控制，确保服务器仅运行适当的应用程序和进程
  - 运用文件完整性监视 (FIM) 监视对重要文件和注册表项的更改
- 请注意，必须将整个订阅委托给管理租户；委托资源组不支持 Azure 安全中心方案

[Azure Sentinel](../../sentinel/multiple-tenants-service-providers.md)：

- 管理[客户租户中的](../../sentinel/multiple-tenants-service-providers.md) Azure Sentinel 资源
- [跨多个租户跟踪攻击并查看安全警报](https://techcommunity.microsoft.com/t5/azure-sentinel/using-azure-lighthouse-and-azure-sentinel-to-monitor-across/ba-p/1043899)
- 跨分布在租户中的多个 Azure Sentinel 工作区[查看事件](../../sentinel/multiple-workspace-view.md)

[Azure 服务运行状况](../../service-health/index.yml)：

- 通过 Azure 资源运行状况监视客户资源的运行状况
- 跟踪客户使用的 Azure 服务的运行状况

[Azure Site Recovery](../../site-recovery/index.yml)：

- 为客户租户中的 Azure 虚拟机管理灾难恢复选项（请注意，不能使用 `RunAs` 帐户复制 VM 扩展）

[Azure 虚拟机](../../virtual-machines/index.yml)：

- 使用虚拟机扩展在 Azure VM 上提供部署后配置和自动化任务
- 使用启动诊断对 Azure VM 进行故障排除
- 使用串行控制台访问 VM
- 通过使用[通过策略管理的标识](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/create-keyvault-secret)，将 VM 与 Azure Key Vault 集成以使用密码、机密或加密密钥进行磁盘加密，确保机密存储在托管租户的 Key Vault 中
- 请注意，不能使用 Azure Active Directory 远程登录到 VM

支持请求：

- 从 Azure 门户中的 [**帮助 + 支持** 中，打开对委托资源的支持请求](../../azure-portal/supportability/how-to-create-azure-support-request.md#getting-started)（选择对委托范围可用的支持计划）
- 使用 [Azure 配额 API](/rest/api/reserved-vm-instances/quotaapi) 查看和管理委托的客户资源的 Azure 服务配额

## <a name="current-limitations"></a>当前限制

对于所有方案，都请注意以下当前限制：

- 可以使用 Azure Lighthouse 执行 Azure 资源管理器处理的请求。 这些请求的操作 URI 都以 `https://management.azure.com` 开头。 但是，Azure Lighthouse 不支持由资源类型的实例处理的请求（如 Key Vault 机密访问或存储数据访问）。 这些请求的操作 URI 通常以实例特有的地址开头，例如 `https://myaccount.blob.core.windows.net` 或 `https://mykeyvault.vault.azure.net/`。 后者通常也是数据操作，而不是管理操作。
- 角色分配必须使用 [Azure 内置角色](../../role-based-access-control/built-in-roles.md)。 除了“所有者”或具有 [`DataActions`](../../role-based-access-control/role-definitions.md#dataactions) 权限的任何内置角色之外，Azure Lighthouse 当前支持其他所有内置角色。 仅在[向托管标识分配角色](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant)时才支持使用用户访问管理员角色。  不支持自定义角色和[经典订阅管理员角色](../../role-based-access-control/classic-administrators.md)。
- 尽管你可以加入使用 Azure Databricks 的订阅，但管理租户中的用户目前无法在委托订阅上启动 Azure Databricks 工作区。
- 虽然可以加入具有资源锁的订阅和资源组，但这些锁不会阻止管理租户中的用户执行操作。 用于保护系统管理资源（例如由 Azure 托管应用程序或 Azure 蓝图创建的资源）的[拒绝分配](../../role-based-access-control/deny-assignments.md)（系统分配的拒绝分配）会阻止管理租户中的用户对这些资源进行操作；但是，目前客户租户中的用户无法创建自己的拒绝分配（用户分配的拒绝分配）。
- 不支持跨[国家云](../../active-directory/develop/authentication-national-cloud.md)和 Azure 公有云或跨两个不同的国家云进行订阅委托。

## <a name="next-steps"></a>后续步骤

- 要将客户加入 Azure Lighthouse，可以[使用 Azure 资源管理器模板](../how-to/onboard-customer.md)，或[将专用或公共托管服务发布到 Azure 市场](../how-to/publish-managed-services-offers.md)。
- 在 Microsoft Azure 门户中转到“我的客户”，以[查看和管理客户](../how-to/view-manage-customers.md)。
