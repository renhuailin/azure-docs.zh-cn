---
title: 在 Azure 中设置用于处理数据的函数
titleSuffix: Azure Digital Twins
description: 了解如何在 Azure 中创建可以进行访问且由数字孪生触发的函数。
author: baanders
ms.author: baanders
ms.date: 8/27/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 8aaa1b2865b1d0f39e6cb224c3979b4f53eeee81
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110066711"
---
# <a name="connect-function-apps-in-azure-for-processing-data"></a>在 Azure 中连接函数应用以处理数据

可以通过计算资源使用[事件路由](concepts-route-events.md)来基于数据更新数字孪生体。 例如，使用 [Azure Functions](../azure-functions/functions-overview.md) 创建的函数可以更新数字孪生体，以响应：
* 来自 Azure IoT 中心的设备遥测数据。
* 属性更改，或来自孪生体图中另一个数字孪生体的其他数据。

本文将演示如何在 Azure 中创建用于 Azure 数字孪生的函数。 若要创建函数，请执行以下基本步骤：

1. 在 Visual Studio 中创建 Azure Functions 项目。
2. 编写具有 [Azure 事件网格](../event-grid/overview.md)触发器的函数。
3. 向函数添加验证码，以便能够访问 Azure 数字孪生。
4. 将函数应用发布到 Azure。
5. 为函数应用设置[安全性](concepts-security.md)。

## <a name="prerequisite-set-up-azure-digital-twins"></a>先决条件：设置 Azure 数字孪生

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="create-a-function-app-in-visual-studio"></a>在 Visual Studio 中创建函数应用

在 Visual Studio 2019 中，依次选择“文件” > “新建” > “项目”。   搜索“Azure Functions”模板。 选择“**下一步**”。

:::image type="content" source="media/how-to-create-azure-function/create-azure-function-project.png" alt-text="Visual Studio 的屏幕截图，其中显示了新建项目对话框。突出显示了 Azure Functions 项目模板。":::

为函数应用指定名称，然后选择“创建”。

:::image type="content" source="media/how-to-create-azure-function/configure-new-project.png" alt-text="Visual Studio 的屏幕截图，其中显示了用于配置新项目的对话框。设置包括项目名称、保存位置、新建解决方案的选择，以及解决方案名称。":::

选择函数应用类型“事件网格触发器”，然后选择“创建”。

:::image type="content" source="media/how-to-create-azure-function/event-grid-trigger-function.png" alt-text="Visual Studio 的屏幕截图，其中显示了用于新建 Azure Functions 应用程序的对话框。突出显示了事件网格触发器选项。":::

创建函数应用后，Visual Studio 会在项目文件夹中的 Function1.cs 文件中生成代码示例。 这一简短的函数用于记录事件。

:::image type="content" source="media/how-to-create-azure-function/visual-studio-sample-code.png" alt-text="Visual Studio 的屏幕截图。显示了新项目的项目窗口。示例函数的代码显示在名为 Function1 的文件中。" lightbox="media/how-to-create-azure-function/visual-studio-sample-code.png":::

## <a name="write-a-function-that-has-an-event-grid-trigger"></a>编写具有事件网格触发器的函数

可以通过将 SDK 添加到函数应用来编写函数。 函数应用使用[适用于 .NET (C#) 的 Azure 数字孪生 SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) 与 Azure 数字孪生进行交互。 

要使用 SDK，需在项目中加入以下包。 使用 Visual Studio NuGet 包管理器安装包。 或在命令行工具中使用 `dotnet` 添加包。

* [Azure.DigitalTwins.Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core/)
* [Azure.Identity](https://www.nuget.org/packages/Azure.Identity/)
* [System.Net.Http](https://www.nuget.org/packages/System.Net.Http/)
* [Azure.Core](https://www.nuget.org/packages/Azure.Core/)

接下来，在 Visual Studio 解决方案资源管理器中，打开包含示例代码的 Function1.cs 文件。 为包添加以下 `using` 语句。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="Function_dependencies":::

## <a name="add-authentication-code-to-the-function"></a>向函数添加验证码

现在声明类级别变量并添加验证码，使函数可以访问 Azure 数字孪生。 将变量和代码添加到 Function1.cs 文件中的函数。

* 用于将 Azure 数字孪生服务 URL 读作环境变量的代码。 最好从环境变量读取服务 URL，而不是在函数中对其进行硬编码。 你将在[本文后面部分](#set-up-security-access-for-the-function-app)设置此环境变量的值。 有关环境变量的详细信息，请参阅管理函数应用。

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ADT_service_URL":::

* **用于保存 HttpClient 实例的静态变量。** HttpClient 的创建成本相对较高，建议避免在每次调用函数时创建。

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="HTTP_client":::

* 托管标识凭据。
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ManagedIdentityCredential":::

* 本地变量 DigitalTwinsClient。 在函数内添加该变量，用于保存 Azure 数字孪生客户端实例。 请勿在类中将此变量设置为静态。
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="DigitalTwinsClient":::

* 对 adtInstanceUrl 的 NULL 检查。 添加 NULL 检查，然后将函数逻辑包装在 try/catch 块中以捕获所有异常。

进行这些更改后，函数代码将如以下示例所示。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs":::

编写应用程序后，可以将其发布到 Azure。

## <a name="publish-the-function-app-to-azure"></a>将函数应用发布到 Azure

[!INCLUDE [digital-twins-publish-azure-function.md](../../includes/digital-twins-publish-azure-function.md)]

### <a name="verify-the-publication-of-your-function"></a>验证函数的发布

1. 在 [Azure 门户](https://portal.azure.com/)使用你的凭据登录。
2. 在窗口顶部的搜索框中，搜索函数应用名称，然后选择它。

    :::image type="content" source="media/how-to-create-azure-function/search-function-app.png" alt-text="显示 Azure 门户的屏幕截图。在搜索字段中，输入函数应用名称。" lightbox="media/how-to-create-azure-function/search-function-app.png":::

3. 在打开的“函数应用”页中，选择左侧菜单中的“函数”。 如果成功发布函数，则其名称会出现在列表中。

    > [!Note] 
    > 你可能需要等待几分钟或刷新页面几次，然后你的函数才会出现在已发布函数列表中。

    :::image type="content" source="media/how-to-create-azure-function/view-published-functions.png" alt-text="查看 Azure 门户中的已发布函数。" lightbox="media/how-to-create-azure-function/view-published-functions.png":::

要访问 Azure 数字孪生，函数应用需要系统托管标识并且该标识具有访问 Azure 数字孪生实例的权限。 你接下来要设置此内容。

## <a name="set-up-security-access-for-the-function-app"></a>为函数应用设置安全访问

可以使用 Azure CLI 或 Azure 门户设置函数应用的安全性访问。 按照适用于你首选项的步骤进行操作。

# <a name="cli"></a>[CLI](#tab/cli)

在 [Azure Cloud Shell](https://shell.azure.com) 或[本地 Azure CLI 安装](/cli/azure/install-azure-cli)中运行这些命令。
可以使用函数应用的系统托管标识向其分配 Azure 数字孪生实例的“Azure 数字孪生数据所有者”角色。 该角色会授予实例中的执行数据平面活动的函数应用权限。 然后，通过设置环境变量使函数可以访问实例的 URL。

### <a name="assign-an-access-role"></a>分配访问角色

[!INCLUDE [digital-twins-permissions-required.md](../../includes/digital-twins-permissions-required.md)]

前面示例中的函数主干需要将持有者令牌传递给它。 如果未传递持有者令牌，则函数应用无法与 Azure 数字孪生进行身份验证。 

若要确保传递持有者令牌，请设置[托管标识](../active-directory/managed-identities-azure-resources/overview.md)权限，使函数应用可以访问 Azure 数字孪生。 只需为每个函数应用设置一次这些权限。


1. 使用以下命令查看函数的系统托管标识的详细信息。 记下输出中的 `principalId` 字段。

    ```azurecli-interactive 
    az functionapp identity show --resource-group <your-resource-group> --name <your-App-Service-(function-app)-name>   
    ```

    >[!NOTE]
    > 如果结果为空而不是显示标识详细信息，请使用以下命令为函数创建新的系统托管标识：
    > 
    >```azurecli-interactive    
    >az functionapp identity assign --resource-group <your-resource-group> --name <your-App-Service-(function-app)-name>    
    >```
    >
    > 输出会显示标识的详细信息，包括下一步所需的 `principalId` 值。 

1. 在以下命令中使用 `principalId` 值，以将函数应用的标识分配到 Azure 数字孪生实例的“Azure 数字孪生数据所有者”角色。

    ```azurecli-interactive 
    az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Data Owner"
    ```

### <a name="configure-application-settings"></a>配置应用程序设置

通过为其设置环境变量使函数可以访问实例的 URL。 有关环境变量的详细信息，请参阅管理函数应用。 

> [!TIP]
> 通过将 https:// 添加到实例主机名的开头，创建 Azure 数字孪生实例的 URL。 若要查看主机名以及实例的所有属性，请运行 `az dt show --dt-name <your-Azure-Digital-Twins-instance>`。

```azurecli-interactive 
az functionapp config appsettings set --resource-group <your-resource-group> --name <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-host-name>"
```

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

在 [Azure 门户](https://portal.azure.com/)中执行以下步骤。

### <a name="assign-an-access-role"></a>分配访问角色

[!INCLUDE [digital-twins-permissions-required.md](../../includes/digital-twins-permissions-required.md)]

通过使用系统分配的托管标识，Azure 资源无需在代码中存储凭据即可对云服务（如 Azure Key Vault）进行身份验证。 启用系统分配的托管标识后，可以通过 Azure 基于角色的访问控制授予所有必要的权限。 

此类托管标识的生命周期与此资源的生命周期关联。 此外，每个资源只能具有一个系统分配的托管标识。

1. 在 [Azure 门户](https://portal.azure.com/)的搜索框中，键入函数应用的名称以搜索函数应用。 选择结果中的应用。 

    :::image type="content" source="media/how-to-create-azure-function/portal-search-for-function-app.png" alt-text="Azure 门户的屏幕截图。函数应用的名称处于门户搜索栏中，并突出显示了搜索结果。":::

1. 在函数应用页的左侧菜单中，选择“标识”以使用函数的托管标识。 在“系统分配”页中，验证“状态”是否设置为“开启” 。 如果不是，请立即设置，然后“保存”更改。

    :::image type="content" source="media/how-to-create-azure-function/verify-system-managed-identity.png" alt-text="Azure 门户的屏幕截图。在函数应用的“标识”页中，“状态”选项设置为“开启”。" lightbox="media/how-to-create-azure-function/verify-system-managed-identity.png":::

1. 选择“Azure 角色分配”。

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-1.png" alt-text="Azure 门户的屏幕截图。在 Azure Functions“标识”页中“权限”下，突出显示了“Azure 角色分配”按钮。" lightbox="media/how-to-create-azure-function/add-role-assignment-1.png":::

    选择“+ 添加角色分配(预览版)”。

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-2.png" alt-text="Azure 门户的屏幕截图。在“Azure 角色分配”页中，突出显示了“添加角色分配(预览版)”按钮。" lightbox="media/how-to-create-azure-function/add-role-assignment-2.png":::

1. 在“添加角色分配(预览版)”页上，选择以下值：

    * 范围：资源组
    * **订阅**：选择 Azure 订阅。
    * 资源组：选择你的资源组。
    * 角色：Azure 数字孪生数据所有者

    选择“保存”以保存详细信息。

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-3.png" alt-text="Azure 门户的屏幕截图，演示如何添加新角色分配。该对话框显示“范围”、“订阅”、“资源组”和“角色”的字段。":::

### <a name="configure-application-settings"></a>配置应用程序设置

若要使函数可以访问 Azure 数字孪生实例的 URL，可以设置环境变量。 应用程序设置以环境变量的形式公开，以便允许访问 Azure 数字孪生实例。 有关环境变量的详细信息，请参阅管理函数应用。 

若要使用实例的 URL 设置环境变量，请先查找实例的主机名： 

1. 在 [Azure 门户](https://portal.azure.com)中搜索实例。 
1. 在左侧菜单中选择“概述”。 
1. 复制“主机名”值。

    :::image type="content" source="media/how-to-create-azure-function/instance-host-name.png" alt-text="Azure 门户的屏幕截图。在实例的“概述”页中，突出显示了主机名值。":::

现在可以创建应用程序设置：

1. 在门户搜索栏中搜索函数应用，然后在结果中将其选中。

    :::image type="content" source="media/how-to-create-azure-function/portal-search-for-function-app.png" alt-text="Azure 门户的屏幕截图。正在门户搜索栏中搜索函数应用的名称。突出显示了搜索结果。":::

1. 在左侧选择“配置”。 在“应用程序设置”选项卡上，选择“+ 新建应用程序设置” 。

    :::image type="content" source="media/how-to-create-azure-function/application-setting.png" alt-text="Azure 门户的屏幕截图。在函数应用的“配置”选项卡中，突出显示了用于新建应用程序设置的按钮。":::

1. 在打开的窗口中，使用复制的主机名值创建应用程序设置。
    * **名称**：ADT_SERVICE_URL
    * **值**： https://{your-azure-digital-twins-host-name}
    
    选择“确定”，以创建应用程序设置。
    
    :::image type="content" source="media/how-to-create-azure-function/add-application-setting.png" alt-text="Azure 门户的屏幕截图。在“添加/编辑应用程序设置”页中，填写了“名称”和“值”字段。突出显示了“确定”按钮。":::

1. 创建设置后，它应出现在“应用程序设置 ”选项卡上。验证 ADT_SERVICE_URL 是否出现在列表中。 然后通过选择“保存”来保存新应用程序设置。

    :::image type="content" source="media/how-to-create-azure-function/application-setting-save-details.png" alt-text="Azure 门户的屏幕截图。在应用程序设置选项卡上，突出显示了新的 ADT SERVICE URL 设置。还突出显示了“保存”按钮。":::

1. 对应用程序设置进行的任何更改都需要重启应用程序，因此请在系统提示时选择“继续”以重启应用程序。

    :::image type="content" source="media/how-to-create-azure-function/save-application-setting.png" alt-text="Azure 门户的屏幕截图。一个备注指出对应用程序设置进行的任何更改都会重启应用程序。突出显示了“继续”按钮。":::

---

## <a name="next-steps"></a>后续步骤

在本文中，你在 Azure 中设置了用于 Azure 数字孪生的函数应用。 接下来，了解如何基于基本函数[将 IoT 中心数据引入 Azure 数字孪生](how-to-ingest-iot-hub-data.md)。
