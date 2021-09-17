---
title: 为专用终结点配置 DNS 名称解析
description: 本文简要概述如何对 Purview 帐户使用专用终点
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 08/18/2021
ms.openlocfilehash: b6d0b4cb58562540cd3b2024631bb9cec2d320c9
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2021
ms.locfileid: "122607045"
---
# <a name="configure-and-verify-dns-name-resolution-for-azure-purview-private-endpoints"></a>为 Azure Purview 专用终结点配置和验证 DNS 名称解析

## <a name="conceptual-overview"></a>概念概述
为 Azure Purview 帐户设置专用终结点时，准确的名称解析是一项关键要求。 

你可能需要在 DNS 设置中启用内部名称解析，以将专用终结点 IP 地址解析为数据源中的完全限定域名 (FQDN)，将管理计算机解析为 Azure Purview 帐户和自承载集成运行时，具体取决于正在部署的方案。

以下示例展示了来自虚拟网络外部或未配置 Azure 专用终结点时的 Azure Purview DNS 名称解析。

   :::image type="content" source="media/catalog-private-link/purview-name-resolution-external.png" alt-text="显示来自 CorpNet 外部的 Azure Purview 名称解析的屏幕截图。":::

以下示例展示了来自虚拟网络内部的 Azure Purview DNS 名称解析。

   :::image type="content" source="media/catalog-private-link/purview-name-resolution-private-link.png" alt-text="显示来自 CorpNet 内部的 Purview 名称解析的屏幕截图。":::

## <a name="deployment-options"></a>部署选项 

对 Azure Purview 帐户使用专用终结点时，可使用以下任一选项发送内部名称解析：

-  在专用终结点部署的 Azure 环境部分中[部署新的 Azure 专用 DNS 区域](#option-1---deploy-new-azure-private-dns-zones)。 （默认选项）
- [使用现有的 Azure 专用 DNS 区域](#option-2---use-existing-azure-private-dns-zones)。 如果在来自不同订阅甚至同一订阅的中心辐射模型中使用专用终结点，请使用此选项。 
- 如果不使用 DNS 转发器，而是直接在本地 DNS 服务器中管理 A 记录，则[使用自己的 DNS 服务器](#option-3---use-your-own-dns-servers)。

## <a name="option-1---deploy-new-azure-private-dns-zones"></a>选项 1 - 部署新的 Azure 专用 DNS 区域  

要启用内部名称解析，可以在部署了 Azure Purview 帐户的 Azure 订阅中部署所需的 Azure DNS 区域。 

创建门户和帐户专用终结点时，Azure Purview 的 DNS CNAME 资源记录会自动更新为前缀为 `privatelink` 的子域中的子域名。 默认情况下，我们还会创建一个与 Azure Purview 的 `privatelink` 子域对应的[专用 DNS 区域](../dns/private-dns-overview.md)作为 privatelink.purview.azure.com，包括专用终结点的 DNS A 资源记录。 如果启用引入专用终结点，则托管资源需要额外的 DNS 区域。 

下表显示了 Azure 专用 DNS 区域和 DNS A 记录的示例，如果在部署期间启用 _专用 DNS 集成_，它们将作为 Azure Purview 帐户的专用终结点配置的一部分进行部署： 

专用终结点  |专用终结点关联目标  |DNS 区域（新）  |A 记录（示例） |
|---------|---------|---------|---------|
|帐户     |Azure Purview         |`privatelink.purview.azure.com`         |PurviewA         |
|门户     |Azure Purview 帐户          |`privatelink.purview.azure.com`        |Web         |
|引入     |Purview 托管存储帐户 - Blob          |`privatelink.blob.core.windows.net`          |scaneastusabcd1234         |
|引入   |Purview 托管存储帐户 - 队列         |`privatelink.queue.core.windows.net`         |scaneastusabcd1234         |
|引入     |Purview 托管存储帐户 - 事件中心         |`privatelink.servicebus.windows.net`         |atlas-12345678-1234-1234-abcd-123456789abc         |

使用专用终结点从虚拟网络外部解析 Azure Purview 终结点 URL 时，它会解析为 Azure Purview 的公共终结点。 从托管专用终结点的虚拟网络进行解析时，Azure Purview 终结点 URL 解析为专用终结点的 IP 地址。

例如，如果 Azure Purview 帐户名称为“PurviewA”，则从托管专用终结点的虚拟网络外部解析时，将为：

| 名称 | 类型 | Value |
| ---------- | -------- | --------------- |
| `PurviewA.purview.azure.com` | CNAME | `PurviewA.privatelink.purview.azure.com` |
| `PurviewA.privatelink.purview.azure.com` | CNAME | \<Purview public endpoint\> |
| \<Purview public endpoint\> | A | \<Purview public IP address\> |
| `Web.purview.azure.com` | CNAME | \<Purview public endpoint\> |

当 PurviewA 的 DNS 资源记录由托管专用终结点的虚拟网络解析时，将为：

| 名称 | 类型 | Value |
| ---------- | -------- | --------------- |
| `PurviewA.purview.azure.com` | CNAME | `PurviewA.privatelink.purview.azure.com` |
| `PurviewA.privatelink.purview.azure.com` | A | \<private endpoint IP address\> |
| `Web.purview.azure.com` | CNAME | \<private endpoint IP address\> |

## <a name="option-2---use-existing-azure-private-dns-zones"></a>选项 2 - 使用现有的 Azure 专用 DNS 区域
在部署 Azure Purview 专用终结点期间，可以选择采用了现有 Azure 专用 DNS 区域的 _专用 DNS 集成_。 对于将专用终结点用于 Azure 中的其他服务的组织来说，这是一种常见情况。 在这种情况下，在部署专用端点期间，请务必选择现有 DNS 区域，而不是创建新区域。 

如果你的组织对所有 Azure 专用 DNS 区域使用中央或中心订阅，上述操作也适用。

以下列表显示了 Purview 专用终结点所需的 Azure DNS 区域和 A 记录：

> [!NOTE]
> 使用 `PurviewA`、`scaneastusabcd1234` 和 `atlas-12345678-1234-1234-abcd-123456789abc` 更新环境中包含对应 Azure 资源名称的所有名称。 例如，使用 Azure Purview 托管存储帐户的名称，而非 `scaneastusabcd1234`。

专用终结点  |专用终结点关联目标  |DNS 区域（现有）  |A 记录（示例） |
|---------|---------|---------|---------|
|帐户     |Azure Purview         |`privatelink.purview.azure.com`         |PurviewA         |
|门户     |Azure Purview 帐户          |`privatelink.purview.azure.com`        |Web         |
|引入     |Purview 托管存储帐户 - Blob          |`privatelink.blob.core.windows.net`          |scaneastusabcd1234         |
|引入   |Purview 托管存储帐户 - 队列         |`privatelink.queue.core.windows.net`         |scaneastusabcd1234         |
|引入     |Purview 托管存储帐户 - 事件中心         |`privatelink.servicebus.windows.net`         |atlas-12345678-1234-1234-abcd-123456789abc         |

有关详细信息，请参阅 [Azure 专用终结点 DNS 配置](../private-link/private-endpoint-dns.md)中的[不带自定义 DNS 服务器的虚拟网络工作负荷](../private-link/private-endpoint-dns.md#virtual-network-workloads-without-custom-dns-server)和[使用 DNS 转发器的本地工作负荷](../private-link/private-endpoint-dns.md#on-premises-workloads-using-a-dns-forwarder)方案。

   :::image type="content" source="media/catalog-private-link/purview-name-resolution-diagram.png" alt-text="显示 Azure Purview 名称解析的关系图"lightbox="media/catalog-private-link/purview-name-resolution-diagram.png":::

如果在网络上使用自定义 DNS 服务器，则客户端必须能够将 Azure Purview 终结点的 FQDN 解析为专用终结点 IP 地址。 配置 DNS 服务器以将专用链接子域委托到虚拟网络的专用 DNS 区域。 或者，使用专用终结点 IP 地址为 `PurviewA.privatelink.purview.azure.com` 配置 A 记录。
完成专用终结点部署后，请确保在相应的 Azure 专用 DNS 区域中存在用于名称解析的[虚拟网络链接](../dns/private-dns-virtual-network-links.md)，该链接指向部署了专用终结点的 Azure 虚拟网络。 

有关详细信息，请参阅 [Azure 专用终结点 DNS 配置](../private-link/private-endpoint-dns.md)。

## <a name="option-3---use-your-own-dns-servers"></a>选项 3 - 使用自己的 DNS 服务器

如果不使用 DNS 转发器，而是直接在本地 DNS 服务器中管理 A 记录，以通过其专用 IP 地址解析终结点，则可能需要在 DNS 服务器中创建以下 A 记录。

> [!NOTE]
> 使用 `PurviewA`、`scaneastusabcd1234` 和 `atlas-12345678-1234-1234-abcd-123456789abc` 更新环境中包含对应 Azure 资源名称的所有名称。 例如，使用 Azure Purview 托管存储帐户的名称，而非 `scaneastusabcd1234`。

| 名称 | 类型 | 值 |
| ---------- | -------- | --------------- |
| `web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview> |
| `scaneastusabcd1234.blob.core.windows.net` | A | \<blob-ingestion private endpoint IP address of Azure Purview> |
| `scaneastusabcd1234.queue.core.windows.net` | A | \<queue-ingestion private endpoint IP address of Azure Purview> |
| `atlas-12345678-1234-1234-abcd-123456789abc.servicebus.windows.net`| A | \<namespace-ingestion private endpoint IP address of Azure Purview> |
| `PurviewA.Purview.azure.com` | A | \<account private endpoint IP address of Azure Purview> |
| `PurviewA.scan.Purview.azure.com` | A | \<account private endpoint IP address of Azure Purview> |
| `PurviewA.catalog.Purview.azure.com` | A | \<account private endpoint IP address of Azure Purview\> |
| `PurviewA.proxy.purview.azure.com` | A | \<account private endpoint IP address of Azure Purview\> |
| `PurviewA.guardian.purview.azure.com` | A | \<account private endpoint IP address of Azure Purview\> |
| `PurviewA.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.manifest.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.cdn.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.hub.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.catalog.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.cseo.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.datascan.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.datashare.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.datasource.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.policy.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.sensitivity.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |

## <a name="verify-and-dns-test-name-resolution-and-connectivity"></a>验证和 DNS 测试名称解析和连接 

1. 如果使用 Azure 专用 DNS 区域，请确保在 Azure 订阅中创建以下 DNS 区域和相应的 A 记录：

   |专用终结点  |专用终结点关联目标  |DNS 区域  |A 记录（示例） |
   |---------|---------|---------|---------|
   |帐户     |Azure Purview         |`privatelink.purview.azure.com`         |PurviewA         |
   |门户     |Azure Purview 帐户          |`privatelink.purview.azure.com`        |Web         |
   |引入     |Purview 托管存储帐户 - Blob          |`privatelink.blob.core.windows.net`          |scaneastusabcd1234         |
   |引入   |Purview 托管存储帐户 - 队列         |`privatelink.queue.core.windows.net`         |scaneastusabcd1234         |
   |引入     |Purview 托管存储帐户 - 事件中心         |`privatelink.servicebus.windows.net`         |atlas-12345678-1234-1234-abcd-123456789abc         |

2. 在 Azure 虚拟网络的 Azure 专用 DNS 区域中创建[虚拟网络链接](../dns/private-dns-virtual-network-links.md)以便于进行内部名称解析。
   
3. 从管理 PC 和自托管集成运行时 VM 中，使用 Nslookup.exe 和 PowerShell 等工具测试名称解析以及与 Azure Purview 帐户的网络连接

要测试名称解析，需要通过其专用 IP 地址解析以下 FQDN：（而不是 PurviewA、scaneastusabcd1234 或 atlas-12345678-1234-1234-abcd-123456789abc，使用与你的权限帐户名称和托管资源名称关联的主机名）

- `PurviewA.purview.azure.com`
- `web.purview.azure.com`
- `scaneastusabcd1234.blob.core.windows.net`
- `scaneastusabcd1234.queue.core.windows.net`
- `atlas-12345678-1234-1234-abcd-123456789abc.servicebus.windows.net`

要测试网络连接，可以从自承载集成运行时 VM 启动 PowerShell 控制台，然后使用 `Test-NetConnection` 来测试连接。 必须通过其专用终结点解析每个终结点，并获得值为 true 的 TcpTestSucceeded。 （而不是 PurviewA、scaneastusabcd1234 或 atlas-12345678-1234-1234-abcd-123456789abc，使用与你的权限帐户名称和托管资源名称关联的主机名）

- `Test-NetConnection -ComputerName PurviewA.purview.azure.com -port 443`
- `Test-NetConnection -ComputerName web.purview.azure.com -port 443`
- `Test-NetConnection -ComputerName scaneastusabcd1234.blob.core.windows.net -port 443`
- `Test-NetConnection -ComputerName scaneastusabcd1234.queue.core.windows.net -port 443`
- `Test-NetConnection -ComputerName atlas-12345678-1234-1234-abcd-123456789abc.servicebus.windows.net -port 443` 

## <a name="next-steps"></a>后续步骤

- [对 Azure Purview 帐户的专用终结点配置进行故障排除](catalog-private-link-troubleshoot.md)
- [在 Azure Purview 中管理数据源](./manage-data-sources.md)
