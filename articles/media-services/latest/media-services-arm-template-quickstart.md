---
title:媒体服务帐户 ARM 模板:Azure 媒体服务说明:本文介绍如何使用 ARM 模板创建媒体服务帐户。
services: media-services documentationcenter: '' author:IngridAtMicrosoft manager: femila editor: ''

ms.service: media-services ms.workload: ms.topic: quickstart ms.date: 03/23/2021 ms.author: inhenkel ms.custom: subject-armqs

---

# <a name="quickstart-media-services-account-arm-template"></a>快速入门：媒体服务帐户 ARM 模板

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

本文介绍如何使用Azure 资源管理器模板（ARM 模板）创建媒体服务帐户。

## <a name="introduction"></a>简介

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

熟悉 ARM 模板的读者可以继续阅读[部署部分](#deploy-the-template)。

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请选择“部署到 Azure”按钮。 Azure 门户中会打开模板。

[![部署到 Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.media%2Fmedia-services-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

如果之前从未部署过 ARM 模板，请阅读 [Azure ARM 模板](../../azure-resource-manager/templates/index.yml)并浏览[教程](../../azure-resource-manager/templates/template-tutorial-create-first-template.md?tabs=azure-powershell)，这会很有帮助。

## <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/media-services-create/)。

JSON 代码隔离区的语法为：

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.media/media-services-create/azuredeploy.json":::

该模板中定义了三种 Azure 资源类型：

- [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)：创建存储帐户
- [Microsoft.Media/mediaservices](/azure/templates/microsoft.media/mediaservices)：创建媒体服务帐户

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

![已创建快速入门资源](./media/media-services-arm-template-quickstart/quickstart-arm-template-resources.png)

## <a name="clean-up-resources"></a>清理资源

如果你不打算使用刚刚创建的资源，则可以删除资源组。

```azurecli-interactive

az group delete --name {name of the resource group}

```

## <a name="next-steps"></a>后续步骤

若要通过使用参数、变量等创建模板的过程来了解有关使用 ARM 模板的更多信息，请尝试

> [!div class="nextstepaction"]
> [创建并部署第一个 ARM 模板](../../azure-resource-manager/templates/template-tutorial-create-first-template.md)
