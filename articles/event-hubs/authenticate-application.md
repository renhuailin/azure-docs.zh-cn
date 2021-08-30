---
title: 对访问 Azure 事件中心资源的应用程序进行身份验证
description: 本文提供有关对使用 Azure Active Directory 访问 Azure 事件中心资源的应用程序进行身份验证的信息
ms.topic: conceptual
ms.date: 06/14/2021
ms.custom: subject-rbac-steps
ms.openlocfilehash: f87866ece2699a457e00a4afba6855933118cf19
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/15/2021
ms.locfileid: "112123081"
---
# <a name="authenticate-an-application-with-azure-active-directory-to-access-event-hubs-resources"></a>对使用 Azure Active Directory 访问事件中心资源的应用程序进行身份验证
Microsoft Azure 基于 Azure Active Directory (Azure AD) 针对资源和应用程序提供了集成的访问控制管理功能。 将 Azure AD 与 Azure 事件中心配合使用的主要优势在于，不再需要将凭据存储在代码中。 可以从 Microsoft 标识平台请求 OAuth 2.0 访问令牌。 请求令牌的资源名称为 `https://eventhubs.azure.net/`，所有云/租户都是如此（对于 Kafka 客户端，请求令牌的资源为 `https://<namespace>.servicebus.windows.net`）。 Azure AD 对运行应用程序的安全主体（用户、组或服务主体）进行身份验证。 如果身份验证成功，Azure AD 会将访问令牌返回应用程序，应用程序可随之使用访问令牌对 Azure 事件中心资源请求授权。

将角色分配到 Azure AD 安全主体后，Azure 会向该安全主体授予对这些资源的访问权限。 访问权限的范围可限定为订阅、资源组、事件中心命名空间级别或其下的任何资源。 Azure AD 安全主体可向用户、组、应用程序服务主体或 [Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/overview.md)分配角色。 

> [!NOTE]
> 角色定义是权限的集合。 Azure 基于角色的访问控制 (Azure RBAC) 控制如何通过角色分配实施这些权限。 角色分配包含三个要素：安全主体、角色订阅和范围。 有关详细信息，请参阅[了解不同的角色](../role-based-access-control/overview.md)。

## <a name="built-in-roles-for-azure-event-hubs"></a>Azure 事件中心的内置角色
Azure 提供了以下 Azure 内置角色，用于通过 Azure AD 和 OAuth 授予对事件中心数据的访问权限：

- [Azure 事件中心数据所有者](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner)：使用此角色可以授予对事件中心资源的完全访问权限。
- [Azure 事件中心数据发送者](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender)：使用此角色可以授予对事件中心资源的发送访问权限。
- [Azure 事件中心数据接收者](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver)：使用此角色可以授予对事件中心资源的接收访问权限。   

要了解架构注册表内置角色，请参阅[架构注册表角色](schema-registry-overview.md#azure-role-based-access-control)。

> [!IMPORTANT]
> 预览版支持向“所有者”或“参与者”角色添加事件中心数据访问特权。 但是，不再授予“所有者”和“参与者”角色的数据访问特权。 如果使用“所有者”或“参与者”角色，请改用“Azure 事件中心数据所有者”角色。


## <a name="authenticate-from-an-application"></a>通过应用程序进行身份验证
将 Azure AD 与事件中心配合使用的主要优势之一在于，不再需要在代码中存储凭据。 可以从 Microsoft 标识平台请求 OAuth 2.0 访问令牌。 Azure AD 对运行应用程序的安全主体（用户、组或服务主体）进行身份验证。 如果身份验证成功，Azure AD 会将访问令牌返回应用程序，应用程序可随之使用访问令牌对 Azure 事件中心请求授权。

以下部分介绍如何配置本机应用程序或 Web 应用程序，以便在 Microsoft 标识平台 2.0 中进行身份验证。 有关 Microsoft 标识平台 2.0 的详细信息，请参阅 [Microsoft 标识平台 (v2.0) 概述](../active-directory/develop/v2-overview.md)。

有关 OAuth 2.0 代码授权流的概述，请参阅[使用 OAuth 2.0 代码授权流来授权访问 Azure Active Directory Web 应用程序](../active-directory/develop/v2-oauth2-auth-code-flow.md)。

### <a name="register-your-application-with-an-azure-ad-tenant"></a>将应用程序注册到 Azure AD 租户
使用 Azure AD 授权访问事件中心资源的第一步是，通过 [Azure 门户](https://portal.azure.com/)在 Azure AD 租户中注册客户端应用程序。 注册客户端应用程序时，需要向 AD 提供关于应用程序的信息。 Azure AD 随后会提供客户端 ID（也称为应用程序 ID）。在运行时，可以使用该 ID 将应用程序与 Azure AD 关联。 若要详细了解客户端 ID，请参阅 [Azure Active Directory 中的应用程序对象和服务主体对象](../active-directory/develop/app-objects-and-service-principals.md)。 

下图显示了注册 Web 应用程序的步骤：

![注册应用程序](./media/authenticate-application/app-registrations-register.png)

> [!Note]
> 如果将应用程序注册为本机应用程序，可为重定向 URI 指定任何有效的 URI。 对于本机应用程序，此值不一定要是实际的 URL。 对于 Web 应用程序，重定向 URI 必须是有效的 URI，因为它指定了要向哪个 URL 提供令牌。

注册应用程序后，可在“设置”下看到“应用程序(客户端) ID”：

![已注册的应用程序的应用程序 ID](./media/authenticate-application/application-id.png)

有关向 Azure AD 注册应用程序的详细信息，请参阅[将应用程序与 Azure Active Directory](../active-directory/develop/quickstart-register-app.md) 集成。


### <a name="create-a-client-secret"></a>创建客户端机密   
请求令牌时，应用程序需要使用客户端机密来证明其身份。 若要添加客户端机密，请执行以下步骤。

1. 在 Azure 门户中导航到你的应用注册。
1. 选择“证书和机密”设置。
1. 在“客户端机密”下，选择“新建客户端机密”以创建新的机密。 
1. 提供机密说明，并选择所需的过期时间间隔。
1. 请马上将新机密的值复制到安全位置。 填充值只会显示一次。

    ![客户端机密](./media/authenticate-application/client-secret.png)


## <a name="assign-azure-roles-using-the-azure-portal"></a>使用 Azure 门户分配 Azure 角色  
将其中一个[事件中心角色](#built-in-roles-for-azure-event-hubs)分配给所需范围（事件中心命名空间、资源组、订阅）内的应用程序服务主体。 有关详细步骤，请参阅[使用 Azure 门户分配 Azure 角色](../role-based-access-control/role-assignments-portal.md)。

定义角色及其范围后，可以使用[此 GitHub 位置](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac)提供的示例测试此行为。 若要详细了解如何使用 Azure RBAC 和 Azure 门户管理对 Azure 资源的访问，请参阅[此文](..//role-based-access-control/role-assignments-portal.md)。 


### <a name="client-libraries-for-token-acquisition"></a>用于获取令牌的客户端库  
注册应用程序并向其授予在 Azure 事件中心发送/接收数据的权限后，可将代码添加到应用程序，以便对安全主体进行身份验证并获取 OAuth 2.0 令牌。 若要进行身份验证并获取令牌，可以使用 [Microsoft 标识平台身份验证库](../active-directory/develop/reference-v2-libraries.md)，或者其他支持 OpenID 或 Connect 1.0 的开源库。 然后，应用程序可以使用访问令牌来授权针对 Azure 事件中心发出的请求。

有关支持获取令牌的方案列表，请参阅[适用于 .NET 的 Microsoft 身份验证库 (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) GitHub 存储库的[方案](https://aka.ms/msal-net-scenarios)部分。

## <a name="samples"></a>示例
- [Microsoft.Azure.EventHubs 示例](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac)。 
    
    这些示例使用旧的 **Microsoft.Azure.EventHubs** 库，但你可以轻松地将其更新为使用最新的 **Azure.Messaging.EventHubs** 库。 若要将示例从使用旧库迁移到使用新库，请参阅[从 Microsoft.Azure.EventHubs 迁移到 Azure.Messaging.EventHubs 的指南](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md)。
- [Azure.Messaging.EventHubs 示例](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)

    此示例已更新为使用最新 **Azure.Messaging.EventHubs** 库。

## <a name="next-steps"></a>后续步骤
- 若要详细了解 Azure RBAC，请参阅[什么是 Azure 基于角色的访问控制 (Azure RBAC)](../role-based-access-control/overview.md)？
- 若要了解如何使用 Azure PowerShell、Azure CLI 或 REST API 分配和管理 Azure 角色分配，请参阅以下文章：
    - [使用 Azure PowerShell 添加或删除 Azure 角色分配](../role-based-access-control/role-assignments-powershell.md)  
    - [使用 Azure CLI 添加或删除 Azure 角色分配](../role-based-access-control/role-assignments-cli.md)
    - [使用 REST API 添加或删除 Azure 角色分配](../role-based-access-control/role-assignments-rest.md)
    - [使用 Azure 资源管理器模板添加 Azure 角色分配](../role-based-access-control/role-assignments-template.md)

请参阅以下相关文章：
- [使用 Azure Active Directory 对托管标识的事件中心资源访问进行身份验证](authenticate-managed-identity.md)
- [使用共享访问签名对 Azure 事件中心请求进行身份验证](authenticate-shared-access-signature.md)
- [使用 Azure Active Directory 授权访问事件中心资源](authorize-access-azure-active-directory.md)
- [使用共享访问签名授权访问事件中心资源](authorize-access-shared-access-signature.md)
