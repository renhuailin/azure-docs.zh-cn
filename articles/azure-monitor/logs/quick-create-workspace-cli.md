---
title: 使用 Azure CLI 创建 Log Analytics 工作区 | Microsoft Docs
description: 了解如何使用 Azure CLI 创建 Log Analytics 工作区，以启用管理解决方案以及从云和本地环境进行的数据收集。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/26/2020
ms.openlocfilehash: f15658d7d800f9c7d4ab5cbfe193386a66ba7602
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122324754"
---
# <a name="create-a-log-analytics-workspace-with-azure-cli-20"></a>使用 Azure CLI 2.0 创建 Log Analytics 工作区

Azure CLI 2.0 用于从命令行或脚本创建和管理 Azure 资源。 本快速入门展示了如何使用 Azure CLI 2.0 在 Azure Monitor 中部署 Log Analytics 工作区。 Log Analytics 工作区是适用于 Azure Monitor 日志数据的唯一环境。 每个工作区都有其自己的数据存储库和配置，并且数据源和解决方案均配置为将其数据存储在特定工作区中。 如果打算从以下源收集数据，则需要 Log Analytics 工作区：

* 订阅中的 Azure 资源  
* 受 System Center Operations Manager 监视的本地计算机  
* Configuration Manager 中的设备集合  
* Azure 存储中的诊断或日志数据  


[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- 本文需要 Azure CLI 版本 2.0.30 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。

## <a name="create-a-workspace"></a>创建工作区
使用 [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create) 创建工作区。 以下示例使用本地计算机上的资源管理器模板在 eastus 位置中创建工作区。 JSON 模板在经过配置后，只提示你输入工作区的名称，并为其他参数指定默认值，这些参数将会用作环境中的标准配置。 也可以将模板存储在 Azure 存储帐户中，以便在组织中共享访问。 有关使用模板的更多信息，请参阅[使用资源管理器模板和 Azure CLI 部署资源](../../azure-resource-manager/templates/deploy-cli.md)

有关支持的区域的信息，请参阅[可在哪些区域使用 Log Analytics](https://azure.microsoft.com/regions/services/)，并在“搜索产品”字段中搜索 Azure Monitor。

以下参数设置默认值：

* 位置 - 默认为“美国东部”
* sku - 默认为新的“按 GB”定价层，该层已在 2018 年 4 月的定价模型中发布

>[!WARNING]
>如果在订阅中创建或配置 Log Analytics 工作区，而该订阅已加入 2018 年 4 月的新定价模型，则唯一有效的 Log Analytics 定价层为 **PerGB2018**。
>

### <a name="create-and-deploy-template"></a>创建和部署模板

1. 将以下 JSON 语法复制并粘贴到文件中：

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
        "location": {
            "type": "String",
            "allowedValues": [
              "eastus",
              "westus"
            ],
            "defaultValue": "eastus",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        },
        "sku": {
            "type": "String",
            "allowedValues": [
              "Standalone",
              "PerNode",
              "PerGB2018"
            ],
            "defaultValue": "PerGB2018",
            "metadata": {
            "description": "Specifies the service tier of the workspace: Standalone, PerNode, Per-GB"
        }
          }
    },
    "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2015-11-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": {
                    "Name": "[parameters('sku')]"
                },
                "features": {
                    "searchVersion": 1
                }
            }
          }
       ]
    }
    ```

2. 按要求编辑模板。 查看 [Microsoft.OperationalInsights/workspaces 模板](/azure/templates/microsoft.operationalinsights/2015-11-01-preview/workspaces)参考，了解支持的属性和值。
3. 在本地文件夹中将此文件另存为 **deploylaworkspacetemplate.json**。   
4. 已做好部署此模板的准备。 在包含模板的文件夹中使用以下命令。 当系统提示输入工作区名称时，提供一个在资源组中唯一的名称。

    ```azurecli
    az deployment group create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deploylaworkspacetemplate.json
    ```

部署可能需要几分钟才能完成。 完成后，会看到一条包含结果的消息，如下所示：

![部署完成后的示例结果](media/quick-create-workspace-cli/template-output-01.png)

## <a name="troubleshooting"></a>疑难解答
如果创建了一个工作区，该工作区已在过去 14 天内删除且处于[软删除状态](../logs/delete-workspace.md#soft-delete-behavior)，那么该操作可能会有不同的结果，具体取决于你的工作区配置：
1. 如果你提供的工作区名称、资源组、订阅和区域与已删除的工作区中的相同，则将恢复你的工作区，包括其数据、配置和连接的代理。
2. 每个资源组的工作区名称必须是唯一的。 如果你使用已经存在（也在资源组的软删除中）的工作区名称，则会收到错误消息“工作区名称‘workspace-name’不唯一”或“冲突” 。 若要替换软删除，同时永久删除你的工作区并创建新的同名工作区，请按照以下步骤，先恢复工作区再执行永久删除：
   * [恢复](../logs/delete-workspace.md#recover-workspace)工作区
   * [永久删除](../logs/delete-workspace.md#permanent-workspace-delete)工作区
   * 使用相同的工作区名称创建新的工作区

## <a name="next-steps"></a>后续步骤
现在，你已有可用的工作区，可以配置监视遥测收集、运行日志搜索分析该数据，以及添加管理解决方案以提供其他数据和分析见解。  

* 若要启用通过 Azure 诊断或 Azure 存储从 Azure 资源收集数据，请参阅[收集要在 Log Analytics 中使用的 Azure 服务日志和指标](../essentials/resource-logs.md#send-to-log-analytics-workspace)。  
* [将 System Center Operations Manager 添加为数据源](../agents/om-agents.md)以从报告 Operations Manager 管理组的代理收集数据并将其存储在 Log Analytics 工作区中。  
* 连接 [Configuration Manager](../logs/collect-sccm.md) 以导入作为层次结构中集合成员的计算机。  
* 查看可用的[监视解决方案](../insights/solutions.md)以及如何从工作区添加或删除解决方案。

