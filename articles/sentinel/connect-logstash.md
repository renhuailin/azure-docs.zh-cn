---
title: 通过 Logstash 将数据源连接到 Azure Sentinel | Microsoft Docs
description: 了解如何使用 Logstash 将日志从外部数据源转发到 Azure Sentinel。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/10/2020
ms.author: yelevin
ms.openlocfilehash: 37b09e09207b91e966fef7249cc38a2f13fe4e68
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747146"
---
# <a name="use-logstash-to-connect-data-sources-to-azure-sentinel"></a>使用 Logstash 将数据源连接到 Azure Sentinel

> [!IMPORTANT]
> 使用 Logstash 输出插件的数据引入目前为公共预览版。 此功能不附带服务级别协议，不建议将其用于生产工作负载。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

对 Logstash 数据收集引擎使用 Azure Sentinel 的输出插件时，可以通过 Logstash 将所需的任何类型的日志直接发送到 Azure Sentinel 中的 Log Analytics 工作区。 日志将发送到使用输出插件定义的自定义表格。

若要详细了解如何使用 Logstash 数据收集引擎，请参阅 [Logstash 入门](https://www.elastic.co/guide/en/logstash/current/getting-started-with-logstash.html)。

## <a name="overview"></a>概述

### <a name="architecture-and-background"></a>体系结构和背景

![Logstash 体系结构的示意图。](./media/connect-logstash/logstash-architecture.png)

Logstash 引擎由三个组件组成：

- 输入插件：以自定义的方式从各种源收集数据。
- 筛选器插件：根据指定的条件对数据进行处理和规范化。
- 输出插件：以自定义的方式将收集和处理的数据发送到各种目标。

> [!NOTE]
> - Microsoft 仅支持此处讨论的由 Azure Sentinel 提供的 Logstash 输出插件。 此插件的当前版本为 v1.0.0，发布于 2020 年 8 月 25 日。 可以针对有关输出插件的任何问题[提交支持票证](https://ms.portal.azure.com/#create/Microsoft.Support)。
>
> - Microsoft 不支持第三方输出插件，或其他任何类型的 Logstash 插件或组件。
>
> - Azure Sentinel Logstash 输出插件仅支持 Logstash 版本 7.0 到 7.9。

Logstash 的 Azure Sentinel 输出插件使用 Log Analytics HTTP 数据收集器 REST API 将 JSON 格式的数据发送到 Log Analytics 工作区。 数据将引入自定义日志。

- 详细了解 [Log Analytics REST API](/rest/api/loganalytics/create-request)。
- 详细了解[自定义日志](../azure-monitor/agents/data-sources-custom-logs.md)。

## <a name="deploy-the-azure-sentinel-output-plugin-in-logstash"></a>在 Logstash 部署 Azure Sentinel 输出插件

### <a name="step-1-installation"></a>步骤 1：安装

Logstash 集合中提供了 Azure Sentinel 输出插件。

- 请按照 Logstash [使用插件](https://www.elastic.co/guide/en/logstash/current/working-with-plugins.html)文档中的说明安装 [microsoft-logstash-output-azure-loganalytics](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/microsoft-logstash-output-azure-loganalytics) 插件。
   
- 如果 Logstash 系统无法访问 Internet，请按照 Logstash [脱机插件管理](https://www.elastic.co/guide/en/logstash/current/offline-plugins.html)文档中的说明准备和使用脱机插件包。 （这将需要构建另一个具有 Internet 访问权限的 Logstash 系统。）

### <a name="step-2-configuration"></a>步骤 2：配置

使用 Logstash [配置文件的结构](https://www.elastic.co/guide/en/logstash/current/configuration-file-structure.html)文档中的信息，并通过以下键和值将 Azure Sentinel 输出插件添加配置中。 （正确的的配置文件语法显示在表格后。）

| 字段名称 | 数据类型 | 说明 |
|----------------|---------------|-----------------|
| `workspace_id` | 字符串 | 输入工作区 ID GUID（参见“提示”）。 |
| `workspace_key` | 字符串 | 输入工作区主键 GUID（参见“提示”）。 |
| `custom_log_table_name` | string | 设置将引入日志的表格的名称。 只能为每个输出插件配置一个表格名称。 日志表格将显示在 Azure Sentinel 的“日志”下的“表格”中的“自定义日志”类别中，并带有 `_CL` 后缀  。 |
| `endpoint` | string | 可选字段。 默认情况下，这是 Log Analytics 终结点。 使用此字段可设置备用终结点。 |
| `time_generated_field` | string | 可选字段。 此属性将替代 Log Analytics 中默认的 TimeGenerated 字段。 在数据源中输入时间戳字段的名称。 该字段中的数据必须遵循 ISO 8601 格式 (`YYYY-MM-DDThh:mm:ssZ`) |
| `key_names` | array | 输入 Log Analytics 输出架构字段的列表。 每个列表项都应加单引号，各项之间以逗号分隔，并且整个列表用方括号括起来。 请参阅以下示例。 |
| `plugin_flush_interval` | 数字 | 可选字段。 设置此项以定义向 Log Analytics 传输信息的最大时间间隔（以秒为单位）。 默认值为 5。 |
| `amount_resizing` | boolean | True 或 False。 启用或禁用自动缩放机制，该机制根据收到的日志数据量调整消息缓冲区大小。 |
| `max_items` | 数字 | 可选字段。 仅在 `amount_resizing` 设置为“false”时适用。 用于设置（记录中的）消息缓冲区大小的上限。 默认为 2000。  |
| `azure_resource_id` | string | 可选字段。 定义数据所在的 Azure 资源的 ID。 <br>如果使用[资源上下文 RBAC](resource-context-rbac.md)提供仅针对特定数据的访问，则资源 ID 值特别有用。 |
| | | |

> [!TIP]
> - 在“代理管理”下可找到工作区资源中的工作区 ID 和主键。
> - 但是，由于凭据和其他敏感信息在配置文件中以明文方式，这不符合安全最佳做法，因此强烈建议你使用 Logstash 密钥存储，以便安全地在配置中包括工作区 ID 和工作区主密钥。    请参阅 [Elastic 文档](https://www.elastic.co/guide/en/logstash/current/getting-started-with-logstash.html)来获取说明。

#### <a name="sample-configurations"></a>示例配置

下面是一些使用几个不同选项的示例配置。

- 使用 filebeat 输入管道的基本配置：

   ```ruby
    input {
        beats {
            port => "5044"
        }
    }
    filter {
    }
    output {
        microsoft-logstash-output-azure-loganalytics {
          workspace_id => "4g5tad2b-a4u4-147v-a4r7-23148a5f2c21" # <your workspace id>
          workspace_key => "u/saRtY0JGHJ4Ce93g5WQ3Lk50ZnZ8ugfd74nk78RPLPP/KgfnjU5478Ndh64sNfdrsMni975HJP6lp==" # <your workspace key>
          custom_log_table_name => "tableName"
        }
    }
   ```
    
- 使用 tcp 输入管道的基本配置：

   ```ruby
    input {
        tcp {
            port => "514"
            type => syslog #optional, will effect log type in table
        }
    }
    filter {
    }
    output {
        microsoft-logstash-output-azure-loganalytics {
          workspace_id => "4g5tad2b-a4u4-147v-a4r7-23148a5f2c21" # <your workspace id>
          workspace_key => "u/saRtY0JGHJ4Ce93g5WQ3Lk50ZnZ8ugfd74nk78RPLPP/KgfnjU5478Ndh64sNfdrsMni975HJP6lp==" # <your workspace key>
          custom_log_table_name => "tableName"
        }
    }
   ```
  
- 高级配置：

   ```ruby    
    input {
        tcp {
            port => 514
            type => syslog
        }
    }
    filter {
        grok {
            match => { "message" => "<%{NUMBER:PRI}>1 (?<TIME_TAG>[0-9]{4}-[0-9]{1,2}-[0-9]{1,2}T[0-9]{1,2}:[0-9]{1,2}:[0-9]{1,2})[^ ]* (?<HOSTNAME>[^ ]*) %{GREEDYDATA:MSG}" }
        }
    }
    output {
        microsoft-logstash-output-azure-loganalytics {
            workspace_id => "<WS_ID>"
            workspace_key => "${WS_KEY}"
            custom_log_table_name => "logstashCustomTable"
            key_names => ['PRI','TIME_TAG','HOSTNAME','MSG']
            plugin_flush_interval => 5
        }
    } 
   ```

   > [!NOTE]
   > 请访问输出插件 [GitHub 存储库](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/microsoft-logstash-output-azure-loganalytics)，详细了解其内部工作原理、配置和性能设置。

### <a name="step-3-restart-logstash"></a>步骤 3：重启 Logstash

### <a name="step-4-view-incoming-logs-in-azure-sentinel"></a>步骤 4：查看 Azure Sentinel 中的传入日志

1. 验证消息是否发送到输出插件。

1. 在 Azure Sentinel 导航菜单中单击“日志”。 在“表格”标题下，展开“自定义日志”类别 。 找到在配置中指定的表格的名称（带有 `_CL` 后缀）并单击该名称。

   :::image type="content" source="./media/connect-logstash/logstash-custom-logs-menu.png" alt-text="Logstash 自定义日志的屏幕截图。":::

1. 若要查看表格中的记录，请将表格名称用作架构来查询表。

   :::image type="content" source="./media/connect-logstash/logstash-custom-logs-query.png" alt-text="Logstash 自定义日志查询的屏幕截图。":::

## <a name="monitor-output-plugin-audit-logs"></a>监视输出插件审核日志

若要监视 Azure Sentinel 输出插件的连接和活动，请启用相应的 Logstash 日志文件。 有关日志文件位置的信息，请参阅 [Logstash 目录布局](https://www.elastic.co/guide/en/logstash/current/dir-layout.html#dir-layout)文档。

如果在此日志文件中看不到任何数据，请（通过输入插件和筛选器插件）在本地生成并发送一些事件以确保输出插件在接收数据。 Azure Sentinel 将支持只与输出插件相关的问题。

## <a name="next-steps"></a>后续步骤

本文档介绍了如何使用 Logstash 将外部数据源连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 使用[内置](detect-threats-built-in.md)或[自定义](detect-threats-custom.md)规则开始通过 Azure Sentinel 检测威胁。
