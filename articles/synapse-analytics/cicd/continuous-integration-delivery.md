---
title: Azure Synapse Analytics 中的持续集成和持续交付
description: 了解如何使用持续集成和持续交付 (CI/CD) 在 Azure Synapse Analytics 工作区中的环境之间部署更改。
author: liudan66
ms.service: synapse-analytics
ms.subservice: cicd
ms.topic: conceptual
ms.date: 10/08/2021
ms.author: liud
ms.reviewer: pimorano
ms.openlocfilehash: 3269bfed98264574d5b44759cceae6e1613eb5f8
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2021
ms.locfileid: "129729205"
---
# <a name="continuous-integration-and-delivery-for-an-azure-synapse-analytics-workspace"></a>Azure Synapse Analytics 工作区的持续集成和持续交付

持续集成 (CI) 是每次团队成员将更改提交到版本控制时自动生成和测试代码的过程。 持续交付 (CD) 指从多个测试或过渡环境到生产环境的生成、测试、配置和部署过程。

在 Azure Synapse Analytics 工作区中，CI/CD 将所有实体从一个环境（开发、测试、生产）移到另一个环境。 将你的工作区提升到另一个工作区的过程包括两个步骤。 首先，使用 [Azure 资源管理器模板（ARM 模板）](../../azure-resource-manager/templates/overview.md)创建或更新工作区资源（池和工作区）。 然后，在 Azure DevOps 或 GitHub 中使用 Azure Synapse CI/CD 工具来迁移 SQL 脚本、笔记本、Spark 作业定义、管道、数据集和数据流等项目。 

本文概述了如何使用 Azure DevOps 发布管道和 GitHub 操作自动将 Azure Synapse 工作区部署到多个环境。

## <a name="prerequisites"></a>先决条件

若要自动将 Azure Synapse 工作区部署到多个环境，必须准备好以下先决条件和配置。

### <a name="azure-devops"></a>Azure DevOps

- 准备好一个用于运行发布管道的 Azure DevOps 项目。
- [为将签入代码的用户授予组织级别的“基本”访问权限](/azure/devops/organizations/accounts/add-organization-users?view=azure-devops&tabs=preview-page&preserve-view=true)，以便他们能够查看存储库。
- 授予对 Azure Synapse 存储库的“所有者”权限。
- 确保已创建自承载 Azure DevOps VM 代理，或使用 Azure DevOps 托管代理。
- 授予[为资源组创建 Azure 资源管理器服务连接](/azure/devops/pipelines/library/service-endpoints?view=azure-devops&tabs=yaml&preserve-view=true)的权限。
- Azure Active Director (Azure AD) 管理员必须[在 Azure DevOps 组织中安装 Azure DevOps Synapse 工作区部署代理扩展](/azure/devops/marketplace/install-extension)。
- 创建或指定现有服务帐户，以便管道以该服务帐户的身份运行。 你可以使用个人访问令牌，而不是服务帐户，但在删除用户帐户后，你的管道将不起作用。

### <a name="github"></a>GitHub

- 创建包含 Azure Synapse 工作区项目和工作区模板的 GitHub 存储库。 
- 确保已创建自承载运行器，或者使用 GitHub 承载的运行器。

### <a name="azure-active-directory"></a>Azure Active Directory

- 如果使用服务主体，请在 Azure AD 中创建用于部署的服务主体。 
- 如果使用托管标识，请在 Azure 中的 VM 上启用系统分配的托管标识作为代理或运行器，然后将其作为 Synapse 管理员添加到 Azure Synapse Studio。
- 请使用 Azure AD 管理员角色来完成这些操作。

### <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

> [!NOTE]
> 可以通过使用相同的管道、ARM 模板或 Azure CLI 来自动化和部署这些必备组件，但本文不介绍这些过程。

- 必须在 Azure Synapse Studio 中为用于开发的“源”工作区配置 Git 存储库。 有关详细信息，请参阅 [Azure Synapse Studio 中的源代码管理](source-control.md#configuration-method-2-manage-hub)。

- 设置要部署到的空白工作区，以便：

  1. 创建新的 Azure Synapse 工作区。
  1. 向 VM 代理和服务主体授予对承载新工作区的资源组的“参与者”权限。
  1. 在该工作区中，请不要配置 Git 存储库连接。
  1. 在 Azure 门户中找到新的 Azure Synapse 工作区，然后向你自己和要运行 Azure DevOps 管道的用户授予对 Azure Synapse 工作区的“所有者”权限。 
  1. 将 Azure DevOps VM 代理和服务主体添加到该工作区的“参与者”角色。 （应已继承该角色，但请验证是否确实如此。）
  1. 在 Azure Synapse 工作区中，转到“Studio” > “管理” > “访问控制”  。 将 Azure DevOps VM 代理和服务主体添加到工作区管理员组。
  1. 打开用于工作区的存储帐户。 在“标识和访问管理”窗格中，将 VM 代理和服务主体添加到“存储 Blob 数据参与者”角色。
  1. 在支持订阅中创建密钥保管库，并确保现有工作区和新工作区都至少具有对保管库的 GET 和 LIST 权限。
  1. 为了使自动部署正常工作，请确保在链接服务中指定的任何连接字符串都位于密钥保管库中。

### <a name="other-prerequisites"></a>其他先决条件
 
- 未在工作区部署任务中创建 Spark 池和自承载集成运行时。 如果有使用自承载集成运行时的链接服务，请在新的工作区中手动创建该运行时。
- 如果开发工作区中的项与特定池连接，请确保在参数文件中为目标工作区中的池创建或参数化相同的名称。  
- 如果在尝试部署时预配的 SQL 池已暂停，则部署可能会失败。

有关详细信息，请参阅 [Azure Synapse Analytics 中的 CI CD 第 4 部分 - 发布管道](https://techcommunity.microsoft.com/t5/data-architecture-blog/ci-cd-in-azure-synapse-analytics-part-4-the-release-pipeline/ba-p/2034434)。 


## <a name="set-up-a-release-pipeline-in-azure-devops"></a>在 Azure DevOps 中设置发布管道

本部分介绍如何在 Azure DevOps 中部署 Azure Synapse 工作区。 

1. 在 [Azure DevOps](https://dev.azure.com/) 中，打开你创建的用于发布的项目。

1. 在左侧菜单中，选择“管道” > “发布” 。

    :::image type="content" source="media/create-release-pipeline.png" alt-text="显示在 Azure DevOps 菜单中依次选择“管道”和“发布”的屏幕截图。":::        
 
1. 选择“新建管道”。 如果你有现有的管道，请选择“新建” > “新建发布管道” 。

1. 选择“空作业”模板。

    :::image type="content" source="media/create-release-select-empty.png" alt-text="显示选择“空作业”模板的屏幕截图。":::

1. 在“阶段名称”中，输入你的环境名称。

1. 选择“添加项目”，然后选择在开发环境中使用 Azure Synapse Studio 配置的 Git 存储库。 选择要在其中管理池和工作区 ARM 模板的 Git 存储库。 如果使用 GitHub 作为源，请为 GitHub 帐户和拉取存储库创建服务连接。 有关详细信息，请参阅[服务连接](/azure/devops/pipelines/library/service-endpoints)。

    :::image type="content" source="media/release-creation-github.png" alt-text="显示选择 GitHub 以便为新项目添加发布分支的屏幕截图。":::

1. 选择资源 ARM 模板分支。 对于“默认版本”，请选择“默认分支中的最新版本”。 

    :::image type="content" source="media/release-creation-arm-template-branch.png" lightbox="media/release-creation-arm-template-branch.png" alt-text="显示设置资源 ARM 模板分支的屏幕截图。":::

1. 对于“默认分支”项目，请选择存储库[发布分支](source-control.md#configure-publishing-settings)。 默认情况下，发布分支为 `workspace_publish`。 对于“默认版本”，请选择“默认分支中的最新版本”。 

    :::image type="content" source="media/release-creation-publish-branch.png" alt-text="显示设置项目分支的屏幕截图。":::

### <a name="set-up-a-stage-task-for-an-arm-template-to-create-and-update-a-resource"></a>为 ARM 模板设置阶段任务，以创建和更新资源 

如果你有用于部署资源（例如 Azure Synapse 工作区、Spark 池、SQL 池或密钥保管库）的 ARM 模板，请添加 Azure 资源管理器部署任务来创建或更新这些资源：

1. 在阶段视图中选择“查看阶段任务”。

    :::image type="content" source="media/release-creation-stage-view.png" alt-text="显示设置阶段视图的屏幕截图。":::

1. 创建新任务。 搜索“ARM 模板部署”，然后选择“添加”。

1. 在部署“任务”选项卡上，选择工作区的订阅、资源组和位置。 根据需要提供凭据。

1. 对于“操作”，请选择“创建或更新资源组” 。

1. 对于“模板”，请选择省略号按钮 (...) 。 转到工作区的 ARM 模板。

1. 对于“模板参数”，请选择“…”  以选择参数文件。

    :::image type="content" source="media/pools-resource-deploy.png" lightbox="media/pools-resource-deploy.png" alt-text="显示工作区和池部署的屏幕截图。":::

1. 对于“替代模板参数”，请选择“...”，然后输入要用于工作区的参数值 。 

1. 对于“部署模式”，请选择“增量” 。

1. （可选）添加“Azure PowerShell”以便授予和更新工作区角色分配。 如果使用发布管道创建 Azure Synapse 工作区，则会将管道的服务主体添加为默认工作区管理员。可以运行 PowerShell 来向其他帐户授予对工作区的访问权限。 

    :::image type="content" source="media/release-creation-grant-permission.png" lightbox="media/release-creation-grant-permission.png" alt-text="演示如何运行 PowerShell 脚本来授予权限的屏幕截图。":::

> [!WARNING]
> 在完整部署模式下，将会删除资源组中的、尚未在新 ARM 模板中指定的资源。 有关详细信息，请参阅 [Azure 资源管理部署模式](../../azure-resource-manager/templates/deployment-modes.md)。

### <a name="set-up-a-stage-task-for-azure-synapse-artifacts-deployment"></a>为 Azure Synapse 项目部署设置阶段任务 

使用 [Synapse 工作区部署](https://marketplace.visualstudio.com/items?itemName=AzureSynapseWorkspace.synapsecicd-deploy)扩展在 Azure Synapse 工作区中部署其他项。 可部署的项包括数据集、SQL 脚本、笔记本、Spark 作业定义、数据流、管道、链接服务、凭据和集成运行时。  

1. 从 [Visual Studio Marketplace](https://marketplace.visualstudio.com/azuredevops) 搜索并获取该扩展。

    :::image type="content" source="media/get-extension-marketplace.png" alt-text="显示 Visual Studio Marketplace 中的 Synapse 工作区部署扩展的屏幕截图。":::

1. 选择要在其中安装该扩展的 Azure DevOps 组织。 

    :::image type="content" source="media/install-extension.png" alt-text="显示选择要在其中安装 Synapse 工作区部署扩展的组织的屏幕截图。":::

1. 确保已向 Azure DevOps 管道的服务主体授予“订阅”权限，并已将该服务主体分配为工作区的 Synapse 工作区管理员。 

1. 若要创建新任务，请搜索“Synapse 工作区部署”，然后选择“添加” 。

    :::image type="content" source="media/add-extension-task.png" alt-text="显示搜索“Synapse 工作区部署”以创建任务的屏幕截图。":::

1. 在任务中的“模板”旁边，选择“...”  以选择模板文件。

1. 在“模板参数”旁边，选择“…”   以选择参数文件。

1. 选择连接、资源组，以及工作区的名称。 

1. 在“替代模板参数”旁边，选择“…”  . 输入要用于工作区的参数值，包括在链接服务中使用的连接字符串和帐户密钥。 有关详细信息，请参阅 [Azure Synapse Analytics 中的 CI/CD](https://techcommunity.microsoft.com/t5/data-architecture-blog/ci-cd-in-azure-synapse-analytics-part-4-the-release-pipeline/ba-p/2034434)。

    :::image type="content" source="media/create-release-artifacts-deployment.png" lightbox="media/create-release-artifacts-deployment.png" alt-text="显示为工作区设置 Synapse 部署任务的屏幕截图。":::

> [!IMPORTANT]
> 在 CI/CD 方案中，不同环境中的集成运行时类型必须相同。 例如，如果你在开发环境中有一个自承载集成运行时，则在其他环境（例如测试和生产）中也必须自承载该集成运行时。 同样，如果你跨多个阶段共享集成运行时，则必须在所有环境（例如开发、测试和生产）中链接并自承载这些集成运行时。

### <a name="create-a-release-for-deployment"></a>创建发布进行部署 

保存所有更改后，可以选择“创建发布”以手动创建发布。 若要了解如何自动创建发布，请参阅 [Azure DevOps 发布触发器](/azure/devops/pipelines/release/triggers)。

:::image type="content" source="media/release-creation-manually.png" lightbox="media/release-creation-manually.png" alt-text="显示“新建发布管道”窗格的屏幕截图，其中突出显示了“创建发布”。":::

## <a name="set-up-a-release-in-github-actions"></a>在 GitHub Actions 中设置发布 

本部分介绍如何使用 GitHub Actions 为 Azure Synapse 工作区部署创建 GitHub工作流。

可以使用 [Azure 资源管理器模板的 GitHub 操作](https://github.com/marketplace/actions/deploy-azure-resource-manager-arm-template)将用于工作区和计算池的 ARM 模板自动部署到 Azure。

### <a name="workflow-file"></a>工作流文件

在存储库的 /.github/workflows/ 路径下的 YAML (.yml) 文件中定义 GitHub Actions 工作流。 定义包含构成工作流的各个步骤和参数。

.yml 文件包含两个部分：

|部分  |任务  |
|---------|---------|
|**身份验证** | 1.定义服务主体。 <br /> 2.创建 GitHub 机密。 |
|**部署** | 部署工作区项目。 |

### <a name="configure-github-actions-secrets"></a>配置 GitHub Actions 机密

GitHub Actions 机密是已加密的环境变量。 对此存储库拥有“协作者”权限的任何人都可以使用这些机密来与该存储库中的操作交互。

1. 在 GitHub 存储库中选择“设置”选项卡，然后选择“机密” > “新建存储库机密”  。

    :::image type="content" source="media/create-secret-new.png" lightbox="media/create-secret-new.png" alt-text="显示为了创建新存储库机密而要选择的 GitHub 元素的屏幕截图。":::

1. 如果使用服务主体进行部署，请为客户端 ID 添加新机密，并添加新的客户端机密。 还可以选择保存订阅 ID 和租户 ID 作为机密。 

### <a name="add-your-workflow"></a>添加工作流

在 GitHub 存储库中，转到“操作”。 

1. 选择“自己设置工作流”。
1. 在工作流文件中，删除 `on:` 部分后面的所有内容。 例如，剩下的工作流可能如以下示例所示： 

    ```yaml
    name: CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]
    ```

1. 重命名工作流。 在“Marketplace”选项卡上，搜索 Synapse 工作区部署操作，然后添加该操作。 

    :::image type="content" source="media/search-action.png" lightbox="media/search-action.png" alt-text="显示在“Marketplace”选项卡上搜索 Synapse 工作区部署任务的屏幕截图。":::

1. 设置所需的值和工作区模板：

    ```yaml
    name: workspace deployment

    on:
        push:
            branches: [ publish_branch ]
    jobs:
        release:
            # You also can use the self-hosted runners.
            runs-on: windows-latest
            steps:
            # Checks out your repository under $GITHUB_WORKSPACE, so your job can access it.
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

1. 现在可以提交更改。 选择“开始提交”，输入标题，并添加说明（可选）。 然后选择“提交新文件”。

    :::image type="content" source="media/commit-workflow.png" lightbox="media/commit-workflow.png" alt-text="显示在 GitHub 中提交工作流的屏幕截图。":::

   该文件将显示在存储库的 github/workflows 文件夹中。

   > [!NOTE]
   > 只有 Azure 中的自承载 VM 才支持托管标识。 请务必将运行器设置为“自承载”。 为 VM 启用系统分配的托管标识，并将其作为 Synapse 管理员添加到 Azure Synapse Studio。

### <a name="review-your-deployment"></a>查看部署

1. 在 GitHub 存储库中，转到“操作”。
1. 若要查看工作流的详细运行日志，请打开第一条结果：

    :::image type="content" source="media/review-deploy-status.png" lightbox="media/review-deploy-status.png" alt-text="显示在 GitHub 的存储库“操作”中选择工作区部署日志的屏幕截图。":::

## <a name="create-custom-parameters-in-the-workspace-template"></a>在工作区模板中创建自定义参数 

如果你使用自动化 CI/CD，并想要在部署期间更改某些属性，但这些属性在默认情况下并未参数化，则你可以替代默认参数模板。

若要替代默认参数模板，请在 Git 协作分支的根文件夹中创建名为 template-parameters-definition.json 的自定义参数模板。 必须使用与此完全相同的文件名。 当 Azure Synapse 工作区从协作分支发布时，将会读取此文件，并使用其配置来生成参数。 如果 Azure Synapse 工作区找不到该文件，则会使用默认参数模板。

### <a name="custom-parameter-syntax"></a>自定义参数语法

可以使用以下准则来创建自定义参数文件：

* 输入相关实体类型下的属性路径。
* 将属性名称设置为 `*` 表示你要参数化该属性下的所有属性（只参数化到第一个级别，而不是递归性参数化）。 可为此配置设置例外情况。
* 将属性值设置为字符串表示你希望参数化该属性。 使用格式 `<action>:<name>:<stype>`。
   *  `<action>` 可以是以下字符之一：
      * `=` 表示将当前值保留为参数的默认值。
      * `-` 表示不保留参数的默认值。
      * `|` 是 Azure Key Vault 中的机密的特例，用于连接字符串或密钥。
   * `<name>` 是参数的名称。 如果为空，将采用属性的名称。 如果值以 `-` 字符开头，则会简写名称。 例如，`AzureStorage1_properties_typeProperties_connectionString` 将简写为 `AzureStorage1_connectionString`。
   * `<stype>` 是参数的类型。 如果 `<stype>` 为空，则默认类型为 `string`。 支持的值：`string`、`securestring`、`int`、`bool`、`object`、`secureobject` 和 `array`。
* 在该文件中指定数组，则表示模板中的匹配属性是数组。 Azure Synapse 会通过使用指定的定义来循环访问该数组中的所有对象。 第二个对象（一个字符串）成为属性的名称，这用作每次遍历的参数的名称。
* 定义不能特定于资源实例。 任何定义都将应用到该类型的所有资源。
* 默认情况下，会参数化密钥保管库机密等安全字符串，以及连接字符串、密钥和令牌等安全字符串。

### <a name="parameter-template-definition-example"></a>参数模板定义示例 

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

下面说明如何按资源类型构造上述模板。

**`notebooks`**

- `properties/bigDataPool/referenceName` 路径中的任何属性均使用其默认值进行参数化。 可为每个笔记本文件参数化附加的 Spark 池。 

**`sqlscripts`**

- 在 `properties/content/currentConnection` 路径中，`poolName` 和 `databaseName` 属性参数化为在模板中没有默认值的字符串。 

**`pipelines`**

- 将参数化 `activities/typeProperties/waitTimeInSeconds` 路径中的任何属性。 包含名为 `waitTimeInSeconds` 的代码级属性的管道中的任一活动（例如 `Wait` 活动）将以默认名称参数化为数字。 该属性在资源管理器模板中没有默认值。 在资源管理器部署期间，需要为该属性提供输入。
- `headers` 属性（例如在 `Web` 活动中）将以 `object`（对象）类型参数化。 `headers` 属性具有默认值，该值与源工厂的默认值相同。

**`integrationRuntimes`**

- `typeProperties` 路径中的所有属性以其各自的默认值参数化。 例如，`IntegrationRuntimes` 类型属性下有两个属性：`computeProperties` 和 `ssisProperties`。 这两个属性类型是使用各自的默认值和类型 (Object) 创建的。

**`triggers`**

- `typeProperties` 下的两个属性将参数化：
  - `maxConcurrency` 属性具有默认值，其类型为 `string`。 `maxConcurrency` 属性的默认参数名称为 `<entityName>_properties_typeProperties_maxConcurrency`。
  - 另外还会参数化 `recurrence` 属性。 `recurrence` 属性下的所有属性均设置为参数化为字符串，并具有默认值和参数名称。 `interval` 属性例外，它将参数化为 `int` 类型。 参数名称带有 `<entityName>_properties_typeProperties_recurrence_triggerSuffix` 后缀。 同样，`freq` 属性是字符串，将参数化为字符串。 但是，将参数化 `freq` 属性且不提供默认值。 名称将会简写并带有后缀，例如 `<entityName>_freq`。

**`linkedServices`**

- 链接服务是独特的。 由于链接服务和数据集的类型多种多样，你可以提供类型特定的自定义。 在上面的示例中，对于 `AzureDataLakeStore` 类型的所有链接服务，将应用特定的模板。 对于所有其他链接服务（可使用 `*` 字符来识别），将应用不同的模板。
- `connectionString` 属性将参数化为 `securestring` 值。 该属性没有默认值。 参数名称将会简写并带有 `connectionString` 后缀。
- `secretAccessKey` 属性将参数化为 `AzureKeyVaultSecret` 值（例如，在 Amazon S3 链接服务中）。 该属性自动参数化为 Azure 密钥保管库机密，可从配置的密钥保管库提取。 还可以参数化密钥保管库本身。

**`datasets`**

- 尽管可以自定义数据集中的类型，但不需要进行显式的 \* 级配置。 在前面的示例中，`typeProperties` 下的所有数据集属性都将参数化。

## <a name="best-practices-for-cicd"></a>CI/CD 最佳做法

如果使用 Azure Synapse 工作区的 Git 集成，并且某个 CI/CD 管道会将更改从开发环境依次转移到测试和生产环境，则我们建议采用以下最佳做法：

-   仅将开发工作区与 Git 集成。 如果你使用 Git 集成，请仅将开发 Azure Synapse 工作区与 Git 集成。 对测试和生产工作区所做的更改将通过 CI/CD 进行部署，不需要 Git 集成。
-   在迁移项目之前准备好池。 如果已将 SQL 脚本或笔记本附加到开发工作区中的池，请为不同环境中的池使用相同的名称。 
-   在基础结构即代码方案中同步版本控制。 若要在描述性模型中管理基础结构（网络、虚拟机、负载均衡器和连接拓扑），请使用 DevOps 团队对源代码所用的相同版本控制。 
-   查看Azure 数据工厂最佳做法。 如果使用数据工厂，请参阅[数据工厂项目的最佳做法](../../data-factory/continuous-integration-deployment.md#best-practices-for-cicd)。

## <a name="troubleshoot-artifacts-deployment"></a>排查项目部署问题 

### <a name="use-the-synapse-workspace-deployment-task"></a>使用 Synapse 工作区部署任务

在 Azure Synapse 中，某些项目不是资源管理器资源，这与数据工厂中的情况不同。 不能使用 ARM 模板部署任务来部署 Azure Synapse 项目， 而应该改用 Synapse 工作区部署任务。
 
### <a name="unexpected-token-error-in-release"></a>发布中出现意外的标记错误

如果参数文件包含未转义的参数值，则发布管道将无法分析该文件，并会生成 `unexpected token` 错误。 建议替代参数，或使用密钥保管库来检索参数值。 也可以使用双转义字符来解决该问题。
