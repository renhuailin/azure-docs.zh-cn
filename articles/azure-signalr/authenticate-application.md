---
title: 对应用程序进行身份验证以访问 Azure SignalR 服务
description: 本文介绍如何使用 Azure Active Directory 对应用程序进行身份验证以访问 Azure SignalR 服务
author: terencefan
ms.author: tefa
ms.service: signalr
ms.topic: conceptual
ms.date: 08/03/2020
ms.openlocfilehash: 597b69c1180ea1fb2a6812d648f8b8ad37707d66
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "101092610"
---
# <a name="authenticate-an-application-with-azure-active-directory-to-access-azure-signalr-service"></a>使用 Azure Active Directory 对应用程序进行身份验证以访问 Azure SignalR 服务
Microsoft Azure 基于 Azure Active Directory (Azure AD) 针对资源和应用程序提供了集成的访问控制管理功能。 将 Azure AD 与 Azure SignalR 服务配合使用的主要优势在于，不再需要将凭据存储在代码中。 可以从 Microsoft 标识平台请求 OAuth 2.0 访问令牌。 用于请求令牌的资源名称为 `https://signalr.azure.com/`。 Azure AD 对运行应用程序的安全主体（应用程序、资源组或服务主体）进行身份验证。 如果身份验证成功，Azure AD 会将访问令牌返回应用程序，应用程序可随之使用访问令牌对 Azure SignalR 服务资源请求进行授权。

将角色分配到 Azure AD 安全主体后，Azure 会向该安全主体授予对这些资源的访问权限。 访问权限可被限定为订阅级别、资源组或 Azure SignalR 资源。 Azure AD 安全主体可向用户、组、应用程序服务主体或 [Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/overview.md)分配角色。 

> [!NOTE]
> 角色定义是权限的集合。 基于角色的访问控制 (RBAC) 控制如何通过角色分配实施这些权限。 角色分配包含三个要素：安全主体、角色订阅和范围。 有关详细信息，请参阅[了解不同的角色](../role-based-access-control/overview.md)。

## <a name="register-your-application-with-an-azure-ad-tenant"></a>将应用程序注册到 Azure AD 租户
使用 Azure AD 授权 Azure SignalR 服务资源的第一步是，通过 [Azure门户](https://portal.azure.com/)向 Azure AD 租户注册应用程序。 注册应用程序时，需要向 AD 提供应用程序的相关信息。 Azure AD 随后会提供客户端 ID（也称为应用程序 ID）。在运行时，可以使用该 ID 将应用程序与 Azure AD 关联。 若要详细了解客户端 ID，请参阅 [Azure Active Directory 中的应用程序对象和服务主体对象](../active-directory/develop/app-objects-and-service-principals.md)。 

下图显示了注册 Web 应用程序的步骤：

![注册应用程序](./media/authenticate/app-registrations-register.png)

> [!Note]
> 如果将应用程序注册为本机应用程序，可为重定向 URI 指定任何有效的 URI。 对于本机应用程序，此值不一定要是实际的 URL。 对于 Web 应用程序，重定向 URI 必须是有效的 URI，因为它指定了要向哪个 URL 提供令牌。

注册应用程序后，可在“设置”下看到“应用程序(客户端) ID”：

![已注册的应用程序的应用程序 ID](./media/authenticate/application-id.png)

有关向 Azure AD 注册应用程序的详细信息，请参阅[将应用程序与 Azure Active Directory](../active-directory/develop/quickstart-register-app.md) 集成。


### <a name="create-a-client-secret"></a>创建客户端机密   
请求令牌时，应用程序需要使用客户端机密来证明其身份。 若要添加客户端机密，请执行以下步骤。

1. 在 Azure 门户中导航到你的应用注册。
1. 选择“证书和机密”设置。
1. 在“客户端机密”下，选择“新建客户端机密”以创建新的机密。 
1. 提供机密说明，并选择所需的过期时间间隔。
1. 请马上将新机密的值复制到安全位置。 填充值只会显示一次。

![创建客户端密码](./media/authenticate/client-secret.png)

### <a name="upload-a-certificate"></a>上传证书

也可上传证书，而不是创建客户端密码。

![上传证书](./media/authenticate/certification.png)

## <a name="assign-azure-roles-using-the-azure-portal"></a>使用 Azure 门户分配 Azure 角色  
若要详细了解如何使用 Azure RBAC 和 Azure 门户管理对 Azure 资源的访问，请参阅[此文](..//role-based-access-control/role-assignments-portal.md)。 

在确定角色分配的适当范围后，在 Azure 门户中导航到该资源。 显示资源的“访问控制(IAM)”设置，并按以下说明管理角色分配：

1. 在 [Azure 门户](https://portal.azure.com/)中，导航到你的 SignalR 资源。
1. 选择“访问控制(IAM)”，显示 Azure SignalR 的访问控制设置。 
1. 选择“角色分配”  选项卡以查看角色分配列表。 在工具栏上选择“添加”按钮，然后选择“添加角色分配”。 

    ![工具栏上的“添加”按钮](./media/authenticate/role-assignments-add-button.png)

1. 在“添加角色分配”  页上，执行以下步骤：
    1. 选择要分配的 Azure SignalR 角色。 
    1. 通过搜索找到要为其分配该角色的 **安全主体**（用户、组、服务主体）。
    1. 选择“保存”以保存角色分配。 

        ![将角色分配给应用程序](./media/authenticate/assign-role-to-application.png)

    1. 分配有该角色的标识列出在该角色下。 例如，下图显示应用程序 `signalr-dev` 和 `signalr-service` 具有 SignalR 应用服务器角色。 
        
        ![角色分配列表](./media/authenticate/role-assignment-list.png)

可采用类似的步骤来分配限定为资源组或订阅范围的角色。 定义角色及其范围后，可以使用[此 GitHub 位置](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac)提供的示例测试此行为。

## <a name="sample-codes-while-configuring-your-app-server"></a>配置应用服务器时的示例代码。

在 `AddAzureSignalR` 时，添加以下选项：

```C#
services.AddSignalR().AddAzureSignalR(option =>
{
    option.ConnectionString = "Endpoint=https://<name>.signalr.net;AuthType=aad;clientId=<clientId>;clientSecret=<clientSecret>;tenantId=<tenantId>";
});
```

或者仅在 `appsettings.json` 文件中配置 `ConnectionString`。

```json
{
"Azure": {
    "SignalR": {
      "Enabled": true,
      "ConnectionString": "Endpoint=https://<name>.signalr.net;AuthType=aad;clientId=<clientId>;clientSecret=<clientSecret>;tenantId=<tenantId>"
    }
  },
}
```

使用 `certificate` 时，将 `clientSecret` 更改为 `clientCert`，如下所示：

```C#
    option.ConnectionString = "Endpoint=https://<name>.signalr.net;AuthType=aad;clientId=<clientId>;clientCert=<clientCertFilepath>;tenantId=<tenantId>";
```

## <a name="next-steps"></a>后续步骤
- 若要详细了解 RBAC，请参阅[什么是 Azure 基于角色的访问控制 (Azure RBAC)](../role-based-access-control/overview.md)？
- 若要了解如何使用 Azure PowerShell、Azure CLI 或 REST API 分配和管理 Azure 角色分配，请参阅以下文章：
    - [使用 Azure PowerShell 管理基于角色的访问控制 (RBAC)](../role-based-access-control/role-assignments-powershell.md)  
    - [使用 Azure CLI 管理基于角色的访问控制 (RBAC)](../role-based-access-control/role-assignments-cli.md)
    - [使用 REST API 管理基于角色的访问控制 (RBAC)](../role-based-access-control/role-assignments-rest.md)
    - [使用 Azure 资源管理器模板管理基于角色的访问控制 (RBAC)](../role-based-access-control/role-assignments-template.md)

请参阅以下相关文章：
- [使用 Azure Active Directory 对托管标识进行身份验证，以访问 Azure SignalR 服务](authenticate-managed-identity.md)
- [使用 Azure Active Directory 授予对 Azure SignalR 服务的访问权限](authorize-access-azure-active-directory.md)