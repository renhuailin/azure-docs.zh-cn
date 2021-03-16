---
title: 在 Azure 中设置用于处理数据的函数
titleSuffix: Azure Digital Twins
description: 了解如何在 Azure 中创建可以进行访问且由数字孪生触发的函数。
author: baanders
ms.author: baanders
ms.date: 8/27/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 4889744347b72603a0f6318f981bc2db4906b835
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2021
ms.locfileid: "102433533"
---
# <a name="connect-function-apps-in-azure-for-processing-data"></a>在 Azure 中连接函数应用以处理数据

使用[事件路由](concepts-route-events.md)通过计算资源（例如使用 [Azure Functions](../azure-functions/functions-overview.md) 创建的函数）来处理基于数据的数字孪生更新。 函数可用于更新数字孪生，以响应：
* 来自 IoT 中心的设备遥测数据
* 属性变更，或来自孪生图中另一个数字孪生的其他数据

本文将介绍如何在 Azure 中创建用于 Azure 数字孪生的函数。 

下面概述了其中包含的步骤：

1. 在 Visual Studio 中创建 Azure Functions 项目
2. 使用[事件网格](../event-grid/overview.md)触发器来编写函数
3. 向函数添加验证码（以便能够访问 Azure 数字孪生）
4. 将函数应用发布到 Azure
5. 为函数应用设置[安全](concepts-security.md)访问

## <a name="prerequisite-set-up-azure-digital-twins-instance"></a>先决条件：设置 Azure 数字孪生实例

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="create-a-function-app-in-visual-studio"></a>在 Visual Studio 中创建函数应用

在 Visual Studio 2019 中，选择“文件”>“新建”>“项目”，然后搜索 Azure Functions 模板 。 选择“_下一步_”。

:::image type="content" source="media/how-to-create-azure-function/create-azure-function-project.png" alt-text="Visual Studio 的屏幕截图，其中显示了新建项目对话框。突出显示了 Azure Functions 项目模板。":::

为函数应用指定名称并选择“创建”。

:::image type="content" source="media/how-to-create-azure-function/configure-new-project.png" alt-text="Visual Studio 的屏幕截图，其中显示了用于配置新项目的对话框，包括项目名称、保存位置、新建解决方案的选择，以及解决方案名称。":::

选择事件网格触发器的函数应用类型，然后选择“创建”。

:::image type="content" source="media/how-to-create-azure-function/event-grid-trigger-function.png" alt-text="Visual Studio 的屏幕截图，其中显示了用于新建 Azure Functions 应用程序的对话框。突出显示了事件网格触发器选项。":::

创建函数应用后，Visual Studio 将在项目文件夹中的 Function1.cs 文件中生成代码示例。 这一简短的函数用于记录事件。

:::image type="content" source="media/how-to-create-azure-function/visual-studio-sample-code.png" alt-text="已创建的新项目的项目窗口中的 Visual Studio 屏幕截图。其中显示了名为 Function1 的示例函数的代码。" lightbox="media/how-to-create-azure-function/visual-studio-sample-code.png":::

## <a name="write-a-function-with-an-event-grid-trigger"></a>使用事件网格触发器来编写函数

可以通过将 SDK 添加到函数应用来编写函数。 函数应用使用[适用于 .NET (C#) 的 Azure 数字孪生 SDK](/dotnet/api/overview/azure/digitaltwins/client) 与 Azure 数字孪生进行交互。 

为使用 SDK，需在项目中加入以下包。 可以使用 Visual Studio 的 NuGet 包管理器安装包，也可以在命令行工具中使用 `dotnet` 添加包。

* [Azure.DigitalTwins.Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core/)
* [Azure.Identity](https://www.nuget.org/packages/Azure.Identity/)
* [System.Net.Http](https://www.nuget.org/packages/System.Net.Http/)
* [Azure.Core](https://www.nuget.org/packages/Azure.Core/)

接下来，在 Visual Studio 解决方案资源管理器中，打开包含示例代码的 Function1.cs 文件，并将这些包的以下 `using` 语句添加到函数。 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="Function_dependencies":::

## <a name="add-authentication-code-to-the-function"></a>向函数添加验证码

现在，你需要将声明类级别变量并添加验证码，使函数可以访问 Azure 数字孪生。 你将向 Function1.cs 文件中的函数添加以下内容。

* 用于将 Azure 数字孪生服务 URL 读作环境变量的代码。 最好从环境变量读取服务 URL，而不是在函数中对其进行硬编码。 你将在[本文后面部分](#set-up-security-access-for-the-function-app)设置此环境变量的值。 有关环境变量的详细信息，请参阅[管理函数应用](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal)。

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ADT_service_URL":::

* 用于保存 HttpClient 实例的静态变量。 HttpClient 的创建成本相对较高，建议避免在每次调用函数时创建。

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="HTTP_client":::

* 在 Azure Functions 中，可使用托管标识凭据。
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ManagedIdentityCredential":::

* 在函数内添加本地变量 DigitalTwinsClient，用于保存 Azure 数字孪生客户端实例。 请勿在类中将此变量设置为静态。
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="DigitalTwinsClient":::

* 为 adtInstanceUrl 添加 NULL 检查，然后将函数逻辑包装在 try/catch 块中以捕获所有异常。

进行这些更改后，函数代码将如下所示：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs":::

编写应用程序后，可以按照下一部分中的步骤将其发布到 Azure。

## <a name="publish-the-function-app-to-azure"></a>将函数应用发布到 Azure

[!INCLUDE [digital-twins-publish-azure-function.md](../../includes/digital-twins-publish-azure-function.md)]

## <a name="set-up-security-access-for-the-function-app"></a>为函数应用设置安全访问

可以使用 Azure CLI 或 Azure 门户设置函数应用的安全性访问。 针对你的首选项，按照以下步骤操作。

# <a name="cli"></a>[CLI](#tab/cli)

可以在 [Azure Cloud Shell](https://shell.azure.com) 或[本地 Azure CLI 安装](/cli/azure/install-azure-cli)中运行这些命令。

### <a name="assign-access-role"></a>分配访问角色

前面示例中的函数主干要求将持有者令牌传递给它，以便能够使用 Azure 数字孪生进行身份验证。 为确保传递此持有者令牌，需要为函数应用设置[托管服务标识 (MSI)](../active-directory/managed-identities-azure-resources/overview.md) 权限以访问 Azure 数字孪生。 每个函数应用只需设置一次。

可以使用函数应用的系统托管标识向其分配 Azure 数字孪生实例的“Azure 数字孪生数据所有者”角色。 这将授予实例中的执行数据平面活动的函数应用权限。 然后，通过设置环境变量使函数可以访问 Azure 数字孪生实例的 URL。

1. 使用以下命令查看函数的系统托管标识的详细信息。 记下输出中的 principalId 字段。

    ```azurecli-interactive 
    az functionapp identity show -g <your-resource-group> -n <your-App-Service-(function-app)-name> 
    ```

    >[!NOTE]
    > 如果结果为空而不是显示标识详细信息，请使用以下命令为函数创建新的系统托管标识：
    > 
    >```azurecli-interactive    
    >az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>  
    >```
    >
    > 然后，输出将显示标识的详细信息，包括下一步所需的 principalId 值。 

1. 在以下命令中使用 principalId 值将函数应用的标识分配给 Azure 数字孪生实例的 Azure 数字孪生数据所有者角色。

    ```azurecli-interactive 
    az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Data Owner"
    ```

### <a name="configure-application-settings"></a>配置应用程序设置

最后，通过为函数设置环境变量使其可以访问 Azure 数字孪生实例的 URL。 有关环境变量的详细信息，请参阅[管理函数应用](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal)。 

> [!TIP]
> 通过将 https:// 添加到 Azure 数字孪生实例 hostName 的开头，创建 Azure 数字孪生实例的 URL 。 若要查看主机名以及实例的所有属性，可以运行 `az dt show --dt-name <your-Azure-Digital-Twins-instance>`。

```azurecli-interactive 
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-host-name>"
```

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

在 [Azure 门户](https://portal.azure.com/)中执行以下步骤。

### <a name="assign-access-role"></a>分配访问角色

通过使用系统分配的托管标识，Azure 资源无需在代码中存储凭据即可对云服务（如 Azure Key Vault）进行身份验证。 启用该标识后，可以通过 Azure 基于角色的访问控制授予所有必要的权限。 此类托管标识的生命周期与此资源的生命周期关联。 此外，每个资源只能具有一个系统分配的托管标识。

1. 在 [Azure 门户](https://portal.azure.com/)的搜索栏中，键入函数应用的名称以搜索函数应用。 选择结果中的应用。 

    :::image type="content" source="media/how-to-create-azure-function/portal-search-for-function-app.png" alt-text="Azure 门户的屏幕截图：正在门户搜索栏中搜索函数应用的名称，并突出显示了搜索结果。":::

1. 在函数应用页的左侧导航栏中，选择“标识”以使用函数的托管标识。 在“系统分配”页上，验证“状态”是否设置为“开启”（如果尚未，请立即设置并保存更改） 。

    :::image type="content" source="media/how-to-create-azure-function/verify-system-managed-identity.png" alt-text="Azure 门户的屏幕截图：在函数应用的“标识”页中，“状态”选项设置为“开启”。" lightbox="media/how-to-create-azure-function/verify-system-managed-identity.png":::

1. 选择“Azure 角色分配”按钮，该操作将打开“Azure 角色分配”页。

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-1.png" alt-text="Azure 门户的屏幕截图：突出显示了 Azure Function“标识”页中“权限”下的“Azure 角色分配”按钮。" lightbox="media/how-to-create-azure-function/add-role-assignment-1.png":::

    选择“+ 添加角色分配(预览版)”。

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-2.png" alt-text="Azure 门户的屏幕截图：突出显示了“Azure 角色分配”页中的“+ 添加角色分配(预览版)”。" lightbox="media/how-to-create-azure-function/add-role-assignment-2.png":::

1. 在打开的“+ 添加角色分配(预览版)”页上，选择以下值：

    * **范围**：资源组
    * **订阅**：选择 Azure 订阅
    * **资源组**：从下拉列表中选择资源组
    * 角色：从下拉列表中选择“Azure 数字孪生数据所有者”

    然后，点击“保存”按钮以保存详细信息。

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-3.png" alt-text="Azure 门户的屏幕截图：用于添加新角色分配的对话框（预览版）。其中显示了“范围”、“订阅”、“资源组”和“角色”的字段。":::

### <a name="configure-application-settings"></a>配置应用程序设置

若要使函数可以访问 Azure 数字孪生实例的 URL，可以为其设置环境变量。 有关环境变量的详细信息，请参阅[管理函数应用](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal)。 应用程序设置以环境变量的形式公开，以访问 Azure 数字孪生实例。 

若要使用实例的 URL 设置环境变量，请先查找 Azure 数字孪生实例的主机名来获取 URL。 在 [Azure 门户](https://portal.azure.com)搜索栏中搜索实例。 然后，选择左侧导航栏中的“概述”并查看主机名 。 复制此值。

:::image type="content" source="media/how-to-create-azure-function/instance-host-name.png" alt-text="Azure 门户的屏幕截图：Azure 数字孪生实例的“概述”页中突出显示了“主机名”值。":::

现在，你可以按照以下步骤创建应用程序设置：

1. 在门户搜索栏中搜索函数应用，然后在结果中将其选中。

    :::image type="content" source="media/how-to-create-azure-function/portal-search-for-function-app.png" alt-text="Azure 门户的屏幕截图：正在门户搜索栏中搜索函数应用的名称，并突出显示了搜索结果。":::

1. 在左侧的导航栏中，选择“配置”。 在“应用程序设置”选项卡中，选择“+ 新建应用程序设置” 。

    :::image type="content" source="media/how-to-create-azure-function/application-setting.png" alt-text="Azure 门户的屏幕截图：函数应用的“配置”页中突出显示了用于新建应用程序设置的按钮。":::

1. 在打开的窗口中，使用先前复制的主机名值创建应用程序设置。
    * **名称**：ADT_SERVICE_URL
    * **值**： https://{your-azure-digital-twins-host-name}
    
    选择“确定”，以创建应用程序设置。
    
    :::image type="content" source="media/how-to-create-azure-function/add-application-setting.png" alt-text="Azure 门户的屏幕截图：在“添加/编辑应用程序设置”页中填写“名称”和“值”字段后，突出显示了“确定”按钮。":::

1. 创建设置后，“应用程序设置”选项卡中应显示该设置。验证列表中是否显示了 ADT_SERVICE_URL，然后选择“保存”按钮以保存新的应用程序设置。

    :::image type="content" source="media/how-to-create-azure-function/application-setting-save-details.png" alt-text="Azure 门户的屏幕截图：“应用程序设置”页中突出显示了新的 ADT_SERVICE_URL 设置。“保存”按钮也进行了突出显示。":::

1. 对应用程序设置进行的所有更改都需要重启应用程序才能生效，因此请在系统提示时选择“继续”以重启应用程序。

    :::image type="content" source="media/how-to-create-azure-function/save-application-setting.png" alt-text="Azure 门户的屏幕截图：请注意重启应用程序后对应用程序设置进行的所有更改。突出显示了“继续”按钮。":::

---

## <a name="next-steps"></a>后续步骤

在本文中，你已按照步骤在 Azure 中设置了用于 Azure 数字孪生的函数应用。

接下来，了解如何基于基本函数将 IoT 中心数据引入 Azure 数字孪生：
* [操作说明：从 IoT 中心引入遥测数据](how-to-ingest-iot-hub-data.md)
