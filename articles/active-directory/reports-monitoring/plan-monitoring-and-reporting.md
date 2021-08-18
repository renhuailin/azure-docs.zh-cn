---
title: 计划报表和监视部署 - Azure AD
description: 介绍如何计划和执行报表和监视的实现。
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: baselden
ms.reviewer: plenzke
ms.collection: M365-identity-device-management
ms.openlocfilehash: f831acacfd1aaf2248e7993c3e7161cad62312b3
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113733392"
---
# <a name="plan-an-azure-active-directory-reporting-and-monitoring-deployment"></a>计划 Azure Active Directory 报告和监视部署

Azure Active Directory (Azure AD) 报表和监视解决方案取决于法律、安全和运营等方面的要求以及现有的环境和过程。 本文介绍各种不同的设计选项并指导你掌握正确的部署策略。

### <a name="benefits-of-azure-ad-reporting-and-monitoring"></a>Azure AD 报表和监视的优点

Azure AD 报表提供环境中 Azure AD 活动（包括登录事件、审核事件和对目录所做的更改）的综合性视图和日志。

可以将提供的数据用于：

* 确定如何使用应用和服务。

* 检测影响环境运行状况的潜在风险。

* 排查妨碍用户完成其工作的问题。

* 通过查看对 Azure AD 目录的更改的审核事件来获取见解。

> [!IMPORTANT]
> 利用 Azure AD 监视，可以将 Azure AD 报表生成的日志路由到不同的目标系统。 然后，可以将其保存以供长期使用，或者将其与第三方安全信息和事件管理 (SIEM) 工具集成，以便获取有关环境的见解。

利用 Azure AD 监视，可以将日志路由到：

* Azure 存储帐户，以便进行存档。
* Azure Monitor 日志（以前称为 Azure Log Analytics 工作区），可以在其中分析数据、创建仪表板，并对特定事件发出警报。
* Azure 事件中心，可以在其中与现有的 SIEM 工具（如 Splunk、Sumologic 或 QRadar）集成。

> [!NOTE]
> 我们最近开始使用术语“Azure Monitor 日志”，而不使用“Log Analytics”。 日志数据仍然存储在 Log Analytics 工作区中，并仍然由同一 Log Analytics 服务收集并分析。 我们正在更新术语，以便更好地反映 [Azure Monitor 中的日志](../../azure-monitor/data-platform.md)的角色。 有关详细信息，请参阅 [Azure Monitor 术语更改](../../azure-monitor/terminology.md)。

[详细了解报告保留策略](./reference-reports-data-retention.md)。

### <a name="licensing-and-prerequisites-for-azure-ad-reporting-and-monitoring"></a>Azure AD 报告和监视的许可和先决条件

需要 Azure AD Premium 许可证才能访问 Azure AD 登录日志。

[Azure Active Directory 定价指南](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing)中介绍了详细功能和许可信息。

若要部署Azure AD 监视和报告，需要一个用户身份，该用户需要是 Azure AD 租户的全局管理员或安全管理员。

根据日志数据的最终目标，你将需要以下某个帐户：

* 你对其拥有 ListKeys 权限的 Azure 存储帐户。 建议使用常规存储帐户而非 Blob 存储帐户。 有关存储定价信息，请参阅 [Azure 存储定价计算器](https://azure.microsoft.com/pricing/calculator/?service=storage)。

* 用于与第三方 SIEM 解决方案集成的 Azure 事件中心命名空间。

* 用于将日志发送到 Azure Monitor 日志的 Azure Log Analytics 工作区。

## <a name="plan-an-azure-reporting-and-monitoring-deployment-project"></a>计划 Azure 报表和监视部署项目

在此项目中，你将定义那些将会消耗和监视报表的受众，并定义 Azure AD 监视体系结构。

### <a name="engage-the-right-stakeholders"></a>让合适的利益干系人参与

当技术项目失败时，它们通常是由于在影响、结果和责任方面不符合预期而导致的。 为了避免这些缺陷，请[确保让正确的利益干系人参与](../fundamentals/active-directory-deployment-plans.md)。 另外，请确保通过记录利益干系人及其项目的输入信息和相应责任，使项目中利益干系人的角色得到充分了解。

### <a name="plan-communications"></a>规划沟通

沟通对于任何新服务的成功都至关重要。 主动与用户交流他们的体验将如何变化、何时会变化以及在遇到问题时如何获取支持。

### <a name="document-your-current-infrastructure-and-policies"></a>记录你当前的基础结构和策略

你当前的基础结构和策略将会推动你的报表和监视设计。 请确保你知晓

* 你在使用哪些的 SIEM 工具（如果有）。

* 你的 Azure 基础结构，包括现有的存储帐户和正在使用的监视。

* 你的组织为日志制定的保留策略，包括所需的任何适用的符合性框架。 

## <a name="plan-an-azure-ad-reporting-and-monitoring-deployment"></a>计划 Azure AD 报表和监视部署

报表和监视用于满足业务需求，深入了解使用模式，以及改善组织的安全状态。

### <a name="business-use-cases"></a>业务用例

* 对于满足业务需求的解决方案而言是必需的
* 对于满足业务需求而言最好具备
* 不适用

|区域 |说明 |
|-|-|
|保留| 日志保留期超过 30 天。 ‎由于法律或业务方面的要求，需要将 Azure AD 的审核日志和登录日志存储 30 天以上。 |
|分析| 需要能够搜索日志。 ‎需要能够使用分析工具搜索存储的日志。 |
| 操作见解| 适用于各个不同团队的见解。 需要为不同用户提供访问权限，以获取操作见解，如应用程序使用情况、登录错误、自助服务使用情况、趋势，等等。 |
| 安全见解| 适用于各个不同团队的见解。 需要为不同用户提供访问权限，以获取操作见解，如应用程序使用情况、登录错误、自助服务使用情况、趋势，等等。 |
| SIEM 系统中的集成      | SIEM 集成。 ‎需要将 Azure AD 登录日志和审核日志集成并流式传输到现有的 SIEM 系统。 |

### <a name="choose-a-monitoring-solution-architecture"></a>选择监视解决方案体系结构

使用 Azure AD 监视，可将 Azure AD 活动日志路由到最符合业务需求的系统。 然后，可以保留这些日志进行长期报告和分析，以便深入了解环境，并将环境与 SIEM 工具集成。

#### <a name="decision-flow-chartan-image-showing-what-is-described-in-subsequent-sections"></a>决策流程图![显示后续部分中所述内容的图像](media/reporting-deployment-plan/deploy-reporting-flow-diagram.png)

#### <a name="archive-logs-in-a-storage-account"></a>在存储帐户中将日志存档

通过将日志路由到 Azure 存储帐户，这些日志的保留时间可以比[保留策略](./reference-reports-data-retention.md)中列出的默认保留期更长。 如果需要将日志存档，但不需要将其与 SIEM 系统集成，并且不需要进行持续查询和分析，请使用此方法。 你仍然可以执行按需搜索。

了解如何[将数据路由到存储帐户](./quickstart-azure-monitor-route-logs-to-storage-account.md)。

#### <a name="send-logs-to-azure-monitor-logs"></a>将日志发送到 Azure Monitor 日志

[Azure Monitor 日志](../../azure-monitor/logs/log-query-overview.md)整合了来自不同源的监视数据。 它提供查询语言和分析引擎，可让你深入了解应用程序的操作和资源的使用。 通过将 Azure AD 活动日志发送到 Azure Monitor 日志，可以快速检索和监视收集到的数据并针对这些数据发出警报。 如果没有要直接将数据发送到的现有 SIEM 解决方案，但的确又需要查询和分析，请使用此方法。 在数据位于 Azure Monitor 日志中后，可将这些数据发送到事件中心，并从数据中心再将数据发送到 SIEM（如果需要）。

了解如何[将数据发送到 Azure Monitor 日志](./howto-integrate-activity-logs-with-log-analytics.md)。

还可以安装针对 Azure AD 活动日志的预生成视图，以监视涉及登录和审核事件的常见场景。

了解如何[安装和使用用于 Azure AD 活动日志的 Log Analytics 视图](./howto-install-use-log-analytics-views.md)。

#### <a name="stream-logs-to-your-azure-event-hub"></a>将日志流式传输到 Azure 事件中心

将日志路由到 Azure 事件中心，可实现与第三方 SIEM 工具集成。 可以通过此集成将 Azure AD 活动日志数据与其他由 SIEM 托管的数据组合起来，获取更丰富的有关环境的见解。 

了解如何[将日志流式传输到事件中心](./tutorial-azure-monitor-stream-logs-to-event-hub.md)。

## <a name="plan-operations-and-security-for-azure-ad-reporting-and-monitoring"></a>计划 Azure AD 报表和监视的操作和安全性

利益干系人需要访问 Azure AD 日志来获取操作见解。 可能的用户包括安全团队成员、内部或外部审计人员，以及标识和访问管理操作团队。

利用 Azure AD 角色，可以根据自己的角色来委托配置和查看 Azure AD 报表的功能。 确定组织中的哪些人员需要读取 Azure AD 报表的权限以及哪些角色适用于这些人员。 

以下角色可以读取 Azure AD 报表：

* 全局管理员

* 安全管理员

* 安全读取者

* 报表读取者

详细了解 [Azure AD 管理角色](../roles/permissions-reference.md)。

始终应用最小特权的概念以降低帐户泄露的风险。 请考虑实现 [Privileged Identity Management](../privileged-identity-management/pim-configure.md)，以进一步保护组织。


## <a name="deploy-azure-ad-reporting-and-monitoring"></a>部署 Azure AD 报表和监视

根据前面使用上述设计指南做出的决策，本部分将指导你阅读有关不同部署选项的文档。

### <a name="consume-and-archive-azure-ad-logs"></a>Azure AD 日志的消耗和存档

[在 Azure 门户中查找活动报告](./howto-find-activity-reports.md)

[将 Azure AD 日志存档到 Azure 存储帐户](./quickstart-azure-monitor-route-logs-to-storage-account.md)

### <a name="implement-monitoring-and-analytics"></a>实现监视和分析

[将日志发送到 Azure Monitor](./howto-integrate-activity-logs-with-log-analytics.md)

[安装和使用用于 Azure Active Directory 的日志分析视图](./howto-install-use-log-analytics-views.md)

[使用 Azure Monitor 日志分析 Azure AD 活动日志](./howto-analyze-activity-logs-log-analytics.md)

* [解释 Azure Monitor 中的审核日志架构](./overview-reports.md)

* [解释 Azure Monitor 中的登录日志架构](./reference-azure-monitor-sign-ins-log-schema.md)

 * [将 Azure AD 日志流式传输到 Azure 事件中心](./tutorial-azure-monitor-stream-logs-to-event-hub.md)

* [使用 Azure Monitor 将 Azure AD 日志与 Splunk 集成](./howto-integrate-activity-logs-with-splunk.md)

* [使用 Azure Monitor 将 Azure AD 日志与 SumoLogic 集成](./howto-integrate-activity-logs-with-sumologic.md)

 

 

## <a name="next-steps"></a>后续步骤

考虑实现 [Privileged Identity Management](../privileged-identity-management/pim-configure.md) 

考虑实现 [Azure 基于角色的访问控制 (Azure RBAC)](../../role-based-access-control/overview.md)