---
title: 在 Azure CLI 中管理 Azure Monitor 日志
description: 了解如何使用 Azure CLI 命令管理 Azure Monitor 日志中的工作区，包括工作区如何与其他 Azure 服务交互。
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 08/16/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: 548ddbc3fce6220e32de2df43024b7c4ed22f5e7
ms.sourcegitcommit: ef448159e4a9a95231b75a8203ca6734746cd861
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123187886"
---
# <a name="managing-azure-monitor-logs-in-azure-cli"></a>在 Azure CLI 中管理 Azure Monitor 日志

使用本文所述的 Azure CLI 命令来管理 Azure Monitor 中的 Log Analytics 工作区。

> [!NOTE]
> 2024 年 8 月 31 日，Microsoft 将停用 Log Analytics 代理。 在此之后，你可以使用 Azure Monitor 代理。 有关详细信息，请参阅 [Azure Monitor 代理概述](../agents/agents-overview.md)。

[!INCLUDE [Prepare your Azure CLI environment](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-a-workspace-for-monitor-logs"></a>为 Monitor 日志创建工作区

运行 [az group create](/cli/azure/group#az_group_create) 命令创建资源组或使用现有资源组。 若要创建工作区，请使用 [az monitor log-analytics workspace create](/cli/azure/monitor/log-analytics/workspace#az_monitor_log_analytics_workspace_create) 命令。

```azurecli
az group create --name ContosoRG --location eastus2
az monitor log-analytics workspace create --resource-group ContosoRG \
   --workspace-name ContosoWorkspace
```

有关工作区的详细信息，请参阅 [Azure Monitor 日志概述](/azure/azure-monitor/logs/data-platform-logs)。

## <a name="list-tables-in-your-workspace"></a>列出工作区中的表

每个工作区都包含表，这些表包含具有多行数据的列。 每个表都由数据源提供的一组唯一数据列定义。

若要查看工作区中的表，请使用 [az monitor log-analytics workspace table list](/cli/azure/monitor/log-analytics/workspace/table#az_monitor_log_analytics_workspace_table_list) 命令：

```azurecli
az monitor log-analytics workspace table list --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --output table
```

输出值 `table` 以更易读的格式呈现结果。 有关详细信息，请参阅[输出格式设置](/cli/azure/use-cli-effectively#output-formatting)。

若要更改表的保留时间，请运行 [az monitor log-analytics workspace table update](/cli/azure/monitor/log-analytics/workspace/table#az_monitor_log_analytics_workspace_table_update) 命令：

```azurecli
az monitor log-analytics workspace table update --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --name Syslog --retention-time 45
```

保留时间在 30 到 730 天之间。

有关表的详细信息，请参阅[数据结构](/azure/azure-monitor/logs/data-platform-logs#data-structure)。

## <a name="export-data-from-selected-tables"></a>从所选表导出数据

可以将所选表中的数据持续导出到 Azure 存储帐户或 Azure 事件中心。 请使用 [az monitor log-analytics workspace data-export create](/cli/azure/monitor/log-analytics/workspace/data-export#az_monitor_log_analytics_workspace_data_export_create) 命令：

```azurecli
az monitor log-analytics workspace data-export create --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --name DataExport --table Syslog \
   --destination /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Storage/storageAccounts/exportaccount \
   --enable
```

若要查看数据导出，请运行 [az monitor log-analytics workspace data-export list](/cli/azure/monitor/log-analytics/workspace/data-export#az_monitor_log_analytics_workspace_data_export_list) 命令。

```azurecli
az monitor log-analytics workspace data-export list --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --output table
```

若要删除数据导出，请运行 [az monitor log-analytics workspace data-export delete](/cli/azure/monitor/log-analytics/workspace/data-export#az_monitor_log_analytics_workspace_data_export_delete) 命令。 `--yes` 参数将跳过确认。

```azurecli
az monitor log-analytics workspace data-export delete --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --name DataExport --yes
```

有关数据导出的详细信息，请参阅 [Azure Monitor 中的 Log Analytics 工作区数据导出](/azure/azure-monitor/logs/logs-data-export)。

## <a name="manage-a-linked-service"></a>管理链接服务

链接服务定义从工作区到另一个 Azure 资源的关系。 Azure Monitor 日志和 Azure 资源将在其操作中使用此关联。 链接服务的使用示例，包括自动化帐户以及工作区与客户管理的密钥的关联。

若要创建链接服务，请运行 [az monitor log-analytics workspace linked-service create](/cli/azure/monitor/log-analytics/workspace/linked-service#az_monitor_log_analytics_workspace_linked_service_create) 命令：

```azurecli
az monitor log-analytics workspace linked-service create --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --name linkedautomation \
   --resource-id /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Web/sites/ContosoWebApp09

az monitor log-analytics workspace linked-service list --resource-group ContosoRG \
   --workspace-name ContosoWorkspace
```

若要删除链接服务关系，请运行 [az monitor log-analytics workspace linked-service delete](/cli/azure/monitor/log-analytics/workspace/linked-service#az_monitor_log_analytics_workspace_linked_service_delete) 命令：

```azurecli
az monitor log-analytics workspace linked-service delete --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --name linkedautomation
```

有关详细信息，请参阅 [az monitor log-analytics workspace linked-service](/cli/azure/monitor/log-analytics/workspace/linked-service)。

## <a name="manage-linked-storage"></a>管理链接存储

如果你为 Log Analytics 提供并管理自己的存储帐户，可以使用以下 Azure CLI 命令进行管理。

若要将工作区链接到存储帐户，请运行 [az monitor log-analytics workspace linked-storage create](/cli/azure/monitor/log-analytics/workspace/linked-storage#az_monitor_log_analytics_workspace_linked_storage_create) 命令：

```azurecli
az monitor log-analytics workspace linked-storage create --resource-group ContosoRG \
   --workspace-name ContosoWorkspace \
   --storage-accounts /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Storage/storageAccounts/contosostorage \
   --type Alerts

az monitor log-analytics workspace linked-storage list --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --output table
```

若要删除指向存储帐户的链接，请运行 [az monitor log-analytics workspace linked-storage delete](/cli/azure/monitor/log-analytics/workspace/linked-storage#az_monitor_log_analytics_workspace_linked_storage_delete) 命令：

```azurecli
az monitor log-analytics workspace linked-storage delete --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --type Alerts
```

有关详细信息，请参阅[在 Azure Monitor Log Analytics 中使用客户管理的存储帐户](/azure/azure-monitor/logs/private-storage)。

## <a name="manage-intelligence-packs"></a>管理智能包

若要查看可用的智能包，请运行 [az monitor log-analytics workspace pack list](/cli/azure/monitor/log-analytics/workspace/pack#az_monitor_log_analytics_workspace_pack_list) 命令。 该命令还会指出该包是否已启用。

```azurecli
az monitor log-analytics workspace pack list --resource-group ContosoRG \
   --workspace-name ContosoWorkspace
```

使用 [az monitor log-analytics workspace pack enable](/cli/azure/monitor/log-analytics/workspace/pack#az_monitor_log_analytics_workspace_pack_enable) 或 [az monitor log-analytics workspace pack disable](/cli/azure/monitor/log-analytics/workspace/pack#az_monitor_log_analytics_workspace_pack_disable) 命令：

```azurecli
az monitor log-analytics workspace pack enable --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --name NetFlow

az monitor log-analytics workspace pack disable --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --name NetFlow
```

## <a name="manage-saved-searches"></a>管理保存的搜索

若要创建保存的搜索，请运行 [az monitor log-analytics workspace saved-search](/cli/azure/monitor/log-analytics/workspace/saved-search#az_monitor_log_analytics_workspace_saved_search_create) 命令：

```azurecli
az monitor log-analytics workspace saved-search create --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --name SavedSearch01 \
   --category "Log Management" --display-name SavedSearch01 \
   --saved-query "AzureActivity | summarize count() by bin(TimeGenerated, 1h)" --fa Function01 --fp "a:string = value"
```

使用 [az monitor log-analytics workspace saved-search show](/cli/azure/monitor/log-analytics/workspace/saved-search#az_monitor_log_analytics_workspace_saved_search_show) 命令可查看保存的搜索。 使用 [az monitor log-analytics workspace saved-search list](/cli/azure/monitor/log-analytics/workspace/saved-search#az_monitor_log_analytics_workspace_saved_search_list) 可查看所有保存的搜索。

```azurecli
az monitor log-analytics workspace saved-search show --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --name SavedSearch01
az monitor log-analytics workspace saved-search list --resource-group ContosoRG \
   --workspace-name ContosoWorkspace
```

若要删除保存的搜索，请运行 [az monitor log-analytics workspace saved-search delete](/cli/azure/monitor/log-analytics/workspace/saved-search#az_monitor_log_analytics_workspace_saved_search_delete) 命令：

```azurecli
az monitor log-analytics workspace saved-search delete --resource-group ContosoRG \
   --workspace-name ContosoWorkspace --name SavedSearch01 --yes
```

## <a name="clean-up-deployment"></a>清理部署

如果创建了一个资源组来测试这些命令，则可以使用 [az group delete](/cli/azure/group#az-group-delete) 命令删除该资源组及其所有内容：

```azurecli
az group delete --name ContosoRG
```

若要从现有资源组中删除新工作区，请运行 [az monitor log-analytics workspace delete](/cli/azure/monitor/log-analytics/workspace#az_monitor_log_analytics_workspace_delete) 命令：

```azurecli
az monitor log-analytics workspace delete --resource-group ContosoRG 
   --workspace-name ContosoWorkspace --yes
```

Log Analytics 工作区有一个软删除选项。 你可以在删除后的两周内恢复已删除的工作区。 运行 [az monitor log-analytics workspace recover](/cli/azure/monitor/log-analytics/workspace#az_monitor_log_analytics_workspace_recover) 命令：

```azurecli
az monitor log-analytics workspace recover --resource-group ContosoRG 
   --workspace-name ContosoWorkspace
```

在 delete 命令中，添加 `--force` 参数可立即删除工作区。

## <a name="azure-cli-commands-used-in-this-article"></a>本文中使用的 Azure CLI 命令

- [az group create](/cli/azure/group#az_group_create)
- [az group delete](/cli/azure/group#az-group-delete)
- [az monitor log-analytics workspace create](/cli/azure/monitor/log-analytics/workspace#az_monitor_log_analytics_workspace_create)
- [az monitor log-analytics workspace data-export create](/cli/azure/monitor/log-analytics/workspace/data-export#az_monitor_log_analytics_workspace_data_export_create)
- [az monitor log-analytics workspace data-export delete](/cli/azure/monitor/log-analytics/workspace/data-export#az_monitor_log_analytics_workspace_data_export_delete)
- [az monitor log-analytics workspace data-export list](/cli/azure/monitor/log-analytics/workspace/data-export#az_monitor_log_analytics_workspace_data_export_list)
- [az monitor log-analytics workspace delete](/cli/azure/monitor/log-analytics/workspace#az_monitor_log_analytics_workspace_delete)
- [az monitor log-analytics workspace linked-service create](/cli/azure/monitor/log-analytics/workspace/linked-service#az_monitor_log_analytics_workspace_linked_service_create)
- [az monitor log-analytics workspace linked-service delete](/cli/azure/monitor/log-analytics/workspace/linked-service#az_monitor_log_analytics_workspace_linked_service_delete)
- [az monitor log-analytics workspace linked-storage create](/cli/azure/monitor/log-analytics/workspace/linked-storage#az_monitor_log_analytics_workspace_linked_storage_create)
- [az monitor log-analytics workspace linked-storage delete](/cli/azure/monitor/log-analytics/workspace/linked-storage#az_monitor_log_analytics_workspace_linked_storage_delete)
- [az monitor log-analytics workspace pack disable](/cli/azure/monitor/log-analytics/workspace/pack#az_monitor_log_analytics_workspace_pack_disable)
- [az monitor log-analytics workspace pack enable](/cli/azure/monitor/log-analytics/workspace/pack#az_monitor_log_analytics_workspace_pack_enable)
- [az monitor log-analytics workspace pack list](/cli/azure/monitor/log-analytics/workspace/pack#az_monitor_log_analytics_workspace_pack_list)
- [az monitor log-analytics workspace recover](/cli/azure/monitor/log-analytics/workspace#az_monitor_log_analytics_workspace_recover)
- [az monitor log-analytics workspace saved-search delete](/cli/azure/monitor/log-analytics/workspace/saved-search#az_monitor_log_analytics_workspace_saved_search_delete)
- [az monitor log-analytics workspace saved-search list](/cli/azure/monitor/log-analytics/workspace/saved-search#az_monitor_log_analytics_workspace_saved_search_list)
- [az monitor log-analytics workspace saved-search show](/cli/azure/monitor/log-analytics/workspace/saved-search#az_monitor_log_analytics_workspace_saved_search_show)
- [az monitor log-analytics workspace saved-search](/cli/azure/monitor/log-analytics/workspace/saved-search#az_monitor_log_analytics_workspace_saved_search_create)
- [az monitor log-analytics workspace table list](/cli/azure/monitor/log-analytics/workspace/table#az_monitor_log_analytics_workspace_table_list)
- [az monitor log-analytics workspace table update](/cli/azure/monitor/log-analytics/workspace/table#az_monitor_log_analytics_workspace_table_update)

## <a name="next-steps"></a>后续步骤

[Azure Monitor 中的 Log Analytics 概述](log-analytics-overview.md)
