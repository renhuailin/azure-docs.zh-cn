---
title: 针对 Azure 澳大利亚安全性的系统监视
description: 有关在澳大利亚区域根据澳大利亚政府的具体政策、法规和法律要求配置系统监视的指导。
author: emilyre
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: yvettep
ms.openlocfilehash: 775b2e366d335944205197aad97c1752b6ee7d04
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2021
ms.locfileid: "117029091"
---
# <a name="system-monitoring-for-security-in-azure-australia"></a>针对 Azure 澳大利亚安全性的系统监视

具有可靠的安全策略（包括实时监视和日常安全评估）对于增强 IT 环境（包括云）的日常操作安全至关重要。

云安全是客户与云提供商共同努力的结果。 考虑到[澳大利亚网络安全中心 (ACSC) 的信息安全手册控制 (ISM)](https://acsc.gov.au/infosec/ism/index.htm)中包含的建议（具体是针对实现集中式事件日志、事件日志审核以及安全漏洞评估和管理的建议），Microsoft Azure 提供了四项服务来满足这些要求。 Microsoft Azure 服务包括：

* Azure 安全中心
* Azure Monitor
* Azure 顾问
* Azure Policy

ACSC 建议对受保护的数据使用这些服务。 通过使用这些服务，你可以主动监视和分析 IT 环境，并就最好在何处分配资源做出明智的决策，以增强安全性。 其中每项服务都是组合解决方案的一部分，可为你尽可能提供最佳的见解、建议和保护。

## <a name="azure-security-center"></a>Azure 安全中心

[Azure 安全中心](../security-center/security-center-introduction.md)提供统一的安全管理控制台，可用于监视和增强 Azure 资源和托管数据的安全性。 Azure 安全中心包含安全功能分数，此分数基于对 Azure 顾问的最佳做法配置状态和 Azure Policy 的总体合规性的分析。

Azure 安全中心为 Azure 客户提供以下功能：

* 安全策略、评估和建议
* 安全事件收集和搜索
* 访问和应用程序控制
* 高级威胁检测
* 实时虚拟机访问控制
* 混合安全

可以扩展由 Azure 安全中心监视的资源范围，以包括混合云环境中受支持的本地资源。 这包括当前由受支持的 System Center Operations Manager 版本监视的本地资源。

安全中心“标准”层还提供了 [ASD Essential 8](https://acsc.gov.au/publications/protect/essential-eight-explained.htm) 所需的基于云的安全控制。 其中包括应用程序筛选和通过实时访问来限制管理权限。

### <a name="azure-monitor"></a>Azure Monitor

[Azure Monitor](../azure-monitor/overview.md) 是适用于所有 Azure 资源的集中式日志记录解决方案，并包括 Log Analytics 和 Application Insights。 从 Azure 资源收集的两种主要数据类型：日志和指标。 在 Azure Monitor 收集日志记录信息后，可以通过各种工具使用这些信息并将其用于各种用途。

![Azure Monitor 概述](media/overview.png)

Azure Monitor 还包括“Azure 活动日志”。 活动日志存储 Azure 内发生的所有订阅级别事件。 Azure 客户可通过它查看在其 Azure 资源上进行的操作背后的“人员、内容和时间”。 发送到 Azure Monitor 的基于资源的日志和 Azure 活动日志事件均可使用内置的 Kusto 查询语言进行分析。 然后，可以导出这些日志，用于创建自定义仪表板和视图，并将其配置为触发警报和通知。

### <a name="azure-advisor"></a>Azure 顾问

[Azure 顾问](../advisor/advisor-overview.md)分析 Azure 订阅中受支持的 Azure 资源、系统生成的日志文件和当前的资源配置。 Azure 顾问中提供的分析是实时生成的，并基于 Microsoft 建议的最佳做法。 将分析添加到环境中的任何受支持的 Azure 资源，并提供相应的建议。 Azure 顾问建议分为四个最佳做法类别：

* 安全
* 高可用性
* 性能
* 成本

Azure 顾问生成的安全建议构成了 Azure 安全中心提供的总体安全分析的一部分。

Azure 顾问收集的信息为管理员提供以下内容：

* 关于不符合建议的最佳做法的资源配置的见解
* 有关要执行的特定修正操作的指导
* 指示应优先执行哪些修正操作的排名

### <a name="azure-policy"></a>Azure Policy

[Azure Policy](../governance/policy/overview.md) 提供应用规则的功能，这些规则管理 Azure 资源的类型及其允许的配置。 Policy 可用于控制资源创建和配置，也可用于审核环境中的配置设置。 这些审核结果可用于构成修正活动的基础。 Azure Policy 不同于 Azure 基于角色的访问控制 (Azure RBAC)；Azure Policy 用于限制资源及其配置，而 Azure RBAC 用于限制 Azure 用户的特权访问。

无论是强制执行特定策略还是审核策略的效果，都会持续监视策略合规性，并向管理员提供特定于资源的总体合规性信息。 Azure Policy 合规性数据被提供给 Azure 安全中心，构成安全功能分数的一部分。

## <a name="key-design-considerations"></a>关键设计考虑因素

实现事件日志策略时，ACSC ISM 突出显示以下注意事项：

* 集中式日志记录设施
* 要记录的特定事件
* 事件日志保护
* 事件日志保留
* 事件日志审核

除了收集和管理日志外，ISM 还建议对组织的 IT 环境进行日常漏洞评估。

### <a name="centralised-logging"></a>集中式日志记录

任何日志记录解决方案应尽可能将捕获的日志合并到单个数据存储库中。 这不仅降低了操作复杂性并防止了多个数据孤岛的产生，还支持一起分析从多个源收集的数据，从而识别任何关联事件。 这对于检测和管理任何网络安全事件的范围至关重要。

对于使用 Azure Monitor 的所有 Azure 客户，这一要求都得到了满足。 此产品/服务不仅在 Azure 中为所有 Azure 资源提供集中式日志记录存储库，还可让你将数据流式传输到 Azure 事件中心。 Azure 事件中心提供完全托管的实时数据引入服务。 将 Azure Monitor 数据流式传输到 Azure 事件中心后，还可以轻松地将数据连接到受支持的现有安全信息和事件管理 (SIEM) 存储库及其他第三方监视工具。

Microsoft 还提供其自己的 Azure 本机 SIEM 解决方案，即 Azure Sentinel。 Azure Sentinel 支持各种数据连接器，并可用于监视整个企业中的安全事件。 通过将来自受支持的[数据连机器](../sentinel/connect-data-sources.md)的数据、Azure Sentinel 的内置机器学习和 Kusto 查询语言相结合，为安全管理员提供了针对警报检测、威胁可见性、主动搜寻和威胁响应的单一解决方案。 Azure Sentinel 还提供了一项搜寻和笔记本功能，使安全管理员能够将安全调查过程中执行的所有步骤记录在可在组织内共享的可重用的 playbook 中。 安全管理员甚至可以使用内置的[用户分析](../sentinel/overview.md)来调查单个指定用户的操作。

### <a name="logged-events-and-log-detail"></a>记录的事件和日志详细信息

ISM 提供了应包含在任何日志记录策略中的事件日志类型的详细列表。 任何捕获的日志都必须包含足够的详细信息，以便在进行分析和调查时有任何实际用途。

Azure 中收集的日志属于以下三个类别之一：

* **控制和管理日志**：这些日志提供有关 Azure 资源管理器 CREATE、UPDATE 和 DELETE 操作的信息。

* **数据平面日志**：这些日志包含作为 Azure 资源使用情况的一部分引发的事件。 其中包括诸如 Windows 事件日志（包括系统、安全性和应用程序日志）的源。

* **已处理的事件**：这些事件包含有关 Azure 代表客户已自动处理的事件和警报的信息。 已处理的事件的一个示例是 Azure 安全中心警报。

Azure 虚拟机监视通过部署适用于 Windows 和 Linux 的虚拟机代理得以增强。 这会显著增加收集的日志记录信息的广度。 可以将此代理的部署配置为通过 Azure 安全中心自动执行。

Microsoft 提供有关特定于 Azure 资源的日志及其[架构](../security/fundamentals/log-audit.md)的详细信息。

### <a name="log-retention-and-protection"></a>日志保留和保护

 必须在所需的保持期内安全地存储事件日志。 ISM 建议日志的保留时间至少为七年。 Azure 提供多种方法来确保所收集日志的使用寿命长。 默认情况下，Azure 日志事件存储时间为 90 天。 Azure Monitor 捕获的日志数据可根据需要移动并存储在 Azure 存储帐户中，以便长期保留。 存储在 Azure 存储帐户上的活动日志可保留一定的天数，也可根据需要无限期保留。

用于存储 Azure 日志事件的 Azure 存储帐户可以实现异地冗余，并且可以使用 Azure 备份进行备份。 一旦被 Azure 备份捕获，包含日志的备份的任何删除操作都需要管理员审批，并且标记以供删除的备份仍将保留 14 天，以便恢复。 Azure 备份支持一个受保护实例具有 9999 个副本，提供超过 27 年的每日备份。

Azure 基于角色的访问控制 (Azure RBAC) 应该用于控制对用于 Azure 日志记录的资源的访问。 Azure Monitor、Azure 存储帐户和 Azure 备份都应配置 Azure RBAC，以确保日志中包含的数据的安全性。

### <a name="log-auditing"></a>日志审核

日志的真正价值在经过分析后会体现出来。 使用自动和手动分析并熟悉可用的工具将帮助你检测和管理违反组织安全策略的行为以及网络安全事件。 Azure Monitor 提供了一组丰富的工具来分析收集的日志。 然后，可以在系统之间共享、可视化或以多种形式传播此分析结果。

存储在 Azure Monitor 中的日志数据保存在 Log Analytics 工作区中。 所有分析都从查询开始。 Azure Monitor 查询以 Kusto 查询语言编写。 从 Azure 仪表板到预警规则，查询构成了 Azure Monitor 中所有输出的基础。

![Azure 日志查询概述](media/queries-overview.png)

可以通过使用监视解决方案来增强日志审核。 这些是预先打包的解决方案，其中包含集合逻辑、查询和数据可视化视图。 Microsoft [提供](../azure-monitor/monitor-reference.md)许多监视解决方案，并且可在 Azure 市场找到产品供应商提供的其他解决方案。

### <a name="vulnerability-assessment-and-management"></a>漏洞评估和管理

ISM 指出，日常漏洞评估和管理至关重要。 IT 环境不断发展，外部安全威胁也不断变化。 借助 Azure 安全中心，可以执行自动漏洞评估，并获取有关如何计划和执行修正活动的指南。

Azure 安全中心的安全功能分数提供建议列表，应用这些建议将提高环境的安全性。 该列表按照对总体安全功能分数的影响程度从高到低进行排序。 通过按影响程度对列表进行排序，你可以专注于在增强安全性方面提供最大价值的优先级最高的建议。

Azure Policy 在正在进行的漏洞评估方面也发挥着重要作用。 Azure Policy 中可用的策略类型范围从强制使用资源标记和值，到限制可在其中创建资源的 Azure 区域，再到完全阻止创建特定的资源类型。 可以将一组 Azure 策略分组到“计划”中。 计划用于应用相关的 Azure 策略，这些策略以组的形式同时应用时可构成特定安全或合规性目标的基础。

Azure Policy 具有一个不断增长的内置策略定义库。 通过 Azure 门户，你还可以选择创作自己的自定义 Azure Policy 定义。 在现有库中找到策略或创建新策略后，可以将该策略分配给 Azure 资源。 可以将这些分配的[范围限定](../governance/policy/tutorials/create-and-manage.md)在资源管理层次结构中的各个级别。 策略分配具有继承性，这意味着范围内的所有子资源均接收相同的策略分配。 还可根据需要从限定范围内的策略分配中排除资源。

所有部署的 Azure 策略都有助于提升组织的安全功能分数。 在高度定制的环境中，可创建和部署自定义 Azure Policy 定义，以提供针对特定工作负载定制的审核信息。

## <a name="getting-started"></a>入门

若要开始使用 Azure 安全中心并充分利用 Azure Monitor、顾问和 Policy，Microsoft 建议执行以下初始步骤：

* 启用 Azure 安全中心
* 升级到标准层
* 向受支持的 Azure 虚拟机启用 Microsoft Monitoring Agent 的自动预配
* 在安全中心仪表板上查看安全建议和解决警报并确定其优先级

## <a name="next-steps"></a>后续步骤

阅读 [Azure Policy 和 Azure 蓝图](azure-policy.md)，详细了解如何实现对 Azure 澳大利亚资源的治理和控制以确保策略和法规符合性。