---
title: 保护单租户工作流与虚拟网络之间的流量
description: 保护 Azure 逻辑应用中虚拟网络、存储帐户和单租户工作流之间的流量。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: conceptual
ms.date: 07/25/2021
ms.openlocfilehash: 4726df91efb18b2d9beec77606db449bd4aee3fa
ms.sourcegitcommit: 6f21017b63520da0c9d67ca90896b8a84217d3d3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2021
ms.locfileid: "114652641"
---
# <a name="secure-traffic-between-virtual-networks-and-single-tenant-workflows-in-azure-logic-apps-using-private-endpoints"></a>使用专用终结点保护 Azure 逻辑应用中虚拟网络和单租户工作流之间的流量

为了在逻辑应用工作流与虚拟网络之间以专用且安全的方式进行通信，可为入站流量设置专用终结点，并将虚拟网络集成用于出站流量。

专用终结点是一个网络接口，可以通过专用且安全的方式连接到 Azure 专用链接支持的服务。 此服务可以是 Azure 服务（如 Azure 逻辑应用、Azure 存储、Azure Cosmos DB 和 SQL），也可以是自己的专用链接服务。 专用终结点使用虚拟网络中的专用 IP 地址将服务有效地接入虚拟网络中。

本文介绍如何通过专用终结点为入站流量、出站流量和到存储帐户的连接设置访问权限。

有关详细信息，请参阅以下文档：

- [什么是 Azure 专用终结点？](../private-link/private-endpoint-overview.md)

- [什么是 Azure 专用链接？](../private-link/private-link-overview.md)

- [什么是 Azure 逻辑应用中的单租户逻辑应用工作流？](single-tenant-overview-compare.md)

## <a name="prerequisites"></a>先决条件

你需要有一个新的或现有的 Azure 虚拟网络，其中包括一个没有任何委派的子网。 该子网用于在虚拟网络中部署和分配专用 IP 地址。

有关详细信息，请参阅以下文档：

- [快速入门：使用 Azure 门户创建虚拟网络](../virtual-network/quick-create-portal.md)

- [什么是子网委托？](../virtual-network/subnet-delegation-overview.md)

- [添加或删除子网委托](../virtual-network/manage-subnet-delegation.md)

<a name="set-up-inbound"></a>

## <a name="set-up-inbound-traffic-through-private-endpoints"></a>设置通过专用终结点的入站流量

若要保护工作流的入站流量，请完成以下大致步骤：

1. 使用可接收和处理入站请求的内置触发器（例如请求触发器或 HTTP + Webhook 触发器）启动工作流。 此触发器使用可调用的终结点设置工作流。

1. 将专用终结点添加到虚拟网络。

1. 进行测试调用以检查对终结点的访问。 若要在设置此终结点后调用逻辑应用工作流，必须连接到虚拟网络。

### <a name="prerequisites-for-inbound-traffic-through-private-endpoints"></a>通过专用终结点的入站流量的先决条件

除了[顶级先决条件中的虚拟网络设置](#prerequisites)外，还需要有一个新的或现有的基于单租户的逻辑应用工作流（工作流使用可接收请求的内置触发器启动）。

例如，请求触发器在工作流上创建了一个终结点，可接收和处理来自其他调用方（包括工作流）的入站请求。 此终结点提供了一个 URL，可用于调用和触发工作流。 在本示例中，步骤继续使用请求触发器。

有关详细信息，请参阅以下文档：

- [在 Azure 逻辑应用中创建单租户逻辑应用工作流](create-single-tenant-workflows-azure-portal.md)

- [使用 Azure 逻辑应用接收和响应入站 HTTPS 请求](../connectors/connectors-native-reqres.md)

### <a name="create-the-workflow"></a>创建工作流

1. 如果尚未创建，请创建一个基于单租户的逻辑应用和一个空白工作流。

1. 设计器打开后，添加请求触发器作为工作流的第一步。

   > [!NOTE]
   > 只能从虚拟网络内部调用请求触发器和 Webhook 触发器。 由于托管 API Webhook 触发器和操作需要公共终结点来接收调用，因此它们不起作用。 

1. 根据场景要求，添加想要在工作流中运行的其他操作。

1. 完成后，保存工作流。

有关详细信息，请参阅[在 Azure 逻辑应用中创建单租户逻辑应用工作流](create-single-tenant-workflows-azure-portal.md)。

#### <a name="copy-the-endpoint-url"></a>复制终结点 URL

1. 在工作流菜单上，选择“概述”。

1. 在“概述”页，复制并保存“工作流 URL”，供以后使用 。

   若要触发工作流，请调用此 URL 或向其发送请求。

1. 通过调用 URL 或向 URL 发送请求，确保 URL 正常工作。 可使用所需的任何工具来发送请求，例如 Postman。

### <a name="set-up-private-endpoint-connection"></a>读取专用终结点连接

1. 在逻辑应用菜单的“设置”下，选择“网络” 。

1. 在“网络”页的“专用终结点连接”下，选择“配置专用终结点连接”  。

1. 在“专用终结点连接”页中选择“添加” 。

1. 在打开的“添加专用终结点”窗格中，提供所请求的有关终结点的信息。

   有关详细信息，请查看[专用终结点属性](../private-link/private-endpoint-overview.md#private-endpoint-properties)。

1. Azure 成功预配专用终结点后，请重试调用工作流 URL。

   此时会收到一个预期的 `403 Forbidden` 错误，表示专用终结点已设置并正常工作。

1. 若要确保连接正常工作，请在具有专用终结点的同一虚拟网络中创建虚拟机，然后尝试调用逻辑应用工作流。

### <a name="considerations-for-inbound-traffic-through-private-endpoints"></a>通过专用终结点的入站流量的注意事项

- 如果从虚拟网络外部访问，监视视图无法访问触发器和操作中的输入和输出。

- 从 Visual Studio Code 或 Azure CLI 进行的部署只能从虚拟网络内部进行。 可使用部署中心将逻辑应用链接到 GitHub 存储库。 然后，可使用 Azure 基础结构生成和部署代码。 

  要使 GitHub 集成正常工作，需在逻辑应用中删除 `WEBSITE_RUN_FROM_PACKAGE` 设置，或将该值设置为 `0`。

- 启用专用链接不会影响出站流量，流量仍会流经应用服务基础结构。

<a name="set-up-outbound"></a>

## <a name="set-up-outbound-traffic-through-private-endpoints"></a>设置通过专用终结点的出站流量

若要保护来自逻辑应用的出站流量，可将逻辑应用与虚拟网络集成。 默认情况下，来自逻辑应用的出站流量进入专用地址（例如`10.0.0.0/8`、`172.16.0.0/12` 和 `192.168.0.0/16`）时，仅受网络安全组 (NSG) 和用户定义的路由 (UDR) 的影响。 但是，通过将所有出站流量路由通过自己的虚拟网络，可使所有出站流量受到 NSG、路由和防火墙的约束。 若要确保集成子网中所有出站流量都受到 NSG 和 UDR 的影响，请将逻辑应用设置 `WEBSITE_VNET_ROUTE_ALL` 设置为 `1`。

> [!IMPORTANT]
> 若要使逻辑应用运行时正常工作，则与后端存储的连接不能间断。 若要使 Azure 托管的托管连接器正常工作，则与托管 API 服务的连接不能间断。

若要确保逻辑应用在虚拟网络中使用专用域名服务器 (DNS) 区域，将 WEBSITE_DNS_SERVER 设置为 168.63.129.16，以确保应用在 vNET 中使用专用 DNS 区域

### <a name="considerations-for-outbound-traffic-through-private-endpoints"></a>通过专用终结点的出站流量的注意事项

设置虚拟网络集成不会影响入站流量，流量会继续使用应用服务共享的终结点。 若要保护入站流量，请查看[设置通过专用终结点的入站流量](#set-up-inbound)。

有关详细信息，请参阅以下文档：

- [将应用与 Azure 虚拟网络集成](../app-service/web-sites-integrate-with-vnet.md)
- [网络安全组](../virtual-network/network-security-groups-overview.md)
- [虚拟网络流量路由](../virtual-network/virtual-networks-udr-overview.md)

## <a name="connect-to-storage-account-with-private-endpoints"></a>使用专用终结点连接到存储帐户

可限制存储帐户访问，以便只有虚拟网络中的资源才能连接。 Azure 存储支持将专用终结点添加到存储帐户。 然后，逻辑应用工作流可使用这些终结点与存储帐户通信。

在逻辑应用设置中，选择下面其中一个选项，将 `AzureWebJobsStorage` 设置为具有专用终结点的存储帐户的连接字符串：

- **Azure 门户**：在逻辑应用菜单上，选择“配置”。 用存储帐户的连接字符串更新 `AzureWebJobsStorage` 设置。

- **Visual Studio Code**：在项目根级别 local.settings.json 文件中，用存储帐户的连接字符串更新 `AzureWebJobsStorage` 设置。

 有关详细信息，请参阅[对 Azure 存储使用专用终结点文档](../storage/common/storage-private-endpoints.md)。

### <a name="considerations-for-private-endpoints-on-storage-accounts"></a>存储帐户上专用终结点的注意事项

- 为每个表、队列、Blob 和文件存储服务创建不同的专用终结点。

- 使用此设置，将所有出站流量路由通过虚拟网络：

  `"WEBSITE_VNET_ROUTE_ALL": "1"`

- 若要使逻辑应用在虚拟网络中使用专用域名服务器 (DNS) 区域，请将逻辑应用的 `WEBSITE_DNS_SERVER` 设置设为 `168.63.129.16`。

- 部署自己的逻辑应用时，需要有一个单独的可公开访问的存储帐户。 请确保将 `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` 设置设为该存储帐户的连接字符串。

- 如果逻辑应用使用专用终结点，请使用 [GitHub 集成](https://docs.github.com/en/github/customizing-your-github-workflow/about-integrations)进行部署。

  如果逻辑应用不使用专用终结点，则可在 Visual Studio Code 中进行部署，并将 `WEBSITE_RUN_FROM_PACKAGE` 设置设为 `1`。 

## <a name="next-steps"></a>后续步骤

- [随处运行的逻辑应用：逻辑应用（单租户）进行网络连接的可能性](https://techcommunity.microsoft.com/t5/integrations-on-azure/logic-apps-anywhere-networking-possibilities-with-logic-app/ba-p/2105047)
