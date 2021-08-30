---
title: 排查 SQL 见解（预览版）问题
description: 在 Azure Monitor 中排查 SQL 见解问题
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/04/2021
ms.openlocfilehash: 53940c21a96da9b763a0b2f25400fb13cbba7098
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/15/2021
ms.locfileid: "112119688"
---
# <a name="troubleshooting-sql-insights-preview"></a>排查 SQL 见解（预览版）问题
若要排查 SQL 见解中的数据收集问题，请在“管理配置文件”选项卡中检查监视虚拟机的状态。状态为下列其中之一：

- 正在收集 
- 未收集 
- 收集时出现错误 
 
单击“状态”以深入钻取并查看日志和详细信息，这可能有助于解决问题。 

:::image type="content" source="media/sql-insights-enable/monitoring-machine-status.png" alt-text="监视虚拟机状态":::

## <a name="not-collecting-state"></a>“未收集”状态 
如果在过去 10 分钟内 SQL 的“InsightsMetrics”中没有任何数据，则监视虚拟机的状态为“未收集”。 

> [!NOTE]
> 请确认你正在尝试从受支持的 SQL 版本收集数据。[](sql-insights-overview.md#supported-versions) 例如，尝试使用有效的配置文件和连接字符串从不受支持的 Azure SQL 数据库版本收集数据会导致不收集状态。

SQL 见解使用以下查询来检索此信息：

```kusto
InsightsMetrics 
    | extend Tags = todynamic(Tags) 
    | extend SqlInstance = tostring(Tags.sql_instance) 
    | where TimeGenerated > ago(10m) and isnotempty(SqlInstance) and Namespace == 'sqlserver_server_properties' and Name == 'uptime' 
```

检查 Telegraf 中是否有任何日志可帮助确定问题的根本原因。 如果有日志条目，则可以单击“未收集”并查看日志和故障排除信息以排查常见问题。 


如果没有日志，则必须查看监视虚拟机上的日志来排查两个虚拟机扩展安装的以下服务的问题：

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

查看最近的日志：`tail -n 100 -f /var/log/ms-telegraf/telegraf.log` 查看最近的错误和警告日志：`tail -n 1000 /var/log/ms-telegraf/telegraf.log | grep "E\!\|W!"`

 Telegraf 使用的配置由 wli 服务生成并置于：`/etc/ms-telegraf/telegraf.d/wli`
 
如果生成了错误的配置，ms-telegraf 服务可能无法启动，请使用此命令检查 ms-telegraf 服务是否正在运行：`service ms-telegraf status`

若要查看 telegraf 服务的错误消息，请使用以下命令手动运行此服务： 

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

“未收集”状态的一个原因是监视虚拟机配置无效。  以下为默认配置：

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
机密是一种令牌，可在运行时从 Azure 密钥保管库检索其值。 机密由密钥保管库引用和机密名称对定义。 这样，Azure Monitor 便可以获取机密的动态值，并将其用作下游配置引用。

可以根据需要在配置中定义尽可能多的机密，其中包括存储在单独的密钥保管库中的机密。

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

为监视虚拟机上的托管服务标识提供访问密钥保管库的权限。 Azure Monitor 要求密钥保管库为虚拟机至少提供机密获取权限。 可以从 Azure 门户、PowerShell、CLI 或资源管理器模板启用。

#### <a name="parameters"></a>参数
参数是可以在配置文件配置中通过 JSON 模板化引用的标记。 参数具有名称和值。 值可以是任意 JSON 类型，包括对象和数组。 在配置文件配置中使用 `.Parameters.<name>` 约定的参数名引用参数。

参数可以使用相同的约定引用密钥保管库中的机密。 例如，`sqlAzureConnections` 使用约定 `$telegrafPassword` 引用机密 `telegrafPassword`。

在运行时，将解析所有参数和机密并将其与配置文件配置合并，以构造要在监视虚拟机上使用的实际配置。

> [!NOTE]
> 配置中必须提供 `sqlAzureConnections`、`sqlVmConnections` 和 `sqlManagedInstanceConnections` 的参数名，即使没有将提供给其中某些参数名的连接字符串。


## <a name="collecting-with-errors-state"></a>“收集时出现错误”状态
如果至少有一条 InsightsMetrics 日志，但 Operation 表中也存在错误，则监视虚拟机将处于“收集时出现错误”状态。

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
> 如果在“WorkloadDiagnosticLogs”数据类型中看不到任何数据，则可能需要更新监视配置文件以存储这些数据。  从 SQL insights UX 中选择“管理配置文件”，选择“编辑配置文件”，然后选择“更新监视配置文件”。


一般情况下，日志视图中提供了故障排除知识： 

:::image type="content" source="media/sql-insights-enable/troubleshooting-logs-view.png" alt-text="故障排除日志视图。":::



## <a name="next-steps"></a>后续步骤

- 获取有关[启用 SQL 见解](sql-insights-enable.md)的详细信息。
