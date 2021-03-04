---
title: 在 Azure 中设置用于处理数据的函数
titleSuffix: Azure Digital Twins
description: 请参阅如何在 Azure 中创建可通过数字孪生访问和触发的函数。
author: baanders
ms.author: baanders
ms.date: 8/27/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 2419761c195258c60561e284abf0227b915ed4f6
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102123626"
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

若要使用 SDK，需要在项目中包含以下包。 可以使用 Visual Studio 的 NuGet 包管理器安装包，也可以 `dotnet` 在命令行工具中使用添加包。 请按照以下步骤操作，了解首选方法。

**选项1。使用 Visual Studio 包管理器添加包：**
    
右键选择项目，然后从列表中选择 " _管理 NuGet 包_ "。 然后，在打开的窗口中，选择 " _浏览_ " 选项卡，然后搜索下列包。 选择 " _安装_ 并 _接受_ 许可协议" 以安装包。

* `Azure.DigitalTwins.Core`
* `Azure.Identity`
* `System.Net.Http`
* `Azure.Core.Pipeline`

**选项2。使用 `dotnet` 命令行工具添加包：**

或者，你可以 `dotnet add` 在命令行工具中使用以下命令：

```cmd/sh
dotnet add package Azure.DigitalTwins.Core
dotnet add package Azure.Identity
dotnet add package System.Net.Http
dotnet add package Azure.Core.Pipeline
```

接下来，在 Visual Studio 解决方案资源管理器中，打开 _Function1.cs_ 文件，其中包含示例代码，并将以下 `using` 语句添加到函数。 

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

### <a name="option-1-set-up-security-access-for-the-function-app-using-cli"></a>选项1：使用 CLI 为函数应用设置安全访问权限

前面示例中的函数主干要求向其传递持有者令牌，以便能够通过 Azure 数字孪生进行身份验证。 若要确保传递此持有者令牌，需要为 function app 设置 [托管服务标识 (MSI) ](../active-directory/managed-identities-azure-resources/overview.md) 。 只需对每个 function app 执行一次此操作。

你可以创建系统管理的标识，并将 function app 的标识分配给 Azure 数字孪生实例的 _**Azure 数字孪生数据所有者**_ 角色。 这将为实例中的 function app 权限指定执行数据平面活动。 然后，通过设置环境变量，使 Azure 数字孪生实例的 URL 可供函数访问。

使用 [Azure Cloud Shell](https://shell.azure.com) 运行命令。

使用以下命令创建系统管理的标识。 记下输出中的 principalId 字段。

```azurecli-interactive 
az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>   
```
在以下命令中使用 principalId 值将函数应用的标识分配给 Azure 数字孪生实例的 Azure 数字孪生数据所有者角色。

```azurecli-interactive 
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Data Owner"
```
最后，通过为你的函数设置 **环境变量** ，使你的 Azure 数字孪生实例的 URL 可供你使用。 有关环境变量的详细信息，请参阅 [*管理 function app*](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal)。 

> [!TIP]
> Azure 数字孪生实例的 URL 是通过将 *https://* 添加到 Azure 数字孪生实例的 *主机名* 的开头来完成的。 若要查看主机名以及实例的所有属性，可以运行 `az dt show --dt-name <your-Azure-Digital-Twins-instance>` 。

```azurecli-interactive 
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-hostname>"
```
### <a name="option-2-set-up-security-access-for-the-function-app-using-azure-portal"></a>选项2：使用 Azure 门户设置函数应用的安全访问权限

系统分配的托管标识使 Azure 资源能够对云服务进行身份验证 (例如 Azure Key Vault) ，而不将凭据存储在代码中。 启用后，可以通过 Azure 基于角色的访问权限授予所有必要的权限。 此类托管标识的生命周期受此资源的生命周期的限制。 此外，每个资源 (例如，虚拟机) 只能有一个系统分配的托管标识。

在 [Azure 门户](https://portal.azure.com/)中，在搜索栏中搜索 " _function app_ "，其中包含之前创建的函数应用名称。 从列表中选择 *Function App* 。 

:::image type="content" source="media/how-to-create-azure-function/portal-search-for-function-app.png" alt-text="Azure 门户的屏幕截图：正在门户搜索栏中搜索函数应用的名称，并且突出显示搜索结果。":::

在 "函数应用" 窗口的左侧导航栏中选择 " _标识_ "，以启用托管标识。
在 " _系统分配_ " 选项卡下，将 _状态_ 切换到 "打开" 并 _保存_ 。 你将看到一个弹出窗口，用于 _启用系统分配的托管标识_。
选择 _"是"_ 按钮。 

:::image type="content" source="media/how-to-create-azure-function/enable-system-managed-identity.png" alt-text="Azure 门户的屏幕截图：在 function app 的 &quot;标识&quot; 页中，用于启用系统分配的托管标识的选项将设置为 &quot;是&quot;。Status 选项设置为 On。":::

可以在通知中验证函数已成功注册到 Azure Active Directory。

:::image type="content" source="media/how-to-create-azure-function/notifications-enable-managed-identity.png" alt-text="Azure 门户的屏幕截图：在门户顶部栏中选择 &quot;钟形&quot; 图标的通知列表。通知用户已启用系统分配的托管标识。":::

另请注意 "_标识_" 页上显示的 **对象 ID** ，它将在下一部分中使用。

:::image type="content" source="media/how-to-create-azure-function/object-id.png" alt-text="Azure 门户的屏幕截图： Azure 函数的标识页上的 &quot;对象 ID&quot; 字段周围突出显示的内容。":::

### <a name="assign-access-roles-using-azure-portal"></a>使用 Azure 门户分配访问角色

选择 " _azure 角色分配_ " 按钮，这将打开 " *azure 角色分配* " 页。 然后，选择 " _+ 添加角色分配 (预览")_。

:::image type="content" source="media/how-to-create-azure-function/add-role-assignments.png" alt-text="Azure 门户的屏幕截图： Azure 功能的 &quot;标识&quot; 页中 &quot;权限&quot; 下的 &quot;Azure 角色分配&quot; 按钮的突出显示。":::

在打开的 " _添加角色分配 (预览")_ 页上，选择：

* _范围_：资源组
* _订阅_：选择 Azure 订阅
* _资源组_：从下拉列表中选择资源组
* _角色_：从下拉列表中选择 " _Azure 数字孪生数据所有者_ "

然后，按 " _保存_ " 按钮保存详细信息。

:::image type="content" source="media/how-to-create-azure-function/add-role-assignment.png" alt-text="Azure 门户：对话框的屏幕截图，用于添加新的角色分配 (预览版) 。作用域、订阅、资源组和角色都有相应的字段。":::

### <a name="configure-application-settings-using-azure-portal"></a>使用 Azure 门户配置应用程序设置

若要使 Azure 数字孪生实例的 URL 可供函数访问，可以为其设置 **环境变量** 。 有关环境变量的详细信息，请参阅 [*管理 function app*](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal)。 应用程序设置公开为环境变量以访问 Azure 数字孪生实例。 

若要使用实例的 URL 设置环境变量，请首先通过查找 Azure 数字孪生实例的主机名来获取 URL。 在 [Azure 门户](https://portal.azure.com) 搜索栏中搜索实例。 然后，在左侧导航栏中选择 " _概述_ " 以查看 _主机名_。 复制此值。

:::image type="content" source="media/how-to-create-azure-function/adt-hostname.png" alt-text="Azure 门户的屏幕截图： Azure 数字孪生实例的 &quot;概述&quot; 页中，将突出显示 &quot;主机名&quot; 值。":::

你现在可以按照以下步骤创建应用程序设置：

1. 在门户搜索栏中搜索函数应用，然后从结果中选择它
1. 选择左侧导航栏上的 " _配置_ " 可创建新的应用程序设置
1. 在 "_应用程序设置_" 选项卡中，选择 " _+ 新建应用程序设置_"

:::image type="content" source="media/how-to-create-azure-function/portal-search-for-function-app.png" alt-text="Azure 门户的屏幕截图：正在门户搜索栏中搜索函数应用的名称，并且突出显示搜索结果。":::

:::image type="content" source="media/how-to-create-azure-function/application-setting.png" alt-text="Azure 门户的屏幕截图：在 function app 的配置页中，将突出显示用于创建新应用程序设置的按钮。":::

在打开的窗口中，使用上面复制的 "主机名" 值创建应用程序设置。
* **名称**： ADT_SERVICE_URL
* **值**： https：//{你的-孪生}

选择 _"确定"_ 以创建应用程序设置。

:::image type="content" source="media/how-to-create-azure-function/add-application-setting.png" alt-text="Azure 门户屏幕截图：在 &quot;添加/编辑应用程序设置&quot; 页中填写 &quot;名称&quot; 和 &quot;值&quot; 字段之后，突出显示 &quot;确定&quot; 按钮。":::

可以在 " _名称_ " 字段下查看应用程序的应用程序设置。 然后，通过选择 " _保存_ " 按钮保存应用程序设置。

:::image type="content" source="media/how-to-create-azure-function/application-setting-save-details.png" alt-text="Azure 门户的屏幕截图： &quot;应用程序设置&quot; 页，其中突出显示了新的 ADT_SERVICE_URL 设置。还将突出显示 &quot;保存&quot; 按钮。":::

对应用程序设置所做的任何更改都需要重新启动应用程序才能生效。 选择 " _继续_ " 以重新启动应用程序。

:::image type="content" source="media/how-to-create-azure-function/save-application-setting.png" alt-text="Azure 门户的屏幕截图：会有一条通知：对应用程序设置的任何更改都需要重新启动应用程序。将突出显示 &quot;继续&quot; 按钮。":::

可以通过选择 " _通知_ " 图标来查看更新应用程序设置。 如果未创建应用程序设置，则可以按照上述过程重试添加应用程序设置。

:::image type="content" source="media/how-to-create-azure-function/notifications-update-web-app-settings.png" alt-text="Azure 门户的屏幕截图：在门户顶部栏中选择 &quot;钟形&quot; 图标的通知列表。通知已成功更新 web 应用设置。":::

## <a name="next-steps"></a>后续步骤

本文介绍如何在 Azure 中设置用于 Azure 数字孪生的函数应用。

接下来，请参阅如何构建基本函数以将 IoT 中心数据引入 Azure 数字孪生：
* [*如何：从 IoT 中心引入遥测数据*](how-to-ingest-iot-hub-data.md)
