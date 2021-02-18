---
title: Azure 应用配置 REST API - Azure Active Directory 授权
description: 通过 REST API 使用 Azure Active Directory 针对 Azure 应用程序配置进行授权
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: c028dcf6649da9abd196997d077b58386a5e6dd9
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2021
ms.locfileid: "101092795"
---
# <a name="azure-active-directory-authorization---rest-api-reference"></a>Azure Active Directory 授权 - REST API 参考

使用 Azure Active Directory (Azure AD) 身份验证时，由基于角色的访问控制 (RBAC) 处理授权。 RBAC 要求将用户分配到角色，以便授予对资源的访问权限。 每个角色都包含一组分配给该角色的用户能够执行的操作。

## <a name="roles"></a>角色

默认情况下，以下角色在 Azure 订阅中可用：

- **Azure 应用配置数据所有者**：此角色提供对所有操作的完全访问权限。
- **Azure 应用配置数据读取者**：此角色支持读取操作。

## <a name="actions"></a>操作

角色包含分配给该角色的用户可执行的操作的列表。 Azure 应用配置支持以下操作：

- `Microsoft.AppConfiguration/configurationStores/keyValues/read`：此操作允许对应用程序配置键/值资源（例如 /kv 和 /labels）进行读取访问。
- `Microsoft.AppConfiguration/configurationStores/keyValues/write`：此操作允许对应用配置键/值资源进行写入访问。
- `Microsoft.AppConfiguration/configurationStores/keyValues/delete`：此操作允许删除应用配置键/值资源。 请注意，删除资源将返回已删除的键/值。

## <a name="error"></a>错误

```http
HTTP/1.1 403 Forbidden
```

**原因：** 发出请求的主体没有执行请求的操作所需的权限。
**解决方案：** 将执行请求的操作所需的角色分配给发出请求的主体。

## <a name="managing-role-assignments"></a>管理角色分配

可以通过使用在所有 Azure 服务中均为标准的 [AZURE RBAC 过程](../role-based-access-control/overview.md) 来管理角色分配。 可以通过 Azure CLI、PowerShell 和 Azure 门户执行此操作。 有关详细信息，请参阅 [使用 Azure 门户分配 Azure 角色](../role-based-access-control/role-assignments-portal.md)。