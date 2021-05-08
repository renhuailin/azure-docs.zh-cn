---
title: 为 Azure Active Directory B2C 配置 Onfido 的教程
titleSuffix: Azure AD B2C
description: 了解如何将 Azure AD B2C 身份验证与 Onfido 集成以进行文档 ID 和面部生物识别验证
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/03/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 1441210303984bf777c6b580f6f7ab0c67ce6f21
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2021
ms.locfileid: "107257851"
---
# <a name="tutorial-for-configuring-onfido-with-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中配置 Onfido 的教程

在本示例教程中，我们提供有关如何将 Azure AD B2C 与 [Onfido](https://onfido.com/) 集成的指导。 Onfido 是一个文档 ID 和面部生物识别验证应用。 利用该应用，公司可以实时了解你的客户和标识要求。 Onfido 使用基于 AI 的复杂身份验证，这种验证方式首先验证照片 ID，然后将照片 ID 与它们的面部生物识别进行匹配。 此解决方案将数字标识与它们的真实人员联系在一起，并在降低欺诈的同时提供安全的加入体验。

在此示例中，我们在注册或登录流中连接 Onfido 的服务来进行身份验证。 根据 Onfido 的结果，做出用户可以访问哪些产品和服务的明智决策。

## <a name="prerequisites"></a>先决条件

若要开始，需要：

- 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。

- 一个已链接到 Azure 订阅的 [Azure AD B2C 租户](./tutorial-create-tenant.md)。

- Onfido [试用版帐户](https://onfido.com/signup/)。

## <a name="scenario-description"></a>方案描述

Onfido 集成包括以下组件：

- Azure AD B2C 租户 – 授权服务器，负责根据该租户中定义的自定义策略来验证用户的凭据。 它也被称为“标识提供者”。 它承载 Onfido 客户端应用，该应用收集用户文档并将这些文档传输到 Onfido API 服务。

- Onfido 客户端 – 可配置的 JavaScript 客户端文档收集实用工具，部署在其他网页中。 收集文档并执行初步检查，如文档大小和质量。

- 中间 Rest API – 提供 Azure AD B2C 租户的终结点，以便与 Onfido API 服务通信，控制数据处理并遵守这两项 API 的安全要求。

- Onfido API 服务 – Onfido 提供的后端服务，用于保存和验证由用户提供的文档。

以下体系结构图体现了实施详情。

![onfido 体系结构图的屏幕截图](media/partner-onfido/onfido-architecture-diagram.png)

|步骤 | 说明 |
|:-----| :-----------|
| 1. | 用户访问登录页。 用户注册以创建新帐户，并在该页面中输入信息。 Azure AD B2C 收集用户属性。 在 Azure AD B2C 中托管的 Onfido 客户端应用会初步检查用户信息。
| 2. | Azure AD B2C 调用中间层 API 并传递用户属性。
| 3. | 中间层 API 收集用户属性，并将其转换为 Onfido API 可以使用的格式。 然后，将其发送到 Onfido。
| 4. | Onfido 使用这些信息并对其进行处理，以验证用户标识。 然后，它会将结果返回到中间层 API。
| 5. | 中间层 API 处理这些信息，并将相关信息以正确的 JSON 格式发送回 Azure AD B2C。
| 6. | Azure AD B2C 接收从中间层 API 返回的信息。 如果它显示“失败”响应，则会向用户显示错误消息。 如果它显示“成功”响应，则会对用户进行身份验证并将该用户写入目录。

## <a name="onboard-with-onfido"></a>通过 Onfido 加入

1. 若要创建 Onfido 帐户，请联系 [Onfido](https://onfido.com/signup/)。

2. 在创建帐户后，请创建 [API 密钥](https://documentation.onfido.com/)。 实时密钥是可计费的，但是，你可以使用[沙盒密钥来测试](https://documentation.onfido.com/?javascript#sandbox-and-live-differences)解决方案。 沙盒密钥生成与实时密钥相同的结果结构，但是，这些结果始终是预先确定的。 不会处理或保存文档。

>[!NOTE]
> 稍后你会需要该密钥。

有关 Onfido 的详细信息，请参阅 [Onfido API 文档](https://documentation.onfido.com)和 [Onfido 开发人员中心](https://developers.onfido.com)。  

## <a name="configure-azure-ad-b2c-with-onfido"></a>为 Azure AD B2C 配置 Onfido

### <a name="part-1---deploy-the-api"></a>第 1 部分 - 部署 API

- 将提供的 [API 代码](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/OnFido-Combined/API/Onfido.Api)部署到 Azure 服务。 可以按照这些[说明](/visualstudio/deployment/quickstart-deploy-to-azure)从 Visual Studio 发布代码。
- 设置 CORS，将允许的原点添加为 https://{your_tenant_name}.b2clogin.com

>[!NOTE]
>你将会需要已部署的服务的 URL，以便为 Azure AD 配置所需的设置。

#### <a name="adding-sensitive-configuration-settings"></a>添加敏感配置设置

可以在 [Azure 中的应用服务](../app-service/configure-common.md#configure-app-settings)中配置应用程序设置。 应用服务允许安全地配置设置，而无需将其签入存储库。 Rest API 需要以下设置：

| 应用程序设置名称 | Source | 注释 |
|:-------------------------|:-------|:-------|
|OnfidoSettings:AuthToken| Onfido 帐户 |

### <a name="part-2---deploy-the-ui"></a>第 2 部分 - 部署 UI

#### <a name="configure-your-storage-location"></a>配置存储位置

1. 设置[存储帐户中的 Blob 存储容器](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)

2. 将 UI 文件从 [UI 文件夹](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/OnFido-Combined/UI)存储到 Blob 容器。

3. 通过按照以下说明操作来允许 CORS 访问你创建的存储容器：

   a. 转到“设置” >“允许的原点”，输入 `https://{your_tenant_name}.b2clogin.com` 。 将 your-tenant-name 替换为 Azure AD B2C 租户的名称。 例如 https://fabrikam.b2clogin.com 。 在输入租户名称时，请使用全小写字母。

   b. 对于“允许的方法”，请选择 `GET` 和 `PUT`。

   c. 选择“保存”。

#### <a name="update-ui-files"></a>更新 UI 文件

1. 在 UI 文件中，转到文件夹 [ocean_blue](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/OnFido-Combined/UI/ocean_blue)

2. 打开每个 html 文件。

3. 查找 {your-ui-blob-container-url} 并将其替换为你的 UI ocean_blue、dist 和 assets 文件夹所在的 URL  

4. 查找 {your-intermediate-api-url} 并将其替换为中间 API 应用服务的 URL。

#### <a name="upload-your-files"></a>上传文件

1. 将 UI 文件从 UI 文件夹存储到 Blob 容器。

2. 使用 [Azure 存储资源管理器](../virtual-machines/disks-use-storage-explorer-managed-disks.md)来管理文件和访问权限。

### <a name="part-3---configure-azure-ad-b2c"></a>第 3 部分 - 配置 Azure AD B2C

#### <a name="replace-the-configuration-values"></a>替换配置值

在提供的[自定义策略](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/OnFido-Combined/Policies)中，查找以下占位符，并将其替换为你实例中的相应值。

| 占位符 | 替换为值 | 示例  |
|:---------------|:----------------|:-------------------|
| {your_tenant_name}  | 你的租户短名称 |  "yourtenant" from yourtenant.onmicrosoft.com |
| {your_tenantID} | Azure AD B2C 租户的 TenantID | 01234567-89ab-cdef-0123-456789abcdef           |
| {your_tenant_IdentityExperienceFramework_appid}        | Azure AD B2C 租户中配置的 IdentityExperienceFramework 应用的应用 ID      | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_ ProxyIdentityExperienceFramework _appid} | Azure AD B2C 租户中配置的 ProxyIdentityExperienceFramework 应用的应用 ID | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_extensions_appid}                         | 租户的存储应用程序的应用 ID                                      | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_extensions_app_objectid}                  | 租户的存储应用程序的对象 ID                                   | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_app_insights_instrumentation_key} | 应用见解实例的检测密钥*| 01234567-89ab-cdef-0123-456789abcdef|
|{your_ui_file_base_url}| UI ocean_blue、dist和 assets 文件夹所在位置的 URL   | https://yourstorage.blob.core.windows.net/UI/|
| {your_app_service_URL}                                 | 已设置的应用服务的 URL                                             | `https://yourapp.azurewebsites.net`          |

*应用见解可以在不同的租户中。 此步骤是可选的。 删除相应的 TechnicalProfiles 和 OrchestrationSteps（如果不需要）。

### <a name="part-4---configure-the-azure-ad-b2c-policy"></a>第 4 部分 - 配置 Azure AD B2C 策略

请参考本[文档](tutorial-create-user-flows.md?pivots=b2c-custom-policy#custom-policy-starter-pack)，以了解有关如何设置 Azure AD B2C 租户和配置策略的说明。

>[!NOTE]
> 作为最佳做法，我们会建议客户在属性收集页中添加许可通知。 通知用户：信息将会发送到第三方服务来进行身份验证。

## <a name="test-the-user-flow"></a>测试用户流

1. 打开 Azure AD B2C 租户，在“策略”下选择“Identity Experience Framework”。

2. 选择前面创建的“SignUpSignIn”。

3. 选择“运行用户流”，然后选择设置：

   a. 应用程序：选择已注册的应用（示例为 JWT）

   b. 回复 URL：选择“重定向 URL” 

   c. 选择“运行用户流”。

4. 完成注册流程并创建帐户

5. Onfido 服务将在该流程过程中（创建用户属性后）调用 HYPR。 如果该流程未完成，请检查该用户是否未保存在目录中。

## <a name="next-steps"></a>后续步骤

有关更多信息，请参阅以下文章：

- [Azure AD B2C 中的自定义策略](./custom-policy-overview.md)

- [Azure AD B2C 中的自定义策略入门](tutorial-create-user-flows.md?pivots=b2c-custom-policy)
