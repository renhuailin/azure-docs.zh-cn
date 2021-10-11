---
title: Azure 数据工厂的新增功能
description: 本页重点介绍 Azure 数据工厂的新功能和最新改进。 Azure 数据工厂是一项托管云服务，旨在用于复杂的混合提取-转换-加载 (ETL)、提取-加载-转换 (ELT) 和数据集成项目。
author: pennyzhou-msft
ms.author: xupzhou
ms.service: data-factory
ms.subservice: concepts
ms.topic: overview
ms.date: 07/14/2021
ms.openlocfilehash: 090b0eaca0e46969aa98e6ac7878da3333602ab6
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129533487"
---
# <a name="whats-new-in-azure-data-factory"></a>Azure 数据工厂的新增功能

Azure 数据工厂服务正在持续改进。 为了让大家随时了解最新的开发成果，本文将提供以下方面的信息：

- 最新版本
- 已知问题
- Bug 修复
- 已弃用的功能
- 更改计划

本页面每月更新，请不时回来查看。 

## <a name="september-2021"></a>2021 年 9 月
<br>
<table>
<tr><td>服务类别<b></b></td><td><b>服务改进</b></td><td><b>详细信息</b></td></tr>
  <tr><td><b>持续集成和交付 (CI/CD)</b></td><td>扩展的 CI/CD 功能</td><td>现在可以基于 Azure 数据工厂中的任何其他分支创建新的 Git 分支。<br><a href="source-control.md#version-control">了解详细信息</a></td></tr>
<tr><td rowspan=3><b>数据移动</b></td><td>适用于 Oracle 源的 Amazon 关系数据库服务 (RDS)</td><td>Amazon RDS for Oracle 源连接器现已在 Azure 数据工厂 和 Azure Synapse 中可用。<br><a href="connector-amazon-rds-for-oracle.md">了解详细信息</a></td></tr>
<tr><td>Amazon RDS for SQL Server 源</td><td>Amazon RDS for SQL Server 源连接器现已在 Azure 数据工厂 和 Azure Synapse 中可用。<br><a href="connector-amazon-rds-for-sql-server.md">了解详细信息</a></td></tr>
<tr><td>支持从 Azure Database for PostgreSQL 进行并行复制</td><td>Azure Database for PostgreSQL 连接器现在支持并行复制操作。<br><a href="connector-azure-database-for-postgresql.md">了解详细信息</a></td></tr>
<tr><td rowspan=3><b>数据流</b></td><td>使用 Azure Data Lake Storage (ADLS) Gen2 执行预处理和后处理命令</td><td>现在可以使用数据流中的 ADLS Gen2 接收器执行 Hadoop 分布式文件系统 (HDFS) 预处理和后处理命令<br><a href="connector-azure-data-lake-storage.md#pre-processing-and-post-processing-commands">了解详细信息</a></td></tr>
<tr><td>为 Azure Integration Runtime (IR) 的现有实例编辑数据流属性</td><td>Azure Integration Runtime (IR) 已更新，允许编辑现有 IR 的数据流属性。 现在无需创建新的 Azure IR 即可修改数据流计算属性。<br><a href="concepts-integration-runtime.md">了解详细信息</a></td></tr>
<tr><td>用于改进管道活动执行启动时间的 Azure Synapse 的 TTL 设置</td><td>Azure Synapse Analytics 已将 TTL 添加到 Azure Integration Runtime，使数据流管道活动能够在几秒钟内开始执行，从而大大缩短数据流管道的运行时间。<br><a href="control-flow-execute-data-flow-activity.md#data-flow-integration-runtime">了解详细信息</a></td></tr>
<tr><td><b>集成运行时</b></td><td>Azure 数据工厂托管 vNet 正式发布</td><td>现在，可以将 Azure Integration Runtime 作为托管虚拟网络的一部分进行预配，并利用专用终结点安全地连接到受支持的数据存储。 数据流量通过 Azure 专用链接，这些链接提供到数据源的安全连接。 此外，它还可防止数据外泄到公共 Internet。<br><a href="managed-virtual-network-private-endpoint.md">了解详细信息</a></td></tr>
<tr><td><b>业务流程</b></td><td>为数据管道操作和提供 SLA</td><td>新的运行时间管道运行指标与数据工厂警报相结合，使数据管道开发人员能够更好地向客户提供 SLA：你告诉我们管道应该运行多长时间，当管道运行时间超过预期时，我们会主动通知你。<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/operationalize-and-provide-sla-for-data-pipelines/ba-p/2767768">了解详细信息</a></td></tr>
</table>

## <a name="august-2021"></a>2021 年 8 月
<br>
<table>
<tr><td>服务类别<b></b></td><td><b>服务改进</b></td><td><b>详细信息</b></td></tr>
  <tr><td><b>持续集成和交付 (CI/CD)</b></td><td>Azure 政府和 Azure 中国中 GitHub 支持的 CICD 改进</td><td>我们为 Azure 美国政府版和 Azure 中国添加了对 GitHub 的支持。<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/cicd-improvements-with-github-support-in-azure-government-and/ba-p/2686918">了解详细信息</a></td></tr>
<tr><td rowspan=2><b>数据移动</b></td><td>Azure Cosmos DB 的用于 MongoDB 连接器的 API 在 Azure 数据工厂中支持 3.6 和 4.0 版本</td><td>Azure 数据工厂 Cosmos DB 的用于 MongoDB 连接器的 API 现在支持服务器版本 3.6 和 4.0。<br><a href="connector-azure-cosmos-db-mongodb-api.md">了解详细信息</a></td></tr>
<tr><td>增强通过使用 COPY 语句将数据加载到 Azure Synapse Analytics 的功能</td><td>Azure 数据工厂 Azure Synapse Analytics 连接器现在支持暂存复制功能以及使用 *.* 作为 COPY 语句的 wildcardFilename 来复制源。<br><a href="connector-azure-sql-data-warehouse.md#use-copy-statement">了解详细信息</a></td></tr>
<tr><td><b>数据流</b></td><td>REST 终结点可用作数据流中的源和接收器</td><td>现在，Azure 数据工厂和 Azure Synapse Analytics 中的数据流支持 REST 终结点作为源和接收器，同时完全支持 JSON 和 XML 有效负载。<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/rest-source-and-sink-now-available-for-data-flows/ba-p/2596484">了解详细信息</a></td></tr>
<tr><td><b>集成运行时</b></td><td>诊断工具可用于自承载集成运行时</td><td>用于自承载集成运行时的诊断工具旨在提供更好的用户体验并帮助用户查找潜在问题。 该工具在自承载集成运行时计算机上运行一系列测试方案，并且每个方案都有针对常见问题的典型运行状况检查案例。<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/diagnostic-tool-for-self-hosted-integration-runtime/ba-p/2634905">了解详细信息</a></td></tr>
<tr><td><b>业务流程</b></td><td>具有高级筛选选项的自定义事件触发器已正式发布</td><td>现在可以创建一个触发器，用于响应发布到事件网格的自定义主题。 此外，可以利用高级筛选对要响应的事件进行精细控制。<br><a href="how-to-create-custom-event-trigger.md">了解详细信息</a></td></tr>
</table>

## <a name="july-2021"></a>2021 年 7 月
<br>
<table>
<tr><td>服务类别<b></b></td><td><b>服务改进</b></td><td><b>详细信息</b></td></tr>
<tr><td><b>数据移动</b></td><td>10 分钟内在 ADF 复制数据工具上获取元数据驱动的数据引入管道（公共预览版）</td><td>通过此操作，可以在 10 分钟内在复制数据工具（公共预览版）上使用元数据驱动的方法生成大规模数据复制管道。<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/get-metadata-driven-data-ingestion-pipelines-on-adf-within-10/ba-p/2528219">了解详细信息</a></td></tr>
<tr><td><b>数据流</b></td><td>在数据流转换函数中添加的新映射函数</td><td>添加了一组新的数据流转换函数，使数据工程师能够轻松生成、读取和更新映射数据类型和复杂映射结构。<br><a href="data-flow-expression-functions.md#map-functions">了解详细信息</a></td></tr>
<tr><td><b>集成运行时</b></td><td>Azure 数据工厂托管 VNET（公共预览版）中提供了 5 个新区域</td><td>这 5 个新区域（中国东部 2、中国北部 2、US Gov 亚利桑那州、US Gov 德克萨斯州、US Gov 弗吉尼亚州）在 Azure 数据工厂托管虚拟网络（公共预览版）中可用。<br><a href="managed-virtual-network-private-endpoint.md#azure-data-factory-managed-virtual-network-is-available-in-the-following-azure-regions">了解详细信息</a></td></tr>
<tr><td rowspan=2><b>开发人员工作效率</b></td><td>重新设计了 ADF 主页，添加了一些会话</td><td>重新设计了数据工厂主页，具有更好的对比度和重排功能。 此外，主页上还引入了一些部分，可帮助你提高数据集成过程中的工作效率。<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/the-new-and-refreshing-data-factory-home-page/ba-p/2515076">了解详细信息</a></td></tr>
<tr><td>Azure 数据工厂工作室的新登陆页面</td><td>Azure 门户中“数据工厂”边栏选项卡的登陆页。<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/the-new-and-refreshing-data-factory-home-page/ba-p/2515076">了解详细信息</a></td></tr>
</table>

## <a name="june-2021"></a>2021 年 6 月
<br>
<table>
<tr><td>服务类别<b></b></td><td><b>服务改进</b></td><td><b>详细信息</b></td></tr>
<tr><td rowspan=4 valign="middle"><b>数据移动</b></td><td>提供 Azure 数据工厂复制数据工具的新用户体验</td><td>重新设计的复制数据工具现已推出，并改进了数据引入体验。<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/a-re-designed-copy-data-tool-experience/ba-p/2380634">了解详细信息</a></td></tr>
<tr><td>支持将 MongoDB 和 MongoDB Atlas 用作源和接收器</td><td>借助此项改进，可以在任何受支持的数据存储与 MongoDB 或 MongoDB Atlas 数据库之间复制数据。<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/new-connectors-available-in-adf-mongodb-and-mongodb-atlas-are/ba-p/2441482">了解详细信息</a></td></tr>
<tr><td>用作源和接收器的 Azure SQL 数据库、Azure SQL 托管实例和 SQL Server 连接器支持 Always Encrypted</td><td>Always Encrypted 可用于 Azure SQL 数据库、Azure SQL 托管实例和 SQL Server 连接器的 Azure 数据工厂中的复制活动。<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/azure-data-factory-copy-now-supports-always-encrypted-for-both/ba-p/2461346">了解详细信息</a></td></tr>
<tr><td>在接收到 ADLS Gen2 或 Azure Blob 时，支持在复制活动中设置自定义元数据</td><td>在写入到 ADLS Gen2 或 Azure Blob 时，复制活动支持设置自定义元数据，或将源文件的上次已修改信息存储为元数据。<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/support-setting-custom-metadata-when-writing-to-blob-adls-gen2/ba-p/2545506#M490">了解详细信息</a></td></tr>
<tr><td rowspan=4 valign="middle"><b>数据流</b></td><td>现在支持将 SQL Server 用作数据流中的源和接收器</td><td>现在支持将 SQL Server 用作数据流中的源和接收器。 单击以下链接可了解如何使用 Azure Integration Runtime 托管 VNET 功能配置网络，以便与本地 SQL Server 和基于云 VM 的实例通信。<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/new-data-flow-connector-sql-server-as-source-and-sink/ba-p/2406213">了解详细信息</a></td></tr>
<tr><td>现在，默认将为所有新的 Azure Integration Runtime 启用数据流群集快速重用</td><td>ADF 很高兴地宣布，热门的数据流快速启动重用功能已正式发布。 现在，所有新的 Azure Integration Runtime 默认将启用快速重用。<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/how-to-startup-your-data-flows-execution-in-less-than-5-seconds/ba-p/2267365">了解详细信息</a></td></tr>
<tr><td>ADF 中的 Power Query 活动公共预览版</td><td>现在，可以使用 Azure 数据工厂数据整理来生成复杂字段到 Power Query 接收器的映射。 为了适应此项更新，管道中的 Power Query（预览版）活动内现已配置接收器。<br><a href="wrangling-tutorial.md">了解详细信息</a></td></tr>
<tr><td>更新了 Azure 数据工厂中的数据流监视 UI</td><td>Azure 数据工厂为监视 UI 提供了新的更新，以便更轻松地查看数据流 ETL 作业执行，并快速确定哪些方面需要优化性能。<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/updated-data-flows-monitoring-ui-in-adf-amp-synapse/ba-p/2432199">了解详细信息</a></td></tr>
<tr><td><b>SQL Server Integration Services (SSIS)</b></td><td>只需执行 3 个简单步骤，就能在 Azure 数据工厂中使用 SSIS 在任意位置运行任何 SQL 语句</td><td>以下链接提供了 3 个简单步骤，用于在 Azure 数据工厂中使用 SSIS 在任意位置运行任何 SQL 语句/脚本。<ol><li>准备自承载集成运行时/SSIS 集成运行时。</li><li>在 Azure 数据工厂管道中准备“执行 SSIS 包”活动。</li><li>在自承载集成运行时/SSIS 集成运行时中运行“执行 SSIS 包”活动。</li></ol><a href="https://techcommunity.microsoft.com/t5/sql-server-integration-services/run-any-sql-anywhere-in-3-easy-steps-with-ssis-in-azure-data/ba-p/2457244">了解详细信息</a></td></tr>
</table>

## <a name="more-information"></a>详细信息

- [博客 - Azure 数据工厂](https://techcommunity.microsoft.com/t5/azure-data-factory/bg-p/AzureDataFactoryBlog)
- [Stackoverflow 论坛](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Twitter](https://twitter.com/AzDataFactory?ref_src=twsrc%5Egoogle%7Ctwcamp%5Eserp%7Ctwgr%5Eauthor)
- [视频](https://www.youtube.com/channel/UC2S0k7NeLcEm5_IhHUwpN0g/featured)
