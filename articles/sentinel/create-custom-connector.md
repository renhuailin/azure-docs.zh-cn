---
title: 用于创建 Azure Sentinel 自定义连接器的资源 |Microsoft Docs
description: 了解为 Azure Sentinel 创建自定义连接器的可用资源。 方法包括 Log Analytics 代理和 API、Logstash、逻辑应用、PowerShell 和 Azure Functions。
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
ms.openlocfilehash: 25f83088bdc55dbafe7ccf0ff06b0c6595c9ea71
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101724347"
---
# <a name="resources-for-creating-azure-sentinel-custom-connectors"></a>用于创建 Azure Sentinel 自定义连接器的资源

Azure Sentinel 提供 [适用于 Azure 服务和外部解决方案的各种内置连接器](connect-data-sources.md)，还支持来自某些源的引入数据，无需使用专用连接器。

如果无法使用任何可用的现有解决方案将数据源连接到 Azure Sentinel，请考虑创建自己的数据源连接器。

有关支持的连接器的完整列表，请参阅 [Azure Sentinel：连接器总计 (CEF、Syslog、直接、代理、自定义和更多) ](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-sentinel-the-connectors-grand-cef-syslog-direct-agent/ba-p/803891) 博客文章。

## <a name="compare-custom-connector-methods"></a>比较自定义连接器方法

下表比较了有关创建本文所述的自定义连接器的每个方法的基本详细信息。 选择表中的链接，以获取有关每种方法的更多详细信息。

|方法说明  |功能 | 无服务器    |复杂性  |
|---------|---------|---------|---------|
|**[Log Analytics 代理](#connect-with-the-log-analytics-agent)** <br>最适合从本地和 IaaS 源收集文件   | 仅文件收集  |   否      |低         |
|**[Logstash](#connect-with-logstash)** <br>最适合用于本地和 IaaS 源、插件可用的任何源以及已熟悉 Logstash 的组织  | 可用插件、自定义插件、功能可提供极大的灵活性。   |   不需要 VM 或 VM 群集才能运行           |   低级支持多个插件方案      |
|**[逻辑应用](#connect-with-logic-apps)** <br>高成本;避免大容量数据 <br>最适用于低容量云源  | 无代码置备编程可实现有限的灵活性，无需实现算法。<br><br> 如果任何可用操作都已不支持你的要求，则创建自定义操作可能会增加复杂性。    |    是         |   低级简单、无代码置备的开发      |
|**[PowerShell](#connect-with-powershell)** <br>最适合用于原型制作和定期文件上传 | 直接支持文件收集。 <br><br>可以使用 PowerShell 来收集更多源，但需要对脚本进行编码并将其配置为服务。      |否               |  低       |
|**[Log Analytics API](#connect-with-the-log-analytics-api)** <br>最适合用于实现集成和独特集合要求的 Isv   | 支持代码提供的所有功能。  | 取决于实现           |     高    |
|**[Azure Functions](#connect-with-azure-functions)** 最适用于大容量云源和独特集合要求  | 支持代码提供的所有功能。  |  是             |     严重需要编程知识    |
|     |         |                |

> [!TIP]
> 有关对同一连接器使用逻辑应用和 Azure Functions 的比较，请参阅：
> 
> - [将 Fastly Web 应用程序防火墙日志引入 Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/ingest-fastly-web-application-firewall-logs-into-azure-sentinel/ba-p/1238804)
> - Office 365 (azure Sentinel GitHub 社区) ：[逻辑应用连接器](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Get-O365Data)  |  [azure 函数连接器](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/O365%20Data)
> 

## <a name="connect-with-the-log-analytics-agent"></a>与 Log Analytics 代理连接

如果数据源在文件中传递事件，我们建议使用 Azure Monitor Log Analytics 代理创建自定义连接器。

- 有关详细信息，请参阅 [在 Azure Monitor 中收集自定义日志](../azure-monitor/agents/data-sources-custom-logs.md)。

- 有关此方法的示例，请参阅 [在 Azure Monitor 中使用适用于 Linux 的 Log Analytics Agent 收集自定义 JSON 数据源](../azure-monitor/agents/data-sources-json.md)。

## <a name="connect-with-logstash"></a>连接 Logstash

如果熟悉 [Logstash](https://www.elastic.co/logstash)，则可能需要将 Logstash 与用于 [Azure Sentinel 的 Logstash 输出插件](connect-logstash.md) 结合使用，以创建自定义连接器。

使用 Azure Sentinel Logstash Output 插件，可以使用任何 Logstash 输入和筛选插件，并将 Azure Sentinel 配置为 Logstash 管道的输出。 Logstash 有一个大库插件，可用于从各种源（例如事件中心、Apache Kafka、文件、数据库和云服务）进行输入。 使用筛选插件来分析事件，筛选不必要的事件，筛选值，等等。

有关使用 Logstash 作为自定义连接器的示例，请参阅：

- [使用 Azure Sentinel () 博客查找 AWS 日志中的大写一个违规 https](https://techcommunity.microsoft.com/t5/azure-sentinel/hunting-for-capital-one-breach-ttps-in-aws-logs-using-azure/ba-p/1019767)
- [Radware Azure Sentinel 实现指南](https://support.radware.com/ci/okcsFattach/get/1025459_3)

有关有用的 Logstash 插件的示例，请参阅：

- [Cloudwatch 输入插件](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-cloudwatch.html)
- [Azure 事件中心插件](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-azure_event_hubs.html)
- [Google 云存储输入插件](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-google_cloud_storage.html)
- [Google_pubsub 输入插件](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-google_pubsub.html)

> [!TIP]
> 使用 Logstash 还可以使用群集来实现缩放的数据收集。 有关详细信息，请参阅 [大规模使用负载平衡的 LOGSTASH VM](https://techcommunity.microsoft.com/t5/azure-sentinel/scaling-up-syslog-cef-collection/ba-p/1185854)。
>

## <a name="connect-with-logic-apps"></a>与逻辑应用连接

使用 [Azure 逻辑应用](../logic-apps/index.yml) 创建无服务器的自定义连接器用于 Azure Sentinel。

> [!NOTE]
> 使用逻辑应用创建无服务器连接器可能会很方便，但对于大量数据，使用连接器的逻辑应用可能会耗费大量的数据。
>
> 建议仅将此方法用于低容量数据源，或浓缩数据上传。
>

1. **使用以下触发器之一启动逻辑应用**：

    |触发器  |说明  |
    |---------|---------|
    |**定期任务**     |   例如，将逻辑应用计划为从特定文件、数据库或外部 Api 定期检索数据。 <br>有关详细信息，请参阅 [在 Azure 逻辑应用中创建、计划和运行定期任务和工作流](../connectors/connectors-native-recurrence.md)。      |
    |**按需触发**     | 按需运行逻辑应用，以进行手动数据收集和测试。 <br>有关详细信息，请参阅  [使用 HTTPS 终结点调用、触发或嵌套逻辑应用](../logic-apps/logic-apps-http-endpoint.md)。        |
    |**HTTP/S 终结点**     |  建议用于流式处理，并且源系统可以启动数据传输。 <br>有关详细信息，请参阅 [通过 HTTP 或 HTTPs 调用服务终结点](../connectors/connectors-native-http.md)。       |
    |     |         |

1. **使用任何可读取信息的逻辑应用连接器获取事件**。 例如：

    - [连接到 REST API](/connectors/custom-connectors/)
    - [连接到 SQL Server](/connectors/sql/)
    - [连接到文件系统](/connectors/filesystem/)

    > [!TIP]
    > 用于 REST Api、SQL Server 和文件系统的自定义连接器还支持从本地数据源检索数据。 有关详细信息，请参阅 [安装本地数据网关](/connectors/filesystem/) 文档。
    >

1. **准备要检索的信息**。

    例如，使用 " [分析 json" 操作](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action) 可以访问 JSON 内容中的属性，使你能够在为逻辑应用指定输入时从动态内容列表中选择这些属性。

    有关详细信息，请参阅 [在 Azure 逻辑应用中执行数据操作](../logic-apps/logic-apps-perform-data-operations.md)。

1. 将 **数据写入 Log Analytics**。

    有关详细信息，请参阅 [Azure Log Analytics 数据收集器](/connectors/azureloganalyticsdatacollector/) 文档。

有关如何使用逻辑应用为 Azure Sentinel 创建自定义连接器的示例，请参阅：

- [使用数据收集器 API 创建数据管道](/connectors/azureloganalyticsdatacollector/)
- [Palo Alto Prisma 逻辑应用连接器使用 webhook](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Ingest-Prisma) (Azure Sentinel GitHub 社区) 
- [通过计划的激活 (博客) 保护你的 Microsoft 团队调用](https://techcommunity.microsoft.com/t5/azure-sentinel/secure-your-calls-monitoring-microsoft-teams-callrecords/ba-p/1574600)
- [引入 AlienVault OTX (博客中的威胁指标](https://techcommunity.microsoft.com/t5/azure-sentinel/ingesting-alien-vault-otx-threat-indicators-into-azure-sentinel/ba-p/1086566)) 
- [将 PROOFPOINT ON DEMAND 点击日志发送到 Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/sending-proofpoint-tap-logs-to-azure-sentinel/ba-p/767727) (博客) 


## <a name="connect-with-powershell"></a>使用 PowerShell 连接

使用 [AzMonitorLog powershell 脚本](https://www.powershellgallery.com/packages/Upload-AzMonitorLog/) 可以通过命令行使用 powershell 将事件或上下文信息流式传输到 Azure Sentinel。 此流式处理会有效地在数据源和 Azure Sentinel 之间创建自定义连接器。

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

[AzMonitorLog PowerShell 脚本](https://www.powershellgallery.com/packages/Upload-AzMonitorLog/)脚本使用以下参数：

|参数  |说明  |
|---------|---------|
|**WorkspaceId**     |   你的 Azure Sentinel 工作区 ID，你将在其中存储数据。  [查找工作区 ID 和密钥](#find-your-workspace-id-and-key)。  |
|**WorkspaceKey**     |   用于存储数据的 Azure Sentinel 工作区的主密钥或辅助密钥。 [查找工作区 ID 和密钥](#find-your-workspace-id-and-key)。  |
|**LogTypeName**     |    要在其中存储数据的自定义日志表的名称。 **_CL** 的后缀将自动添加到表名称的末尾。  |
|**AddComputerName**     |   如果此参数存在，则脚本会将当前计算机名称添加到名为 **computer** 的字段中的每个日志记录。      |
|**TaggedAzureResourceId**     | 如果此参数存在，则脚本会将所有已上传的日志记录与指定的 Azure 资源关联。 <br><br>此关联为资源上下文查询启用上传的日志记录，并遵循以资源为中心的基于角色的访问控制。       |
|**AdditionalDataTaggingName**     |      如果此参数存在，该脚本将向每个日志记录添加另一个字段，其中包含已配置的名称，以及为 **AdditionalDataTaggingValue** 参数配置的值。 <br><br>在这种情况下， **AdditionalDataTaggingValue** 不能为空。 |
|**AdditionalDataTaggingValue**     |   如果此参数存在，该脚本将向每个日志记录添加另一个字段，其中包含配置的值和为 **AdditionalDataTaggingName** 参数配置的字段名称。 <br><br>如果 **AdditionalDataTaggingName** 参数为空，但配置了值，则默认字段名称为 **DataTagging**。       |
|     |         |

### <a name="find-your-workspace-id-and-key"></a>查找工作区 ID 和密钥

在 Azure Sentinel 中查找 **WorkspaceID** 和 **WorkspaceKey** 参数的详细信息：

1. 在 Azure Sentinel 中，选择左侧的 " **设置** "，然后选择 " **工作区设置** " 选项卡。

1. 在 "**开始使用 Log Analytics**  >  **1 连接数据源**" 下，选择 " **Windows 和 Linux 代理管理**"。

1. 在 " **Windows 服务器** " 选项卡上找到你的工作区 ID、主密钥和辅助密钥。
## <a name="connect-with-the-log-analytics-api"></a>与 Log Analytics API 连接

可以通过使用 Log Analytics 数据收集器 API 直接调用 RESTful 终结点，将事件流式传输到 Azure Sentinel。

直接调用 RESTful 终结点需要更多编程，它还提供更多的灵活性。

有关详细信息，请参阅 [Log Analytics 数据收集器 API](../azure-monitor/logs/data-collector-api.md)，尤其是以下示例：

- [C#](../azure-monitor/logs/data-collector-api.md#c-sample)
- [Python 2](../azure-monitor/logs/data-collector-api.md#python-2-sample)

## <a name="connect-with-azure-functions"></a>连接 Azure Functions

结合使用 Azure Functions 和各种编码语言（例如 [PowerShell](../azure-functions/functions-reference-powershell.md)）来创建无服务器的自定义连接器。

有关此方法的示例，请参阅：

- [通过 Azure Function 将 VMware 碳黑色云终结点标准连接到 Azure Sentinel](connect-vmware-carbon-black.md)
- [将 Okta Single Sign-On 连接到 Azure Sentinel with Azure Function](connect-okta-single-sign-on.md)
- [通过 Azure Function 将 Proofpoint on demand 点击连接到 Azure Sentinel](connect-proofpoint-tap.md)
- [将 Qualys VM 与 azure Function 连接到 Azure Sentinel](connect-qualys-vm.md)
- [引入 XML、CSV 或其他格式的数据](../azure-monitor/logs/create-pipeline-datacollector-api.md#ingesting-xml-csv-or-other-formats-of-data)
- [利用 Azure Sentinel 监视缩放](https://techcommunity.microsoft.com/t5/azure-sentinel/monitoring-zoom-with-azure-sentinel/ba-p/1341516) (博客) 
- [部署用于将 Office 365 管理 API 数据导入 Azure sentinel](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/O365%20Data) (Azure sentinel GitHub 社区的 Function App) 

## <a name="parse-your-custom-connector-data"></a>分析自定义连接器数据

你可以使用自定义连接器的内置分析技术来提取相关信息并在 Azure Sentinel 中填充相关字段。

例如：

- **如果已使用 Logstash**，请使用 [Grok](https://www.elastic.co/guide/en/logstash/current/plugins-filters-grok.html) 筛选器插件来分析你的数据。
- **如果已使用 Azure 函数**，请使用代码分析数据。 有关详细信息，请参阅 [分析器](normalization.md#parsers)。

Azure Sentinel 支持在查询时进行分析。 在查询时进行分析后，可以按原始格式推送数据，然后在需要时进行分析。

在查询时进行分析还意味着无需事先知道数据的确切结构、创建自定义连接器，甚至需要提取信息。 即使是在调查过程中，你也可随时分析你的数据。

> [!NOTE]
> 更新分析器还适用于已引入到 Azure Sentinel 中的数据。
> 
## <a name="next-steps"></a>后续步骤

使用 Azure Sentinel 中的数据引入，通过以下任意过程保护环境：

- [了解警报](quickstart-get-visibility.md)
- [可视化和监视你的数据](tutorial-monitor-your-data.md)
- [调查事件](tutorial-investigate-cases.md)
- [检测威胁](tutorial-detect-threats-built-in.md)
- [自动防御威胁](tutorial-respond-threats-playbook.md)
- [寻找威胁](hunting.md)