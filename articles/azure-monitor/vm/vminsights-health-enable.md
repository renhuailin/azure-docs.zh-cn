---
title: '启用 VM insights 来宾运行状况 (预览) '
description: 介绍如何在你的订阅中启用 VM insights 来宾运行状况以及如何载入 Vm。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/16/2020
ms.custom: references_regions
ms.openlocfilehash: 5d4ff622f69445880c0de8cb74dc1aeee422c89b
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102052154"
---
# <a name="enable-vm-insights-guest-health-preview"></a>启用 VM insights 来宾运行状况 (预览) 
VM insights 来宾运行状况允许你查看按固定时间间隔采样的一组性能度量定义的虚拟机的运行状况。 本文介绍如何在你的订阅中启用此功能，以及如何为每个虚拟机启用来宾监视。

## <a name="current-limitations"></a>当前限制
VM insights 来宾运行状况在公共预览版中具有以下限制：

- 目前仅支持 Azure 虚拟机。 当前不支持 Azure Arc for servers。


## <a name="supported-operating-systems"></a>支持的操作系统
虚拟机必须运行以下操作系统之一： 

  - Ubuntu 16.04 LTS、Ubuntu 18.04 LTS
  - Windows Server 2012 或更高版本

## <a name="supported-regions"></a>支持的区域

虚拟机必须位于以下区域之一：

- 澳大利亚中部
- 澳大利亚东部
- 澳大利亚东南部
- 加拿大中部
- 印度中部
- 美国中部
- 东亚
- 美国东部
- 美国东部 2
- 美国东部 2 EUAP
- 法国中部
- 德国中西部
- Japan East
- 韩国中部
- 美国中北部
- 北欧
- 美国中南部
- 南非北部
- 东南亚
- 瑞士北部
- 英国南部
- 英国西部
- 美国中西部
- 西欧
- 美国西部
- 美国西部 2


Log Analytics 工作区必须位于以下区域之一：

- 澳大利亚中部
- 澳大利亚东部
- 澳大利亚东南部
- 加拿大中部
- 加拿大印度
- 美国中部
- 东亚
- 美国东部
- 美国东部 2
- 美国东部 2 EUAP
- 法国中部
- 日本东部
- 美国中北部
- 北欧
- 美国中南部
- 东南亚
- 瑞士北部
- 英国南部
- 西欧区域
- 美国西部
- 美国西部 2

## <a name="prerequisites"></a>先决条件

- 虚拟机必须载入 VM insights。
- 执行载入步骤的用户必须至少具有 "参与者" 级别对虚拟机和数据收集规则所在的订阅的访问权限。
- 必需的 Azure 资源提供程序必须注册，如以下部分所述。

## <a name="register-required-azure-resource-providers"></a>注册所需的 Azure 资源提供程序
为你的订阅注册以下 Azure 资源提供程序以启用 VM insights 来宾运行状况。 

- Microsoft.WorkloadMonitor
- Microsoft.Insights

可以使用任何可用的方法来注册资源提供程序，如 [Azure 资源提供程序和类型](../../azure-resource-manager/management/resource-providers-and-types.md)中所述。 你还可以使用以下示例命令，使用 armclient、postman 或其他方法对 Azure 资源管理器进行身份验证调用：

```
POST https://management.azure.com/subscriptions/[subscriptionId]/providers/Microsoft.WorkloadMonitor/register?api-version=2019-10-01
POST https://management.azure.com/subscriptions/[subscriptionId]/providers/Microsoft.Insights/register?api-version=2019-10-01
```


## <a name="enable-a-virtual-machine-using-the-azure-portal"></a>使用 Azure 门户启用虚拟机
在 Azure 门户中为虚拟机启用来宾运行状况时，系统将为你执行所有必需的配置。 这包括创建需要数据收集规则、在虚拟机上安装来宾运行状况扩展，以及创建与数据收集规则的关联。

在 VM insights 的 " **入门** " 视图中，单击虚拟机的升级消息旁边的链接，然后单击 " **升级** " 按钮。 还可选择多个虚拟机一起升级。

![启用虚拟机上的运行状况功能](media/vminsights-health-enable/enable-agent.png)


## <a name="enable-a-virtual-machine-using-resource-manager-template"></a>使用资源管理器模板启用虚拟机
使用 Azure 资源管理器启用虚拟机需要执行三个步骤。

- 创建数据收集规则。
- 在每个虚拟机上安装来宾运行状况扩展
- 在虚拟机和数据收集规则之间创建关联。

### <a name="create-data-collection-rule-dcr"></a> (DCR) 创建数据收集规则

> [!NOTE]
> 如果使用 Azure 门户启用虚拟机，则会为你创建此处所述的数据收集规则。 在这种情况下，不需要执行此步骤。

VM insights 来宾运行状况中的监视器的配置存储在 [数据收集规则 (DCR) ](../agents/data-collection-rule-overview.md)。 具有来宾运行状况扩展的每个虚拟机都需要与此规则关联。

> [!NOTE]
> 可以根据在 [VM insights 来宾健康状况 (预览) 中配置监视 ](vminsights-health-configure.md)中所述，创建其他数据收集规则来修改监视器的默认配置。

模板需要以下参数的值：

- **defaultHealthDataCollectionRuleName**：保留在模板中定义的默认名称。
- **destinationWorkspaceResourceId**：用于虚拟机数据收集的 Log Analytics 工作区的资源 ID。
- **dataCollectionRuleLocation**：数据收集规则的区域。 这必须与 Log Analytics 工作区的区域匹配。


使用 [资源管理器模板的任何部署方法](../../azure-resource-manager/templates/deploy-powershell.md)部署模板。 以下命令使用 PowerShell 或 Azure CLI 部署模板和参数文件。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
New-AzResourceGroupDeployment -Name GuestHealthDataCollectionRule -ResourceGroupName my-resource-group -TemplateFile Health.DataCollectionRule.template.json -TemplateParameterFile Health.DataCollectionRule.template.parameters.json
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create --name GuestHealthDataCollectionRule --resource-group my-resource-group --template-file Health.DataCollectionRule.template.json --parameters Health.DataCollectionRule.template.parameters.json
```

---

以下资源管理器模板中定义的数据收集规则将启用具有来宾运行状况扩展的虚拟机的所有监视器。 它必须包含监视器使用的每个性能计数器的数据源。

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "defaultHealthDataCollectionRuleName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the data collection rule to create."
      },
      "defaultValue": "Microsoft-VMInsights-Health"
    },
    "destinationWorkspaceResourceId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the Azure resource ID of the Log Analytics workspace to use to store virtual machine health data."
      }
    },
    "dataCollectionRuleLocation": {
      "type": "string",
      "metadata": {
        "description": "The location code in which the data collection rule should be deployed. Examples: eastus, westeurope, etc"
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Insights/dataCollectionRules",
      "name": "[parameters('defaultHealthDataCollectionRuleName')]",
      "location": "[parameters('dataCollectionRuleLocation')]",
      "apiVersion": "2019-11-01-preview",
      "properties": {
        "description": "Data collection rule for VM Insights health.",
        "dataSources": {
          "performanceCounters": [
              {
                  "name": "VMHealthPerfCounters",
                  "streams": [ "Microsoft-Perf" ],
                  "scheduledTransferPeriod": "PT1M",
                  "samplingFrequencyInSeconds": 60,
                  "counterSpecifiers": [
                      "\\LogicalDisk(*)\\% Free Space",
                      "\\Memory\\Available Bytes",
                      "\\Processor(_Total)\\% Processor Time"
                  ]
              }
          ],
          "extensions": [
            {
              "name": "Microsoft-VMInsights-Health",
              "streams": [
                "Microsoft-HealthStateChange"
              ],
              "extensionName": "HealthExtension",
              "extensionSettings": {
                "schemaVersion": "1.0",
                "contentVersion": "",
                "healthRuleOverrides": [
                  {
                    "scopes": [ "*" ],
                    "monitors": ["root"],
                    "alertConfiguration": {
                      "isEnabled": true
                    }
                  }
                ]
              },
              "inputDataSources": [
                  "VMHealthPerfCounters"
              ]

            }
          ]
        },
        "destinations": {
          "logAnalytics": [
            {
              "workspaceResourceId": "[parameters('destinationWorkspaceResourceId')]",
              "name": "Microsoft-HealthStateChange-Dest"
            }
          ]
        },                  
        "dataFlows": [
          {
            "streams": [
              "Microsoft-HealthStateChange"
            ],
            "destinations": [
              "Microsoft-HealthStateChange-Dest"
            ]
          }
        ]
      }
    }
  ]
}
```

### <a name="sample-parameter-file"></a>示例参数文件

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "destinationWorkspaceResourceId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace"
      },
      "dataCollectionRuleLocation": {
        "value": "eastus"
      }
  }
}
```



### <a name="install-guest-health-extension-and-associate-with-data-collection-rule"></a>安装来宾运行状况扩展并与数据收集规则关联
使用以下资源管理器模板启用虚拟机以进行来宾健康状况。 这将安装来宾运行状况扩展，并创建与数据收集规则的关联。 你可以使用 [资源管理器模板的任何部署方法](../../azure-resource-manager/templates/deploy-powershell.md)部署此模板。


例如，使用以下命令通过 PowerShell 或 Azure CLI 将模板和参数文件部署到资源组中。


# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
New-AzResourceGroupDeployment -Name GuestHealthDeployment -ResourceGroupName my-resource-group -TemplateFile azure-monitor-deploy.json -TemplateParameterFile azure-monitor-deploy.parameters.json
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create --name GuestHealthDeployment --resource-group my-resource-group --template-file Health.VirtualMachine.template.json --parameters Health.VirtualMachine.template.parameters.json
```

---

### <a name="template-file"></a>模板文件

``` json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "virtualMachineName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the virtual machine."
      }
    },
    "virtualMachineLocation": {
      "type": "string",
      "metadata": {
        "description": "The location code of the virtual machine region (location). Examples: eastus, westeurope, etc"
      }
    },
    "virtualMachineOsType": {
      "type": "string",
      "metadata": {
        "description": "Specifies operating system type of the target virtual machine."
      },
      "allowedValues": ["windows", "linux"]
    },
    "dataCollectionRuleAssociationName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the data collection rule association to create."
      },
      "defaultValue": "VM-Health-Dcr-Association"
    },
    "healthDataCollectionRuleResourceId": {
      "type": "string",
      "metadata": {
        "description": "Specifies resource id of Azure Monitor Data Collection Rule for virtual machine health data."
      }
    }
  },
  "variables": {
    "healthExtensionProperties": {
      "windows": {
        "publisher": "Microsoft.Azure.Monitor.VirtualMachines.GuestHealth",
        "type": "GuestHealthWindowsAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true
      },
      "linux": {
        "publisher": "Microsoft.Azure.Monitor.VirtualMachines.GuestHealth",
        "type": "GuestHealthLinuxAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true
      }
    },
    "azureMonitorAgentExtensionProperties": {
      "windows": {
        "publisher": "Microsoft.Azure.Monitor", 
        "type": "AzureMonitorWindowsAgent", 
        "typeHandlerVersion": "1.0", 
        "autoUpgradeMinorVersion": false 
      },
      "linux": {
        "publisher": "Microsoft.Azure.Monitor", 
        "type": "AzureMonitorLinuxAgent", 
        "typeHandlerVersion": "1.5", 
        "autoUpgradeMinorVersion": false 
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[parameters('virtualMachineName')]",
      "location": "[parameters('virtualMachineLocation')]",
      "apiVersion": "2017-03-30",
      "identity": {
        "type": "SystemAssigned"
      }
    },
    {
      "type": "Microsoft.Compute/virtualMachines/providers/dataCollectionRuleAssociations",
      "name": "[concat(parameters('virtualMachineName'), '/Microsoft.Insights/', parameters('dataCollectionRuleAssociationName'))]",
      "apiVersion": "2019-11-01-preview",
      "properties": {
        "description": "Association of data collection rule for VM Insights Health.",
        "dataCollectionRuleId": "[parameters('healthDataCollectionRuleResourceId')]"
      }
    },
    { 
      "type": "Microsoft.Compute/virtualMachines/extensions", 
      "apiVersion": "2019-12-01", 
      "name": "[concat(parameters('virtualMachineName'), '/', variables('azureMonitorAgentExtensionProperties')[parameters('virtualMachineOsType')].type)]",
      "location": "[parameters('virtualMachineLocation')]",
      "dependsOn": [
        "[resourceId('Microsoft.Compute/virtualMachines/providers/dataCollectionRuleAssociations', parameters('virtualMachineName'), 'Microsoft.Insights', parameters('dataCollectionRuleAssociationName'))]"
      ], 
      "properties": "[variables('azureMonitorAgentExtensionProperties')[parameters('virtualMachineOsType')]]"
    },
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('virtualMachineName'), '/', variables('healthExtensionProperties')[parameters('virtualMachineOsType')].type)]",
      "location": "[parameters('virtualMachineLocation')]",
      "apiVersion": "2018-06-01",
      "dependsOn": [ 
        "[resourceId('Microsoft.Compute/virtualMachines/extensions', parameters('virtualMachineName'), variables('azureMonitorAgentExtensionProperties')[parameters('virtualMachineOsType')].type)]",
        "[resourceId('Microsoft.Compute/virtualMachines/providers/dataCollectionRuleAssociations', parameters('virtualMachineName'), 'Microsoft.Insights', parameters('dataCollectionRuleAssociationName'))]"
      ],    
      "properties": "[variables('healthExtensionProperties')[parameters('virtualMachineOsType')]]"
    }               
  ]
}
```

### <a name="sample-parameter-file"></a>示例参数文件

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "virtualMachineName": {
        "value": "myvm"
      },
      "virtualMachineLocation": {
        "value": "eastus"
      },
      "virtualMachineOsType": {
        "value": "linux"
      },
      "healthDataCollectionRuleResourceId": {
        "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/my-resource-group/providers/Microsoft.Insights/dataCollectionRules/Microsoft-VMInsights-Health"
      }
  }
}
```

## <a name="next-steps"></a>后续步骤

- [自定义 VM insights 启用的监视器](vminsights-health-configure.md)