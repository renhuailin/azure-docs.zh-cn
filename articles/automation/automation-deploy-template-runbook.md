---
title: 在 Azure 自动化 PowerShell Runbook 中部署 Azure 资源管理器模板
description: 本文介绍如何通过 PowerShell Runbook 部署 Azure 存储中存储的 Azure 资源管理器模板。
services: automation
ms.subservice: process-automation
ms.date: 09/23/2021
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 61ff25cd9878ee94ce0ba6db7b2c4e4ac8e649de
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2021
ms.locfileid: "129057750"
---
# <a name="deploy-an-azure-resource-manager-template-in-an-automation-powershell-runbook"></a>在自动化 PowerShell Runbook 中部署 Azure 资源管理器模板

可以编写一个[自动化 PowerShell Runbook](./learn/automation-tutorial-runbook-textual-powershell.md)，用于通过 [Azure 资源管理模板](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)部署 Azure 资源。 借助模板可以通过 Azure 自动化来自动部署 Azure 资源。 可以在一个安全的中心位置（例如 Azure 存储）维护资源管理器模板。

本文创建一个 PowerShell Runbook，该 Runbook 使用 [Azure 存储](../storage/common/storage-introduction.md)中存储的资源管理器模板部署新的 Azure 存储帐户。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

* 至少具有一个用户分配的托管标识的 Azure 自动化帐户。 有关详细信息，请参阅[对 Azure 自动化帐户使用用户分配的托管标识](./add-user-assigned-identity.md)。

* Az 模块：`Az.Accounts`、`Az.ManagedServiceIdentity`、`Az.Resources` 和 `Az.Storage`。 导入到自动化帐户中。 有关详细信息，请参阅[导入 Az 模块](./shared-resources/modules.md#import-az-modules)。

* 要在其中存储资源管理器模板的 [Azure 存储帐户](../storage/common/storage-account-create.md)。

* 安装在本地计算机上的 Azure PowerShell。 若要详细了解如何获得 Azure PowerShell，请参阅[安装 Azure Powershell 模块](/powershell/azure/install-az-ps)。 还需要模块 [Az.ManagedServiceIdentity](/powershell/module/az.managedserviceidentity)。 `Az.ManagedServiceIdentity` 是预览模块，不作为 Az 模块的一部分安装。 若要安装该模块，请运行 `Install-Module -Name Az.ManagedServiceIdentity`

## <a name="assign-permissions-to-managed-identities"></a>向托管标识分配权限

向托管标识分配权限，以执行 Runbook 中的存储相关任务。

1. 使用 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet 以交互方式登录到 Azure，并按照说明进行操作。

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }
    
    # If you have multiple subscriptions, set the one to use
    # Select-AzSubscription -SubscriptionId <SUBSCRIPTIONID>
    ```

1. 为以下变量提供适当的值，然后执行脚本。

    ```powershell
    $resourceGroup = "resourceGroup"
    $automationAccount = "automationAccount"
    $storageAccount = "storageAccount"
    $userAssignedManagedIdentity = "userAssignedManagedIdentity"
    $storageTemplate = "path\storageTemplate.json"
    $runbookScript = "path\runbookScript.ps1"
    ```

1. 为系统分配的托管标识分配 `reader` 角色以执行 cmdlet `Get-AzUserAssignedIdentity`。

    ```powershell
    $SAMI = (Get-AzAutomationAccount -ResourceGroupName $resourceGroup -Name $automationAccount).Identity.PrincipalId
    New-AzRoleAssignment `
        -ObjectId $SAMI `
        -ResourceGroupName $resourceGroup `
        -RoleDefinitionName "Reader"
    ```

1. 将角色 `Storage Account Contributor` 分配给用户分配的托管标识，以针对存储帐户执行操作。

    ```powershell
    $UAMI_ID = (Get-AzUserAssignedIdentity -ResourceGroupName $resourceGroup -Name $userAssignedManagedIdentity).PrincipalId
    New-AzRoleAssignment `
        -ObjectId $UAMI_ID `
        -ResourceGroupName $resourceGroup `
        -RoleDefinitionName "Storage Account Contributor"
    ```


## <a name="create-the-resource-manager-template"></a>创建 Resource Manager 模板

在本示例中，使用用于部署新 Azure 存储帐户的资源管理器模板。 创建名为 `storageTemplate.json` 的本地文件，然后粘贴以下代码：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "2018-02-01",
      "location": "[parameters('location')]",
      "sku": {
          "name": "[parameters('storageAccountType')]"
      },
      "kind": "Storage", 
      "properties": {
      }
    }
  ],
  "outputs": {
      "storageAccountName": {
          "type": "string",
          "value": "[variables('storageAccountName')]"
      }
  }
}
```

## <a name="save-the-resource-manager-template-in-azure-files"></a>在 Azure 文件存储中保存资源管理器模板

使用 PowerShell 创建 Azure 文件共享并上传 `storageTemplate.json`。 有关如何在 Azure 门户中创建文件共享和上传文件的说明，请参阅[在 Windows 上开始使用 Azure 文件存储](../storage/files/storage-files-quick-create-use-windows.md)。

运行以下命令创建文件共享，并将资源管理器模板上传到该文件共享。

```powershell
# Get the access key for your storage account
$key = Get-AzStorageAccountKey -ResourceGroupName $resourceGroup -Name $storageAccount

# Create an Azure Storage context using the first access key
$context = New-AzStorageContext -StorageAccountName $storageAccount -StorageAccountKey $key[0].value

# Create a file share named 'resource-templates' in your Azure Storage account
$fileShare = New-AzStorageShare -Name 'resource-templates' -Context $context

# Add the storageTemplate.json file to the new file share
Set-AzStorageFileContent -ShareName $fileShare.Name -Context $context -Source $storageTemplate
```

## <a name="create-the-powershell-runbook-script"></a>创建 PowerShell Runbook 脚本

创建一个 PowerShell 脚本，用于从 Azure 存储获取 `storageTemplate.json` 文件，并部署模板来创建新的 Azure 存储帐户。  创建名为 `runbookScript.ps1` 的本地文件，然后粘贴以下代码：


```powershell
param (
    [Parameter(Mandatory=$true)]
    [string]
    $resourceGroup,

    [Parameter(Mandatory=$true)]
    [string]
    $storageAccount,

    [Parameter(Mandatory=$true)]
    [string]
    $storageAccountKey,

    [Parameter(Mandatory=$true)]
    [string]
    $storageFileName,

    [Parameter(Mandatory=$true)]
    [string]
    $userAssignedManagedIdentity
)

# Ensures you do not inherit an AzContext in your runbook
Disable-AzContextAutosave -Scope Process

# Connect to Azure with user-assigned managed identity
$AzureContext = (Connect-AzAccount -Identity).context
$identity = Get-AzUserAssignedIdentity -ResourceGroupName $resourceGroup `
    -Name $userAssignedManagedIdentity `
    -DefaultProfile $AzureContext
$AzureContext = (Connect-AzAccount -Identity -AccountId $identity.ClientId).context

# set and store context
$AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription `
    -DefaultProfile $AzureContext

#Set the parameter values for the Resource Manager template
$Parameters = @{
    "storageAccountType"="Standard_LRS"
    }

# Create a new context
$Context = New-AzStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageAccountKey

Get-AzStorageFileContent `
    -ShareName 'resource-templates' `
    -Context $Context `
    -path 'storageTemplate.json' `
    -Destination 'C:\Temp' -Force

$TemplateFile = Join-Path -Path 'C:\Temp' -ChildPath $storageFileName

# Deploy the storage account
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroup `
    -TemplateFile $TemplateFile `
    -TemplateParameterObject $Parameters 
```

## <a name="import-and-publish-the-runbook-into-your-azure-automation-account"></a>在 Azure 自动化帐户中导入并发布 Runbook

使用 PowerShell 将 Runbook 导入自动化帐户，并发布该 Runbook。 有关在 Azure 门户中导入和发布 Runbook 的信息，请参阅[在 Azure 自动化中管理 Runbook](manage-runbooks.md)。

若要将 `runbookScript.ps1` 以 PowerShell Runbook 的形式导入自动化帐户，请运行以下 PowerShell 命令：

```powershell
$importParams = @{
    Path = $runbookScript
    ResourceGroupName = $resourceGroup
    AutomationAccountName = $automationAccount
    Type = "PowerShell"
}
Import-AzAutomationRunbook @importParams

# Publish the runbook
$publishParams = @{
    ResourceGroupName = $resourceGroup
    AutomationAccountName = $automationAccount
    Name = "runbookScript"
}
Publish-AzAutomationRunbook @publishParams
```

## <a name="start-the-runbook"></a>启动 Runbook

现在，通过调用 [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook) cmdlet 来启动该 Runbook。 有关如何在 Azure 门户中启动 Runbook 的信息，请参阅[在 Azure 自动化中启动 Runbook](./start-runbooks.md)。

在 PowerShell 控制台中运行以下命令：

```powershell
# Set up the parameters for the runbook
$runbookParams = @{
    resourceGroup = $resourceGroup
    storageAccount = $storageAccount
    storageAccountKey = $key[0].Value # We got this key earlier
    storageFileName = "storageTemplate.json"
    userAssignedManagedIdentity = $userAssignedManagedIdentity
}

# Set up parameters for the Start-AzAutomationRunbook cmdlet
$startParams = @{
    resourceGroup = $resourceGroup
    AutomationAccountName = $automationAccount
    Name = "runbookScript"
    Parameters = $runbookParams
}

# Start the runbook
$job = Start-AzAutomationRunbook @startParams
```

Runbook 运行后，可以通过检索作业对象 `$job.Status` 的属性值来检查其状态。

Runbook 会获取资源管理器模板，并使用它来部署新的 Azure 存储帐户。 运行以下命令后，可以看到已创建新的存储帐户：

```powershell
Get-AzStorageAccount
```

## <a name="next-steps"></a>后续步骤

* 若要详细了解资源管理器模板，请参阅 [Azure 资源管理器概述](../azure-resource-manager/management/overview.md)。
* 若要开始使用 Azure 存储，请参阅 [Azure 存储简介](../storage/common/storage-introduction.md)。
* 若要查找其他有用的 Azure 自动化 Runbook，请参阅[在 Azure 自动化中使用 Runbook 和模块](automation-runbook-gallery.md)。
