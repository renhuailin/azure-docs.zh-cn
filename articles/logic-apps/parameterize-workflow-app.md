---
title: 在单租户工作流中创建参数值
description: 在单租户 Azure 逻辑应用的部署环境中，为不同工作流的值定义参数。
services: logic-apps
ms.suite: integration
ms.reviewer: azla
ms.topic: how-to
ms.date: 08/09/2021
ms.openlocfilehash: 32d15d248154c61320fe12074373a6401fe3bdcd
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121742198"
---
# <a name="create-parameters-to-use-in-workflows-across-environments-in-single-tenant-azure-logic-apps"></a>在单租户 Azure 逻辑应用内的各个环境中，创建要在工作流中使用的参数

在 Azure 逻辑应用中，可以通过定义“参数”来抽象可能在整个开发、测试和生产环境内更改的工作流中的值。 改为使用参数后，可以更专注于设计工作流，并在稍后插入特定于环境的变量。

本文介绍了参数在单租户 Azure 逻辑应用中的工作方式，以及使用单租户参数体验编辑、引用和管理环境变量的方法。

<a name="parameters-introduction"></a>

## <a name="parameters-in-single-tenant-versus-multi-tenant"></a>单租户与多租户中的参数

如果熟悉“多租户”Azure 逻辑应用，可能已对参数比较熟悉。 在单租户 Azure 逻辑应用中对工作流输入做参数化处理的方式与在多租户 Azure 逻辑应用中的类似。不过，存在着一个主要区别。

例如，在工作流设计器中操作时定义参数，这一操作同时适用于单租户和多租户服务。 定义参数后，可以从同一逻辑应用资源内的任意工作流或连接中引用该参数。 但是，对于多租户服务中，在设计器中创建并使用参数后，需要在 Azure 资源管理器模板（ARM 模板）和模板参数文件中定义和设置环境变量。 在此方案中，你必须在部署阶段定义和设置参数。这意味着，即使只需更改一个变量，也必须重新部署逻辑应用的 ARM 模板。

相比之下，对于“单租户”服务，可以通过使用参数和应用设置在运行时和部署时使用环境变量。 在单租户 Azure 逻辑应用中，应用设置包含同一逻辑应用中所有工作流的全局配置选项。 有关详细信息，请查阅 [基于逻辑应用编辑单租户的主机和应用设置](edit-app-settings-host-settings.md)。

例如，可以使用应用设置与 Azure Key Vault 集成，并[直接引用安全字符串](../app-service/app-service-key-vault-references.md)，例如连接字符串和密钥。 与 ARM 模板类似，在部署时可以定义环境变量，可以在[逻辑应用工作流定义](/azure/templates/microsoft.logic/workflows)中定义应用设置。 然后，可以捕获动态生成的基础结构值，例如连接终结点、存储字符串等。

但是，应用设置受大小限制，系统无法在 Azure 逻辑应用中的某些区域将其引用。 参数可提供比应用设置范围更广的用例，例如支持大值大小和复杂对象。

例如，如果使用 Visual Studio Code 作为在本地运行工作流的本地开发工具，则可以在逻辑应用项目中使用“parameters.json”文件定义参数。 然后，可以从项目“workflow.js”文件内的任意工作流或者项目“connections.json”文件的任意连接对象中引用此参数文件内的任何参数，。 下表介绍了几个常见用例：

* 创建包含所有值，且会在测试时使用这些值的测试参数文件。 部署时，可以将测试参数文件替换为生产参数文件。

* 将“connections.js”文件的不同部分参数化。 然后，可以检查源代码管理中的 connections.json 文件，之后通过 parameters.json 文件管理连接。

* 还可以将复杂对象参数化，如 `authentication` JSON 对象。 例如，将 `authentication` 对象值替换为包含单个参数表达式的字符串，例如 `@parameters('api-auth')`。

* 查看和编辑项目“local.settings.js”文件的应用设置。 然后，可以在参数中引用这些应用设置。

> [!NOTE]
> 一般建议考虑使用参数作为将值参数化的默认方法，而非应用设置。 这样，在需要存储安全密钥或字符串时，可以根据建议从参数中引用应用设置。 如有需要，可以通过使用参数引用应用设置的方式，在解决方案中同时使用这两个选项。

## <a name="prerequisites"></a>先决条件

* Azure 帐户和订阅。 如果没有订阅，可以[注册免费的 Azure 帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* [托管在单租户 Azure 逻辑应用中的逻辑应用工作流](single-tenant-overview-compare.md)。

  如果没有逻辑应用，请在 Azure 门户或 [Visual Studio Code 中](create-single-tenant-workflows-visual-studio-code.md) [创建逻辑应用（标准）](create-single-tenant-workflows-azure-portal.md)。

## <a name="define-use-and-edit-parameters"></a>定义、使用和编辑参数

### <a name="azure-portal"></a>Azure 门户

1. 在 [Azure 门户](https://portal.azure.com)中，打开基于单租户的逻辑应用。 在“工作流”下，选择并打开设计器中的工作流。

1. 在设计器工具栏上，选择“参数”。

   ![屏幕截图内容：Azure 门户设计器、工作流设计器和设计器工具栏中已选的“参数”。](./media/parameterize-workflow-app/portal-designer-select-parameters.png)

1. 在“参数”窗格中选择“创建参数”。

1. 提供以下与待创建参数相关的信息：

   | 属性 | 必须 | 说明 |
   |----------|----------|-------------|
   | **名称** | 是 | 要创建参数的名称。 |
   | 类型 | 是 | 参数的数据类型，例如“数组”、“布尔型”、“浮点”、“整形”、“对象”和“字符串”。 <p><p>注意：在基于单租户的工作流中，不支持诸如 `securestring` 和 `secureobject` 安全数据类型。 |
   | **值** | 是 | 参数的值。 <p><p>由于工作流逻辑、连接信息和参数值不位于单个位置，因此在单租户 Azure 逻辑应用中，必须指定参数值。 设计器必须能够在加载前即能解析参数。 |
   |||

   以下示例介绍了字符串参数的定义：

   ![屏幕截图内容：Azure 门户、工作流设计器和显示示例参数定义的“参数”窗格。](./media/parameterize-workflow-app/define-parameter.png)

1. 完成后，关闭“参数”窗格，但切记保存工作流，以保存新的参数定义。

1. 若要在触发器或操作中引用参数，且该触发器位于同一逻辑应用内的任意工作流中，请执行以下步骤：

   1. 在设计器中打开目标工作流，然后展开触发器或操作。

   1. 在要使用参数的属性中，单击该属性编辑框的内部。

   1. 在已打开的动态内容列表的“参数”下，选择之前创建的参数，如下所示：

      ![屏幕截图内容：光标位于属性编辑框的示例操作，已展开的动态内容列表，以及先前已选的已创建参数。](./media/parameterize-workflow-app/reference-parameter.png)

1. 若要在同一逻辑应用内查看或编辑参数，请执行以下任一步骤：

   * 打开逻辑应用中的任意工作流。 在工作流菜单中选择“设计器”。 在设计器工具栏中选择“参数”。

     “参数”窗格会打开并显示在逻辑应用的工作流内定义的所有参数。

   * 若要批量查看或编辑 JSON，请在逻辑应用的主菜单中选择“参数”。

     “参数”JSON 视图会打开并显示在逻辑应用的工作流内定义的所有参数。

### <a name="visual-studio-code"></a>Visual Studio Code

1. 在名为“parameters.json”的项目根级别 JSON 文件中，定义“全部”参数及其值。 此文件内含包含“键值”对的对象。 每个“键”即每个参数的名称，而每个“值”则为每个参数的结构。 每个结构都需要同时包含 `type` 和 `value` 声明。

   > [!IMPORTANT]
   > parameters.json 文件必须定义并包含所有参数及其值。可在项目的其他位置引用或使用的这些参数，例如，在工作流定义或连接中。

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
                "value": "@appsetting('<AzureFunctionsOperation-FunctionAppKey>')" 
            }
        }
    }
    ```

    > [!NOTE]
    > parameters.json 文件中唯一有效的表达式类型是 `@appsetting`。

1. 若要引用触发器或操作输入中的参数，请使用表达式 `@parameters('<parameter-name>')`。

#### <a name="parameterize-connections-file"></a>参数化连接文件

若要对 connections.json 文件进行参数化处理，请将文本值（如 `ConnectionRuntimeUrl`）替换为单个 `parameters()` 表达式，例如 `@parameters('api-runtimeUrl')`。 connections.json 中唯一有效的表达式类型是 `@parameters` 和 `@appsetting`。

> [!IMPORTANT]
>
> 如果在开发过程中将 connections.json 文件参数化，则本地和 Azure 门户中的设计器体验将受到限制。 如果需要使用设计器进行开发，请改用未经参数化处理的 connections.json 文件。 然后，在部署管道中，替换为参数化文件。 
> 运行时仍适用于参数化。 设计器改进正在开发中。

以下示例展示了同时采用“应用设置”和“参数”，且经参数化处理的 connections.json 文件。 尽管都希望尽可能使用“参数”，但该示例是异常情况或边角案例。由于“应用部署”在部署阶段即会生成，且更易于在开发管道中动态填充。因此，通常而言，对“应用设置”的使用情况多于“参数”。 此示例文件将参数用于复杂的 `blob_auth` 身份验证对象，并将应用设置用于其他值。 在这种情况下，可以将参数用于身份验证对象，因为已不太可能在工作流中引用该参数：

```json
{
   "serviceProviderConnections": {
      "serviceBus": {
         "parameterValues": {
            "connectionString": "@appsetting('serviceBus_connectionString')"
        },
        "serviceProvider": {
           "id": "/serviceProviders/serviceBus"
        },
        "displayName": "servicebus"
     }
   },
   "managedApiConnections": {
      "azureblob": {
         "api": {
            "id": "/subscriptions/@appsetting('WORKFLOWS_SUBSCRIPTION_ID')/providers/Microsoft.Web/locations/@appsetting('WORKFLOWS_LOCATION_NAME')/managedApis/azureblob"
         },
         "connection": {
            "id": "/subscriptions/@appsetting('WORKFLOWS_SUBSCRIPTION_ID')/resourceGroups/@appsetting('WORKFLOWS_RESOURCE_GROUP_NAME')/providers/Microsoft.Web/connections/azureblob"
         },
         "connectionRuntimeUrl": "@appsetting('BLOB_CONNECTION_RUNTIMEURL')",
         "authentication": "@parameters('blob_auth')"
      }
   }
}
```

## <a name="manage-parameters-files"></a>管理参数文件

通常，需要管理参数文件的多个版本。 不同的部署环境（例如开发、测试和生产）下可能会存在不同的目标值。 这些参数文件的管理方式与管理 ARM 模板参数文件类似。 部署到特定环境时，通常可通过 DevOps 的管道来升级相应的参数文件。

若要使用 Azure CLI 动态替换参数文件，请运行以下命令：

```azurecli
az functionapp deploy --resource-group MyResourceGroup --name MyLogicApp --src-path C:\parameters.json --type static --target-path parameters.json
```

如果存在一个基于 NuGet 的逻辑应用项目，则必须更新项目文件 (&lt;logic-app-name&gt;.csproj) 以在生成输出中包含参数文件，如下所示：

```csproj
<ItemGroup>
  <None Update="parameters.json">
    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
  </None>
</ItemGroup>
```

> [!NOTE]
> 目前，在 Azure 门户或工作流设计器中，动态替换参数文件的功能尚不可用。

有关设置用于 DevOps 部署的逻辑应用的详细信息，请参阅以下文档：

* [基于单租户的逻辑应用的 DevOps 部署概述](devops-deployment-single-tenant-azure-logic-apps.md)
* [设置面向基于单租户逻辑应用的 DevOps 部署](set-up-devops-deployment-single-tenant-azure-logic-apps.md)

## <a name="manage-app-settings"></a>管理应用设置

在单租户 Azure 逻辑应用中，应用设置包含同一逻辑应用中所有工作流的全局配置选项。 在 Visual Studio Code 本地运行工作流时，可以将这些应用设置作为 local.settings.json 文件中的本地环境变量并加以访问。 然后，可以在参数中引用这些应用设置。

若要添加、更新或删除应用设置，请选择并查看 Visual Studio Code、Azure 门户、Azure CLI 或 ARM (Bicep) 模板的下列部分。

### <a name="azure-portal"></a>[Azure 门户](#tab/azure-portal)

若要在 Azure 门户中查看逻辑应用的应用设置，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中，打开基于单租户的逻辑应用。

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
