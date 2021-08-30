---
title: 与逻辑应用集成
titleSuffix: Azure Digital Twins
description: 了解如何使用自定义连接器将逻辑应用连接到 Azure 数字孪生
author: baanders
ms.author: baanders
ms.date: 9/11/2020
ms.topic: how-to
ms.service: digital-twins
ms.reviewer: baanders
ms.openlocfilehash: 7aca72097e1d6aee40e5e9ead0c60be34d3e65d5
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114437822"
---
# <a name="integrate-with-logic-apps-using-a-custom-connector"></a>使用自定义连接器与逻辑应用集成

[Azure 逻辑应用](../logic-apps/logic-apps-overview.md)是一项云服务，可帮助你跨应用和服务自定义工作流。 通过将逻辑应用连接到 Azure 数字孪生 API，可以围绕 Azure 数字孪生及其数据创建这样的自动化流程。

Azure 数字孪生当前没有针对逻辑应用的已认证（预建）连接器。 当前通过 Azure 数字孪生使用逻辑应用的过程是，使用自定义 Azure 数字孪生 Swagger 创建经修改可用于逻辑应用的[自定义逻辑应用连接器](/samples/azure-samples/digital-twins-custom-swaggers/azure-digital-twins-custom-swaggers/)。

> [!NOTE]
> 上面链接的自定义 Swagger 示例包含多个版本的 Swagger。 可在具有最近日期的子文件夹中找到最新版本，但仍支持示例中包含的较旧版本。

在本文中，你将使用 [Azure 门户](https://portal.azure.com)创建自定义连接器，此连接器可用于将逻辑应用连接到 Azure 数字孪生实例。 然后，你将创建使用此连接作为示例方案的逻辑应用，其中由计时器触发的事件会自动更新 Azure 数字孪生实例中的一个孪生体。 

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
使用此帐户登录到 [Azure 门户](https://portal.azure.com)。 

作为先决条件的一部分，还需要完成以下各项。 本节的剩余部分将引导你完成这些步骤：
- 设置 Azure 数字孪生实例
- 添加数字孪生
- 设置 Azure Active Directory (Azure AD) 应用注册

### <a name="set-up-azure-digital-twins-instance"></a>设置 Azure 数字孪生实例

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

### <a name="add-a-digital-twin"></a>添加数字孪生

本文使用逻辑应用来更新 Azure 数字孪生实例中的孪生体。 若要继续，应在实例中添加至少一个孪生体。 

可以使用 [DigitalTwins API](/rest/api/digital-twins/dataplane/twins)、[.Net (C# ) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) 或 [Azure 数字孪生 CLI](/cli/azure/dt?view=azure-cli-latest&preserve-view=true) 添加孪生体。 有关如何使用这些方法创建孪生体的详细步骤，请参阅[管理数字孪生体](how-to-manage-twin.md)。

需要已创建实例中的孪生体的孪生体 ID。

### <a name="set-up-app-registration"></a>设置应用注册

[!INCLUDE [digital-twins-prereq-registration.md](../../includes/digital-twins-prereq-registration.md)]

## <a name="create-custom-logic-apps-connector"></a>创建自定义逻辑应用连接器

现在，你已准备好为 Azure 数字孪生 API 创建[自定义逻辑应用连接器](../logic-apps/custom-connector-overview.md)。 完成此操作后，在下一部分中创建逻辑应用时，可以挂接 Azure 数字孪生。

导航到 Azure 门户中的[逻辑应用自定义连接器](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Web%2FcustomApis)页面（可使用此链接，也可以在门户搜索栏中搜索它）。 选择“+ 添加”。

:::image type="content" source="media/how-to-integrate-logic-apps/logic-apps-custom-connector.png" alt-text="Azure 门户中“逻辑应用自定义连接器”页面的屏幕截图。突出显示了“添加”按钮。":::

在随后的“创建逻辑应用自定义连接器”页面中，选择你的订阅和资源组，以及新连接器的名称和部署位置。 选择“查看 + 创建”。 

:::image type="content" source="media/how-to-integrate-logic-apps/create-logic-apps-custom-connector.png" alt-text="Azure 门户中的“创建逻辑应用自定义连接器”页面的屏幕截图。":::

你将转到“查看 + 创建”选项卡，可在其中选择底部的“创建”以创建资源。 

:::image type="content" source="media/how-to-integrate-logic-apps/review-logic-apps-custom-connector.png" alt-text="Azure 门户中“创建逻辑应用自定义连接器”页面的“查看 + 创建”选项卡的屏幕截图。":::

你将转到连接器的部署页面。 完成部署后，选择“转到资源”按钮以在门户中查看连接器的详细信息。

### <a name="configure-connector-for-azure-digital-twins"></a>为 Azure 数字孪生配置连接器

接下来，配置你创建的连接器以访问 Azure 数字孪生。

首先，下载经修改适用于逻辑应用的自定义 Azure 数字孪生 Swagger。 选择“下载 ZIP”按钮，下载 [Azure 数字孪生自定义 Swagger（逻辑应用连接器）示例](/samples/azure-samples/digital-twins-custom-swaggers/azure-digital-twins-custom-swaggers/)。 导航到已下载的 Azure_Digital_Twins_custom_Swaggers__Logic_Apps_connector_.zip 文件夹，然后将其解压缩。 

此教程的自定义 Swagger 位于 Azure_Digital_Twins_custom_Swaggers__Logic_Apps_connector_\LogicApps 文件夹中。 此文件夹包含名为 stable 和 preview 的子文件夹，其中都包含 Swagger 的不同版本，按日期排列。  具有最近日期的文件夹包含 Swagger 的最新副本。 无论选择哪个版本，Swagger 文件的名称都是 digitaltwins.json。

> [!NOTE]
> 除非你要使用预览功能，否则一般建议使用 Swagger 的最新 stable 版本。 不过，也支持 Swagger 的较旧版本和预览版本。 

接下来，在 [Azure 门户](https://portal.azure.com)中转到连接器的“概述”页，然后选择“编辑”。

:::image type="content" source="media/how-to-integrate-logic-apps/edit-connector.png" alt-text="在上一步中创建的连接器的“概述”页的屏幕截图。突出显示了“编辑”按钮。":::

在随后的“编辑逻辑应用自定义连接器”页面中，配置以下信息：
* **自定义连接器**
    - API 终结点：REST（保留默认值）
    - 导入模式：OpenAPI 文件（保留默认值）
    - 文件：这将是你先前下载的自定义 Swagger 文件。 选择“导入”，找到计算机上的文件 (Azure_Digital_Twins_custom_Swaggers__Logic_Apps_connector_\LogicApps\...\digitaltwins.json)，然后选择“打开”。
* **常规信息**
    - 图标：上传你喜欢的图标
    - 图标背景色：为所选颜色以“#xxxxxx”格式输入十六进制代码作。
    - 说明：填写你想要的任何值。
    - 方案：HTTPS（保留默认值）
    - 主机：Azure 数字孪生实例的主机名。
    - 基 URL：/（保留默认值）

然后，选择窗口底部的“安全性”按钮，继续执行下一个配置步骤。

:::image type="content" source="media/how-to-integrate-logic-apps/configure-next.png" alt-text="“编辑逻辑应用自定义连接器”页面底部的屏幕截图。突出显示了用于连接到“安全性”的按钮。":::

在“安全性”步骤中，选择“编辑”，并配置以下信息：
* 身份验证类型：OAuth 2.0
* OAuth 2.0：
    - 标识提供者：Azure Active Directory
    - 客户端 ID：你在[先决条件](#prerequisites)中创建的用于 Azure AD 应用注册的应用程序（客户端）ID
    - 客户端密码：应用注册的客户端密码 
    - 登录 URL：https://login.windows.net（保留默认值）
    - 租户 ID：Azure AD 应用注册的 Directory（租户）ID
    - 资源 URL：0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    - 作用域：Directory.AccessAsUser.All
    - 重定向 URL：（暂时保留默认值）

注意“重定向 URL”字段显示“保存自定义连接器以生成重定向 URL”。 现在可通过选择窗格顶部的“更新连接器”以确认连接器设置，立即执行此操作。

:::image type="content" source="media/how-to-integrate-logic-apps/update-connector.png" alt-text="“编辑逻辑应用自定义连接器”页面顶部的屏幕截图。突出显示了“更新连接器”按钮。":::

返回到“重定向 URL”字段，复制已生成的值。 将在下一步中使用它。

:::image type="content" source="media/how-to-integrate-logic-apps/copy-redirect-url.png" alt-text="“编辑逻辑应用自定义连接器”页面中的“重定向 URL”字段的屏幕截图。突出显示了用于复制该值的按钮。":::

这是创建连接器所需的全部信息（无需在“安全性”之后继续执行“定义”步骤）。 可以关闭“编辑逻辑应用自定义连接器”窗格。

>[!NOTE]
>返回到你最初在其中选择了“编辑”的连接器“概述”页，注意，再次选择“编辑”将重启输入配置选择的整个过程。 它不会填充上次操作时填写的值，因此，如果要保存包含任何更改后的值的更新的配置，还必须重新输入其他所有值，以免其被默认值覆盖。

### <a name="grant-connector-permissions-in-the-azure-ad-app"></a>在 Azure AD 应用中授予连接器权限

接下来，使用你在上一步中复制的自定义连接器的重定向 URL 值，授予连接器在 Azure AD 应用注册中的权限。

在 Azure 门户中导航到[应用注册](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)页面，然后从列表中选择注册。 

在注册菜单中的“身份验证”下，添加 URI。

:::image type="content" source="media/how-to-integrate-logic-apps/add-uri.png" alt-text="Azure 门户中应用注册的“身份验证”页的屏幕截图，突出显示了“添加 URI”按钮和“身份验证”菜单。"::: 

在新字段中输入自定义连接器的重定向 URL，并选择“保存”图标。 

:::image type="content" source="media/how-to-integrate-logic-apps/save-uri.png" alt-text="Azure 门户中应用注册的“身份验证”页的屏幕截图，突出显示了新的重定向 URL 和“保存”按钮。":::

你现在已经设置了一个可访问 Azure 数字孪生 API 的自定义连接器。 

## <a name="create-logic-app"></a>创建逻辑应用

接下来，你将创建一个逻辑应用，该应用将使用新连接器自动执行 Azure 数字孪生更新。

在 [ Azure 门户](https://portal.azure.com)的门户搜索栏中搜索“逻辑应用”。 选择它应会转到“逻辑应用”页。 选择“创建逻辑应用”按钮以创建新的逻辑应用。

:::image type="content" source="media/how-to-integrate-logic-apps/create-logic-app.png" alt-text="Azure 门户中“逻辑应用”页的屏幕截图，突出显示了“创建逻辑应用”按钮。":::

在随后的“逻辑应用”页面中，输入你的订阅和资源组。 另外，为逻辑应用选择一个名称，然后选择部署位置。

选择“查看 + 创建”按钮。

将转到“查看 + 创建”选项卡，可在其中查看详细信息，并选择底部的“创建”以创建资源。 

你将被转到逻辑应用的部署页面。 完成部署后，请选择“转到资源”按钮以继续访问逻辑应用设计器，你将在其中填写工作流的逻辑。 

### <a name="design-workflow"></a>设计工作流

在“逻辑应用设计器”中，在“使用常见触发器启动”下，选择“重复周期”。

:::image type="content" source="media/how-to-integrate-logic-apps/logic-apps-designer-recurrence.png" alt-text="Azure 门户中的“逻辑应用设计器”页的屏幕截图，突出显示了“定期”通用触发器。":::

在随即显示的“逻辑应用设计器”页中，将“定期”频率更改为“秒”，以便每 3 秒触发一次事件。  这样，以后就可以轻松地查看结果，而无需等待很长时间。

选择“+新建步骤”。

随即将打开“选择操作”框。 切换到“自定义”选项卡。你应会在顶部框中看到前面的自定义连接器。

:::image type="content" source="media/how-to-integrate-logic-apps/custom-action.png" alt-text="在 Azure 门户中的逻辑应用设计器中创建流的屏幕截图。突出显示了自定义连接器。":::

选择它以显示该连接器中包含的 API 列表。 使用搜索栏或滚动浏览列表来选择“DigitalTwins_Add”。 （本文中使用此 API，但你也可以选择任何其他 API 作为逻辑应用连接的有效选项）。

系统可能会要求你用 Azure 凭据登录以连接到连接器。 如果看到“请求的权限”对话框，请按照提示为你的应用授予同意并接受。

在新的“DigitalTwinsAdd Update”框中，填写以下字段：
* id：填写你希望逻辑应用更新的实例中的数字孪生体的孪生体 ID。
* twin：在此字段中，输入所选 API 请求所需的正文。 对于 DigitalTwinsUpdate，此正文采用 JSON 修补程序代码的形式。 有关构造用于更新孪生体的 JSON 修补程序的详细信息，请参阅“如何：管理数字孪生体”中的[更新数字孪生体](how-to-manage-twin.md#update-a-digital-twin)部分。
* API 版本：最新的 API 版本。 目前，此值为 2020-10-31。

在逻辑应用设计器中选择“保存”。

可以在同一窗口中选择“+ 新建步骤”，来选择其他操作。

:::image type="content" source="media/how-to-integrate-logic-apps/save-logic-app.png" alt-text="逻辑应用连接器中应用的完成视图的屏幕截图。“DigitalTwinsAdd”框中填充了上述值。":::

## <a name="query-twin-to-see-the-update"></a>查询孪生体以查看更新

现在，你已创建逻辑应用，你在逻辑应用设计器中定义的孪生体更新事件应每 3 秒发生一次。 这意味着，3 秒后，你应该能够查询孪生体，并看到反映出修补后的新值。

可以通过你选择的方法（如[自定义客户端应用](tutorial-command-line-app.md)、[Azure Digital Twins Explorer](concepts-azure-digital-twins-explorer.md)、[SDK 和 API](concepts-apis-sdks.md) 或 [CLI](concepts-cli.md)）来查询孪生体。 

有关查询 Azure 数字孪生实例的详细信息，请参阅[查询孪生体图](how-to-query-graph.md)。

## <a name="next-steps"></a>后续步骤

在本文中，你创建了一个逻辑应用，它会用你提供的修补程序定期更新 Azure 数字孪生实例中的孪生体。 可以尝试在自定义连接器中选择其他 API，为对实例的各种操作创建逻辑应用。

若要详细了解可用的 API 操作及其所需的详细信息，请访问 [Azure 数字孪生 API 和 SDK](concepts-apis-sdks.md)。
