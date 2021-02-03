---
title: 在 Azure Stack Edge Pro GPU 设备上启用 Azure Monitor
description: 描述如何在 Azure Stack Edge Pro GPU 设备上启用 Azure Monitor。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/02/2021
ms.author: alkohli
ms.openlocfilehash: 171b4dbfb2a5852e270c483a28cad31f97dcb42b
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493906"
---
# <a name="enable-azure-monitor-on-your-azure-stack-edge-pro-gpu-device"></a>在 Azure Stack Edge Pro GPU 设备上启用 Azure Monitor

当你运行多个计算应用程序时，Azure Stack Edge Pro GPU 设备上的监视容器非常重要。 Azure Monitor 使你能够从设备上运行的 Kubernetes 群集收集容器日志、内存和处理器指标。

本文介绍了在设备上启用 Azure Monitor 并在 Log Analytics 工作区中收集容器日志所需的步骤。 Azure Stack Edge Pro GPU 设备目前不支持 Azure Monitor 度量值存储。 


## <a name="prerequisites"></a>先决条件

在开始之前，需要：

- Azure Stack Edge Pro 设备。 请确保根据 [教程：激活设备](azure-stack-edge-gpu-deploy-activate.md)中的步骤激活设备。
- 已根据教程完成 **配置计算** 步骤：在设备上的 [Azure Stack Edge Pro 设备上配置计算](azure-stack-edge-gpu-deploy-configure-compute.md) 。 设备应有关联的 IoT 中心资源、IoT 设备和 IoT Edge 设备。


## <a name="create-log-analytics-workspace"></a>创建 Log Analytics 工作区

执行以下步骤以创建 log analytics 工作区。 Log analytics 工作区是收集和存储日志数据的逻辑存储单元。

1. 在 Azure 门户中，选择 " **+ 创建资源** " 并搜索 **Log Analytics "工作区** ，然后选择" **创建**"。 
1. 在 " **创建 Log Analytics" 工作区** 中，配置以下设置。 接受余数作为默认值。

    1. 在 " **基本** 信息" 选项卡上，提供工作区的订阅、资源组、名称和区域。 

        ![Log Analytics 工作区的 "基本信息" 选项卡](media/azure-stack-edge-gpu-enable-azure-monitor/create-log-analytics-workspace-basics-1.png)  

    1. 在 " **定价层** " 选项卡上，接受默认的即 **用即付计划**。

        ![Log Analytics 工作区的 "定价" 选项卡](media/azure-stack-edge-gpu-enable-azure-monitor/create-log-analytics-workspace-pricing-1.png) 

    1. 在 " **查看** " 和 "创建" 选项卡上，查看工作区的信息，然后选择 " **创建**"。

        ![查看和创建 Log Analytics 工作区](media/azure-stack-edge-gpu-enable-azure-monitor/create-log-analytics-workspace-review-create-1.png)

有关详细信息，请参阅 [通过 Azure 门户创建 Log Analytics 工作区](../azure-monitor/learn/quick-create-workspace.md)中的详细步骤。



## <a name="enable-container-insights"></a>启用容器见解

执行以下步骤以在工作区中启用容器见解。 

1. 按照 [如何添加 Azure Monitor 容器解决方案](../azure-monitor/insights/container-insights-hybrid-setup.md#how-to-add-the-azure-monitor-containers-solution)中的详细步骤进行操作。 使用以下模板文件 `containerSolution.json` ：

    ```yml
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceResourceId": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace Resource ID"
            }
        },
        "workspaceRegion": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace region"
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "name": "[Concat('ContainerInsights', '-',  uniqueString(parameters('workspaceResourceId')))]",
            "apiVersion": "2017-05-10",
            "subscriptionId": "[split(parameters('workspaceResourceId'),'/')[2]]",
            "resourceGroup": "[split(parameters('workspaceResourceId'),'/')[4]]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "apiVersion": "2015-11-01-preview",
                            "type": "Microsoft.OperationsManagement/solutions",
                            "location": "[parameters('workspaceRegion')]",
                            "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                            "properties": {
                                "workspaceResourceId": "[parameters('workspaceResourceId')]"
                            },
                            "plan": {
                                "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                                "product": "[Concat('OMSGallery/', 'ContainerInsights')]",
                                "promotionCode": "",
                                "publisher": "Microsoft"
                            }
                        }
                    ]
                },
                "parameters": {}
            }
            }
        ]
    }
    ```

1. 获取资源 ID 和位置。 转到  `Your Log Analytics workspace > General > Properties` 。 复制以下信息：

    - **资源 id**，它是 azure Log Analytics 工作区的完全限定的 AZURE 资源 id。 
    - **位置**，即 Azure 区域。

    ![Log Analytics 工作区的属性](media/azure-stack-edge-gpu-enable-azure-monitor/log-analytics-workspace-properties-1.png) 

1. 使用以下参数文件 `containerSolutionParams.json` 。 将替换 `workspaceResourceId` 为资源 ID，将替换为 `workspaceRegion` 在前面步骤中复制的位置。

    ```yaml
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        "workspaceResourceId": {
            "value": "/subscriptions/fa68082f-8ff7-4a25-95c7-ce9da541242f/resourcegroups/myaserg/providers/microsoft.operationalinsights/workspaces/myaseloganalyticsws"
        },
        "workspaceRegion": {
        "value": "westus"
        }
        }
    }
    ```
    
    下面是启用了容器 Insights 的 Log Analytics 工作区的示例输出：
    
    ```powershell
    Requesting a Cloud Shell.Succeeded.
    Connecting terminal...
    MOTD: Switch to Bash from PowerShell: bash
    VERBOSE: Authenticating to Azure ...
    VERBOSE: Building your Azure drive ...
    
    PS /home/alpa> az account set -s fa68082f-8ff7-4a25-95c7-ce9da541242f
    PS /home/alpa> ls
    clouddrive  containerSolution.json
    PS /home/alpa> ls
    clouddrive  containerSolution.json  containerSolutionParams.json
    PS /home/alpa> az deployment group create --resource-group myaserg --name Testdeployment1 --template-file containerSolution.json --parameters containerSolutionParams.json
    {- Finished ..
        "id": "/subscriptions/fa68082f-8ff7-4a25-95c7-ce9da541242f/resourceGroups/myaserg/providers/Microsoft.Resources/deployments/Testdeployment1",
        "location": null,
        "name": "Testdeployment1",
        "properties": {
        "correlationId": "3a9045fe-2de0-428c-b17b-057508a8c575",
        "debugSetting": null,
        "dependencies": [],
        "duration": "PT11.1588316S",
        "error": null,
        "mode": "Incremental",
        "onErrorDeployment": null,
        "outputResources": [
            {
            "id": "/subscriptions/fa68082f-8ff7-4a25-95c7-ce9da541242f/resourceGroups/myaserg/providers/Microsoft.OperationsManagement/solutions/ContainerInsights(myaseloganalyticsws)",
            "resourceGroup": "myaserg"
            }
        ],
        "outputs": null,
        "parameters": {
            "workspaceRegion": {
            "type": "String",
            "value": "westus"
            },
            "workspaceResourceId": {
            "type": "String",
            "value": "/subscriptions/fa68082f-8ff7-4a25-95c7-ce9da541242f/resourcegroups/myaserg/providers/microsoft.operationalinsights/workspaces/myaseloganalyticsws"
            }
        },
        "parametersLink": null,
        "providers": [
            {
            "id": null,
            "namespace": "Microsoft.Resources",
            "registrationPolicy": null,
            "registrationState": null,
            "resourceTypes": [
                {
                "aliases": null,
                "apiProfiles": null,
                "apiVersions": null,
                "capabilities": null,
                "defaultApiVersion": null,
                "locations": [
                    null
                ],
                "properties": null,
                "resourceType": "deployments"
                }
            ]
            }
        ],
        "provisioningState": "Succeeded",
        "templateHash": "10500027184662969395",
        "templateLink": null,
        "timestamp": "2020-11-06T22:09:56.908983+00:00",
        "validatedResources": null
        },
        "resourceGroup": "myaserg",
        "tags": null,
        "type": "Microsoft.Resources/deployments"
    }
    PS /home/alpa>
    ```

## <a name="configure-azure-monitor-on-your-device"></a>在设备上配置 Azure Monitor

1. 中转到新创建的 Log Analytics 资源，并将 **工作区 ID** 和 **主密钥** (工作区密钥) 。

    ![Log Analytics 工作区中的代理管理](media/azure-stack-edge-gpu-enable-azure-monitor/log-analytics-workspace-agents-management-1.png)

    保存此信息，因为你将在后面的步骤中使用它。

1. [连接到设备的 PowerShell 接口](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface)。 
    
1. 使用 log analytics 工作区 ID 和工作区密钥，使用以下 cmdlet：

    `Set-HcsKubernetesAzureMonitorConfiguration -WorkspaceId <> -WorkspaceKey <>`

1. 启用 Azure Monitor 后，你应该会在 "Log Analytics" 工作区中看到 "日志"。 若要查看设备上部署的 Kubernetes 群集的状态，请参阅 **Azure Monitor > Insights > 容器**。 对于 "环境" 选项，请选择 " **全部**"。 

    ![Log Analytics 工作区中的指标](media/azure-stack-edge-gpu-enable-azure-monitor/log-analytics-workspace-metrics-1.png)

## <a name="next-steps"></a>后续步骤

- 了解如何 [通过 Kubernetes 仪表板监视 Kubernetes 工作负荷](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md)。
- 了解如何 [管理设备事件警报通知](azure-stack-edge-gpu-manage-device-event-alert-notifications.md)。 