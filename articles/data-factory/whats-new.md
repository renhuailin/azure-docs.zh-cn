---
title: Azure 数据工厂的新增功能
description: 本“新增功能”页重点介绍了 Azure 数据工厂的新功能和改进。
author: pennyzhou-msft
ms.author: xupzhou
ms.reviewer: xupzhou
ms.service: data-factory
ms.topic: overview
ms.date: 07/14/2021
ms.openlocfilehash: fe4c5fb72ce3cd32e14bad211683e01ac6edf3ab
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "114341470"
---
# <a name="whats-new-in-azure-data-factory"></a>Azure 数据工厂的新增功能

Azure 数据工厂会不断改进。 为了让大家随时了解最新的开发成果，本文将提供以下方面的信息：

- 最新版本
- 已知问题
- Bug 修复
- 已弃用的功能
- 更改计划

本页每月更新，请不时回来查看。 

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





