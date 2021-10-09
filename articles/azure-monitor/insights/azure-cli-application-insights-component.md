---
title: 在 Azure CLI 中管理 Application Insights 组件
description: 使用此示例代码来管理 Application Insights 中的组件。 此功能是 Azure Monitor 的一部分。
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 09/10/2012
ms.custom: devx-track-azurecli
ms.openlocfilehash: b8c7b81847a1ac2484a1eaa0df56bde35eec6385
ms.sourcegitcommit: 3ef5a4eed1c98ce76739cfcd114d492ff284305b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128709091"
---
# <a name="manage-application-insights-components-by-using-azure-cli"></a>使用 Azure CLI 管理 Application Insights 组件

在 Azure Monitor 中，组件是分布式或微服务应用程序的可独立部署的部分。 使用以下 Azure CLI 命令来管理 Application Insights 中的组件。

本文中的示例执行以下管理任务：

- 创建组件。
- 将组件连接到 webapp。
- 将组件链接到包含组件的存储帐户。
- 为组件创建连续导出配置。

[!INCLUDE [Prepare your Azure CLI environment](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-a-component"></a>创建组件

如果还没有资源组和工作区，请使用 [az group create](/cli/azure/group#az_group_create) 和 [az monitor log-analytics workspace create](/cli/azure/monitor/log-analytics/workspace#az_monitor_log_analytics_workspace_create) 创建它们：

```azurecli
az group create --name ContosoAppInsightRG --location eastus2
az monitor log-analytics workspace create --resource-group ContosoAppInsightRG \
   --workspace-name AppInWorkspace
```

若要创建组件，请运行 [az monitor app-insights component create](/cli/azure/monitor/app-insights/component#az_monitor_app_insights_component_create) 命令。 [az monitor app-insights component show](/cli/azure/monitor/app-insights/component#az_monitor_app_insights_component_show) 命令可显示组件。

```azurecli
az monitor app-insights component create --resource-group ContosoAppInsightRG \
   --app ContosoApp --location eastus2 --kind web --application-type web \
   --retention-time 120
az monitor app-insights component show --resource-group ContosoAppInsightRG --app ContosoApp
```

## <a name="connect-a-webapp"></a>连接 webapp

本示例将组件连接到 webapp。 你可以使用 [az appservice plan create](/cli/azure/appservice/plan#az_appservice_plan_create) 和 [az webapp create](/cli/azure/webapp#az_webapp_create) 命令创建 webapp：

```azurecli
az appservice plan create --resource-group ContosoAppInsightRG --name ContosoAppService
az webapp create --resource-group ContosoAppInsightRG --name ContosoApp \
   --plan ContosoAppService --name ContosoApp8765
```

运行 [az monitor app-insights component connect-webapp](/cli/azure/monitor/app-insights/component#az_monitor_app_insights_component_connect_webapp) 命令，将组件连接到 webapp：

```azurecli
az monitor app-insights component connect-webapp --resource-group ContosoAppInsightRG \
   --app ContosoApp --web-app ContosoApp8765 --enable-debugger false --enable-profiler false
```

可以改为使用 [az monitor app-insights component connect-function](/cli/azure/monitor/app-insights/component#az_monitor_app_insights_component_connect_function) 命令连接到 Azure 函数。

## <a name="link-a-component-to-storage"></a>将组件链接到存储

你可以将组件链接到存储帐户。 如需创建存储帐户，可使用 [az storage account create](/cli/azure/storage/account#az_storage_account_create) 命令：

```azurecli
az storage account create --resource-group ContosoAppInsightRG \
   --name contosolinkedstorage --location eastus2 --sku Standard_LRS
```

若要将组件链接到存储帐户，请运行 [az monitor app-insights component links-storage link](/cli/azure/monitor/app-insights/component/linked-storage#az_monitor_app_insights_component_linked_storage_link) 命令。 可以使用 [az monitor app-insights component links-storage show](/cli/azure/monitor/app-insights/component/linked-storage#az_monitor_app_insights_component_linked_storage_show) 命令查看现有链接：


```azurecli
az monitor app-insights component linked-storage link  --resource-group ContosoAppInsightRG \
   --app ContosoApp --storage-account contosolinkedstorage
az monitor app-insights component linked-storage show --resource-group ContosoAppInsightRG \
   --app ContosoApp
```

若要取消链接存储，请运行 [az monitor app-insights component links-storage unlink](/cli/azure/monitor/app-insights/component/linked-storage#az_monitor_app_insights_component_linked_storage_unlink) 命令：

```AzureCLI
az monitor app-insights component linked-storage unlink  \
   --resource-group ContosoAppInsightRG --app ContosoApp
```

## <a name="set-up-continuous-export"></a>设置连续导出

连续导出可将来自 Application Insights 门户的事件以 JSON 格式保存在存储容器中。

> [!NOTE]
> 只有经典 Application Insights 资源支持连续导出。 [基于工作区的 Application Insights 资源](../app/create-workspace-resource.md)必须使用[诊断设置](../app/create-workspace-resource.md#export-telemetry)。
>

若要创建存储容器，请运行 [az storage container create](/cli/azure/storage/container#az_storage_container_create) 命令。 

```azurecli
az storage container create --name contosostoragecontainer --account-name contosolinkedstorage \
   --public-access blob 
```

你需要将对容器的访问权限设置为只写。 运行 [az storage container policy create](/cli/azure/storage/container/policy#az_storage_container_policy_create) cmdlet：

```azurecli
az storage container policy create --container-name contosostoragecontainer \
   --account-name contosolinkedstorage --name WAccessPolicy --permissions w
```

使用 [az storage container generate-sas](/cli/azure/storage/container#az_storage_container_generate_sas) 命令创建 SAS 密钥。 请确保使用 `--output tsv` 参数值来保存密钥，以免出现不需要的格式（如引号）。 有关详细信息，请参阅[高效使用 Azure CLI](/cli/azure/use-cli-effectively)。

```azurecli
containersas=$(az storage container generate-sas --name contosostoragecontainer \
   --account-name contosolinkedstorage --permissions w --output tsv)
```

若要创建连续导出，请运行 [az monitor app-insights component continues-export create](/cli/azure/monitor/app-insights/component/continues-export#az_monitor_app_insights_component_continues_export_create) 命令：

```azurecli
az monitor app-insights component continues-export create --resource-group ContosoAppInsightRG \
   --app ContosoApp --record-types Event --dest-account contosolinkedstorage \
   --dest-container contosostoragecontainer --dest-sub-id 00000000-0000-0000-0000-000000000000 \
   --dest-sas $containersas
```

你可以使用 [az monitor app-insights component continues-export delete](/cli/azure/monitor/app-insights/component/continues-export#az_monitor_app_insights_component_continues_export_delete) 命令删除已配置的连续导出： 

```azurecli
az monitor app-insights component continues-export list \
   --resource-group ContosoAppInsightRG --app ContosoApp
az monitor app-insights component continues-export delete \
   --resource-group ContosoAppInsightRG --app ContosoApp --id abcdefghijklmnopqrstuvwxyz=
```

## <a name="clean-up-deployment"></a>清理部署

如果创建了一个资源组来测试这些命令，则可以使用 [az group delete](/cli/azure/group#az_group_delete) 命令删除该资源组及其所有内容：

```azurecli
az group delete --name ContosoAppInsightRG 
```

## <a name="azure-cli-commands-used-in-this-article"></a>本文中使用的 Azure CLI 命令

- [az appservice plan create](/cli/azure/appservice/plan#az_appservice_plan_create)
- [az group create](/cli/azure/group#az_group_create)
- [az group delete](/cli/azure/group#az_group_delete)
- [az monitor app-insights component connect-webapp](/cli/azure/monitor/app-insights/component#az_monitor_app_insights_component_connect_webapp)
- [az monitor app-insights component continues-export create](/cli/azure/monitor/app-insights/component/continues-export#az_monitor_app_insights_component_continues_export_create)
- [az monitor app-insights component continues-export delete](/cli/azure/monitor/app-insights/component/continues-export#az_monitor_app_insights_component_continues_export_delete)
- [az monitor app-insights component continues-export list](/cli/azure/monitor/app-insights/component/continues-export#az_monitor_app_insights_component_continues_export_list)
- [az monitor app-insights component create](/cli/azure/monitor/app-insights/component#az_monitor_app_insights_component_create)
- [az monitor app-insights component linked-storage link](/cli/azure/monitor/app-insights/component/linked-storage#az_monitor_app_insights_component_linked_storage_link)
- [az monitor app-insights component linked-storage unlink](/cli/azure/monitor/app-insights/component/linked-storage#az_monitor_app_insights_component_linked_storage_unlink)
- [az monitor app-insights component show](/cli/azure/monitor/app-insights/component#az_monitor_app_insights_component_show)
- [az monitor log-analytics workspace create](/cli/azure/monitor/log-analytics/workspace#az_monitor_log_analytics_workspace_create)
- [az storage account create](/cli/azure/storage/account#az_storage_account_create)
- [az storage container create](/cli/azure/storage/container#az_storage_container_create)
- [az storage container generate-sas](/cli/azure/storage/container#az_storage_container_generate_sas)
- [az storage container policy create](/cli/azure/storage/container/policy#az_storage_container_policy_create)
- [az webapp create](/cli/azure/webapp#az_webapp_create)

## <a name="next-steps"></a>后续步骤

[Azure Monitor CLI 示例](../cli-samples.md)

[查找和诊断性能问题](../app/tutorial-performance.md)

[监视应用程序运行状况并根据情况发出警报](../app/tutorial-alert.md)
