---
title: Synapse 工作区的持续集成和交付
description: 了解如何使用持续集成和交付将工作区中的更改从一个环境（开发、测试、生产）部署到另一个环境。
author: liudan66
ms.service: synapse-analytics
ms.subservice: cicd
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: liud
ms.reviewer: pimorano
ms.openlocfilehash: a590a2a0470710a74a6f1441a1f1859f974c2f97
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123259388"
---
# <a name="continuous-integration-and-delivery-for-azure-synapse-workspace"></a>Azure Synapse 工作区的持续集成和交付

## <a name="overview"></a>概述

持续集成是每次团队成员将更改提交到版本控制时自动生成和测试代码的过程。 持续部署 (CD) 指从多个测试或过渡环境到生产环境的生成、测试、配置和部署过程。

在 Azure Synapse Analytics 工作区中，持续集成和交付 (CI/CD) 将所有实体从一个环境（开发、测试、生产）移到另一个环境。 若要将你的工作区提升为另一个工作区，相关过程包括两个部分。 首先，使用 [Azure 资源管理器模板（ARM 模板）](../../azure-resource-manager/templates/overview.md)创建或更新工作区资源（池和工作区）。 然后，在 Azure DevOps 或 GitHub 中通过 Azure Synapse Analytics CI/CD 工具迁移项目（SQL 脚本、笔记本、Spark 作业定义、管道、数据集、数据流等等）。 

本文概述了如何使用 Azure DevOps 发布管道和 GitHub 操作自动将 Azure Synapse 工作区部署到多个环境。

## <a name="prerequisites"></a>先决条件

必须准备好这些先决条件和配置才可自动将 Azure Synapse 工作区部署到多个环境。

### <a name="azure-devops"></a>Azure DevOps

- Azure DevOps 项目已准备好运行发布管道。
- [为将签入代码的用户授予组织级别的“基本”访问权限](/azure/devops/organizations/accounts/add-organization-users?view=azure-devops&tabs=preview-page&preserve-view=true)，以便他们能够查看存储库。
- 授予针对 Azure Synapse 存储库的所有者权限。
- 请确保已创建自承载 Azure DevOps VM 代理，或使用 Azure DevOps 托管的代理。
- [为资源组创建 Azure 资源管理器服务连接](/azure/devops/pipelines/library/service-endpoints?view=azure-devops&tabs=yaml&preserve-view=true)的权限。
- Azure Active Director (Azure AD) 管理员必须[在 Azure DevOps 组织中安装 Azure DevOps Synapse 工作区部署代理扩展](/azure/devops/marketplace/install-extension)。
- 创建或指定现有服务帐户，以便管道以该服务帐户的身份运行。 你可以使用个人访问令牌，而不是服务帐户，但在删除用户帐户后，你的管道将不起作用。

### <a name="github"></a>GitHub

- 具有 synapse 工作区项目和工作区模板的 GitHub 存储库 
- 请确保已创建自承载运行器，或者使用 GitHub 承载的运行器。

### <a name="azure-active-directory"></a>Azure Active Directory

- 在 Azure AD 中，如果要使用服务主体，请创建用于部署的服务主体。 
- 要使用托管标识，需要在 Azure 中的 VM 上启用系统分配的托管标识作为代理或运行器，并将其作为 Synapse 管理员添加到 Synapse Studio。
- 此操作需要 Azure AD 管理员权限。

### <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

> [!NOTE]
> 可以通过使用相同的管道、ARM 模板或 Azure CLI 来自动化和部署这些先决条件，但本文不介绍相关过程。

- 必须在 Synapse Studio 中为用于开发的“源”工作区配置 Git 存储库。 有关详细信息，请参阅 [Synapse Studio 中的源代码管理](source-control.md#configuration-method-2-manage-hub)。

- 要部署到的空白工作区。 设置空白工作区：

  1. 创建新的 Azure Synapse Analytics 工作区。
  1. 向 VM 代理和服务主体参与者授予针对托管新工作区的资源组的权限。
  1. 在目标工作区中，请勿配置 Git 存储库连接。
  1. 在 Azure 门户中，找到新的 Azure Synapse Analytics 工作区，并向自己和要运行 Azure DevOps 管道的任何人授予 Azure Synapse Analytics 工作区所有者权限。 
  1. 将 Azure DevOps VM 代理和服务主体添加到工作区的参与者角色（应该已继承该角色，但请验证是否已继承）。
  1. 在 Azure Synapse Analytics 工作区中，转到“Studio” > “管理” > “访问控制”  。 将 Azure DevOps VM 代理和服务主体添加到工作区管理员组。
  1. 打开用于工作区的存储帐户。 在 IAM 中，将 VM 代理和服务主体添加到“选择存储 Blob 数据参与者”角色。
  1. 在支持订阅中创建密钥保管库，并确保现有工作区和新工作区都至少具有对保管库的 GET 和 LIST 权限。
  1. 为了使自动部署正常工作，请确保在链接服务中指定的任何连接字符串都位于密钥保管库中。

### <a name="additional-prerequisites"></a>其他先决条件
 
 - 未在工作区部署任务中创建 Spark 池和自承载集成运行时。 如果有使用自承载集成运行时的链接服务，请在新的工作区中手动创建该运行时。
 - 如果开发工作区中的项与特定池连接，请确保在目标工作区中创建同名的池或在参数文件中参数化池。  
 - 如果在尝试部署时预配的 SQL 池已暂停，则部署可能会失败。

有关详细信息，请参阅 [Azure Synapse Analytics 中的 CI CD 第 4 部分 - 发布管道](https://techcommunity.microsoft.com/t5/data-architecture-blog/ci-cd-in-azure-synapse-analytics-part-4-the-release-pipeline/ba-p/2034434)。 


## <a name="set-up-a-release-pipeline-in-azure-devops"></a>在 Azure DevOps 中设置发布管道

在本部分中，你将了解如何在 Azure DevOps 中部署 synapse。 

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

### <a name="set-up-a-stage-task-for-an-arm-template-to-create-and-update-resource"></a>为 ARM 模板设置阶段任务，以创建和更新资源 

如果你有用于部署资源（如 Azure Synapse 工作区、Spark 池、SQL 池或密钥保管库）的 ARM 模板，请添加 Azure 资源管理器部署任务来创建或更新这些资源：

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

### <a name="set-up-a-stage-task-for-synapse-artifacts-deployment"></a>为 Synapse 项目部署设置阶段任务 

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

1. 在“替代模板参数”框旁边 选择“…”，并输入目标工作区所需的参数值（包括在链接服务中使用的连接字符串和帐户密钥）。 有关详细信息，请参阅 [Azure Synapse Analytics 中的 CI/CD](https://techcommunity.microsoft.com/t5/data-architecture-blog/ci-cd-in-azure-synapse-analytics-part-4-the-release-pipeline/ba-p/2034434)。

    ![Synapse 工作区部署](media/create-release-artifacts-deployment.png)

> [!IMPORTANT]
> 在 CI/CD 方案中，不同环境中的集成运行时 (IR) 类型必须相同。 例如，如果在开发环境中使用自承载 IR，则同一 IR 在测试和生产等其他环境中的类型也必须是自承载。 同样，如果跨多个阶段共享集成运行时，则必须在所有环境（例如开发、测试和生产）中将集成运行时配置为链接自承载。

### <a name="create-release-for-deployment"></a>创建发布进行部署 

保存所有更改后，可以选择“创建发布”以手动创建发布。 若要自动创建发布，请参阅 [Azure DevOps 发布触发器](/azure/devops/pipelines/release/triggers)

   ![选择“创建发布”](media/release-creation-manually.png)

## <a name="set-up-a-release-with-github-action"></a>使用 GitHub 操作设置发布 

在此部分，你将了解如何使用 GitHub Actions 创建 GitHub工作流以进行 synapse 工作区部署。
可以使用[部署Azure 资源管理器模板操作](https://github.com/marketplace/actions/deploy-azure-resource-manager-arm-template)自动将 Azure 资源管理器模板（ARM 模板）部署到 Azure，用于工作区和计算池。

### <a name="workflow-file-overview"></a>工作流文件概述

GitHub Actions 工作流由存储库中 /.github/workflows/ 路径中的 YAML (.yml) 文件定义。 此定义包含组成工作流的各种步骤和参数。

此文件包含两个部分：

|部分  |任务  |
|---------|---------|
|**身份验证** | 1.定义服务主体。 <br /> 2.创建 GitHub 机密。 |
|**部署** | 1. 部署工作区项目。 |

### <a name="configure-the-github-secrets"></a>配置 GitHub 机密

机密是加密的环境变量。 具有此存储库的协作者访问权限的任何人都可以使用 Actions 的这些机密。

1. 导航到存储库，选择“设置”并导航到“机密”，然后单击“新建机密”。

    ![创建新机密](media/create-secret-new.png)

1. 如果使用服务主体进行部署，请为客户端 ID、客户端机密添加新机密。还可以选择将订阅 ID 和租户 ID 另存为机密。 

### <a name="add-your-workflow"></a>添加工作流

转到 GitHub 存储库的“Actions”。 

1. 选择“自己设置工作流”。 
1. 删除工作流文件 `on:` 部分后面的所有内容。 例如，剩下的工作流可能如下所示。 

    ```yaml
    name: CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]
    ```

1. 重命名工作流，在市场中搜索 Synapse 工作区部署操作，然后添加该操作。 

     ![搜索操作](media/search-the-action.png)

1. 指定所需的值和工作区模板。

    ```yaml
    name: workspace deployment

    on:
        push:
            branches: [ publish_branch ]
    jobs:
        release:
            # You can also use the self-hosted runners
            runs-on: windows-latest
            steps:
            # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
            - uses: actions/checkout@v2
            - uses: azure/synapse-workspace-deployment@release-1.0
            with:
              TargetWorkspaceName: 'target workspace name'
              TemplateFile: './path of the TemplateForWorkspace.json'
              ParametersFile: './path of the TemplateParametersForWorkspace.json'
              OverrideArmParameters: './path of the parameters.yaml'
              environment: 'Azure Public'
              resourceGroup: 'target workspace resource group'
              clientId: ${{secrets.CLIENTID}}
              clientSecret:  ${{secrets.CLIENTSECRET}}
              subscriptionId: 'subscriptionId of the target workspace'
              tenantId: 'tenantId'
              DeleteArtifactsNotInTemplate: 'true'
              managedIdentity: 'False'
    ``` 

1. 你现在可以提交更改了。 选择“开始提交”，键入标题，然后添加说明（可选）。 接下来，单击“提交新文件”。

    ![提交工作流](media/commit-the-workflow.png)    


1. 文件将显示在存储库的 `.github/workflows` 文件夹中。

> [!NOTE]
> 托管标识仅支持 Azure 上的自承载 VM。 请将运行器设置为自承载。 为 VM 启用了系统分配的托管标识，并将其添加到 Synapse Studio 作为 Synapse 管理员。

### <a name="review-your-deployment"></a>查看部署

1. 转到 GitHub 存储库的“Actions”。
1. 打开第一个结果，查看工作流运行的详细日志

    ![查看部署](media/review-deploy-status.png)    

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

如果使用 Azure Synapse 工作区的 Git 集成，并且某个 CI/CD 管道会将更改从开发环境依次转移到测试和生产环境，则我们建议采用以下最佳做法：

-   **Git 集成**。 仅配置具有 Git 集成的开发 Azure Synapse 工作区。 对测试和生产工作区所做的更改将通过 CI/CD 进行部署，不需要 Git 集成。
-   **在项目迁移之前准备池**。 如果已将 SQL 脚本或笔记本附加到开发工作区中的池，则需要在不同环境中使用相同的池名称。 
-   **基础结构即代码 (IaC)** 。 在描述性模型中管理基础结构（网络、虚拟机、负载均衡器和连接拓扑），使用与 DevOps 团队所用相同的版本控制来管理源代码。 
-   其他。 请参阅 [ADF 项目的最佳做法](../../data-factory/continuous-integration-deployment.md#best-practices-for-cicd)

## <a name="troubleshooting-artifacts-deployment"></a>排查项目部署问题 

### <a name="use-the-azure-synapse-analytics-workspace-deployment-task"></a>使用 Azure Synapse Analytics 工作区部署任务

在 Azure Synapse Analytics 中，有许多不是 ARM 资源的项目。 不同于 Azure 数据工厂。 ARM 模板部署任务无法正常工作来部署 Azure Synapse Analytics 项目。
 
### <a name="unexpected-token-error-in-release"></a>发布中出现意外的标记错误

如果参数文件包含未转义的参数值，则发布管道将无法分析文件，并生成错误“意外标记”。 建议替代参数或使用 Azure Key Vault 检索参数值。 还可以使用双转义符作为变通方法。
