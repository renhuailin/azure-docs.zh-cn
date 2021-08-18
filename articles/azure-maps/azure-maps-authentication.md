---
title: 使用 Microsoft Azure Maps 进行身份验证
titleSuffix: Azure Maps
description: 了解 Azure Maps 中进行请求身份验证的两种方式：共享密钥身份验证和 Azure Active Directory (Azure AD) 身份验证。
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/25/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.custom: mvc
ms.openlocfilehash: 75098cdba1281e9ebd4162001652be4650bf83ba
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121725646"
---
# <a name="authentication-with-azure-maps"></a>使用 Azure Maps 进行身份验证

在 Azure Maps 中，有两种方式可以对请求进行身份验证，分别是：共享密钥身份验证和 [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) 身份验证。 本文介绍了这两种身份验证方法，以帮助指导用户实现 Azure Maps 服务。

> [!NOTE]
> 为了改善与 Azure Maps 的安全通信，我们现在支持传输层安全性 (TLS) 1.2，并逐步停止支持 TLS 1.0 和 1.1。 如果当前使用的是 TLS 1.x，请根据[解决 TLS 1.0 问题](/security/solving-tls1-problem)中所述的测试，评估 TLS 1.2 就绪情况并制定合适的迁移计划。

## <a name="shared-key-authentication"></a>共享密钥身份验证

 创建了 Azure Maps 帐户后，将生成主密钥和辅助密钥。 建议在通过共享密钥身份验证调用 Azure Maps 时，使用主密钥作为订阅密钥。 共享密钥身份验证会将 Azure Maps 帐户生成的密钥传递到 Azure Maps 服务。 对于发送到 Azure Maps 服务的每个请求，请将 *订阅密钥* 作为参数添加到 URL 中。 辅助密钥可用于滚动密钥更改等方案中。  

有关在 Azure 门户中查看密钥的信息，请参阅[管理身份验证](./how-to-manage-authentication.md#view-authentication-details)。

> [!NOTE]
> 应将主密钥和辅助密钥视为敏感数据。 共享密钥用于对所有 Azure Maps REST API 进行身份验证。  使用共享密钥的用户应该通过环境变量或安全的机密存储将 API 密钥提取出来，以便集中进行管理。

## <a name="azure-ad-authentication"></a>Azure AD 身份验证

Azure 订阅中自带 Azure AD 租户，可实现精细的访问控制。 Azure Maps 支持使用 Azure AD 对 Azure Maps 服务进行身份验证。 Azure AD 为 Azure AD 租户中注册的用户和应用程序提供了基于身份的身份验证。

对于与包含 Azure Maps 帐户的 Azure 订阅相关联的 Azure AD 租户，Azure Maps 接受 **OAuth 2.0** 访问令牌。 Azure Maps 还接受以下对象的令牌：

* Azure AD 用户
* 使用了用户委托权限的合作伙伴应用程序
* Azure 资源的托管标识

Azure Maps 为每个 Azure Maps 帐户生成一个唯一的标识符（客户端 ID）。 将此客户端 ID 与其他参数结合使用时，可从 Azure AD 请求令牌。

有关如何为 Azure Maps 配置 Azure AD 和请求令牌的详细信息，请参阅[在 Azure Maps 中管理身份验证](./how-to-manage-authentication.md)。

有关使用 Azure AD 进行身份验证的常规信息，请参阅[什么是身份验证？](../active-directory/develop/authentication-vs-authorization.md)。

### <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Azure 资源和 Azure Maps 的托管标识

[Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/overview.md)为 Azure 服务提供一个自动托管的基于应用程序的安全主体，该安全主体可通过 Azure AD 进行身份验证。 使用 Azure 基于角色的访问控制 (Azure RBAC)，可以授权托管标识安全主体访问 Azure Maps 服务。 托管标识的一些示例包括：Azure 应用服务、Azure Functions 和 Azure 虚拟机。 有关托管标识的列表，请参阅 [Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)。

### <a name="configuring-application-azure-ad-authentication"></a>配置应用程序 Azure AD 身份验证

应用程序将使用 Azure AD 提供的一个或多个受支持方案，对 Azure AD 租户进行身份验证。 每个 Azure AD 应用程序方案代表了不同业务需求下的不同要求。 某些应用程序可能要求用户登录体验，还有一些应用程序可能要求应用程序登录体验。 有关详细信息，请参阅[身份验证流和应用程序方案](../active-directory/develop/authentication-flows-app-scenarios.md)。

在应用程序收到访问令牌后，SDK 和/或应用程序将发送 HTTPS 请求，其中包括以下一组必需的 HTTP 标头，以及其他 REST API HTTP 标头：

| 标头名称    | 值               |
| :------------- | :------------------ |
| x-ms-client-id | 30d7cc….9f55        |
| 授权  | Bearer eyJ0e….HNIVN |

> [!NOTE]
> `x-ms-client-id` 是 Azure Maps 身份验证页上显示的基于 Azure Maps 帐户的 GUID。

下面是使用 Azure AD OAuth 持有者令牌的 Azure Maps 路由请求示例：

```http
GET /route/directions/json?api-version=1.0&query=52.50931,13.42936:52.50274,13.43872
Host: atlas.microsoft.com
x-ms-client-id: 30d7cc….9f55
Authorization: Bearer eyJ0e….HNIVN
```

有关如何查看客户端 ID 的信息，请参阅[查看身份验证详细信息](./how-to-manage-authentication.md#view-authentication-details)。

## <a name="authorization-with-role-based-access-control"></a>使用基于角色的访问控制进行授权

Azure Maps 支持访问 [Azure 基于角色的访问控制 (Azure RBAC)](../role-based-access-control/overview.md) 的所有主体类型，包括：各个 Azure AD 用户、组、应用程序、Azure 资源和 Azure 托管标识。 主体类型被授予了一组权限，也称为角色定义。 角色定义提供了对 REST API 操作的权限。 将访问权限应用到一个或多个 Azure Maps 帐户称为作用域。 在应用主体、角色定义和作用域时，将创建角色分配。

以下部分将讨论与 Azure Maps 与 Azure RBAC 集成的概念和组件。 在设置 Azure Maps 帐户的过程中，Azure AD 目录将关联到 Azure Maps 帐户所在的 Azure 订阅。

当配置 Azure RBAC 时，需选择一个安全主体并将其应用于角色分配。 如果要了解如何将角色分配添加到 Azure 门户，请参阅[分配 Azure 角色](../role-based-access-control/role-assignments-portal.md)。

### <a name="picking-a-role-definition"></a>选取角色定义

有下列角色定义类型支持应用程序方案。

| Azure 角色定义       | 说明                                                                                              |
| :-------------------------- | :------------------------------------------------------------------------------------------------------- |
| Azure Maps 数据读取器      | 提供对不可变 Azure Maps REST API 的访问。                                                       |
| Azure Maps 数据参与者 | 提供对可变 Azure Maps REST API 的访问。 可变性根据是否支持写入和删除操作来确定。 |
| 自定义角色定义      | 创建精心设计的角色，灵活限制对 Azure Maps REST API 的访问。                      |

某些 Azure Maps 服务可能需要提升的权限才能对 Azure Maps REST API 执行写入或删除操作。 对于提供写入或删除操作的服务，必须具有 Azure Maps 数据参与者角色。 下表描述了在给定服务上使用写入或删除操作时，Azure Maps 数据参与者适用于哪些服务。 如果服务上只使用了读取操作，则可以使用 Azure Maps 数据读取器，而不需要 Azure Maps 数据参与者。

| Azure Maps 服务 | Azure Maps 角色定义  |
| :----------------- | :-------------------------- |
| 数据               | Azure Maps 数据参与者 |
| 创建者            | Azure Maps 数据参与者 |
| 空间            | Azure Maps 数据参与者 |

有关查看 Azure RBAC 设置的信息，请参阅[如何为 Azure Maps 配置 Azure RBAC](./how-to-manage-authentication.md)。

#### <a name="custom-role-definitions"></a>自定义角色定义

在应用程序安全性方面，有一个特性是适用最小特权原则。 根据此原则，安全主体应只允许访问所需的内容，没有其他访问权限。 对于需要进一步细化访问控制的用例，可通过创建自定义角色定义予以支持。 如果要创建自定义角色定义，可选择定义中要包含或排除的特定数据操作。

然后，即可在任何安全主体的角色分配中使用该自定义角色定义。 如果要了解有关 Azure 自定义角色定义的详细信息，请参阅 [Azure 自定义角色](../role-based-access-control/custom-roles.md)。

下面是一些示例方案，其中的自定义角色可以提高应用程序安全性。

| 方案                                                                                                                                                                                                                 | 自定义角色数据操作                                                                                                                  |
| :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------ |
| 包含基础地图图块但没有其他 REST API 的面向公众或交互式登录网页。                                                                                                                              | `Microsoft.Maps/accounts/services/render/read`                                                                                              |
| 只需要反向地理编码而无需其他 REST API 的应用程序。                                                                                                                                             | `Microsoft.Maps/accounts/services/search/read`                                                                                              |
| 安全主体的一个角色，请求读取基于 Azure Maps 创建者的地图数据和基础地图图块 REST API。                                                                                                 | `Microsoft.Maps/accounts/services/data/read`, `Microsoft.Maps/accounts/services/render/read`                                                |
| 安全主体的一个角色，要求能读取、写入和删除基于创建者的地图数据。 这可以定义为地图数据编辑器角色，但不允许访问基础地图图块等其他 REST API。 | `Microsoft.Maps/accounts/services/data/read`, `Microsoft.Maps/accounts/services/data/write`, `Microsoft.Maps/accounts/services/data/delete` |

### <a name="understanding-scope"></a>了解作用域

创建角色分配时，它是在 Azure 资源层次结构中进行定义。 层次结构的顶部是[管理组](../governance/management-groups/overview.md)，最底部是 Azure 资源，例如 Azure Maps 帐户。
如果为某个资源组分配了角色分配，则将允许访问该组中的多个 Azure Maps 帐户或资源。

> [!TIP]
> Microsoft 的一般建议是将访问权限分配到 Azure Maps 帐户作用域，因为这样可以避免对同一 Azure 订阅中现有的 **其他 Azure Maps 帐户进行意外的访问**。

## <a name="next-steps"></a>后续步骤

如果要详细了解 Azure RBAC，请参阅
> [!div class="nextstepaction"]
> [Azure 基于角色的访问控制](../role-based-access-control/overview.md)

如果要详细了解如何使用 Azure AD 和 Azure Maps 对应用程序进行身份验证，请参阅
> [!div class="nextstepaction"]
> [在 Azure Maps 中管理身份验证](./how-to-manage-authentication.md)

如果要详细了解如何使用 Azure AD 对 Azure Maps Map Control 进行身份验证，请参阅
> [!div class="nextstepaction"]
> [使用 Azure Maps Map Control](./how-to-use-map-control.md)