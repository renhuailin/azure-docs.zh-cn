---
title: 在 Azure CLI 中创建指标警报监视器
description: 了解如何使用 Azure CLI 命令在 Azure Monitor 中创建指标警报。 这些示例为虚拟机和应用服务计划创建警报。
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 08/06/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: 60d2b7f706c985eaa13c634e0323927eb39446ad
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123117377"
---
# <a name="create-metric-alert-monitors-in-azure-cli"></a>在 Azure CLI 中创建指标警报监视器

这些示例使用 Azure CLI 命令在 Azure Monitor 中创建指标警报监视器。 第一个示例为虚拟机创建警报。 第二个命令创建一个警报，其中包含应用服务计划的维度。  

[!INCLUDE [Prepare your Azure CLI environment](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-an-alert"></a>创建警报

此警报监视 `ContosoVMRG` 资源组中名为 `VM07` 的现有虚拟机。

可以使用 [az group create](/cli/azure/group#az-group-create) 命令创建资源组。 有关创建虚拟机的信息，请参阅[使用 Azure CLI 创建 Windows 虚拟机](../virtual-machines/windows/quick-create-cli.md)、[使用 Azure CLI 创建 Linux 虚拟机](../virtual-machines/linux/quick-create-cli.md)[az vm create](/cli/azure/vm#az-vm-create)命令。

```azurecli
# resource group name: ContosoVMRG
# virtual machine name: VM07

# Create scope
scope=$(az vm show --resource-group ContosoVMRG --name VM07 --output tsv --query id)

# Create action
action=$(az monitor action-group create --name ContosoWebhookAction \
  --resource-group ContosoVMRG --output tsv --query id \
  --action webhook https://alerts.contoso.com usecommonalertschema)

# Create condition
condition=$(az monitor metrics alert condition create --aggregation Average \
  --metric "Percentage CPU" --op GreaterThan --type static --threshold 90 --output tsv)

# Create metrics alert
az monitor metrics alert create --name alert-01 --resource-group ContosoVMRG \
  --scopes $scope --action $action --condition $condition --description "Test High CPU"
```

此示例使用 `tsv` 输出类型，该类型不包含不需要的符号（例如引号）。 有关详细信息，请参阅[高效使用 Azure CLI](/cli/azure/use-cli-effectively)。

## <a name="create-an-alert-with-a-dimension"></a>创建具有维度的警报

此示例创建一个应用服务计划，然后为其创建指标警报。 示例使用维度来指定应用服务计划的所有实例都将在此指标下。 此示例创建资源组和应用程序服务计划。

```azurecli
# Create resource group
az group create --name ContosoRG --location eastus2
 
# Create application service plan
az appservice plan create --resource-group ContosoRG --name ContosoAppServicePlan \
   --is-linux --number-of-workers 4 --sku S1 
 
# Create scope
scope=$(az appservice plan show --resource-group ContosoRG --name ContosoAppServicePlan \
   --output tsv --query id) 
 
# Create dimension
dim01=$(az monitor metrics alert dimension create --name Instance --value * --op Include --output tsv)
 
# Create condition
condition=$(az monitor metrics alert condition create --aggregation Average \
   --metric CpuPercentage --op GreaterThan --type static --threshold 90 \
   --dimension $dim01 --output tsv)
```

若要查看可能的指标列表，请运行 [az monitor metrics list-definitions](/cli/azure/monitor/metrics#az_monitor_metrics_list_definitions) 命令。 `--output` 参数以可读格式显示值。


```azurecli
az monitor metrics list-definitions --resource $scope --output table 
 
# Create metrics alert
az monitor metrics alert create --name alert-02 --resource-group ContosoRG \
   --scopes $scope --condition $condition --description "Service Plan High CPU"
```

## <a name="clean-up-deployment"></a>清理部署

如果创建了一个资源组来测试这些命令，则可以使用 [az group delete](/cli/azure/group#az-group-delete) 命令删除资源组及其所有内容：

```azurecli
az group delete --name ContosoVMRG

az group delete --name ContosoRG
```

如果使用了要保留的现有资源，请使用 [az monitor metrics alert delete](/cli/azure/monitor/metrics/alert#az-monitor-metrics-alert-delete) 命令删除练习警报：

```azurecli
az monitor metrics alert delete --name alert-01

az monitor metrics alert delete --name alert-02
```

## <a name="azure-cli-commands-used-in-this-article"></a>本文中使用的 Azure CLI 命令

本文使用以下 Azure CLI 命令：

- [az appservice plan create](/cli/azure/appservice/plan#az_appservice_plan_create)
- [az appservice plan show](/cli/azure/appservice/plan#az_appservice_plan_show)
- [az group create](/cli/azure/group#az-group-create)
- [az group delete](/cli/azure/group#az-group-delete)
- [az monitor action-group create](/cli/azure/monitor/action-group#az_monitor_action_group_create)
- [az monitor metrics alert condition create](/cli/azure/monitor/metrics/alert#az-monitor-metrics-alert-condition-create)
- [az monitor metrics alert create](/cli/azure/monitor/metrics/alert#az-monitor-metrics-alert-create)
- [az monitor metrics alert delete](/cli/azure/monitor/metrics/alert#az-monitor-metrics-alert-delete)
- [az monitor metrics alert dimension create](/cli/azure/monitor/metrics/alert#az-monitor-metrics-alert-dimension-create)
- [az monitor metrics list-definitions](/cli/azure/monitor/metrics#az_monitor_metrics_list_definitions)
- [az vm show](/cli/azure/vm#az_vm_show)

## <a name="next-steps"></a>后续步骤

- [Azure Monitor CLI 示例](cli-samples.md)
- [了解指标警报在 Azure Monitor 中的工作原理](alerts/alerts-metric-overview.md)
