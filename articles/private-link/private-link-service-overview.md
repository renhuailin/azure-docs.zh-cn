---
title: 什么是 Azure 专用链接服务？
description: 了解 Azure 专用链接服务。
services: private-link
author: sumeetmittal
ms.service: private-link
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: sumi
ms.openlocfilehash: 50d918cb6cb096674dc5b79d478d37d65422106e
ms.sourcegitcommit: ef448159e4a9a95231b75a8203ca6734746cd861
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123186004"
---
# <a name="what-is-azure-private-link-service"></a>什么是 Azure 专用链接服务？

Azure 专用链接服务是对你自己的、由 Azure 专用链接驱动的服务的引用。 可为 [Azure 标准负载均衡器](../load-balancer/load-balancer-overview.md)后面运行的服务启用专用链接访问，使该服务的使用者能够从其自己的 VNet 以私密方式访问该服务。 你的客户可在其 VNet 中创建专用终结点，然后将此终结点映射到此服务。 本文解释与服务提供商一方相关的概念。 

:::image type="content" source="./media/private-link-service-overview/consumer-provider-endpoint.png" alt-text="专用链接服务工作流" border="true":::

*图：Azure 专用链接服务。*

## <a name="workflow"></a>工作流

![专用链接服务工作流](media/private-link-service-overview/private-link-service-workflow.png)


*图：Azure 专用链接服务工作流。*

### <a name="create-your-private-link-service"></a>创建专用链接服务

- 将应用程序配置为在虚拟网络中的标准负载均衡器后面运行。 如果已在标准负载均衡器后面配置了应用程序，则可以跳过此步骤。   
- 创建引用上述负载均衡器的专用链接服务。 在负载均衡器选择过程中，选择要在其中接收流量的前端 IP 配置。 选择专用链接服务的 NAT IP 地址的子网。 建议至少提供子网中可用的八个 NAT IP 地址。 所有使用者流量看上去都源自此专用 IP 地址池，并发往服务提供商。 为专用链接服务选择适当的属性/设置。    

    > [!NOTE]
    > 只有标准负载均衡器支持 Azure 专用链接服务。 
    
### <a name="share-your-service"></a>共享服务

创建专用链接服务后，Azure 将会根据提供的服务名称，生成称作“别名”的全局唯一命名对象。 可与客户脱机共享服务的别名或资源 URI。 使用者可以使用别名或资源 URI 发起专用链接连接。
 
### <a name="manage-your-connection-requests"></a>管理连接请求

使用者发起连接后，服务提供商可以接受或拒绝连接请求。 所有连接请求将列在专用链接服务中的 **privateendpointconnections** 属性下。
 
### <a name="delete-your-service"></a>删除服务

如果不再使用专用链接服务，可将其删除。 但是，在删除该服务之前，请确保它没有任何关联的专用终结点连接。 可以拒绝所有连接并删除服务。

## <a name="properties"></a>属性

专用链接服务指定以下属性： 

|属性 |说明  |
|---------|---------|
|预配状态 (provisioningState)  |一个只读属性，列出专用链接服务的当前预配状态。 适用的预配状态为：“Deleting；Failed；Succeeded；Updating”。 如果预配状态为“Succeeded”，则表示已成功预配专用链接服务。        |
|别名 (alias)     | 别名是服务的全局唯一只读字符串。 它可以帮助掩码服务的客户数据，同时为服务创建一个易于共享的名称。 创建专用链接服务时，Azure 将为该服务生成可与客户共享的别名。 客户可以使用此别名请求连接到你的服务。          |
|可见性 (visibility)     | 可见性是控制专用链接服务的公开设置的属性。 服务提供商可以选择使用 Azure 基于角色的访问控制 (Azure RBAC) 权限、一组受限的订阅或所有 Azure 订阅，来限制其服务对订阅的曝光程度。          |
|自动批准 (autoApproval)    |   自动批准控制对专用链接服务的自动访问。 从这些订阅中的专用终结点请求连接时，会自动批准“自动批准”列表中指定的订阅。          |
|负载均衡器前端 IP 配置 (loadBalancerFrontendIpConfigurations)    |    专用链接服务绑定到标准负载均衡器的前端 IP 地址。 发往服务的所有流量将抵达 SLB 的前端。 可以配置 SLB 规则，以将此流量定向到运行应用程序的相应后端池。 负载均衡器前端 IP 配置不同于 NAT IP 配置。      |
|NAT IP 配置 (ipConfigurations)    |    此属性引用专用链接服务的 NAT（网络地址转换）IP 配置。 可以从服务提供商虚拟网络中的任何子网选择 NAT IP。 专用链接服务对专用链接流量执行目标端 NAT。 这可以确保源（使用者端）与目标（服务提供商）地址空间之间不会出现 IP 冲突。 在目标端（服务提供商端），NAT IP 地址将显示为服务收到的所有数据包的源 IP，并显示为服务发送的所有数据包的目标 IP。       |
|专用终结点连接 (privateEndpointConnections)     |  此属性列出连接到专用链接服务的专用终结点。 多个专用终结点可以连接到同一个专用链接服务，服务提供商可以控制单个专用终结点的状态。        |
|TCP 代理 V2 (EnableProxyProtocol)     |  此属性允许服务提供商使用 tcp 代理 v2 检索有关服务使用者的连接信息。 服务提供商负责设置接收方配置，以便能够分析代理协议 v2 标头。        |
|||


### <a name="details"></a>详细信息

- 可以从任何公共区域中已批准的专用终结点访问专用链接服务。 可以使用专用 VPN 或 ExpressRoute 连接，从同一个虚拟网络、区域对等互连的 VNet、全球对等互连的 VNet 和本地访问专用终结点。 
 
- 创建专用链接服务时，将为资源的生命周期创建一个网络接口。 此接口不可由客户管理。
 
- 必须将专用链接服务部署在虚拟网络和标准负载均衡器所在的同一个区域。  
 
- 可以从属于不同 VNet、订阅和/或 Active Directory 租户的多个专用终结点访问单个专用链接服务。 连接是通过连接工作流建立的。 
 
- 可以使用不同的前端 IP 配置在同一个标准负载均衡器上创建多个专用链接服务。 可为每个标准负载均衡器和每个订阅创建的专用链接服务数量有限制。 有关详细信息，请参阅  [Azure 限制](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)。
 
- 可将多个 NAT IP 配置链接到专用链接服务。 选择多个 NAT IP 配置有助于服务提供商进行缩放。 目前，服务提供商最多可为每个专用链接服务分配八个 NAT IP 地址。 对于每个 NAT IP 地址，可为 TCP 连接分配更多的端口，从而进行横向扩展。将多个 NAT IP 地址添加到专用链接服务后，无法删除 NAT IP 地址。 这是为了确保在删除 NAT IP 地址时活动的连接不会受到影响。


## <a name="alias"></a>别名

“别名”是服务的全局唯一名称。  它可以帮助掩码服务的客户数据，同时为服务创建一个易于共享的名称。 创建专用链接服务时，Azure 将为该服务生成可与客户共享的别名。 客户可以使用此别名请求连接到你的服务。

别名由三个部分组成：<前缀>.<GUID>.<后缀>   

- 前缀是服务名称。 可以选择自己的前缀。 创建“别名”后，无法对其进行更改，因此请适当地选择前缀。  
- GUID 由平台提供。 它帮助确保名称的全局唯一性。 
- 后缀由 Azure 追加：<区域>.azure.privatelinkservice  

完整别名：<前缀>.  {GUID}.<区域>.azure.privatelinkservice   

## <a name="control-service-exposure"></a>控制服务的公开

专用链接服务在“可见性”设置中提供了三个选项来控制服务的公开程度。 可见性设置决定使用者是否可以连接到你的服务。 下面是可见性设置选项（从限制最多到限制最少）：
 
- 仅限基于角色的访问控制：如果你的服务是专用的（从你拥有的不同 VNet 进行使用），你可以在与同一 Active Directory 租户关联的订阅中使用 RBAC 作为访问控制机制。 注意：允许通过 RBAC 实现跨租户可见性。
- 受订阅限制：如果服务会在不同租户之间使用，则只能将其公开给你信任的有限订阅集。 可以预先批准授权。
- 使用你的别名的任何人：如果你想让服务公开，并允许任何使用你的专用链接服务别名的人请求连接，请选择此选项。 

## <a name="control-service-access"></a>控制服务的访问

能够看到你的专用链接服务的使用者（由可见性设置控制）可以在其 VNet 中创建专用终结点，并请求连接到该专用链接服务。 专用终结点连接将在专用链接服务对象中以“挂起”状态创建。 服务提供商负责处理连接请求。 可以批准连接、拒绝连接，或删除连接。 只有已批准的连接才能将流量发送到专用链接服务。

可以通过在专用链接服务中使用自动批准属性，自动完成批准连接的操作。 “自动批准”是为服务提供商提供的一项功能，它可以预先批准一组订阅，使它们能够访问服务提供商的服务。 客户需要脱机共享其订阅，才能让服务提供商将这些订阅添加到自动批准列表中。 自动批准是可见性数组的子集。 可见性控制公开设置，而自动批准则控制服务的批准设置。 如果客户从自动批准列表中的订阅请求连接，将自动批准该连接并建立连接。 服务提供商不再需要手动批准请求。 另一方面，如果客户从可见性数组而不是自动批准数组中的订阅请求连接，该请求将抵达服务提供商，但服务提供商必须手动批准连接。

## <a name="getting-connection-information-using-tcp-proxy-v2"></a>使用 TCP 代理 v2 获取连接信息

使用专用链接服务时，来自专用终结点的数据包的源 IP 地址是服务提供商端的网络地址转换 (NAT)，使用从提供商的虚拟网络分配的 NAT IP。 因此，应用程序将接收分配的 NAT IP 地址，而非接收服务使用者的实际源 IP 地址。 如果应用程序需要来自使用方的实际源 IP 地址，则你可以在服务上启用代理协议，并从代理协议标头中检索该信息。 除了源 IP 地址外，代理协议标头还携带专用终结点的 LinkID。 源 IP 地址和 LinkID 的组合可以帮助服务提供商唯一识别其使用者。 有关代理协议的详细信息，请访问[此处](https://www.haproxy.org/download/1.8/doc/proxy-protocol.txt)。 

此信息使用自定义的 Type-Length-Value (TLV) 向量进行编码，如下所示：

自定义 TLV 详细信息：

|字段 |长度（八进制）  |说明  |
|---------|---------|----------|
|类型  |1        |PP2_TYPE_AZURE (0xEE)|
|Length  |2      |值的长度|
|Value  |1     |PP2_SUBTYPE_AZURE_PRIVATEENDPOINT_LINKID (0x01)|
|  |4        |UINT32（4个字节），表示专用终结点的 LINKID。 编码为 little endian 格式。|

 > [!NOTE]
 > 服务提供商负责确保在专用链接服务上启用代理协议时，将标准负载均衡器后面的服务配置为按照[规范](https://www.haproxy.org/download/1.8/doc/proxy-protocol.txt)分析代理协议标头。 如果在专用链接服务上启用了代理协议设置，但未将服务提供商的服务配置为分析该标头，则请求会失败。 同样，如果服务提供商的服务需要代理协议标头，而专用链接服务上未启用该设置，则请求会失败。 启用代理协议设置后，代理协议标头也将包含在从主机到后端虚拟机的 HTTP/TCP 运行状况探测中，即使标头中没有客户端信息也是如此。 

## <a name="limitations"></a>限制

下面是使用专用链接服务时存在的已知限制：
- 仅在标准负载均衡器上受支持。 在基本负载均衡器上不受支持。  
- 仅在使用 VM/VMSS 时通过 NIC 配置后端池的标准负载均衡器上受支持。
- 仅支持 IPv4 流量
- 仅支持 TCP 和 UDP 流量


## <a name="next-steps"></a>后续步骤
- [使用 Azure PowerShell 创建专用链接服务](create-private-link-service-powershell.md)
- [使用 Azure CLI 创建专用链接服务](create-private-link-service-cli.md)
