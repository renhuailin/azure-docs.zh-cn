---
title: Azure 医疗保健 Api 身份验证和授权
description: 本文概述了医疗保健 Api 的身份验证和授权。
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.topic: overview
ms.date: 07/19/2021
ms.author: ginle
ms.openlocfilehash: 53f31790d5b92234bfd35e723e377e682cd13a4d
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123252510"
---
# <a name="authentication--authorization-for-the-healthcare-apis-preview"></a>身份验证 & 对医疗保健 Api (预览版的授权) 

> [!IMPORTANT]
> Azure Healthcare APIs 目前为预览版。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

本文提供了用于访问医疗保健 Api 服务的身份验证和授权过程的概述。

## <a name="authentication"></a>身份验证

医疗保健 api 是一个受保护的托管服务的集合，该集合使用[Azure Active Directory (Azure AD) ](../active-directory/index.yml)，这是一个支持[OAuth 2.0](https://oauth.net/2/)的全局标识提供者。

要使医疗保健 Api 服务访问 Azure 资源（例如存储帐户和事件中心），必须 **启用系统管理的标识**，并向托管标识 **授予适当的权限** 。 有关详细信息，请参阅 [Azure 托管标识](../active-directory/managed-identities-azure-resources/overview.md)。

医疗保健 Api 不支持其他标识提供者。 但是，客户可以使用其自己的标识提供者来保护应用程序的安全，并使他们能够通过管理客户端应用程序和用户数据访问控制来与医疗保健 Api 交互。

客户端应用程序在 Azure AD 中注册，可用于访问医疗保健 api。 用户数据访问控制在实现业务逻辑的应用程序或服务中完成。

### <a name="application-roles"></a>应用程序角色

必须向经过身份验证的用户和医疗保健 Api 的客户端应用程序授予正确的应用程序角色。

医疗保健 Api 的 FHIR 服务提供以下角色：

* **FHIR 数据读取器**：可以读取 (和搜索) FHIR 数据。
* **FHIR 数据编写器**：可读取、写入和软删除 FHIR 数据。
* **FHIR 数据导出** 程序：可以读取和导出 ($export 运算符) 数据。
* **FHIR 数据参与者**：可执行所有数据平面操作。
* **FHIR 数据转换器**：可以使用转换器执行数据转换。

医疗保健 Api 的 DICOM 服务提供以下角色：

* **Dicom 数据所有者**：可以读取、写入和删除 dicom 数据。
* **Dicom 数据读取**：可以读取 dicom 数据。

IoT Connector 不需要应用程序角色，但它确实依赖于 "Azure 事件中心数据接收方" 来检索客户订阅的事件中心中存储的数据。

## <a name="authorization"></a>授权

使用正确的应用程序角色授予后，经过身份验证的用户和客户端应用程序可以通过获取 Azure AD 颁发的 **有效访问令牌**，并执行应用程序角色定义的特定操作来访问医疗保健 api 服务。
 
* 对于 FHIR 服务，访问令牌特定于服务或资源。
* 对于 DICOM 服务，访问令牌将授予 `dicom.healthcareapis.azure.com` 资源，而不是特定服务。
* 对于 IoT Connector，访问令牌不是必需的，因为它不会公开给用户或客户端应用程序。

### <a name="steps-for-authorization"></a>授权步骤

可以通过两种常见方法获取访问令牌，如 Azure AD 文档中所述：[授权代码流](../active-directory/develop/v2-oauth2-auth-code-flow.md)和[客户端凭据流](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md)。

若要获取医疗保健 Api 的访问令牌，请使用 **授权代码流**：

1. **客户端向 Azure AD 授权终结点发送请求。** Azure AD 将客户端重定向到登录页面，用户使用相应的凭据进行身份验证 (例如：用户名和密码，或双因素身份验证) 。 **身份验证成功后，会将授权代码返回到客户端。** Azure AD 只允许将此授权代码返回到客户端应用程序注册中配置的已注册答复 URL。

2. **客户端应用程序在 Azure AD 令牌终结点交换访问令牌的授权代码。** 请求令牌时，客户端应用程序可能需要提供客户端机密 (你可以在应用程序注册) 期间添加该密钥。
 
3. **客户端向医疗保健 api 发出请求**，例如， `GET` 请求搜索 FHIR 服务中的所有患者。 发出请求时，它会 **在 `HTTP` 请求标头中包含访问令牌**，例如 **`Authorization: Bearer xxx`** 。

4. **医疗保健 Api 服务将验证令牌中是否包含标记) 中 (属性的适当声明。** 如果它有效，它将完成请求并将数据返回到客户端。

在 **客户端凭据流** 中，权限直接授予应用程序本身。 当应用程序向资源出示令牌时，该资源会强制应用程序本身具有执行操作的授权，因为身份验证中没有用户参与。 因此，不同于 **授权代码流** 的方式如下：

- 用户或客户端无需以交互方式登录
- 授权代码不是必需的。
- 直接通过应用程序权限获取访问令牌。

### <a name="access-token"></a>访问令牌

访问令牌是一组已签名、 [Base64](https://en.wikipedia.org/wiki/Base64) 编码的属性 (声明) ，用于传达有关为用户或客户端授予的客户端标识、角色和特权的信息。

医疗保健 Api 通常需要一个 [JSON Web 令牌](https://en.wikipedia.org/wiki/JSON_Web_Token)。 它由三个部分组成： 
* 标头
* 负载 (声明) 
* 签名，如下图所示。 有关详细信息，请参阅 [Azure 访问令牌](../active-directory/develop/active-directory-configurable-token-lifetimes.md)。

[![JASON web 令牌签名。 ](media/azure-access-token.png) ](media/azure-access-token.png#lightbox)

可以使用联机工具（如） [https://jwt.ms](https://jwt.ms/) 查看令牌内容。 例如，你可以查看声明详细信息。

|**声明类型**          |值               |备注                               |
|------------------------|------------------------|----------------------------------------|
|aud                     |https://xxx.fhir.azurehealthcareapis.com|标识令牌的目标接收方。 在 `id_tokens` 中，受众是在 Azure 门户中分配给应用的应用程序 ID。 应用应该验证此值并拒绝其值不匹配的令牌。|
|iss                     |https://sts.windows.net/{tenantid}/|标识构造并返回令牌的安全令牌服务 (STS)，以及对用户进行身份验证的 Azure AD 租户。 如果令牌由 v2.0 终结点颁发，则 URI 将以 `/v2.0` 结尾。 表示用户是来自 Microsoft 帐户的使用者用户的 GUID 为 `9188040d-6c67-4c5b-b112-36a304b66dad`。 应用应使用声明的 GUID 部分来限制可登录到应用的租户集（如果适用）。|
|iat                     | (时间戳)             |“Issued At”表示针对此令牌进行身份验证的时间。|
|nbf                     | (时间戳)             |“nbf”（不早于）声明指定只能在哪个时间之后接受 JWT 的处理。|
|exp                     | (时间戳)             |“exp”（过期时间）声明指定只能在哪个时间（含）之前接受 JWT 的处理。 请务必注意，资源可能会在此时间之前拒绝标记，如需要对身份验证进行更改或检测到令牌吊销。|
|aio                     |E2ZgYxxx                |一个内部声明，Azure AD 用它来记录有关重复使用令牌的数据。 应忽略。|
|appid                   |e97e1b8c-xxx            |这是使用令牌的客户端的应用程序 ID。 该应用程序可以自身名义或者代表用户进行操作。 应用程序 ID 通常表示应用程序对象，但它还可以表示 Azure AD 中的服务主体对象。|
|appidacr                |1                       |表示对客户端进行身份验证的方式。 对于公共客户端，值为“0”。 如果使用客户端 ID 和客户端机密，则值为“1”。 如果使用客户端证书进行身份验证，则值为“2”。|
|idp                     |https://sts.windows.net/{tenantid}/|记录对令牌使用者进行身份验证的标识提供者。 此值与颁发者声明的值相同，除非用户帐户与颁发者帐户不在同一租户中，例如。 如果声明不存在，则意味着可以改为使用 iss 的值。 对于组织上下文中使用的个人帐户 (例如，邀请到 Azure AD 租户) 的个人帐户，idp 声明可以是 "live.com"，也可以是包含 Microsoft 帐户租户9188040d-6c67-4c5b-b112-36a304b66dad 的 STS URI。|
|oid                     |例如，tenantid         |这是 Microsoft 标识系统（在本例中为用户帐户）中的对象的不可变标识符。 此 ID 在应用程序之间唯一标识用户-同一用户中两个不同的应用程序登录将在 oid 声明中接收相同的值。 Microsoft Graph 将返回此 id 作为给定用户帐户的 id 属性。 因为 oid 允许多个应用关联用户，所以需要配置文件范围才能接收此声明。 注意：如果单个用户存在于多个租户中，则用户将在每个租户中包含不同的对象 ID-它们被视为不同的帐户，即使用户使用相同的凭据登录到每个帐户也是如此。|
|rh                       |0. ARoxxx              |Azure 用来重新验证令牌的内部声明。 应忽略它。|
|sub                      |例如，tenantid        |令牌针对其断言信息的主体，例如应用的用户。 此值固定不变，无法重新分配或重复使用。 使用者是成对标识符 - 它对特定应用程序 ID 是唯一的。 因此，如果单个用户使用两个不同的客户端 ID 登录到两个不同的应用，这些应用将收到两个不同的使用者声明值。 这不一定是所需的，具体取决于体系结构和隐私要求。|
|tid                      |例如，tenantid        |表示用户所属的 Azure AD 租户的 GUID。 对于工作和学校帐户，该 GUID 就是用户所属组织的不可变租户 ID。 对于个人帐户，值为9188040d-6c67-4c5b-b112-36a304b66dad。 需要配置文件范围才能接收此声明。
|uti                      |bY5glsxxx             |Azure 用来重新验证令牌的内部声明。 应忽略它。|
|ver                      |1                     |指示令牌的版本。|
 
**默认情况下，访问令牌的有效期为一小时。你可以获取新令牌，或使用刷新令牌续订令牌，然后才会到期。**

若要获取访问令牌，可以使用 Postman 等工具、Visual Studio Code、PowerShell、CLI、卷和[Azure AD 身份验证库](../active-directory/develop/reference-v2-libraries.md)中的 Rest 客户端扩展。

## <a name="encryption"></a>加密

当你创建新的 Azure 医疗保健 Api 服务时，默认情况下，你的数据将使用 **Microsoft 托管的密钥** 进行加密。 

* 在数据存储中保存数据时，FHIR 服务提供静态数据的加密。
* DICOM 服务提供静态数据的加密，在数据存储中保留图像数据（包括嵌入的元数据）。 如果在 FHIR 服务中提取并保存元数据，则会自动对其进行加密。
* IoT Connector，在数据映射和规范化之后，会将设备消息保存到 FHIR 服务，该服务会自动加密。 如果将设备消息发送到使用 Azure 存储存储数据的 Azure 事件中心，则会使用 Azure 存储服务加密 (Azure SSE) 来自动加密数据。

## <a name="next-steps"></a>后续步骤

本文档介绍了医疗保健 Api 的身份验证和授权。 若要了解如何部署医疗保健 Api 服务的实例，请参阅

>[!div class="nextstepaction"]
>[使用 Azue 门户) 工作区部署医疗保健 Api (预览](healthcare-apis-quickstart.md)
