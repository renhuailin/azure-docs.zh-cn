---
title: 面向服务提供商的 Azure Monitor 日志 | Microsoft Docs
description: Azure Monitor 日志可以帮助托管服务提供商 (MSP)、大型企业、独立软件供应商 (ISV) 和主机托管服务提供商管理和监视客户本地或云基础结构中的服务器。
ms.topic: conceptual
author: MeirMen
ms.author: meirm
ms.date: 02/03/2020
ms.openlocfilehash: d2e0dcb7d973a4e4b2b6f4438aab5f08187ae44b
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111953106"
---
# <a name="azure-monitor-logs-for-service-providers"></a>面向服务提供商的 Azure Monitor 日志

Azure Monitor 中的 Log Analytics 工作区可以帮助托管服务提供商 (MSP)、大型企业、独立软件供应商 (ISV) 和主机托管服务提供商管理和监视客户本地或云基础结构中的服务器。

大型企业与服务提供商有许多相似之处，特别是当有一个集中式 IT 团队负责管理许多不同业务部门的 IT 时。 为了简单起见，本文档使用术语 *服务提供商*，但同样的功能也可用于企业和其他客户。

对于加入[云解决方案提供商 (CSP)](https://partner.microsoft.com/membership/cloud-solution-provider) 计划的合作伙伴和服务提供商，Azure Monitor 中的 Log Analytics 是 Azure CSP 订阅中可用的 Azure 服务之一。

服务提供商也可以使用 Azure Monitor 中的 Log Analytics，通过 [Azure Lighthouse](../../lighthouse/overview.md)中的 Azure 委托资源管理功能来管理客户资源。

## <a name="architectures-for-service-providers"></a>适用于服务提供商的体系结构

借助 Log Analytics 工作区，管理员可控制[日志](../logs/data-platform-logs.md)数据的流动和隔离，并创建满足其特定业务需求的体系结构。 [此文](../logs/design-logs-deployment.md)介绍了工作区的设计、部署和迁移注意事项，[管理访问权限](../logs/manage-access.md)一文讨论了如何应用和管理日志数据的权限。 服务提供商有其他注意事项。

对于 Log Analytics 工作区，有三种可能的体系结构适用于服务提供商：

### <a name="1-distributed---logs-are-stored-in-workspaces-located-in-the-customers-tenant"></a>1.分布式：日志存储在位于客户租户中的工作区内

在此体系结构中，工作区部署在客户的租户中，此租户用于该客户的所有日志。

服务提供商管理员可通过两种方式访问客户租户中的 Log Analytics 工作区：

- 客户可将服务提供商的个人用户添加为 [Azure Active Directory 来宾用户 (B2B)](../../active-directory/external-identities/what-is-b2b.md)。 服务提供商管理员必须在 Azure 门户中登录到每个客户的目录才能访问这些工作区。 这还需要客户管理每个服务提供商管理员的个人访问权限。
- 为了获得更好的可伸缩性和灵活性，服务提供商可以使用 [Azure Lighthouse](../../lighthouse/overview.md) 来访问客户的租户。 借助此方法，服务提供商租户的 Azure AD 用户组中将包含服务提供商管理员，且在载入期间将为此组授予对每个客户的访问权限。 然后，这些管理员可以从自己的服务提供商租户访问每个客户的工作区，而无需单独登录到每个客户的租户。 以这种方式访问客户的 Log Analytics 工作区资源可以减少客户所需的工作，并且可以通过 [Azure Monitor 工作簿](../visualize/workbooks-overview.md)等工具更轻松地收集和分析由同一服务提供商管理的多个客户的数据。 有关详细信息，请参阅[大规模监视客户资源](../../lighthouse/how-to/monitor-at-scale.md)。

分布式体系结构的优势是：

* 客户可以通过 [Azure 委派资源管理](../../lighthouse/concepts/architecture.md)来确认特定级别的权限，也可以使用自己的 [Azure 基于角色的访问控制 (Azure RBAC)](../../role-based-access-control/overview.md) 来管理对日志的访问权限。
* 可从所有类型的资源中收集日志，而不仅仅是从基于代理的 VM 数据收集。 例如，Azure 审核日志。
* 每个客户都可以为其工作区设置不同的设置，例如保留期和数据上限。
* 在客户之间进行隔离以遵守监管和合规要求。
* 每个工作区的费用将计入客户的订阅中。

分布式体系结构的劣势是：

* 使用 Azure Monitor 工作簿之类的工具集中可视化和分析[客户租户中](cross-workspace-query.md)的数据可能会导致体验变慢，尤其是分析 50 个以上的工作区的数据时。
* 如果未为客户完成 Azure 委托资源管理的加入，则必须在客户目录中预配服务提供商管理员，而服务提供商难以同时管理大量的客户租户。

### <a name="2-central---logs-are-stored-in-a-workspace-located-in-the-service-provider-tenant"></a>2.集中式 - 日志存储在位于服务提供商租户中的工作区内

在此体系结构中，日志不会存储在客户的租户中，而是仅存储在服务提供商的某个订阅内的中心位置。 客户 VM 上安装的代理配置为使用工作区 ID 和密钥将其日志发送到此工作区。

集中式体系结构的优势是：

* 可轻松管理大量客户并将其集成到各种后端系统。
* 服务提供商对日志和各种项目（例如函数和保存的查询）拥有完全所有权。
* 服务提供商可以对其所有客户执行分析。

集中式体系结构的劣势是：

* 此体系结构仅适用于基于代理的 VM 数据，不涵盖 PaaS、SaaS 和 Azure 结构数据源。
* 将客户合并到单个工作区时，可能很难分隔客户之间的数据。 唯一效果不错的方法是使用计算机的完全限定域名 (FQDN) 或通过 Azure 订阅 ID 进行分隔。
* 来自所有客户的全部数据都将存储在具有单独帐单和相同保留期及配置设置的同一区域中。
* Azure 结构及 Azure 诊断和 Azure 审核日志等 PaaS 服务要求工作区与资源位于同一租户中，因此它们无法将日志发送到中心工作区。
* 来自所有客户的全部 VM 代理都将使用相同的工作区 ID 和密钥对中心工作区进行身份验证。 无法在不干扰其他客户的情况下阻止来自特定客户的日志。

### <a name="3-hybrid---logs-are-stored-in-workspace-located-in-the-customers-tenant-and-some-of-them-are-pulled-to-a-central-location"></a>3.混合式：日志存储在位于客户租户中的工作区内，且其中一部分日志会被提取到中心位置。

第三种体系结构混合使用两个选项。 它以第一种分布式体系结构为基础，其中日志对于每个客户而言位于本地，但通过某种机制创建了日志的中心存储库。 一部分日志被提取到中心位置用于报告和分析。 此部分可以是少量数据类型，也可以是每日统计数据等活动摘要。

在中心位置实现日志有两种选择：

1. 中心工作区：服务提供商可以在其租户中创建工作区，并使用利用[查询 API](https://dev.loganalytics.io/) 和[数据收集 API](../logs/data-collector-api.md) 的脚本将来自各个工作区的数据提取到此中心位置。 脚本之外的另一种选择是使用 [Azure 逻辑应用](../../logic-apps/logic-apps-overview.md)。

2. 使用 Power BI 作为中心位置：当各个工作区利用 Log Analytics 工作区和 [Power BI](../visualize/powerbi.md) 之间的集成将数据导出到 Power BI 时，Power BI 可以充当中心位置。

## <a name="next-steps"></a>后续步骤

* 使用[Resource Manager 模板](../logs/resource-manager-workspace.md)自动执行创建和配置工作区

* 使用 [PowerShell](../logs/powershell-workspace-configuration.md) 自动创建工作区

* 使用[警报](../alerts/alerts-overview.md)以便与现有系统集成

* 使用 [Power BI](../visualize/powerbi.md) 生成摘要报告

* 将客户载入到 [Azure 委派资源管理](../../lighthouse/concepts/architecture.md)。