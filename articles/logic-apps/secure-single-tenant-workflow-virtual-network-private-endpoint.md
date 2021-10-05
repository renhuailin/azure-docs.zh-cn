---
title: 保护单租户工作流与虚拟网络之间的流量
description: 保护 Azure 逻辑应用中虚拟网络、存储帐户和单租户工作流之间的流量。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 08/31/2021
ms.openlocfilehash: 658d8c8c43bd2795a6a25730ff85ffb6bbd3a63c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128598168"
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

若要保护来自逻辑应用的出站流量，可将逻辑应用与虚拟网络集成。 默认情况下，来自逻辑应用的出站流量进入专用地址（例如`10.0.0.0/8`、`172.16.0.0/12` 和 `192.168.0.0/16`）时，仅受网络安全组 (NSG) 和用户定义的路由 (UDR) 的影响。

如果你将自己的域名服务器 (DNS) 用于虚拟网络，请将逻辑应用资源的 `WEBSITE_DNS_SERVER` 应用设置指定为 DNS 的 IP 地址。 如果你有辅助 DNS，请添加名为 `WEBSITE_DNS_ALT_SERVER` 的另一个应用设置，并将值也设置为 DNS 的 IP。 另外，请更新 DNS 记录以将专用终结点指向内部 IP 地址。 专用终结点会将 DNS 查找发送到专用地址，而不是发送到特定资源的公共地址。 有关详细信息，请查看[专用终结点 - 将应用与 Azure 虚拟网络集成](../app-service/web-sites-integrate-with-vnet.md#private-endpoints)。

> [!IMPORTANT]
> 要使 Azure 逻辑应用运行时正常工作，与后端存储的连接不能中断。 若要使 Azure 托管的托管连接器正常工作，则与托管 API 服务的连接不能间断。

### <a name="considerations-for-outbound-traffic-through-private-endpoints"></a>通过专用终结点的出站流量的注意事项

设置虚拟网络集成只会影响出站流量。 若要保护继续使用应用服务共享终结点的入站流量，请查看[通过专用终结点设置入站流量](#set-up-inbound)。

有关详细信息，请参阅以下文档：

- [将应用与 Azure 虚拟网络集成](../app-service/web-sites-integrate-with-vnet.md)

- [网络安全组](../virtual-network/network-security-groups-overview.md)

- [虚拟网络流量路由](../virtual-network/virtual-networks-udr-overview.md)

## <a name="connect-to-storage-account-with-private-endpoints"></a>使用专用终结点连接到存储帐户

可限制存储帐户访问，以便只有虚拟网络中的资源才能连接。 Azure 存储支持将专用终结点添加到存储帐户。 然后，逻辑应用工作流可使用这些终结点与存储帐户通信。 有关详细信息，请查看[对 Azure 存储使用专用终结点](../storage/common/storage-private-endpoints.md)。

> [!NOTE]
> 以下步骤要求在存储帐户上暂时启用公共访问。 如果由于组织策略的原因而无法启用公共访问，你仍可以使用专用存储帐户来部署逻辑应用。 但是，必须使用 Azure 资源管理器模板（ARM 模板）进行部署。 有关 ARM 模板示例，请查看[使用安全存储帐户和专用终结点部署逻辑应用](https://github.com/VeeraMS/LogicApp-deployment-with-Secure-Storage)。

1. 为每个表、队列和 Blob 和文件存储服务创建不同的专用终结点。

1. 部署逻辑应用时，在存储帐户上启用临时公共访问。

   1. 在 [Azure 门户](https://portal.azure.com)中，打开你的存储帐户资源。

   1. 在存储帐户资源菜单中的“安全性 + 网络”下，选择“网络” 。

   1. 在“网络”窗格中的“防火墙和虚拟网络”选项卡上，在“允许从以下位置访问”下，选择“所有网络”   。

1. 使用 Azure 门户或 Visual Studio Code 部署逻辑应用资源。

1. 部署完成后，在连接到存储帐户的虚拟网络或子网上的逻辑应用和专用终结点之间启用集成。

   1. 在 [Azure 门户](https://portal.azure.com)中，打开你的逻辑应用资源。

   1. 在逻辑应用资源菜单中的“设置”下，选择“网络” 。

   1. 在逻辑应用与专用终结点 IP 地址之间设置必要的连接。

   1. 若要通过虚拟网络访问逻辑应用工作流数据，请在逻辑应用资源设置中将 `WEBSITE_CONTENTOVERVNET` 设置指定为 `1`。

   如果你将自己的域名服务器 (DNS) 用于虚拟网络，请将逻辑应用资源的 `WEBSITE_DNS_SERVER` 应用设置指定为 DNS 的 IP 地址。 如果你有辅助 DNS，请添加名为 `WEBSITE_DNS_ALT_SERVER` 的另一个应用设置，并将值也设置为 DNS 的 IP。 另外，请更新 DNS 记录以将专用终结点指向内部 IP 地址。 专用终结点会将 DNS 查找发送到专用地址，而不是发送到特定资源的公共地址。 有关详细信息，请查看[专用终结点 - 将应用与 Azure 虚拟网络集成](../app-service/web-sites-integrate-with-vnet.md#private-endpoints)。

1. 应用这些应用设置后，可以从存储帐户中删除公共访问。

## <a name="next-steps"></a>后续步骤

- [随处运行的逻辑应用：逻辑应用（单租户）进行网络连接的可能性](https://techcommunity.microsoft.com/t5/integrations-on-azure/logic-apps-anywhere-networking-possibilities-with-logic-app/ba-p/2105047)
