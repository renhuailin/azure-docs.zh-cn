---
title: Synapse 工作区的持续集成和交付
description: 了解如何使用持续集成和交付将工作区中的更改从一个环境（开发、测试、生产）部署到另一个环境。
services: synapse-analytics
author: liud
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: liud
ms.reviewer: pimorano
ms.openlocfilehash: de3738573bb9bb6f045a45d290c74ba9e6902a5e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "103561951"
---
# <a name="continuous-integration-and-delivery-for-azure-synapse-workspace"></a>Azure Synapse 工作区的持续集成和交付

## <a name="overview"></a>概述

持续集成是每次团队成员将更改提交到版本控制时自动生成和测试代码的过程。 持续部署 (CD) 指从多个测试或过渡环境到生产环境的生成、测试、配置和部署过程。

对于 Azure Synapse 工作区，持续集成和交付 (CI/CD) 将所有实体从一个环境（开发、测试、生产）移到另一个环境。 若要将工作区提升到其他工作区，请完成两个部分：使用 [Azure 资源管理器模板](../../azure-resource-manager/templates/overview.md)创建或更新工作区资源（池和工作区）；在 Azure DevOps 中通过 Synapse CI/CD 工具迁移项目（SQL 脚本、笔记本、Spark 作业定义、管道、数据集、数据流等）。 

本文概述如何使用 Azure 发布管道将 Synapse 工作区自动部署到多个环境。

## <a name="prerequisites"></a>先决条件

-   用于开发的工作区已在 Studio 中使用 Git 存储库进行配置，请参阅 [Synapse Studio 中的源代码管理](source-control.md)。
-   Azure DevOps 项目已准备好运行发布管道。

## <a name="set-up-a-release-pipelines"></a>设置发布管道

1.  在 [Azure DevOps](https://dev.azure.com/)中，打开为发布创建的项目。

1.  在页面左侧选择“管道”，然后选择“发布”。 

    ![依次选择“管道”、“发布”](media/create-release-1.png)

1.  选择“新建管道”；如果有现有的管道，请依次选择“新建”、“新建发布管道”。  

1.  选择“空作业”模板。

    ![选择“空作业”](media/create-release-select-empty.png)

1.  在“阶段名称”框中，输入环境的名称。

1.  选择“添加项目”，然后选择配置了开发 Synapse Studio 的 git 存储库。 选择用于管理池和工作区的 ARM 模板的 git 存储库。 如果使用 GitHub 作为源，需要为 GitHub 帐户和拉取存储库创建服务连接。 了解有关[服务连接](/azure/devops/pipelines/library/service-endpoints)的信息 

    ![添加发布分支](media/release-creation-github.png)

1.  选择用于资源更新的 ARM 模板分支。 对于“默认版本”，请选择“默认分支中的最新版本”。 

    ![添加 ARM 模板](media/release-creation-arm-branch.png)

1.  选择“默认分支”对应的存储库的[发布分支](source-control.md#configure-publishing-settings)。 默认情况下，此发布分支为 `workspace_publish`。 对于“默认版本”，请选择“默认分支中的最新版本”。 

    ![添加项目](media/release-creation-publish-branch.png)

## <a name="set-up-a-stage-task-for-arm-resource-create-and-update"></a>为 ARM 资源创建和更新设置阶段任务 

添加 Azure 资源管理器部署任务来创建或更新资源，包括工作区和池：

1. 在阶段视图中选择“查看阶段任务”。

    ![阶段视图](media/release-creation-stage-view.png)

1. 创建新任务。 搜索“ARM 模板部署”，然后选择“添加”。 

1. 在“部署任务”中，选择目标工作区的订阅、资源组和位置。 根据需要提供凭据。

1. 在“操作”列表中，选择“创建或更新资源组”。 

1. 选择“模板”框旁边的省略号按钮 ( **...** )。 浏览目标工作区的 Azure 资源管理器模板

1. 选择“模板参数”框旁边 的“…”，以便选择参数文件。

1. 在“替代模板参数”框旁边 （位于“替代模板参数”框旁边），并输入目标工作区的所需参数值。 

1. 选择“增量”作为“部署模式”。 
    
    ![工作区和池部署](media/pools-resource-deploy.png)

1. 根据需要添加 Azure PowerShell 来授予和更新工作区角色分配。 如果使用发布管道创建 Synapse 工作区，则会将管道的服务主体添加为默认工作区管理员。可以运行 PowerShell 来向其他帐户授予对工作区的访问权限。 
    
    ![授予权限](media/release-creation-grant-permission.png)

 > [!WARNING]
> 在完整部署模式下，将删除资源组中已存在但尚未在新资源管理器模板中指定的资源。 有关详细信息，请参阅 [Azure 资源管理部署模式](../../azure-resource-manager/templates/deployment-modes.md)

## <a name="set-up-a-stage-task-for-artifacts-deployment"></a>为项目部署设置阶段任务 

使用 [Synapse 工作区部署](https://marketplace.visualstudio.com/items?itemName=AzureSynapseWorkspace.synapsecicd-deploy)扩展在 Synapse 工作区中部署其他项，例如数据集、SQL 脚本、笔记本、spark 作业定义、数据流、管道、链接服务、凭据和 IR（集成运行时）。  

1. 搜索并从 Azure DevOps 市场 (https://marketplace.visualstudio.com/azuredevops) 获取扩展 

     ![获取扩展](media/get-extension-from-market.png)

1. 选择要安装扩展的组织。 

     ![安装扩展](media/install-extension.png)

1. 请确保已向 Azure DevOps 管道的服务主体授予订阅的权限，并已将该主体分配为目标工作区的工作区管理员。 

1. 创建新任务。  搜索“Synapse 工作区部署”，然后选择“添加”。

     ![添加扩展](media/add-extension-task.png)

1.  在任务中选择“...” （位于“模板”框旁边）以选择模板文件。

1. 选择“模板参数”框旁边 的“…”，以便选择参数文件。

1. 选择连接、资源组和目标工作区的名称。 

1. 在“替代模板参数”框旁边 （位于“替代模板参数”框旁边），并输入目标工作区的所需参数值。 

    ![Synapse 工作区部署](media/create-release-artifacts-deployment.png)

> [!IMPORTANT]
> 在 CI/CD 方案中，不同环境中的集成运行时 (IR) 类型必须相同。 例如，如果在开发环境中使用自承载 IR，则同一 IR 在测试和生产等其他环境中的类型也必须是自承载。 同样，如果跨多个阶段共享集成运行时，则必须在所有环境（例如开发、测试和生产）中将集成运行时配置为链接自承载。

## <a name="create-release-for-deployment"></a>创建发布进行部署 

保存所有更改后，可以选择“创建发布”以手动创建发布。 若要自动创建发布，请参阅 [Azure DevOps 发布触发器](/azure/devops/pipelines/release/triggers)

   ![选择“创建发布”](media/release-creation-manually.png)

## <a name="use-custom-parameters-of-the-workspace-template"></a>使用工作区模板的自定义参数 

你使用自动化 CI/CD，想要在部署期间更改某些属性，但这些属性在默认情况下不会参数化。 在这种情况下，可以重写默认参数模板。

若要重写默认参数模板，需要创建自定义参数模板，即，在 git 协作分支的根文件夹中创建名为“template-parameters-definition.js”的文件。 必须使用这个具体的文件名。 在从协作分支发布时，Synapse 工作区会读取此文件，并使用其配置来生成参数。 如果找不到文件，则会使用默认参数模板。

### <a name="custom-parameter-syntax"></a>自定义参数语法

下面是创建自定义参数文件的一些准则：

* 输入相关实体类型下的属性路径。
* 将属性名称设置为 `*` 表示要将其下的所有属性参数化（仅参数化到第一个级别，而不是递归性的参数化）。 还可为此配置提供例外情况。
* 将属性值设置为字符串表示你希望参数化该属性。 使用格式 `<action>:<name>:<stype>`。
   *  `<action>` 可以是以下字符之一：
      * `=` 表示将当前值保留为参数的默认值。
      * `-` 表示不保留参数的默认值。
      * `|` 是 Azure Key Vault 中的机密的特例，用于连接字符串或密钥。
   * `<name>` 是参数的名称。 如果为空，将采用属性的名称。 如果值以 `-` 字符开头，则会简写名称。 例如，`AzureStorage1_properties_typeProperties_connectionString` 将简写为 `AzureStorage1_connectionString`。
   * `<stype>` 是参数的类型。 如果 `<stype>` 为空，则默认类型为 `string`。 支持的值：`string`、`securestring`、`int`、`bool`、`object`、`secureobject` 和 `array`。
* 在该文件中指定数组，则表示模板中的匹配属性是数组。 Synapse 会通过使用指定的定义来循环访问该数组中的所有对象。 第二个对象（一个字符串）成为属性的名称，这用作每次遍历的参数的名称。
* 定义不能特定于资源实例。 任何定义都将应用到该类型的所有资源。
* 默认情况下，会参数化 Key Vault 机密等安全字符串，以及连接字符串、密钥和令牌等安全字符串。

### <a name="parameter-template-definition-samples"></a>参数模板定义示例 

下面是参数模板定义的示例：

```json
{
"Microsoft.Synapse/workspaces/notebooks": {
        "properties":{
            "bigDataPool":{
                "referenceName": "="
            }
        }
    },
    "Microsoft.Synapse/workspaces/sqlscripts": {
     "properties": {
         "content":{
             "currentConnection":{
                    "*":"-"
                 }
            } 
        }
    },
    "Microsoft.Synapse/workspaces/pipelines": {
        "properties": {
            "activities": [{
                 "typeProperties": {
                    "waitTimeInSeconds": "-::int",
                    "headers": "=::object"
                }
            }]
        }
    },
    "Microsoft.Synapse/workspaces/integrationRuntimes": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.Synapse/workspaces/triggers": {
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
    "Microsoft.Synapse/workspaces/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                     "*": "="
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
    "Microsoft.Synapse/workspaces/datasets": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    }
}
```
下面说明如何构造上述模板并按资源类型划分该模板。

#### <a name="notebooks"></a>笔记本 

* 路径 `properties/bigDataPool/referenceName` 中的任何属性均使用其默认值进行参数化。 可以为每个笔记本文件参数化附加的 Spark 池。 

#### <a name="sql-scripts"></a>SQL 脚本 

* 模板中没有默认值，路径 `properties/content/currentConnection` 中的属性（poolName 和 databaseName）被参数化为字符串。 

#### <a name="pipelines"></a>管道

* 路径 `activities/typeProperties/waitTimeInSeconds` 中的任何属性已参数化。 包含名为 `waitTimeInSeconds` 的代码级属性的管道中的任一活动（例如 `Wait` 活动）将以默认名称参数化为数字。 但是，该活动在资源管理器模板中没有默认值。 在部署资源管理器期间，必须输入其默认值。
* 同样，名为“`headers`”的属性（例如，`Web` 活动中的该属性）会以 `object`（对象）类型参数化。 该属性具有默认值，该值与源工厂的默认值相同。

#### <a name="integrationruntimes"></a>IntegrationRuntimes

* 路径 `typeProperties` 下的所有属性以其各自的默认值参数化。 例如，`IntegrationRuntimes` 类型属性下有两个属性：`computeProperties` 和 `ssisProperties`。 这两个属性类型是使用各自的默认值和类型 (Object) 创建的。

#### <a name="triggers"></a>触发器

* 在 `typeProperties` 下，会参数化两个属性。 第一个属性是 `maxConcurrency`，该属性指定为具有默认值，类型为 `string`。 其默认参数名称为 `<entityName>_properties_typeProperties_maxConcurrency`。
* 另外还会参数化 `recurrence` 属性。 该属性级别下的所有属性均指定为参数化为字符串，并具有默认值和参数名称。 `interval` 属性例外，它将参数化为类型 `int`。 参数名称带有 `<entityName>_properties_typeProperties_recurrence_triggerSuffix` 后缀。 同样，`freq` 属性是字符串，将参数化为字符串。 但是，将参数化 `freq` 属性且不提供默认值。 名称将会简写并带有后缀。 例如，`<entityName>_freq` 。

#### <a name="linkedservices"></a>LinkedServices

* 链接服务是独特的。 由于链接服务和数据集的类型多种多样，你可以提供类型特定的自定义。 在此示例中，对于 `AzureDataLakeStore` 类型的所有链接服务，将应用特定的模板。 对于所有其他链接服务（通过 `*` 获取），将应用不同的模板。
* `connectionString` 属性将参数化为 `securestring` 值。 该属性没有默认值。 它使用带有 `connectionString` 后缀的简写参数名称。
* 属性 `secretAccessKey` 正好是 `AzureKeyVaultSecret`（例如，在 Amazon S3 链接服务中就是如此）。 它自动参数化为 Azure Key Vault 机密，可从配置的密钥保管库提取。 你还可以参数化密钥保管库本身。

#### <a name="datasets"></a>数据集

* 尽管类型特定的自定义可用于数据集，但你无需显式使用 \* 级配置即可提供配置。 在前面的示例中，`typeProperties` 下的所有数据集属性都将参数化。


## <a name="best-practices-for-cicd"></a>CI/CD 最佳做法

如果使用 Synapse 工作区的 Git 集成，并且某个 CI/CD 管道会将更改从开发环境依次转移到测试和生产环境，则我们建议采用以下最佳做法：

-   **Git 集成**。 仅配置具有 Git 集成的开发 Synapse 工作区。 对测试和生产工作区所做的更改将通过 CI/CD 进行部署，不需要 Git 集成。
-   **在项目迁移之前准备池**。 如果已将 SQL 脚本或笔记本附加到开发工作区中的池，则需要在不同环境中使用相同的池名称。 
-   **基础结构即代码 (IaC)** 。 在描述性模型中管理基础结构（网络、虚拟机、负载均衡器和连接拓扑），使用与 DevOps 团队所用相同的版本控制来管理源代码。 
-   其他。 请参阅 [ADF 项目的最佳做法](../../data-factory/continuous-integration-deployment.md#best-practices-for-cicd)

## <a name="troubleshooting-artifacts-deployment"></a>排查项目部署问题 

### <a name="use-the-synapse-workspace-deployment-task"></a>使用 Synapse 工作区部署任务

在 Synapse 中，有许多不是 ARM 资源的项目。 不同于 Azure 数据工厂。 ARM 模板部署任务无法正常工作来部署 Synapse 项目
 
### <a name="unexpected-token-error-in-release"></a>发布中出现意外的标记错误

如果参数文件包含未转义的参数值，则发布管道将无法分析文件，并生成错误“意外标记”。 建议替代参数或使用 Azure KeyVault 检索参数值。 还可以使用双转义符作为变通方法。
