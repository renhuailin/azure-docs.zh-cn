---
title: 部署逻辑应用模板
description: 了解如何为 Azure 逻辑应用部署已创建的 Azure 资源管理器模板
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 08/04/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8be8361b58e4b1b1fad3f41b29958a360e206890
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121733778"
---
# <a name="deploy-azure-resource-manager-templates-for-azure-logic-apps"></a>为 Azure 逻辑应用部署 Azure 资源管理器模板

创建用于逻辑应用的 Azure 资源管理器模板后，可按以下方式部署该模板：

* [Azure 门户](#portal)
* [Visual Studio](#visual-studio)
* [Azure PowerShell](#powershell)
* [Azure CLI](#cli)
* [Azure Resource Manager REST API](../azure-resource-manager/templates/deploy-rest.md)
* [Azure DevOps](#azure-pipelines)

<a name="portal"></a>

## <a name="deploy-through-azure-portal"></a>通过 Azure 门户进行部署

若要自动将逻辑应用模板部署到 Azure，可以选择下面的“部署到 Azure”按钮，这样可以登录到 Azure 门户，系统会提示你输入有关逻辑应用的信息。 然后，可对逻辑应用模板或参数进行任何必要的更改。

[![“部署到 Azure”](./media/logic-apps-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.logic%2Flogic-app-create%2Fazuredeploy.json)

例如，在登录到 Azure 门户后，系统会提示输入以下信息：

* Azure 订阅名称
* 要使用的资源组
* 逻辑应用位置
* 逻辑应用的名称
* 测试 URI
* 接受指定的条款和条件

有关详细信息，请参阅以下主题：

* [概述：使用 Azure 资源管理器模板自动完成逻辑应用的部署](logic-apps-azure-resource-manager-templates-overview.md)
* [使用 Azure 资源管理器模板和 Azure 门户部署资源](../azure-resource-manager/templates/deploy-portal.md)

<a name="visual-studio"></a>

## <a name="deploy-with-visual-studio"></a>使用 Visual Studio 进行部署

若要从使用 Visual Studio 创建的 Azure 资源组项目部署逻辑应用模板，请按[这些步骤](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure)操作，将逻辑应用手动部署到 Azure。

<a name="powershell"></a>

## <a name="deploy-with-azure-powershell"></a>使用 Azure PowerShell 部署

若要部署到特定的 Azure 资源组，请使用以下命令：

```powershell
New-AzResourceGroupDeployment -ResourceGroupName <Azure-resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.logic/logic-app-create/azuredeploy.json
```

有关详细信息，请参阅以下主题：

* [使用 Resource Manager 模板和 Azure PowerShell 部署资源](../azure-resource-manager/templates/deploy-powershell.md)
* [`New-AzResourceGroupDeployment`](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment)

<a name="cli"></a>

## <a name="deploy-with-azure-cli"></a>使用 Azure CLI 进行部署

若要部署到特定的 Azure 资源组，请使用以下命令：

```azurecli
az deployment group create -g <Azure-resource-group-name> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.logic/logic-app-create/azuredeploy.json
```

有关详细信息，请参阅以下主题：

* [使用 Resource Manager 模板和 Azure CLI 部署资源](../azure-resource-manager/templates/deploy-cli.md)
* [`az deployment group create`](/cli/azure/deployment/group#az_deployment_group_create)

<a name="azure-pipelines"></a>

## <a name="deploy-with-azure-devops"></a>使用 Azure DevOps 进行部署

若要部署逻辑应用模板并管理环境，团队通常会使用 [Azure DevOps](/azure/devops/user-guide/what-is-azure-devops-services) 中的 [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) 等工具。 Azure Pipelines 提供一个可以添加到任何生成管道或发布管道的 [Azure 资源组部署任务](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzureResourceGroupDeploymentV2)。 若要授权部署和生成发布管道，还需要一个 Azure Active Directory (AD) [服务主体](../active-directory/develop/app-objects-and-service-principals.md)。 详细了解如何[在 Azure Pipelines 中使用服务主体](/azure/devops/pipelines/library/connect-to-azure)。

若要详细了解如何使用 Azure Pipelines 进行与 Azure 资源管理器模板相对应的持续集成和持续部署 (CI/CD)，请参阅以下主题和示例：

* [将资源管理器模板与 Azure Pipelines 集成](../azure-resource-manager/templates/add-template-to-azure-pipelines.md)
* [教程：使用 Azure Pipelines 持续集成 Azure 资源管理器模板](../azure-resource-manager/templates/deployment-tutorial-pipeline.md)
* [示例：使用 Azure 逻辑应用协调 Azure Pipelines](https://github.com/Azure-Samples/azure-logic-apps-pipeline-orchestration)
* [示例：从 Azure 逻辑应用连接到 Azure 存储帐户并在 Azure DevOps 中使用 Azure Pipelines 进行部署](https://github.com/Azure-Samples/azure-logic-apps-deployment-samples/tree/master/storage-account-connections)
* [示例：从 Azure 逻辑应用连接到 Azure 服务总线队列并在 Azure DevOps 中使用 Azure Pipelines 进行部署](https://github.com/Azure-Samples/azure-logic-apps-deployment-samples/tree/master/service-bus-connections)
* [示例：为 Azure 逻辑应用设置 Azure Functions 操作并在 Azure DevOps 中使用 Azure Pipelines 进行部署](https://github.com/Azure-Samples/azure-logic-apps-deployment-samples/tree/master/function-app-actions)
* [示例：从 Azure 逻辑应用连接到集成帐户并在 Azure DevOps 中使用 Azure Pipelines 进行部署](https://github.com/Azure-Samples/azure-logic-apps-deployment-samples/tree/master/integration-account-connections)

下面是有关使用 Azure Pipelines 的一般性概要步骤：

1. 在 Azure Pipelines 中创建空管道。

1. 选择管道所需的资源，例如，手动生成的或者在生成过程中生成的逻辑应用模板和模板参数文件。

1. 对于代理作业，请找到并添加“Azure 资源组部署”任务。

   ![添加“Azure 资源组部署”任务](./media/logic-apps-deploy-azure-resource-manager-templates/add-azure-resource-group-deployment-task.png)

1. 使用[服务主体](/azure/devops/pipelines/library/connect-to-azure)进行配置。

1. 添加对逻辑应用模板和模板参数文件的引用。

1. 继续在发布过程中根据需要为其他任何环境、自动测试或审批程序构建步骤。

<a name="authorize-oauth-connections"></a>

## <a name="authorize-oauth-connections"></a>授权 OAuth 连接

部署后，逻辑应用将使用有效的参数完成端到端工作。但是，若要生成用于[验证凭据](../active-directory/develop/authentication-vs-authorization.md)的有效访问令牌，你仍然需要对 OAuth 连接授权，或使用预先授权的 OAuth 连接。 不过，你只需要部署和验证 API 连接资源一次，这意味着你不必在后续部署中包括这些连接资源，除非你必须更新连接信息。 如果使用持续集成和持续部署管道，则只部署更新的逻辑应用资源，而不必每次都对连接重新授权。

以下是关于如何处理授权连接的一些建议：

* 若要对 OAuth 连接进行手动授权，请在 Azure 门户或 Visual Studio 的逻辑应用设计器中打开逻辑应用。 授权连接时，可能会显示一个确认页，供你授权访问。

* 在同一区域的逻辑应用中对 API 连接资源进行预授权和共享。 API 连接作为 Azure 资源独立于逻辑应用而存在。 虽然逻辑应用依赖于 API 连接资源，但 API 连接资源不依赖于逻辑应用，在删除依赖的逻辑应用后仍然存在。 此外，逻辑应用可以使用其他资源组中存在的 API 连接。 但是，逻辑应用设计器支持仅在逻辑应用所在的资源组中创建 API 连接。

  > [!NOTE]
  > 如果考虑共享 API 连接，请确保你的解决方案可以[处理潜在的限制问题](../logic-apps/handle-throttling-problems-429-errors.md#connector-throttling)。 限制发生在连接级别，因此在多个逻辑应用中重用同一连接可能会提高出现限制问题的可能性。

* 除非你的方案涉及需要多重身份验证的服务和系统，否则你可以使用 PowerShell 脚本为每个 OAuth 连接提供同意，方法是：在具有活动浏览器会话且已提供授权和同意的虚拟机上以普通用户帐户身份运行持续集成辅助角色。 例如，可以重新调整[逻辑应用 GitHub 存储库中的 LogicAppConnectionAuth 项目](https://github.com/logicappsio/LogicAppConnectionAuth)提供的示例脚本的用途。

* 如果改用 Azure Active Directory (Azure AD) [服务主体](../active-directory/develop/app-objects-and-service-principals.md)来授权连接，请了解如何[在逻辑应用模板中指定服务主体参数](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [监视逻辑应用](../logic-apps/monitor-logic-apps.md)
