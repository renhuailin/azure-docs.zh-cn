---
title: 如何停止监视 Azure Red Hat OpenShift v3 群集 | Microsoft Docs
description: 本文介绍如何通过容器见解停止监视 Azure Red Hat OpenShift 群集。
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: 6a278898942e40d3347791d31a3c38341df01f7c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "101731810"
---
# <a name="how-to-stop-monitoring-your-azure-red-hat-openshift-v3-cluster"></a>如何停止监视 Azure Red Hat OpenShift v3 群集

>[!IMPORTANT]
> Azure Red Hat OpenShift 3.11 将于 2022 年 6 月停用。
>
> 从 2020 年 10 月开始，你将无法再创建新的 3.11 群集。
> 现有的 3.11 群集将继续运行到 2022 年 6 月，但在此日期之后将不再受支持。
>
> 请按照本指南[创建 Azure Red Hat OpenShift 4 群集](../../openshift/tutorial-create-cluster.md)。
> 如有具体问题，[请与我们联系](mailto:aro-feedback@microsoft.com)。

启用 Azure Red Hat OpenShift 版本 3.x 群集的监视后，如果用户决定不再想要对其进行监视，可以使用“容器见解”停止监视群集。 本文介绍如何使用提供的 Azure 资源管理器模板完成此操作。  

## <a name="azure-resource-manager-template"></a>Azure 资源管理器模板

下面提供了两个 Azure 资源管理器模板，以支持在资源组中一致且重复地删除解决方案资源。 一个是 JSON 模板，用于指定配置以停止监视，另一个模板包含配置的参数值，用于指定在其中部署群集的 OpenShift 群集资源 ID 和 Azure 区域。

如果不熟悉使用模板部署资源的概念，请参阅：
* [使用 Resource Manager 模板和 Azure PowerShell 部署资源](../../azure-resource-manager/templates/deploy-powershell.md)
* [使用资源管理器模板和 Azure CLI 部署资源](../../azure-resource-manager/templates/deploy-cli.md)

如果选择使用 Azure CLI，首先需要在本地安装和使用 CLI。 必须运行 Azure CLI 版本 2.0.65 或更高版本。 若要确定版本，请运行 `az --version`。 如果需要安装或升级 Azure CLI，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

### <a name="create-template"></a>创建模板

1. 将以下 JSON 语法复制并粘贴到文件中：

    ```json
    {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
         "aroResourceId": {
           "type": "string",
           "metadata": {
             "description": "ARO Cluster Resource ID"
          }
        },
        "aroResourceLocation": {
          "type": "string",
          "metadata": {
            "description": "Location of the aro cluster resource e.g. westcentralus"
          }
        }
      },
      "resources": [
        {
           "name": "[split(parameters('aroResourceId'),'/')[8]]",
           "type": "Microsoft.ContainerService/openShiftManagedClusters",
           "location": "[parameters('aroResourceLocation')]",
           "apiVersion": "2019-09-30-preview",
           "properties": {
             "mode": "Incremental",
             "id": "[parameters('aroResourceId')]",
             "monitorProfile": {
               "workspaceResourceID": null,
               "enabled": false
            }
          }
        }
      ]
    }
    ```

2. 将此文件以“OptOutTemplate.json”文件名保存到本地文件夹  。

3. 将以下 JSON 语法粘贴到文件中：

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aroResourceId": {
          "value": "/subscriptions/<subscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.ContainerService/openShiftManagedClusters/<clusterName>"
        },
        "aroResourceLocation": {
          "value": "<azure region of the cluster e.g. westcentralus>"
        }
      }
    }
    ```

4. 使用可在选定群集的“属性”页面上找到的 OpenShift 群集的值，编辑 aroResourceId 和 aroResourceLocation 的值。

    ![容器属性页面](media/container-insights-optout-openshift/cluster-properties-page.png)

5. 将此文件以“OptOutParam.json”文件名保存到本地文件夹  。

6. 已做好部署此模板的准备。

### <a name="remove-the-solution-using-azure-cli"></a>使用 Azure CLI 删除解决方案

在 Linux 上使用 Azure CLI 执行以下命令以删除解决方案并清除群集上的配置。

```azurecli
az login   
az account set --subscription "Subscription Name"
az deployment group create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
```

配置更改可能需要几分钟才能完成。 完成后，系统会返回包含结果的消息，如下所示：

```output
ProvisioningState       : Succeeded
```

### <a name="remove-the-solution-using-powershell"></a>使用 PowerShell 删除解决方案

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

在包含模板的文件夹中执行以下 PowerShell 命令以删除解决方案并从群集中清除配置。    

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <yourSubscriptionName>
New-AzResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
```

配置更改可能需要几分钟才能完成。 完成后，系统会返回包含结果的消息，如下所示：

```output
ProvisioningState       : Succeeded
```

## <a name="next-steps"></a>后续步骤

如果创建的工作区仅用于支持监视群集且不再被需要，则需要手动删除它。 如果不熟悉如何删除工作区，请参阅[删除 Azure Log Analytics 工作区](../logs/delete-workspace.md)。