---
title: 从 SharePoint Online 索引数据（预览版）
titleSuffix: Azure Cognitive Search
description: 设置 SharePoint Online 索引器，以自动索引 Azure 认知搜索中的文档库内容。
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/01/2021
ms.openlocfilehash: 3a6bb0fd360b334299c6cd1be2795121a3b53203
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124777519"
---
# <a name="index-data-from-sharepoint-online"></a>从 SharePoint Online 索引数据

> [!IMPORTANT] 
> 根据[补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，SharePoint Online 支持目前为公共预览版。 [请求访问](https://aka.ms/azure-cognitive-search/indexer-preview)此功能，并在启用访问权限后，使用[预览版 REST API（2020-06-30-preview 或更高版本）](search-api-preview.md)索引内容。 目前提供有限的门户支持，不提供 .NET SDK 支持。

本文介绍了如何使用 Azure 认知搜索将 SharePoint Online 文档库中存储的文档（如 PDF、Microsoft Office 文档和其他几种常见格式）索引到 Azure 认知搜索索引中。 首先，本文介绍了设置和配置索引器的基础知识。 其次，本文更加深入地探讨了你可能会遇到的行为和场景。

> [!NOTE]
> SharePoint Online 支持一种精细授权模型，它可确定每位用户在文档级别的访问权限。 SharePoint Online 索引器不会将这些权限拉取到搜索索引中，并且认知搜索不支持文档级授权。 将 SharePoint Online 中的某文档索引到搜索服务后，任何对该索引具有读取访问权限的人都可使用该内容。 如果需要文档级权限，应调查安全筛选器来剪裁未经授权的内容结果。 有关详细信息，请参阅[使用 Active Directory 标识进行安全剪裁](search-security-trimming-for-azure-search-with-aad.md)。

## <a name="functionality"></a>功能

Azure 认知搜索中的索引器是一种爬网程序，可从数据源中提取可搜索的数据和元数据。 SharePoint Online 索引器将连接到你的 SharePoint Online 站点，并从一个或多个文档库索引文档。 该索引器提供以下功能：
+ 从一个或多个 SharePoint Online 文档库中索引内容。
+ 索引器支持增量索引，这意味着它将标识文档库中出现了更改的内容，并在今后索引运行时只索引已更新的内容。 例如，如果索引器最初索引了 5 个 PDF，随后 1 个 PDF 更新了，那么再次运行索引器时，它将只索引已更新的那一个 PDF。
+ 默认情况下，将从已编制索引的文档中提取文本和规范化图像。 可选择将技能组添加到管道中来进一步扩充内容。 有关技能组的详细信息，可查看 [Azure 认知搜索中的技能组概念](cognitive-search-working-with-skillsets.md)一文。

## <a name="supported-document-formats"></a>支持的文档格式

Azure 认知搜索 SharePoint Online 索引器可从以下文档格式提取文本：

[!INCLUDE [search-document-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="incremental-indexing-and-deletion-detection"></a>增量索引和删除检测
默认情况下，SharePoint Online 索引器支持增量索引，这意味着它会标识文档库中已更改的内容，并在今后索引运行时只索引已更新的内容。 例如，如果索引器最初索引了 5 个 Word 文档，随后 1 个文档更新了，那么再次运行索引器时，它将只重新索引已更新的那一个 Word 文档。

默认情况下，还支持删除检测。 这意味着，如果从 SharePoint Online 文档库中删除文档，索引器将在未来索引器运行期间检测该删除，并从索引中删除该文档。

## <a name="setting-up-sharepoint-online-indexing"></a>设置 SharePoint Online 索引
若要设置 SharePoint Online 索引器，需要在 Azure 门户中执行一些操作，并使用预览版 REST API 进行一些操作。 SDK 不支持此预览版。

 以下视频演示如何设置 SharePoint Online 索引器。
 
> [!VIDEO https://www.youtube.com/embed/QmG65Vgl0JI]

### <a name="step-1-optional-enable-system-assigned-managed-identity"></a>步骤 1（可选）：启用系统分配的托管标识

启用系统分配的托管标识后，Azure 将为搜索服务创建一个可供索引器使用的标识。 此标识用于自动检测在其中预配搜索服务的租户。

如果 SharePoint Online 站点与搜索服务位于同一租户中，则需要为搜索服务启用系统分配的托管标识。 如果 SharePoint Online 站点与搜索服务位于不同租户中，则不需要启用系统分配的托管标识。

![启用系统分配的托管标识](media/search-howto-index-sharepoint-online/enable-managed-identity.png "启用系统分配的托管标识")

选择“保存”后，将看到已分配给搜索服务的对象 ID。

![系统分配托管标识](media/search-howto-index-sharepoint-online/system-assigned-managed-identity.png "系统分配托管标识")

### <a name="step-2-create-an-aad-application"></a>步骤 2：创建 AAD 应用程序
SharePoint Online 索引器将使用此 AAD 应用程序进行身份验证。 

1.  导航到 [Azure 门户](https://portal.azure.com/)。

1.  打开主页左侧的菜单，选择“Azure Active Directory”，然后选择“应用注册” 。 选择“+新建注册”。
    1.  为应用提供一个名称。
    2.  选择“单个租户”。
    3.  无需重定向 URI。
    4.  选择“注册”

1.  从左侧菜单中选择“API 权限”，选择“添加权限”，然后依次选择“Microsoft Graph”和“委托的权限”   。 请添加以下 API 权限： 
    1.  **委托 - Files.Read.All** 
    2.  **委托 - Sites.Read.All** 
    3.  **委托 - User.Read**

    ![委托的 API 权限](media/search-howto-index-sharepoint-online/delegated-api-permissions.png "委托的 API 权限")

    使用委托权限意味着索引器将在用户上下文中访问 SharePoint 站点。 因此在运行索引器时，它只能访问登录用户有权访问的内容。 创建索引器或更新数据源时，会进行用户登录。 有关登录步骤，可查看本文稍后部分。

1.  选择“身份验证”选项卡。将“允许公共客户端流”设置为“是”，然后选择“保存”  。

1.  依次选择“+添加平台”和“移动和桌面应用程序”，选中 `https://login.microsoftonline.com/common/oauth2/nativeclient`，然后选择“配置”  。

    ![AAD 应用身份验证配置](media/search-howto-index-sharepoint-online/aad-app-authentication-configuration.png "AAD 应用身份验证配置")

1.  授予管理员同意（仅某些租户需要）。

    某些租户被锁定，需要管理员同意才能获取这些委托的 API 权限。 如果是这种情况，则需要在创建索引器之前，让管理员对此 AAD 应用程序授予管理员同意。

    并非所有租户都有此要求，因此建议先跳过此步骤，继续查看说明。 在创建索引器时，如果身份验证失败，告知需要管理员批准身份验证，你将知道是否需要管理员同意。 在这种情况下，请使用下面的按钮让租户管理员予以同意。

    ![AAD 应用授予管理员同意](media/search-howto-index-sharepoint-online/aad-app-grant-admin-consent.png "AAD 应用授予管理员同意")

<a name="create-data-source"></a>

### <a name="step-3-create-data-source"></a>步骤 3：创建数据源
> [!IMPORTANT] 
> 从这部分开始，你需要使用预览版 REST API 来执行其余步骤。 如果不熟悉 Azure 认知搜索 REST API，建议查看此[快速入门](search-get-started-rest.md)。

数据源指定要编制索引的数据、访问数据所需的凭据和有效标识数据更改（新行、修改的行或删除的行）的策略。 一个数据源可供同一搜索服务中的多个索引器使用。

对于 SharePoint 索引，数据源必须具有以下属性：
+ **name** 是搜索服务中数据源的唯一名称。
+ type 必须为“sharepoint”。 需要区分大小写。
+ credentials 提供 SharePoint Online 终结点和 AAD 应用程序（客户端）ID。 `https://microsoft.sharepoint.com/teams/MySharePointSite` 就是一个 SharePoint Online 终结点。 可导航到 SharePoint 站点的主页，并从浏览器复制 URL 来获取 SharePoint Online 终结点。
+ container 指定要索引的文档库。 若要详细了解如何创建容器，可查看本文档的[控制要索引哪些文档](#controlling-which-documents-are-indexed)部分。

创建数据源：

```http
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sharepoint-datasource",
    "type" : "sharepoint",
    "credentials" : { "connectionString" : "SharePointOnlineEndpoint=[SharePoint Online site url];ApplicationId=[AAD App ID];TenantId=[SharePoint Online site tenant id]" },
    "container" : { "name" : "defaultSiteLibrary", "query" : null }
}
```

> [!NOTE]
> 如果 SharePoint Online 站点与搜索服务位于同一租户中，并且启用了系统分配的托管标识，则 `TenantId` 无需包含在连接字符串中。 如果 SharePoint Online 站点与搜索服务位于不同租户中，则必须包含 `TenantId`。

### <a name="step-4-create-an-index"></a>步骤 4：创建索引
索引指定文档、属性和其他构造中可以塑造搜索体验的字段。

下面介绍如何创建包含可搜索内容字段的索引，以存储从文档库中的文档提取到的文本：

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

有关详细信息，请参阅[创建索引 (REST API)](/rest/api/searchservice/create-index)。

### <a name="step-5-create-an-indexer"></a>步骤 5：创建索引器
索引器将数据源与目标搜索索引关联，并提供自动执行数据刷新的计划。 创建索引和数据源后，就可创建索引器了！

在此部分中，需要使用有权访问 SharePoint 站点的组织凭据进行登录。 如果可能，建议创建新的组织用户帐户，并为该新用户提供希望索引器具有的确切权限。

创建索引器需要几步操作：

1.  发送请求以尝试创建索引器。

    ```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    
        {
          "name" : "sharepoint-indexer",
          "dataSourceName" : "sharepoint-datasource",
          "targetIndexName" : "sharepoint-index",
          "fieldMappings" : [
            { 
              "sourceFieldName" : "metadata_spo_site_library_item_id", 
              "targetFieldName" : "id", 
              "mappingFunction" : { 
                "name" : "base64Encode" 
              } 
            }
          ]
        }
    
    ```

1.  首次创建索引器时会失败，并且出现以下错误。 前往错误消息中的链接。 如果 10 分钟内未到达链接，代码将过期，需要重新创建[数据源](#create-data-source)。

    ```http
    {
        "error": {
            "code": "",
            "message": "Error with data source: To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code <CODE> to authenticate.  Please adjust your data source definition in order to proceed."
        }
    }
    ```

1.  提供错误消息中给出的代码。

    ![输入设备代码](media/search-howto-index-sharepoint-online/enter-device-code.png "输入设备代码")

1.  SharePoint 索引器将以登录用户的身份访问 SharePoint 内容。 在该步骤中登录的用户就是该登录用户。 因此，如果登录时使用的用户帐户无权访问文档库中你想要索引的文档，那么索引器将无法访问该文档。

    如果可能，建议创建新的用户帐户，并为该新用户提供希望索引器具有的确切权限。

1.  批准所请求的权限。

    ![审批 API 权限](media/search-howto-index-sharepoint-online/aad-app-approve-api-permissions.png "审批 API 权限")

1.  重新发送索引器创建请求。 这次，请求应会成功。 

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

### <a name="step-6-check-the-indexer-status"></a>步骤 6：检查索引器状态
创建索引器后，可发出以下请求来检查索引器状态。

```http
GET https://[service name].search.windows.net/indexers/sharepoint-indexer/status?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]
```

有关索引器状态的详细信息，可查看[获取索引器状态](/rest/api/searchservice/get-indexer-status)。

## <a name="updating-the-data-source"></a>更新数据源
如果数据源对象没有更新，则索引器可按计划运行，无需任何用户交互。 不过，每次更新 Azure 认知搜索数据源对象时，你都需要再次登录，这样索引器才会运行。 例如，如果更改数据源查询，则需要使用 `https://microsoft.com/devicelogin` 和新的代码再次登录。

数据源更新后，请执行以下步骤：

1.  手动启动索引器的运行。

    ```http
    POST https://[service name].search.windows.net/indexers/sharepoint-indexer/run?api-version=2020-06-30-Preview  
    Content-Type: application/json
    api-key: [admin key]
    ```

    若要详细了解索引器运行请求，可查看[运行索引器](/rest/api/searchservice/run-indexer)。

1.  检查索引器状态。 如果最后一次索引器运行出现错误，指示要前往 `https://microsoft.com/devicelogin`，请前往该页面并提供新代码。 

    ```http
    GET https://[service name].search.windows.net/indexers/sharepoint-indexer/status?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    ```

    有关索引器状态的详细信息，可查看[获取索引器状态](/rest/api/searchservice/get-indexer-status)。

1.  登录

1.  再次手动启动索引器运行并检查索引器状态。 这次，索引器运行应会成功启动。

## <a name="indexing-document-metadata"></a>索引文档元数据
如果已将索引器设置为索引文档元数据，则以下元数据将可用于索引。

> [!NOTE]
> 当前预览版中不包含自定义元数据。

| 标识符 | 类型 | 说明 | 
| ------------- | -------------- | ----------- |
| metadata_spo_site_library_item_id | Edm.String | 站点 ID、库 ID 和项 ID 的组合键，用于唯一标识站点文档库中的项。 |
| metadata_spo_site_id | Edm.String | SharePoint Online 站点的 ID。 |
| metadata_spo_library_id | Edm.String | 文档库的 ID。 |
| metadata_spo_item_id | Edm.String | 库中（文档）项的 ID。 |
| metadata_spo_item_last_modified | Edm.DateTimeOffset | 项的最后修改日期/时间 (UTC)。 |
| metadata_spo_item_name | Edm.String | 项的名称。 |
| metadata_spo_item_size | Edm.Int64 | 项的大小（以字节为单位）。 | 
| metadata_spo_item_content_type | Edm.String | 项的内容类型。 | 
| metadata_spo_item_extension | Edm.String | 项的扩展名。 |
| metadata_spo_item_weburi | Edm.String | 项的 URI。 |
| metadata_spo_item_path | Edm.String | 父路径和项名称的组合。 | 

SharePoint Online 索引器还支持每种文档类型特定的元数据。 有关详细信息，可查看 [Azure 认知搜索中使用的内容元数据属性](search-blob-metadata-properties.md)。

<a name="controlling-which-documents-are-indexed"></a>

## <a name="controlling-which-documents-are-indexed"></a>控制要索引哪些文档
一个 SharePoint Online 索引器可从一个或多个文档库中索引内容。 创建数据源时，请使用 container 参数来指示要索引的文档库。 数据源 container 有两个属性：name 和 query  。 

### <a name="name"></a>名称
name 属性是必需的，并且必须是以下三个值之一：
+ defaultSiteLibrary
    + 从站点默认文档库中索引所有内容。
+   allSiteLibraries
    + 从站点中的所有文档库中索引所有内容。 这不会从子网中索引文档库。 不过，可在 query 中指定这些内容。
+   useQuery
    + 仅索引在 query 中定义的内容。

### <a name="query"></a>查询
query 属性由关键字/值对组成。 下面是可使用的关键字。 这些值为站点 URL 或文档库 URL。

> [!NOTE]
> 若要获取特定关键字的值，建议在浏览器中打开 SharePoint Online，导航到想要包含/排除的文档库，并从浏览器复制相应的 URI。 这是获取要在查询中与关键字一起使用的值的最简便方法。

| 关键字 | 查询说明 | 示例 |
| ------------- | -------------- | ----------- |
| null | 如果为 null 或为空，则索引默认文档库或所有文档库，具体取决于容器名称。 | 从默认站点库中索引所有内容： <br><br>  ``` "container" : { "name" : "defaultSiteLibrary", "query" : null } ``` |
| includeLibrariesInSite | 从连接字符串中定义的站点内的所有库索引内容。 这些仅限于站点的子站点 <br><br> 此关键字的 query 值应为站点或子站点的 URI。 | 从 mysite 中的所有文档库中索引所有内容。 <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrariesInSite=https://mycompany.sharepoint.com/mysite" } ``` |
| includeLibrary | 从此库中索引内容。 <br><br> 此关键字的 query 值应采用以下格式之一： <br><br> 示例 1： <br><br> includeLibrary=[站点或子站点]/[文档库] <br><br> 示例 2： <br><br> 从浏览器复制的 URI。 | 从 MyDocumentLibrary 中索引所有内容： <br><br> 示例 1： <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrary=https://mycompany.sharepoint.com/mysite/MyDocumentLibrary" } ``` <br><br> 示例 2： <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrary=https://mycompany.sharepoint.com/teams/mysite/MyDocumentLibrary/Forms/AllItems.aspx" } ``` |
| excludeLibrary |  不从此库中索引内容。 <br><br> 此关键字的 query 值应采用以下格式之一： <br><br> 示例 1： <br><br> excludeLibrary=[站点或子站点 URI]/[文档库] <br><br> 示例 2： <br><br> 从浏览器复制的 URI。 | 从我的所有库中索引所有内容（MyDocumentLibrary 除外）： <br><br> 示例 1： <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrariesInSite=https://mysite.sharepoint.com/subsite1; excludeLibrary=https://mysite.sharepoint.com/subsite1/MyDocumentLibrary" } ``` <br><br> 示例 2： <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrariesInSite=https://mycompany.sharepoint.com/teams/mysite; excludeLibrary=https://mycompany.sharepoint.com/teams/mysite/MyDocumentLibrary/Forms/AllItems.aspx" } ``` |

## <a name="index-by-file-type"></a>按文件类型编制索引
可控制要索引哪些文档以及要跳过哪些文档。

### <a name="include-documents-having-specific-file-extensions"></a>包括具有特定文件扩展名的文档
可使用索引器配置参数 `indexedFileNameExtensions` 来仅索引具有你指定的文件扩展名的文档。 值是包含文件扩展名（包括前置句点）逗号分隔列表的字符串。 例如，若要只索引 .PDF 和 .DOCX 文档，可执行以下操作：

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
可使用配置参数 `excludedFileNameExtensions` 从索引中排除具有特定文件扩展名的文档。 值是包含文件扩展名（包括前置句点）逗号分隔列表的字符串。 例如，若要索引除具有 .PNG 和 .JPEG 扩展名的内容之外的所有内容，可执行以下操作：

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
默认情况下，SharePoint Online 索引器一旦遇到包含不受支持的内容类型（例如图像）的文档时，就会立即停止。 当然，可以使用 `excludedFileNameExtensions` 参数跳过某些内容类型。 但是，可能需要在未提前知道所有可能的内容类型的情况下索引文档。 若要在遇到不受支持的内容类型时继续索引，可将配置参数 `failOnUnsupportedContentType` 设置为 false：

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    ... other parts of indexer definition
    "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false } }
}
```

对于某些文档，Azure 认知搜索无法确定其内容类型，或者无法处理其他受支持内容类型的文档。 若要忽略此故障模式，将 `failOnUnprocessableDocument` 配置参数设置为 false：

```http
"parameters" : { "configuration" : { "failOnUnprocessableDocument" : false } }
```

Azure 认知搜索会限制索引的文档的大小。 这些限制记录在 [Azure 认知搜索中的服务限制](./search-limits-quotas-capacity.md)中。 文档太大会被默认视为错误。 不过，如果将配置参数 `indexStorageMetadataOnlyForOversizedDocuments` 设为 true，则仍可索引过大文档的存储元数据：

```http
"parameters" : { "configuration" : { "indexStorageMetadataOnlyForOversizedDocuments" : true } }
```

如果在任意处理点发生错误（无论是在分析文档时，还是在将文档添加到索引时），都仍可继续索引。 若要忽略特定的错误数，将 `maxFailedItems` 和 `maxFailedItemsPerBatch` 配置参数设置为所需值。 例如：

```http
{
    ... other parts of indexer definition
    "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
}
```

## <a name="see-also"></a>另请参阅
+ [Azure 认知搜索中的索引器](search-indexer-overview.md)
+ [Azure 认知搜索中使用的内容元数据属性](search-blob-metadata-properties.md)
