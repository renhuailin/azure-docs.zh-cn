---
title: 使用 Azure Active Directory 授予访问权限
description: 本文提供有关使用 Azure Active Directory 授权访问事件中心资源的信息。
ms.topic: conceptual
ms.date: 09/20/2021
ms.openlocfilehash: f2076d5fe8c1e77523a57337e339a50f3af3b420
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129361642"
---
# <a name="authorize-access-to-event-hubs-resources-using-azure-active-directory"></a>使用 Azure Active Directory 授权访问事件中心资源
Azure 事件中心支持使用 Azure Active Directory (Azure AD) 授权对事件中心资源的请求。 可以通过 Azure AD 使用 Azure 基于角色的访问控制 (Azure RBAC) 向安全主体授予权限，该安全主体可能是用户，也可能是应用程序服务主体。 若要详细了解角色和角色分配，请参阅[了解不同的角色](../role-based-access-control/overview.md)。

## <a name="overview"></a>概述
当安全主体（用户或应用程序）尝试访问事件中心资源时，必须授权该请求。 使用 Azure AD 是，访问资源的过程包括两个步骤。 

 1. 首先，验证安全主体的身份并返回 OAuth 2.0 令牌。 请求令牌的资源名称是 `https://eventhubs.azure.net/`，它对于所有云/租户都是相同的。 对于 Kafka 客户端，请求令牌的资源为 `https://<namespace>.servicebus.windows.net`。
 1. 接下来，将该令牌作为请求的一部分传递给事件中心服务，用于授权访问指定的资源。

身份验证步骤要求应用程序请求包含在运行时使用的 OAuth 2.0 访问令牌。 如果应用程序在 Azure 实体（如 Azure VM、虚拟机规模集或 Azure 函数应用）中运行，它可以使用托管标识来访问资源。 若要了解如何对托管标识向事件中心服务发出的请求进行身份验证，请参阅[对使用 Azure Active Directory 和 Azure 资源的托管标识访问 Azure 事件中心资源进行身份验证](authenticate-managed-identity.md)。 

授权步骤要求将一个或多个 Azure 角色分配给安全主体。 Azure 事件中心提供了 Azure 角色，其中包括事件中心资源的权限集。 分配给安全主体的角色确定了该主体拥有的权限。 有关 Azure 角色的详细信息，请参阅 [Azure 事件中心的内置 Azure 角色](#azure-built-in-roles-for-azure-event-hubs)。 

向事件中心发出请求的本机应用程序和 Web 应用程序也可以使用 Azure AD 进行授权。 若要了解如何请求访问令牌并使用它来授权对事件中心资源的请求，请参阅[对使用 Azure AD 从应用程序访问 Azure 事件中心进行身份验证](authenticate-application.md)。 

## <a name="assign-azure-roles-for-access-rights"></a>分配 Azure 角色以授予访问权限
Azure Active Directory (Azure AD) 通过 [Azure 基于角色的访问控制 (Azure RBAC)](../role-based-access-control/overview.md) 授予对受保护资源的访问权限。 Azure 事件中心定义了一组内置的 Azure 角色，它们包含用于访问事件中心数据的通用权限集。你也可以定义用于访问数据的自定义角色。

将 Azure 角色分配到 Azure AD 安全主体后，Azure 会向该安全主体授予对这些资源的访问权限。 访问权限的范围可限定为订阅、资源组、事件中心命名空间级别或其下的任何资源。 Azure AD 安全主体可以是用户或应用程序服务主体，也可以是 [Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/overview.md)。

## <a name="azure-built-in-roles-for-azure-event-hubs"></a>Azure 事件中心的内置 Azure 角色
Azure 提供了以下 Azure 内置角色，用于通过 Azure AD 和 OAuth 授予对事件中心数据的访问权限：

| 角色 | 说明 | 
| ---- | ----------- | 
| [Azure 事件中心数据所有者](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner) | 使用此角色可以授予对事件中心资源的完全访问权限。 |
| [Azure 事件中心数据发送者](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender) | 使用此角色可以授予对事件中心资源的发送访问权限。 |
| [Azure 事件中心数据接收者](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver) | 使用此角色可以授予对事件中心资源的使用/接收访问权限。 |

要了解架构注册表内置角色，请参阅[架构注册表角色](schema-registry-overview.md#azure-role-based-access-control)。

## <a name="resource-scope"></a>资源范围 
向安全主体分配 Azure 角色之前，请确定安全主体应具有的访问权限的范围。 最佳做法指出，最好是授予尽可能小的范围。

以下列表描述了可将事件中心资源访问权限限定到哪些级别，从最小的范围开始：

- **使用者组**：在此范围，角色分配仅应用到此实体。 目前，Azure 门户不支持在此级别向安全主体分配 Azure 角色。 
- **事件中心**：角色分配将应用到事件中心实体及其下面的使用者组。
- **命名空间**：角色分配横跨命名空间下事件中心的整个拓扑，并延伸至与之关联的使用者组。
- **资源组**：角色分配将应用到资源组下的所有事件中心资源。
- **订阅**：角色分配将应用到订阅的所有资源组中的所有事件中心资源。

> [!NOTE]
> - 请记住，Azure 角色分配可能需要最多五分钟的时间进行传播。 
> - 此内容适用于事件中心及用于 Apache Kafka 的事件中心。 有关用于 Kafka 的事件中心支持的详细信息，请参阅[用于 Kafka 的事件中心 - 安全性和身份验证](event-hubs-for-kafka-ecosystem-overview.md#security-and-authentication)。


有关如何定义内置角色的详细信息，请参阅[了解角色定义](../role-based-access-control/role-definitions.md#control-and-data-actions)。 若要了解如何创建 Azure 自定义角色，请参阅 [Azure 自定义角色](../role-based-access-control/custom-roles.md)。



## <a name="samples"></a>示例
- [Microsoft.Azure.EventHubs 示例](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac)。 
    
    这些示例使用旧的 **Microsoft.Azure.EventHubs** 库，但你可以轻松地将其更新为使用最新的 **Azure.Messaging.EventHubs** 库。 若要将示例从使用旧库迁移到使用新库，请参阅[从 Microsoft.Azure.EventHubs 迁移到 Azure.Messaging.EventHubs 的指南](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md)。
- [Azure.Messaging.EventHubs 示例](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)

    此示例已更新为使用最新 **Azure.Messaging.EventHubs** 库。
- [用于 Kafka 的事件中心 - OAuth 示例](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth)。 


## <a name="next-steps"></a>后续步骤
- 若要了解如何向安全主体分配内置 Azure 角色，请参阅[使用 Azure Active Directory 对事件中心资源访问进行身份验证](authenticate-application.md)。
- 了解[如何使用 Azure RBAC 创建自定义角色](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/CustomRole)。
- 了解[如何将 Azure Active Directory 与 EH 配合使用](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/AzureEventHubsSDK)

请参阅以下相关文章：

- [使用 Azure Active Directory 对应用程序的 Azure 事件中心请求进行身份验证](authenticate-application.md)
- [使用 Azure Active Directory 对托管标识的事件中心资源访问进行身份验证](authenticate-managed-identity.md)
- [使用共享访问签名对 Azure 事件中心请求进行身份验证](authenticate-shared-access-signature.md)
- [使用共享访问签名授权访问事件中心资源](authorize-access-shared-access-signature.md)
