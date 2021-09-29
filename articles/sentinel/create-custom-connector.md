---
title: 用于创建 Azure Sentinel 自定义连接器的资源 | Microsoft Docs
description: 了解可用来为 Azure Sentinel 创建自定义连接器的资源。 方法包括 Log Analytics 代理和 API、Logstash、逻辑应用、PowerShell 以及 Azure Functions。
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/09/2021
ms.author: bagol
ms.openlocfilehash: 3b578ffbb734c738aa3f082d192eae8557f4e307
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124755102"
---
# <a name="resources-for-creating-azure-sentinel-custom-connectors"></a>用于创建 Azure Sentinel 自定义连接器的资源

Azure Sentinel 提供各种[适用于 Azure 服务和外部解决方案的内置连接器](connect-data-sources.md)，还支持从某些来源引入数据，无需使用专用连接器。

如果你无法使用任何可用的现有解决方案将数据源连接至 Azure Sentinel，请考虑创建自己的数据源连接器。

如需支持的连接器完整列表，请参阅 [Azure Sentinel：连接器总览（CEF、Syslog、直接、代理、自定义等）](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-sentinel-the-connectors-grand-cef-syslog-direct-agent/ba-p/803891)博客文章。

## <a name="compare-custom-connector-methods"></a>自定义连接器方法对比

下表对本文介绍的每种创建自定义连接器的方法的基本详细信息进行对比。 如需每种方法的更多详细信息，请选择表中的链接。

|方法说明  |功能 | 无服务器    |复杂性  |
|---------|---------|---------|---------|
|**[Log Analytics 代理](#connect-with-the-log-analytics-agent)** <br>最适合从本地和 IaaS 源收集文件   | 仅限文件收集  |   否      |低         |
|**[Logstash](#connect-with-logstash)** <br>最适合用于本地和 IaaS 源、任何有可用插件的源，以及已熟悉 Logstash 的组织  | 可用的插件以及自定义插件，功能可提供极大的灵活性。   |   否；需要 VM 或 VM 群集才能运行           |   低；支持许多使用插件的方案      |
|[逻辑应用](#connect-with-logic-apps) <br>高成本；避免大量数据 <br>最适合用于低数据量的云源  | 无代码编程可实现有限的灵活性，不支持实现算法。<br><br> 没有任何可用操作已支持你的需求时，创建自定义操作可能会增加复杂性。    |    是         |   低；简单、无代码的开发      |
|**[PowerShell](#connect-with-powershell)** <br>最适合用于建立原型和定期文件上传 | 直接支持文件收集。 <br><br>可以使用 PowerShell 来收集更多的源，但需要编码脚本以及将脚本配置为服务。      |否               |  低       |
|**[Log Analytics API](#connect-with-the-log-analytics-api)** <br>最适合用于实现集成的 ISV，以及用于独特的收集需求   | 支持所有通过代码提供的功能。  | 取决于实现           |     高    |
|**[Azure Functions](#connect-with-azure-functions)** 最适合用于大数据量的云源，以及用于独特的收集需求  | 支持所有通过代码提供的功能。  |  是             |     高；需要编程知识    |
|     |         |                |

> [!TIP]
> 有关将逻辑应用和 Azure Functions 用于同一连接器的比较，请参阅：
> 
> - [将 Fastly Web 应用程序防火墙日志引入 Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/ingest-fastly-web-application-firewall-logs-into-azure-sentinel/ba-p/1238804)
> - Office 365（Azure Sentinel GitHub 社区）：[逻辑应用连接器](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Get-O365Data) | [Azure Function 连接器](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/O365%20Data)
> 

## <a name="connect-with-the-log-analytics-agent"></a>使用 Log Analytics 代理进行连接

如果数据源在文件中交付事件，我们建议你使用 Azure Monitor Log Analytics 代理创建自定义连接器。

- 有关详细信息，请参阅[在 Azure Monitor 中收集自定义日志](../azure-monitor/agents/data-sources-custom-logs.md)。

- 如需此方法的示例，请参阅[在 Azure Monitor 中使用适用于 Linux 的 Log Analytics 代理收集自定义 JSON 数据源](../azure-monitor/agents/data-sources-json.md)。

## <a name="connect-with-logstash"></a>使用 Logstash 进行连接

如果熟悉 [Logstash](https://www.elastic.co/logstash)，你可将 Logstash 与[适用于 Azure Sentinel 的 Logstash 输出插件](connect-logstash.md)结合使用，以创建自定义连接器。

利用 Azure Sentinel Logstash 输出插件，你可使用任何 Logstash 输入和筛选插件，并将 Azure Sentinel 配置为 Logstash 管道的输出。 Logstash 有一个大型插件库，其中的插件支持从各种源（例如，事件中心、Apache Kafka、文件、数据库和云服务）进行输入。 使用筛选插件可以分析事件、筛选不必要的事件、混淆值，等等。

如需使用 Logstash 作为自定义连接器的示例，请参阅：

- [使用 Azure Sentinel 在 AWS 日志中搜寻 Capital One 违规 TTP](https://techcommunity.microsoft.com/t5/azure-sentinel/hunting-for-capital-one-breach-ttps-in-aws-logs-using-azure/ba-p/1019767)（博客）
- [Radware Azure Sentinel 实现指南](https://support.radware.com/ci/okcsFattach/get/1025459_3)

如需实用 Logstash 插件的示例，请参阅：

- [Cloudwatch 输入插件](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-cloudwatch.html)
- [Azure 事件中心插件](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-azure_event_hubs.html)
- [Google 云存储输入插件](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-google_cloud_storage.html)
- [Google_pubsub 输入插件](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-google_pubsub.html)

> [!TIP]
> 利用 Logstash，你还可以使用群集进行大规模数据收集。 有关详细信息，请参阅[大规模使用负载平衡的 Logstash VM](https://techcommunity.microsoft.com/t5/azure-sentinel/scaling-up-syslog-cef-collection/ba-p/1185854)。
>

## <a name="connect-with-logic-apps"></a>使用逻辑应用进行连接

使用 [Azure 逻辑应用](../logic-apps/index.yml)创建适用于 Azure Sentinel 的无服务器自定义连接器。

> [!NOTE]
> 使用逻辑应用创建无服务器的连接器可能很方便，但数据量较大时，将逻辑应用用于连接器的成本可能很高。
>
> 建议仅将此方法用于低数据量的数据源，或者用于扩充数据上传。
>

1. 使用以下触发器之一启动逻辑应用：

    |触发器  |说明  |
    |---------|---------|
    |重复性任务     |   例如，编制逻辑应用的计划，定期从特定文件、数据库或外部 API 检索数据。 <br>有关详细信息，请参阅[在 Azure 逻辑应用中创建、计划和运行重复性任务与工作流](../connectors/connectors-native-recurrence.md)。      |
    |按需触发     | 按需运行逻辑应用，以进行手动数据收集和测试。 <br>有关详细信息，请参阅[使用 HTTPS 终结点调用、触发或嵌套逻辑应用](../logic-apps/logic-apps-http-endpoint.md)。        |
    |HTTP/S 终结点     |  建议用于流式处理，并在源系统可以启动数据传输的情况下使用。 <br>有关详细信息，请参阅[通过 HTTP 或 HTTPS 调用服务终结点](../connectors/connectors-native-http.md)。       |
    |     |         |

1. 使用任何可读取信息的逻辑应用连接器获取事件。 例如：

    - [连接到 REST API](/connectors/custom-connectors/)
    - [连接到 SQL Server](/connectors/sql/)
    - [连接到文件系统](/connectors/filesystem/)

    > [!TIP]
    > 用于 REST API、SQL Server 和文件系统的自定义连接器还支持从本地数据源检索数据。 有关详细信息，请参阅[安装本地数据网关](/connectors/filesystem/)文档。
    >

1. 准备要检索的信息。

    例如，使用[分析 JSON 操作](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action)可以访问 JSON 内容中的属性，因此在为逻辑应用指定输入时，能够从动态内容列表中选择这些属性。

    有关详细信息，请参阅[在 Azure 逻辑应用中执行数据操作](../logic-apps/logic-apps-perform-data-operations.md)。

1. 将数据写入 Log Analytics。

    有关详细信息，请参阅[Azure Log Analytics 数据收集器](/connectors/azureloganalyticsdatacollector/)文档。

有关如何使用逻辑应用为 Azure Sentinel 创建自定义连接器的示例，请参阅：

- [使用数据收集器 API 创建数据管道](/connectors/azureloganalyticsdatacollector/)
- [使用 Webhook 的 Palo Alto Prisma 逻辑应用连接器](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Ingest-Prisma)（Azure Sentinel GitHub 社区）
- [通过计划的激活保护 Microsoft Teams 调用](https://techcommunity.microsoft.com/t5/azure-sentinel/secure-your-calls-monitoring-microsoft-teams-callrecords/ba-p/1574600)（博客）
- [将 AlienVault OTX 威胁指标引入 Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/ingesting-alien-vault-otx-threat-indicators-into-azure-sentinel/ba-p/1086566)（博客）

## <a name="connect-with-powershell"></a>使用 PowerShell 连接

利用 [Upload-AzMonitorLog PowerShell 脚本](https://www.powershellgallery.com/packages/Upload-AzMonitorLog/)，可以使用 PowerShell 通过命令行将事件或上下文信息流式传输到 Azure Sentinel。 这种流式处理可在数据源与 Azure Sentinel 之间有效创建自定义连接器。

例如，以下脚本将 CSV 文件上传到 Azure Sentinel：

``` PowerShell
Import-Csv .\testcsv.csv
| .\Upload-AzMonitorLog.ps1
-WorkspaceId '69f7ec3e-cae3-458d-b4ea-6975385-6e426'
-WorkspaceKey $WSKey
-LogTypeName 'MyNewCSV'
-AddComputerName
-AdditionalDataTaggingName "MyAdditionalField"
-AdditionalDataTaggingValue "Foo"
```

[Upload-AzMonitorLog PowerShell 脚本](https://www.powershellgallery.com/packages/Upload-AzMonitorLog/)使用下列参数：

|参数  |说明  |
|---------|---------|
|WorkspaceId     |   要在其中存储数据的 Azure Sentinel 工作区的 ID。  [查找工作区 ID 和密钥](#find-your-workspace-id-and-key)。  |
|WorkspaceKey     |   要在其中存储数据的 Azure Sentinel 工作区的主密钥或辅助密钥。 [查找工作区 ID 和密钥](#find-your-workspace-id-and-key)。  |
|LogTypeName     |    要在其中存储数据的自定义日志表的名称。 后缀 _CL 会自动添加到表名末尾。  |
|AddComputerName     |   存在此参数时，脚本会将当前计算机名称添加到每条日志记录中名为 Computer 的字段。      |
|TaggedAzureResourceId     | 存在此参数时，脚本会将所有已上传的日志记录与指定的 Azure 资源关联。 <br><br>此项关联使上传的日志记录支持资源上下文查询，并遵循以资源为中心、基于角色的访问控制。       |
|AdditionalDataTaggingName     |      存在此参数时，脚本会在每条日志记录中添加另一个字段，该字段具有配置的名称，并包含你为 AdditionalDataTaggingValue 参数配置的值。 <br><br>在这种情况下，AdditionalDataTaggingValue 不能为空。 |
|AdditionalDataTaggingValue     |   存在此参数时，脚本会在每条日志记录中添加另一个字段，该字段包含配置的值，并具有你为 AdditionalDataTaggingName 参数配置的字段名。 <br><br>如果 AdditionalDataTaggingName 参数为空，但配置了值，则默认字段名为 DataTagging。        |
|     |         |

### <a name="find-your-workspace-id-and-key"></a>查找工作区 ID 和密钥

在 Azure Sentinel 中查找 WorkspaceID 和 WorkspaceKey 参数的详细信息： 

1. 在 Azure Sentinel 中，从左侧选择“设置”，然后选择“工作区设置”选项卡。 

1. 在“开始使用 Log Analytics” > “1 连接数据源”下，选择“Windows 和 Linux 代理管理”。  

1. 在“Windows 服务器”选项卡上，找到你的工作区 ID、主密钥和辅助密钥。
## <a name="connect-with-the-log-analytics-api"></a>使用 Log Analytics API 进行连接

通过使用 Log Analytics 数据收集器 API 直接调用 RESTful 终结点，可以将事件流式传输到 Azure Sentinel。

直接调用 RESTful 终结点需要较多的编程，但更加灵活。

有关详细信息，请参阅 [Log Analytics 数据收集器 API](../azure-monitor/logs/data-collector-api.md)，尤其是下列示例：

- [C#](../azure-monitor/logs/data-collector-api.md#c-sample)
- [Python 2](../azure-monitor/logs/data-collector-api.md#python-2-sample)

## <a name="connect-with-azure-functions"></a>使用 Azure Functions 进行连接

将 Azure Functions 与 RESTful API 和各种编码语言（例如，[PowerShell](../azure-functions/functions-reference-powershell.md)）结合使用，以创建无服务器的自定义连接器。

如需这种方法的示例，请参阅：

- [使用 Azure Function 将 VMware Carbon Black Cloud Endpoint Standard 连接到 Azure Sentinel](./data-connectors-reference.md#vmware-carbon-black-endpoint-standard-preview)
- [使用 Azure Function 将 Okta Single Sign-On 连接到 Azure Sentinel](./data-connectors-reference.md#okta-single-sign-on-preview)
- [使用 Azure Function 将 Proofpoint TAP 连接到 Azure Sentinel](./data-connectors-reference.md#proofpoint-targeted-attack-protection-tap-preview)
- [使用 Azure Function 将 Qualys VM 连接到 Azure Sentinel](./data-connectors-reference.md#qualys-vulnerability-management-vm-preview)
- [引入 XML、CSV 或其他格式的数据](../azure-monitor/logs/create-pipeline-datacollector-api.md#ingesting-xml-csv-or-other-formats-of-data)
- [使用 Azure Sentinel 监视 Zoom](https://techcommunity.microsoft.com/t5/azure-sentinel/monitoring-zoom-with-azure-sentinel/ba-p/1341516)（博客）
- [部署 Function App 以将 Office 365 管理 API 数据引入 Azure Sentinel](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/O365%20Data)（Azure Sentinel GitHub 社区）

## <a name="parse-your-custom-connector-data"></a>分析自定义连接器数据

你可使用自定义连接器的内置分析技术提取相关信息，并在 Azure Sentinel 中填充相关的字段。

例如：

- 如果你已使用 Logstash，请使用 [Grok](https://www.elastic.co/guide/en/logstash/current/plugins-filters-grok.html) 筛选器插件来分析数据。
- 如果你已使用 Azure 函数，请使用代码分析数据。

Azure Sentinel 支持在查询时进行分析。 通过在查询时进行分析，可以按原始格式推送数据，然后在需要时进行分析。 

在查询时进行分析还意味着不需要在创建自定义连接器时提前知道数据的确切结构，甚至无需知道需要提取的信息。 你随时可以分析数据，即使在调查期间也是如此。

有关在查询时进行分析的详细信息，请参阅[分析程序](normalization-about-parsers.md)。

> [!NOTE]
> 更新分析程序还会应用于已引入到 Azure Sentinel 的数据。
> 
## <a name="next-steps"></a>后续步骤

利用下列任何过程，使用已引入到 Azure Sentinel 的数据来保护环境：

- [了解警报](get-visibility.md)
- [可视化和监视数据](monitor-your-data.md)
- [调查事件](investigate-cases.md)
- [检测威胁](detect-threats-built-in.md)
- [自动防御威胁](tutorial-respond-threats-playbook.md)
- [寻找威胁](hunting.md)

此外，了解一个创建自定义连接器以监视 Zoom 的示例：[使用 Azure Sentinel 监视 Zoom](https://techcommunity.microsoft.com/t5/azure-sentinel/monitoring-zoom-with-azure-sentinel/ba-p/1341516)。