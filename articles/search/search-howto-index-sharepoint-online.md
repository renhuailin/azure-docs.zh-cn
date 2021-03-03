---
title: " (预览配置 SharePoint Online 索引器) "
titleSuffix: Azure Cognitive Search
description: 设置 SharePoint Online 索引器以自动索引 Azure 认知搜索中的文档库内容。
manager: luisca
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/01/2021
ms.openlocfilehash: 558df115043d76acf865f19611e8c4cd322e00a7
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679146"
---
# <a name="how-to-configure-sharepoint-online-indexing-in-cognitive-search-preview"></a>如何在认知搜索 (预览版中配置 SharePoint Online 索引) 

> [!IMPORTANT] 
> SharePoint Online 支持目前处于 **封闭公共预览版** 中。 你可以通过填写 [此窗体](https://aka.ms/azure-cognitive-search/indexer-preview)请求对 "封闭预览版" 的访问权限。
>
> 提供的预览版功能不附带服务级别协议，我们不建议将其用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
> 
> [REST API 版本 2020-06-30-Preview](search-api-preview.md) 提供此功能。 目前没有门户或 SDK 支持。

本文介绍如何使用 Azure 认知搜索将文档 (（如 Pdf、Microsoft Office 文档和其他一些常见格式）编入 Azure 认知搜索索引中，) 存储在 SharePoint Online 文档库中。 首先，它介绍了设置和配置索引器的基本知识。 其次，本文更加深入地探讨了你可能会遇到的行为和场景。

## <a name="functionality"></a>功能
Azure 认知搜索中的索引器是从数据源提取可搜索的数据和元数据的爬网程序。 SharePoint Online 索引器将从一个或多个文档库连接到您的 SharePoint Online 网站和索引文档。 索引器提供以下功能：
+ 从一个或多个 SharePoint Online 文档库中为内容编制索引。
+ 从 SharePoint Online 文档库中索引与 Azure 认知搜索服务位于同一租户中的内容。 索引器将无法与 Azure 认知搜索服务在不同租户中使用的 SharePoint 网站。 
+ 索引器将支持增量索引，这意味着它将标识文档库中的哪些内容已更改，并只在以后索引运行时为更新的内容编制索引。 例如，如果5个 Pdf 最初由索引器编制索引，则更新1，然后索引器再次运行，索引器将只为已更新的1个 PDF 编制索引。
+ 默认情况下，将从已编制索引的文档中提取文本和规范化图像。 可以选择将技能组合添加到管道中，以便进一步扩充内容。 有关技能集的详细信息，请参阅 [Azure 中的技能组合概念认知搜索](cognitive-search-working-with-skillsets.md)。

## <a name="supported-document-formats"></a>支持的文档格式

Azure 认知搜索 SharePoint Online 索引器可以从以下文档格式提取文本：

[!INCLUDE [search-document-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="incremental-indexing-and-deletion-detection"></a>增量索引和删除检测
默认情况下，SharePoint Online 索引器支持渐进式索引，这意味着它将标识文档库中的哪些内容已更改，并且仅在未来索引运行时为更新的内容编制索引。 例如，如果最初由索引器编制5个 Word 文档的索引，则更新1，然后索引器再次运行，索引器将仅对已更新的1个 Word 文档重新建立索引。

默认情况下，还支持删除检测。 这意味着，如果从 SharePoint Online 文档库中删除文档，索引器将在以后的索引器运行期间检测到删除，并从索引中删除该文档。

## <a name="setting-up-sharepoint-online-indexing"></a>设置 SharePoint Online 索引
若要设置 SharePoint Online 索引器，需要在 Azure 门户中执行某些操作，并使用预览 REST API 执行一些操作。 SDK 不支持此预览版。

### <a name="step-1-enable-system-assigned-managed-identity"></a>步骤1：启用系统分配的托管标识
启用系统分配的托管标识后，Azure 将为你的搜索服务创建一个可供索引器使用的标识。

![启用系统分配的托管标识](media/search-howto-index-sharepoint-online/enable-managed-identity.png "启用系统分配的托管标识")

选择“保存”后，将看到已分配给搜索服务的对象 ID。

![系统分配托管标识](media/search-howto-index-sharepoint-online/system-assigned-managed-identity.png "系统分配托管标识")

### <a name="step-2-create-an-aad-application"></a>步骤2：创建 AAD 应用程序
SharePoint Online 索引器将使用此 AAD 应用程序进行身份验证。 

1.  导航到 [Azure 门户](https://portal.azure.com/)。

1.  打开主页左侧的菜单，选择 **Azure Active Directory** ，然后选择 " **应用注册**"。 选择 " **+ 新注册**"。
    1.  提供应用程序的名称。
    2.  选择 **单个租户**。
    3.  不需要重定向 URI。
    4.  选择“注册”

1.  从左侧菜单中选择 " **API 权限** "，然后 **添加权限**，然后 **Microsoft Graph** " **委托的权限**"。 添加以下 API 权限： 
    1.  **委托-文件。读取. 全部** 
    2.  **委托-站点读取。全部** 
    3.  **委托-用户读取**

    ![委托的 API 权限](media/search-howto-index-sharepoint-online/delegated-api-permissions.png "委托的 API 权限")

    使用委托权限意味着索引器将在用户上下文中访问 SharePoint 站点。 因此，当您运行索引器时，它将只具有对登录用户有权访问的内容的访问权限。 创建索引器或更新日期源时，会进行用户登录。 此登录步骤将在本文的后面部分进行介绍。

1.  选择 "**身份验证**" 选项卡。将 "**允许公共客户端流向**"，然后选择 "**保存** **"** 。

1.  选择 " **+ 添加平台**"，然后依次选择 " **移动和桌面应用程序" 和** `https://login.microsoftonline.com/common/oauth2/nativeclient` " **配置**"。

    ![AAD 应用身份验证配置](media/search-howto-index-sharepoint-online/aad-app-authentication-configuration.png "AAD 应用身份验证配置")

1.  仅为某些租户)  (要求管理员同意。

    某些租户被锁定，这种方法要求管理员同意这些委派的 API 权限。 如果是这种情况，则需要让管理员在创建索引器之前授予此 AAD 应用程序的管理员同意。 

    由于并非所有租户都有此要求，因此我们建议先跳过此步骤，并继续执行说明。 如果创建索引器时，你会知道是否需要管理员同意，则身份验证将会失败，告知你需要管理员批准身份验证。 在这种情况下，请使用下面的按钮让租户管理员授予许可。

    ![AAD 应用授予管理员许可](media/search-howto-index-sharepoint-online/aad-app-grant-admin-consent.png "AAD 应用授予管理员许可")

<a name="create-data-source"></a>

### <a name="step-3-create-data-source"></a>步骤3：创建数据源
> [!IMPORTANT] 
> 从此部分开始，你需要使用预览版 REST API 来执行剩余的步骤。 如果你不熟悉 Azure 认知搜索 REST API，建议你参阅此 [快速入门](search-get-started-rest.md)教程。

数据源指定要编制索引的数据、访问数据所需的凭据和有效标识数据更改（新行、修改的行或删除的行）的策略。 一个数据源可供同一搜索服务中的多个索引器使用。

对于 SharePoint 索引，数据源必须具有以下必需属性：
+ **name** 是搜索服务中数据源的唯一名称。
+ **类型** 必须为 "sharepoint"。 这区分大小写。
+ **凭据** 提供 SharePoint Online 终结点和 AAD 应用程序 (客户端) ID。 SharePoint Online 终结点示例 `https://microsoft.sharepoint.com/teams/MySharePointSite` 。 可以通过导航到 SharePoint 站点的主页并从浏览器复制 URL 来获取 SharePoint Online 终结点。
+ **容器** 指定要编制索引的文档库。 有关创建容器的详细信息，请参阅本文档的 " [控制哪些文档为索引](#controlling-which-documents-are-indexed) " 一节。

创建数据源：

```http
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sharepoint-datasource",
    "type" : "sharepoint",
    "credentials" : { "connectionString" : "SharePointOnlineEndpoint=[SharePoint Online site url];ApplicationId=[AAD App ID]" },
    "container" : { "name" : "defaultSiteLibrary", "query" : null }
}
```

### <a name="step-4-create-an-index"></a>步骤4：创建索引
索引指定文档、属性和其他构造中可以塑造搜索体验的字段。

下面介绍如何创建包含可搜索内容字段的索引，以存储从文档库中的文档提取的文本：

```http
POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sharepoint-index",
    "fields": [
        { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
        { "name": "metadata_spo_item_name", "type": "Edm.String", "key": false, "searchable": true, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_spo_item_path", "type": "Edm.String", "key": false, "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_spo_item_content_type", "type": "Edm.String", "key": false, "searchable": false, "filterable": true, "sortable": false, "facetable": true },
        { "name": "metadata_spo_item_last_modified", "type": "Edm.DateTimeOffset", "key": false, "searchable": false, "filterable": false, "sortable": true, "facetable": false },
        { "name": "metadata_spo_item_size", "type": "Edm.Int64", "key": false, "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
    ]
}

```

有关详细信息，请参阅 [创建索引 (REST API) ](https://docs.microsoft.com/rest/api/searchservice/create-index)。

### <a name="step-5-create-an-indexer"></a>步骤5：创建索引器
索引器将数据源与目标搜索索引关联，并提供自动执行数据刷新的计划。 创建索引和数据源后，就可以创建索引器了！

在此部分中，系统将要求你使用有权访问 SharePoint 站点的组织凭据进行登录。 如果可能，我们建议创建新的组织用户帐户，并为该新用户提供希望索引器具有的确切权限。

创建索引器有几个步骤：

1.  发送请求以尝试创建索引器。

    ```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    
        {
          "name" : "sharepoint-indexer",
          "dataSourceName" : "sharepoint-datasource",
          "targetIndexName" : "sharepoint-index"
        }
    
    ```

1.  首次创建索引器时，它将失败，并且您将看到以下错误。 中转到错误消息中的链接。 如果在10分钟内未到达链接，则代码将过期，你将需要重新创建 [数据源](#create-data-source)。

    ```http
    {
        "error": {
            "code": "",
            "message": "Error with data source: To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code <CODE> to authenticate.  Please adjust your data source definition in order to proceed."
        }
    }
    ```

1.  提供错误消息中提供的代码。

    ![输入设备代码](media/search-howto-index-sharepoint-online/enter-device-code.png "输入设备代码")

1.  SharePoint 索引器将作为已登录用户访问 SharePoint 内容。 在此步骤中登录的用户将是已登录的用户。 因此，如果使用无法访问要编制索引的文档库中的文档的用户帐户登录，则该索引器将无法访问该文档。

    如果可能，我们建议创建新的用户帐户，并为该新用户提供索引器所需的确切权限。

1.  批准请求的权限。

    ![审批 API 权限](media/search-howto-index-sharepoint-online/aad-app-approve-api-permissions.png "审批 API 权限")

1.  重新发送索引器创建请求。 这次请求应成功。 

    ```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    
    {
        "name" : "sharepoint-indexer",
        "dataSourceName" : "sharepoint-datasource",
        "targetIndexName" : "sharepoint-index"
    }
    ```

### <a name="step-6-check-the-indexer-status"></a>步骤6：检查索引器状态
创建索引器后，可以通过发出以下请求来检查索引器状态。

```http
GET https://[service name].search.windows.net/indexers/sharepoint-indexer/status?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]
```

有关索引器状态的详细信息，请参阅： [获取索引器状态](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)。

## <a name="updating-the-data-source"></a>更新数据源
如果没有对数据源对象的更新，则索引器可按计划运行，无需任何用户交互。 不过，每次更新 Azure 认知搜索数据源对象时，都需要再次登录，以便索引器运行。 例如，如果您更改数据源查询，则需要使用 `https://microsoft.com/devicelogin` 和新的代码再次登录。

数据源更新后，请执行以下步骤：

1.  手动启动索引器的运行。

    ```http
    POST https://[service name].search.windows.net/indexers/sharepoint-indexer/run?api-version=2020-06-30-Preview  
    Content-Type: application/json
    api-key: [admin key]
    ```

    有关索引器运行请求的详细信息，请参阅： [Run 索引器](https://docs.microsoft.com/rest/api/searchservice/run-indexer)。

1.  检查索引器状态。 如果最后一个索引器运行有一个错误，指示你要执行 `https://microsoft.com/devicelogin` ，请前往该页并提供新代码。 

    ```http
    GET https://[service name].search.windows.net/indexers/sharepoint-indexer/status?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    ```

    有关索引器状态的详细信息，请参阅： [获取索引器状态](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)。

1.  登录

1.  手动启动索引器运行并检查索引器状态。 这次，索引器运行应成功启动。

## <a name="indexing-document-metadata"></a>为文档元数据编制索引
如果已将索引器设置为索引文档元数据，则可将以下元数据用于索引。

> [!NOTE]
> 预览的当前版本中不包含自定义元数据。

| 标识符 | 类型 | 说明 | 
| ------------- | -------------- | ----------- |
| metadata_spo_site_library_item_id | Edm.String | 站点 ID、库 ID 和项 ID 的组合键，用于唯一标识站点的文档库中的项。 |
| metadata_spo_site_id | Edm.String | SharePoint Online 网站的 ID。 |
| metadata_spo_library_id | Edm.String | 文档库的 ID。 |
| metadata_spo_item_id | Edm.String | 库中 (文档) 项的 ID。 |
| metadata_spo_item_last_modified | Edm.DateTimeOffset | 项的上次修改日期/时间 (UTC) 。 |
| metadata_spo_item_name | Edm.String | 项的名称。 |
| metadata_spo_item_size | Edm.Int64 | 项的大小 (以字节为单位) 。 | 
| metadata_spo_item_content_type | Edm.String | 项的内容类型。 | 
| metadata_spo_item_extension | Edm.String | 项的扩展。 |
| metadata_spo_item_weburi | Edm.String | 项的 URI。 |
| metadata_spo_item_path | Edm.String | 父路径和项名称的组合。 | 

SharePoint Online 索引器还支持特定于每个文档类型的元数据。 有关详细信息，请参阅 [Azure 认知搜索中使用的内容元数据属性](search-blob-metadata-properties.md)。

<a name="controlling-which-documents-are-indexed"></a>

## <a name="controlling-which-documents-are-indexed"></a>控制要编制索引的文档
单个 SharePoint Online 索引器可以为一个或多个文档库中的内容编制索引。 创建数据源时，请使用 *container* 参数，以指示要编制索引的文档库。 数据源 *容器* 有两个属性： *名称* 和 *查询*。 

### <a name="name"></a>名称
*Name* 属性是必需的，必须是以下三个值之一：
+ *defaultSiteLibrary*
    + 为站点默认文档库中的所有内容编制索引。
+   *allSiteLibraries*
    + 为站点中的所有文档库中的所有内容编制索引。 这不会为子网站中的文档库编制索引。 不过，可以在 *查询* 中指定这些项。
+   *useQuery*
    + 仅索引在 *查询* 中定义的内容。

### <a name="query"></a>查询
*Query* 属性由关键字/值对组成。 下面是可以使用的关键字。 这些值为网站 url 或文档库 url。

> [!NOTE]
> 若要获取特定关键字的值，建议在浏览器中打开 SharePoint Online，导航到要尝试包含/排除的文档库，并从浏览器复制该 URI。 这是获取要在查询中与关键字一起使用的值的最简单方法。

| 关键字 | 查询说明 | 示例 |
| ------------- | -------------- | ----------- |
| Null | 如果为 null 或空，则根据容器名称为默认文档库或所有文档库编制索引。 | 索引默认网站库中的所有内容： <br><br>  ``` "container" : { "name" : "defaultSiteLibrary", "query" : null } ``` |
| includeLibrariesInSite | 从连接字符串中定义的站点中的所有库中为内容编制索引。 这些仅限于网站的子网站 <br><br> 此关键字的 *查询* 值应为站点或子站点的 URI。 | 为 "我的所有文档" 库中的所有内容编制索引。 <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrariesInSite=https://mycompany.sharepoint.com/mysite" } ``` |
| includeLibrary | 为此库中的内容编制索引。 <br><br> 此关键字的 *查询* 值应采用以下格式之一： <br><br> 示例 1： <br><br> *includeLibrary = [网站或子网站]/[文档库]* <br><br> 示例 2： <br><br> 从浏览器复制的 URI。 | 为 MyDocumentLibrary 中的所有内容编制索引： <br><br> 示例 1： <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrary=https://mycompany.sharepoint.com/mysite/MyDocumentLibrary" } ``` <br><br> 示例 2： <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrary=https://mycompany.sharepoint.com/teams/mysite/MyDocumentLibrary/Forms/AllItems.aspx" } ``` |
| excludeLibrary |  不要为此库中的内容编制索引。 <br><br> 此关键字的 *查询* 值应采用以下格式之一： <br><br> 示例 1： <br><br> *excludeLibrary = [站点或子站点 URI]/[文档库]* <br><br> 示例 2： <br><br> 从浏览器复制的 URI。 | 索引除 MyDocumentLibrary 之外的所有库中的所有内容： <br><br> 示例 1： <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrariesInSite=https://mysite.sharepoint.com/subsite1; excludeLibrary=https://mysite.sharepoint.com/subsite1/MyDocumentLibrary" } ``` <br><br> 示例 2： <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrariesInSite=https://mycompany.sharepoint.com/teams/mysite; excludeLibrary=https://mycompany.sharepoint.com/teams/mysite/MyDocumentLibrary/Forms/AllItems.aspx" } ``` |

## <a name="index-by-file-type"></a>按文件类型编制索引
您可以控制要对哪些文档编制索引，并跳过哪些文档。

### <a name="include-documents-having-specific-file-extensions"></a>包含具有特定文件扩展名的文档
您只能使用索引器配置参数为具有指定的文件扩展名的文档编制索引 `indexedFileNameExtensions` 。 值是包含文件扩展名（包括前置句点）逗号分隔列表的字符串。 例如，若为，则只对进行索引。PDF 和。.DOCX 文档，请执行以下操作：

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    ... other parts of indexer definition
    "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
}
```

### <a name="exclude-documents-having-specific-file-extensions"></a>排除具有特定文件扩展名的文档
您可以使用配置参数从索引中排除具有特定文件扩展名的文档 `excludedFileNameExtensions` 。 值是包含文件扩展名（包括前置句点）逗号分隔列表的字符串。 例如，若要对除具有的内容以外的所有内容编制索引。PNG 和。JPEG 扩展，请执行以下操作：

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    ... other parts of indexer definition
    "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
}
```

如果同时存在 `indexedFileNameExtensions` 和 `excludedFileNameExtensions` 参数，Azure 认知搜索会先查看 `indexedFileNameExtensions`，再查看 `excludedFileNameExtensions`。 这意味着，如果两个列表中存在同一个文件扩展名，将从索引编制中排除该扩展名。

## <a name="handling-errors"></a>处理错误
默认情况下，SharePoint Online 索引器会在遇到具有不支持内容类型的文档时立即停止 (例如，图像) 。 当然，可以使用 `excludedFileNameExtensions` 参数跳过某些内容类型。 但是，你可能需要提前对文档编制索引，而无需事先知道所有可能的内容类型。 若要在遇到不支持的内容类型时继续编制索引，请将 `failOnUnsupportedContentType` 配置参数设置为 false：

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    ... other parts of indexer definition
    "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false } }
}
```

对于某些文档，Azure 认知搜索无法确定内容类型，或者无法处理其他受支持的内容类型的文档。 若要忽略此故障模式，将 `failOnUnprocessableDocument` 配置参数设置为 false：

```http
"parameters" : { "configuration" : { "failOnUnprocessableDocument" : false } }
```

Azure 认知搜索限制已编制索引的文档的大小。 这些限制记录在 [Azure 认知搜索中的服务限制](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)中。 默认情况下，超大的文档被视为错误。 但是，如果将配置参数设置为 true，则仍可为超大文档的存储元数据编制索引 `indexStorageMetadataOnlyForOversizedDocuments` ：

```http
"parameters" : { "configuration" : { "indexStorageMetadataOnlyForOversizedDocuments" : true } }
```

如果在分析文档时或在将文档添加到索引的过程中发生错误，也可以继续编制索引。 若要忽略特定的错误数，将 `maxFailedItems` 和 `maxFailedItemsPerBatch` 配置参数设置为所需值。 例如：

```http
{
    ... other parts of indexer definition
    "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
}
```

## <a name="see-also"></a>另请参阅
+ [Azure 认知搜索中的索引器](search-indexer-overview.md)
+ [Azure 认知搜索中使用的内容元数据属性](search-blob-metadata-properties.md)
