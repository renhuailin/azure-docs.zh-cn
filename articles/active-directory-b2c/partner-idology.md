---
title: IDology 与 Azure Active Directory B2C 集成
titleSuffix: Azure AD B2C
description: 了解如何将 Azure AD B2C 中的示例在线支付应用与 IDology 集成。 IDology 是具有多个解决方案的标识验证和校对提供程序。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: df0fcaf7987e30f9c2599346aaef5fcabb4f04e9
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2021
ms.locfileid: "107256644"
---
# <a name="tutorial-for-configuring-idology-with-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中配置 IDology 的教程 

在本示例教程中，我们会提供有关如何将 Azure AD B2C 与 [IDology](https://www.idology.com/solutions/) 集成的指导。 IDology 是具有多个解决方案的标识验证和校对提供程序。 在此示例中，我们将介绍 IDology 提供的 ExpectID 解决方案。

## <a name="prerequisites"></a>先决条件

若要开始，需要：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 一个已链接到 Azure 订阅的 [Azure AD B2C 租户](tutorial-create-tenant.md)。

## <a name="scenario-description"></a>方案描述

IDology 集成包括以下组件：

- Azure AD B2C – 负责验证用户凭据的授权服务器。 它也被称为“标识提供者”。
- IDology – IDology 服务获取用户提供的输入，并验证用户的身份。
- 自定义 Rest API – 此 API 实现 Azure AD 与 IDology 服务之间的集成。

以下体系结构图体现了实施详情。

![IDology 体系结构图](media/partner-idology/idology-architecture-diagram.png)

| 步骤 | 说明 |
|------|------|
|1     | 一名用户访问登录页。 |
|2     | 该用户选择注册选项来创建新帐户，并在该页面中输入信息。 Azure AD B2C 收集用户属性。 |
|3     | Azure AD B2C 调用中间层 API 并传递这些用户属性。 |
|4     | 该中间层 API 收集用户属性，并将这些属性转换为 IDOlogy API 可以使用的格式。 然后，它将信息发送到 IDology。 |
|5     | IDology 使用这些信息并对其进行处理，然后将结果返回到中间层 API。 |
|6     | 中间层 API 处理信息并将相关信息发送回 Azure AD B2C。 |
|7     | Azure AD B2C 接收从中间层 API 返回的信息。 如果它显示“失败”响应，则会向用户显示错误消息。 如果它显示“成功”响应，则会对用户进行身份验证并将该用户写入目录。 |
|      |      |

> [!NOTE]
> Azure AD B2C 还可以要求客户执行升级身份验证，但此场景超出了本教程的范围。

## <a name="onboard-with-idology"></a>加入 IDology

1. IDology 提供了多种解决方案，可在[此处](https://www.idology.com/solutions/)找到。 在此示例中，我们使用 ExpectID。

2. 若要创建 IDology 帐户，请联系 [IDology](https://www.idology.com/request-a-demo/microsoft-integration-signup/)。

3. 在在创建帐户后，你将收到进行 API 配置所需的信息。 以下各部分将介绍这一过程。

## <a name="integrate-with-azure-ad-b2c"></a>与 Azure AD B2C 集成

### <a name="part-1---deploy-the-api"></a>第 1 部分 - 部署 API

将提供的 [API 代码](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/IDology/Api)部署到 Azure 服务。 可以按照这些[说明](/visualstudio/deployment/quickstart-deploy-to-azure)从 Visual Studio 发布代码。

你将会需要已部署的服务的 URL，以便为 Azure AD 配置所需设置。

### <a name="part-2---configure-the-api"></a>第 2 部分 - 配置 API 

可以[在 Azure 中的应用服务中配置](../app-service/configure-common.md#configure-app-settings)应用程序设置。 采用这种方法，可以安全地配置设置，而无需将其签入存储库。 需要为 Rest API 提供以下设置：

| 应用程序设置 | Source | 注释 |
| :-------- | :------------| :-----------|
|IdologySettings:ApiUsername | IDology 帐户配置 |     |
|IdologySettings:ApiPassword | IDology 帐户配置 |     |
|WebApiSettings:ApiUsername |定义 API 的用户名| 在 ExtId 配置中使用 |
|WebApiSettings:ApiPassword | 定义 API 的密码 | 在 ExtId 配置中使用

### <a name="part-3---create-api-policy-keys"></a>第 3 部分 - 创建 API 策略密钥

请按照此[文档](secure-rest-api.md#add-rest-api-username-and-password-policy-keys)的说明创建两个策略密钥：一个用于 API 用户名，另一个用于前面定义的 API 密码。

该示例策略使用下列密钥名称：

* B2C_1A_RestApiUsername
* B2C_1A_RestApiPassword

### <a name="part-4---configure-the-azure-ad-b2c-policy"></a>第 4 部分 - 配置 Azure AD B2C 策略

1. 请按照此[文档](tutorial-create-user-flows.md?pivots=b2c-custom-policy#custom-policy-starter-pack)的说明下载[本地帐户入门包](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts)，并为 Azure AD B2C 租户配置策略。 按照说明进行操作，直到完成“测试自定义策略”部分。

2. 在[此处](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/IDology/policy)下载两个示例策略。

3. 更新这两个示例策略：

   1. 将两个策略都打开：

      1. 在 `Idology-ExpectId-API` 部分中，用上面部署的 API 的位置更新 `ServiceUrl` 元数据项。

      1. 将 `yourtenant` 替换为 Azure AD B2C 租户的名称。
      例如，如果 Azure AD B2C 租户的名称为  `contosotenant`，则将  `yourtenant.onmicrosoft.com`  的所有实例均替换为 `contosotenant.onmicrosoft.com`。

   1. 打开 TrustFrameworkExtensions.xml 文件：

      1. 找到  `<TechnicalProfile Id="login-NonInteractive">` 元素。 将  `IdentityExperienceFrameworkAppId`  的两个实例都替换为前面创建的 IdentityExperienceFramework 应用程序的 ID。

      1. 将  `ProxyIdentityExperienceFrameworkAppId`  的两个实例都替换为前面创建的 ProxyIdentityExperienceFramework 应用程序的 ID。

4. 将前面步骤 1 中上传到 Azure AD B2C 的 SignInorSignUp.xml 和 TrustFrameworkExtensions.xml 替换为两个更新的示例策略。

> [!NOTE]
> 作为最佳做法，建议客户在属性收集页中添加许可通知。 通知用户：信息将会发送到第三方服务来进行身份验证。

## <a name="test-the-user-flow"></a>测试用户流

1. 打开 Azure AD B2C 租户，在“策略”下选择“用户流” 。

2. 选择前面创建的用户流。

3. 选择“运行用户流”，然后选择设置：

   1. 应用程序 - 选择已注册的应用（示例为 JWT）。

   1. 回复 URL - 选择“重定向 URL” 。

   1. 选择“运行用户流”。

4. 浏览注册流并创建帐户。

5. 注销。

6. 浏览登录流。

7. 输入“继续”后，将会显示 IDology 拼图。

## <a name="next-steps"></a>后续步骤

有关更多信息，请参阅以下文章：

- [Azure AD B2C 中的自定义策略](custom-policy-overview.md)

- [Azure AD B2C 中的自定义策略入门](tutorial-create-user-flows.md?pivots=b2c-custom-policy)
