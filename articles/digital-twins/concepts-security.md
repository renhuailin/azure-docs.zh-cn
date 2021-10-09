---
title: Azure 数字孪生解决方案的安全性
titleSuffix: Azure Digital Twins
description: 了解 Azure 数字孪生的最佳安全做法。
author: baanders
ms.author: baanders
ms.date: 6/1/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 253d763cffc8b70d134182c13678320f4ac50f4c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128586317"
---
# <a name="secure-azure-digital-twins"></a>Azure 数字孪生安全性

为确保安全，Azure 数字孪生支持对部署中的特定数据、资源和操作进行精确的访问控制。 它通过称为“Azure 基于角色的访问控制 (Azure RBAC)”的粒度角色和权限管理策略来实现此功能。

Azure 数字孪生还支持静态数据加密。

## <a name="roles-and-permissions-with-azure-rbac"></a>Azure RBAC 中的角色和权限

Azure RBAC 通过与 [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) 集成提供给 Azure 数字孪生。

可使用 Azure RBAC 授予对安全主体的权限，该主体可以是用户、组或应用程序服务主体。 安全主体经 Azure AD 进行身份验证后会接收到返回的 OAuth 2.0 令牌。 此令牌可用于对 Azure 数字孪生实例的访问请求进行授权。

### <a name="authentication-and-authorization"></a>身份验证和授权

使用 Azure AD 访问的过程分为两个步骤。 当某个安全主体（用户、组或应用程序）尝试访问 Azure 数字孪生时，请求必须经过身份验证并获得授权。  

1. 首先，验证安全主体的身份并返回 OAuth 2.0 令牌。
2. 接下来，将该令牌作为请求的一部分传递给 Azure 数字孪生服务，用于授权访问指定的资源。

身份验证步骤要求应用程序请求在运行时包含 OAuth 2.0 访问令牌。 如果应用程序在 Azure 实体（例如 [Azure Functions](../azure-functions/functions-overview.md) 应用）中运行，则它可以使用托管标识来访问资源。 阅读下一部分中有关托管标识的详细信息。

授权步骤要求将一个 Azure 角色分配给安全主体。 分配给安全主体的角色确定了该主体拥有的权限。 Azure 数字孪生提供 Azure 角色，其中包含对 Azure 数字孪生资源的权限集。 这些角色在本文后面介绍。

若要了解有关 Azure 中支持的角色和角色分配的详细信息，请参阅 Azure RBAC 文档中的了解不同角色 。

#### <a name="authentication-with-managed-identities"></a>使用托管标识进行身份验证

[Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/overview.md)是一项跨 Azure 功能，可便于用户创建与其中运行应用程序代码的部署关联的安全标识。 然后可以将该标识与访问控制角色进行关联来授予自定义权限，以便访问应用程序所需的特定 Azure 资源。

借助托管标识，Azure 平台可管理此运行时标识。 对于标识本身和需要访问的资源，都不需要在应用程序代码或配置中存储和保护访问密钥。 在 Azure 应用服务应用程序内运行的 Azure 数字孪生客户端应用不需要处理 SAS 规则和密钥，也不需要处理任何其他访问令牌。 客户端应用只需要 Azure 数字孪生命名空间的终结点地址。 当应用连接时，Azure 数字孪生会将托管实体的上下文绑定到客户端。 与托管标识关联后，Azure 数字孪生客户端便能执行所有授权操作。 然后，通过将托管实体与 Azure 数字孪生 Azure 角色关联进行授权（如下所述）。

#### <a name="authorization-azure-roles-for-azure-digital-twins"></a>授权：Azure 数字孪生的 Azure 角色

Azure 提供了两个 Azure 内置角色，用于授权对 Azure 数字孪生[数据平面 API](concepts-apis-sdks.md#overview-data-plane-apis) 的访问。 可以按名称或 ID 引用角色：

| 内置角色 | 说明 | ID | 
| --- | --- | --- |
| Azure 数字孪生数据所有者 | 提供对 Azure 数字孪生资源的完全访问权限 | bcd981a7-7f74-457b-83e1-cceb9e632ffe |
| Azure 数字孪生数据读者 | 提供对 Azure 数字孪生资源的只读访问权限 | d57506d4-4c8d-48b1-8587-93c323f6a5a3 |

可以采用两种方式分配角色：
* 通过 Azure 门户 Azure 数字孪生的访问控制 (IAM) 窗格（请参阅使用 Azure 门户分配 Azure 角色）
* 通过 CLI 命令添加或删除角色

有关如何执行此操作的更多详细步骤，请参阅 Azure 数字孪生[连接端到端解决方案](tutorial-end-to-end.md)进行试用。

有关如何定义内置角色的详细信息，请参阅 Azure RBAC 文档中的了解角色定义。 若要了解如何创建 Azure 自定义角色，请参阅 [Azure 自定义角色](../role-based-access-control/custom-roles.md)。

##### <a name="automating-roles"></a>自动化角色

在自动化方案中引用角色时，建议按 ID 而非名称来引用它们。 这些名称在各个版本之中可能会发生更改，但 ID 不会，因此在自动化中的引用会更加稳定。

> [!TIP]
> 如果使用 cmdlet（如 `New-AzRoleAssignment` ([reference](/powershell/module/az.resources/new-azroleassignment))）分配角色，则可以使用 `-RoleDefinitionId` 参数传递 ID，而非 `-RoleDefinitionName` 传递角色名。

### <a name="permission-scopes"></a>权限范围

向安全主体分配 Azure 角色之前，请确定安全主体应具有的访问权限的范围。 最佳做法规定，最好只授予最小的可能范围。

以下列表描述可以限定 Azure 数字孪生资源访问权限范围的等级。
* 模型：对此资源的操作决定了对上传于 Azure 数字孪生的[模型](concepts-models.md)的控制。
* 查询数字孪生图：对此资源的操作决定了在 Azure 数字孪生图内对数字孪生体运行[查询操作](concepts-query-language.md)的功能。
* 数字孪生体：对此资源的操作控制了在孪生图中对[数字孪生体](concepts-twins-graph.md)可执行的 CRUD 操作。
* 数字孪生体关系：对此资源的操作控制了在孪生图中对数字孪生体间[关系](concepts-twins-graph.md)可执行的 CRUD 操作。
* 事件路由：对此资源的操作确定了从 Azure 数字孪生到终结点服务（如[事件中心](../event-hubs/event-hubs-about.md)、[事件网格](../event-grid/overview.md)或[服务总线](../service-bus-messaging/service-bus-messaging-overview.md)）执行[路由事件](concepts-route-events.md)的权限。

### <a name="troubleshooting-permissions"></a>权限故障排除

如果用户尝试执行其角色不允许的操作，则可能会收到服务请求错误，显示为 `403 (Forbidden)`。 有关详细信息和故障排除步骤，请参阅故障排除：Azure 数字孪生请求失败，状态为：403 (禁用)。

## <a name="managed-identity-for-accessing-other-resources"></a>用于访问其他资源的托管标识

为 Azure 数字孪生实例设置 [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) 托管标识，便于实例轻松访问其他受 Azure AD 保护的资源，例如 [Azure 密钥保管库](../key-vault/general/overview.md)。 该标识由 Azure 平台托管，无需预配或转交任何机密。 有关 Azure AD 托管标识的详细信息，请参阅 Azure 资源的托管标识。 

Azure 支持两种类型的托管标识：系统分配和用户分配的托管标识。 目前，Azure 数字孪生仅支持系统分配的标识。 

可以将系统分配的托管标识用于 Azure 数字实例，以便对[自定义终结点](concepts-route-events.md#create-an-endpoint)进行身份验证。 针对[事件中心](../event-hubs/event-hubs-about.md)和 [服务总线](../service-bus-messaging/service-bus-messaging-overview.md) 目标的终结点和[死信事件](concepts-route-events.md#dead-letter-events)的 [Azure 存储容器](../storage/blobs/storage-blobs-introduction.md) 终结点，Azure 数字孪生支持基于系统分配标识的身份验证。 托管标识目前不支持用于[事件网格](../event-grid/overview.md) 终结点。

有关如何为 Azure 数字孪生启用系统托管的标识并将其用于路由事件的说明，请参阅[通过托管标识路由事件](how-to-route-with-managed-identity.md)。

## <a name="private-network-access-with-azure-private-link-preview"></a>使用 Azure 专用链接进行专用网络访问（预览）

使用 [Azure 专用链接](../private-link/private-link-overview.md)服务，可以通过 [Azure 虚拟网络 (VNet)](../virtual-network/virtual-networks-overview.md) 中的专用终结点访问 Azure 资源（例如 [Azure 事件中心](../event-hubs/event-hubs-about.md)、[Azure 存储](../storage/common/storage-introduction.md)和 [Azure Cosmos DB](../cosmos-db/introduction.md)）以及 Azure 托管的客户服务和合作伙伴服务。 

同样，可以将专用终结点用于 Azure 数字孪生实例，允许位于虚拟网络中的客户端通过专用链接安全地访问实例。 

专用终结点使用 Azure VNet 地址空间的 IP 地址。 专用网络上的客户端与 Azure 数字孪生之间的网络流量将通过 VNet 以及 Microsoft 主干网络上的专用链接，因此不会在公共 Internet 公开。 下方直观地展示了此系统：

:::image type="content" source="media/concepts-security/private-link.png" alt-text="网络示意图，该网络是不具有公有云访问权限的受保护 VNET，通过专用链接连接到 Azure 数字孪生实例。":::

通过为 Azure 数字孪生实例配置专用终结点，可以保护 Azure 数字孪生实例，消除曝光危险，以及避免 VNet 数据外泄。

有关如何为 Azure 数字孪生设置专用链接的说明，请参阅[使用专用链接启用专用访问](./how-to-enable-private-link.md)。

### <a name="design-considerations"></a>设计注意事项 

为 Azure 数字孪生使用专用链接时，需要考虑以下几个因素：
* **定价**：有关定价详细信息，请参阅  [Azure 专用链接定价](https://azure.microsoft.com/pricing/details/private-link)。 
* **区域可用性**：对于 Azure 数字孪生，支持 Azure 数字孪生的所有 Azure 区域均支持此功能。 
* **每个 Azure 数字孪生实例的最大专用终结点数**：10 个

有关专用链接限制的信息，请参阅  [Azure 专用链接文档：限制](../private-link/private-link-service-overview.md#limitations)。

## <a name="service-tags"></a>服务标记

服务标记表示给定 Azure 服务的一组 IP 地址前缀。 Microsoft 会管理服务标记包含的地址前缀，并在地址更改时自动更新服务标记，从而尽量减少频繁更新网络安全规则所需的复杂操作。 有关服务标记的详细信息，请参阅虚拟网络标记。 

可以使用服务标记来定义对 [网络安全组](../virtual-network/network-security-groups-overview.md#security-rules) 或  [Azure 防火墙](../firewall/service-tags.md)的网络访问控制，方法是在创建安全规则时使用服务标记替换特定的 IP 地址。 通过在规则的相应“源”或“目标”字段中指定服务标记名称（在本方案中为 AzureDigitalTwins），可允许或拒绝相应服务的流量。 ****  **   **   

下面是 AzureDigitalTwins 服务标记的详细信息。

| 标记 | 目的 | 可以使用入站还是出站连接？ | 可以支持区域范围？ | 是否可与 Azure 防火墙一起使用？ |
| --- | --- | --- | --- | --- |
| AzureDigitalTwins | Azure 数字孪生<br>注意：此标记或此标记所涵盖的 IP 地址可用于限制对[事件路由](concepts-route-events.md)配置的终结点的访问。 | 入站 | 否 | 是 |

### <a name="using-service-tags-for-accessing-event-route-endpoints"></a>使用服务标记访问事件路由终结点 

下面是在 Azure 数字孪生中使用服务标记访问[事件路由](concepts-route-events.md)终结点的步骤。

1. 首先，下载显示有 Azure IP 范围和服务标记的 JSON 文件引用：AZURE IP 范围和服务标记。 

2. 在 JSON 文件中查找 "AzureDigitalTwins" IP 范围。  

3. 请参阅有关连接到终结点的外部资源的文档（例如，[事件网格](../event-grid/overview.md)、[事件中心](../event-hubs/event-hubs-about.md)、[服务总线](../service-bus-messaging/service-bus-messaging-overview.md)或[死信事件](concepts-route-events.md#dead-letter-events)的 [Azure 存储](../storage/blobs/storage-blobs-overview.md)），查看如何为资源设置 IP 筛选器。

4. 使用步骤 2中的 IP 范围为外部资源设置 IP 筛选器。  

5. 根据需要定期更新 IP 范围。 此范围可能会随时间而变化，因此最好定期检查并在需要时刷新它们。 更新频率会有所不同，但最好每周检查一次。

## <a name="encryption-of-data-at-rest"></a>静态数据加密

Azure 数字孪生对写入数据中心的静态数据和传输中的数据进行加密，并在你访问数据时解密。 使用 Microsoft 管理的加密密钥进行加密。

## <a name="cross-origin-resource-sharing-cors"></a>跨源资源共享 (CORS)

Azure 数字孪生目前不支持跨域资源共享 (CORS)。 因此，如果从浏览器应用、[API 管理 (APIM)](../api-management/api-management-key-concepts.md) 接口或 [Power Apps](/powerapps/powerapps-overview) 连接器中调用 REST API，则可能出现策略错误。

若要解决此错误，可以执行下列某种操作：
* 去除消息中的 CORS 标头 `Access-Control-Allow-Origin`。 此标头指示是否可以共享响应。 
* 或者，创建 CORS 代理，并通过它请求 Azure 数字孪生 REST API。 

## <a name="next-steps"></a>后续步骤

* 请参阅[设置实例和身份验证](how-to-set-up-instance-portal.md)，了解这些概念的实际应用。

* 请参阅[编写应用验证码](how-to-authenticate-client.md)，了解如何从客户端应用程序代码着手与这些概念交互。

* 详细了解 [Azure RBAC](../role-based-access-control/overview.md)。