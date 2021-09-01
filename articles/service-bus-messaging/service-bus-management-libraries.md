---
title: 以编程方式管理 Azure 服务总线命名空间和实体
description: 本文介绍如何以动态或编程方式预配服务总线命名空间和实体。
ms.topic: article
ms.date: 08/06/2021
ms.openlocfilehash: b053ff5cce51fbcd0ce56e2bdbfaff39dddb6394
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122178920"
---
# <a name="dynamically-provision-service-bus-namespaces-and-entities"></a>动态预配服务总线命名空间和实体 
Azure 服务总线提供的库可以帮助动态预配服务总线命名空间和实体。 这样可以实现复杂的部署和消息方案，并能以编程方式确定要预配的实体。

## <a name="overview"></a>概述
可以采用两种方法以编程方式管理 Azure 服务总线资源。 第一种是使用基于 [Azure 资源管理器](../azure-resource-manager/management/overview.md)的库，这些库允许用户管理命名空间、队列、主题、订阅、规则和 SAS 策略。 基于 Azure 资源管理器的库支持通过 Azure Active Directory 进行身份验证，但不支持通过连接字符串进行身份验证。 第二种方法是利用用于发送和接收消息的同一个服务总线客户端库。 客户端库还提供 API 来帮助管理现有命名空间中的队列、主题、订阅和规则。 它们支持使用连接字符串进行身份验证。 在决定采用哪种方法时，请考虑下列事项。 

在管理服务总线命名空间和实体（如队列、主题和订阅等）方面，基于 Azure 资源管理器的库提供与 Azure 门户、CLI 和 PowerShell 相同的功能。如果你一直使用 Azure 门户、CLI 或 PowerShell 进行管理操作，并且想要以动态方式执行该操作，则这些库可能是更好的选择。 

但是，如果你已经在使用服务总线的客户端库来执行服务特定操作（如发送和接收消息），并且需要管理服务总线实体，则使用同一个库可能会更方便。 客户端库中有一个 `ServiceBusAdministrationClient`（在旧的库中名为 `ServiceBusManagementClient`）提供基于 Azure 资源管理器的库提供的管理功能的子集。 必须强调的是，虽然基于 Azure 资源管理器的库允许同时管理服务总线命名空间和实体，但客户端库只允许管理现有命名空间中的实体，而不允许管理命名空间本身。

## <a name="manage-using-azure-resource-manager-based-libraries"></a>使用基于 Azure 资源管理器的库进行管理

基于 Azure 资源管理器的库允许管理命名空间、队列、主题、订阅、规则和 SAS 策略。  它们仅支持使用 Azure Active Directory (Azure AD) 进行身份验证，不支持连接字符串。 

| 语言 | 程序包 | 文档 | 示例|
|-|-|-|-|
|.NET | [Microsoft.Azure.Management.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.Management.ServiceBus/) |[Microsoft.Azure.Management.ServiceBus 的 API 参考](/dotnet/api/microsoft.azure.management.servicebus)|[.NET](https://github.com/Azure-Samples/service-bus-dotnet-management/tree/a55185bef30d1763c1a8182a3361dbb548bad436) |
| Java | [azure-resourcemanager-servicebus](https://search.maven.org/artifact/com.azure.resourcemanager/azure-resourcemanager-servicebus)|[com.azure.resourcemanager.servicebus 的 API 参考](/java/api/com.azure.resourcemanager.servicebus)|[Java](https://github.com/Azure-Samples/service-bus-java-manage-publish-subscribe-with-basic-features/tree/e4718a825e8fcfe58e5921770ff8084da67ccd89)|
| JavaScript |[@azure/arm-servicebus](https://www.npmjs.com/package/@azure/arm-servicebus)|[@azure/arm-servicebus 的 API 参考](/javascript/api/@azure/arm-servicebus/)||
|Python|[azure-mgmt-servicebus](https://pypi.org/project/azure-mgmt-servicebus/)|[azure-mgmt-servicebus 的 API 参考](/python/api/azure-mgmt-servicebus/azure.mgmt.servicebus)||


### <a name="fluent-net-and-java-libraries"></a>Fluent .NET 和 Java 库
基于 Azure 资源管理器的库有 Fluent 版本。 

|语言|程序包|文档|
|-|-|-|
|.NET|[Microsoft.Azure.Management.ServiceBus.Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.ServiceBus.Fluent/) |[Microsoft.Azure.Management.ServiceBus.Fluent 的 API 参考](/dotnet/api/microsoft.azure.management.servicebus.fluent) |
| Java|[azure-resourcemanager-servicebus](https://search.maven.org/artifact/com.azure.resourcemanager/azure-resourcemanager-servicebus)|[com.azure.resourcemanager.servicebus.fluent 的 API 参考](/java/api/com.azure.resourcemanager.servicebus.fluent) |

## <a name="manage-using-service-bus-client-libraries"></a>使用服务总线客户端库进行管理 

用于发送和接收消息等操作的服务总线客户端库也可用于管理现有服务总线命名空间中的队列、主题、订阅和规则。 此功能可通过最新的库中的 `ServiceBusAdministrationClient` 和旧库中的 `ServiceBusManagementClient` 来获取。 强烈建议使用最新的库。

### <a name="latest-service-bus-libraries"></a>最新的服务总线库
|语言|程序包|文档|示例|
|-|-|-|-|
|.NET|  [Azure.Messaging.ServiceBus](https://www.nuget.org/packages/Azure.Messaging.ServiceBus)|[ServiceBusAdministrationClient](/dotnet/api/azure.messaging.servicebus.administration.servicebusadministrationclient)|[.NET](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/)|
|Java|[azure-messaging-servicebus](https://search.maven.org/artifact/com.azure/azure-messaging-servicebus)|[ServiceBusAdministrationAsyncClient](/java/api/com.azure.messaging.servicebus.administration.servicebusadministrationasyncclient)、[ServiceBusAdministrationClient](/java/api/com.azure.messaging.servicebus.administration.servicebusadministrationclient)| [Java](/samples/azure/azure-sdk-for-java/servicebus-samples/)|
|JavaScript|[@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus)|[ServiceBusAdministrationClient](/javascript/api/@azure/service-bus/servicebusadministrationclient)|[JavaScript](/samples/azure/azure-sdk-for-js/service-bus-javascript/)/[TypeScript](/samples/azure/azure-sdk-for-js/service-bus-typescript/)|
|Python|[azure-servicebus](https://pypi.org/project/azure-servicebus/)|[ServiceBusAdministrationClient](/python/api/azure-servicebus/azure.servicebus.management.servicebusadministrationclient)|[Python](/samples/azure/azure-sdk-for-python/servicebus-samples/)|

### <a name="legacy-service-bus-libraries"></a>旧的 Azure 服务总线库
|语言|程序包|文档|示例|
|-|-|-|-|
|.NET|[Microsoft.Azure.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/)|[ManagementClient](/dotnet/api/microsoft.azure.servicebus.management.managementclient)|[.NET](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus)|
|Java|[azure-mgmt-servicebus](https://search.maven.org/artifact/com.microsoft.azure/azure-mgmt-servicebus)|[ManagementClientAsync](/java/api/com.microsoft.azure.servicebus.management.managementclientasync)、[ManagementClient](/java/api/com.microsoft.azure.servicebus.management.managementclient)|[Java](https://github.com/Azure/azure-service-bus/tree/master/samples/Java)|


## <a name="next-steps"></a>后续步骤
- 使用最新的服务总线库向队列发送消息并从队列接收消息：[.NET](./service-bus-dotnet-get-started-with-queues.md#send-messages)、[Java](./service-bus-java-how-to-use-queues.md)、[JavaScript](./service-bus-nodejs-how-to-use-queues.md)、[Python](./service-bus-python-how-to-use-queues.md)
- 使用最新的服务总线库向主题发送消息并从订阅接收消息：[.NET](./service-bus-dotnet-how-to-use-topics-subscriptions.md)、[Java](./service-bus-java-how-to-use-topics-subscriptions.md)、[JavaScript](./service-bus-nodejs-how-to-use-topics-subscriptions.md)、[Python](./service-bus-python-how-to-use-topics-subscriptions.md)