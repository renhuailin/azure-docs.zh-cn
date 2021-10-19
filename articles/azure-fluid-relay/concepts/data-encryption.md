---
title: Azure Fluid Relay 中的数据加密
description: 更好地了解 Fluid Relay Server 中的数据加密
author: hickeys
ms.author: hickeys
ms.date: 10/08/2021
ms.service: app-service
ms.topic: reference
ms.openlocfilehash: 3af5ba0580b0cb662c9ab87c73afac965a8c066f
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2021
ms.locfileid: "129730519"
---
# <a name="data-encryption-in-azure-fluid-relay"></a>Azure Fluid Relay 中的数据加密

> [!NOTE]
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。

Microsoft Azure Fluid Relay Server 利用 [Azure Kubernetes](/azure/aks/enable-host-encryption)、[Microsoft Azure Cosmos DB]/azure/cosmos-db/database-encryption-at-rest) 和 [Azure Blob 存储](/azure/storage/common/storage-service-encryption)的静态加密功能。 AFRS 与这些资源之间的服务到服务通信经过 TLS 加密，限定在 Azure 虚拟网络边界范围内，并通过网络安全规则防止其受到外部干扰。

下图概要性地显示了 Azure Fluid Relay Server 的实施方式及其处理数据存储的方式。

:::image type="content" source="../images/data-encryption.png" alt-text="Azure Fluid Relay 中数据存储的示意图":::

## <a name="frequently-asked-questions"></a>常见问题

### <a name="how-much-more-does-azure-fluid-relay-server-cost-if-encryption-is-enabled"></a>如果启用了加密，Azure Fluid Relay Server 成本会增加多少？

默认已启用静态加密。 没有任何额外费用。

### <a name="who-manages-the-encryption-keys"></a>加密密钥由谁管理？

密钥由 Microsoft 管理。

### <a name="how-often-are-encryption-keys-rotated"></a>加密密钥多久轮换一次？

Microsoft 有一套关于加密密钥轮换的内部指导原则，Azure Fluid Relay Server 也遵循这些指导原则。 具体指导方针不对外公布。 Microsoft 会对外公布[安全开发生命周期 (SDL)](https://www.microsoft.com/sdl/default.aspx)，其被视为内部指导的一部分，可为开发人员提供有用的最佳实践。

### <a name="can-i-use-my-own-encryption-keys"></a>我可以使用自己的加密密钥吗？

不可以，此功能尚不可用。 请关注有关此功能的最新信息。 

### <a name="what-regions-have-encryption-turned-on"></a>哪些区域已开启了此加密？

所有 Azure Fluid Relay Server 区域都已针对所有用户数据启用了此加密。

### <a name="does-encryption-affect-the-performance-latency-and-throughput-slas"></a>加密是否会影响性能延迟和吞吐量 SLA？

答：启用静态加密不会使性能 SLA 受到影响或发生变化。

## <a name="see-also"></a>请参阅

- [Azure Fluid Relay 体系结构概述](architecture.md)
- [Azure Fluid Relay 令牌协定](../how-tos/fluid-json-web-token.md)
- [应用中的身份验证和授权](authentication-authorization.md)
