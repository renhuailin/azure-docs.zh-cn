---
title: 媒体服务 v2 到 v3 迁移设置
description: 本文将帮助你设置用于从 Azure 媒体服务 v2 迁移到 v3 的环境。
services: media-services
author: IngridAtMicrosoft
manager: femila
editor: ''
tags: ''
keywords: azure 媒体服务, 迁移, 流式传输, 广播, 实时, SDK
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: c2a08059de275efcfb3e65c6a316566a6a081830
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129357948"
---
# <a name="step-3---set-up-to-migrate-to-the-v3-rest-api-or-client-sdk"></a>步骤 3 - 进行设置，以迁移到 V3 REST API 或客户端 SDK

![迁移指南徽标](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![迁移步骤 2](./media/migration-guide/steps-3.svg)

下文描述了将环境设置为使用媒体服务 V3 API 需要执行的步骤。

## <a name="sdk-model"></a>SDK 模型

V2 API 中有两个不同的客户端 SDK，一个用于管理 API，可同构它以编程方式创建帐户，而另一个用于管理资源。

以前，除了 AMS 帐户的特定 V2 REST API 终结点，开发人员还会使用 Azure 服务主体客户端 ID 和客户端密码。

V3 API 基于 Azure 资源管理 (ARM)。 它使用 Azure Active Directory (Azure AD) 服务主体 ID 和密钥连接到 API。 开发人员将需要创建服务主体或托管标识才能连接到 API。 在 V3 API 中，API 会使用标准 ARM 终结点，对其他所有 Azure 服务使用相似且一致的模型。

以前使用 2015-10-01 版本的 ARM 管理 API 来管理其 V2 帐户的客户应使用 2020-05-01（或更新）版本的 ARM 管理 API，新版本支持 V3 API 访问。

## <a name="create-a-new-media-services-account-for-testing"></a>创建新的媒体服务帐户进行测试

在 Azure 门户中，按照有关[设置环境](setup-azure-subscription-how-to.md?tabs=portal)的快速入门步骤进行操作。 选择 API 访问和服务主体身份验证，来生成用于此测试帐户的新的 Azure AD 应用程序 ID 和机密。

[创建媒体服务帐户](account-create-how-to.md?tabs=portal)。
[获取用于访问媒体服务 API 的凭据](access-api-howto.md?tabs=portal)。

## <a name="download-client-sdk-of-your-choice-and-set-up-your-environment"></a>下载所选的客户端 SDK 并设置环境

- 适用于  [.NET](/dotnet/api/overview/azure/mediaservices/management)、.NET Core、 [Node.js](/javascript/api/overview/azure/mediaservices/management)、 [Python](/python/api/overview/azure/mediaservices/management)、 [Java](/java/api/overview/azure/mediaservices/management)、 [Go](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/mediaservices/mgmt/2018-07-01/media) 和 [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md) 的 SDK。
- 用于简化脚本支持的 [Azure CLI](/cli/azure/ams)  集成。

> [!NOTE]
> 社区 PHP SDK 无法再用于 V3 上的 Azure 媒体服务。 如果正在 V2 中使用 PHP，应直接在代码中迁移到 REST API。

## <a name="open-api-specifications"></a>Open API 规范

- V3 基于统一的 API 接口，该接口公开了在 Azure 资源管理器之上构建的管理和操作功能。 Azure 资源管理器模板可用于创建和部署转换、流式处理终结点、直播活动等等。

- [OpenAPI 规范](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01)（之前称为 Swagger）文档说明了所有服务组件的架构。

- 所有客户端 SDK 都是从 GitHub 上发布的 Open API 规范派生和生成的。 在本文发布时，最新的 Open API 规范在 GitHub 中公开进行维护。 2020-05-01 版本是[最新的稳定版本](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01)。

## <a name="rest"></a>[REST](#tab/rest)

使用 [Postman](./setup-postman-rest-how-to.md) 进行媒体服务 v3 REST API 调用。
请阅读 [REST API 参考页面](/rest/api/media/)。

应在 Postman 集合中使用 2020-05-01（或更新）版本的字符串。

## <a name="net"></a>[.NET](#tab/net)

若要设置环境，请阅读[使用 .NET 连接到媒体服务 v3 API](configure-connect-dotnet-howto.md) 一文。

如果只想使用 PackageManager 安装最新的 SDK，请使用以下命令：

`Install-Package Microsoft.Azure.Management.Media`

若要使用 .NET CLI 安装最新的 SDK，请使用以下命令：

`dotnet add package Microsoft.Azure.Management.Media`

此外，对于各种方案，都可在 [Azure-Samples/media-services-v3-dotnet](https://github.com/Azure-Samples/media-services-v3-dotnet) 中找到完整的 .NET 示例。 此存储库中的项目演示了如何使用 v3 版本实现不同的 Azure 媒体服务方案。

### <a name="get-started-adjusting-your-code"></a>开始调整代码

在代码库中搜索 `CloudMediaContext` 使用实例，开始升级到 V3 API 的过程。

下面的代码演示了以前是如何使用 v2 .NET SDK 访问 v2 API 的。 开发人员会先创建一个 `CloudMediaContext` 和一个包含 `AzureAdTokenCredentials` 对象的实例。

```dotnet

class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        private static CloudMediaContext _context = null;

        static void Main(string[] args)
        {
        try
        {
            AzureAdTokenCredentials tokenCredentials = 
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

```

## <a name="java"></a>[Java](#tab/java)

若要设置环境，请阅读[使用 Java 连接到媒体服务 v3 API](configure-connect-java-howto.md) 一文。

## <a name="python"></a>[Python](#tab/python)

若要设置环境，请阅读[连接到 Azure 媒体服务 v3 API - Python](configure-connect-python-howto.md) 一文。

## <a name="nodejs"></a>[Node.js](#tab/nodejs)

若要设置环境，请参阅[连接到 Azure 媒体服务 v3 API - Node.js](configure-connect-nodejs-howto.md) 一文。

## <a name="ruby"></a>[Ruby](#tab/ruby)

- 在 GitHub 上获取 [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md) SDK。

## <a name="go"></a>[Go](#tab/go)

下载 [Go](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/mediaservices/mgmt/2018-07-01/media) SDK。

---
