---
title: 在 Azure 中设置用于处理数据的函数
titleSuffix: Azure Digital Twins
description: 了解如何在 Azure 中创建可以进行访问且由数字孪生触发的函数。
author: baanders
ms.author: baanders
ms.date: 7/14/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: eaa26b4682dc7e984c0c84575d9e87b5255f6c99
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114386966"
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

若要了解如何使用 Visual Studio 创建函数应用，请参阅[使用 Visual Studio 开发 Azure Functions](../azure-functions/functions-develop-vs.md#publish-to-azure)。

## <a name="write-a-function-that-has-an-event-grid-trigger"></a>编写具有事件网格触发器的函数

可以通过将 SDK 添加到函数应用来编写函数。 函数应用使用[适用于 .NET (C#) 的 Azure 数字孪生 SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) 与 Azure 数字孪生进行交互。 

要使用 SDK，需在项目中加入以下包。 使用 Visual Studio NuGet 包管理器安装包。 或在命令行工具中使用 `dotnet` 添加包。

* [Azure.DigitalTwins.Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core/)
* [Azure.Identity](https://www.nuget.org/packages/Azure.Identity/)
* [System.Net.Http](https://www.nuget.org/packages/System.Net.Http/)
* [Azure.Core](https://www.nuget.org/packages/Azure.Core/)

接下来，在 Visual Studio 解决方案资源管理器中，打开包含示例代码的 .cs 文件。 为包添加以下 `using` 语句。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="Function_dependencies":::

## <a name="add-authentication-code-to-the-function"></a>向函数添加验证码

现在声明类级别变量并添加验证码，使函数可以访问 Azure 数字孪生。 将变量和代码添加到函数中。

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

若要了解如何发布函数应用，请参阅[使用 Visual Studio 开发 Azure Functions](../azure-functions/functions-develop-vs.md#publish-to-azure)。

### <a name="verify-the-publication-of-your-function"></a>验证函数的发布

1. 在 [Azure 门户](https://portal.azure.com/)使用你的凭据登录。
2. 在窗口顶部的搜索框中，搜索函数应用名称，然后选择它。

    :::image type="content" source="media/how-to-create-azure-function/search-function-app.png" alt-text="显示 Azure 门户的屏幕截图。在搜索字段中，输入函数应用名称。" lightbox="media/how-to-create-azure-function/search-function-app.png":::

3. 在打开的“函数应用”页中，选择左侧菜单中的“函数”。 如果成功发布函数，则其名称会出现在列表中。

    > [!Note] 
    > 你可能需要等待几分钟或刷新页面几次，然后你的函数才会出现在已发布函数列表中。

    :::image type="content" source="media/how-to-create-azure-function/view-published-functions.png" alt-text="显示 Azure 门户中已发布的函数的屏幕截图。" lightbox="media/how-to-create-azure-function/view-published-functions.png":::

要访问 Azure 数字孪生，函数应用需要系统托管标识并且该标识具有访问 Azure 数字孪生实例的权限。 你接下来要设置此内容。

## <a name="set-up-security-access-for-the-function-app"></a>为函数应用设置安全访问

[!INCLUDE [digital-twins-configure-function-app.md](../../includes/digital-twins-configure-function-app.md)]

## <a name="next-steps"></a>后续步骤

在本文中，你在 Azure 中设置了用于 Azure 数字孪生的函数应用。 接下来，了解如何基于基本函数[将 IoT 中心数据引入 Azure 数字孪生](how-to-ingest-iot-hub-data.md)。
