---
title: 从现有 SIEM 迁移到 Azure Sentinel。
description: 了解如何以最佳方式从现有 SIEM 迁移到 Azure Sentinel，以便在整个组织中进行可缩放的智能安全分析。
services: sentinel
documentationcenter: na
author: batamig
ms.service: azure-sentinel
ms.topic: conceptual
ms.date: 07/04/2021
ms.author: bagol
ms.openlocfilehash: dc2c1b6ed2d40e7d1af82b3c62f378bfd046a94c
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123254116"
---
# <a name="migrate-to-azure-sentinel-from-an-existing-siem"></a>从现有 SIEM 迁移到 Azure Sentinel

安全运营中心 (SOC) 团队将使用集中式安全信息和事件管理 (SIEM) 以及安全业务流程、自动化和响应 (SOAR) 解决方案来保护日益分散的数字资产。

旧式 SIEM 通常位于本地，可以保持对本地资产的良好覆盖。 但是，本地体系结构对云资产的覆盖可能不足，例如 Azure、Microsoft 365、AWS 或 Google Cloud Platform (GCP) 中的资产。 相比之下，Azure Sentinel 可以从本地和云资产引入数据，确保覆盖整个资产。

本文介绍如何通过并行配置或者通过过渡到完整 Azure Sentinel 部署，从现有的旧式 SIEM 迁移到 Azure Sentinel。

## <a name="plan-your-migration"></a>规划迁移

你可能已根据自己的业务需求和可用资源，决定直接或逐步过渡到 Azure Sentinel。

需要适当规划迁移，确保过渡不会导致覆盖范围出现缺口，从而损害组织的安全性。

若要开始，请确定关键的核心功能和首要要求。 评估当前 SIEM 覆盖的关键用例，并确定 Azure Sentinel 需要通过哪些检测和功能继续提供覆盖度。

考虑要迁移的确切数据源和检测规则时，将在迁移过程的每个步骤中添加更多进程内计划。 有关详细信息，请参阅[迁移数据](#migrate-your-data)和[迁移分析规则](#migrate-analytics-rules)。

> [!TIP]
> 当前 SIEM 可能具有多不胜数的检测和用例。 确定哪些检测和用例对你的业务最为有用，以及哪些检测和用例可能不需要迁移。 例如，检查过去一年中哪些检测产生了成果。
>

### <a name="compare-your-legacy-siem-to-azure-sentinel"></a>将旧式 SIEM 与 Azure Sentinel 相比较

将旧式 SIEM 与 Azure Sentinel 相比较，以帮助细化迁移完成条件，并了解可以在何处使用 Azure Sentinel 创造更多价值。

例如，评估以下重要方面：

|评估方面 |说明  |
|---------|---------|
|攻击检测覆盖度。     | 使用 [MITRE ATT&CK](https://attack.mitre.org/) 或类似框架比较每个 SIEM 检测全范围攻击的效果。        |
|响应能力。     |   衡量平均确认时间 (MTTA)，即，从 SIEM 中出现警报，到分析师开始处理该警报的间隔时间。 不同 SIEM 的此时间指标可能相似。      |
|平均修正时间 (MTTR)。     |    比较每个 SIEM 调查的事件的 MTTR（假设分析师的技能水平相同）。     |
|搜寻速度和敏捷度。     | 衡量团队的搜寻速度，从完全形成假设开始，到查询数据，再到在每个 SIEM 平台上获得结果为止。        |
|容量增长阻力。     |  比较随着用量的增长添加容量的难度。 请记住，与传统的本地工作负载相比，云服务和应用程序往往会生成更多日志数据。       |
|     |         |

如果你对现有本地 SIEM 的投资有限或完全没有投资，则迁移到 Azure Sentinel 可能是一种简单而直接的部署。 但是，大量投资于旧式 SIEM 的企业通常需要通过一个多阶段过程来适应过渡任务。

尽管 Azure Sentinel 为本地和云提供扩展的数据和响应，但你可能希望通过[并行](#select-a-side-by-side-approach-and-method)运行 Azure Sentinel 和旧式 SIEM 来缓慢开始迁移。 在并行体系结构中，本地资源可以使用本地 SIEM 和云资源，而新工作负载可以使用基于云的分析。

除非选择长期并行配置，否则请迁移到完整 Azure Sentinel 部署，以降低基础结构成本并实现实时威胁分析和云可伸缩性。

## <a name="select-a-side-by-side-approach-and-method"></a>选择并行方法

根据组织的 SIEM 需求，将并行体系结构用作短期过渡阶段来实现完全由云托管的 SIEM，或用作中长期操作模型。

例如，虽然建议的体系结构是使用并行体系结构合理的一段时间，只要能够完成迁移即可，但你的组织可能希望保持使用并行配置更长的时间（例如，你尚未准备好从旧式 SIEM 迁移时）。 通常，使用长期并行配置的组织只会使用 Azure Sentinel 来分析其云数据。

在确定要在迁移中使用哪种方法时，请考虑每种方法的优缺点。

> [!NOTE]
> 由于成本和复杂性，许多组织都会避免运行多个本地分析解决方案。
>
> Azure Sentinel 提供[即用即付定价](azure-sentinel-billing.md)和灵活的基础结构，使 SOC 团队有时间适应变化。 以最适合组织的步调迁移并测试内容。
>
### <a name="short-term-approach"></a>短期方法

:::row:::
   :::column span="":::
      **优点**

        - 使 SOC 员工在迁移工作负载和分析期间有时间适应新的过程。

        - 获得搜寻方案的所有数据源之间的深度关联。

        - 无需在 SIEM 之间执行分析、创建转发规则以及在两个位置结束调查。

        - 可让 SOC 团队快速降级旧式 SIEM 解决方案，从而消除基础结构成本和许可成本。
   :::column-end:::
   :::column span="":::
      **缺点**

        - SOC 员工可能需要经历一段跨越式的学习曲线。
   :::column-end:::
:::row-end:::

### <a name="medium--to-long-term-approach"></a>中长期方法

:::row:::
   :::column span="":::
      **优点**

        - 可让你使用重要的 Azure Sentinel 优势（例如 AI、ML 和调查功能），而无需完全从旧式 SIEM 迁移。

        - 与旧式 SIEM 相比，在 Azure Sentinel 中分析云数据或 Microsoft 数据更加节省。
   :::column-end:::
   :::column span="":::
      **缺点**

        - 需要隔离不同数据库的分析，因此复杂性更高。

        - 根据多环境事件拆分案例管理和调查。

        - 产生的人员成本和基础结构成本更高。

        - 要求 SOC 员工了解两种不同的 SIEM 解决方案。
   :::column-end:::
:::row-end:::



### <a name="send-alerts-from-a-legacy-siem-to-azure-sentinel-recommended"></a>将警报从旧式 SIEM 发送到 Azure Sentinel（建议）

将警报或异常活动指标从旧式 SIEM 发送到 Azure Sentinel。

- 在 Azure Sentinel 中引入和分析云数据
- 使用旧式 SIEM 分析本地数据并生成警报。
- 将警报从本地 SIEM 转发到 Azure Sentinel 以建立单一接口。

例如，使用 [Logstash](connect-logstash.md)、[API](/rest/api/securityinsights/) 或 [Syslog](connect-syslog.md) 转发警报，并将其以 [JSON](https://techcommunity.microsoft.com/t5/azure-sentinel/tip-easily-use-json-fields-in-sentinel/ba-p/768747) 格式存储在 Azure Sentinel [Log Analytics 工作区](../azure-monitor/logs/quick-create-workspace.md)中。

通过将警报从旧式 SIEM 发送到 Azure Sentinel，团队可以在 Azure Sentinel 中交叉关联和调查这些警报。 如果需要，团队仍可以访问旧式 SIEM 以进行更深入的调查。 同时，可以在较长的过渡期内继续迁移数据源。

这种建议的并行迁移方法提供 Azure Sentinel 的全部价值，使你可以按照适合组织的步调迁移数据源。 此方法可避免在移动数据源时重复产生数据存储和引入成本。

有关详细信息，请参阅：

- [将 QRadar 违规事件迁移到 Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/migrating-qradar-offenses-to-azure-sentinel/ba-p/2102043)
- [将数据从 Splunk 导出到 Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/how-to-export-data-from-splunk-to-azure-sentinel/ba-p/1891237)。


### <a name="send-alerts-and-enriched-incidents-from-azure-sentinel-to-a-legacy-siem"></a>将警报和扩充的事件从 Azure Sentinel 发送到旧式 SIEM

分析 Azure Sentinel 中的某些数据（例如云数据），然后将生成的警报发送到旧式 SIEM。 使用旧式 SIEM 作为单一接口来与 Azure Sentinel 生成的警报进行交叉关联。 仍可使用 Azure Sentinel 对 Azure Sentinel 生成的警报进行更深入的调查。

此配置经济高效，因为你可以将云数据分析迁移到 Azure Sentinel，且不会重复产生成本或者两次支付数据费用。 你仍可以按照自己的步调自由迁移。 随着你不断地将数据源和检测转移到 Azure Sentinel，迁移到用作主接口的 Azure Sentinel 会变得越来越容易。 不过，只是将扩充的事件转发到旧式 SIEM 并不能充分利用 Azure Sentinel 的调查、搜寻和自动化功能所带来的价值。

有关详细信息，请参阅：

- [将扩充的 Azure Sentinel 警报发送到旧式 SIEM](https://techcommunity.microsoft.com/t5/azure-sentinel/sending-enriched-azure-sentinel-alerts-to-3rd-party-siem-and/ba-p/1456976)
- [将扩充的 Azure Sentinel 警报发送到 IBM QRadar](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-sentinel-side-by-side-with-qradar/ba-p/1488333)
- [将 Azure Sentinel 警报引入 Splunk](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-sentinel-side-by-side-with-splunk/ba-p/1211266)

### <a name="other-methods"></a>其他方法

下表描述了不建议的并行配置，并详细说明了原因：

|方法  |说明  |
|---------|---------|
|将 Azure Sentinel 日志发送到旧式 SIEM     |  使用此方法时，仍然遇到本地 SIEM 的成本和规模问题。 <br><br>需要支付 Azure Sentinel 中的数据引入费用以及旧式 SIEM 中的存储费用，并且无法利用 Azure Sentinel 的 SIEM 和 SOAR 检测、分析、用户实体行为分析 (UEBA)、AI 或者调查和自动化工具。       |
|将日志从旧式 SIEM 发送到 Azure Sentinel     |   虽然此方法提供 Azure Sentinel 的完整功能，但组织仍然需要为两个不同的数据引入源付费。 除了增大体系结构复杂性之外，此模型还会导致成本提高。     |
|将 Azure Sentinel 和旧式 SIEM 用作两个完全独立的解决方案     |  可以使用 Azure Sentinel 分析某些数据源（例如云数据），并继续使用本地 SIEM 分析其他源。 使用这种设置可以明确界定何时使用每个解决方案，并避免重复产生成本。 <br><br>但是，交叉关联会变得困难，且无法完全诊断跨这两组数据源的攻击。 在当今的环境中，威胁通常会在整个组织中横向转移，这种可见性差距可能会带来严重的安全风险。       |
|     |         |



## <a name="migrate-your-data"></a>迁移数据

确保仅迁移代表当前关键用例的数据。

1. 确定支持每个用例所需的数据。

1. 确定当前数据源是否提供有价值的数据。

1. 确定当前 SIEM 中的任何可见性差距，以及如何弥补这些差距。

1. 对于每个数据源，考虑是否需要引入原始日志（成本可能非常高昂），或者扩充的警报是否为关键用例提供足够的上下文。

      例如，可以从整个组织的安全产品中引入扩充的数据，然后使用 Azure Sentinel 交叉关联这些数据，而无需从数据源本身引入原始日志。

1. 使用下列任一资源来引入数据：

    - 使用 Azure Sentinel 的[内置数据连接器](connect-data-sources.md)开始引入数据。 例如，可以开始使用[免费试用版](azure-sentinel-billing.md#free-trial)来处理云数据，或使用[免费的数据连接器](azure-sentinel-billing.md#free-data-sources)从其他 Microsoft 产品中引入数据。

    - 使用 [Syslog](connect-data-sources.md#syslog)、[通用事件格式 (CEF)](connect-data-sources.md#common-event-format-cef) 或 [REST API](connect-data-sources.md#rest-api-integration) 连接其他数据源。

        有关详细信息，请参阅 [Azure Sentinel 数据连接器参考](data-connectors-reference.md)和 [Azure Sentinel 解决方案目录](sentinel-solutions-catalog.md)。

> [!TIP]
> - 只是从免费数据源引入数据可能无法充分测试对你很重要的数据。 测试时，请考虑从免费和付费数据连接器引入有限的数据，使测试结果发挥最大的价值。
>
> - 在 Azure Sentinel 中迁移检测和生成用例时，请留意引入的数据，并验证它为你的重要优先事务带来的价值。 重新访问数据收集对话，确保跨用例的数据深度和广度足够。
>

## <a name="migrate-analytics-rules"></a>迁移分析规则

Azure Sentinel 使用机器学习分析来创建高保真的且可处理的事件，某些现有检测在 Azure Sentinel 中可能是多余的。 因此，不要盲目地迁移所有检测和分析规则：

- 确保选择能够证明规则迁移合理的用例，同时考虑业务优先级和效率。

- 查看可能已解决你的用例需求的[内置分析规则](detect-threats-built-in.md)。 在 Azure Sentinel 中，转到“配置”>“分析”>“规则模板”选项卡，以基于内置模板创建规则。

- 查看过去 6-12 个月未触发任何警报的任何规则，并确定这些规则是否仍然相关。

- 消除你在日常中会忽略的低级别威胁或警报。

若要将分析规则迁移到 Azure Sentinel，请执行以下操作：

1. 验证是否为要迁移的每个规则准备好了一个测试系统。

    1. 为迁移的规则准备验证过程，包括完整测试方案和脚本。

    1. 确保团队具有有用的资源可用于测试迁移的规则。

    1. 确认已连接全部所需的数据源，并检查数据连接方法。

1. 验证你的检测是否是作为 Azure Sentinel 中的内置模板提供的：

    - 如果内置规则足以满足需求，请使用内置规则模板为你自己的工作区创建规则。

        在 Azure Sentinel 中，转到“配置”>“分析”>“规则模板”选项卡，然后创建并更新每个相关的分析规则。

        有关详细信息，请参阅[直接检测威胁](detect-threats-built-in.md)。

    - 如果 Azure Sentinel 内置规则未涵盖你的检测，请尝试使用联机查询转换器（例如 [Uncoder.io](https://uncoder.io/)）将查询转换为 KQL。

        确定触发条件和规则操作，然后构建并检查 KQL 查询。

    - 如果内置规则和联机规则转换器都不足以满足需求，则需要手动创建规则。 在这种情况下，请使用以下步骤开始创建规则：

        1. 确定要在规则中使用的数据源。 需要在 Azure Sentinel 中的数据源与数据表之间创建一个映射表，以标识要查询的表。

        1. 确定数据中的要在规则中使用的任何属性、字段或实体。

        1. 确定规则条件和逻辑。 在此阶段，可以使用规则模板作为示例来了解如何构造 KQL 查询。

            考虑筛选器、关联规则、活动列表、参考集、监视列表、检测异常情况、聚合等。 可以使用旧式 SIEM 提供的参考来了解如何以最佳方式映射查询语法。

            有关示例，请参阅：

            - [ArcSight/QRadar 与 Azure Sentinel 之间的示例规则映射](https://github.com/Azure/Azure-Sentinel/blob/master/Tools/RuleMigration/Rule%20Logic%20Mappings.md)
            - [SPL 到 KQL 的映射示例](https://github.com/Azure/Azure-Sentinel/blob/master/Tools/RuleMigration/Rule%20Logic%20Mappings.md) 

        1. 确定触发条件和规则操作，然后构建并检查 KQL 查询。 检查查询时，请考虑 KQL 优化指导资源。

1. 使用每个相关用例来测试规则。 如果该规则未提供预期的结果，你可以检查 KQL 并再次测试。

1. 如果对结果满意时，可以考虑迁移该规则。 根据需要为规则操作创建一个 playbook。 有关详细信息，请参阅[使用 Azure Sentinel 中的 playbook 自动响应威胁](automate-responses-with-playbooks.md)。

有关详细信息，请参阅：

- [创建自定义分析规则以检测威胁](detect-threats-custom.md)。 使用[警报分组](detect-threats-custom.md#alert-grouping)，通过将给定时间范围内发生的警报分组到一起来减轻警报疲劳。
- [将数据字段映射到 Azure Sentinel 中的实体](map-data-fields-to-entities.md)，使 SOC 工程师能够将实体定义为在调查期间要跟踪的证据的一部分。 实体映射还可让 SOC 分析师利用直观的调查图 (investigate-cases.md#use-the-investigation-graph-to-deep-dive) 来帮助减少时间和工作量。
- [使用 UEBA 数据调查事件](investigate-with-ueba.md)，例如，使用证据来显示与事件预览窗格中特定事件关联的事件、警报和任何书签。
- [Kusto 查询语言 (KQL)](/azure/data-explorer/kusto/query/)，可用于将只读请求发送到 [Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md) 数据库，以处理数据并返回结果。 还可以在 [Microsoft Defender for Endpoint](https://www.microsoft.com/microsoft-365/security/endpoint-defender) 和 [Application Insights](../azure-monitor/app/app-insights-overview.md) 等其他 Microsoft 服务中使用 KQL。

## <a name="use-automation-to-streamline-processes"></a>使用自动化来简化过程

使用自动工作流将警报分组为常见事件，并修改此事件的优先级。

有关详细信息，请参阅：

- [Azure Sentinel 中的安全业务流程、自动化和响应 (SOAR)](automation-in-azure-sentinel.md)。
- [在 Azure Sentinel 中使用 playbook 实现威胁响应自动化](automate-responses-with-playbooks.md)
- [在 Azure Sentinel 中使用自动化规则自动处理事件](automate-incident-handling-with-automation-rules.md)

## <a name="retire-your-legacy-siem"></a>停用旧式 SIEM

使用以下清单确保已完全迁移到 Azure Sentinel，并已准备好停用旧式 SIEM：


|就绪性方面  |详细信息  |
|---------|---------|
|技术就绪性     | 检查关键数据：确保所有来源和警报已在 Azure Sentinel 中提供。 <br><br>存档所有记录：保存重要的既往事件和案例记录（原始数据是可选的）以保留制度性历史记录。   |
|过程就绪性     |  Playbook：更新[调查和搜寻过程](investigate-cases.md)，使其适合 Azure Sentinel。<br><br>指标：确保可以从 Azure Sentinel 获取所有关键指标。<br><br>工作簿：创建[自定义工作簿](monitor-your-data.md)或使用内置工作簿模板，以便在[连接到数据源](connect-data-sources.md)后立即获取见解。<br><br>事件：确保将所有当前事件（包括所需的源数据）传输到新系统。        |
|人员就绪性     |  SOC 分析师：确保团队中的每个人都接受了 Azure Sentinel 培训，并且离开了旧式 SIEM 也能顺利地工作。   |
|     |         |
## <a name="next-steps"></a>后续步骤

迁移后，请浏览 Microsoft 的 Azure Sentinel 资源以拓展自己的技能并充分利用 Azure Sentinel。

另外，请考虑结合使用 Azure Sentinel 以及可实现[集成式威胁防护](https://www.microsoft.com/security/business/threat-protection)的 [Microsoft 365 Defender](./microsoft-365-defender-sentinel-integration.md) 和 [Azure Defender](../security-center/azure-defender.md) 来增强威胁防护。 这样可以受益于 Azure Sentinel 提供的可见性幅度，同时可以更深入地调查详细的威胁分析结果。

有关详细信息，请参阅：

- [规则迁移最佳做法](https://techcommunity.microsoft.com/t5/azure-sentinel/best-practices-for-migrating-detection-rules-from-arcsight/ba-p/2216417)
- [网络研讨会：有关转换检测规则的最佳做法](https://www.youtube.com/watch?v=njXK1h9lfR4)
- [Azure Sentinel 中的安全业务流程、自动化和响应 (SOAR)](automation-in-azure-sentinel.md)
- [利用事件指标更好地管理 SOC](manage-soc-with-incident-metrics.md)
- [Azure Sentinel 学习路径](/learn/paths/security-ops-sentinel/)
- [SC-200 Microsoft 安全运营分析师认证](/learn/certifications/exams/sc-200)
- [Azure Sentinel 忍者培训](https://techcommunity.microsoft.com/t5/azure-sentinel/become-an-azure-sentinel-ninja-the-complete-level-400-training/ba-p/1246310)
- [使用 Azure Sentinel 调查针对混合环境的攻击](https://mslearn.cloudguides.com/guides/Investigate%20an%20attack%20on%20a%20hybrid%20environment%20with%20Azure%20Sentinel)