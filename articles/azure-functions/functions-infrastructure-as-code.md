---
title: 自动将函数应用资源部署到 Azure
description: 了解如何生成用于部署函数应用的 Azure 资源管理器模板。
ms.assetid: d20743e3-aab6-442c-a836-9bcea09bfd32
ms.topic: conceptual
ms.date: 04/03/2019
ms.custom: fasttrack-edit, devx-track-azurepowershell
ms.openlocfilehash: 62330b341d2aa59f33b5f780005042d114400748
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128625670"
---
# <a name="automate-resource-deployment-for-your-function-app-in-azure-functions"></a>为 Azure Functions 中的函数应用自动执行资源部署

可以使用 Azure 资源管理器模板来部署函数应用。 本文概述了完成此操作所需的资源和参数。 可能还需要部署其他资源，具体取决于函数应用中的[触发器和绑定](functions-triggers-bindings.md)。

有关创建模板的详细信息，请参阅[创作 Azure 资源管理器模板](../azure-resource-manager/templates/syntax.md)。

有关示例模板，请参阅：
- [基于消耗计划的函数应用]
- [基于 Azure 应用服务计划的函数应用]

## <a name="required-resources"></a>所需资源

Azure Functions 部署通常包括以下资源：

| 资源                                                                           | 要求 | 语法和属性参考                                                         |
|------------------------------------------------------------------------------------|-------------|-----------------------------------------------------------------------------------------|
| 函数应用                                                                     | 必选    | [Microsoft.Web/sites](/azure/templates/microsoft.web/sites)                             |
| [Azure 存储](../storage/index.yml)帐户                                   | 必选    | [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| [Application Insights](../azure-monitor/app/app-insights-overview.md) 组件 | 可选    | [Microsoft.Insights/components](/azure/templates/microsoft.insights/components)         |
| [托管计划](./functions-scale.md)                                             | 可选<sup>1</sup>    | [Microsoft.Web/serverfarms](/azure/templates/microsoft.web/serverfarms)                 |

<sup>1</sup>只有选择在[高级计划](./functions-premium-plan.md)或[应用服务计划](../app-service/overview-hosting-plans.md)上运行函数应用时，托管计划才是必需的。

> [!TIP]
> 虽然不是必需的，但强烈建议为应用配置 Application Insights。

<a name="storage"></a>
### <a name="storage-account"></a>存储帐户

函数应用需要 Azure 存储帐户。 你需要一个支持 blob、表、队列和文件的常规用途帐户。 有关详细信息，请参阅 [Azure Functions 存储帐户要求](storage-considerations.md#storage-account-requirements)。

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2019-06-01",
    "location": "[resourceGroup().location]",
    "kind": "StorageV2",
    "sku": {
        "name": "[parameters('storageAccountType')]"
    }
}
```

还必须在站点配置中将 `AzureWebJobsStorage` 属性指定为应用设置。 如果函数应用未使用 Application Insights 进行监视，还应将 `AzureWebJobsDashboard` 指定为应用设置。

Azure Functions 运行时使用 `AzureWebJobsStorage` 连接字符串创建内部队列。  未启用 Application Insights 时，运行时使用 `AzureWebJobsDashboard` 连接字符串登录到 Azure 表存储并启动门户中的“监视”选项卡。

这些属性在 `siteConfig` 对象中的 `appSettings` 集合中指定：

```json
"appSettings": [
    {
        "name": "AzureWebJobsStorage",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
    },
    {
        "name": "AzureWebJobsDashboard",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
    }
]
```

### <a name="application-insights"></a>Application Insights

建议使用 Application Insights 监视函数应用。 使用类型“Microsoft.Insights/components”和种类“web”定义 Application Insights 资源：

```json
        {
            "apiVersion": "2015-05-01",
            "name": "[variables('appInsightsName')]",
            "type": "Microsoft.Insights/components",
            "kind": "web",
            "location": "[resourceGroup().location]",
            "tags": {
                "[concat('hidden-link:', resourceGroup().id, '/providers/Microsoft.Web/sites/', variables('functionAppName'))]": "Resource"
            },
            "properties": {
                "Application_Type": "web",
                "ApplicationId": "[variables('appInsightsName')]"
            }
        },
```

此外，需要使用 `APPINSIGHTS_INSTRUMENTATIONKEY` 应用程序设置向函数应用提供检测密钥。 此属性在 `siteConfig` 对象的 `appSettings` 集合中指定：

```json
"appSettings": [
    {
        "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
    }
]
```

### <a name="hosting-plan"></a>托管计划

托管计划的定义是变化的，并且可能是下列项之一：
* [消耗计划](#consumption)（默认值）
* [高级计划](#premium)
* [应用服务计划](#app-service-plan)

### <a name="function-app"></a>函数应用

函数应用资源是使用类型为 **Microsoft.Web/sites** 且种类为 **functionapp** 的资源定义的：

```json
{
    "apiVersion": "2015-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Insights/components', variables('appInsightsName'))]"
    ]
}
```

> [!IMPORTANT]
> 如果你要显式定义托管计划，则 dependsOn 数组中可能将需要一个额外的项：`"[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"`

函数应用必须包括以下应用程序设置：

| 设置名                 | 说明                                                                               | 示例值                        |
|------------------------------|-------------------------------------------------------------------------------------------|---------------------------------------|
| AzureWebJobsStorage          | Functions 运行时用于内部排队的存储帐户的连接字符串 | 请参阅[存储帐户](#storage)       |
| FUNCTIONS_EXTENSION_VERSION  | Azure Functions 运行时的版本                                                | `~3`                                  |
| FUNCTIONS_WORKER_RUNTIME     | 要为此应用中的函数使用的语言堆栈                                   | `dotnet`、`node`、`java`、`python` 或 `powershell` |
| WEBSITE_NODE_DEFAULT_VERSION | 只有当使用 `node` 语言堆栈时必需，指定要使用的版本              | `10.14.1`                             |

这些属性是在 `siteConfig` 属性中的 `appSettings` 集合中指定的：

```json
"properties": {
    "siteConfig": {
        "appSettings": [
            {
                "name": "AzureWebJobsStorage",
                "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
            },
            {
                "name": "FUNCTIONS_WORKER_RUNTIME",
                "value": "node"
            },
            {
                "name": "WEBSITE_NODE_DEFAULT_VERSION",
                "value": "10.14.1"
            },
            {
                "name": "FUNCTIONS_EXTENSION_VERSION",
                "value": "~3"
            }
        ]
    }
}
```

<a name="consumption"></a>

## <a name="deploy-on-consumption-plan"></a>在消耗计划上部署

消耗计划会在代码运行时自动分配计算能力，根据需要增加分配以处理负载，然后在代码未运行时相应减少分配。 你不需要为空闲的 VM 付费，也不需要提前保留容量。 若要了解更多信息，请参阅 [Azure Functions 的缩放和托管](consumption-plan.md)。

有关 Azure 资源管理器模板示例，请参阅[基于消耗计划的函数应用]。

### <a name="create-a-consumption-plan"></a>创建消耗计划

无需定义消耗计划。 创建函数应用资源本身时，不会基于区域自动创建或选择消耗计划。

消耗计划是一种特殊的“serverfarm”资源。 对于 Windows，可以通过为 `computeMode` 和 `sku` 属性使用 `Dynamic` 值来指定它：

```json
{
   "type":"Microsoft.Web/serverfarms",
   "apiVersion":"2016-09-01",
   "name":"[variables('hostingPlanName')]",
   "location":"[resourceGroup().location]",
   "properties":{
      "name":"[variables('hostingPlanName')]",
      "computeMode":"Dynamic"
   },
   "sku":{
      "name":"Y1",
      "tier":"Dynamic",
      "size":"Y1",
      "family":"Y",
      "capacity":0
   }
}
```

> [!NOTE]
> 不能显式为 Linux 定义消耗计划。 它将自动创建。

如果确实显式定义了消耗计划，则需要在应用上设置 `serverFarmId` 属性，使其指向计划的资源 ID。 你还应当确保函数应用有一个针对该计划的 `dependsOn` 设置。

### <a name="create-a-function-app"></a>创建函数应用

在消耗计划中运行的函数应用所需的设置在 Windows 和 Linux 之间有所不同。

#### <a name="windows"></a>Windows

在 Windows 上，消耗计划要求在站点配置中指定另一项设置：[`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](functions-app-settings.md#website_contentazurefileconnectionstring)。 此属性配置用于存储函数应用代码和配置的存储帐户。

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~3"
                }
            ]
        }
    }
}
```

> [!IMPORTANT]
> 请勿在部署槽位中设置 [`WEBSITE_CONTENTSHARE`](functions-app-settings.md#website_contentshare) 设置。 在部署槽位中创建应用时，会生成此设置。

#### <a name="linux"></a>Linux

在 Linux 上，函数应用必须将其 `kind` 设置为 `functionapp,linux`，并将其 `reserved` 属性设置为 `true`。

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp,linux",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountName'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~3"
                }
            ]
        },
        "reserved": true
    }
}
```

Linux 不支持 [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](functions-app-settings.md#website_contentazurefileconnectionstring) 和 [`WEBSITE_CONTENTSHARE`](functions-app-settings.md#website_contentshare) 设置。

<a name="premium"></a>
## <a name="deploy-on-premium-plan"></a>在高级计划上部署

高级计划提供与消耗计划相同的扩展，但包括专用资源和附加功能。 若要了解详细信息，请参阅 [Azure Functions 高级计划](./functions-premium-plan.md)。

### <a name="create-a-premium-plan"></a>创建高级计划

高级计划是特殊类型的“serverfarm”资源。 可以通过使用 `EP1`、`EP2` 或 `EP3` 作为 `sku` [描述对象](/azure/templates/microsoft.web/2018-02-01/serverfarms#skudescription-object)中的 `Name` 属性值来指定它。

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[parameters('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[parameters('hostingPlanName')]",
        "workerSize": "[parameters('workerSize')]",
        "workerSizeId": "[parameters('workerSizeId')]",
        "numberOfWorkers": "[parameters('numberOfWorkers')]",
        "hostingEnvironment": "[parameters('hostingEnvironment')]",
        "maximumElasticWorkerCount": "20"
    },
    "sku": {
        "Tier": "ElasticPremium",
        "Name": "EP1"
    }
}
```

### <a name="create-a-function-app"></a>创建函数应用

高级计划的函数应用必须将 `serverFarmId` 属性设置为之前创建的计划的资源 ID。 此外，高级计划要求在站点配置中指定一项额外设置：[`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](functions-app-settings.md#website_contentazurefileconnectionstring)。 此属性配置用于存储函数应用代码和配置的存储帐户。

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~3"
                }
            ]
        }
    }
}
```
> [!IMPORTANT]
> 请勿设置 [`WEBSITE_CONTENTSHARE`](functions-app-settings.md#website_contentshare) 设置，因为它是在第一次创建站点时为你生成的。

<a name="app-service-plan"></a>

## <a name="deploy-on-app-service-plan"></a>在应用服务计划上部署

在应用服务计划中，函数应用在基本、标准和高级 SKU 中的专用 VM 上运行，类似于 Web 应用。 如需详细了解如何使用应用服务计划，请参阅 [Azure 应用服务计划深入概述](../app-service/overview-hosting-plans.md)。

有关 Azure 资源管理器模板示例，请参阅[基于 Azure 应用服务计划的函数应用]。

### <a name="create-an-app-service-plan"></a>创建应用服务计划

应用服务计划是由“serverfarm”资源定义的。

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "S1",
        "tier": "Standard",
        "size": "S1",
        "family": "S",
        "capacity": 1
    }
}
```

若要在 Linux 上运行应用，还必须将 `kind` 设置为 `Linux`：

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "kind": "Linux",
    "sku": {
        "name": "S1",
        "tier": "Standard",
        "size": "S1",
        "family": "S",
        "capacity": 1
    }
}
```

### <a name="create-a-function-app"></a>创建函数应用

应用服务计划上的函数应用必须将 `serverFarmId` 属性设置为之前创建的计划的资源 ID。

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~3"
                }
            ]
        }
    }
}
```

Linux 应用还应包含 `siteConfig` 下的 `linuxFxVersion` 属性。 如果只是部署代码，则此值由所需的运行时堆栈决定，格式为 ```runtime|runtimeVersion```：

| 堆栈            | 示例值                                         |
|------------------|-------------------------------------------------------|
| Python           | `python|3.7`      |
| JavaScript       | `node|12`          |
| .NET             | `dotnet|3.1` |

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~3"
                }
            ],
            "linuxFxVersion": "node|12"
        }
    }
}
```

如果要[部署自定义容器映像](./functions-create-function-linux-custom-image.md)，必须将其指定为 `linuxFxVersion`，并包括允许拉取映像的配置，如[用于容器的 Web 应用](../app-service/index.yml)中所示。 此外，将 `WEBSITES_ENABLE_APP_SERVICE_STORAGE` 设置为 `false`，因为容器本身提供了应用内容：

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~3"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_URL",
                    "value": "[parameters('dockerRegistryUrl')]"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
                    "value": "[parameters('dockerRegistryUsername')]"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
                    "value": "[parameters('dockerRegistryPassword')]"
                },
                {
                    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
                    "value": "false"
                }
            ],
            "linuxFxVersion": "DOCKER|myacr.azurecr.io/myimage:mytag"
        }
    }
}
```

## <a name="deploy-to-azure-arc"></a>部署到 Azure Arc

Azure Functions 可以部署到[支持 Azure Arc 的 Kubernetes](../app-service/overview-arc-integration.md)。 此过程主要遵循[部署到应用服务计划](#deploy-on-app-service-plan)，但有几个差异需要注意。

若要创建应用和计划资源，必须已为支持 Azure Arc 的 Kubernetes 群集[创建应用服务 Kubernetes 环境](../app-service/manage-create-arc-environment.md)。 这些示例假定你知道要部署到的自定义位置和应用服务 Kubernetes 环境的资源 ID。 对于大多数模板，可以将这两个资源 ID 作为参数提供。

```json
{
    "parameters": {
        "kubeEnvironmentId" : {
            "type": "string"
        },
        "customLocationId" : {
            "type": "string"
        }
    }
}
```

站点和计划必须通过 `extendedLocation` 字段引用自定义位置。 此块位于 `properties` 之外，与 `kind` 和 `location` 对等：

```json
{
    "extendedLocation": {
        "type": "customlocation",
        "name": "[parameters('customLocationId')]"
    },
}
```

计划资源应该使用 Kubernetes (K1) SKU，并且其 `kind` 字段应该为“linux,kubernetes”。 在 `properties` 中，`reserved` 应该为“true”，并且 `kubeEnvironmentProfile.id` 应设置为应用服务 Kubernetes 环境的资源 ID。 示例计划可能如下所示：

```json
{
    "type": "Microsoft.Web/serverfarms",
    "name": "[variables('hostingPlanName')]",
    "location": "[parameters('location')]",
    "apiVersion": "2020-12-01",
    "kind": "linux,kubernetes",
    "sku": {
        "name": "K1",
        "tier": "Kubernetes"
    },
    "extendedLocation": {
        "type": "customlocation",
        "name": "[parameters('customLocationId')]"
    },
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "location": "[parameters('location')]",
        "workerSizeId": "0",
        "numberOfWorkers": "1",
        "kubeEnvironmentProfile": {
            "id": "[parameters('kubeEnvironmentId')]"
        },
        "reserved": true
    }
}
```

函数应用资源的 `kind` 字段应设置为“functionapp,linux,kubernetes”或“functionapp,linux,kubernetes,container”，具体取决于是打算通过代码还是容器进行部署。 示例函数应用可能如下所示：

```json
 {
    "apiVersion": "2018-11-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('appName')]",
    "kind": "kubernetes,functionapp,linux,container",
    "location": "[parameters('location')]",
    "extendedLocation": {
        "type": "customlocation",
        "name": "[parameters('customLocationId')]"
    },
    "dependsOn": [
        "[resourceId('Microsoft.Insights/components', variables('appInsightsName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[variables('hostingPlanId')]"
    ],
    "properties": {
        "serverFarmId": "[variables('hostingPlanId')]",
        "siteConfig": {
            "linuxFxVersion": "DOCKER|mcr.microsoft.com/azure-functions/dotnet:3.0-appservice-quickstart",
            "appSettings": [
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~3"
                },
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"

                },
                {
                    "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
                    "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
                }
            ],
            "alwaysOn": true
        }
    }
}
```

## <a name="customizing-a-deployment"></a>自定义部署

函数应用有许多可用于部署的子资源，包括应用设置和源代码管理选项。 还可以选择删除 **sourcecontrols** 子资源，改用另一个 [部署选项](functions-continuous-deployment.md)。

> [!IMPORTANT]
> 若要使用 Azure 资源管理器成功部署应用程序，了解如何在 Azure 中部署资源尤为重要。 在下面的示例中，通过使用 **siteConfig** 应用顶级配置。 请务必在顶级设置这些配置，因为这些配置会将信息传达给 Functions 运行时和部署引擎。 应用 **sourcecontrols/web** 子资源前，需要顶级信息。 虽然可以在子级别 **config/appSettings** 资源中配置这些设置，但在某些情况下，需要在应用 **config/appSettings** 之前部署函数应用。 比如在[逻辑应用](../logic-apps/index.yml)中使用函数时，函数是另一资源的依赖项。

```json
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('appName')]",
  "type": "Microsoft.Web/sites",
  "kind": "functionapp",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Web/serverfarms', parameters('appName'))]"
  ],
  "properties": {
     "serverFarmId": "[variables('appServicePlanName')]",
     "siteConfig": {
        "alwaysOn": true,
        "appSettings": [
            {
                "name": "FUNCTIONS_EXTENSION_VERSION",
                "value": "~3"
            },
            {
                "name": "Project",
                "value": "src"
            }
        ]
     }
  },
  "resources": [
     {
        "apiVersion": "2015-08-01",
        "name": "appsettings",
        "type": "config",
        "dependsOn": [
          "[resourceId('Microsoft.Web/Sites', parameters('appName'))]",
          "[resourceId('Microsoft.Web/Sites/sourcecontrols', parameters('appName'), 'web')]",
          "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
        ],
        "properties": {
          "AzureWebJobsStorage": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]",
          "AzureWebJobsDashboard": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]",
          "FUNCTIONS_EXTENSION_VERSION": "~3",
          "FUNCTIONS_WORKER_RUNTIME": "dotnet",
          "Project": "src"
        }
     },
     {
          "apiVersion": "2015-08-01",
          "name": "web",
          "type": "sourcecontrols",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites/', parameters('appName'))]"
          ],
          "properties": {
            "RepoUrl": "[parameters('sourceCodeRepositoryURL')]",
            "branch": "[parameters('sourceCodeBranch')]",
            "IsManualIntegration": "[parameters('sourceCodeManualIntegration')]"
          }
     }
  ]
}
```
> [!TIP]
> 此模板使用 [Project](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file) 应用设置值，这将设置基本目录，Functions 部署引擎 (Kudu) 在此目录中查找可部署代码。 在存储库内，函数位于 **src** 文件夹的子文件夹中。 因此，在前一个示例中，将应用设置值设置为 `src`。 如果函数位于存储库的根目录中，或者不从源代码管理进行部署，则可删除此应用设置值。

## <a name="deploy-your-template"></a>部署模板

可以使用以下任意方法部署模板：

* [PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
* [Azure CLI](../azure-resource-manager/templates/deploy-cli.md)
* [Azure 门户](../azure-resource-manager/templates/deploy-portal.md)
* [REST API](../azure-resource-manager/templates/deploy-rest.md)

### <a name="deploy-to-azure-button"></a>“部署到 Azure”按钮

将 ```<url-encoded-path-to-azuredeploy-json>``` 替换为 GitHub 中 `azuredeploy.json` 文件的原始路径的 [URL 编码](https://www.bing.com/search?q=url+encode)版本。

以下示例使用 Markdown：

```markdown
[![Deploy to Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>)
```

以下示例使用 HTML：

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"></a>
```

### <a name="deploy-using-powershell"></a>使用 PowerShell 进行部署

以下 PowerShell 命令将创建一个资源组并部署一个模板，该模板将创建具有所需资源的函数应用。 若要在本地运行，必须安装 [Azure PowerShell](/powershell/azure/install-az-ps)。 运行 [`Connect-AzAccount`](/powershell/module/az.accounts/connect-azaccount) 进行登录。

```powershell
# Register Resource Providers if they're not already registered
Register-AzResourceProvider -ProviderNamespace "microsoft.web"
Register-AzResourceProvider -ProviderNamespace "microsoft.storage"

# Create a resource group for the function app
New-AzResourceGroup -Name "MyResourceGroup" -Location 'West Europe'

# Create the parameters for the file, which for this template is the function app name.
$TemplateParams = @{"appName" = "<function-app-name>"}

# Deploy the template
New-AzResourceGroupDeployment -ResourceGroupName "MyResourceGroup" -TemplateFile template.json -TemplateParameterObject $TemplateParams -Verbose
```

若要测试此部署，可以使用[这样的模板](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.web/function-app-create-dynamic/azuredeploy.json)，该模板在消耗计划中在 Windows 上创建函数应用。 将 `<function-app-name>` 替换为你的函数应用的唯一名称。

## <a name="next-steps"></a>后续步骤

深入了解如何开发和配置 Azure Functions。

* [Azure Functions 开发人员参考](functions-reference.md)
* [如何配置 Azure 函数应用设置](functions-how-to-use-azure-function-app-settings.md)
* [创建第一个 Azure 函数](./functions-get-started.md)

<!-- LINKS -->

[基于消耗计划的函数应用]: https://azure.microsoft.com/resources/templates/function-app-create-dynamic/
[基于 Azure 应用服务计划的函数应用]: https://azure.microsoft.com/resources/templates/function-app-create-dedicated/
