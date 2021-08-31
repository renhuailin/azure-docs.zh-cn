---
title: 从逻辑应用工作流调用 Azure Functions
description: 通过创建和调用 Azure Functions，在使用 Azure 逻辑应用创建的工作流中运行自己的代码。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 06/14/2021
ms.custom: devx-track-js
ms.openlocfilehash: b04ce478f214358c6cd55a35ebfe05f0863e053e
ms.sourcegitcommit: 5a27d9ba530aee0e563a1b0159241078e8c7c1e4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2021
ms.locfileid: "112422672"
---
# <a name="create-and-run-your-own-code-from-workflows-in-azure-logic-apps-by-using-azure-functions"></a>使用 Azure Functions 从 Azure 逻辑应用中的工作流创建和运行你自己的代码

若要运行在逻辑应用工作流中执行特定作业的代码，可以使用 [Azure Functions](../azure-functions/functions-overview.md) 创建函数。 该服务有助于创建 Node.js、C# 和 F# 函数，使你无需为运行代码而构建完整的应用或基础结构。 还可以[从 Azure Functions 内部调用逻辑应用工作流](#call-logic-app)。 Azure Functions 在云中提供无服务器计算，且对执行某些任务非常有用，例如：

* 通过 Node.js 或 C# 函数扩展逻辑应用的行为。
* 在逻辑应用工作流中执行计算。
* 在逻辑应用工作流中应用高级格式设置或计算字段。

若要在不使用 Azure Functions 的情况下运行代码片段，请参阅如何[添加和运行内联代码](../logic-apps/logic-apps-add-run-inline-code.md)。

> [!NOTE]
> Azure 逻辑应用不支持使用启用了部署槽的 Azure Functions。 尽管此方案有时可能会起作用，但此行为是不可预测的，并且可能会在工作流尝试调用 Azure 函数时导致授权问题。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* 一个函数应用，是在 Azure Functions 中创建的函数以及你创建的函数的容器。

  若没有函数应用，请先[创建函数应用](../azure-functions/functions-get-started.md)。 然后，可以在 Azure 门户中在逻辑应用外部创建函数，也可以在工作流设计器中在[逻辑应用内部](#create-function-designer)创建函数。

* 使用逻辑应用时，同样的要求适用于函数应用和函数，不管它们是现有的还是全新的：

  * 函数应用和逻辑应用必须使用同一 Azure 订阅。

  * 新的函数应用必须使用 .NET 或 JavaScript 作为运行时堆栈。 将新函数添加到现有的函数应用时，可以选择 C# 或 JavaScript。

  * 你的函数使用 **HTTP 触发器** 模板。

    此 HTTP 触发器模板可从逻辑应用接受具有 `application/json` 类型的内容。 当你向逻辑应用添加函数时，工作流设计器会显示 Azure 订阅内基于此模板创建的自定义函数。

  * 函数不使用自定义路由，除非你定义了 [OpenAPI 定义](../azure-functions/functions-openapi-definition.md)（旧称为“[Swagger 文件](https://swagger.io/)”）。

  * 如果你已为函数定义了 OpenAPI 定义，工作流设计器会在你使用函数参数时提供更丰富的体验。 在逻辑应用查找并访问具有 OpenAPI 定义的函数之前，请先[按以下步骤设置函数应用](#function-swagger)。

* 要在其中添加函数的逻辑应用（加入[触发器](../logic-apps/logic-apps-overview.md#logic-app-concepts)是逻辑应用中的第一步）

  在添加运行函数的操作之前，必须使用触发器启动逻辑应用。 如果不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用](../logic-apps/logic-apps-overview.md)和[快速入门：创建第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

<a name="function-swagger"></a>

## <a name="find-functions-that-have-openapi-descriptions"></a>查找包含 OpenAPI 说明的函数

为了在使用函数参数时在工作流设计器中获得更丰富的体验，请为函数[生成 OpenAPI 定义](../azure-functions/functions-openapi-definition.md)（旧称为 [Swagger 文件](https://swagger.io/)）。 若要设置函数应用以使其可查找和使用具备 Swagger 描述的函数，请按照以下步骤操作：

1. 确保函数应用正在运行。

1. 按照以下步骤，在函数应用中，设置[跨源资源共享 (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) 以便允许所有来源：

   1. 从“函数应用”列表中选择自己的函数应用。 在右侧窗格中，选择“平台功能” > “CORS”。

      ![选择函数应用 >“平台功能”>“CORS”](./media/logic-apps-azure-functions/function-platform-features-cors.png)

   1. 在“CORS”下，添加星号 (`*`) 通配符，但删除列表中的其他所有源，然后选择“保存”。

      ![将 CORS 设为通配符“*”](./media/logic-apps-azure-functions/function-platform-features-cors-origins.png)

## <a name="access-property-values-inside-http-requests"></a>访问 HTTP 请求中的属性值

Webhook 函数能接受 HTTP 请求作为输入，并将这些请求传递至其他函数。 例如，尽管逻辑应用具有[转换 DateTime 值的函数](../logic-apps/workflow-definition-language-functions-reference.md)，但此基本示例 JavaScript 函数演示了如何访问传递给函数的请求对象中的属性，并对该属性值执行操作。 为访问对象内的属性，此示例使用[点 (.) 运算符](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/Property_accessors)：

```javascript
function convertToDateString(request, response){
   var data = request.body;
   response = {
      body: data.date.ToDateString();
   }
}
```

下面是此函数内部发生的情况：

1. 该函数创建 `data` 变量并将 `request` 对象内的 `body` 对象分配给该变量。 该函数使用点 (.) 运算符引用 `request` 对象内的 `body` 对象：

   ```javascript
   var data = request.body;
   ```

1. 该函数现在可以通过 `data` 变量访问 `date` 属性，并通过调用 `ToDateString()` 函数将该属性值从 DateTime 类型转换为 DateString 类型。 该函数还通过函数响应中的 `body` 属性返回结果：

   ```javascript
   body: data.date.ToDateString();
   ```

现已在 Azure 中创建了自己的函数，请按步骤[将函数添加到逻辑应用](#add-function-logic-app)。

<a name="create-function-designer"></a>

## <a name="create-functions-inside-logic-apps"></a>在逻辑应用内部创建函数

使用工作流设计器中的内置 Azure Functions 操作，可以直接通过逻辑应用的工作流创建函数，但这种方法只能用于使用 JavaScript 编写的函数。 对于其他语言，可以通过 Azure 门户中的 Azure Functions 体验来创建函数。 有关详细信息，请参阅[在 Azure 门户中创建首个函数](../azure-functions/functions-get-started.md)。

不过，必须已经有函数应用（即函数容器），然后才能在 Azure 中创建函数。 若没有函数应用，请先创建一个。 请参阅[在 Azure 门户中创建第一个函数](../azure-functions/functions-get-started.md)。

1. 在 [Azure 门户](https://portal.azure.com)的设计器中打开逻辑应用。

1. 按照适用于自身方案的步骤，创建并添加函数：

   * 如果处于逻辑应用工作流最后一步，请选择“新建步骤”。

   * 如果介于逻辑应用工作流中现有步骤之间，请将鼠标移至箭头上，选择加号 (+)，然后选择“添加操作”。

1. 在搜索框中输入 `azure functions`。 在操作列表中，选择“选择 Azure 函数”操作，例如：

   ![在 Azure 门户中查找函数。](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. 从函数应用列表中选择自己的函数应用。 在操作列表打开后，选择此操作：“新建函数”。

   ![选择函数应用](./media/logic-apps-azure-functions/select-function-app-create-function.png)

1. 在函数定义编辑器中定义函数：

   1. 在“函数名称”框中提供函数的名称。

   1. 在“代码”框中，将代码添加到函数模板中，包括你希望在函数运行结束后返回给逻辑应用的响应和有效负载。 完成操作后，选择“创建”。

   例如：

   ![定义函数](./media/logic-apps-azure-functions/add-code-function-definition.png)

   模板代码中的 `context` 对象表示逻辑应用通过后续步骤中“请求正文”字段发送的消息。 要从函数内访问 `context` 对象的属性，请使用如下语法：

   `context.body.<property-name>`

   例如，要引用 `context` 对象内的 `content` 属性，请使用如下语法：

   `context.body.content`

   此模板代码还包含一个 `input` 变量，此变量存储来自 `data` 参数的值，因此函数可对该值执行操作。 在 JavaScript 函数内，`data` 变量也是 `context.body` 的一种快捷方式。

   > [!NOTE]
   > 此处的 `body` 属性适用于 `context` 对象，但与来自操作输出的 Body 令牌不同，你可能也会希望将后者传递到函数。

1. 在“请求正文”框中，提供函数的输入，其格式必须为 JavaScript 对象表示法 (JSON) 对象。

   此输入是逻辑应用发送到函数的上下文对象或消息。 点击“请求正文”字段时，界面会显示动态内容列表，以便你可以为先前步骤中的输出选择令牌。 此示例指定上下文有效负载包含名为 `content` 的属性，其中有来自电子邮件触发器的“发件人”标记的值。

   ![“请求正文”示例 - 上下文对象有效负载](./media/logic-apps-azure-functions/function-request-body-example.png)

   此处的上下文对象没有强制转换为字符串，因此对象的内容被直接添加到 JSON 有效负载中。 但是，如果该上下文对象不是传递字符串、JSON 对象或 JSON 数组的 JSON 令牌，则会出现错误。 因此，如果本示例使用了 **Received Time** 标记，则可通过添加双引号将此上下文对象强制转换为字符串。

   ![将对象强制转换为字符串](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. 若要指定其他详细信息，例如要使用的方法、请求标头、查询参数或身份验证，请打开“添加新参数”列表，然后选择所需选项。 身份验证的选项因所选函数而异。 请参阅[为函数启用身份验证](#enable-authentication-functions)。

<a name="add-function-logic-app"></a>

## <a name="add-existing-functions-to-logic-apps"></a>将现有函数添加至逻辑应用

若要从逻辑应用调用现有函数，可以添加函数，具体方法与在工作流设计器中执行的任何其他操作一样。

1. 在 [Azure 门户](https://portal.azure.com)的设计器中打开逻辑应用。

1. 在要添加函数的步骤下，选择“新建步骤”。

1. 在“选择操作”下的搜索框中输入 `azure functions`。 在操作列表中，选择“选择 Azure 函数”操作，例如：

   ![在 Azure 中查找函数。](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. 从函数应用列表中选择自己的函数应用。 在显示的函数列表中选择函数。

   ![选择函数应用和函数](./media/logic-apps-azure-functions/select-function-app-existing-function.png)

   对于具备 API 定义（Swagger 描述）的函数以及那些[设置为可供逻辑应用查找和访问](#function-swagger)的函数，可以选择“Swagger 操作”。

   ![依次选择函数应用、“Swagger 操作”和函数](./media/logic-apps-azure-functions/select-function-app-existing-function-swagger.png)

1. 在“请求正文”框中，提供函数的输入，其格式必须为 JavaScript 对象表示法 (JSON) 对象。

   此输入是逻辑应用发送到函数的上下文对象或消息。 单击“请求正文”字段时，会看到动态内容列表，这样就可以为前面步骤的输出选择标记。 本示例指定上下文有效负载包含一个名为 `content` 的属性，该属性具有来自电子邮件触发器的 **From** 标记的值。

   ![“请求正文”示例 - 上下文对象有效负载](./media/logic-apps-azure-functions/function-request-body-example.png)

   此处的上下文对象没有强制转换为字符串，因此对象的内容被直接添加到 JSON 有效负载中。 但是，如果该上下文对象不是传递字符串、JSON 对象或 JSON 数组的 JSON 令牌，则会出现错误。 因此，假如本示例使用 Received Time 令牌，则可通过添加双引号将此上下文对象强制转换为字符串：

   ![将对象强制转换为字符串](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. 若要指定其他详细信息，例如要使用的方法、请求标头、查询参数或身份验证，请打开“添加新参数”列表，然后选择所需选项。 身份验证的选项因所选函数而异。 请参阅[在函数中启用身份验证](#enable-authentication-functions)。

<a name="call-logic-app"></a>

## <a name="call-logic-apps-from-functions"></a>从函数调用逻辑应用

若要从函数内部触发逻辑应用，则必须使用提供可调用终结点的触发器启动该逻辑应用。 例如，可使用 HTTP 触发器、请求触发器、Azure 队列触发器或事件网格触发器启动逻辑应用   。 在函数内，向触发器的 URL 发送一个 HTTP POST 请求，并加入需要逻辑应用处理的有效负载。 有关详细信息，请参阅[调用、触发器或嵌套逻辑应用](../logic-apps/logic-apps-http-endpoint.md)。

<a name="enable-authentication-functions"></a>

## <a name="enable-authentication-for-functions"></a>为函数启用身份验证

为了轻松验证对 Azure Active Directory (Azure AD) 保护的资源的访问，而无需登录并提供凭据或机密，逻辑应用可以使用[托管标识](../active-directory/managed-identities-azure-resources/overview.md)（旧称为“托管服务标识 (MSI)”）。 由于无需提供或轮换机密，因此 Azure 会为你管理此标识，并且会帮助保护凭据。 详细了解[支持 Azure AD 身份验证的托管标识的 Azure 服务](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)。

如果你将逻辑应用设置为使用系统分配标识或手动创建的用户分配标识，那么逻辑应用中的函数也可以使用同一标识进行身份验证。 若要详细了解逻辑应用中函数的身份验证支持，请参阅[向出站调用添加身份验证](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)。

若要设置托管标识并用于函数，请按照以下步骤操作：

1. 在逻辑应用上启用托管标识，并设置此标识对目标资源的访问权限。 请参阅[在 Azure 逻辑应用中使用托管标识验证对 Azure 资源的访问](../logic-apps/create-managed-service-identity.md)。

1. 若要在函数和函数应用中启用身份验证，请按照以下步骤操作：

   * [在函数中设置匿名身份验证](#set-authentication-function-app)
   * [在函数应用中设置 Azure AD 身份验证](#set-azure-ad-authentication)

<a name="set-authentication-function-app"></a>

### <a name="set-up-anonymous-authentication-in-your-function"></a>在函数中设置匿名身份验证

若要在函数中使用逻辑应用的托管标识，则必须将函数的身份验证级别设置为“匿名”。 否则，逻辑应用会抛出“BadRequest”错误。

1. 在 [Azure 门户](https://portal.azure.com)中，找到并选择你的函数应用。 这些步骤使用“FabrikamFunctionApp”作为示例函数应用。

1. 在函数应用窗格中选择“平台功能”。 在“开发工具”下，选择“高级工具(Kudu)”。 

   ![打开 Kudu 高级工具](./media/logic-apps-azure-functions/open-advanced-tools-kudu.png)

1. 在 Kudu 网站的标题栏上，从“调试控制台”菜单中选择“CMD”。 

   ![在调试控制台菜单中选择“CMD”选项](./media/logic-apps-azure-functions/open-debug-console-kudu.png)

1. 下一页出现后，从文件夹列表中选择“站点” > “wwwroot” > “<你的函数>”。  这些步骤使用“FabrikamAzureFunction”作为示例函数。

   ![选择“站点”>“wwwroot”> 你的函数](./media/logic-apps-azure-functions/select-site-wwwroot-function-folder.png)

1. 打开 `function.json` 文件进行编辑。

   ![单击“function.json”文件对应的编辑图标](./media/logic-apps-azure-functions/edit-function-json-file.png)

1. 在 `bindings` 对象中，检查 `authLevel` 属性是否存在。 如果该属性存在，请将属性值设置为 `anonymous`。 否则，请添加该属性并设置值。

   ![添加“authLevel”属性并将其设置为“anonymous”](./media/logic-apps-azure-functions/set-authentication-level-function-app.png)

1. 完成后，保存设置，然后转到下一部分。

<a name="set-azure-ad-authentication"></a>

### <a name="set-up-azure-ad-authentication-for-your-function-app"></a>为函数应用设置 Azure AD 身份验证

在开始此任务之前，请找到以下值并将其复制到某个位置，供稍后使用：

* 为表示逻辑应用的系统分配标识生成的对象 ID

  * 若要生成此对象 ID，请[启用逻辑应用的系统分配标识](../logic-apps/create-managed-service-identity.md#azure-portal-system-logic-app)。

  * 否则，若要查找此对象 ID，请在设计器中打开逻辑应用。 在逻辑应用菜单中的“设置”下，选择“标识” > “系统分配”。  

* Azure Active Directory (Azure AD) 中租户的目录 ID

  若要获取租户的目录 ID，可以运行 [`Get-AzureAccount`](/powershell/module/servicemanagement/azure.service/get-azureaccount) PowerShell 命令。 或者，在 Azure 门户中执行以下步骤：

  1. 在 [Azure 门户](https://portal.azure.com)中，找到并选择你的函数应用。

  1. 查找并选择你的 Azure AD 租户。 这些步骤使用“Fabrikam”作为示例租户。

  1. 在租户菜单中的“管理”下，选择“属性”。 

  1. 复制租户的目录 ID，并保存该 ID 供稍后使用。

     ![找到并复制 Azure AD 租户的目录 ID](./media/logic-apps-azure-functions/azure-active-directory-tenant-id.png)

* 要访问的目标资源的资源 ID

  * 若要查找这些资源 ID，请查看[支持 Azure AD 的 Azure 服务](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)。

  > [!IMPORTANT]
  > 此资源 ID 必须与 Azure AD 所需的值完全匹配，包括所有必需的尾部斜杠。

  稍后在[设置函数操作以使用系统分配的标识](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity)时，也要在“受众”属性中使用此资源 ID。

现已准备好为函数应用设置 Azure AD 身份验证。

1. 在 [Azure 门户](https://portal.azure.com)中，找到并选择你的函数应用。

1. 在函数应用窗格中选择“平台功能”。 在“网络”下，选择“身份验证/授权”。 

   ![查看身份验证和授权设置](./media/logic-apps-azure-functions/view-authentication-authorization-settings.png)

1. 将“应用服务身份验证”设置更改为“打开”。  在“请求未经身份验证时需执行的操作”列表中，选择“使用 Azure Active Directory 登录” 。 在“验证提供程序”下，选择“Azure Active Directory” 。

   ![打开使用 Azure AD 进行身份验证](./media/logic-apps-azure-functions/turn-on-authentication-azure-active-directory.png)

1. 在“Azure Active Directory 设置”窗格中执行以下步骤：

   1. 将“管理模式”设置为“高级”。

   1. 在“客户端 ID”属性中，输入逻辑应用的系统分配标识的对象 ID。

   1. 在“颁发者 URL”属性中输入 `https://sts.windows.net/` URL，并追加 Azure AD 租户的目录 ID。

      `https://sts.windows.net/<Azure-AD-tenant-directory-ID>`

   1. 在“允许的令牌受众”属性中，输入要访问的目标资源的资源 ID。

      稍后在[设置函数操作以使用系统分配的标识](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity)时，需在“受众”属性中使用此同一个资源 ID 值。

   现在，你的版本如以下示例所示：

   ![Azure Active Directory 身份验证设置](./media/logic-apps-azure-functions/azure-active-directory-authentication-settings.png)

1. 完成后，请选择“确定”。

1. 返回到设计器，并按照[使用托管标识验证访问的步骤](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity)操作。

## <a name="next-steps"></a>后续步骤

* 了解[逻辑应用连接器](../connectors/apis-list.md)
