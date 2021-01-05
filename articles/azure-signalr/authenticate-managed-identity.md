---
title: 使用 Azure Active Directory 对托管标识进行身份验证
description: 本文提供了有关使用 Azure Active Directory 访问 Azure SignalR 服务对托管标识进行身份验证的信息
author: terencefan
ms.author: tefa
ms.date: 08/03/2020
ms.service: signalr
ms.topic: conceptual
ms.openlocfilehash: 46d66451bb8f2cd6c5d4448131b5f4842a728fd0
ms.sourcegitcommit: ab829133ee7f024f9364cd731e9b14edbe96b496
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/28/2020
ms.locfileid: "97797469"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-azure-signalr-resources"></a>使用 Azure Active Directory 验证托管标识以访问 Azure SignalR 资源
Azure SignalR Service 支持 Azure Active Directory (Azure AD [azure 资源的托管标识](../active-directory/managed-identities-azure-resources/overview.md)) 身份验证。 Azure 资源的托管标识可以使用 Azure AD 凭据从 Azure 虚拟机中运行的应用程序 (Vm) 、函数应用、虚拟机规模集和其他服务）授权访问 Azure SignalR 服务资源。 将 Azure 资源的托管标识与 Azure AD 身份验证结合使用，可避免将凭据随在云中运行的应用程序一起存储。

本文介绍如何使用 Azure 虚拟机中的托管标识授予对 Azure SignalR 服务的访问权限。

## <a name="enable-managed-identities-on-a-vm"></a>在 VM 上启用托管标识
必须先为 VM 上的 Azure 资源启用托管标识，然后才能使用 Azure 资源的托管标识来授权 VM 的 Azure SIgnalR 服务资源。 若要了解如何为 Azure 资源启用托管标识，请参阅下述文章之一：

- [Azure 门户](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager 模板](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure 资源管理器客户端库](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>向 Azure AD 中的托管标识授予权限
若要从应用程序中的托管标识对 Azure SignalR 服务的请求进行授权，请先为该托管标识配置基于角色的访问控制 (RBAC) 设置。 Azure SignalR 服务定义包含用于获取或的权限的 RBAC 角色 `AccessKey` `ClientToken` 。 将 RBAC 角色分配到托管标识后，会在相应的范围内授予托管标识访问 Azure SignalR 服务的权限。

有关分配 RBAC 角色的详细信息，请参阅 [使用 Azure Active Directory 进行身份验证以访问 Azure SignalR 服务资源](authorize-access-azure-active-directory.md)。

## <a name="connect-to-azure-signalr-service-with-managed-identities"></a>连接到具有托管标识的 Azure SignalR 服务
若要连接到具有托管标识的 Azure SignalR Service，需要为该角色分配角色和相应的作用域。 本节中的过程使用一个在托管标识下运行并访问 Azure SignalR 服务资源的简单应用程序。

这里我们使用的是示例 Azure 虚拟机资源。

1. 转到“设置”，然后选择“标识”。 
1. 选择“状态”，将其切换到“启用”。 
1. 选择“保存”，保存设置。 

    ![虚拟机的托管标识](./media/authenticate/identity-virtual-machine.png)

启用此设置后，会在 Azure Active Directory (Azure AD) 中创建一个新的服务标识并将其配置到应用服务主机中。

现在，请将此服务标识分配给 Azure SignalR 服务资源中所需范围内的角色。

## <a name="assign-rbac-roles-using-the-azure-portal"></a>使用 Azure 门户分配 RBAC 角色  
若要详细了解如何使用 RBAC 和 Azure 门户管理对 Azure 资源的访问，请参阅[此文](..//role-based-access-control/role-assignments-portal.md)。 

在确定角色分配的适当范围后，在 Azure 门户中导航到该资源。 显示资源的“访问控制(IAM)”设置，并按以下说明管理角色分配：

1. 在 [Azure 门户](https://portal.azure.com/)中，导航到 SignalR 资源。
1. 选择 " **访问控制 (IAM)** 以显示 Azure SignalR 的访问控制设置。 
1. 选择“角色分配”  选项卡以查看角色分配列表。 在工具栏上选择“添加”按钮，然后选择“添加角色分配”。 

    ![工具栏上的“添加”按钮](./media/authenticate/role-assignments-add-button.png)

1. 在“添加角色分配”  页上，执行以下步骤：
    1. 选择要分配的 **Azure SignalR 角色** 。 
    1. 通过搜索找到要为其分配该角色的 **安全主体**（用户、组、服务主体）。
    1. 选择“保存”以保存角色分配。 

        ![向应用程序分配角色](./media/authenticate/assign-role-to-application.png)

    1. 分配有该角色的标识列出在该角色下。 例如，下图显示了该应用程序 `signalr-dev` 并 `signalr-service` 在 SignalR 应用服务器角色中。 
        
        ![角色分配列表](./media/authenticate/role-assignment-list.png)

可以执行类似的步骤来分配作用域为资源组或订阅的角色。 定义角色及其范围后，可以使用[此 GitHub 位置](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac)提供的示例测试此行为。

## <a name="samples-code-while-configuring-your-app-server"></a>配置应用服务器时示例代码

在以下情况下添加下列选项 `AddAzureSignalR` ：

```C#
services.AddSignalR().AddAzureSignalR(option =>
{
    option.ConnectionString = "Endpoint=https://<name>.signalr.net;AuthType=aad;";
});
```

## <a name="next-steps"></a>后续步骤
- 若要详细了解 RBAC，请参阅[什么是 Azure 基于角色的访问控制 (Azure RBAC)](../role-based-access-control/overview.md)？
- 若要了解如何使用 Azure PowerShell、Azure CLI 或 REST API 分配和管理 Azure 角色分配，请参阅以下文章：
    - [使用 Azure PowerShell 管理基于角色的访问控制 (RBAC)](../role-based-access-control/role-assignments-powershell.md)  
    - [使用 Azure CLI 管理基于角色的访问控制 (RBAC)](../role-based-access-control/role-assignments-cli.md)
    - [使用 REST API 管理基于角色的访问控制 (RBAC)](../role-based-access-control/role-assignments-rest.md)
    - [使用 Azure 资源管理器模板管理基于角色的访问控制 (RBAC)](../role-based-access-control/role-assignments-template.md)

请参阅以下相关文章：
- [使用 Azure Active Directory 访问 Azure SignalR 服务，对应用程序进行身份验证](authenticate-application.md)
- [使用 Azure Active Directory 授权访问 Azure SignalR 服务](authorize-access-azure-active-directory.md)