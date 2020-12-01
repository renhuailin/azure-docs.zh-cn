---
title: Azure 数字孪生解决方案的安全性
titleSuffix: Azure Digital Twins
description: 了解 Azure 数字孪生的最佳安全方案。
author: baanders
ms.author: baanders
ms.date: 3/18/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: d62e7566038af6647cab2992b02184a4ea5ba30b
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96344141"
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

>[!NOTE]
> 最近在预览版中将这些角色重命名为其以前的名称：
> * *Azure 数字孪生数据所有者* 以前是 *Azure 数字孪生所有者 (预览)*。
> * *Azure 数字孪生数据读取器* 是以前的 *Azure 数字孪生读卡器 (预览)*。

可以通过两种方式分配角色：
* 通过访问控制 (的 IAM) 窗格中的 Azure 数字孪生在 Azure 门户中 (参阅 [*使用 Azure 门户添加或删除 azure 角色分配*](../role-based-access-control/role-assignments-portal.md)) 
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

## <a name="service-tags"></a>服务标记

**服务标记** 表示给定 Azure 服务中的一组 IP 地址前缀。 Microsoft 会管理服务标记包含的地址前缀，并在地址更改时自动更新服务标记，从而尽量减少频繁更新网络安全规则所需的复杂操作。 有关服务标记的详细信息，请参阅  [*虚拟网络标记*](../virtual-network/service-tags-overview.md)。 

你可以使用服务标记来定义 [网络安全组](../virtual-network/network-security-groups-overview.md#security-rules)或 Azure 防火墙上的网络访问控制    [Azure Firewall](../firewall/service-tags.md)，方法是在创建安全规则时使用服务标记来替换特定的 IP 地址。 在这种情况下，通过指定服务标记名称 **AzureDigitalTwins** (，在规则的相应 *源*   或 *目标* 字段中 AzureDigitalTwins)   ，可以允许或拒绝相应服务的流量。 

下面是 **AzureDigitalTwins** 服务标记的详细信息。

| 标记 | 目的 | 可以使用入站或出站？ | 可以支持区域范围？ | 是否可与 Azure 防火墙一起使用？ |
| --- | --- | --- | --- | --- |
| AzureDigitalTwins | Azure 数字孪生<br>注意：此标记或此标记涵盖的 IP 地址可用于限制对配置为 [事件路由](concepts-route-events.md)的终结点的访问。 | 入站 | 否 | 是 |

### <a name="using-service-tags-for-accessing-event-route-endpoints"></a>使用服务标记访问事件路由终结点 

下面是通过 Azure 数字孪生使用服务标记访问 [事件路由](concepts-route-events.md) 终结点的步骤。

1. 首先，下载此 JSON 文件引用，其中显示了 Azure IP 范围和服务标记： [*AZURE Ip 范围和服务标记*](https://www.microsoft.com/download/details.aspx?id=56519)。 

2. 在 JSON 文件中查找 "AzureDigitalTwins" IP 范围。  

3. 请参阅连接到终结点的外部资源的文档 (例如，[事件网格](../event-grid/overview.md)、[事件中心](../event-hubs/event-hubs-about.md)、[服务总线](../service-bus-messaging/service-bus-messaging-overview.md)或用于[死信事件](concepts-route-events.md#dead-letter-events)的[Azure 存储](../storage/blobs/storage-blobs-overview.md)) ，查看如何为该资源设置 IP 筛选器。

4. 使用 *步骤 2* 中的 ip 范围) 对外部资源 (s 设置 ip 筛选器。  

5. 根据需要定期更新 IP 范围。 范围可能会随时间而变化，因此最好定期检查并刷新它们。 这些更新的频率可能有所不同，但最好是每周检查一次。

## <a name="encryption-of-data-at-rest"></a>静态数据加密

Azure 数字孪生提供静态数据和传输中的数据加密，因为它是在我们的数据中心编写的，在你访问数据中心时，会对其进行解密。 使用 Microsoft 托管的加密密钥进行加密。

## <a name="cross-origin-resource-sharing-cors"></a>跨源资源共享 (CORS)

Azure 数字孪生目前不支持 **(CORS) 的跨域资源共享**。 因此，如果从浏览器应用中调用 REST API， [API 管理 (APIM) ](../api-management/api-management-key-concepts.md) 接口或 [Power Apps](/powerapps/powerapps-overview) 连接器，则可能会出现策略错误。

若要解决此错误，可以执行以下操作之一：
* 从消息中去除 CORS 标头 `Access-Control-Allow-Origin` 。 此标头指示是否可以共享响应。 
* 或者，创建 CORS 代理，并通过它 REST API 请求来请求 Azure 数字孪生。 

## <a name="next-steps"></a>后续步骤

* 请参阅操作 [*方法：设置实例和身份验证*](how-to-set-up-instance-portal.md)中的操作中的这些概念。

* 请参阅如何 [*：编写应用身份验证代码*](how-to-authenticate-client.md)中的客户端应用程序代码中的这些概念的交互。

* 阅读有关 [AZURE RBAC](../role-based-access-control/overview.md)的详细信息。