---
title: 通过 Azure 中继资源（预览版）的托管标识进行身份验证
description: 本文介绍如何使用托管标识访问 Azure 中继资源。
ms.topic: article
ms.date: 07/19/2021
ms.openlocfilehash: fa82f41056f3a5a8617bab572b8b4f312861419f
ms.sourcegitcommit: 6f21017b63520da0c9d67ca90896b8a84217d3d3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2021
ms.locfileid: "114654157"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-azure-relay-resources-preview"></a>使用 Azure Active Directory 对托管标识进行身份验证，以访问 Azure 中继资源（预览版）
[Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/overview.md)是一项跨 Azure 功能，可便于用户创建与其中运行应用程序代码的部署关联的安全标识。 然后可以将该标识与访问控制角色进行关联，后者授予的自定义权限可用于访问应用程序需要的特定 Azure 资源。

借助托管标识，Azure 平台可管理此运行时标识。 对于标识本身和需要访问的资源，都不需要在应用程序代码或配置中存储和保护访问密钥。 如果中继客户端应用在 Azure 应用服务应用程序内或在虚拟机中运行，且启用了 Azure 资源的托管标识支持，则无需处理 SAS 规则和密钥或其他任何访问令牌。 客户端应用只需要中继命名空间的终结点地址。 当应用连接时，中继通过本文后面示例中展示的操作，将托管实体的上下文绑定到客户端。 与托管标识关联后，中继客户端便能执行所有授权操作。 授权是通过关联托管标识与中继角色进行授予。

[!INCLUDE [relay-roles](./includes/relay-roles.md)]

## <a name="enable-managed-identity"></a>启用托管标识
首先，启用需要访问 Azure 中继实体（混合连接或 WCF 中继）的 Azure 资源的托管标识。 例如，如果中继客户端应用程序在 Azure VM 上运行，请按照[为 Azure VM 配置托管标识](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)一文中的说明为 VM 启用托管标识。 启用此设置后，将在 Azure Active Directory (Azure AD) 中创建新的托管服务标识。

如需支持托管标识的服务列表，请参阅[支持 Azure 资源托管标识的服务](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)。

## <a name="assign-an-azure-relay-role-to-the-managed-identity"></a>将 Azure 中继角色分配给托管标识
启用托管标识后，将 Azure 中继角色之一（Azure 中继所有者、Azure 中继侦听器或 Azure 中继发送方）分配给相应范围内的标识。 将 Azure 角色分配到某个托管标识后，将在适当的范围授予该托管标识访问中继实体的权限。

以下部分使用在 Azure VM 实例上的托管标识下运行的简单应用程序，并访问中继资源。

## <a name="sample-app-on-vm-accessing-relay-entities"></a>VM 上的示例应用访问中继实体

1. 从 GitHub 将[混合连接示例控制台应用程序](https://github.com/Azure/azure-relay/tree/master/samples/hybrid-connections/dotnet/rolebasedaccesscontrol)下载到计算机。
1. [创建 Azure VM](../virtual-machines/windows/quick-create-portal.md)。 对于本示例，使用 Windows 10 映像。 
1. 为 Azure VM 启用系统分配的标识或用户分配的标识。 如需说明，请参阅[为 VM 启用标识](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)。 
1. 将其中一个中继角色分配给所需范围的托管服务标识（中继实体、中继命名空间、资源组、订阅）。 有关详细步骤，请参阅[使用 Azure 门户分配 Azure 角色](../role-based-access-control/role-assignments-portal.md)。
1. 按照[自述文档](https://github.com/Azure/azure-relay/tree/master/samples/hybrid-connections/dotnet/rolebasedaccesscontrol#rolebasedaccesscontrol-hybrid-connection-sample)中的说明在本地计算机上以本地方式生成控制台应用。 
1. 将 \<your local path\>\RoleBasedAccessControl\bin\Debug 文件夹下的可执行文件复制到 VM。 可使用 RDP 连接到 Azure VM。 有关详细信息，请参阅[如何连接并登录到运行 Windows 的 Azure 虚拟机](../virtual-machines/windows/connect-logon.md)。
1. 按照[自述文档](https://github.com/Azure/azure-relay/tree/master/samples/hybrid-connections/dotnet/rolebasedaccesscontrol#rolebasedaccesscontrol-hybrid-connection-sample)中的说明在 Azure VM 上运行 RoleBasedAccessControl.exe。 

    > [!NOTE]
    > 按照上述相同步骤为 [WCF 中继运行控制台应用程序](https://github.com/Azure/azure-relay/tree/master/samples/wcf-relay/RoleBasedAccessControl)。

#### <a name="highlighted-code-from-the-sample"></a>示例中突出显示的代码
下面是示例中的代码，演示如何使用 Azure AD 身份验证连接到 Azure 中继服务。  

1. 使用 `TokenProvider.CreateManagedIdentityTokenProvider` 方法创建 [TokenProvider](/dotnet/api/microsoft.azure.relay.tokenprovider) 对象。 
    
    - 如果使用系统分配的托管标识：
        ```csharp
        TokenProvider.CreateManagedIdentityTokenProvider();
        ```
    - 如果使用用户分配的托管标识，从 Azure 门户中的“托管标识”页为用户分配的托管标识获取“客户端 ID”。   如需说明，请参阅[列出用户分配的托管标识](../active-directory/managed-identities-azure-resources/how-manage-user-assigned-managed-identities.md?pivots=identity-mi-methods-azp#list-user-assigned-managed-identities)。
        ```csharp
        var managedCredential = new ManagedIdentityCredential(clientId);
        tokenProvider = TokenProvider.CreateManagedIdentityTokenProvider(managedCredential);    
        ```
1. 通过传递混合连接 URI 和在上一步中创建的令牌提供程序来创建 [HybridConnectionListener](/dotnet/api/microsoft.azure.relay.hybridconnectionlistener.-ctor#Microsoft_Azure_Relay_HybridConnectionListener__ctor_System_Uri_Microsoft_Azure_Relay_TokenProvider_) 或 [HybridConnectionClient](/dotnet/api/microsoft.azure.relay.hybridconnectionclient.-ctor#microsoft-azure-relay-hybridconnectionclient-ctor(system-uri-microsoft-azure-relay-tokenprovider)) 对象。

    **侦听器：**
    ```csharp
    var listener = new HybridConnectionListener(hybridConnectionUri, tokenProvider);    
    ```
    
    发送方：
    ```csharp
    var sender = new HybridConnectionClient(hybridConnectionUri, tokenProvider);    
    ```

## <a name="next-steps"></a>后续步骤
若要了解有关 Azure 中继的详细信息，请参阅以下主题。
- [什么是中继？](relay-what-is-it.md)
- [开始使用 Azure 中继混合连接 WebSocket](relay-hybrid-connections-dotnet-get-started.md)
- [开始使用 Azure 中继混合连接 HTTP 请求](relay-hybrid-connections-http-requests-dotnet-get-started.md)



