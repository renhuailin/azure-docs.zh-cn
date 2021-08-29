---
author: spelluru
ms.service: service-bus-relay
ms.topic: include
ms.date: 07/19/2021
ms.author: spelluru
ms.openlocfilehash: e4fd2d455a294b247353e4983c379066d704c87a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121802883"
---
## <a name="overview"></a>概述
在某个安全主体（用户、组、应用程序）试图访问中继实体时，请求必须获得授权。 使用 Azure AD 是，访问资源的过程包括两个步骤。

1. 首先，该安全主体的身份会接受身份验证，并且会返回 OAuth 2.0 令牌。 用于请求令牌的资源名称为 `https://relay.azure.net`。 如果某个应用程序在 Azure 实体（例如 Azure VM、虚拟机规模集或 Azure 函数应用）中运行，那么，它就可以使用托管标识来访问这些资源。
2. 接下来，该令牌会作为请求的一部分传递到中继服务，以授权访问指定的资源（混合连接、WCF 中继）。 Azure Active Directory (Azure AD) 通过 [Azure 基于角色的访问控制 (Azure RBAC)](../../role-based-access-control/overview.md) 授予对受保护资源的访问权限。 Azure 中继定义了一组 Azure 内置角色，它们包含用于访问中继实体的通用权限集。 还可以定义自定义角色来访问这些数据。 有关 Azure 中继支持的内置角色的列表，请参阅[适用于 Azure 中继的 Azure 内置角色](#azure-built-in-roles-for-azure-relay)。 向中继发出请求的本机应用程序和 Web 应用程序也可以通过 Azure AD 进行授权。  

## <a name="azure-built-in-roles-for-azure-relay"></a>适用于 Azure 中继的 Azure 内置角色
对于 Azure 中继，通过 Azure 门户和 Azure 资源管理 API 对命名空间和所有相关资源进行的管理已使用 Azure RBAC 模型进行了保护。 Azure 提供以下 Azure 内置角色，用于授权访问中继命名空间：

| Role | 说明 | 
| ---- | ----------- | 
| [Azure 中继所有者](../../role-based-access-control/built-in-roles.md#azure-relay-owner) | 使用此角色授予对 Azure 中继资源的完全访问权限。 |
| [Azure 中继侦听器](../../role-based-access-control/built-in-roles.md#azure-relay-listener) | 使用此角色授予对 Azure 中继资源的侦听和实体读取访问权限。 |
| [Azure 中继发送方](../../role-based-access-control/built-in-roles.md#azure-relay-sender) | 使用此角色授予对 Azure 中继资源的发送和实体读取访问权限。 | 

## <a name="resource-scope"></a>资源范围
向安全主体分配 Azure 角色之前，请确定安全主体应具有的访问权限的范围。 最佳做法规定，始终最好只授予最小的可能范围。

以下列表描述了可以限定 Azure 中继资源访问权限范围的级别，从最窄的范围开始：

- 中继实体：角色分配应用于某个特定中继实体，例如混合连接或 WCF 中继。
- 中继命名空间：角色分配应用于该命名空间下的所有中继实体。
- 资源组：角色分配应用于该资源组下的所有中继资源。
- 订阅：角色分配应用于该订阅中所有资源组内的所有中继资源。

> [!NOTE]
> 请记住，Azure 角色分配可能需要最多五分钟的时间进行传播。 有关如何定义内置角色的详细信息，请参阅[了解角色定义](../../role-based-access-control/role-definitions.md#management-and-data-operations)。 若要了解如何创建 Azure 自定义角色，请参阅 [Azure 自定义角色](../../role-based-access-control/custom-roles.md)。 

