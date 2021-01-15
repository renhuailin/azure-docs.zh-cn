---
title: 媒体服务帐户 ARM 模板
titleSuffix: Azure Media Services
description: 本文介绍如何使用 ARM 模板创建媒体服务帐户。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: quickstart
ms.date: 11/24/2020
ms.author: inhenkel
ms.custom: subject-armqs
ms.openlocfilehash: 6a23c3a20e79fe6fff7de8faccf4e4ef78f02585
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185024"
---
# <a name="quickstart-media-services-account-arm-template"></a>快速入门：媒体服务帐户 ARM 模板

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

本文介绍如何使用Azure 资源管理器模板（ARM 模板）创建媒体服务帐户。

## <a name="introduction"></a>简介

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

熟悉 ARM 模板的读者可以继续阅读[部署部分](#deploy-the-template)。

<!-- this section will be added when the template is merged. If your environment meets the prerequisites and you're familiar with using ARM templates, select the **Deploy to Azure** button. The template will open in the Azure portal.

[![Deploy to Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/<template's URI>)
-->

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

如果之前从未部署过 ARM 模板，请阅读 [Azure ARM 模板](https://docs.microsoft.com/azure/azure-resource-manager/templates/)并浏览[教程](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-tutorial-create-first-template?tabs=azure-powershell)，这会很有帮助。

## <a name="review-the-template"></a>查看模板

<!-- this will be added when the template is merged. The template used in this quickstart is from [Azure Quickstart Templates](https://azure.microsoft.com/resources/templates/101-media-services-account-create/).

The syntax for the JSON code fence is:

:::code language="json" source="~/quickstart-templates/101-media-services-account-create/azuredeploy.json"::: -->

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "mediaServiceName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Media Services account. A Media Services account name is unique in a given region, all lowercase letters or numbers with no spaces."
      }
    }
  },
  "variables": {
    "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "name": "[parameters('mediaServiceName')]",
      "type": "Microsoft.Media/mediaServices",
      "apiVersion": "2018-07-01",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageName'))]"
      ],
      "properties": {
        "storageAccounts": [
          {
            "id": "[resourceId('microsoft.storage/storageaccounts/', variables('storageName'))]",
            "type": "Primary"
          }
        ]
      }
    },
    {
      "name": "[variables('storageName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      },
      "kind": "StorageV2",
      "apiVersion": "2017-10-01",
      "location": "[resourceGroup().location]",
      "tags": {},
      "scale": null,
      "properties": {
          "encryption": {
              "services": {
                  "file": {
                      "enabled": true
                  },
                  "blob": {
                      "enabled": true
                  }
              },
              "keySource": "Microsoft.Storage"
          },
          "accessTier": "Hot"
      }
    }
  ]
}

```

该模板中定义了三种 Azure 资源类型：

- [Microsoft.Media/mediaservices](https://docs.microsoft.com/azure/templates/microsoft.media/mediaservices)：创建媒体服务帐户
- [Microsoft.Storage/storageAccounts](https://docs.microsoft.com/azure/templates/microsoft.storage/storageaccounts)：创建存储帐户

## <a name="set-the-account"></a>设置帐户

```azurecli-interactive

az account set --subscription {your subscription name or id}

```

## <a name="create-a-resource-group"></a>创建资源组

```azurecli-interactive

az group create --name {the name you want to give your resource group} --location "{pick a location}"

```

## <a name="assign-a-variable-to-your-deployment-file"></a>向部署文件分配变量

为方便起见，请创建一个存储模板文件路径的变量。 此变量使你能够更轻松地运行部署命令，因为无需在每次部署时都重新键入路径。

```azurecli-interactive

templateFile="{provide the path to the template file}"

```

## <a name="deploy-the-template"></a>部署模板

系统会提示输入媒体服务帐户名。

```azurecli-interactive

az deployment group create --name {the name you want to give to your deployment} --resource-group {the name of resource group you created} --template-file $templateFile

```

## <a name="review-deployed-resources"></a>查看已部署的资源

应会显示如下所示的 JSON 响应：

```json
{
  "id": "/subscriptions/{subscriptionid}/resourceGroups/amsarmquickstartrg/providers/Microsoft.Resources/deployments/amsarmquickstartdeploy",
  "location": null,
  "name": "amsarmquickstartdeploy",
  "properties": {
    "correlationId": "{correlationid}",
    "debugSetting": null,
    "dependencies": [
      {
        "dependsOn": [
          {
            "id": "/subscriptions/{subscriptionid}/resourceGroups/amsarmquickstartrg/providers/Microsoft.Storage/storageAccounts/storagey44cfdmliwatk",
            "resourceGroup": "amsarmquickstartrg",
            "resourceName": "storagey44cfdmliwatk",
            "resourceType": "Microsoft.Storage/storageAccounts"
          }
        ],
        "id": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/amsarmquickstartrg/providers/Microsoft.Media/mediaServices/{accountname}",
        "resourceGroup": "amsarmquickstartrg",
        "resourceName": "{accountname}",
        "resourceType": "Microsoft.Media/mediaServices"
      }
    ],
    "duration": "PT1M10.8615001S",
    "error": null,
    "mode": "Incremental",
    "onErrorDeployment": null,
    "outputResources": [
      {
        "id": "/subscriptions/{subscriptionid}/resourceGroups/amsarmquickstartrg/providers/Microsoft.Media/mediaServices/{accountname}",
        "resourceGroup": "amsarmquickstartrg"
      },
      {
        "id": "/subscriptions/{subscriptionid}/resourceGroups/amsarmquickstartrg/providers/Microsoft.Storage/storageAccounts/storagey44cfdmliwatk",
        "resourceGroup": "amsarmquickstartrg"
      }
    ],
    "outputs": null,
    "parameters": {
      "mediaServiceName": {
        "type": "String",
        "value": "{accountname}"
      }
    },
    "parametersLink": null,
    "providers": [
      {
        "id": null,
        "namespace": "Microsoft.Media",
        "registrationPolicy": null,
        "registrationState": null,
        "resourceTypes": [
          {
            "aliases": null,
            "apiVersions": null,
            "capabilities": null,
            "locations": [
              "eastus"
            ],
            "properties": null,
            "resourceType": "mediaServices"
          }
        ]
      },
      {
        "id": null,
        "namespace": "Microsoft.Storage",
        "registrationPolicy": null,
        "registrationState": null,
        "resourceTypes": [
          {
            "aliases": null,
            "apiVersions": null,
            "capabilities": null,
            "locations": [
              "eastus"
            ],
            "properties": null,
            "resourceType": "storageAccounts"
          }
        ]
      }
    ],
    "provisioningState": "Succeeded",
    "templateHash": "{templatehash}",
    "templateLink": null,
    "timestamp": "2020-11-24T23:25:52.598184+00:00",
    "validatedResources": null
  },
  "resourceGroup": "amsarmquickstartrg",
  "tags": null,
  "type": "Microsoft.Resources/deployments"
}

```

在 Azure 门户中，确认已创建资源。

![已创建快速入门资源](./media/quickstart-arm-template-account/quickstart-arm-template-resources.png)

## <a name="clean-up-resources"></a>清理资源

如果你不打算使用刚刚创建的资源，则可以删除资源组。

```azurecli-interactive

az group delete --name {name of the resource group}

```

## <a name="next-steps"></a>后续步骤

若要通过使用参数、变量等创建模板的过程来了解有关使用 ARM 模板的更多信息，请尝试

> [!div class="nextstepaction"]
> [创建并部署第一个 ARM 模板](/azure/azure-resource-manager/templates/template-tutorial-create-first-template)