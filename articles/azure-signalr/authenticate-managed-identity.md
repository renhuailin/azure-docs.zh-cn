---
title: 使用 Azure Active Directory 对托管标识进行身份验证
description: 本文提供有关对使用 Azure Active Directory 访问 Azure SignalR 服务的托管标识进行身份验证的信息
author: terencefan
ms.author: tefa
ms.date: 08/03/2020
ms.service: signalr
ms.topic: conceptual
ms.openlocfilehash: e25fa98e2ca43b6697de6290ad122b6585b6a072
ms.sourcegitcommit: ef448159e4a9a95231b75a8203ca6734746cd861
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123186022"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-azure-signalr-resources"></a>使用 Azure Active Directory 对托管标识进行身份验证，以访问 Azure SignalR 资源
Azure SignalR 服务支持使用 [Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/overview.md)进行 Azure Active Directory (Azure AD) 身份验证。 Azure 资源的托管标识可以从 Azure 虚拟机 (VM)、函数应用、虚拟机规模集和其他服务中运行的应用程序使用 Azure AD 凭据授权对 Azure SignalR 服务资源的访问权限。 将 Azure 资源的托管标识与 Azure AD 身份验证结合使用，可避免将凭据随在云中运行的应用程序一起存储。

本文介绍如何使用托管标识授予对 Azure SignalR 服务的访问权限。

## <a name="enable-managed-identities"></a>启用托管标识
在使用 Azure 资源的托管标识授予对 Azure SIgnalR 服务的访问权限之前，必须首先启用 Azure 资源的托管标识。 
### <a name="for-azure-resources-on-a-vm"></a>对于 VM 上的 Azure 资源
若要了解如何在 VM 上为 Azure 资源启用托管标识，请参阅下述文章之一：

- [Azure 门户](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
  1. 转到 Azure 门户上的“设置”并选择“标识” 。 
  2. 选择“状态”，将其切换到“启用”。 
  3. 选择“保存”，保存设置。 

      ![虚拟机的托管标识](./media/authenticate/identity-virtual-machine.png)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager 模板](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure 资源管理器客户端库](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

### <a name="for-azure-functions"></a>对于 Azure Functions

请参阅[如何使用应用服务和 Azure Functions 的托管标识](../app-service/overview-managed-identity.md)。

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>向 Azure AD 中的托管标识授予权限
若要通过应用程序中的托管标识授权对 Azure SignalR 服务的请求，请先为该托管标识配置基于角色的访问控制 (RBAC) 设置。 Azure SignalR 服务定义 RBAC 角色，这些角色包含用于获取 `AccessKey` 或 `ClientToken` 的权限。 将 RBAC 角色分配给托管标识后，将在适当的范围内授予托管标识对 Azure SignalR 服务的访问权限。

按照以下说明来管理角色分配：

1. 在 [Azure 门户](https://portal.azure.com/)中，导航到你的 SignalR 资源。
1. 选择“访问控制(IAM)”，显示 Azure SignalR 的访问控制设置。 
1. 选择“角色分配”  选项卡以查看角色分配列表。 在工具栏上选择“添加”按钮，然后选择“添加角色分配”。 

    ![工具栏上的“添加”按钮](./media/authenticate/role-assignments-add-button.png)

1. 在“添加角色分配”  页上，执行以下步骤：
    1. 选择“SignalR 服务所有者”作为角色。
    1. 通过搜索找到要为其分配该角色的 **安全主体**（用户、组、服务主体）。
    1. 选择“保存”以保存角色分配。 

        ![将角色分配给应用程序](./media/authenticate/assign-role-to-application.png)

    1. 分配有该角色的标识列出在该角色下。 例如，下图显示应用程序 `signalr-dev` 和 `signalr-service` 具有 SignalR 应用服务器角色。 
        
        ![角色分配列表](./media/authenticate/role-assignment-list.png)

可采用类似的步骤来分配限定为资源组或订阅范围的角色。 定义角色及其范围后，可以使用[此 GitHub 位置](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac)提供的示例测试此行为。

有关分配 RBAC 角色的详细信息，请参阅[使用 Azure Active Directory 进行身份验证以访问 Azure SignalR 服务资源](authorize-access-azure-active-directory.md)。

若要详细了解如何使用 Azure RBAC 和 Azure 门户管理对 Azure 资源的访问，请参阅[此文](..//role-based-access-control/role-assignments-portal.md)。 

## <a name="configure-your-app"></a>配置应用程序
### <a name="app-server"></a>应用服务器

在 `AddAzureSignalR` 时，添加以下选项：

```C#
services.AddSignalR().AddAzureSignalR(option =>
{
    option.ConnectionString = "Endpoint=https://<name>.signalr.net;AuthType=aad;Version=1.0;";
});
```

### <a name="azure-functions-app"></a>Azure Functions 应用

在 Azure 门户上，添加名称为 `AzureSignalRConnectionString` 且值为 `Endpoint=https://<name>.signalr.net;AuthType=aad;` 的应用程序设置。

在本地的 `local.appsettings.json` 文件中，添加到 `Values` 部分：
```json
{
    "Values": {
        "AzureSignalRConnectionString": "Endpoint=https://<name>.signalr.net;AuthType=aad;Version=1.0;"
    }
}
```

## <a name="next-steps"></a>后续步骤
- 若要详细了解 RBAC，请参阅[什么是 Azure 基于角色的访问控制 (Azure RBAC)](../role-based-access-control/overview.md)？
- 若要了解如何使用 Azure PowerShell、Azure CLI 或 REST API 分配和管理 Azure 角色分配，请参阅以下文章：
    - [使用 Azure PowerShell 管理基于角色的访问控制 (RBAC)](../role-based-access-control/role-assignments-powershell.md)  
    - [使用 Azure CLI 管理基于角色的访问控制 (RBAC)](../role-based-access-control/role-assignments-cli.md)
    - [使用 REST API 管理基于角色的访问控制 (RBAC)](../role-based-access-control/role-assignments-rest.md)
    - [使用 Azure 资源管理器模板管理基于角色的访问控制 (RBAC)](../role-based-access-control/role-assignments-template.md)

请参阅以下相关文章：
- [使用 Azure Active Directory 对应用程序进行身份验证以访问 Azure SignalR 服务](authenticate-application.md)
- [使用 Azure Active Directory 授予对 Azure SignalR 服务的访问权限](authorize-access-azure-active-directory.md)
