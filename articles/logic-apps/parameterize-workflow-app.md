---
title: 在单租户 Azure 逻辑应用中创建工作流的参数
description: 在单租户 Azure 逻辑应用的部署环境中，为工作流的不同值定义参数。
services: logic-apps
ms.suite: integration
ms.reviewer: azla
ms.topic: how-to
ms.date: 05/25/2021
ms.openlocfilehash: e9e29a091608be54c806a98323b9e485bc7a49a8
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110385298"
---
# <a name="create-parameters-for-values-that-change-in-workflows-across-environments-for-single-tenant-azure-logic-apps"></a>在各个环境的单租户 Azure 逻辑应用中的工作流中，针对要在其中更改的值创建参数

在 Azure 逻辑应用中，可以使用参数来抽象化可能在环境之间发生更改的值。 通过定义在工作流中使用的参数，可以先专注于设计工作流，然后稍后插入特定于环境的变量。

在多租户 Azure 逻辑应用中，可以在工作流设计器中创建和引用参数，然后在 Azure 资源管理器 (ARM) 模板和参数文件设置变量。 在部署时定义和设置参数。 因此，即使只需要更改一个变量，也必须重新部署逻辑应用的 ARM 模板。

在单租户 Azure 逻辑应用中，可以通过使用参数和应用设置在运行时和部署时使用环境变量。 本文介绍如何通过新的单租户参数体验来编辑、调用和引用环境变量。

## <a name="prerequisites"></a>先决条件

- Azure 帐户和订阅。 如果没有订阅，可以[注册免费的 Azure 帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- [托管在单租户 Azure 逻辑应用中的逻辑应用工作流](single-tenant-overview-compare.md)。

  如果没有逻辑应用，请在 Azure 门户或 [Visual Studio Code 中](create-single-tenant-workflows-visual-studio-code.md) [创建逻辑应用（标准）](create-single-tenant-workflows-azure-portal.md)。

## <a name="parameters-versus-app-settings"></a>参数与应用设置

在决定将环境变量存储到何处之前，请查看以下信息。

如果已使用 Azure Functions 或 Azure Web 应用，则应该会熟悉应用设置。 在 Azure 逻辑应用中，应用设置中集成了 Azure Key Vault。 可以直接[引用安全字符串](../app-service/app-service-key-vault-references.md)，例如连接字符串和密钥。 与 ARM 模板类似，在部署时可以定义环境变量，可以在[逻辑应用工作流定义](/azure/templates/microsoft.logic/workflows)中定义应用设置。 然后，可以捕获动态生成的基础结构值，例如连接终结点、存储字符串等。 但是，应用设置具有大小限制，无法从 Azure 逻辑应用中的某些区域引用。

如果熟悉多租户 Azure 逻辑应用中的工作流，可能也对参数有几分熟悉。 可以在比应用设置更广泛的用例中使用参数，例如支持大型的复杂对象和值。 如果使用 Visual Studio Code 作为本地开发工具，则还可以引用逻辑应用项目在文件 workflow.json 和 connections.json 中的参数。 如果要在解决方案中同时使用这两个选项，还可使用参数引用应用设置。

> [!NOTE]
> 对于开发工作，如果对 connections.json 文件做参数化处理，本地和 Azure 门户中的设计器体验将受到限制。 如果需要使用设计器进行开发，请使用非参数化 connections.json 文件。 然后，在部署管道中，替换为参数化文件。 运行时仍适用于参数化。 设计器改进正在开发中。

请考虑我们的建议，将参数用作参数化的默认机制。 这样，在需要存储安全密钥或字符串时，可以根据建议从参数中引用应用设置。

## <a name="what-is-parameterization"></a>什么是参数化？

如果使用 Visual Studio Code，可以在逻辑应用项目中定义 parameters.json 文件中的参数。 可以从逻辑应用中的任何工作流或连接对象中，引用 parameters.json 文件中的任何参数。 在单租户 Azure 逻辑应用中对工作流输入做参数化处理的方式与多租户 Azure 逻辑应用类似。

若要引用触发器或操作输入中的参数，请使用表达式 `@parameters('<parameter-name>')`。

> [!IMPORTANT]
> 请确保已添加引用自 parameters.json 文件的参数。

在单租户 Azure 逻辑应用中，可以对 connections.js 文件的不同部分做参数化处理。 然后，可以检查源代码管理中的 connections.json 文件，之后通过 parameters.json 文件管理连接。 若要对 connections.json 文件进行参数化处理，请将文本值（如 `ConnectionRuntimeUrl`）替换为单个 `parameters()` 表达式，例如 `@parameters('api-runtimeUrl')`。

还可以参数化复杂对象，如 `authentication` JSON 对象。 例如，将 `authentication` 对象值替换为包含单个参数表达式的字符串，例如 `@parameters('api-auth')`。 

> [!NOTE]
> connections.json 中唯一有效的表达式类型是 `@parameters` 和 `@appsetting`。

## <a name="define-parameters"></a>定义参数

在基于单租户的工作流中，需要将所有参数值放入名为 parameters.json 的根级别 JSON 文件中。 此文件包含一个包含键值对的对象。 键是每个参数的名称，值是每个参数的结构。 每个结构都需要同时包含 `type` 和 `value` 声明。

> [!NOTE]
> parameters.json 文件中唯一有效的表达式类型是 `@appsetting`。

基本参数文件如以下示例所示：

```json
{ 
    "responseString": { 
        "type": "string", 
        "value": "hello" 
    }, 
    "functionAuth": { 
        "type": "object", 
        "value": { 
            "type": "QueryString", 
            "name": "Code", 
            "value": "@appsetting('<AzureFunctionsOperation-FunctionAppKey')" 
        } 
    } 
} 
```

通常，需要管理参数文件的多个版本。 不同的部署环境（例如开发、测试和生产）下可能会存在不同的目标值。 这些参数文件的管理方式与管理 ARM 模板参数文件类似。 部署到特定环境时，可通过 DevOps 的管道来升级相应的参数文件。

若要使用 Azure CLI 动态替换参数文件，请运行以下命令：

```azurecli
az functionapp deploy --resource-group MyResourceGroup --name MyLogicApp --src-path C:\parameters.json --type static --target-path parameters.json
```

> [!NOTE]
> 目前，在 Azure 门户或工作流设计器中，动态替换参数文件的功能尚不可用。

有关设置用于 DevOps 部署的逻辑应用的详细信息，请参阅以下文档：

- [基于单租户的逻辑应用的 DevOps 部署概述](devops-deployment-single-tenant-azure-logic-apps.md)
- [设置面向基于单租户逻辑应用的 DevOps 部署](set-up-devops-deployment-single-tenant-azure-logic-apps.md)

## <a name="manage-app-settings"></a>管理应用设置

在单租户 Azure 逻辑应用中，应用设置包含同一逻辑应用中所有工作流的全局配置选项。 在 Visual Studio Code 本地运行工作流时，可以将这些设置作为 local.settings.json 文件中的本地环境变量并加以访问。 然后，可以在参数中引用这些应用设置。

若要添加、更新或删除应用设置，请选择并查看 Visual Studio Code、Azure 门户、Azure CLI 或 ARM (Bicep) 模板的下列部分。

### <a name="azure-portal"></a>[Azure 门户](#tab/azure-portal)

若要在 Azure 门户中查看逻辑应用的应用设置，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)搜索框中，找到并打开基于单租户的逻辑应用。
1. 在逻辑应用菜单上的“设置”下，选择“配置” 。
1. 在“配置”页上的“应用程序设置”选项卡上，查看逻辑应用的应用设置。
1. 若要查看所有值，请选择“显示值”。 或者，若要查看单个值，请选择该值。

若要新增设置，请执行以下步骤：

1. 在“应用程序设置”选项卡上的“应用程序设置”下，选择“新建应用程序设置”。
1. 对于“名称”，输入新设置的键或名称。
1. 对于“值”，输入新设置的值。
1. 准备好创建新的键值对时，请选择“确定”。

:::image type="content" source="./media/parameterize-workflow-app/portal-app-settings-values.png" alt-text="屏幕截图显示 Azure 门户和配置窗格，其中包含基于单租户的逻辑应用的应用设置和值。" lightbox="./media/parameterize-workflow-app/portal-app-settings-values.png":::

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 查看当前应用设置，请运行 `az logicapp config appsettings list` 命令。 请确保命令包含 `--name -n` 和 `--resource-group -g` 参数，例如：

```azurecli
az logicapp config appsettings list --name MyLogicApp --resource-group MyResourceGroup
```

若要使用 Azure CLI 添加或更新应用设置，请运行命令 `az logicapp config appsettings set`。 请确保命令包含 `--name n` 和 `--resource-group -g` 参数。 例如，以下命令可用于创建一个设置，该设置具有名为 `CUSTOM_LOGIC_APP_SETTING` 的键，其值为 `12345`：

```azurecli
az logicapp config appsettings set --name MyLogicApp --resource-group MyResourceGroup --settings CUSTOM_LOGIC_APP_SETTING=12345 
```

### <a name="resource-manager-or-bicep-template"></a>[资源管理器或 Bicep 模板](#tab/azure-resource-manager)

若要查看和定义 ARM 模板或 Bicep 模板中的应用设置，请查找逻辑应用的资源定义，并更新 `appSettings` JSON 对象。 有关完整资源定义，请参阅 [ARM 模板参考](/azure/templates/microsoft.web/sites)。

ARM 模板或 Bicep 模板的文件设置如以下示例所示：

```json
"appSettings": [
    {
        "name": "string",
        "value": "string"
    },
    {
        "name": "string",
        "value": "string"
    },
    <...>
], 
```

---

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [适用于 Azure 逻辑应用的单租户与多租户和集成服务环境的比较情况](single-tenant-overview-compare.md)
