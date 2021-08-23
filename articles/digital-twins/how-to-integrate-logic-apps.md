---
title: 与逻辑应用集成
titleSuffix: Azure Digital Twins
description: 请参阅如何使用自定义连接器将逻辑应用连接到 Azure 数字孪生
author: baanders
ms.author: baanders
ms.date: 9/11/2020
ms.topic: how-to
ms.service: digital-twins
ms.reviewer: baanders
ms.openlocfilehash: 3fbd9016bcbfa83574d894af7ca728b863f54344
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93129314"
---
# <a name="integrate-with-logic-apps-using-a-custom-connector"></a>使用自定义连接器与逻辑应用集成

[Azure 逻辑应用](../logic-apps/logic-apps-overview.md) 是一种云服务，可帮助你跨应用和服务自动执行工作流。 通过将逻辑应用连接到 Azure 数字孪生 Api，你可以围绕 Azure 数字孪生及其数据创建此类自动流程。

Azure 数字孪生当前没有针对逻辑应用的已认证 (预建) 连接器。 相反，将逻辑应用与 Azure 数字孪生配合使用的当前过程是使用已修改为可用于逻辑应用的 [自定义 Azure 数字孪生 Swagger](/samples/azure-samples/digital-twins-custom-swaggers/azure-digital-twins-custom-swaggers/)创建 [**自定义逻辑应用连接器**](../logic-apps/custom-connector-overview.md)。

> [!NOTE]
> 上面链接的自定义 Swagger 示例包含多个版本的 Swagger。 最新版本将在具有最近日期的子文件夹中找到，但仍支持示例中包含的较早版本。

本文介绍如何使用 [Azure 门户](https://portal.azure.com)**创建自定义连接器**，此连接器可用于将逻辑应用连接到 Azure 数字孪生实例。 然后，将 **创建一个逻辑应用** ，该应用使用此连接作为示例方案，在此方案中，计时器触发的事件会自动更新 Azure 数字孪生实例中的非整数。 

## <a name="prerequisites"></a>先决条件

如果还没有 Azure 订阅，可以在开始前 **创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)** 。
利用此帐户登录到 [Azure 门户](https://portal.azure.com) 。 

还需要在安装先决条件过程中完成以下各项。 本部分的其余部分将指导你完成以下步骤：
- 设置 Azure 数字孪生实例
- 添加数字输出

### <a name="set-up-azure-digital-twins-instance"></a>设置 Azure 数字孪生实例

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

### <a name="add-a-digital-twin"></a>添加数字输出

本文使用逻辑应用来更新 Azure 数字孪生实例中的克隆。 若要继续，你应在实例中至少添加一个 "双子入"。 

可以使用 [DigitalTwins api](/rest/api/digital-twins/dataplane/twins)、 [.Net (c # ) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet-preview&preserve-view=true)或 [Azure 数字孪生 CLI](how-to-use-cli.md)添加孪生。 有关如何使用这些方法创建孪生的详细步骤，请参阅 [*操作方法：管理数字孪生*](how-to-manage-twin.md)。

你将需要已创建的实例中的克隆的克隆 **_ID_** 。

## <a name="set-up-app-registration"></a>设置应用注册

[!INCLUDE [digital-twins-prereq-registration.md](../../includes/digital-twins-prereq-registration.md)]

### <a name="get-app-registration-client-secret"></a>获取应用注册客户端密钥

还需要创建 Azure AD 应用注册的 **_客户端机密_** 。 为此，请导航到 Azure 门户中的 " [应用注册](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) " 页 (你可以使用此链接，或在门户搜索栏) 中查找它。 从列表中选择在上一部分中创建的注册，以便打开其详细信息。 

从注册的菜单中点击 *证书和机密* ，然后选择 " *+ 新建客户端密钥*"。

:::image type="content" source="media/how-to-integrate-logic-apps/client-secret.png" alt-text="Azure AD 应用注册的门户视图。资源菜单中的 &quot;证书和机密&quot; 周围有一个突出显示，并在页面上突出显示了 &quot;新建客户端密码&quot;":::

输入想要用于 *说明* 和 *过期* 的任何值，然后单击 " *添加*"。

:::image type="content" source="media/how-to-integrate-logic-apps/add-client-secret.png" alt-text="添加客户端密码":::

现在，验证客户端密码在 " _证书 & 机密_ " 页上显示 " _过期_ " 和 " _值_ " 字段。 记下它的 _值_ 以便以后使用 (您还可以使用 "复制" 图标将其复制到剪贴板) 

:::image type="content" source="media/how-to-integrate-logic-apps/client-secret-value.png" alt-text="复制客户端密码值":::

## <a name="create-custom-logic-apps-connector"></a>创建自定义逻辑应用连接器

现在，你已准备好为 Azure 数字孪生 Api 创建 [自定义逻辑应用连接器](../logic-apps/custom-connector-overview.md) 。 完成此操作后，在下一部分中创建逻辑应用时，可以将 Azure 数字孪生挂钩。

导航到 Azure 门户中的 " [逻辑应用自定义连接器](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Web%2FcustomApis) " 页面 (你可以使用此链接，或在门户搜索栏) 中搜索它。 单击 " *+ 添加*"。

:::image type="content" source="media/how-to-integrate-logic-apps/logic-apps-custom-connector.png" alt-text="Azure 门户中的 &quot;逻辑应用自定义连接器&quot; 页。&quot;添加&quot; 按钮周围突出显示":::

在随后的 " *创建逻辑应用自定义连接器* " 页面中，选择你的订阅和资源组，以及新连接器的名称和部署位置。 点击 *评审 + 创建*。 

:::image type="content" source="media/how-to-integrate-logic-apps/create-logic-apps-custom-connector.png" alt-text="Azure 门户中的 &quot;创建逻辑应用自定义连接器&quot; 页面。":::

这会将你转到 "查看" 和 " *创建* " 选项卡，你可以在 *底部点击 "创建"* 来创建资源。

:::image type="content" source="media/how-to-integrate-logic-apps/review-logic-apps-custom-connector.png" alt-text="Azure 门户中 &quot;查看逻辑应用自定义连接器&quot; 页的 &quot;查看和创建&quot; 选项卡。&quot;创建&quot; 按钮周围突出显示":::

你将转到连接器的部署页。 完成部署后，单击 " *前往资源* " 按钮，在门户中查看连接器的详细信息。

### <a name="configure-connector-for-azure-digital-twins"></a>为 Azure 数字孪生配置连接器

接下来，将创建的连接器配置为连接到 Azure 数字孪生。

首先，下载已修改为可用于逻辑应用的自定义 Azure 数字孪生 Swagger。 通过点击 "*下载 ZIP* " 按钮，从此 [**链接**](/samples/azure-samples/digital-twins-custom-swaggers/azure-digital-twins-custom-swaggers/)下载 **Azure 数字孪生 custom swagger (逻辑应用连接器)** 示例。 导航到下载的 *Azure_Digital_Twins_custom_Swaggers__Logic_Apps_connector_.zip* 文件夹并将其解压缩。 

本教程的自定义 Swagger 位于 _* * Azure_Digital_Twins_custom_Swaggers__Logic_Apps_connector_\LogicApps **_ 文件夹中。此文件夹包含名为 " *稳定* " 和 " *预览*" 的子文件夹，这两者都包含按日期组织的不同版本的 Swagger。具有最近日期的文件夹将包含 Swagger 的最新副本。无论选择哪种版本，Swagger 文件** 在 * * _ 上都命名为 _digitaltwins.js。

> [!NOTE]
> 除非使用的是预览功能，否则通常建议使用最新的 Swagger *稳定* 版本。 不过，还会支持更早版本的 Swagger 和预览版本。 

接下来，请在 [Azure 门户](https://portal.azure.com) 中中转到连接器的 "概述" 页，然后单击 " *编辑*"。

:::image type="content" source="media/how-to-integrate-logic-apps/edit-connector.png" alt-text="在上一步中创建的连接器的 &quot;概述&quot; 页。在 &quot;编辑&quot; 按钮周围突出显示":::

在下面的 " *编辑逻辑应用自定义连接器* " 页面中，配置以下信息：
* **自定义连接器**
    - API 终结点： REST (保留默认值) 
    - 导入模式： OpenAPI 文件 (保留默认值) 
    - 文件：这将是你先前下载的自定义 Swagger 文件。 命中 *导入*，在计算机上查找 (*Azure_Digital_Twins_custom_Swaggers__Logic_Apps_connector_ \LogicApps \...\digitaltwins.js* ，并单击) " *打开*"。
* **常规信息**
    - 图标：上传喜欢的图标
    - 图标背景色：按照您的颜色的 "#xxxxxx" 格式输入十六进制代码。
    - 说明：填写所需的任何值。
    - 方案： HTTPS (保留默认值) 
    - Host： Azure 数字孪生实例的 *主机名* 。
    - 基 URL：/ (保留默认值) 

然后，单击窗口底部的 " *安全性* " 按钮，继续执行下一个配置步骤。

:::image type="content" source="media/how-to-integrate-logic-apps/configure-next.png" alt-text="&quot;编辑逻辑应用自定义连接器&quot; 页面底部的屏幕截图。突出显示按钮以继续安全":::

在 "安全" 步骤中，点击 " *编辑* " 并配置以下信息：
* **身份验证类型**： OAuth 2。0
* **OAuth 2.0**：
    - 标识提供程序： Azure Active Directory
    - 客户端 ID： *应用程序 (客户端) ID* 用于 Azure AD 应用注册
    - 客户端密码：在 Azure AD 应用注册的 [*先决条件*](#prerequisites)中创建的 *客户端密码*
    - 登录 URL： https://login.windows.net (保留默认值) 
    - 租户 ID： Azure AD 应用注册的 *目录 (租户) ID*
    - 资源 URL： 0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    - 作用域： AccessAsUser。所有
    - 重定向 URL： (现在保留默认值) 

请注意，"重定向 URL" 字段显示 " *保存自定义连接器" 以生成重定向 URL*。 现在，通过在窗格顶部命中 *更新连接器* 来确认连接器设置。

:::image type="content" source="media/how-to-integrate-logic-apps/update-connector.png" alt-text="&quot;编辑逻辑应用自定义连接器&quot; 页顶部的屏幕截图。围绕 &quot;更新连接器&quot; 按钮突出显示":::

<!-- Success message? didn't see one -->

返回到 "重定向 URL" 字段并复制已生成的值。 将在下一步中使用它。

:::image type="content" source="media/how-to-integrate-logic-apps/copy-redirect-url.png" alt-text="&quot;编辑逻辑应用自定义连接器&quot; 页中的 &quot;重定向 URL&quot; 字段现在的值为 &quot; https://logic-apis-westus2.consent.azure-apim.net/redirect &quot;。将突出显示用于复制值的按钮。":::

这是创建连接器所需的全部信息， (无需继续执行定义步骤) 的安全性。 可以关闭 *编辑逻辑应用自定义连接器* 窗格。

>[!NOTE]
>返回到你最初点击 *编辑* 的连接器的 "概述" 页上，请注意，再次点击 " *编辑* " 将重新启动输入配置选择的整个过程。 它不会从上次操作时填充值，因此，如果要使用任何更改的值保存更新的配置，则必须重新输入其他所有值，以避免被默认值覆盖。

### <a name="grant-connector-permissions-in-the-azure-ad-app"></a>在 Azure AD 应用程序中授予连接器权限

接下来，使用上一步骤中复制的自定义连接器的 " *重定向 URL* " 值在 Azure AD 应用注册中授予连接器权限。

导航到 Azure 门户中的 " [应用注册](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) " 页，然后从列表中选择你的注册。 

在注册的菜单中的 " *身份验证* " 下，添加 URI。

:::image type="content" source="media/how-to-integrate-logic-apps/add-uri.png" alt-text="Azure 门户中的应用注册的 &quot;身份验证&quot; 页。菜单中的 &quot;Authentication&quot; 将突出显示，并在页面上突出显示 &quot;添加 URI&quot; 按钮。"::: 

在新字段中输入自定义连接器的 *重定向 URL* ，并单击 " *保存* " 图标。

:::image type="content" source="media/how-to-integrate-logic-apps/save-uri.png" alt-text="Azure 门户中的应用注册的 &quot;身份验证&quot; 页。新的重定向 URL 已突出显示，页面的 &quot;保存&quot; 按钮。":::

你现在已经设置了一个可访问 Azure 数字孪生 Api 的自定义连接器。 

## <a name="create-logic-app"></a>创建逻辑应用

接下来，你将创建一个逻辑应用，该应用将使用新连接器自动执行 Azure 数字孪生更新。

在 [Azure 门户](https://portal.azure.com)的门户搜索栏中搜索 " *逻辑应用* "。 选择它应转到 " *逻辑应用* " 页。 点击 " *创建逻辑应用* " 按钮以创建新的逻辑应用。

:::image type="content" source="media/how-to-integrate-logic-apps/create-logic-app.png" alt-text="Azure 门户中的 &quot;逻辑应用&quot; 页。点击 &quot;添加&quot; 按钮":::

在随后的 " *逻辑应用* " 页中，输入订阅和资源组。 同时，为逻辑应用选择一个名称，并选择部署位置。

按 " _评审 + 创建_ " 按钮。

这会将你转到 "查看" 和 " *创建* " 选项卡，你可以在该选项卡上查看详细信息， *然后点击底部创建资源* 。

你将转到逻辑应用的部署页。 部署完成后，请单击 " *转到资源* " 按钮以继续访问 *逻辑应用设计器*，你将在该设计器中填充工作流的逻辑。

### <a name="design-workflow"></a>设计工作流

在 *逻辑应用设计器* 中，在 " *使用常见触发器启动*" 下，选择 " _**重复周期**_"。

:::image type="content" source="media/how-to-integrate-logic-apps/logic-apps-designer-recurrence.png" alt-text="Azure 门户中的 &quot;逻辑应用设计器&quot; 页。围绕 &quot;定期&quot; 公共触发器突出显示":::

在接下来的 *逻辑应用设计器* 页中，将 **定期** 频率更改为 *第二个*，以便每3秒触发一次事件。 这样，以后就可以轻松地查看结果，而无需等待很长时间。

单击 " *+ 新建步骤*"。

这将打开 " *选择操作* " 框。 切换到 " *自定义* " 选项卡。你应会在顶部的框中看到自定义连接器。

:::image type="content" source="media/how-to-integrate-logic-apps/custom-action.png" alt-text="在 Azure 门户的逻辑应用设计器中创建流。在 &quot;选择操作&quot; 框中，选择了 &quot;自定义&quot; 选项卡。前面的框中显示了用户的自定义连接器，其中突出显示了它。":::

选择此项可显示该连接器中包含的 Api 的列表。 使用搜索栏或滚动浏览列表以选择 **DigitalTwins_Add**。  (本文使用的 API，但你也可以选择任何其他 API 作为逻辑应用连接) 的有效选择。

系统可能会要求你用 Azure 凭据登录以连接到连接器。 如果获得 " *请求的权限* " 对话框，请按照提示为你的应用程序授予同意并接受。

在 "new *DigitalTwinsAdd* " 框中，按如下所示填写字段：
* _id_：在实例中填充要更新逻辑应用的数字克隆的克隆 *id* 。
* 克隆 _：此_ 字段用于输入所选 API 请求所需的正文。 对于 *DigitalTwinsUpdate*，此正文是 JSON 修补程序代码的形式。 有关构造用于更新克隆的 JSON 修补程序的详细信息，请参阅 *操作方法：管理数字孪生* 中的 [更新数字](how-to-manage-twin.md#update-a-digital-twin)大距离部分。
* _api 版本_：最新的 api 版本。 此值目前为 *2020-10-31*。

单击逻辑应用设计器中的 " *保存* "。

您可以选择其他操作，方法是在同一窗口中选择 " _+ 新建步骤_ "。

:::image type="content" source="media/how-to-integrate-logic-apps/save-logic-app.png" alt-text="逻辑应用连接器中应用的已完成视图。DigitalTwinsAdd 框中填充了上述值，包括示例 JSON 修补程序正文。将突出显示该窗口的 &quot;保存&quot; 按钮。":::

## <a name="query-twin-to-see-the-update"></a>查询克隆以查看更新

既然已经创建了逻辑应用，则在逻辑应用设计器中定义的克隆更新事件应该每三秒钟重复一次。 这意味着，三秒钟后，您应该能够查询您的克隆，并看到新修补后的值反映。

你可以通过 (选择的方法（如 [自定义客户端应用](tutorial-command-line-app.md)、 [Azure 数字孪生资源管理器示例应用](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/)、 [sdk 和 api](how-to-use-apis-sdks.md)或 [CLI](how-to-use-cli.md)) ）查询你的。 

有关查询 Azure 数字孪生实例的详细信息，请参阅 [*如何：查询双子图形*](how-to-query-graph.md)。

## <a name="next-steps"></a>后续步骤

本文介绍了如何创建一个逻辑应用，该应用会定期用提供的修补程序来更新 Azure 数字孪生实例中的克隆。 可以尝试在自定义连接器中选择其他 Api，为实例上的各种操作创建逻辑应用。

若要详细了解可用的 Api 操作和所需的详细信息，请访问 [*操作方法：使用 Azure 数字孪生 api 和 sdk*](how-to-use-apis-sdks.md)。