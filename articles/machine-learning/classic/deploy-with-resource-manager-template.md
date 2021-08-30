---
title: ML 工作室（经典）：使用 Azure 资源管理器部署工作区 - Azure
description: 如何使用 Azure 资源管理器模板为机器学习工作室（经典）部署工作区
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18, devx-track-azurepowershell
ms.date: 02/05/2018
ms.openlocfilehash: 4e29e480141d457ee7956c72e280f45160eda687
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122695552"
---
# <a name="deploy-machine-learning-studio-classic-workspace-using-azure-resource-manager"></a>使用 Azure 资源管理器部署机器学习工作室（经典）工作区

**适用对象：** ![适用于.](../../../includes/media/aml-applies-to-skus/yes.png)机器学习工作室（经典）   ![不适用于.](../../../includes/media/aml-applies-to-skus/no.png)[Azure 机器学习](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

[!INCLUDE [ML Studio (classic) retirement](../../../includes/machine-learning-studio-classic-deprecation.md)]

通过提供部署带有验证和重试机制的互连组件的可扩展方法，使用 Azure 资源管理器部署模板可节约时间。 例如，若要设置机器学习工作室（经典）工作区，需要先配置 Azure 存储帐户，然后再部署工作区。 想象为数百个工作区手动执行此操作的样子。 更轻松的替代方法是使用 Azure 资源管理器模板部署工作室（经典）工作区及其所有依赖项。 本文将引导逐步完成此过程。 有关 Azure 资源管理器的整体概述，请参阅 [Azure 资源管理器概述](../../azure-resource-manager/management/overview.md)。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="step-by-step-create-a-machine-learning-workspace"></a>分布说明：创建机器学习工作区
我们将创建 Azure 资源组，然后使用资源管理器模板部署新的 Azure 存储帐户和新的机器学习工作室（经典）工作区。 部署完成后，我们将打印有关所创建工作区的重要信息（主密钥、workspaceID 和该工作区的 URL）。

### <a name="create-an-azure-resource-manager-template"></a>创建 Azure 资源管理器模板

机器学习工作区需要 Azure 存储帐户才能存储链接的数据集。
下面的模板使用资源组名称生成存储帐户名称和工作区名称。  创建工作区时，它还将存储帐户名称用作属性。

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "variables": {
        "namePrefix": "[resourceGroup().name]",
        "location": "[resourceGroup().location]",
        "mlVersion": "2016-04-01",
        "stgVersion": "2015-06-15",
        "storageAccountName": "[concat(variables('namePrefix'),'stg')]",
        "mlWorkspaceName": "[concat(variables('namePrefix'),'mlwk')]",
        "mlResourceId": "[resourceId('Microsoft.MachineLearning/workspaces', variables('mlWorkspaceName'))]",
        "stgResourceId": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "storageAccountType": "Standard_LRS"
    },
    "resources": [
        {
            "apiVersion": "[variables('stgVersion')]",
            "name": "[variables('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[variables('location')]",
            "properties": {
                "accountType": "[variables('storageAccountType')]"
            }
        },
        {
            "apiVersion": "[variables('mlVersion')]",
            "type": "Microsoft.MachineLearning/workspaces",
            "name": "[variables('mlWorkspaceName')]",
            "location": "[variables('location')]",
            "dependsOn": ["[variables('stgResourceId')]"],
            "properties": {
                "UserStorageAccountId": "[variables('stgResourceId')]"
            }
        }
    ],
    "outputs": {
        "mlWorkspaceObject": {"type": "object", "value": "[reference(variables('mlResourceId'), variables('mlVersion'))]"},
        "mlWorkspaceToken": {"type": "string", "value": "[listWorkspaceKeys(variables('mlResourceId'), variables('mlVersion')).primaryToken]"},
        "mlWorkspaceWorkspaceID": {"type": "string", "value": "[reference(variables('mlResourceId'), variables('mlVersion')).WorkspaceId]"},
        "mlWorkspaceWorkspaceLink": {"type": "string", "value": "[concat('https://studio.azureml.net/Home/ViewWorkspace/', reference(variables('mlResourceId'), variables('mlVersion')).WorkspaceId)]"}
    }
}

```
将此模板保存为 c:\temp\ 下的 mlworkspace.json 文件。

### <a name="deploy-the-resource-group-based-on-the-template"></a>根据模板部署资源组

* 打开 PowerShell
* 为 Azure 资源管理器和 Azure 服务管理安装模块

```powershell
# Install the Azure Resource Manager modules from the PowerShell Gallery (press "A")
Install-Module Az -Scope CurrentUser

# Install the Azure Service Management modules from the PowerShell Gallery (press "A")
Install-Module Azure -Scope CurrentUser
```

   这些步骤将下载和安装完成剩余步骤所需的模块。 这在执行 PowerShell 命令的环境中仅需操作一次。

* 向 Azure 进行身份验证

```powershell
# Authenticate (enter your credentials in the pop-up window)
Connect-AzAccount
```
此步骤需要为每个会话重复执行。 验证身份后，会显示订阅信息。

![Azure 帐户](./media/deploy-with-resource-manager-template/azuresubscription.png)

既然我们可以访问 Azure，我们也可以创建资源组。

* 创建资源组

```powershell
$rg = New-AzResourceGroup -Name "uniquenamerequired523" -Location "South Central US"
$rg
```

验证资源组是否预配正确。 **ProvisioningState** 应为“Succeeded”。
资源组名称由模板使用，用于生成存储帐户名称。 存储帐户名称长度必须为 3 到 24 个字符，并且只能使用数字和小写字母。

![资源组](./media/deploy-with-resource-manager-template/resourcegroupprovisioning.png)

* 使用资源组部署可部署新机器学习工作区。

```powershell
# Create a Resource Group, TemplateFile is the location of the JSON template.
$rgd = New-AzResourceGroupDeployment -Name "demo" -TemplateFile "C:\temp\mlworkspace.json" -ResourceGroupName $rg.ResourceGroupName
```

完成部署后，可直接访问所部属工作区的属性。 例如，可访问主密钥令牌。

```powershell
# Access Machine Learning Studio (classic) Workspace Token after its deployment.
$rgd.Outputs.mlWorkspaceToken.Value
```

另一种检索现有工作区令牌的方法是使用 Invoke-AzResourceAction 命令。 例如，可列出所有工作区的主要和辅助令牌。

```powershell
# List the primary and secondary tokens of all workspaces
Get-AzResource |? { $_.ResourceType -Like "*MachineLearning/workspaces*"} |ForEach-Object { Invoke-AzResourceAction -ResourceId $_.ResourceId -Action listworkspacekeys -Force}
```
预配工作区后，还可使用[适用于机器学习工作室（经典）的 PowerShell 模块](https://aka.ms/amlps)自动执行许多机器学习工作室（经典）任务。

## <a name="next-steps"></a>后续步骤

* 了解有关[编写 Azure 资源管理器模板](../../azure-resource-manager/templates/syntax.md)的详细信息
* 请查看 [Azure 快速启动模板存储库](https://github.com/Azure/azure-quickstart-templates)。
* 观看有关 [Azure 资源管理器](https://channel9.msdn.com/Events/Ignite/2015/C9-39)的视频。
* 请参阅[资源管理器模板参考帮助](/azure/templates/microsoft.machinelearning/allversions)

<!--Link references-->