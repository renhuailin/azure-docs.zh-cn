---
title: 通过 Azure 角色授权访问
titleSuffix: Azure Cognitive Search
description: Azure 门户中的 Azure 基于角色的访问控制 (Azure RBAC)，用于控制和委托 Azure 认知搜索管理的管理任务。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/28/2021
ms.openlocfilehash: 4e79af03f4545e4af799c660314212d18dbc91ff
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2021
ms.locfileid: "110459957"
---
# <a name="authorize-access-through-azure-roles-in-azure-cognitive-search"></a>通过 Azure 认知搜索中的 Azure 角色来授权访问

对于通过门户或 Resource Manager API 管理的所有服务，Azure 提供了[基于全局角色的授权模型](../role-based-access-control/role-assignments-portal.md)。 授权模型提供所有者、参与者和读者角色，可确定分配给每个角色的 Active Directory 用户、组和安全主体的服务管理级别。 认知搜索使用这三个角色来授权访问搜索服务管理。

认知搜索不支持：

+ [自定义角色](../role-based-access-control/custom-roles.md)。
+ 对内容相关的操作（例如创建或查询索引）或服务上的任何其他对象进行基于角色的访问控制 (Azure RBAC)。

  授权执行内容操作需要[管理 API 密钥或查询 API 密钥](search-security-api-keys.md)。

> [!Note]
> 如果要对搜索结果进行基于标识的访问（有时称为行级安全性），可创建安全筛选器按标识来剪裁结果，并删除请求者不应具有访问权限的文档。 有关详细信息，请参阅[安全筛选器](search-security-trimming-for-azure-search.md)。

## <a name="roles-used-in-cognitive-search"></a>认知搜索中使用的角色

对于 Azure 认知搜索，角色与支持以下管理任务的权限级别相关联：

| 角色 | 任务 |
| --- | --- |
| 所有者 |创建或删除服务。 创建、更新或删除服务上的任何对象：API 密钥、索引、同义词映射、索引器、索引器数据源和技能组。 </br></br>完全访问在门户中或者通过管理 REST API、Azure PowerShell 或Azure CLI 公开的所有服务信息。 </br></br>分配角色成员身份。</br></br>订阅管理员和服务所有者拥有所有者角色的自动成员身份。 |
| 参与者 | 访问级别与所有者相同，但没有角色分配权限。 [搜索服务参与者](../role-based-access-control/built-in-roles.md#search-service-contributor)等效于通用的“参与者”内置角色。 |
| 读取器 | 对部分服务信息进行受限访问。 在门户中，读取者角色可以访问服务“概述”页、“概要”部分和“监视”选项卡中的信息。在所有其他选项卡和页面中的访问权限受限制。 </br></br>在“概要”部分下：资源组、状态、位置、订阅名称和 ID、标记、URL、定价层、副本、分区和搜索单位。 </br></br>在“监视”选项卡上查看服务指标：搜索延迟、受限制请求百分比、每秒平均查询数。 </br></br>无权访问“使用情况”选项卡（存储、在服务上创建的索引或索引器计数），无权访问“索引”、“索引器”、“数据源”、“技能组”或“调试会话”选项卡中的信息。 |

角色不授予对服务终结点的访问权限。 搜索服务操作（例如索引管理、索引填充和搜索数据的查询）可通过 API 密钥而非角色进行控制。 有关详细信息，请参阅[管理 API 密钥](search-security-api-keys.md)。

## <a name="tasks-and-permission-requirements"></a>任务和权限要求

下表汇总了 Azure 认知搜索中允许的操作，以及哪个角色或密钥可以解锁特定操作的访问。

+ Azure RBAC 成员身份授予对门户页和服务管理任务（创建、删除或更改服务或其 API 密钥）的访问权限。

+ API 密钥是在服务存在后创建的，应用于服务上的内容操作。

此外，对于门户中与内容相关的操作（例如创建或删除对象），支持通过显式角色成员身份（所有者或参与者）以及在门户内部使用的管理密钥完全访问所有操作和信息。 换句话说，如果你在门户中创建或加载索引，则 RBAC 成员身份使你可以访问页面，但门户本身使用管理密钥对服务中的操作进行身份验证。

| 操作 | 控制者 |
|-----------|-------------------------|
| 创建或删除服务 | Azure RBAC 权限：所有者或参与者 |
| 配置网络安全性（IP 防火墙） | Azure RBAC 权限：所有者或参与者 |
| 创建或删除专用终结点 | Azure RBAC 权限：所有者或参与者 |
| 实现客户管理的密钥 | Azure RBAC 权限：所有者或参与者 |
| 调整副本或分区 | Azure RBAC 权限：所有者或参与者|
| 管理管理员或查询密钥 | Azure RBAC 权限：所有者或参与者|
| 在门户或管理 API 中查看服务信息 | Azure RBAC 权限：所有者、参与者或读者  |
| 在门户或管理 API 中查看对象信息和指标 | Azure RBAC 权限：所有者或参与者 |
| 创建、修改、删除服务中的对象： <br>索引和组件部分（包括分析器定义、评分配置文件、CORS 选项）、索引器、数据源、技能组、同义词、建议器 | 管理密钥（如果使用 API），以及 Azure RBAC 所有者或参与者（如果使用门户） |
| 查询索引 | 管理或查询密钥（如果使用 API），以及 Azure RBAC 所有者或参与者（如果使用门户） |
| 查询有关对象的系统信息，例如返回统计信息、计数和对象列表 | 管理密钥（如果使用 API），以及 Azure RBAC 所有者或参与者（如果使用门户） |

## <a name="next-steps"></a>后续步骤

+ [使用 PowerShell 管理](search-manage-powershell.md) 
+ [Azure 认知搜索中的性能和优化](search-performance-optimization.md)
+ [什么是 Azure 基于角色的访问控制 (Azure RBAC)](../role-based-access-control/overview.md)。
