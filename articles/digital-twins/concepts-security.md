---
title: Azure 数字孪生解决方案的安全性
titleSuffix: Azure Digital Twins
description: 了解 Azure 数字孪生的最佳安全方案。
author: baanders
ms.author: baanders
ms.date: 3/18/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: d129890cc63e5a0e4cbc6139e1de2df7a31f6f77
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2021
ms.locfileid: "101094556"
---
# <a name="secure-azure-digital-twins"></a>保护 Azure 数字孪生

为了安全起见，Azure 数字孪生支持对部署中特定数据、资源和操作的精确访问控制。 它通过称作 azure **RBAC)  (** 的粒度角色和权限管理策略来实现此功能。 可在 [此处](../role-based-access-control/overview.md)阅读 Azure RBAC 的一般原则。

Azure 数字孪生还支持静态数据加密。

## <a name="roles-and-permissions-with-azure-rbac"></a>Azure RBAC 中的角色和权限

Azure RBAC 通过与 [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) 集成提供给 Azure 数字孪生。

可以使用 Azure RBAC 向 *安全主体* 授予权限，这可能是用户、组或应用程序服务主体。 安全主体由 Azure AD 进行身份验证，并在返回时接收 OAuth 2.0 令牌。 此令牌可用于授权对 Azure 数字孪生实例的访问请求。

### <a name="authentication-and-authorization"></a>身份验证和授权

使用 Azure AD，访问过程分为两个步骤。 如果 (用户、组或应用程序的安全主体) 尝试访问 Azure 数字孪生，则必须对该请求进行 *身份验证* 和 *授权*。 

1. 首先，对安全主体的身份进行 *身份验证*，并返回 OAuth 2.0 令牌。
2. 接下来，令牌作为请求的一部分传递到 Azure 数字孪生服务，以 *授权* 访问指定的资源。

身份验证步骤要求在运行时将任何应用程序请求包含 OAuth 2.0 访问令牌。 如果应用程序在 Azure 实体（如 [Azure Functions](../azure-functions/functions-overview.md) 应用）内运行，则它可以使用 **托管标识** 来访问资源。 在下一部分中了解有关托管标识的详细信息。

授权步骤要求向安全主体分配 Azure 角色。 分配给安全主体的角色确定了该主体拥有的权限。 Azure 数字孪生提供 azure 角色，其中包含 Azure 数字孪生资源的权限集。 本文稍后将介绍这些角色。

若要了解有关 Azure 中支持的角色和角色分配的详细信息，请参阅了解 Azure RBAC 文档中 [*的不同角色*](../role-based-access-control/rbac-and-directory-admin-roles.md) 。

#### <a name="authentication-with-managed-identities"></a>使用托管标识进行身份验证

[Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/overview.md) 是一项跨 Azure 功能，可用于创建与应用程序代码运行的部署关联的安全标识。 然后，你可以将该标识与访问控制角色相关联，以授予用于访问应用程序所需的特定 Azure 资源的自定义权限。

借助托管标识，Azure 平台可管理此运行时标识。 对于标识本身和需要访问的资源，都不需要在应用程序代码或配置中存储和保护访问密钥。 在 Azure App Service 应用程序中运行的 Azure 数字孪生客户端应用程序不需要处理 SAS 规则和密钥，也不需要处理任何其他访问令牌。 客户端应用只需要 Azure 数字孪生命名空间的终结点地址。 当应用进行连接时，Azure 数字孪生会将托管实体的上下文绑定到客户端。 一旦将其与托管标识相关联，Azure 数字孪生客户端就可以执行所有授权的操作。 然后，将通过将托管实体与 Azure 数字孪生 Azure 角色关联来授予授权 (如下) 所述。

#### <a name="authorization-azure-roles-for-azure-digital-twins"></a>授权： azure 数字孪生的 Azure 角色

Azure 提供了 **两个 azure 内置角色** ，用于授权对 Azure 数字孪生 [数据平面 api](how-to-use-apis-sdks.md#overview-data-plane-apis)的访问。 可以按名称或 ID 引用角色：

| 内置角色 | 说明 | ID | 
| --- | --- | --- |
| Azure 数字孪生数据所有者 | 通过 Azure 数字孪生资源提供完全访问权限 | bcd981a7-7f74-457b-83e1-cceb9e632ffe |
| Azure 数字孪生数据读者 | 提供对 Azure 数字孪生资源的只读访问权限 | d57506d4-4c8d-48b1-8587-93c323f6a5a3 |

可以通过两种方式分配角色：
* 通过访问控制 (IAM) 窗格中的 Azure 数字孪生在 Azure 门户中 (参阅 [*使用 Azure 门户分配 azure 角色*](../role-based-access-control/role-assignments-portal.md)) 
* 通过 CLI 命令添加或删除角色

有关如何执行此操作的更多详细步骤，请在 Azure 数字孪生教程中试用 [*：连接端到端解决方案*](tutorial-end-to-end.md)。

有关如何定义内置角色的详细信息，请参阅了解 Azure RBAC 文档中的 [*角色定义*](../role-based-access-control/role-definitions.md) 。 有关创建 Azure 自定义角色的详细信息，请参阅 [*azure 自定义角色*](../role-based-access-control/custom-roles.md)。

##### <a name="automating-roles"></a>自动化角色

在自动方案中引用角色时，建议使用其 **id** 而不是名称来引用它们。 这些名称在两个版本之间可能会发生更改，但 Id 将不会更改，使其在自动化中更加稳定。

> [!TIP]
> 如果使用 cmdlet （如 `New-AzRoleAssignment` ([引用](/powershell/module/az.resources/new-azroleassignment)) ） Assiging 角色，则可以使用 `-RoleDefinitionId` 参数而不是 `-RoleDefinitionName` 来传递 ID，而不是传递角色的名称。

### <a name="permission-scopes"></a>权限范围

向安全主体分配 Azure 角色之前，请确定安全主体应具有的访问权限的范围。 最佳做法规定最好只授予最小的可能范围。

以下列表描述了可对 Azure 数字孪生资源的访问进行范围的级别。
* 模型：对此资源的操作规定对 Azure 数字孪生中上载的 [模型](concepts-models.md) 的控制。
* Query 数字孪生 Graph：此资源的操作决定了在 Azure 数字孪生 Graph 内对数字孪生运行 [查询操作](concepts-query-language.md) 的能力。
* 数字超源：此资源的操作可在 "超过程" 图中对 [数字孪生](concepts-twins-graph.md) 上的 CRUD 操作提供控制。
* 数字超并行关系：此资源的操作定义对孪生中数字之间的 [关系](concepts-twins-graph.md) 的 CRUD 操作的控制。
* 事件路由：此资源的操作确定将 [事件](concepts-route-events.md) 从 Azure 数字孪生路由到终结点服务（如 [事件中心](../event-hubs/event-hubs-about.md)、 [事件网格](../event-grid/overview.md)或 [服务总线](../service-bus-messaging/service-bus-messaging-overview.md)）的权限。

### <a name="troubleshooting-permissions"></a>疑难解答权限

如果用户尝试执行其角色不允许的操作，则可能会收到来自服务请求读取的错误 `403 (Forbidden)` 。 有关详细信息和疑难解答步骤，请参阅 [*故障排除： Azure 数字孪生请求失败，状态为： 403 (禁止访问)*](troubleshoot-error-403.md)。

## <a name="managed-identity-for-accessing-other-resources-preview"></a>用于访问其他资源 (预览的托管标识) 

将 [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) Azure 数字孪生实例的 **托管标识** ，可以允许实例轻松访问其他受 Azure AD 保护的资源，例如 [Azure Key Vault](../key-vault/general/overview.md)。 该标识由 Azure 平台管理，并且不要求你预配或轮换任何机密。 有关 Azure AD 中的托管标识的详细信息，请参阅  [*Azure 资源的托管标识*](../active-directory/managed-identities-azure-resources/overview.md)。 

Azure 支持两种类型的托管标识：系统分配和用户分配。 目前，Azure 数字孪生仅支持 **系统分配的标识**。 

你可以对 Azure 数字实例使用系统分配的托管标识，对 [自定义终结点](concepts-route-events.md#create-an-endpoint)进行身份验证。 Azure 数字孪生针对[事件中心](../event-hubs/event-hubs-about.md)和 [服务总线](../service-bus-messaging/service-bus-messaging-overview.md)目标的终结点   和用于死信事件的[azure 存储容器](../storage/blobs/storage-blobs-introduction.md)   终结点[](concepts-route-events.md#dead-letter-events)，支持系统分配的基于身份的身份验证。 [事件网格](../event-grid/overview.md)  托管标识当前不支持终结点。

有关如何为 Azure 数字孪生启用系统管理的标识并使用它来路由事件的说明，请参阅 [*如何：启用托管标识以便路由事件 (预览)*](how-to-enable-managed-identities.md)。

## <a name="private-network-access-with-azure-private-link-preview"></a>使用 Azure Private Link (预览) 进行专用网络访问

[Azure Private Link](../private-link/private-link-overview.md)是一项服务，可用于通过[azure 虚拟网络 (VNet) ](../virtual-network/virtual-networks-overview.md)中的专用终结点访问 azure 资源， (例如[Azure 事件中心](../event-hubs/event-hubs-about.md)、 [azure 存储](../storage/common/storage-introduction.md)和[Azure Cosmos DB](../cosmos-db/introduction.md)) 和 azure 托管的客户和合作伙伴服务。 

同样，可以将专用终结点用于 Azure 数字克隆实例，以允许位于虚拟网络中的客户端通过专用链接安全地访问实例。 

专用终结点使用 Azure VNet 地址空间中的 IP 地址。 专用网络上的客户端与 Azure 数字孪生实例之间的网络流量通过 VNet 和 Microsoft 主干网络上的专用链接进行遍历，从而消除了对公共 internet 的公开。 下面是此系统的直观表示形式：

:::image type="content" source="media/concepts-security/private-link.png" alt-text="显示 PowerGrid 公司网络的关系图，该公司是不带 internet/公有云访问权限的受保护 VNET，通过专用链接连接到名为 CityOfTwins 的 Azure 数字孪生实例。":::

为 Azure 数字孪生实例配置专用终结点可以保护 Azure 数字孪生实例，消除公开泄露，并避免从 VNet 渗透数据。

有关如何为 Azure 数字孪生设置专用链接的说明，请参阅 how [*to： Enable private access With Private link (preview)*](how-to-enable-private-link.md)。

### <a name="design-considerations"></a>设计注意事项 

使用 Azure 数字孪生的专用链接时，需要考虑以下几个因素：
* **定价**：有关定价的详细信息，请参阅  [Azure 专用链接定价](https://azure.microsoft.com/pricing/details/private-link)。 
* **区域可用性**：对于 Azure 数字孪生，此功能在 Azure 数字孪生可用的所有 azure 区域中提供。 
* **每个 Azure 数字孪生实例的最大专用终结** 点数：10

有关 "专用链接限制" 的信息，请参阅 [Azure 专用链接文档：限制](../private-link/private-link-service-overview.md#limitations)。

## <a name="service-tags"></a>服务标记

**服务标记** 表示给定 Azure 服务中的一组 IP 地址前缀。 Microsoft 会管理服务标记包含的地址前缀，并在地址更改时自动更新服务标记，从而尽量减少频繁更新网络安全规则所需的复杂操作。 有关服务标记的详细信息，请参阅  [*虚拟网络标记*](../virtual-network/service-tags-overview.md)。 

你可以使用服务标记来定义 [网络安全组](../virtual-network/network-security-groups-overview.md#security-rules)或 Azure 防火墙上的网络访问控制    [](../firewall/service-tags.md)，方法是在创建安全规则时使用服务标记来替换特定的 IP 地址。 在这种情况下，通过指定服务标记名称 **** (，在规则的相应 *源*   或 *目标* 字段中 AzureDigitalTwins)   ，可以允许或拒绝相应服务的流量。 

下面是 **AzureDigitalTwins** 服务标记的详细信息。

| 标记 | 目的 | 可以使用入站还是出站连接？ | 可以支持区域范围？ | 是否可与 Azure 防火墙一起使用？ |
| --- | --- | --- | --- | --- |
| AzureDigitalTwins | Azure 数字孪生<br>注意：此标记或此标记涵盖的 IP 地址可用于限制对配置为 [事件路由](concepts-route-events.md)的终结点的访问。 | 入站 | 否 | 是 |

### <a name="using-service-tags-for-accessing-event-route-endpoints"></a>使用服务标记访问事件路由终结点 

下面是通过 Azure 数字孪生使用服务标记访问 [事件路由](concepts-route-events.md) 终结点的步骤。

1. 首先，下载此 JSON 文件引用，其中显示了 Azure IP 范围和服务标记： [*AZURE Ip 范围和服务标记*](https://www.microsoft.com/download/details.aspx?id=56519)。 

2. 在 JSON 文件中查找 "AzureDigitalTwins" IP 范围。  

3. 请参阅连接到终结点的外部资源的文档 (例如，[事件网格](../event-grid/overview.md)、[事件中心](../event-hubs/event-hubs-about.md)、[服务总线](../service-bus-messaging/service-bus-messaging-overview.md)或用于[死信事件](concepts-route-events.md#dead-letter-events)的[Azure 存储](../storage/blobs/storage-blobs-overview.md)) ，查看如何为该资源设置 IP 筛选器。

4. 使用 *步骤 2* 中的 ip 范围) 对外部资源 (s 设置 ip 筛选器。  

5. 根据需要定期更新 IP 范围。 范围可能会随时间而变化，因此最好定期检查它们并在需要时对其进行刷新。 这些更新的频率可能有所不同，但最好是每周检查一次。

## <a name="encryption-of-data-at-rest"></a>静态数据加密

Azure 数字孪生提供静态数据和传输中的数据加密，因为它是在我们的数据中心编写的，在你访问数据中心时，会对其进行解密。 使用 Microsoft 托管的加密密钥进行加密。

## <a name="cross-origin-resource-sharing-cors"></a>跨源资源共享 (CORS)

Azure 数字孪生目前不支持 **(CORS) 的跨域资源共享**。 因此，如果从浏览器应用中调用 REST API， [API 管理 (APIM) ](../api-management/api-management-key-concepts.md) 接口或 [Power Apps](/powerapps/powerapps-overview) 连接器，则可能会出现策略错误。

若要解决此错误，可以执行下列操作之一：
* 从消息中去除 CORS 标头 `Access-Control-Allow-Origin` 。 此标头指示是否可以共享响应。 
* 或者，创建 CORS 代理，并通过它 REST API 请求来请求 Azure 数字孪生。 

## <a name="next-steps"></a>后续步骤

* 请参阅操作 [*方法：设置实例和身份验证*](how-to-set-up-instance-portal.md)中的操作中的这些概念。

* 请参阅如何 [*：编写应用身份验证代码*](how-to-authenticate-client.md)中的客户端应用程序代码中的这些概念的交互。

* 阅读有关 [AZURE RBAC](../role-based-access-control/overview.md)的详细信息。