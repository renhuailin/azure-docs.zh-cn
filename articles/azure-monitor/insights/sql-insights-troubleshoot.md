---
title: 排查 SQL 见解（预览版）问题
description: 了解如何在 Azure Monitor 中排查 SQL 见解问题。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/04/2021
ms.openlocfilehash: b76e08103b7af7591e7f71d3fe40e1e018a45665
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128610648"
---
# <a name="troubleshoot-sql-insights-preview"></a>排查 SQL 见解（预览版）问题
若要排查 SQL 见解中的数据收集问题，请在“管理配置文件”选项卡上检查监视虚拟机的状态。状态包括：

- **正在收集** 
- **未收集** 
- **收集时出现错误** 
 
选择状态可查看有助于解决问题的日志和更多详细信息。 

:::image type="content" source="media/sql-insights-enable/monitoring-machine-status.png" alt-text="屏幕截图，显示了监视虚拟机状态。":::

## <a name="status-not-collecting"></a>状态：未收集 
如果在过去 10 分钟内 SQL 的“InsightsMetrics”中没有任何数据，则监视虚拟机的状态为“未收集”。 

> [!NOTE]
> 请确保你在尝试从[受支持的 SQL 版本](sql-insights-overview.md#supported-versions)收集数据。 例如，尝试使用有效的配置文件和连接字符串从不受支持的 Azure SQL 数据库版本收集数据会导致“未收集”状态。

SQL 见解使用以下查询来检索此信息：

```kusto
InsightsMetrics 
    | extend Tags = todynamic(Tags) 
    | extend SqlInstance = tostring(Tags.sql_instance) 
    | where TimeGenerated > ago(10m) and isnotempty(SqlInstance) and Namespace == 'sqlserver_server_properties' and Name == 'uptime' 
```

检查 Telegraf 中是否有任何日志可帮助确定问题的根本原因。 如果有日志条目，则可以选择“未收集”并查看日志和故障排除信息以排查常见问题。 

如果没有日志条目，则必须查看监视虚拟机上的日志来排查两个虚拟机扩展安装的以下服务的问题：

- Microsoft.Azure.Monitor.AzureMonitorLinuxAgent 
  - 服务：mdsd 
- Microsoft.Azure.Monitor.Workloads.Workload.WLILinuxExtension 
  - 服务：wli 
  - 服务：ms-telegraf 
  - 服务：td-agent-bit-wli 
  - 用于检查安装故障的扩展日志：/var/log/azure/Microsoft.Azure.Monitor.Workloads.Workload.WLILinuxExtension/wlilogs.log 

### <a name="wli-service-logs"></a>wli 服务日志 

服务日志：`/var/log/wli.log`

查看最近的日志：`tail -n 100 -f /var/log/wli.log`
 
如果看到以下错误日志，则表明 mdsd 服务出现问题。

```
2021-01-27T06:09:28Z [Error] Failed to get config data. Error message: dial unix /var/run/mdsd/default_fluent.socket: connect: no such file or directory 
```

### <a name="telegraf-service-logs"></a>Telegraf 服务日志 

服务日志：`/var/log/ms-telegraf/telegraf.log`

查看最近的日志：`tail -n 100 -f /var/log/ms-telegraf/telegraf.log`

查看最近的错误和警告日志：`tail -n 1000 /var/log/ms-telegraf/telegraf.log | grep "E\!\|W!"`

Telegraf 使用的配置由 wli 服务生成，置于 `/etc/ms-telegraf/telegraf.d/wli` 中
 
如果生成了错误的配置，ms-telegraf 服务可能无法启动。 请使用此命令检查 ms-telegraf 服务是否正在运行：`service ms-telegraf status`

若要查看 telegraf 服务提供的错误消息，请使用以下命令手动运行此服务： 

```
/usr/bin/ms-telegraf --config /etc/ms-telegraf/telegraf.conf --config-directory /etc/ms-telegraf/telegraf.d/wli --test 
```

### <a name="mdsd-service-logs"></a>mdsd 服务日志 

查看适用于 Azure Monitor 代理的[先决条件](../agents/azure-monitor-agent-install.md#prerequisites)。 


服务日志：  
- `/var/log/mdsd.err`
- `/var/log/mdsd.warn`
- `/var/log/mdsd.info`

查看最近的错误：`tail -n 100 -f /var/log/mdsd.err`

如果需要联系支持部门，请收集以下信息： 

- `/var/log/azure/Microsoft.Azure.Monitor.AzureMonitorLinuxAgent/` 中的日志 
- `/var/log/waagent.log` 中的日志 
- `/var/log/mdsd*` 中的日志
- `/etc/mdsd.d/` 中的文件
- File `/etc/default/mdsd`

### <a name="invalid-monitoring-virtual-machine-configuration"></a>监视虚拟机配置无效

处于“未收集”状态的一个原因是监视虚拟机的配置无效。 这是默认配置：

```json
{
    "version": 1,
    "secrets": {
        "telegrafPassword": {
            "keyvault": "https://mykeyvault.vault.azure.net/",
            "name": "sqlPassword"
        }
    },
    "parameters": {
        "sqlAzureConnections": [
            "Server=mysqlserver.database.windows.net;Port=1433;Database=mydatabase;User Id=telegraf;Password=$telegrafPassword;"
        ],
        "sqlVmConnections": [
        ],
        "sqlManagedInstanceConnections": [
        ]
    }
}
```

此配置指定要在监视虚拟机的配置文件配置中使用的替换令牌。 它还允许引用 Azure 密钥保管库中的机密，因此无需在任何配置中保留机密值（强烈建议这样做）。

#### <a name="secrets"></a>机密
机密是令牌，可在运行时从 Azure 密钥保管库检索其值。 机密由密钥保管库引用和机密名称对进行定义。 有了此定义，Azure Monitor 便可以获取机密的动态值，并在下游配置引用中使用它。

可以根据需要在配置中定义尽可能多的机密，包括存储在单独的密钥保管库中的机密。

```json
   "secrets": {
        "<secret-token-name-1>": {
            "keyvault": "<key-vault-uri>",
            "name": "<key-vault-secret-name>"
        },
        "<secret-token-name-2>": {
            "keyvault": "<key-vault-uri-2>",
            "name": "<key-vault-secret-name-2>"
        }
    }
```

用于访问密钥保管库的权限会提供给监视虚拟机上的托管标识。 Azure Monitor 需要密钥保管库至少提供获取虚拟机访问权限所需的机密。 可以通过 Azure 门户、PowerShell、Azure CLI 或 Azure 资源管理器模板启用它。

#### <a name="parameters"></a>参数
参数是可以在配置文件配置中通过 JSON 模板引用的令牌。 参数具有名称和值。 值可以是任意 JSON 类型，包括对象和数组。 在配置文件配置中通过 `.Parameters.<name>` 约定中的参数名来引用参数。

参数可以使用相同的约定引用密钥保管库中的机密。 例如，`sqlAzureConnections` 使用约定 `$telegrafPassword` 引用机密 `telegrafPassword`。

在运行时，会解析所有参数和机密并将其与配置文件配置合并，以构造要在监视虚拟机上使用的实际配置。

> [!NOTE]
> 参数名称 `sqlAzureConnections`、`sqlVmConnections` 和 `sqlManagedInstanceConnections` 在配置中都是必需的，即使你没有为其中一些参数名称提供连接字符串。


## <a name="status-collecting-with-errors"></a>状态：收集时出现错误
如果至少有一条 InsightsMetrics 日志，但 Operation 表中也存在错误，则监视虚拟机的状态将为“收集时出现错误”。

SQL 见解使用以下查询来检索此信息：

```kusto
InsightsMetrics 
    | extend Tags = todynamic(Tags) 
    | extend SqlInstance = tostring(Tags.sql_instance) 
    | where TimeGenerated > ago(240m) and isnotempty(SqlInstance) and Namespace == 'sqlserver_server_properties' and Name == 'uptime' 
```

```kusto
WorkloadDiagnosticLogs
| summarize Errors = countif(Status == 'Error')
```

> [!NOTE]
> 如果在 `WorkloadDiagnosticLogs` 数据类型中看不到任何数据，则可能需要更新监视配置文件以存储该数据。  从 SQL 见解 UX 中选择“管理配置文件” > “编辑配置文件” > “更新监视配置文件”。  

一般情况下，日志视图中提供了故障排除知识： 

:::image type="content" source="media/sql-insights-enable/troubleshooting-logs-view.png" alt-text="故障排除日志视图。":::

## <a name="next-steps"></a>后续步骤

- 获取有关[启用 SQL 见解](sql-insights-enable.md)的详细信息。
