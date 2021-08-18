---
title: 结合使用 Azure 资源的托管标识与 Azure 服务总线
description: 本文介绍如何使用托管标识访问 Azure 服务总线实体（队列、主题和订阅）。
ms.topic: article
ms.date: 06/14/2021
ms.custom: subject-rbac-steps
ms.openlocfilehash: ed6f7d495466139a7d1a98aed7d5323f7ad4c074
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/15/2021
ms.locfileid: "112123216"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-azure-service-bus-resources"></a>使用 Azure Active Directory 对托管标识进行身份验证，以便访问 Azure 服务总线资源
[Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/overview.md)是一项跨 Azure 功能，可便于用户创建与其中运行应用程序代码的部署关联的安全标识。 然后可以将该标识与访问控制角色进行关联，后者授予的自定义权限可用于访问应用程序需要的特定 Azure 资源。

借助托管标识，Azure 平台可管理此运行时标识。 对于标识本身和需要访问的资源，都不需要在应用程序代码或配置中存储和保护访问密钥。 如果服务总线客户端应用在 Azure 应用服务应用程序内或在虚拟机中运行，且启用了 Azure 资源的托管标识支持，则无需处理 SAS 规则和密钥或其他任何访问令牌。 客户端应用只需要服务总线消息传递命名空间的终结点地址。 当应用连接时，服务总线通过本文后面示例中展示的操作，将托管实体的上下文绑定到客户端。 与托管标识关联后，服务总线客户端便能执行所有授权操作。 授权是通过关联托管标识与服务总线角色进行授予。 

## <a name="overview"></a>概述
当某个安全主体（用户、组或应用程序）尝试访问服务总线实体时，请求必须获得授权。 使用 Azure AD 是，访问资源的过程包括两个步骤。 

 1. 首先，验证安全主体的身份并返回 OAuth 2.0 令牌。 用于请求令牌的资源名称为 `https://servicebus.azure.net`。
 1. 接下来，将该令牌作为请求的一部分传递给服务总线服务，用于授权访问指定的资源。

身份验证步骤要求应用程序请求包含在运行时使用的 OAuth 2.0 访问令牌。 如果应用程序在 Azure 实体（如 Azure VM、虚拟机规模集或 Azure 函数应用）中运行，它可以使用托管标识来访问资源。 

授权步骤要求将一个或多个 Azure 角色分配给安全主体。 Azure 服务总线提供 Azure 角色，这些角色涵盖了针对服务总线资源的权限集。 分配给安全主体的角色确定了该主体拥有的权限。 若要详细了解如何向 Azure 服务总线分配 Azure 角色，请参阅[针对 Azure 服务总线的 Azure 内置角色](#azure-built-in-roles-for-azure-service-bus)。 

向服务总线发出请求的本机应用程序和 Web 应用程序也可以使用 Azure AD 进行授权。 本文介绍如何请求访问令牌，并使用它针对服务总线资源进行请求授权。 


## <a name="assigning-azure-roles-for-access-rights"></a>分配 Azure 角色以授予访问权限
Azure Active Directory (Azure AD) 通过 [Azure 基于角色的访问控制 (Azure RBAC)](../role-based-access-control/overview.md) 授予对受保护资源的访问权限。 Azure 服务总线定义了一组 Azure 内置角色，它们包含用于访问服务总线实体的通用权限集。你也可以定义用于访问数据的自定义角色。

将 Azure 角色分配到 Azure AD 安全主体后，Azure 会向该安全主体授予对这些资源的访问权限。 访问权限可以局限到订阅、资源组或服务总线命名空间级别。 Azure AD 安全主体可以是用户、组、应用程序服务主体，也可以是 Azure 资源的托管标识。

## <a name="azure-built-in-roles-for-azure-service-bus"></a>适用于 Azure 服务总线的 Azure 内置角色
对于 Azure 服务总线，通过 Azure 门户和 Azure 资源管理 API 对命名空间和所有相关资源的管理已使用 Azure RBAC 模型进行了保护。 Azure 提供以下 Azure 内置角色，用于授予对服务总线命名空间的访问权限：

- [Azure 服务总线数据所有者](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner)：允许对服务总线命名空间及其实体（队列、主题、订阅和筛选器）进行数据访问
- [Azure 服务总线数据发送者](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender)：使用此角色可以为服务总线命名空间及其实体提供发送访问权限。
- [Azure 服务总线数据接收者](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver)：使用此角色可以为服务总线命名空间及其实体提供接收访问权限。 

## <a name="resource-scope"></a>资源范围 
向安全主体分配 Azure 角色之前，请确定安全主体应具有的访问权限的范围。 最佳做法指出，最好是授予尽可能小的范围。

以下列表描述了可将服务总线资源访问权限限定到哪些级别，从最小的范围开始：

- **队列**、**主题** 或 **订阅**：角色分配适用于特定的服务总线实体。 目前，Azure 门户不支持在订阅级别为服务总线 Azure 角色分配用户/组/托管标识。 下面是使用 Azure CLI 命令 [az-role-assignment-create](/cli/azure/role/assignment?#az_role_assignment_create) 为服务总线 Azure 角色分配标识的示例： 

    ```azurecli
    az role assignment create \
        --role $service_bus_role \
        --assignee $assignee_id \
        --scope /subscriptions/$subscription_id/resourceGroups/$resource_group/providers/Microsoft.ServiceBus/namespaces/$service_bus_namespace/topics/$service_bus_topic/subscriptions/$service_bus_subscription
    ```
- **服务总线命名空间**：角色分配横跨命名空间中服务总线的整个拓扑，并延伸至与之关联的使用者组。
- **资源组**：角色分配适用于资源组下的所有服务总线资源。
- **订阅**：角色分配适用于订阅的所有资源组中的所有服务总线资源。

> [!NOTE]
> 请记住，Azure 角色分配可能需要最多五分钟的时间进行传播。 

有关如何定义内置角色的详细信息，请参阅[了解角色定义](../role-based-access-control/role-definitions.md#management-and-data-operations)。 若要了解如何创建 Azure 自定义角色，请参阅 [Azure 自定义角色](../role-based-access-control/custom-roles.md)。

## <a name="enable-managed-identities-on-a-vm"></a>在 VM 上启用托管标识
在使用 Azure 资源的托管标识对 VM 中的服务总线资源授权之前，必须首先在 VM 上启用 Azure 资源的托管标识。 若要了解如何为 Azure 资源启用托管标识，请参阅下述文章之一：

- [Azure 门户](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager 模板](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure 资源管理器客户端库](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>向 Azure AD 中的托管标识授予权限
若要通过应用程序中的托管标识对发给服务总线服务的请求授权，请先为该托管标识配置 Azure 基于角色的访问控制 (Azure RBAC) 设置。 Azure 服务总线定义 Azure 角色，这些角色涵盖了从服务总线进行发送和读取操作所需的权限。 将 Azure 角色分配到某个托管标识后，将在适当的范围授予该托管标识访问服务总线实体的权限。

若要详细了解如何分配 Azure 角色，请参阅[使用 Azure Active Directory 进行身份验证和授权以访问服务总线资源](authenticate-application.md#azure-built-in-roles-for-azure-service-bus)。

## <a name="use-service-bus-with-managed-identities-for-azure-resources"></a>结合使用服务总线与 Azure 资源的托管标识
若要将服务总线与托管标识配合使用，需为标识分配角色和相应的范围。 此部分的过程使用一个简单的应用程序，该应用程序在托管标识下运行并访问服务总线资源。

在这里，我们将使用一个在 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)中托管的示例 Web 应用程序。 有关如何创建 Web 应用程序的分步说明，请参阅[在 Azure 中创建 ASP.NET Core Web 应用](../app-service/quickstart-dotnetcore.md)

创建应用程序后，请执行以下步骤： 

1. 转到“设置”，然后选择“标识”。 
1. 选择“状态”，将其切换到“启用”。 
1. 选择“保存”，保存设置。 

    ![Web 应用的托管标识](./media/service-bus-managed-service-identity/identity-web-app.png)

启用此设置后，会在 Azure Active Directory (Azure AD) 中创建一个新的服务标识并将其配置到应用服务主机中。

### <a name="to-assign-azure-roles-using-the-azure-portal"></a>使用 Azure 门户分配 Azure 角色
将其中一个[服务总线角色](#azure-built-in-roles-for-azure-service-bus)分配给所需范围（服务总线命名空间、资源组、订阅）的托管服务标识。 有关详细步骤，请参阅[使用 Azure 门户分配 Azure 角色](../role-based-access-control/role-assignments-portal.md)。 

> [!NOTE]
> 如需支持托管标识的服务列表，请参阅[支持 Azure 资源托管标识的服务](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)。

### <a name="run-the-app"></a>运行应用程序
现在，修改你创建的 ASP.NET 应用程序的默认页面。 可以使用[此 GitHub 存储库](https://github.com/Azure-Samples/app-service-msi-servicebus-dotnet)中的 Web 应用程序代码。  

Default.aspx 页是登陆页面。 可以在 Default.aspx.cs 文件中找到代码。 结果是一个最小的 Web 应用程序，其中包含几个输入字段以及用来连接到服务总线以发送或接收消息的 **send** 和 **receive** 按钮。

请注意 [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient) 对象是如何通过采用 TokenCredential 的构造函数进行初始化的。 DefaultAzureCredential 派生自 TokenCredential，可在此处传递。 因此，不需要保留和使用任何机密。 从托管标识上下文到服务总线的流以及授权握手都是由令牌凭据自动处理。 这是比使用 SAS 更简单的模型。

进行这些更改后，发布并运行应用程序。 若要轻松获取正确的发布数据，可下载发布配置文件，并在 Visual Studio 中导入它：

![获取发布配置文件](./media/service-bus-managed-service-identity/msi3.png)
 
若要发送或接收消息，请输入所创建的命名空间和实体的名称。 然后，单击“发送”  或“接收”  。


> [!NOTE]
> - 托管标识仅适用于 Azure 环境、应用服务、Azure VM 和规模集。 对于 .NET 应用程序，Microsoft.Azure.Services.AppAuthentication 库（由服务总线 NuGet 包使用）提供此协议的摘要并支持本地开发体验。 此库还允许通过 Visual Studio、Azure CLI 2.0 或 Active Directory 集成身份验证使用用户帐户，在开发计算机上对代码进行本地测试。 有关此库的本地开发选项的详细信息，请参阅[使用 .NET 向 Azure Key Vault 进行服务到服务身份验证](/dotnet/api/overview/azure/service-to-service-authentication)。  

## <a name="next-steps"></a>后续步骤

若要了解有关服务总线消息传送的详细信息，请参阅以下主题：

* [服务总线队列、主题和订阅](service-bus-queues-topics-subscriptions.md)
* [服务总线队列入门](service-bus-dotnet-get-started-with-queues.md)
* [如何使用服务总线主题和订阅](service-bus-dotnet-how-to-use-topics-subscriptions.md)
