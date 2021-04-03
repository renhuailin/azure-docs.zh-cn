---
title: 使用 Active Directory 修剪结果的安全筛选器
titleSuffix: Azure Cognitive Search
description: 了解如何使用安全筛选器和 Azure Active Directory (AD) 标识在文档级别针对 Azure 认知搜索的搜索结果实施安全权限。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/16/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 5788585b2365b12a90a508e5a972b61f73e48c15
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "97629504"
---
# <a name="security-filters-for-trimming-azure-cognitive-search-results-using-active-directory-identities"></a>用于使用 Active Directory 标识修剪 Azure 认知搜索结果的安全筛选器

本文演示如何结合使用 Azure Active Directory (AD) 安全标识和 Azure 认知搜索中的筛选器基于用户组成员身份来修剪搜索结果。

本文涵盖以下任务：
> [!div class="checklist"]
> - 创建 Azure AD 组和用户
> - 将用户与创建的组相关联
> - 缓存新组
> - 使用关联的组编制文档索引
> - 使用组标识符筛选器发出搜索请求
> 
> [!NOTE]
> 本文中的示例代码片段是用 C# 语言编写的。 可以 [在 GitHub 上](https://github.com/Azure-Samples/search-dotnet-getting-started)找到完整的源代码。 

## <a name="prerequisites"></a>先决条件

Azure 认知搜索中的索引必须有一个[安全字段](search-security-trimming-for-azure-search.md)用于存储对文档拥有读取访问权限的组标识列表。 此用例假设某个安全对象项（例如个人的大学申请）与指定谁有权访问该项（招生人员）的安全字段之间存在一对一的对应关系。

在本演练中，你必须拥有 Azure AD 管理员权限才能创建用户、组和关联。 

此外，你的应用程序还必须在 Azure AD 中注册为多租户应用，如以下过程所述。

### <a name="register-your-application-with-azure-active-directory"></a>将应用程序注册到 Azure Active Directory

此步骤将应用程序与 Azure AD 集成，以接受用户和组帐户的登录。 如果你不是组织的租户管理员，则可能需要[创建新租户](../active-directory/develop/quickstart-create-new-tenant.md)来执行以下步骤。

1. 在 [Azure 门户](https://portal.azure.com)中，查找你的订阅的 Azure Active Directory 资源。

1. 在左侧的“管理”下选择“应用注册”，然后选择“新建注册”  。

1. 为注册提供一个名称，该名称可以是与搜索应用程序名称类似的名称。 选择“注册”。

1. 创建应用注册后，复制应用程序 ID。 需要将此字符串提供给应用程序。

   如果要单步执行 [DotNetHowToSecurityTrimming](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)，请将此值粘贴到 app.config 文件中。

   为租户 ID 重复此操作。

   :::image type="content" source="media/search-manage-encryption-keys/cmk-application-id.png" alt-text="“概要”部分的应用程序 ID":::

1. 在左侧，依次选择“API 权限”、“添加权限”。  

1. 依次选择“Microsoft Graph”、“委托的权限”。 

1. 搜索并添加以下委托的权限：

   - **Directory.ReadWrite.All**
   - **Group.ReadWrite.All**
   - **User.ReadWrite.All**

可以使用 Microsoft Graph 提供的某个 API 通过 REST API 以编程方式访问 Azure AD。 本演练的代码示例使用调用 Microsoft 图形 API 的权限来创建组、用户和关联。 还可以使用 API 缓存组标识符以提高筛选速度。

## <a name="create-users-and-groups"></a>创建用户和组

如果你正在向建立的应用程序添加搜索，Azure AD 中可能已包含现有的用户和组标识符。 在这种情况下，可以跳过接下来的三个步骤。 

但是，如果没有现有用户，可以使用 Microsoft 图形 API 创建安全主体。 以下代码片段演示如何生成标识符，这些标识符将成为 Azure 认知搜索索引中安全字段的数据值。 在虚构的大学招生应用程序中，这些标识符将是招生工作人员的安全标识符。

用户和组的成员身份可能很不稳定，尤其是在大型组织中。 生成用户和组标识的代码应该以足够高的频率运行，以拾取组织成员身份的更改。 同样，Azure 认知搜索索引需有类似的更新计划，以反映受允许用户和资源的当前状态。

### <a name="step-1-create-group"></a>步骤 1：[创建组](/graph/api/group-post-groups) 

```csharp
private static Dictionary<Group, List<User>> CreateGroupsWithUsers(string tenant)
{
    Group group = new Group()
    {
        DisplayName = "My First Prog Group",
        SecurityEnabled = true,
        MailEnabled = false,
        MailNickname = "group1"
    };
```

### <a name="step-2-create-user"></a>步骤 2：[创建用户](/graph/api/user-post-users)

```csharp
User user1 = new User()
{
    GivenName = "First User",
    Surname = "User1",
    MailNickname = "User1",
    DisplayName = "First User",
    UserPrincipalName = String.Format("user1@{0}", tenant),
    PasswordProfile = new PasswordProfile() { Password = "********" },
    AccountEnabled = true
};
```

### <a name="step-3-associate-user-and-group"></a>步骤 3：将用户和组相关联

```csharp
List<User> users = new List<User>() { user1, user2 };
Dictionary<Group, List<User>> groups = new Dictionary<Group, List<User>>() { { group, users } };
```

### <a name="step-4-cache-the-groups-identifiers"></a>步骤 4：缓存组标识符

（可选）为了降低网络延迟，可以缓存用户与组之间的关联，以便在发出搜索请求后，可以从缓存返回组，免除与 Azure AD 之间的一次往返。 可以使用 [Azure AD Batch API](/graph/json-batching) 发送包含多个用户的单个 Http 请求并生成缓存。

Microsoft Graph 能够处理大量的请求。 如果发出无以数计的请求，Microsoft Graph 将会失败并返回 HTTP 状态代码 429。 有关详细信息，请参阅 [Microsoft Graph 限制](/graph/throttling)。

## <a name="index-document-with-their-permitted-groups"></a>使用受允许的组编制文档索引

Azure 认知搜索中的查询操作是基于 Azure 认知搜索索引执行的。 在此步骤中，索引操作会将可搜索的数据（包括用作安全筛选器的标识符）导入索引。 

Azure 认知搜索不会验证用户的身份，也不提供逻辑来确定用户有权查看哪些内容。 安全修整的用例假设在某个敏感文档与有权访问该文档的组标识符之间提供了关联，并将这种关联按原样导入了搜索索引。 

在虚构的示例中，Azure 认知搜索索引中 PUT 请求的正文应包含申请者的大学申请短文或成绩报告单，以及有权查看该内容的组标识符。 

在本演练的代码示例使用的常规示例中，索引操作可能如下所示：

```csharp
private static void IndexDocuments(string indexName, List<string> groups)
{
    IndexDocumentsBatch<SecuredFiles> batch = IndexDocumentsBatch.Create(
        IndexDocumentsAction.Upload(
            new SecuredFiles()
            {
                FileId = "1",
                Name = "secured_file_a",
                GroupIds = new[] { groups[0] }
            }),
              ...
            };

IndexDocumentsResult result = searchClient.IndexDocuments(batch);
```

## <a name="issue-a-search-request"></a>发出搜索请求

为了进行安全修整，索引的安全字段中的值是静态值，用于在搜索结果中包含或排除文档。 例如，如果招生人员的组标识符为“A11B22C33D44-E55F66G77-H88I99JKK”，则会在发回给请求者的搜索结果中，包含（或排除）安全字段包含该标识符的 Azure 认知搜索索引中的所有文档。

若要基于发出请求的用户组筛选搜索结果中返回的文档，请查看以下步骤。

### <a name="step-1-retrieve-users-group-identifiers"></a>步骤 1：检索用户的组标识符

如果用户的组尚未缓存或缓存已过期，请发出[组](/graph/api/directoryobject-getmembergroups)请求。

```csharp
private static async void RefreshCache(IEnumerable<User> users)
{
    HttpClient client = new HttpClient();
    var userGroups = await _microsoftGraphHelper.GetGroupsForUsers(client, users);
    _groupsCache = new ConcurrentDictionary<string, List<string>>(userGroups);
}
```

### <a name="step-2-compose-the-search-request"></a>步骤 2：撰写搜索请求

假设你拥有用户的组成员身份，则可以使用相应的筛选器值发出搜索请求。

```csharp
private static void SearchQueryWithFilter(string user)
{
    // Using the filter below, the search result will contain all documents that their GroupIds field   
    // contain any one of the Ids in the groups list
    string filter = String.Format("groupIds/any(p:search.in(p, '{0}'))", string.Join(",", String.Join(",", _groupsCache[user])));
    SearchOptions searchOptions =
        new SearchOptions()
        {
            Filter = filter
        };
    searchOptions.Select.Add("name");

    SearchResults<SecuredFiles> results = searchClient.Search<SecuredFiles>("*", searchOptions);

    Console.WriteLine("Results for groups '{0}' : {1}", _groupsCache[user], results.GetResults().Select(r => r.Document.Name));
}
```

### <a name="step-3-handle-the-results"></a>步骤 3：处理结果

响应包含文档的筛选列表，该列表由用户有权查看的文档构成。 根据搜索结果页的构造方式，可能需要包含视觉线索来反映筛选的结果集。

## <a name="next-steps"></a>后续步骤

在本演练中，你已了解了如何通过一种模式使用 Azure AD 登录名筛选 Azure 认知搜索结果中的文档，以及修剪与请求中提供的筛选器不匹配的文档结果。 若要了解可能更简单的备选模式，或者要重新访问其他安全功能，请参阅以下链接。

- [用于修整结果的安全筛选器](search-security-trimming-for-azure-search.md)
- [Azure 认知搜索中的安全性](search-security-overview.md)