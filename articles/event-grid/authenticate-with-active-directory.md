---
title: 使用 Azure Active Directory 对事件网格发布客户端进行身份验证（预览版）
description: 本文介绍如何使用 Azure Active Directory 对 Azure 事件网格发布客户端进行身份验证。
ms.topic: conceptual
ms.date: 08/10/2021
ms.openlocfilehash: c34ce75d02d4e3044819e5e310bdb9e74c1b0004
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778568"
---
# <a name="authentication-and-authorization-with-azure-active-directory-preview"></a>使用 Azure Active Directory 进行身份验证和授权（预览版）
本文介绍如何使用 Azure Active Directory (Azure AD) 对 Azure 事件网格发布客户端进行身份验证。

## <a name="overview"></a>概述
[Microsoft 标识](../active-directory/develop/v2-overview.md)平台针对使用 Azure Active Directory (Azure AD) 作为其标识提供者的资源和应用程序提供集成的身份验证和访问控制管理。 使用 Microsoft 标识平台在应用程序中提供身份验证和授权支持。 它基于 OAuth 2.0 和 OpenID Connect 等开放标准，并提供支持许多身份验证方案的工具和开放源代码库。 它提供[条件访问](../active-directory/conditional-access/overview.md)等高级功能，可（例如）设置需要多重身份验证的策略或允许从特定位置访问。

使用 Azure AD 时提高安全性情况的一个优势是无需将凭据（如身份验证密钥）存储在代码或存储库中。 而是，在向受保护资源进行身份验证时，从应用程序所提供的 Microsoft 标识平台获取 OAuth 2.0 访问令牌。 可以将事件发布应用程序注册到 Azure AD，并获取与所管理和使用的应用相关联的服务主体。 相反，可以将系统分配或用户分配的[托管标识](../active-directory/managed-identities-azure-resources/overview.md)用于甚至更简单的标识管理模型，因为标识生命周期的某些方面是托管的。 

[基于角色的访问控制](../active-directory/develop/custom-rbac-for-developers.md) (RBAC) 允许以某种方式配置授权，使某些安全主体（用户、组或应用的标识）具有对 Azure 资源执行操作的特定权限。 这样，将事件发送到事件网格的客户端应用程序所使用的安全主体必须具有与之关联的 RBAC 角色“EventGrid 数据发送方”。 

### <a name="security-principals"></a>安全主体
讨论事件网格发布客户端的身份验证时，有两大类安全主体适用： 

- “托管标识”。 托管标识可以是系统分配的，在 Azure 资源上启用并只关联到该资源，也可以是用户分配的，由用户显式创建和命名。 用户分配的托管标识可以关联到多个资源。
- “应用程序安全主体”。 它是一种表示应用程序的安全主体，访问受 Azure AD 保护的资源。 

无论使用何种安全主体（托管标识或应用程序安全主体），客户端都会使用该标识在 Azure AD 之前进行身份验证，并获取在将事件发送到事件网格时随请求一起发送的 [OAuth 2.0 访问令牌](../active-directory/develop/access-tokens.md)。 该令牌经过加密签名，事件网格收到令牌后，会验证该令牌。 例如，受众（令牌的目标接收方）确认为事件网格 (`https://eventgrid.azure.net`) 等。 令牌包含有关客户端标识的信息。 事件网格采用该标识，并验证客户端是否分配有角色“EventGrid 数据发送方”。 更准确地说，在允许事件发布请求完成之前，事件网格会验证标识是否具有与标识相关联的 RBAC 角色的 ``Microsoft.EventGrid/events/send/action`` 权限。 
 
如果使用的是事件网格 SDK，则无需担心如何实现获取访问令牌以及如何在事件网格的每一个请求中包括访问令牌的详细信息，因为[事件网格数据平面 SDK](#publish-events-using-event-grids-client-sdks) 会执行这些操作。 

### <a name="high-level-steps"></a>大致步骤
执行以下步骤，使客户端在将事件发送到主题、域或合作伙伴命名空间时能够使用 Azure AD 身份验证。

1. 创建或使用要用于身份验证的安全主体。 如果可以使用[托管标识](#authenticate-using-a-managed-identity)或[应用程序安全主体](#authenticate-using-a-security-principal-of-a-client-application)。
2. 通过向安全主体分配“EventGrid 数据发送方”角色，[向安全主体授予发布事件的权限](#assign-permission-to-a-security-principal-to-publish-events)。
3. 使用事件网格 SDK 将事件发布到事件网格。

## <a name="authenticate-using-a-managed-identity"></a>使用托管标识进行身份验证

托管标识是与 Azure 资源关联的标识。 托管标识提供应用程序在使用支持 Azure AD 身份验证的资源时使用的标识。 应用程序可以使用托管资源（如虚拟机或 Azure 应用服务）的托管标识来获取 Azure AD 令牌，该令牌在将事件发布到事件网格时随请求一起提供。 当应用程序连接时，事件网格会将托管实体的上下文绑定到客户端。 与托管标识关联后，事件网格发布客户端便能执行所有授权操作。 授权是通过将托管标识关联到事件网格 RBAC 角色来授予的。

托管标识为 Azure 服务提供了 Azure AD 中的自动托管标识。 与其他身份验证方法相反，对于标识本身或需要访问的资源，都不需要在应用程序代码或配置中存储和保护访问密钥或共享访问签名 (SAS)。

若要使用托管标识对事件发布客户端进行身份验证，请首先确定客户端应用程序的托管 Azure 服务，然后在该 Azure 服务实例上启用系统分配或用户分配的托管标识。 例如，可以在 [VM](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)、[Azure 应用服务或 Azure Functions](../app-service/overview-managed-identity.md?tabs=dotnet) 上启用托管标识。 

在托管服务中配置托管标识后，[分配将事件发布到该标识的权限](#assign-permission-to-a-security-principal-to-publish-events)。

## <a name="authenticate-using-a-security-principal-of-a-client-application"></a>使用客户端应用程序的安全主体进行身份验证

除了托管标识，另一个标识选项是为客户端应用程序创建安全主体。 为此，需要将应用程序注册到 Azure AD。 注册应用程序是一种手势，通过该手势可以将标识和访问管理控制委托给Azure AD。 执行[注册应用程序](../active-directory/develop/quickstart-register-app.md#register-an-application)部分和[添加客户端机密](../active-directory/develop/quickstart-register-app.md#add-a-client-secret)部分中的步骤。 请确保在开始前查看[先决条件](../active-directory/develop/quickstart-register-app.md#prerequisites)。

拥有应用程序安全主体并执行上述步骤后，[分配将事件发布到该标识的权限](#assign-permission-to-a-security-principal-to-publish-events)。

> [!NOTE]
> 在门户中注册应用程序时，会在主租户中自动创建[应用程序对象](../active-directory/develop/app-objects-and-service-principals.md#application-object)和[服务主体](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)。 或者，可以使用 Microsot Graph 注册应用程序。 但是，如果使用 Microsoft Graph API 注册或创建应用程序，则通过一个单独步骤创建服务主体对象。 

## <a name="assign-permission-to-a-security-principal-to-publish-events"></a>向安全主体分配发布事件的权限

用于将事件发布到事件网格的标识必须具有允许其将事件发送到事件网格的权限 ``Microsoft.EventGrid/events/send/action``。 该权限包含在内置 RBAC 角色 [EventGrid 数据发送方](../role-based-access-control/built-in-roles.md#eventgrid-data-sender)中。 可以将此角色分配给给定[范围](../role-based-access-control/overview.md#scope)的[安全主体](../role-based-access-control/overview.md#security-principal)，可以是管理组、Azure 订阅、资源组或特定事件网格主题、域或合作伙伴命名空间。 执行[分配 Azure 角色](../role-based-access-control/role-assignments-portal.md?tabs=current)中的步骤为安全主体分配“EventGrid 数据发送方”角色，以此方式授予使用该安全主体的应用程序发送事件的访问权限。 或者，可以定义包含 ``Microsoft.EventGrid/events/send/action`` 权限的[自定义角色](../role-based-access-control/custom-roles.md)，并将该自定义角色分配给安全主体。

处理 RBAC 特权后，现在可以[构建客户端应用程序以将事件发送](#publish-events-using-event-grids-client-sdks)到事件网格。

> [!NOTE]
> 除了发送事件之外，事件网格还支持更多 RBAC 角色。 有关详细信息，请参阅[事件网格内置角色](security-authorization.md#built-in-roles)。


## <a name="publish-events-using-event-grids-client-sdks"></a>使用事件网格的客户端 SDK 发布事件

使用[事件网格数据平面 SDK](https://devblogs.microsoft.com/azure-sdk/event-grid-ga/) 将事件发布到事件网格。 事件网格的 SDK 支持所有身份验证方法，包括 Azure AD 身份验证。 

### <a name="prerequisites"></a>先决条件

以下是向事件网格进行身份验证的先决条件。

- 在应用程序上安装 SDK。
   - [Java](/java/api/overview/azure/messaging-eventgrid-readme#include-the-package)
   - [.NET](/dotnet/api/overview/azure/messaging.eventgrid-readme-pre#install-the-package)
   - [JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/eventgrid/eventgrid.md#install-the-azureeventgrid-package)
   - [Python](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/eventgrid/azure-eventgrid#install-the-package)
- 安装 Azure 标识客户端库。 事件网格 SDK 依赖于用于身份验证的 Azure 标识客户端库。 
   - [适用于 Java 的 Azure 标识客户端库](/java/api/overview/azure/identity-readme)
   - [适用于 .NET 的 Azure 标识客户端库](/dotnet/api/overview/azure/identity-readme)
   - [适用于 JavaScript 的 Azure 标识客户端库](/javascript/api/overview/azure/identity-readme)
   - [适用于 Python 的 Azure 标识客户端库](/python/api/overview/azure/identity-readme)
- 创建的主题、域或合作伙伴命名空间，应用程序将向其发送事件。

### <a name="publish-events-using-azure-ad-authentication"></a>使用 Azure AD 身份验证发布事件

若要将事件发送到主题、域或合作伙伴命名空间，可以按照以下方式构建客户端。 首次提供 Azure AD 身份验证支持的 api 版本为 ``2021-06-01-preview``。 在应用程序中使用该 API 版本或更新版本。

```java 
        DefaultAzureCredential credential = new DefaultAzureCredentialBuilder().build();
        EventGridPublisherClient cloudEventClient = new EventGridPublisherClientBuilder()
                .endpoint("<your-event-grid-topic-domain-or-partner-namespace-endpoint>?api-version=2021-06-01-preview")
                .credential(credential)
                .buildCloudEventPublisherClient();
```
如果使用与客户端发布应用程序关联的安全主体，必须配置环境变量，如 [Java SDK 自述文件](/java/api/overview/azure/identity-readme#environment-variables)所示。 `DefaultCredentialBuilder` 读取这些环境变量以使用正确的标识。 有关详细信息，请参阅 [Java API 概述](/java/api/overview/azure/identity-readme#defaultazurecredential)。


有关详细信息，请参阅以下文章：

- [适用于 Java 的 Azure 事件网格客户端库](/java/api/overview/azure/messaging-eventgrid-readme)
- [适用于 .NET 的 Azure 事件网格客户端库](https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/eventgrid/Azure.Messaging.EventGrid#authenticate-using-azure-active-directory)
- [适用于 JavaScript 的 Azure 事件网格客户端库](/javascript/api/overview/azure/eventgrid-readme)
- [适用于 Python 的 Azure 事件网格客户端库](/python/api/overview/azure/eventgrid-readme)

## <a name="disable-key-and-shared-access-signature-authentication"></a>禁用密钥和共享访问签名身份验证

Azure AD 身份验证提供的身份验证支持优于访问密钥或共享访问签名 (SAS) 令牌身份验证所提供的身份验证支持。 使用 Azure AD 身份验证时，将针对 Azure AD 标识提供者来验证标识。 作为开发人员，如果使用 Azure AD 身份验证，则不需要在代码中处理密钥。 还会受益于内置于 Microsoft 标识平台的所有安全功能，例如[条件访问](../active-directory/conditional-access/overview.md)，有助于提高应用程序的安全性情况。 

决定使用 Azure AD 身份验证后，可以禁用基于访问密钥或 SAS 令牌的身份验证。 

> [!NOTE]
> 访问密钥或 SAS 令牌身份验证是一种“本地身份验证”形式。 当讨论此类不依赖于 Azure AD 的身份验证机制时，有时会听到“local auth”。 用于禁用本地身份验证的 API 参数因而称作 ``disableLocalAuth``。

以下 CLI 命令演示了创建禁用本地身份验证的自定义主题的方法。 禁用本地身份验证功能目前以预览版提供，需要使用 API 版本 ``2021-06-01-preview``。

```cli
az resource create --subscription <subscriptionId> --resource-group <resourceGroup> --resource-type Microsoft.EventGrid/topics --api-version 2021-06-01-preview --name <topicName> --location <location> --properties "{ \"disableLocalAuth\": true}"
```

作为参考，以下是根据正在创建或更新的主题可以使用的资源类型值。

| 主题类型        | 资源类型                        |
| ------------------| :------------------------------------|
| 域           | Microsoft.EventGrid/domains          |
| 合作伙伴命名空间 | Microsoft.EventGrid/partnerNamespaces|
| 自定义主题      | Microsoft.EventGrid/topics           |

如果使用的是 PowerShell，则使用以下 cmdlet 创建禁用本地身份验证的自定义主题。 

```PowerShell

Set-AzContext -SubscriptionId <SubscriptionId>

New-AzResource -ResourceGroupName <ResourceGroupName> -ResourceType Microsoft.EventGrid/topics -ApiVersion 2021-06-01-preview -ResourceName <TopicName> -Location <Location> -Properties @{disableLocalAuth=$true}
```

> [!NOTE]
> - 若要了解使用访问密钥或共享访问签名身份验证，请参阅[使用密钥或 SAS 令牌对发布客户端进行身份验证](security-authenticate-publishing-clients.md)
> - 本文介绍将事件发布到事件网格（事件传入）时的身份验证。 传递事件（事件传出）时对事件网格进行身份验证是[对事件处理程序的事件传递进行身份验证](security-authentication.md)一文的主题。 

## <a name="resources"></a>资源
- 数据平面 SDK
    - Java SDK：[github](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventgrid/azure-messaging-eventgrid) | [示例](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventgrid/azure-messaging-eventgrid/src/samples/java/com/azure/messaging/eventgrid) | [以前 SDK 版本的迁移指南](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventgrid/azure-messaging-eventgrid/migration-guide.md)
    - .NET SDK：[github](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventgrid/Azure.Messaging.EventGrid) | [示例](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventgrid/Azure.Messaging.EventGrid/samples) | [以前 SDK 版本的迁移指南](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventgrid/Azure.Messaging.EventGrid/MigrationGuide.md)
    - Python SDK：[github](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventgrid/azure-eventgrid) | [示例](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventgrid/azure-eventgrid/samples) | [以前 SDK 版本的迁移指南](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventgrid/azure-eventgrid/migration_guide.md)
    - JavaScript SDK：[github](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventgrid/eventgrid/) | [示例](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventgrid/eventgrid/samples) | [以前 SDK 版本的迁移指南](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventgrid/eventgrid/migration.md)
- [事件网格 SDK 博客](https://devblogs.microsoft.com/azure-sdk/event-grid-ga/)
- Azure 标识客户端库
   - [Java](https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/identity/azure-identity/README.md)
   - [.NET](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/identity/Azure.Identity/README.md)  
   - [Python](https://github.com/Azure/azure-sdk-for-python/blob/main/sdk/identity/azure-identity/README.md)
   - [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/identity/identity/README.md)
- 了解[托管标识](../active-directory/managed-identities-azure-resources/overview.md)
- 了解[如何使用应用服务和 Azure Functions 的托管标识](../app-service/overview-managed-identity.md?tabs=dotnet)
- 了解[应用程序和服务主体](../active-directory/develop/app-objects-and-service-principals.md)
- 了解[将应用程序注册到 Microsoft 标识平台](../active-directory/develop/quickstart-register-app.md)。
- 了解[授权](../role-based-access-control/overview.md)（RBAC 访问控制）的工作原理。
- 了解事件网格内置 RBAC 角色，包括其 [EventGrid 数据发送方](../role-based-access-control/built-in-roles.md#eventgrid-data-sender)角色。 [事件网格的角色列表](security-authorization.md#built-in-roles)。
- 了解[将 RBAC 角色分配](../role-based-access-control/role-assignments-portal.md?tabs=current)给标识。
- 了解如何定义[自定义 RBAC 角色](../role-based-access-control/custom-roles.md)。
- 了解 [Azure AD 中的应用程序对象和服务主体对象](../active-directory/develop/app-objects-and-service-principals.md)。
- 了解 [Microsoft 标识平台访问令牌](../active-directory/develop/access-tokens.md)。
- 了解 [OAuth 2.0 身份验证代码流和 Microsoft 标识平台](../active-directory/develop/v2-oauth2-auth-code-flow.md)
