---
title: 在 Azure 中设置用于处理数据的函数
titleSuffix: Azure Digital Twins
description: 请参阅如何在 Azure 中创建可通过数字孪生访问和触发的函数。
author: baanders
ms.author: baanders
ms.date: 8/27/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 8ed4e550ea441d5d99a3debb6bf37eb7db2a4a20
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102180106"
---
# <a name="connect-function-apps-in-azure-for-processing-data"></a>连接 Azure 中的函数应用以处理数据

使用 [**事件路由**](concepts-route-events.md) 来处理基于数据的数字孪生，例如通过使用 [Azure Functions](../azure-functions/functions-overview.md)进行的函数。 函数可用于更新数字克隆，以响应：
* 来自 IoT 中心的设备遥测数据
* 属性更改或其他数据来自于克隆图形中的其他数字输出

本文逐步讲解如何在 Azure 中创建用于 Azure 数字孪生的函数。 

下面概述了它包含的步骤：

1. 在 Visual Studio 中创建 Azure Functions 项目
2. 使用 [事件网格](../event-grid/overview.md) 触发器编写函数
3. 将身份验证代码添加到函数 (以便能够访问 Azure 数字孪生) 
4. 将函数应用发布到 Azure
5. 为 function app 设置 [安全](concepts-security.md) 访问

## <a name="prerequisite-set-up-azure-digital-twins-instance"></a>先决条件：设置 Azure 数字孪生实例

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="create-a-function-app-in-visual-studio"></a>在 Visual Studio 中创建函数应用

在 Visual Studio 2019 中，选择 " _文件" > 新建 > 项目_ "，然后搜索 _Azure Functions_ 模板。 选择“_下一页_”。

:::image type="content" source="media/how-to-create-azure-function/create-azure-function-project.png" alt-text="显示 &quot;新建项目&quot; 对话框的 Visual Studio 屏幕截图。将突出显示 &quot;Azure Functions 项目&quot; 模板。":::

指定函数应用的名称，然后选择 " _创建_"。

:::image type="content" source="media/how-to-create-azure-function/configure-new-project.png" alt-text="Visual Studio 的屏幕截图，其中显示了用于配置新项目的对话框，包括项目名称、保存位置、创建新解决方案的选项和解决方案名称。":::

选择 " *事件网格触发器* " 的 "函数应用类型"，然后选择 " _创建_"。

:::image type="content" source="media/how-to-create-azure-function/event-grid-trigger-function.png" alt-text="Visual Studio 的屏幕截图，其中显示了创建新的 Azure Functions 应用程序的对话框。将突出显示事件网格触发器选项。":::

创建 function app 后，Visual Studio 将在项目文件夹中的 **Function1.cs** 文件中生成代码示例。 此 short 函数用于记录事件。

:::image type="content" source="media/how-to-create-azure-function/visual-studio-sample-code.png" alt-text="已创建的新项目的 &quot;项目&quot; 窗口中 Visual Studio 的屏幕截图。示例函数有一个名为 Function1 的代码。" lightbox="media/how-to-create-azure-function/visual-studio-sample-code.png":::

## <a name="write-a-function-with-an-event-grid-trigger"></a>使用事件网格触发器编写函数

可以通过将 SDK 添加到 function app 来编写函数。 函数应用使用 [用于 .net 的 Azure 数字孪生 SDK (c # ) ](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)与 Azure 数字孪生交互。 

若要使用 SDK，需要在项目中包含以下包。 可以使用 Visual Studio 的 NuGet 包管理器安装包，也可以 `dotnet` 在命令行工具中使用添加包。

* [DigitalTwins](https://www.nuget.org/packages/Azure.DigitalTwins.Core/)
* [Azure. 标识](https://www.nuget.org/packages/Azure.Identity/)
* [System.Net.Http](https://www.nuget.org/packages/System.Net.Http/)
* [Azure 核心](https://www.nuget.org/packages/Azure.Core/)

接下来，在 Visual Studio 解决方案资源管理器中，打开 _Function1.cs_ 文件，其中包含示例代码，并将 `using` 这些包的以下语句添加到函数。 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="Function_dependencies":::

## <a name="add-authentication-code-to-the-function"></a>将身份验证代码添加到函数

现在，你将声明类级别变量并添加允许函数访问 Azure 数字孪生的身份验证代码。 将以下项添加到 _Function1.cs_ 文件中的函数。

* 用于读取 Azure 数字孪生服务 URL 作为 **环境变量** 的代码。 最好从环境变量中读取服务 URL，而不是在函数中对其进行硬编码。 [在本文的后面部分中](#set-up-security-access-for-the-function-app)，您将设置此环境变量的值。 有关环境变量的详细信息，请参阅 [*管理 function app*](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal)。

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ADT_service_URL":::

* 用于保存 HttpClient 实例的静态变量。 创建 HttpClient 的成本相对较高，因此，我们希望避免对每个函数调用执行此操作。

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="HTTP_client":::

* 可以在 Azure Functions 中使用托管标识凭据。
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ManagedIdentityCredential":::

* 在函数内部添加一个本地变量 _DigitalTwinsClient_ ，用于保存 Azure 数字孪生客户端实例。 不要 *使此* 变量在类中是静态的。
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="DigitalTwinsClient":::

* 为 _adtInstanceUrl_ 添加 null 检查，并在 try/catch 块中包装函数逻辑以捕获任何异常。

完成这些更改后，函数代码将类似于以下内容：

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs":::

现在，你的应用程序已编写，可以使用下一部分中的步骤将其发布到 Azure。

## <a name="publish-the-function-app-to-azure"></a>将函数应用发布到 Azure

[!INCLUDE [digital-twins-publish-azure-function.md](../../includes/digital-twins-publish-azure-function.md)]

## <a name="set-up-security-access-for-the-function-app"></a>为 function app 设置安全访问

可以使用 Azure CLI 或 Azure 门户为函数应用设置安全访问权限。 按照以下首选选项的步骤进行操作。

# <a name="cli"></a>[CLI](#tab/cli)

可以在 [Azure Cloud Shell](https://shell.azure.com) 或 [本地 Azure CLI 安装](/cli/azure/install-azure-cli)中运行以下命令。

### <a name="assign-access-role"></a>分配访问角色

前面示例中的函数主干要求向其传递持有者令牌，以便能够通过 Azure 数字孪生进行身份验证。 若要确保传递此持有者令牌，需要设置 [托管服务标识 (MSI) ](../active-directory/managed-identities-azure-resources/overview.md) 权限，才能访问 Azure 数字孪生。 只需对每个 function app 执行一次此操作。

你可以使用函数应用的系统管理的标识为 Azure 数字孪生实例提供 _**Azure 数字孪生数据所有者**_ 角色。 这将为实例中的 function app 权限指定执行数据平面活动。 然后，通过设置环境变量，使 Azure 数字孪生实例的 URL 可供函数访问。

1. 使用以下命令查看函数的系统托管标识的详细信息。 记下输出中的 principalId 字段。

    ```azurecli-interactive 
    az functionapp identity show -g <your-resource-group> -n <your-App-Service-(function-app)-name> 
    ```

    >[!NOTE]
    > 如果结果为空，而不是显示标识的详细信息，请使用以下命令为函数创建新的系统托管标识：
    > 
    >```azurecli-interactive    
    >az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>  
    >```
    >
    > 然后，输出将显示标识的详细信息，包括下一步所需的 _principalId_ 值。 

1. 在以下命令中使用 principalId 值将函数应用的标识分配给 Azure 数字孪生实例的 Azure 数字孪生数据所有者角色。

    ```azurecli-interactive 
    az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Data Owner"
    ```

### <a name="configure-application-settings"></a>配置应用程序设置

最后，通过为你的函数设置 **环境变量** ，使你的 Azure 数字孪生实例的 URL 可供你使用。 有关环境变量的详细信息，请参阅 [*管理 function app*](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal)。 

> [!TIP]
> Azure 数字孪生实例的 URL 是通过将 *https://* 添加到 Azure 数字孪生实例的 *主机名* 的开头来完成的。 若要查看主机名以及实例的所有属性，可以运行 `az dt show --dt-name <your-Azure-Digital-Twins-instance>` 。

```azurecli-interactive 
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-host-name>"
```

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

在 [Azure 门户](https://portal.azure.com/)中完成以下步骤。

### <a name="assign-access-role"></a>分配访问角色

系统分配的托管标识使 Azure 资源能够对云服务进行身份验证 (例如 Azure Key Vault) ，而不将凭据存储在代码中。 启用后，可以通过 Azure 基于角色的访问控制授予所有必要的权限。 此类托管标识的生命周期受此资源的生命周期的限制。 此外，每个资源只能有一个系统分配的托管标识。

1. 在 [Azure 门户](https://portal.azure.com/)中，通过在搜索栏中键入函数应用的名称进行搜索。 从结果中选择应用。 

    :::image type="content" source="media/how-to-create-azure-function/portal-search-for-function-app.png" alt-text="Azure 门户的屏幕截图：正在门户搜索栏中搜索函数应用的名称，并且突出显示搜索结果。":::

1. 在 "函数应用" 页上，选择左侧导航栏中的 " _标识_ " 以使用函数的托管标识。 在 " _系统分配_ " 页上，验证 _状态_ 是否设置为 **"在** (如果不是，则立即设置并 *保存* 更改) 。

    :::image type="content" source="media/how-to-create-azure-function/verify-system-managed-identity.png" alt-text="Azure 门户的屏幕截图：在 function app 的 &quot;标识&quot; 页中，&quot;状态&quot; 选项设置为 &quot;打开&quot;。" lightbox="media/how-to-create-azure-function/verify-system-managed-identity.png":::

1. 选择 " _azure 角色分配_ " 按钮，这将打开 " *azure 角色分配* " 页。

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-1.png" alt-text="Azure 门户的屏幕截图： Azure 功能的 &quot;标识&quot; 页中 &quot;权限&quot; 下的 &quot;Azure 角色分配&quot; 按钮的突出显示。" lightbox="media/how-to-create-azure-function/add-role-assignment-1.png":::

    选择 " _+ 添加角色分配 (预览")_。

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-2.png" alt-text="Azure 门户的屏幕截图： &quot;Azure 角色分配&quot; 页中的 &quot;+ 添加角色分配&quot; (预览) 突出显示。" lightbox="media/how-to-create-azure-function/add-role-assignment-2.png":::

1. 在打开的 " _添加角色分配 (预览")_ 页上，选择以下值：

    * **范围**：资源组
    * **订阅**：选择 Azure 订阅
    * **资源组**：从下拉列表中选择资源组
    * **角色**：从下拉列表中选择 " _Azure 数字孪生数据所有者_ "

    然后，按 " _保存_ " 按钮保存详细信息。

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-3.png" alt-text="Azure 门户：对话框的屏幕截图，用于添加新的角色分配 (预览版) 。作用域、订阅、资源组和角色都有相应的字段。":::

### <a name="configure-application-settings"></a>配置应用程序设置

若要使 Azure 数字孪生实例的 URL 可供函数访问，可以为其设置 **环境变量** 。 有关环境变量的详细信息，请参阅 [*管理 function app*](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal)。 应用程序设置公开为环境变量以访问 Azure 数字孪生实例。 

若要使用实例的 URL 设置环境变量，请首先通过查找 Azure 数字孪生实例的主机名来获取 URL。 在 [Azure 门户](https://portal.azure.com) 搜索栏中搜索实例。 然后，在左侧导航栏中选择 " _概述_ " 以查看 _主机名_。 复制此值。

:::image type="content" source="media/how-to-create-azure-function/instance-host-name.png" alt-text="Azure 门户的屏幕截图： Azure 数字孪生实例的 &quot;概述&quot; 页中，将突出显示 &quot;主机名&quot; 值。":::

现在可以使用以下步骤创建应用程序设置：

1. 在门户搜索栏中搜索函数应用，然后从结果中选择它。

    :::image type="content" source="media/how-to-create-azure-function/portal-search-for-function-app.png" alt-text="Azure 门户的屏幕截图：正在门户搜索栏中搜索函数应用的名称，并且突出显示搜索结果。":::

1. 选择左侧导航栏上的 " _配置_ "。 在 " _应用程序设置_ " 选项卡中，选择 " _+ 新建应用程序设置_"。

    :::image type="content" source="media/how-to-create-azure-function/application-setting.png" alt-text="Azure 门户的屏幕截图：在 function app 的配置页中，将突出显示用于创建新应用程序设置的按钮。":::

1. 在打开的窗口中，使用上面复制的 "主机名" 值创建应用程序设置。
    * **名称**： ADT_SERVICE_URL
    * **值**： https：//{你的-孪生}
    
    选择 _"确定"_ 以创建应用程序设置。
    
    :::image type="content" source="media/how-to-create-azure-function/add-application-setting.png" alt-text="Azure 门户屏幕截图：在 &quot;添加/编辑应用程序设置&quot; 页中填写 &quot;名称&quot; 和 &quot;值&quot; 字段之后，突出显示 &quot;确定&quot; 按钮。":::

1. 创建设置后，应会看到它在 " _应用程序设置_ " 选项卡中显示回去。验证列表中是否出现 *ADT_SERVICE_URL* ，然后选择 " _保存_ " 按钮以保存新的应用程序设置。

    :::image type="content" source="media/how-to-create-azure-function/application-setting-save-details.png" alt-text="Azure 门户的屏幕截图： &quot;应用程序设置&quot; 页，其中突出显示了新的 ADT_SERVICE_URL 设置。还将突出显示 &quot;保存&quot; 按钮。":::

1. 对应用程序设置所做的任何更改都需要重新启动应用程序才能生效，因此，请选择 " _继续_ " 以在出现提示时重新启动应用程序。

    :::image type="content" source="media/how-to-create-azure-function/save-application-setting.png" alt-text="Azure 门户的屏幕截图：会有一条通知：对应用程序设置的任何更改都需要重新启动应用程序。将突出显示 &quot;继续&quot; 按钮。":::

---

## <a name="next-steps"></a>后续步骤

本文介绍如何在 Azure 中设置用于 Azure 数字孪生的函数应用。

接下来，请参阅如何构建基本函数以将 IoT 中心数据引入 Azure 数字孪生：
* [*如何：从 IoT 中心引入遥测数据*](how-to-ingest-iot-hub-data.md)
