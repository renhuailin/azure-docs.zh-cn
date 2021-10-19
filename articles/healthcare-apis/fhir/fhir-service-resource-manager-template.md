---
title: 使用 ARM 模板部署 Azure 医疗保健 API FHIR 服务
description: '了解如何使用 ARM 模板模板Azure 资源管理器部署 FHIR (服务) '
author: ginalee-dotcom
ms.service: healthcare-apis
ms.topic: tutorial
ms.author: zxue
ms.date: 08/06/2021
ms.openlocfilehash: 97322c917e12e451affc2fe8a67bb8c8f6200a74
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124782415"
---
# <a name="deploy-a-fhir-service-within-azure-healthcare-apis---using-arm-template"></a>在 Azure 医疗保健 API 中部署 FHIR 服务 - 使用 ARM 模板

> [!IMPORTANT]
> Azure Healthcare APIs 目前为预览版。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

本文将了解如何使用 Azure 资源管理器 模板 (ARM 模板在 Azure 医疗保健 API (中部署 FHIR 服务) 称为 FHIR) 。 我们提供了两个选项，即使用 PowerShell 或 CLI。

[ARM 模板](../../azure-resource-manager/templates/overview.md)是一个 JSON 文件，用于定义项目的基础结构和配置。 模板使用声明性语法。 在声明性语法中，你可以在不编写创建部署的编程命令序列的情况下，描述预期部署。

## <a name="prerequisites"></a>先决条件

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

* 具有活动订阅的 Azure 帐户。 [免费创建一个](https://azure.microsoft.com/free/)。
* 若要在本地运行代码，请安装：
    * [Azure PowerShell](/powershell/azure/install-az-ps)。

# <a name="cli"></a>[CLI](#tab/CLI)

* 具有活动订阅的 Azure 帐户。 [免费创建一个](https://azure.microsoft.com/free/)。
* 若要在本地运行代码，请安装：
    * Bash shell（例如 [Git for Windows](https://gitforwindows.org) 中包含的 Git Bash）。
    * [Azure CLI](/cli/azure/install-azure-cli)。

---

## <a name="review-the-arm-template"></a>查看 ARM 模板

本文中使用的模板来自 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/azure-api-for-fhir/)。

该模板定义了三个 Azure 资源：
- Microsoft.HealthcareApis/workspaces
- Microsoft.HealthcareApis/workspaces/fhirservices      
- Microsoft.Storage/storageAccounts

可以通过删除工作区资源、存储资源和 `dependsOn` "Microsoft.HealthcareApis/workspaces/fhirservices"资源中的 属性来部署 FHIR 服务资源。


```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "authorityurl": {
            "type": "string",
            "defaultValue": "https://login.microsoftonline.com"
        },
        "tagName": {
            "type": "string",
            "defaultValue": "My Deployment"
        },
        "region": {
            "type": "string",
                  "allowedValues": [
                "australiaeast",
                "canadacentral",
                "eastus",
                "eastus2",
                "germanywestcentral",
                "japaneast",
                "northcentralus",
                "northeurope",
                "southafricanorth",
                "southcentralus",
                "southeastasia",
                "switzerlandnorth",
                "uksouth",
                "ukwest",
                "westcentralus",
                "westeurope",
                "westus2"
            ]
        },
        "workspaceName": {
            "type": "string"
        },
        "fhirServiceName": {
            "type": "string"
        },
        "tenantid": {
            "type": "string"
        },
        "storageAccountName": {
            "type": "string"
        },
        "storageAccountConfirm": {
            "type": "bool",
            "defaultValue": true
        },
        "AccessPolicies": {
            "type": "array",
            "defaultValue": []
        },
        "smartProxyEnabled": {
            "type": "bool",
            "defaultValue": false
        }
    },
    "variables": { 
        "authority": "[Concat(parameters('authorityurl'), '/', parameters('tenantid'))]",
        "createManagedIdentity": true,
        "managedIdentityType": {
            "type": "SystemAssigned"
        },
        "storageBlobDataContributerRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'ba92f5b4-2d11-453d-a403-e96b0029c9fe')]"
    },
    "resources": [
        {
            "type": "Microsoft.HealthcareApis/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2020-11-01-preview",
            "location": "[parameters('region')]",
            "properties": {}
        },
       {          
            "type": "Microsoft.HealthcareApis/workspaces/fhirservices",
            "kind": "fhir-R4",
            "name": "[concat(parameters('workspaceName'), '/', parameters('fhirServiceName'))]",
            "apiVersion": "2020-11-01-preview",
            "location": "[parameters('region')]",
            "dependsOn": [
                "[resourceId('Microsoft.HealthcareApis/workspaces', parameters('workspaceName'))]"
            ],
            "tags": {
                "environmentName": "[parameters('tagName')]"
            },
            "properties": {
                "accessPolicies": "[parameters('AccessPolicies')]",
                "authenticationConfiguration": {
                    "authority": "[variables('Authority')]",
                    "audience": "[concat('https//', parameters('workspaceName'), '-', parameters('fhirServiceName'), '.fhir.azurehealthcareapis.com')]",
                    "smartProxyEnabled": "[parameters('smartProxyEnabled')]"
                },
                "corsConfiguration": {
                    "allowCredentials": false,
                    "headers": ["*"],
                    "maxAge": 1440,
                    "methods": ["DELETE", "GET", "OPTIONS", "PATCH", "POST", "PUT"],
                    "origins": ["https://localhost:6001"]
                },
                "exportConfiguration": {
                    "storageAccountName": "[parameters('storageAccountName')]"
                }
            },
            "identity": "[if(variables('createManagedIdentity'), variables('managedIdentityType'), json('null'))]"
        },
        {
            "name": "[parameters('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "location": "[resourceGroup().location]",
            "properties": {
                "supportsHttpsTrafficOnly": "true"
            },
            "condition": "[parameters('storageAccountConfirm')]",
            "dependsOn": [
                "[parameters('fhirServiceName')]"
            ],
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "tags": {
                "environmentName": "[parameters('tagName')]",
                "test-account-rg": "true"
            }
        }
    ],
    "outputs": {
    }
}
```

## <a name="deploy-arm-template"></a>部署 ARM 模板

可以使用两个选项部署 ARM 模板：PowerShell 或 CLI。

下面提供的示例代码使用子文件夹"src"的"templates"子文件夹中的模板。 可能需要更改位置路径以正确引用模板文件。

部署过程需要几分钟才能完成。 记下 FHIR 服务和资源组的名称，稍后将使用该名称。

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

### <a name="deploy-the-template-using-powershell"></a>部署模板：使用 PowerShell

在本地、本地、Visual Studio Code或 Azure Cloud Shell PowerShell 中运行代码，以部署 FHIR 服务。 

如果尚未登录到 Azure，请使用"连接-AzAccount"登录。 登录后，使用"Get-AzContext"验证想要使用的订阅和租户。 如果需要，可以更改订阅和租户。

可以创建新的资源组，或者通过跳过步骤或注释掉以"New-AzResourceGroup"开始行来使用现有的资源组。

```powershell-interactive
### variables
$resourcegroupname="your resource group"
$location="South Central US"
$workspacename="your workspace name"
$servicename="your fhir service name"
$tenantid="xxx"
$subscriptionid="xxx"
$storageaccountname="storage account name"
$storageaccountconfirm=1

### login to azure
Connect-AzAccount 
#Connect-AzAccount SubscriptionId $subscriptionid
Set-AzContext -Subscription $subscriptionid
Connect-AzAccount -Tenant $tenantid -SubscriptionId $subscriptionid
#Get-AzContext 

### create resource group
New-AzResourceGroup -Name $resourcegroupname -Location $location

### deploy the resource
New-AzResourceGroupDeployment -ResourceGroupName $resourcegroupname -TemplateFile "src/templates/fhirtemplate.json" -region $location -workspaceName $workspacename -fhirServiceName $fhirservicename -tenantid $tenantid -storageAccountName $storageaccountname -storageAccountConfirm $storageaccountconfirm
```
# <a name="cli"></a>[CLI](#tab/CLI)

### <a name="deploy-the-template-using-cli"></a>部署模板：使用 CLI

在本地、本地或Visual Studio Code运行Azure Cloud Shell，以部署 FHIR 服务。 

如果尚未登录到 Azure，请使用"az login"登录。 登录后，使用"az account show --output table"验证想要使用的订阅和租户。 如果需要，可以更改订阅和租户。

可以创建新的资源组，或者通过跳过步骤或注释掉以"az group create"开始行来使用现有的资源组。

```azurecli-interactive
### variables
resourcegroupname=your resource group name
location=southcentralus
workspacename=your workspace name
fhirservicename=your fhir service name
tenantid=xxx
subscriptionid=xxx
storageaccountname=your storage account name
storageaccountconfirm=true

### login to azure
az login
az account show --output table
az account set --subscription $subscriptionid

### create resource group
az group create --name $resourcegroupname --location $location

### deploy the resource
az deployment group create --resource-group $resourcegroupname --template-file 'src\\templates\\fhirtemplate.json' --parameters region=$location workspaceName=$workspacename fhirServiceName=$fhirservicename tenantid=$tenantid storageAccountName=$storageaccountname storageAccountConfirm=$storageaccountconfirm
```

---

## <a name="review-the-deployed-resources"></a>查看已部署的资源

可以通过打开浏览器并导航到 来验证 FHIR 服务是否正在运行 `https://<yourfhir servic>.azurehealthcareapis.com/metadata` 。 如果自动显示或下载了功能语句，则部署成功。 

## <a name="clean-up-the-resources"></a>清理资源

如果不再需要资源，请运行以下代码以删除资源组。

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)
```powershell-interactive
$resourceGroupName = “your resource group name”
Remove-AzResourceGroup -Name $resourceGroupName
```
# <a name="cli"></a>[CLI](#tab/CLI)
```azurecli-interactive
resourceGroupName = “your resource group name”
az group delete --name $resourceGroupName
```
---

## <a name="next-steps"></a>后续步骤

本快速入门指南使用 ARM 模板在 Azure Healthcare APis 中部署了 FHIR 服务。 有关 FHIR 服务支持的功能详细信息，请参阅。

>[!div class="nextstepaction"]
>[支持的 FHIR 功能](fhir-features-supported.md)