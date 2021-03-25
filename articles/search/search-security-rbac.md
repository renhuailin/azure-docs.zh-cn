---
title: 为 Azure 管理访问权限设置 Azure 角色
titleSuffix: Azure Cognitive Search
description: Azure 门户中的 Azure 基于角色的访问控制 (Azure RBAC)，用于控制和委托 Azure 认知搜索管理的管理任务。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/16/2020
ms.openlocfilehash: ee122727100ec0abad0dfe93b9e5f1be0276cb8e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "92519493"
---
# <a name="set-azure-roles-for-administrative-access-to-azure-cognitive-search"></a>为针对 Azure 认知搜索的管理访问权限设置 Azure 角色

对于通过门户或 Resource Manager API 管理的所有服务，Azure 提供了[基于全局角色的授权模型](../role-based-access-control/role-assignments-portal.md)。 所有者、参与者和读者角色根据分配给每个角色的 Active Directory 用户、组和安全主体的服务管理，确定服务管理的级别。 

> [!Note]
> 没有用于保护服务内容的 Azure 基于角色的访问控制 (Azure RBAC)。 对于对服务本身发出的经过身份验证的请求，可以使用管理 API 密钥或查询 API 密钥。 如果要实现针对搜索结果的、基于标识的访问，可创建安全筛选器按标识来细化结果，由此去除请求者不应具有访问权限的那些文档。 有关详细信息，请参阅[安全筛选器](search-security-trimming-for-azure-search.md)。

## <a name="management-tasks-by-role"></a>按角色划分的管理任务

对于 Azure 认知搜索，角色与支持以下管理任务的权限级别相关联：

| 角色 | 任务 |
| --- | --- |
| 所有者 |创建或删除服务或者服务上的任何对象，包括 API 密钥、索引、索引器、索引器数据源和索引器计划。<p>查看服务状态，包括计数和存储大小。<p>添加或删除角色成员身份（仅所有者才能管理角色成员身份）。<p>订阅管理员和服务所有者拥有所有者角色的自动成员身份。 |
| 参与者 | 访问级别与所有者的访问级别相同，不包括 Azure 角色管理。 例如，参与者可创建或删除对象，或查看和重新生成 [API 密钥](search-security-api-keys.md)，但不能修改角色成员身份。<br><br>[搜索服务参与者](../role-based-access-control/built-in-roles.md#search-service-contributor)等效于通用的“参与者”内置角色。 |
| 读取器 |查看服务基本信息，如服务终结点、订阅、资源组、区域、层级和容量。 还可以在“监视”选项卡上查看服务指标，例如每秒平均查询数。此角色的成员无法查看索引、索引器、数据源或技能组信息。 这包括这些对象的使用情况数据，如服务上存在多少个索引。 |

角色不授予对服务终结点的访问权限。 搜索服务操作（例如索引管理、索引填充和搜索数据的查询）可通过 API 密钥而非角色进行控制。 有关详细信息，请参阅[管理 API 密钥](search-security-api-keys.md)。

## <a name="permissions-table"></a>权限表

下表概述了 Azure 认知搜索中允许的操作，以及哪个密钥可以解锁特定操作的访问。

Azure RBAC 权限适用于门户操作和服务管理（创建、删除或更改服务或其 API 密钥）。 API 密钥是在服务存在后创建的，应用于服务上的内容操作。 此外，对于门户中与内容相关的操作（如创建或删除对象），Azure RBAC 所有者或参与者使用隐含的管理 API 密钥与服务交互。

| 操作 | 控制者 |
|-----------|-------------------------|
| 创建服务 | Azure RBAC 权限：所有者或参与者 |
| 缩放服务 | Azure RBAC 权限：所有者或参与者|
| 删除服务 | Azure RBAC 权限：所有者或参与者 |
| 管理管理员或查询密钥 | Azure RBAC 权限：所有者或参与者|
| 在门户或管理 API 中查看服务信息 | Azure RBAC 权限：所有者、参与者或读者  |
| 在门户或管理 API 中查看对象信息和指标 | Azure RBAC 权限：所有者或参与者 |
| 创建、修改、删除服务中的对象： <br>索引和组件部分（包括分析器定义、评分配置文件、CORS 选项）、索引器、数据源、同义词、建议器 | 管理密钥（如果使用 API）、Azure RBAC 所有者或参与者（如果使用门户） |
| 查询索引 | 管理密钥或查询密钥（如果使用 API）、Azure RBAC 所有者或参与者（如果使用门户） |
| 查询有关对象的系统信息，例如返回统计信息、计数和对象列表 | 管理密钥（如果使用 API）、Azure RBAC 所有者或参与者（如果使用门户） |

## <a name="next-steps"></a>后续步骤

+ [使用 PowerShell 管理](search-manage-powershell.md) 
+ [Azure 认知搜索中的性能和优化](search-performance-optimization.md)
+ [什么是 Azure 基于角色的访问控制 (Azure RBAC)](../role-based-access-control/overview.md)。
