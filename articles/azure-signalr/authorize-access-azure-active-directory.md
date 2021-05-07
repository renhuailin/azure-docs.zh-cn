---
title: 使用 Azure Active Directory 授予访问权限
description: 本文提供了关于使用 Azure Active Directory 授予 Azure SignalR 服务资源访问权限的信息。
author: terencefan
ms.author: tefa
ms.date: 08/03/2020
ms.service: signalr
ms.topic: conceptual
ms.openlocfilehash: 37c2e41b5c81f941245b895ecd144baee3b9db6d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "97797460"
---
# <a name="authorize-access-to-azure-signalr-service-resources-using-azure-active-directory"></a>使用 Azure Active Directory 授予 Azure SignalR 服务资源的访问权限
Azure SignalR 服务支持使用 Azure Active Directory (Azure AD) 授予 Azure SignalR 服务资源的请求权限。 可以通过 Azure AD 使用基于角色的访问控制 (RBAC) 授予对服务主体的访问权限，该服务主体可能是用户或应用程序服务主体。 若要详细了解角色和角色分配，请参阅[了解不同的角色](../role-based-access-control/overview.md)。

## <a name="overview"></a>概述
当某个安全主体（应用程序）尝试访问 Azure SignalR 服务资源时，请求必须获得授权。 使用 Azure AD 是，访问资源的过程包括两个步骤。 

 1. 首先，验证安全主体的身份并返回 OAuth 2.0 令牌。 用于请求令牌的资源名称为 `https://signalr.azure.com/`。
 2. 接下来，将该令牌作为请求的一部分传递给 Azure SignalR 服务，用于授权访问指定的资源。

身份验证步骤要求应用程序请求在运行时包含 OAuth 2.0 访问令牌。 如果集线器服务器在 Azure 实体（如 Azure VM、虚拟机规模集或 Azure 函数应用）中运行，它可以使用托管标识来访问资源。 若要了解如何对托管标识向 Azure SignalR 服务发出的请求进行身份验证，请参阅[对使用 Azure Active Directory 和 Azure 资源的托管标识访问 Azure SignalR 服务资源进行身份验证](authenticate-managed-identity.md)。 

授权步骤需要将一个或多个 RBAC 角色分配给安全主体。 Azure SignalR 服务提供 RBAC 角色，这些角色涵盖了针对 Azure SignalR 资源的权限集。 分配给安全主体的角色确定了该主体拥有的权限。 有关 RBAC 角色的详细信息，请参阅 [Azure SignalR 服务的 Azure 内置角色](#azure-built-in-roles-for-azure-signalr-service)。 

未在 Azure 实体内运行的 SignalR 集线器服务器还可以使用 Azure AD 进行授权。 若要了解如何请求访问令牌并使用它来授权对 Azure SignalR 服务资源的请求，请参阅[从应用程序中使用 Azure AD 对 Azure SignalR 服务的访问进行身份验证](authenticate-application.md)。 

## <a name="azure-built-in-roles-for-azure-signalr-service"></a>Azure SignalR 服务的 Azure 内置角色

- [SignalR 应用服务器]
- [SignalR 服务读者]
- [SignalR 服务所有者]

## <a name="assign-rbac-roles-for-access-rights"></a>分配 RBAC 角色以授予访问权限
Azure Active Directory (Azure AD) 通过[基于角色的访问控制 (RBAC)](../role-based-access-control/overview.md) 授权访问受保护的资源。 Azure SignalR 服务定义了一组 Azure 内置角色，涵盖了访问 Azure SignalR 服务的通用权限集。你也可以定义用于访问资源的自定义角色。

将 RBAC 角色分配到 Azure AD 安全主体后，Azure 会向该安全主体授予对这些资源的访问权限。 访问权限可被限定为订阅级别、资源组或 Azure SignalR 服务资源。 Azure AD 安全主体可以是用户、应用程序或 [Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/overview.md)。

## <a name="built-in-roles-for-azure-signalr-service"></a>Azure SignalR 服务的内置角色
Azure 提供下列 Azure 内置角色，用于使用 Azure AD 和 OAuth 授予 Azure SignalR 服务资源的访问权限：

### <a name="signalr-app-server"></a>SignalR 应用服务器

使用此角色提供访问权限，允许获取临时访问密钥以对客户端令牌进行签名。

### <a name="signalr-serverless-contributor"></a>SignalR 无服务器参与者

使用此角色授予访问权限，允许直接从 Azure SignalR 服务获取客户端令牌。

## <a name="next-steps"></a>后续步骤

请参阅以下相关文章：

- [使用 Azure AD 对应用程序进行身份验证以访问 Azure SignalR 服务](authenticate-application.md)
- [使用 Azure AD 对托管标识进行身份验证以访问 Azure SignalR 服务](authenticate-managed-identity.md)