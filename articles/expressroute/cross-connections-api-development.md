---
title: Azure ExpressRoute CrossConnnections API 开发和集成
description: 本文为 ExpressRoute 合作伙伴提供有关 expressRouteCrossConnections 资源类型的详细概述。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: duau
ms.openlocfilehash: b80392231dba26a10141dcd8247b092e8171894c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96011976"
---
# <a name="expressroute-crossconnnections-api-development-and-integration"></a>ExpressRoute CrossConnnections API 开发和集成

通过 ExpressRoute 合作伙伴资源管理器 API，ExpressRoute 合作伙伴可以管理客户 ExpressRoute 线路的第 2 层和第 3 层配置。 ExpressRoute 合作伙伴资源管理器 API 引入了新的资源类型 expressRouteCrossConnections。 合作伙伴使用此资源来管理客户 ExpressRoute 线路。

## <a name="workflow"></a>工作流

expressRouteCrossConnections 资源是 ExpressRoute 线路的影子资源。 当 Azure 客户创建 ExpressRoute 线路并选择特定 ExpressRoute 合作伙伴时，Microsoft 会在合作伙伴的 Azure ExpressRoute 管理订阅中创建 expressRouteCrossConnections 资源。 在此过程中，Microsoft 将定义要在其中创建 expressRouteCrossConnections 资源的资源组。 资源组的命名标准是 CrossConnection-PeeringLocation，其中 PeeringLocation = ExpressRoute 位置。 例如，如果客户在丹佛创建了 ExpressRoute 线路，则将在以下资源组的合作伙伴的 Azure 订阅中创建 CrossConnection：CrossConnnection-Denver。

ExpressRoute 合作伙伴通过针对 expressRouteCrossConnections 资源发出 REST 操作来管理第 2 层和第 3 层配置。

## <a name="benefits"></a>优点

移动到 expressRouteCrossConnections 资源的好处：

* 对于 ExpressRoute 合作伙伴，将来任何增强功能都将在 ExpressRouteCrossConnection 资源上提供。

* 合作伙伴可以将 [Azure 基于角色的访问控制 (Azure RBAC)](../role-based-access-control/overview.md) 应用到expressRouteCrossConnection 资源。 这些控制可以定义有关哪些用户帐户可以修改 expressRouteCrossConnection 资源以及添加/更新/删除对等互连配置的权限。

* expressRouteCrossConnection 资源公开了有助于对 ExpressRoute 连接进行故障排除的 API。 其中包括 ARP 表、BGP 路由表摘要和 BGP 路由表详细信息。 经典部署 API 不支持此功能。

* 合作伙伴还可使用 RouteFilter 资源在 Microsoft 对等互连上查找播发的社区。

## <a name="api-development-and-integration-steps"></a>API 开发和集成步骤

为了针对合作伙伴 API 进行开发，ExpressRoute 合作伙伴可利用测试客户和测试合作伙伴设置。 测试客户设置将用于在映射到虚拟设备和端口的测试对等互连位置中创建 ExpressRoute 线路。 测试合作伙伴设置用于管理在测试对等互连位置中创建的 ExpressRoute 线路。

### <a name="1-enlist-subscriptions"></a>1. 登记订阅

若要请求测试合作伙伴和测试客户设置，请为 ExpressRoute 工程联系人登记两个即用即付 Azure 订阅：
* **ExpressRoute_API_Dev_Provider_Sub：** 此订阅将用于管理虚拟设备和端口上测试对等互连位置中创建的 ExpressRoute 线路。

* **ExpressRoute_API_Dev_Customer_Sub：** 此订阅将用于在映射到虚拟设备和端口的测试对等互连位置中创建 ExpressRoute 线路。

测试对等互连位置：默认情况下，不向生产客户公开虚拟设备和端口。 若要创建映射到测试设置的 ExpressRoute 线路，需要启用订阅功能标志。

### <a name="2-register-the-dev_provider-subscription-to-access-the-expressroutecrossconnections-api"></a>2. 注册 Dev_Provider 订阅以访问 expressRouteCrossConnections API

为了访问 expressRouteCrossConnections API，需要在 **Microsoft.Network 资源提供程序** 中注册合作伙伴订阅。 请遵循 [Azure 资源提供程序和类型](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)一文中的步骤完成注册过程。

### <a name="3-set-up-authentication-for-azure-resource-manager-rest-api-calls"></a>3. 为 Azure 资源管理器 REST API 调用设置身份验证

在调用服务 API 之前，大多数 Azure 服务都要求客户端代码使用有效凭据通过资源管理器进行身份验证。 身份验证由 Azure AD 在各个操作者之间进行协调，并为客户端提供访问令牌作为身份验证的证明。

身份验证过程涉及两个主要步骤：

1. [注册客户端](/rest/api/azure/#register-your-client-application-with-azure-ad)。
2. [创建访问请求](/rest/api/azure/#create-the-request)。

### <a name="4-provide-network-contributor-permission-to-the-client-application"></a>4. 向客户端应用程序提供网络参与者权限

成功配置身份验证后，需要在 Dev_Provider_Sub 下向网络参与者授予对客户端应用程序的访问权限。 若要授予权限，请登录到 [Azure 门户](https://ms.portal.azure.com/#home) 并完成以下步骤：

1. 导航到“订阅”，选择“Dev_Provider_Sub”
2. 导航到“访问控制(IAM)”
3. 添加角色分配
4. 选择网络参与者角色
5. 分配对 Azure AD 用户、组或服务主体的访问权限
6. 选择你的客户端应用程序
7. 保存更改

### <a name="5-develop"></a>5. 开发

针对 [expressRouteCrossConnections API](/rest/api/expressroute/expressroutecrossconnections) 进行开发。

## <a name="rest-api"></a>REST API

有关 REST API 文档，请参阅 [ExpressRoute CrossConnections REST API](/rest/api/expressroute/expressroutecrossconnections)。

## <a name="next-steps"></a>后续步骤

有关所有 ExpressRoute REST API 的详细信息，请参阅 [ExpressRoute REST API](/rest/api/expressroute/)。