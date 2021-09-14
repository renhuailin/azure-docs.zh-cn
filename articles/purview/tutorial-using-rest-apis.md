---
title: 教程：使用 REST API
description: 本教程介绍如何使用 Azure Purview REST API 访问目录的内容。
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/03/2020
ms.openlocfilehash: de49c820100140f0822149bba84f7a38f034323b
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2021
ms.locfileid: "123473273"
---
# <a name="tutorial-use-the-rest-apis"></a>教程：使用 REST API

本教程介绍如何使用 Azure Purview REST API。 任何人，如果想要将数据提交到 Azure Purview 目录、将目录作为自动化过程的一部分包括在内，或者在目录上构建自己的用户体验，都可以使用 REST API 来实现。

在本教程中，你将了解如何：

> [!div class="checklist"]
>
> * 创建服务主体（应用程序）。
> * 配置目录以信任服务主体（应用程序）。
> * 查看 REST API 文档。
> * 收集使用 REST API 时所需的值。
> * 使用 Postman 客户端调用 REST API。
> * 生成 .NET 客户端以调用 REST API。

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

## <a name="prerequisites"></a>先决条件

* 若要开始体验，必须具有现有的 Azure Purview 帐户。 如果你没有目录，请参阅[创建 Azure Purview 帐户的快速入门](create-catalog-portal.md)。

* 如果需要将数据添加到目录中，请参阅[运行初学者工具包和扫描数据的教程](tutorial-scan-data.md)。

## <a name="sign-in-to-azure"></a>登录 Azure

登录 [Azure 门户](https://portal.azure.com)。

## <a name="create-a-service-principal-application"></a>创建服务主体（应用程序）

要让 REST API 客户端访问目录，客户端必须具有服务主体（应用程序）和目录可识别并配置为信任的标识。 对目录进行 REST API 调用时，它们会使用服务主体的标识。

使用现有服务主体（应用程序 ID）的客户失败率很高。 因此，建议创建新的服务主体来调用 API。

创建新的服务主体：

1. 从 [Azure 门户](https://portal.azure.com)，搜索并选择“Azure Active Directory”。
1. 在“Azure Active Directory”页面，从左窗格中选择“应用注册” 。
1. 选择“新注册”。
1. 在“注册应用程序”页面上：
    1. 为应用程序输入名称（服务主体名称）。
    1. 选择“仅此组织目录中的帐户(仅 _&lt;租户名称&gt;_ - 单个租户)”。
    1. 对于“重定向 URI (可选)”，选择“Web”并输入一个值 。 此值无需是有效的 URI。
    1. 选择“注册”  。
1. 在“新建服务主体”页上，复制“显示名称”和“应用程序(客户端) ID”的值，保存供稍后使用 。

   应用程序 ID 是示例代码中的 `client_id` 值。

若要使用服务主体（应用程序），需要获取其密码。 下面介绍如何操作：

1. 从 Azure 门户，搜索并选择 Azure Active Directory，然后从左窗格中选择“应用注册” 。
1. 从列表中选择服务主体（应用程序）。
1. 从左窗格中选择“证书和机密”。
1. 选择“新建客户端机密”。
1. 在“客户端机密”页面上，输入“说明”，在“过期”下选择一个过期时间，然后选择“添加”   。

   在“客户端机密”页上，新机密的“值”列中的字符串就是你的密码 。 保存此值。

   :::image type="content" source="./media/tutorial-using-rest-apis/client-secret.png" alt-text="显示客户端机密的屏幕截图。":::

## <a name="configure-your-catalog-to-trust-the-service-principal-application"></a>配置目录以信任服务主体（应用程序）

若要配置 Azure Purview 以信任新的服务主体，请执行以下操作：

1. 导航到你的 Purview 帐户

1. 在“Purview 帐户”页，打开“Purview Studio” 

1. 在左侧菜单中选择“数据映射”。

1. 选择“集合”

1. 在“集合”菜单中选择根集合。 这将是列表中的顶级集合，并将与你的 Purview 帐户同名。

1. 选择“角色分配”选项卡。

1. 向下滚动到“数据管理者”，然后选择 + 用户按钮 。

    > [!Note]
    > 只有集合管理员才可以编辑集合的权限。 如果你不是集合管理员，请联系根集合中列出的管理员之一。 如需了解详细信息，请参阅[“Purview 权限”页](catalog-permissions.md)。

1. 搜索之前创建的要分配的服务主体的名称，然后在结果窗格中单击其名称。

1. 单击“保存”

现在，你已将服务主体配置为应用程序管理员，这使它能够将内容发送到目录。

## <a name="view-the-rest-apis-documentation"></a>查看 REST API 文档

若要查看 API Swagger 文档，请下载 [PurviewCatalogAPISwagger.zip](https://github.com/Azure/Purview-Samples/raw/master/rest-api/PurviewCatalogAPISwagger.zip)，提取其文件并打开 index.html。

如需深入了解有关 Azure Purview 提供的高级搜索/建议 API 的信息，请参阅“Rest API 搜索筛选器”文档。 AutoRest 生成的客户端当前不支持自定义搜索参数。 若要解决此问题，请遵循搜索筛选器文档，在代码中将筛选器类定义为 API 调用参数。 Index.html 文档中包含这些 API 的示例。

## <a name="collect-the-necessary-values-to-use-the-rest-apis"></a>收集使用 REST API 时所需的值

查找并保存以下值：

* 租户 ID：
  * 在 [Azure 门户](https://portal.azure.com)中，搜索并选择“Azure Active Directory”。
  * 在左窗格的“管理”部分，选择“属性”，找到“租户 ID”，然后选择“复制到剪贴板”图标以保存其值   。
* Atlas 终结点：
  * 从 Azure 门户的 [Azure Purview 帐户页面](https://aka.ms/purviewportal)，在列表中找到并选择你的 Azure Purview 帐户。
  * 选择“属性”，找到“Atlas 终结点”，然后选择“复制到剪贴板”图标以保存其值  。 以后使用该字符串时，请删除其中的 https:// 部分。
* 帐户名：
  * 从 Atlas 终结点字符串中提取目录的名称。 例如，如果 Atlas 终结点是 `https://ThisIsMyCatalog.catalog.purview.azure.com`，则帐户名是 `ThisIsMyCatalog`。

## <a name="use-the-postman-client-to-call-the-rest-apis"></a>使用 Postman 客户端调用 REST API

1. 安装 [Postman 客户端](https://www.getpostman.com/)。
1. 在客户端中，选择“导入”并使用 [Test.postman_collection.json](https://raw.githubusercontent.com/Azure/Purview-Samples/master/rest-api/Test.postman_collection.json)。
1. 选择“集合”，然后选择“测试” 。
1. 选择“获取令牌”：
    1. 在 POST 旁边的 URL 中，将 &lt;your-tenant-id&gt; 替换为你在上一部分中复制的租户 ID。
    1. 选择“正文”选项卡，替换上一步中路径和正文中的占位符。

       选择“发送”之后，响应正文会包含一个 JSON 结构，其中包括名称 access_token 和带引号的字符串值。 
    1. 复制持有者令牌值（不带引号），以便在下一步中使用。

1. 选择 /v2/types/typedefs：
    1. 将路径中的占位符替换为 atlas 终结点值。 可通过导航到 Ibiza 门户上的目录实例并单击概述来获取该值。 

       持有者令牌需在第一步中进行设置（也可在“授权”选项卡中手动复制此令牌）。

    1. 选择“发送”以获取响应。

## <a name="generate-a-net-client-to-call-the-rest-apis"></a>生成 .NET 客户端以调用 REST API

### <a name="install-autorest"></a>安装 AutoRest



1. [安装 Node.js](https://github.com/Azure/autorest/blob/v2/docs/installing-autorest.md)。
1. 打开 PowerShell 并运行以下命令：

   ```powershell
   npm install -g autorest@V2
   ```

### <a name="download-rest-api-specszip-and-create-the-client"></a>下载 rest-api-specs.zip 并创建客户端

1. 下载 [rest-api-specs.zip](https://github.com/Azure/Purview-Samples/raw/master/rest-api/rest-api-specs.zip) 并提取其文件。
1. 从提取的 rest-api-specs 文件夹中，在 PowerShell 中运行以下命令：

   ```powershell
   autorest --input-file=data-plane/preview/purviewcatalog.json --csharp --output-folder=PurviewCatalogClient/CSharp --namespace=PurviewCatalog --add-credentials
   ```

   此进程的输出将在 rest-api-specs 文件夹中创建 PurviewCatalogClient 文件夹和 CSharp 子文件夹。

### <a name="create-a-sample-net-console-application"></a>创建示例 .NET 控制台应用程序

1. 打开 Visual Studio 2019。 这些说明已在免费社区版中进行测试。
1. 在“创建新项目”页面中，使用 C# 创建“控制台应用 (.NET Core)”项目 。
1. 复制并粘贴提供的[示例代码](#sample-code-for-the-console-application)。
1. 将“accountName”、“servicePrincipalId”、“servicePrincipalKey”和“tenantId”替换为以前收集的值   。
1. 使用解决方案资源管理器在 Visual Studio 项目中添加一个“Generated”文件夹 。
1. 将先前创建的 rest-api-specs\PurviewCatalogClient\CSharp 文件夹复制到 \Generated 文件夹。 使用文件资源管理器或命令行提示符执行复制操作，这将触发 Visual Studio 自动将文件添加到项目中。
1. 在“解决方案资源管理器”中，右键单击项目，然后选择“管理 NuGet 包” 。
1. 选择“浏览”按钮。找到并选择“Microsoft.Rest.ClientRuntime”。
1. 确保版本至少为 2.3.21，然后选择“安装”。
1. 生成并运行应用程序。

示例代码返回目录中 typedef 的计数，并显示如何处理角色分配。 有关详细信息，请参见示例代码中的 `DoRoleAssignmentOperations()`。 有关项目的详细信息，请参阅[项目设置](https://github.com/Azure/autorest/blob/v2/docs/client/proj-setup.md)。

### <a name="sample-code-for-the-console-application"></a>控制台应用程序的示例代码

```csharp
using Microsoft.Rest;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;

namespace PurviewCatalogSdkTest
{
    class Program
    {
        private static string accountName = "{account-name}";
        private static string servicePrincipalId = "{service-principal-id}";
        private static string servicePrincipalKey = "{service-principal-key}";
        private static string tenantId = "{tenant-id}";

        static void Main(string[] args)
        {
            Console.WriteLine("Azure Purview client");

            // You need to change the api path below (e.g. /api) based on what you're trying to call
            string baseUri = string.Format("https://{0}.catalog.purview.azure.com/api", accountName);

            // Get token and set auth
            var svcClientCreds = new TokenCredentials(getToken(), "Bearer");
            var client = new PurviewCatalog.PurviewCatalogServiceRESTAPIDocument(svcClientCreds);
            client.BaseUri = new System.Uri(baseUri);

            // /v2/types/typedefs
            var task = client.TypesREST.GetAllTypeDefsWithHttpMessagesAsync();
            Console.WriteLine("\nURI:\n" + task.Result.Request.RequestUri);
            Console.WriteLine("\nStatus Code:\n" + task.Result.Response.StatusCode);
            Console.WriteLine("\nResult:\n" + JsonConvert.SerializeObject(task.Result.Body, Formatting.Indented));
        }

        // Replace client_id and client_secret with application ID and password value from service principal
        private static string getToken()
        {
            var values = new Dictionary<string, string>

            // The "resource" could be "https://purview.azure.net" or "73c2949e-da2d-457a-9607-fcc665198967"
            {
                { "grant_type", "client_credentials" },
                { "client_id", servicePrincipalId },
                { "client_secret", servicePrincipalKey },
                { "resource", "73c2949e-da2d-457a-9607-fcc665198967" }
            };

            string authUrl = string.Format("https://login.windows.net/{0}/oauth2/token", tenantId);
            var content = new FormUrlEncodedContent(values);

            HttpClient authClient = new HttpClient();
            var bearerResult = authClient.PostAsync(authUrl, content);
            bearerResult.Wait();
            var resultContent = bearerResult.Result.Content.ReadAsStringAsync();
            resultContent.Wait();
            var bearerToken = JObject.Parse(resultContent.Result)["access_token"].ToString();
            var svcClientCreds = new TokenCredentials(bearerToken, "Bearer");

            return bearerToken;
        }
    }
}
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [管理数据源](manage-data-sources.md)
