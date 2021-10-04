---
title: 资源管理器模板中的自定义参数
description: 了解如何使用资源管理器模板中的自定义参数在 Azure 数据工厂中进行持续集成和交付。
ms.service: data-factory
ms.subservice: ci-cd
author: nabhishek
ms.author: abnarain
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 09/24/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 22c64b5489cbcf0206624e613fb9d3449fa4a4d1
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129219225"
---
# <a name="use-custom-parameters-with-the-resource-manager-template"></a>将自定义参数用于资源管理器模板

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

如果开发实例具有关联的 Git 存储库，则可替代通过发布或导出模板生成的资源管理器模板的默认资源管理器模板参数。 在以下情况下，建议替代默认的资源管理器参数配置：

* 你使用自动化 CI/CD，想要在部署资源管理器期间更改某些属性，但这些属性在默认情况下不会参数化。
* 工厂太大，包含的参数数目超过了允许的最大数目 (256)，以致默认的资源管理器模板无效。

    若要处理自定义参数 256 限制，有三个选项：    
  
    * 使用自定义参数文件，并删除不需要参数化的属性（即，可以保留默认值并因此减少参数计数的属性）。
    * 重构数据流中的逻辑以减少参数。例如，管道参数均具有相同的值，你只需使用全局参数即可。
    * 将一个数据工厂拆分为多个数据流。

若要替代默认资源管理器参数配置，请转到“管理”中心，在“源代码管理”部分中选择“ARM 模板” 。 在“ARM 参数配置”部分下，单击“编辑参数配置”中的“编辑”图标以打开资源管理器参数配置代码编辑器 。

:::image type="content" source="media/author-management-hub/management-hub-custom-parameters.png" alt-text="管理自定义参数":::

> [!NOTE]
> ARM 参数配置仅在“GIT 模式”下启用。 当前它在“实时模式”或“数据工厂”模式下处于禁用状态。

创建自定义资源管理器参数配置会在 git 分支的根文件夹中创建一个名为 arm-template-parameters-definition.js 的文件。 必须使用这个具体的文件名。

:::image type="content" source="media/continuous-integration-delivery/custom-parameters.png" alt-text="自定义参数文件":::

从协作分支发布时，数据工厂将读取此文件，并使用其配置来生成参数化的属性。 如果找不到文件，则使用默认模板。

导出资源管理器模板时，数据工厂将从当前正在处理的任一分支读取此文件，而不是从协作分支读取。 可以在某个专用分支中创建或编辑文件，在此文件中，可以通过选择 UI 中的“导出 ARM 模板”来测试更改。 然后，可将该文件合并到协作分支。

> [!NOTE]
> 自定义资源管理器参数配置不会更改 ARM 模板参数限制 256。 它允许选择和减少参数化属性的数目。

## <a name="custom-parameter-syntax"></a>自定义参数语法

下面是创建自定义参数文件 **arm-template-parameters-definition.json** 时要遵循的一些准则。 对于下述每个实体类型，该文件都包含一个节：触发器、管道、链接服务、数据集、集成运行时和数据流。

* 输入相关实体类型下的属性路径。
* 将属性名称设置为 `*` 表示要将其下的所有属性参数化（仅参数化到第一个级别，而不是递归性的参数化）。 还可为此配置提供例外情况。
* 将属性值设置为字符串表示你希望参数化该属性。 使用格式 `<action>:<name>:<stype>`。
   *  `<action>` 可以是以下字符之一：
      * `=` 表示将当前值保留为参数的默认值。
      * `-` 表示不保留参数的默认值。
      * `|` 是 Azure Key Vault 中的机密的特例，用于连接字符串或密钥。
   * `<name>` 是参数的名称。 如果为空，将采用属性的名称。 如果值以 `-` 字符开头，则会简写名称。 例如，`AzureStorage1_properties_typeProperties_connectionString` 将简写为 `AzureStorage1_connectionString`。
   * `<stype>` 是参数的类型。 如果 `<stype>` 为空，则默认类型为 `string`。 支持的值：`string`、`securestring`、`int`、`bool`、`object`、`secureobject` 和 `array`。
* 在定义文件中指定数组表示模板中匹配的属性是一个数组。 数据工厂使用数组的集成运行时对象中指定的定义来循环访问该数组中的所有对象。 第二个对象（一个字符串）成为属性的名称，这用作每次遍历的参数的名称。
* 定义不能特定于资源实例。 任何定义都将应用到该类型的所有资源。
* 默认情况下，会参数化 Key Vault 机密等安全字符串，以及连接字符串、密钥和令牌等安全字符串。
 
## <a name="sample-parameterization-template"></a>示例参数化模板

下面举例说明了资源管理器参数配置的样式：

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "activities": [{
                "typeProperties": {
                    "waitTimeInSeconds": "-::int",
                    "headers": "=::object"
                }
            }]
        }
    },
    "Microsoft.DataFactory/factories/integrationRuntimes": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "typeProperties": {
                "recurrence": {
                    "*": "=",
                    "interval": "=:triggerSuffix:int",
                    "frequency": "=:-freq"
                },
                "maxConcurrency": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "secretAccessKey": "|"
                }
            }
        },
        "AzureDataLakeStore": {
            "properties": {
                "typeProperties": {
                    "dataLakeStoreUri": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    }
}
```
下面说明如何构造上述模板并按资源类型划分该模板。

### <a name="pipelines"></a>管道
    
* 路径 `activities/typeProperties/waitTimeInSeconds` 中的任何属性已参数化。 包含名为 `waitTimeInSeconds` 的代码级属性的管道中的任一活动（例如 `Wait` 活动）将以默认名称参数化为数字。 但是，该活动在资源管理器模板中没有默认值。 在部署资源管理器期间，必须输入其默认值。
* 同样，名为 `headers` 的属性（例如，在 `Web` 活动中的该属性）将以类型 `object` (JObject) 参数化。 该属性具有默认值，该值与源工厂的默认值相同。

### <a name="integrationruntimes"></a>IntegrationRuntimes

* 路径 `typeProperties` 下的所有属性以其各自的默认值参数化。 例如，`IntegrationRuntimes` 类型属性下有两个属性：`computeProperties` 和 `ssisProperties`。 这两个属性类型是使用各自的默认值和类型 (Object) 创建的。

### <a name="triggers"></a>触发器

* 在 `typeProperties` 下，会参数化两个属性。 第一个属性是 `maxConcurrency`，该属性指定为具有默认值，类型为 `string`。 其默认参数名称为 `<entityName>_properties_typeProperties_maxConcurrency`。
* 另外还会参数化 `recurrence` 属性。 该属性级别下的所有属性均指定为参数化为字符串，并具有默认值和参数名称。 `interval` 属性例外，它将参数化为类型 `int`。 参数名称带有 `<entityName>_properties_typeProperties_recurrence_triggerSuffix` 后缀。 同样，`freq` 属性是字符串，将参数化为字符串。 但是，将参数化 `freq` 属性且不提供默认值。 名称将会简写并带有后缀。 例如，`<entityName>_freq` 。

### <a name="linkedservices"></a>LinkedServices

* 链接服务是独特的。 由于链接服务和数据集的类型多种多样，你可以提供类型特定的自定义。 在此示例中，对于 `AzureDataLakeStore` 类型的所有链接服务，将应用特定的模板。 对于所有其他链接服务（通过 `*` 获取），将应用不同的模板。
* `connectionString` 属性将参数化为 `securestring` 值。 该属性没有默认值。 它使用带有 `connectionString` 后缀的简写参数名称。
* 属性 `secretAccessKey` 正好是 `AzureKeyVaultSecret`（例如，在 Amazon S3 链接服务中就是如此）。 它自动参数化为 Azure Key Vault 机密，可从配置的密钥保管库提取。 你还可以参数化密钥保管库本身。

### <a name="datasets"></a>数据集

* 尽管类型特定的自定义可用于数据集，但你无需显式使用 \* 级配置即可提供配置。 在前面的示例中，`typeProperties` 下的所有数据集属性都将参数化。

> [!NOTE]
> 如果为管道配置了 Azure 警报和矩阵，则当前不支持将其用作 ARM 部署的参数。 若要在新环境中重新应用警报和矩阵，请参照[数据工厂监视、警报和矩阵。](./monitor-metrics-alerts.md)
> 

## <a name="default-parameterization-template"></a>默认参数化模板

下面是当前的默认参数化模板。 如果只需添加少量的参数，直接编辑此模板也许是不错的想法，因为这样不会丢失现有的参数化结构。

```json
{
    "Microsoft.DataFactory/factories": {
        "properties": {
            "globalParameters": {
                "*": {
                    "value": "="
                }
            }
        },
        "location": "="
    },
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                },
                "computeProperties": {
                    "dataFlowProperties": {
                        "externalComputeInfo": [{
                                "accessToken": "-::secureString"
                            }
                        ]
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "host": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "poolName": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "functionAppUrl":"=",
                    "environmentUrl": "=",
                    "aadResourceId": "=",
                    "sasUri": "|:-sasUri:secureString",
                    "sasToken": "|",
                    "connectionString": "|:-connectionString:secureString",
                    "hostKeyFingerprint": "="
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/managedVirtualNetworks/managedPrivateEndpoints": {
        "properties": {
            "*": "="
        }
    }
}
```

## <a name="example-parameterizing-an-existing-azure-databricks-interactive-cluster-id"></a>示例：参数化现有的 Azure Databricks 交互式群集 ID

以下示例演示如何将单个值添加到默认的参数化模板。 我们只想要将 Databricks 链接服务的某个现有 Azure Databricks 交互式群集 ID 添加到参数文件。 请注意，此文件除了在 `Microsoft.DataFactory/factories/linkedServices` 的属性字段下添加了 `existingClusterId` 以外，在其他方面，它与前一个文件相同。

```json
{
    "Microsoft.DataFactory/factories": {
        "properties": {
            "globalParameters": {
                "*": {
                    "value": "="
                }
            }
        },
        "location": "="
    },
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }
 
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "poolName": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "existingClusterId": "-"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```

## <a name="next-steps"></a>后续步骤

- [持续集成和交付概述](continuous-integration-delivery.md)
- [使用 Azure Pipelines 发布自动进行持续集成](continuous-integration-delivery-automate-azure-pipelines.md)
- [手动将资源管理器模板推广到每个环境](continuous-integration-delivery-manual-promotion.md)
- [链接的资源管理器模板](continuous-integration-delivery-linked-templates.md)
- [使用修补程序生产环境](continuous-integration-delivery-hotfix-environment.md)
- [部署前和部署后示例脚本](continuous-integration-delivery-sample-script.md)